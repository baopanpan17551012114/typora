## python进程池

```python
import multiprocessing
import time

def func(msg):
    print "msg:", msg
    time.sleep(3)
    print "end"
    return "done" + msg

if __name__ == "__main__":
    pool = multiprocessing.Pool(processes=4)
    result = []
    for i in xrange(3):
        msg = "hello %d" %(i)
        result.append(pool.apply_async(func, (msg, )))
    pool.close()
    pool.join()
    for res in result:
        print ":::", res.get()
    print "Sub-process(es) done."
    
####################################################################################
from tqdm import tqdm
import multiprocessing

pool = multiprocessing.Pool(processes=20)

def func(merchant_id, goodsid):
    df_line = sql_base.selectFromTable(sql.format(merchant_id, goodsid))
    return df_line

sql = """select pid, goodsid,title, goods_slogan,goods_biz_type, source, point_deduct_ratio, category_id, 
attribute_info, goods_prop_info, inner_goods_prop_info, goods_sku_info, goods_video_url, 
is_multi_sku, is_can_sell, is_put_away, put_away_date, is_membership_discount, is_guide, sale_channel_type, sell_model_type, 
auto_forbid_sell, sort, create_time, update_time, is_deleted
from alg_saas_xd_base_goods where pid={0} and goodsid={1}"""

pids = df_clean["merchant_id"].to_list()
goodsids = df_clean["goodsid"].to_list()

print("训练样本总量：", len(df_clean))
result = []
df_goods_infos = sql_base.selectFromTable(sql.format(pids[0], goodsids[0]))

for i in tqdm(range(1, len(df_clean))):
    merchant_id = pids[i]
    goodsid = goodsids[i]
    result.append(pool.apply_async(func, (merchant_id, goodsid, )))
    
for res in result:
    df_goods_infos = pd.concat([df_goods_infos, res.get()])
print(len(df_goods_infos))
df_goods_infos.to_pickle("goods_infos.pkl")
```

