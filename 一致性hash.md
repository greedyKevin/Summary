## 一致性hash

* 解决了分布式缓存的问题
* 在移除或者添加一个服务器时，能够尽可能小地改变已存在的服务请求与处理请求服务器之间的映射关系

### 过程

* 计算节点与该节点的虚拟节点hash值
* 将hash值插进圆环里
* 计算缓存的key值顺时针找到该缓存节点

### 代码

```java
package com.kevin.test.util;


import com.google.common.hash.HashCode;
import com.google.common.hash.HashFunction;
import com.google.common.hash.Hashing;
import org.springframework.util.StringUtils;

import java.nio.charset.StandardCharsets;
import java.util.LinkedList;
import java.util.List;
import java.util.SortedMap;
import java.util.TreeMap;

/**
 * 一致性hash
 *
 * @author huang jiahui
 * @date 2022/5/7 11:43
 */
public class ConsistentHash {

    static int VIRTUAL_NODE_NUM = 20;
    static final HashFunction HASH_FUNCTION = Hashing.crc32();
    static final List<String> SERVERS = new LinkedList<>();
    static final SortedMap<Integer, String> HASH_RING = new TreeMap<>();

    public static void addNode(String node) {
        if (SERVERS.contains(node)) {
            return;
        }
        // todo 验证IP地址
        SERVERS.add(node);
        HASH_RING.put(hash(node), node);

        // 添加虚拟节点
        for (int i = 0; i < VIRTUAL_NODE_NUM; i++) {
            int hash = hash(node.concat(i + "VIRTUAL_NODE") + i);
            HASH_RING.put(hash, node);
        }
    }


    public static void deleteNode(String node) {
        if (!SERVERS.contains(node)) {
            return;
        }

        SERVERS.remove(node);
        HASH_RING.remove(hash(node));
        // 移除虚拟节点
        for (int i = 0; i < VIRTUAL_NODE_NUM; i++) {
            int hash = hash(node.concat(i + "VIRTUAL_NODE") + i);
            HASH_RING.remove(hash);
        }
    }

    public static String getServer(String key) {
        if (SERVERS.isEmpty()) {
            return null;
        }

        int hash = hash(key);

        SortedMap<Integer, String> sortedMap = HASH_RING.tailMap(hash);

        if (sortedMap.isEmpty()) {
            return HASH_RING.get(HASH_RING.firstKey());
        }

        return HASH_RING.get(sortedMap.firstKey());
    }

    public static void setVirtualNodeNum(int n) {
        if (n < 0) {
            throw new IllegalArgumentException("n must be greater than 0");
        }

        if (n == VIRTUAL_NODE_NUM) {
            return;
        }

        if(n > VIRTUAL_NODE_NUM){
            for (String node:SERVERS){
                for (int i = VIRTUAL_NODE_NUM; i < n; i++) {
                    int hash = hash(node.concat(i + "VIRTUAL_NODE") + i);
                    HASH_RING.put(hash, node);
                }
            }
        }else{
            for (String node:SERVERS){
                for (int i = VIRTUAL_NODE_NUM - 1; i >= n; i--) {
                    int hash = hash(node.concat(i + "VIRTUAL_NODE") + i);
                    HASH_RING.remove(hash);
                }
            }
        }

        VIRTUAL_NODE_NUM = n;
    }

    public static List<String> getServers(){
        return SERVERS;
    }

    public static int getVirtualNodeNum() {
        return VIRTUAL_NODE_NUM;
    }

    public static SortedMap<Integer, String> getHashRing() {
        return HASH_RING;
    }

    public static void reset(){
        VIRTUAL_NODE_NUM = 20;
        SERVERS.clear();
        HASH_RING.clear();
    }

    static final int IPv4_LEN = 4;

    private static int hash(String key) {
        HashCode hashCode = HASH_FUNCTION.hashBytes(key.getBytes(StandardCharsets.UTF_8));

        return hashCode.hashCode();
    }

    private static boolean isIP(String ip){
        if (!StringUtils.hasText(ip)){
            throw new IllegalArgumentException("ip is empty");
        }

        String[] split = ip.split("\\.");

        return split.length == IPv4_LEN;
    }
}

```

