### 使用redis记录节点

### 	用 lua_shared_dict(map) 记录redis的节点信息

```lua
content_by_lua_block:

local redis = require "resty.redis"
local red = redis:new()
red.connect("172.18.27.213",6379)
red.auth("hello123456")
red.select(13)
```



### 计算hash(手动)，确定调用哪一个节点，最好使用consistent hash

```lua
ngx.crc32_long
```



### 在upstream模块使用

```lua
balancer_by_lua_block:

local balancer = require "ngx.balancer"
balancer.set_current_peer("172.18.27.213", 8080)

```



