# Openresty 調用 lua 腳本

## 01. 直接調用
```conf
location /lua {
    default_type text/html;
    content_by_lua 'ngx.say("<h1>Hello, This is lua output !</h1>")';
}
```

## 02. 調用 lua 腳本
```conf
location /lua_file {
    default_type text/html;
    content_by_lua_file conf/001.lua; # 調用 lua 腳本
}
```

#### 001.lua
```lua
ngx.say("<h1>Hello! Here is 001.lua file !</h1>")
```

## 03. 調用 conf 並且執行 lua 腳本文件
```conf
location /include_conf_lua {
    default_type text/html;
    include lua.conf;
}
```

#### lua.conf
```conf
content_by_lua_file conf/002.lua;
```
#### 002.lua
```lua
ngx.say("<h1>Hello! Here is 002.lua file !</h1>")
```

## 04. 調用 lua 腳本: content_by_lua_block
```conf
location /lua_block {
    default_type text/html;
    content_by_lua_block {
        ngx.say("<h1>Hello! I'm lua block !</h1>")
    }
}
```

## 05. 取 uri 變量
```conf
location /lua_var {
    default_type text/html;
    content_by_lua_block {
        ngx.say(ngx.var.arg_username) # ngx 的對象中 var -> arg_{參數}，如這行為例， /lua_var?username=openresty 的話，則頁面會產生 openresty 的文字。
    }
}
```

## 06. 取多變量
```lua
local uri_args = ngx.req.get_uri_args()

for k, v in pairs(uri_args) do
    if type(v) == "table" then
        ngx.say(k, ": ", table.concat(v, ", "), "<br/>")
    else
        ngx.say(k, ": ", v, "<br/>")
    end
end
```

## 07. 取 Headers
```lua
local headers = ngx.req.get_headers()
ngx.say("Host: ", headers["Host"], "<br/>")
ngx.say("User-Agent: ", headers["user-agent"], "<br/>")
ngx.say("User-Agent: ", headers.user_agent, "<br/>")
ngx.say("---------------------------------------------", "<br/>")

for k, v in pairs(headers) do
    if type(v) == "table" then
        ngx.say(k, ": ", table.concat(v, ", "), "<br/>")
    else
        ngx.say(k, ": ", v, "<br/>")
    end
end
```