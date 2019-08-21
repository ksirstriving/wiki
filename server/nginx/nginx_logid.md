# 为nginx添加request_id

> 为了方便追踪日志，从nginx接收请求时，就生成一个uuid，记录在日志中，这个uuid会在这次> 请求中贯穿始终，方便排查问题。  
> nginx1.11.0后支持

```bash
# 在fastcgi_params处增加
fastcgi_param X_REQUEST_ID $request_id;
```
