Ortools 

```python
# coding:utf-8
from ortools.constraint_solver import routing_enums_pb2
from ortools.constraint_solver import pywrapcp

from utils import get_matrix, load_order_file_new, get_time_windows


class MyOrtools:
    def __init__(self, distance_matrix, vehicle_capacities, demands=[], time_windows=[], depot=0, visited_dict={},
                 penalty=10000):
        """
        初始化数据
        :param distance_matrix: 二维矩阵，订单+起始点 每两点之间距离
        :param vehicle_capacities: 车辆(骑士)背单上限
        :param demands: 需求：每个地理位置都有与要提货的商品数量（例如重量或体积）对应的需求
        :param time_windows: list, 营业地点的时间范围数组，可视为请求到访的时间。车辆必须在规定的时间范围内前往某个营业地点。
        :param depot: 起始点index
        :param visited_dict: 特定车辆访问特定地点(同一地点不支持多辆车同时访问)
        :param penalty: 影响解决方案中离散地点的出现, 值越大, 离散地点越可能出现在路径中
        """
        if not demands:
            demands = [0] + [1] * (len(distance_matrix) - 1)
        self.data = {
            'distance_matrix': distance_matrix,
            'vehicle_capacities': vehicle_capacities,
            'num_vehicles': len(vehicle_capacities),
            'demands': demands,
            'time_windows': time_windows,
            'depot': depot,
        }
        self.visited_dict = visited_dict
        self.nodes_num = len(distance_matrix)
        self.penalty = penalty

    def __call__(self, *args, **kwargs):
        return self.main_process()

    def main_process(self):
        """
        主流程
        :return:
        """
        """Entry point of the program."""
        data = self.data

        # Create the routing index manager.
        manager = pywrapcp.RoutingIndexManager(
            len(data["distance_matrix"]), data["num_vehicles"], data["depot"]
        )

        # Create Routing Model.
        routing = pywrapcp.RoutingModel(manager)

        def distance_callback(from_index, to_index):
            """Returns the distance between the two nodes."""
            # Convert from routing variable Index to distance matrix NodeIndex.
            from_node = manager.IndexToNode(from_index)
            to_node = manager.IndexToNode(to_index)
            return data["distance_matrix"][from_node][to_node]

        transit_callback_index = routing.RegisterTransitCallback(distance_callback)

        # Define cost of each arc.
        routing.SetArcCostEvaluatorOfAllVehicles(transit_callback_index)

        # add time limit
        if self.data.get("time_windows", []):
            self.add_time_windows(routing, transit_callback_index, manager)

        def demand_callback(from_index):
            """Returns the demand of the node."""
            # Convert from routing variable Index to demands NodeIndex.
            from_node = manager.IndexToNode(from_index)
            return data["demands"][from_node]

        demand_callback_index = routing.RegisterUnaryTransitCallback(demand_callback)
        routing.AddDimensionWithVehicleCapacity(
            demand_callback_index,
            0,  # null capacity slack
            data["vehicle_capacities"],  # vehicle maximum capacities
            True,  # start cumul to zero
            "Capacity",
        )

        # visited some node
        if self.visited_dict:
            self.visited_points(routing, self.visited_dict)

        # Allow to drop nodes.
        if self.penalty > 0:
            self.drop_some_points(self.penalty, routing, manager)

        # Setting first solution heuristic.
        search_parameters = pywrapcp.DefaultRoutingSearchParameters()
        search_parameters.first_solution_strategy = (
            routing_enums_pb2.FirstSolutionStrategy.PATH_CHEAPEST_ARC
        )
        # 设置最大求解时间（以秒为单位）
        search_parameters.time_limit.seconds = 1

        # Solve the problem.
        solution = routing.SolveWithParameters(search_parameters)
        # Print solution on console.
        if solution:
            print_solution(data, manager, routing, solution)
            return parse_solution(data, manager, routing, solution)
        else:
            print("No solution found !")
            return {}

    def visited_points(self, routing, trans_indexs_dict):
        """
        某些车辆添加经过的点
        :param routing:
        :param trans_indexs_dict: transporter_id: [node1, node2,]
        :return:
        """
        # 反转trans_indexs_dict的key value
        index_transporter_dict = {}
        for k, v in trans_indexs_dict.items():
            for index in v:
                tmp_trans_list = index_transporter_dict.get(index, [])
                tmp_trans_list.append(k)
                index_transporter_dict[index] = tmp_trans_list

        # 必须经过某个点
        for index, trans_list in index_transporter_dict.items():
            routing.SetAllowedVehiclesForIndex(trans_list, index)

    def drop_some_points(self, penalty, routing, manager):
        """

        :param penalty:
        :param routing:
        :param manager:
        :return:
        """
        for node in range(1, self.nodes_num):
            routing.AddDisjunction([manager.NodeToIndex(node)], penalty)

    def add_time_windows(self, routing, transit_callback_index, manager):
        """
        增加时间约束
        :param routing:
        :param transit_callback_index:
        :param manager:
        :return:
        """
        # Add Time Windows constraint.
        time = "Time"
        routing.AddDimension(
            transit_callback_index,
            3000,  # allow waiting time
            999999,  # maximum time per vehicle
            False,  # Don't force start cumul to zero.
            time,
        )
        time_dimension = routing.GetDimensionOrDie(time)
        # Add time window constraints for each location except depot.
        for location_idx, time_window in enumerate(self.data["time_windows"]):
            if location_idx == self.data["depot"]:
                continue
            index = manager.NodeToIndex(location_idx)
            time_dimension.CumulVar(index).SetRange(time_window[0], time_window[1])
        # Add time window constraints for each vehicle start node.
        depot_idx = self.data["depot"]
        for vehicle_id in range(self.data["num_vehicles"]):
            index = routing.Start(vehicle_id)
            time_dimension.CumulVar(index).SetRange(
                self.data["time_windows"][depot_idx][0], self.data["time_windows"][depot_idx][1]
            )

        # Instantiate route start and end times to produce feasible times.
        for i in range(self.data["num_vehicles"]):
            routing.AddVariableMinimizedByFinalizer(
                time_dimension.CumulVar(routing.Start(i))
            )
            routing.AddVariableMinimizedByFinalizer(time_dimension.CumulVar(routing.End(i)))


def print_solution(data, manager, routing, solution):
    """Prints solution on console."""
    print("Objective: {}".format(solution.ObjectiveValue()))
    total_distance = 0
    total_load = 0
    for vehicle_id in range(data["num_vehicles"]):
        index = routing.Start(vehicle_id)
        plan_output = "Route for vehicle {}:\n".format(vehicle_id)
        route_distance = 0
        route_load = 0
        while not routing.IsEnd(index):
            node_index = manager.IndexToNode(index)
            route_load += data["demands"][node_index]
            plan_output += " {} Load({}) -> ".format(node_index, route_load)
            previous_index = index
            index = solution.Value(routing.NextVar(index))
            route_distance += routing.GetArcCostForVehicle(
                previous_index, index, vehicle_id
            )
        plan_output += " {} Load({})\n".format(manager.IndexToNode(index), route_load)
        plan_output += "Distance of the route: {}m\n".format(route_distance)
        plan_output += "Load of the route: {}\n".format(route_load)
        print(plan_output)
        total_distance += route_distance
        total_load += route_load
    print("Total distance of all routes: {}m".format(total_distance))
    print("Total load of all routes: {}".format(total_load))


def parse_solution(data, manager, routing, solution):
    """
    解析路径规划结果
    :param data:
    :param manager:
    :param routing:
    :param solution:
    :return:
    """
    transporter_order_dict = {}
    for vehicle_id in range(data["num_vehicles"]):
        order_index_list = []
        index = routing.Start(vehicle_id)
        while not routing.IsEnd(index):
            index = solution.Value(routing.NextVar(index))
            order_index = manager.IndexToNode(index)
            order_index_list.append(order_index)
        transporter_order_dict[vehicle_id] = order_index_list
    return transporter_order_dict


if __name__ == '__main__':
    points, transporter_ids, time_limit_list, transporter_order_dict, order_id_list = load_order_file_new()
    # 距离矩阵
    distance_matrix = get_matrix(points)

    # 起点
    depot = 0
    # 每辆车背单
    vehicle_capacities = [8] * len(transporter_ids)
    # 时间窗
    time_windows = get_time_windows(time_limit_list)
    #
    visited_dict = {}

    my_ortools = MyOrtools(
        distance_matrix=distance_matrix,
        time_windows=time_windows,
        vehicle_capacities=vehicle_capacities,
        depot=depot,
        visited_dict=visited_dict,
        penalty=-1,
    )
    res = my_ortools()
    print('-' * 100)
    print(res)

```

