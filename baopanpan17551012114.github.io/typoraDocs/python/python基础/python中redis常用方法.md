redis中常用方法

```python
#!/usr/bin/env python
# -*- coding:utf-8 -*-
# author:baopanpan
# datetime:2021/11/12 4:08 下午
# software: PyCharm
import logging
from collections import OrderedDict
from redis import StrictRedis
from rediscluster import StrictRedisCluster


class RedisClient:
    def __init__(self, uri):
        self.is_cluster = False
        self.client = None
        if ',' in uri:
            hosts = uri.split(',')
            nodes = []
            for host in hosts:
                parts = host.split(':')
                nodes.append({
                    'host': parts[0],
                    'port': int(parts[1])
                })
            self.is_cluster = True
            self.client = StrictRedisCluster(startup_nodes=nodes)
        else:
            parts = uri.split(':')
            self.client = StrictRedis(host=parts[0], port=int(parts[1]))

    def _partition_keys(self, keys):
        slots = {}
        if isinstance(keys, list):
            for key in keys:
                slot = self.client.connection_pool.nodes.keyslot(key)
                if slot not in slots:
                    slots[slot] = []
                slots[slot].append(key)
        elif isinstance(keys, dict):
            for k, v in keys.items():
                slot = self.client.connection_pool.nodes.keyslot(k)
                if slot not in slots:
                    slots[slot] = OrderedDict()
                slots[slot][k] = v
        else:
            raise Exception('keys must be a list or a dict')
        return slots

    @staticmethod
    def _sort_result(keys, order, result):
        sorted_result = OrderedDict()
        for key in keys:
            sorted_result[key] = None
        flatten_result = []
        for item in result:
            if isinstance(item, list):
                for sub_item in item:
                    flatten_result.append(sub_item)
            else:
                flatten_result.append(item)
        for k, v in zip(order, flatten_result):
            sorted_result[k] = v
        return_val = []
        for r in sorted_result.values():
            return_val.append(r)
        return return_val

    def _multi_slot_key_cmd(self, cmd, keys, sort_result=False):
        partitioned = self._partition_keys(keys)
        pipeline = self.client.pipeline()
        key_order = None
        if sort_result:
            key_order = []
        for _, slot_keys in partitioned.items():
            if sort_result:
                if isinstance(slot_keys, list):
                    key_order += slot_keys
                elif isinstance(slot_keys, dict):
                    key_order += slot_keys.keys()
            if isinstance(slot_keys, dict):
                args = []
                for k, v in slot_keys.items():
                    args.append(k)
                    args.append(v)
            else:
                args = slot_keys
            pipeline.execute_command(cmd, *args)
        result = pipeline.execute()
        if sort_result:
            return RedisClient._sort_result(keys, key_order, result)
        else:
            return result

    def append(self, key, value):
        return self.client.append(key, value)

    def decr(self, key):
        return self.client.decr(key)

    def decrby(self, key, amount):
        return self.client.decr(key, amount)

    def get(self, key):
        return self.client.get(key)

    def getbit(self, key, offset):
        return self.client.getbit(key, offset)

    def getrange(self, key, start, end):
        return self.client.getrange(key, start, end)

    def getset(self, key, value):
        return self.client.getset(key, value)

    def incr(self, key):
        return self.client.incr(key)

    def incrby(self, key, amount):
        return self.client.incrby(key, amount)

    def incrbyfloat(self, key, amount):
        return self.client.incrbyfloat(key, amount)

    def mget(self, keys, *args):
        if self.is_cluster:
            if len(args) > 0:
                all_keys = [keys]
                for k in args:
                    all_keys.append(k)
                return self._multi_slot_key_cmd('mget', all_keys, sort_result=True)
            elif isinstance(keys, list):
                if len(keys) == 1:
                    return self.client.get(keys[0])
                return self._multi_slot_key_cmd('mget', keys, sort_result=True)
            else:
                return self.client.get(keys)
        else:
            return self.client.mget(keys, *args)

    def mset(self, *args, **kwargs):
        if self.is_cluster:

            if args:
                if len(args) != 1 or not isinstance(args[0], dict):
                    raise Exception('MSET requires **kwargs or a single dict arg')
            kwargs.update(args[0])

            if len(kwargs) == 0:
                return
            elif len(kwargs) == 1:
                kv = kwargs.items()[0]
                return self.set(kv[0], kv[1])
            else:
                return self._multi_slot_key_cmd('mset', kwargs)
        else:
            return self.client.mset(*args, **kwargs)

    def msetnx(self, *args, **kwargs):
        if self.is_cluster:

            if args:
                if len(args) != 1 or not isinstance(args[0], dict):
                    raise Exception('MSETNX requires **kwargs or a single dict arg')
            kwargs.update(args[0])

            if len(kwargs) == 0:
                return
            elif len(kwargs) == 1:
                kv = kwargs.items()[0]
                return self.setnx(kv[0], kv[1])
            else:
                slots = self._partition_keys(kwargs)
                if len(slots) > 1:
                    raise Exception('MSETNX requires keys to be in the same slot (use hash tag)')
                return self.client.msetnx(kwargs)
        else:
            return self.client.msetnx(*args, **kwargs)

    def set(self, key, value, ex=None, px=None, nx=False, xx=False):
        return self.client.set(key, value, ex=ex, px=px, nx=nx, xx=xx)

    def setbit(self, key, offset, value):
        return self.client.setbit(key, offset, value)

    def setex(self, key, seconds, value):
        return self.client.setex(key, seconds, value)

    def psetex(self, key, milliseconds, value):
        return self.client.psetex(key, milliseconds, value)

    def setnx(self, key, value):
        return self.client.setnx(key, value)

    def setrange(self, key, offset, value):
        return self.client.setrange(key, offset, value)

    def strlen(self, key):
        return self.client.strlen(key)

    def delete(self, *keys):
        return self.client.delete(*keys)

    def exists(self, key):
        return self.client.exists(key)

    def expire(self, key, seconds):
        return self.client.expire(key, seconds)

    def expireat(self, key, timestamp):
        return self.client.expireat(key, timestamp)

    def pexpire(self, key, milliseconds):
        return self.client.pexpire(key, milliseconds)

    def pexpireat(self, key, timestamp):
        return self.client.pexpireat(key, timestamp)

    def pttl(self, key):
        return self.client.pttl(key)

    def ttl(self, key):
        return self.client.ttl(key)

    def type(self, key):
        return self.client.type(key)

    def hdel(self, key, *fields):
        return self.client.hdel(key, *fields)

    def hexists(self, key, field):
        return self.client.hexists(key, field)

    def hget(self, key, field):
        return self.client.hget(key, field)

    def hincrby(self, key, field, amount):
        return self.client.hincrby(key, field, amount)

    def hincrbyfloat(self, key, field, amount):
        return self.client.hincrbyfloat(key, field, amount)

    def hgetall(self, key):
        return self.client.hgetall(key)

    def hkeys(self, key):
        return self.client.hkeys(key)

    def hlen(self, key):
        return self.client.hlen(key)

    def hmget(self, key, fields, *args):
        return self.client.hmget(key, fields, *args)

    def hmset(self, key, mapping):
        return self.client.hmset(key, mapping)

    def hset(self, key, field, value):
        return self.client.hset(key, field, value)

    def hsetnx(self, key, field, value):
        return self.client.hsetnx(key, field, value)

    def hvals(self, key):
        return self.client.hvals(key)

    def lindex(self, key, index):
        return self.client.lindex(key, index)

    def linsert(self, key, before, pivot, value):
        return self.client.linsert(key, before, pivot, value)

    def llen(self, key):
        return self.client.llen(key)

    def lpop(self, key):
        return self.client.lpop(key)

    def lpush(self, key, *values):
        return self.client.lpush(key, *values)

    def lpushx(self, key, value):
        return self.client.lpushx(key, value)

    def lrange(self, key, start, stop):
        return self.client.lrange(key, start, stop)

    def lrem(self, key, count, value):
        return self.client.lrem(key, count, value)

    def lset(self, key, index, value):
        return self.client.lset(key, index, value)

    def ltrim(self, key, start, stop):
        return self.client.ltrim(key, start, stop)

    def rpop(self, key):
        return self.client.rpop(key)

    def rpush(self, key, *values):
        return self.client.rpush(key, *values)

    def rpushx(self, key, value):
        return self.client.rpushx(key, value)

    def sadd(self, key, *members):
        return self.client.sadd(key, *members)

    def scard(self, key):
        return self.client.scard(key)

    def sismember(self, key, member):
        return self.client.sismember(key, member)

    def smove(self, source, destination, member):
        return self.client.smove(source, destination, member)

    def smembers(self, key):
        return self.client.smembers(key)

    def spop(self, key):
        return self.client.spop(key)

    def srandmember(self, key, count=None):
        return self.client.srandmember(key, count)

    def srem(self, key, *members):
        return self.client.srem(key, *members)

def test_redis():
    url = "127.0.0.1:6379"
    rc = RedisClient(uri=url)
    re = rc.hset(key="test_key", field="test_field", value="test_value")
    logging.info(re)

if __name__ == '__main__':
    test_redis()
```

