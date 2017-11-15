# H5页面获取微信用户的openid

## 准备工作

### 配置网页授权域名

进入微信公众平台(mp.weixin.qq.com)
设置->公众号设置->功能设置
设置H5页面所在的域名地址。

### 引导用户进入授权页面

H5页面要先跳转到[授权URL(https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect)](https://open.weixin.qq.com/connect/oauth2/authorize?appid=APPID&redirect_uri=REDIRECT_URI&response_type=code&scope=SCOPE&state=STATE#wechat_redirect)
授权成功后，页面将跳转到`redirect_url/?code=CODE&state=STATE`。

参数说明

参数 |是否必须|说明
-----|--------|----
appid|是	  |公众号的唯一标识
redirect_uri|是|授权后重定向的回调链接地址，请使用urlEncode对链接进行处理
response_type|是|返回类型，请填写code
scope|是|应用授权作用域，snsapi_base （不弹出授权页面，直接跳转，只能获取用户openid），snsapi_userinfo（弹出授权页面，可通过openid拿到昵称、性别、所在地。并且，即使在未关注的情况下，只要用户授权，也能获取其信息）
state|否|重定向后会带上state参数，开发者可以填写a-zA-Z0-9的参数值，最多128字节
#wechat_redirect|是|无论直接打开还是做页面302重定向时候，必须带此参数

### 通过返回的code值获取授权access_token
请求下面链接获取access_token
[https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code](https://api.weixin.qq.com/sns/oauth2/access_token?appid=APPID&secret=SECRET&code=CODE&grant_type=authorization_code)

参数说明
<table>
 <tr>
  <th>参数</th><th>是否必须</th><th>说明</th>
 </tr>
 <tr>
  <th>appid</th><th>是</th><th>公众号的唯一标识</th>
 </tr>
 <tr>
  <th>secret</th><th>是</th><th>公众号的appsecret</th>
 </tr>
 <tr>
  <th>code</th><th>是</th><th>填写第一步获取的code参数</th>
 </tr>
 <tr>
  <th>grant_type</th><th>是</th><th>填写为authorization_code</th>
 </tr>
</table>

正确时，返回：

> { "access_token":"ACCESS_TOKEN",    
 "expires_in":7200,    
 "refresh_token":"REFRESH_TOKEN",    
 "openid":"OPENID",    
 "scope":"SCOPE" } 

参数说明
<table>
 <tr>
  <th>参数</th><th>说明</th>
 </tr>
 <tr>
  <th>access_token</th><th>网页授权接口调用凭证,注意：此access_token与基础支持的access_token不同</th>
 </tr>
 <tr>
  <th>expires_in</th><th>access_token接口调用凭证超时时间，单位（秒）</th>
 </tr>
 <tr>
  <th>refresh_token</th><th>用户刷新access_token</th>
 </tr>
 <tr>
  <th>openid</th><th>用户唯一标识，请注意，在未关注公众号时，用户访问公众号的网页，也会产生一个用户和公众号唯一的OpenID</th>
 </tr>
 <tr>
  <th>scope</th><th>用户授权的作用域，使用逗号（,）分隔 </th>
 </tr>
</table>

错误时，返回：

> {"errcode":40029,"errmsg":"invalid code"} 



**遇到的问题：此公众号没有这些scope权限**

1. 检查公众号类型，订阅号是没有此权限的

2. 确认公众号有没有进行认证

3. H5页面的域名是否在微信公众平台进行授权

4. 检查授权的回调地址内`scope`参数


