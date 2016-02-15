title: openresty ip limit times
date: 2016-02-01 20:15:27
tags:
categories: openresty 
description:
---


# openresty restrict ip access times

* 简单写了些基本的结构
* 先把每个ip的访问次数放在一个内存中,
* 每个ip的访问限制次数
* 达到一定的阈值, 进行控制
* 后面也写了一些admin的操作
  *  解除某个ip的限制
  *  得到某个ip的已经记录的访问次数
  *  清空所有的ip限制
  *  得到部分ip次数(高版本openresty可以实现)


```
server {
	listen 8080;
	server_name wmr.com;


	error_log logs/wmr-error.log debug;
	location / {
	access_by_lua '
-- local ip = ngx.var.binary_remote_addr
ip = ngx.var.remote_addr
limit = ngx.shared.limit
req,flag = limit:get(ip)
if req then
    if req < 10 then
	limit:incr(ip,1)
	return  ngx.exec("/ok")
    elseif req >= 10 and  req < 15 then 
        limit:incr(ip,1)
	return 	ngx.exec("/limit")
    else 
        ngx.exit(ngx.HTTP_SERVICE_UNAVAILABLE)
    end
else
    limit:set(ip,1,60)
end
	';
	}

	location ~ ^/limit {
	internal;
	content_by_lua '
		ngx.say("ip will limited if visit more frequent");
	';
	}
	
	location ~ ^/ok {
	internal;
	content_by_lua '
		ngx.say("access ok");
	';
	}


##curl -i  "wmr.com/delete?ip=127.0.0.1"  -x 127.0.0.1:8080
	location ~ ^/delete {
	allow 127.0.0.1;
	deny all;
content_by_lua '
	local limit = ngx.shared.limit
	local args = ngx.req.get_uri_args()
	for  key,val  in  pairs(args)
	do
		if key == "ip" then
			target_ip = val
		end
		if target_ip == nil then
			ngx.say("no input key ")
		end

		if target_ip ~= ""  then
			ngx.say(target_ip)
		 	limit:set(target_ip, nil)
			os.execute("sleep 0.5")
			limit:get(target_ip)
			ngx.exit(ngx.HTTP_OK)
		else
			ngx.say("key error")
		end

		-- if type(val) == "table"  then 
		--	ngx.say(key,":",table.concat(val,","))
		-- else
		-- 	ngx.say(key,":",val)
		-- end
	end

	';
	}



##curl -i  "wmr.com/get?ip=127.0.0.1"  -x 127.0.0.1:8080
	location ~ ^/get {
	allow 127.0.0.1;
	deny all;
	access_by_lua '
		local limit = ngx.shared.limit
		local args = ngx.req.get_uri_args()
		for  key,val  in  pairs(args)
		do
			if key == "ip" then
				target_ip = val
				ngx.say(target_ip)
				local req,flag = limit:get(target_ip)
				ngx.say(req)
				ngx.exit(ngx.HTTP_OK)
			end
		end
	';
	}
	

##curl -i wmr.com/flushall -x 127.0.0.1:8080
	location ~ ^/flushall$ {
	access_by_lua '
		local limit = ngx.shared.limit
		limit:flush_all()
		ngx.say("flush all ip")
		ngx.exit(ngx.HTTP_OK)
	';
	}

##early version not support 
	location ~ ^/getall$ {
	access_by_lua '
		local limit = ngx.shared.limit
		limit:get_keys(5)
		ngx.exit(ngx.HTTP_OK)
	';
	}


}
```
