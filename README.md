# OPENRESTY 嘗試使用 LUA

- [Lua错误摘要](https://www.cnblogs.com/ykpkris/p/13180882.html)


### 遇到的問題
> 跑 mysql 的時候有發生問題，嘗試解決但是解法有點...。 原因好像是 lua 沒解析到對應的域名。要使用 IP 去連入 SQL


# RESTY.UUID
```sh
luarocks install lua-resty-uuid

```
### LUA
```lua
local uuid = require "resty.uuid"

-- 生成 UUID 作為 Session ID
local sessionID = uuid.generate()

ngx.header["identify-uuid"] = sessionID
```


# RESTY.RANDOM

```lua
local resty_random = require "resty.random"

-- 生成一個隨機的 Session ID
local function generate_session_id(length)
    local bytes = resty_random.bytes(length)
    return ngx.encode_base64(bytes):sub(1, length)
end

-- 設定 Session ID 的長度，這裡設定為 32 字符
local session_id_length = 32

-- 生成 Session ID
local session_id = generate_session_id(session_id_length)

-- 將 Session ID 存儲在 cookie 中，並在回應中返回給客戶端
ngx.header["Set-Cookie"] = "sessionID=" .. session_id .. "; Path=/"

-- 將 Session ID 返回給客戶端，如果需要的話
ngx.say("Session ID: " .. session_id)
```


# lua-resty-redis
```sh
luarocks install lua-resty-redis
```

```lua
ngx.say("Executing Lua script");

local redis = require "resty.redis"
local red = redis:new()
red:set_timeout(1000) -- 設定 timeout (ms)

local ok, err = red:connect("172.20.0.2", 6379)
if not ok then
    ngx.say("failed to connect: ", err)
    return
end


local res, err = red:get("my_key")
if not res then
    ngx.say("failed to get my_key: ", err)
    return
end

ngx.say("Value from Redis: ", res)
red:colos()
```