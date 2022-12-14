# 非对称加密
- 效率低下, 一般不加密大数据.
- 可以与对称加密结合使用.
  - 接收方生成`非对称加密的公钥和私钥`, 把公钥传给发送方
  - 发送方使用`非对称加密的公钥`加密`对称性加密的密钥`, 并把`加密后的密钥`发送给接收方;
  - 接收方使用`非对称加密的私钥`解密获得`对称性加密的密钥`.
  - 保证了密钥没有泄漏后, 之后通信都使用`对称性加密`即可

## 如何保证公钥的真实性
- 证书公钥(PKI): 
  - 第三方监管部门(CA)的数字证书
- 标识公钥(IBC): 
  - 公钥即身份标识本身(身份证, 手机号, 家庭地址等)
  - 私钥由密管中心(KGC)下发
  - 更换`密钥对`就意味着需要更换身份标识, 不方便
- 无证书公钥(CL-PKC): 
  - 通过`KGC生成一个**部分私钥**` + `用户选取的**秘密值**`联合生成`用户私钥`. 
  - 密管中心(KGC)只托管了`部分私钥`, 即使泄漏也不会出问题. (不需要证书, 且不会出现私钥托管问题) 
## 加密
> 公钥加密, 私钥解密
主要是数据的`保密性`, 但不能保证数据的`正确性`
公钥加密并发送数据, 私钥接收并解密数据
保证了数据不会被别人得到. 即使被拦截也无法解析.
不能保证接收的数据是正确的, 只能保证发送的数据不被解析出来.

## 验签
> 私钥签名, 公钥验证
> > 私钥加密并发送数据, 公钥接收并解密数据
- `不可否认性`
  - 保证了数据是由签发者自己签名发送的, 签名者不能否认.
    - 除非签名者的私钥被窃取了.
  - 私钥加密发送的数据, 只有对应的公钥才能解密. 换句话说, `既然我用你给的公钥解开了数据, 那这条数据就肯定是你发送的, 你无法否认`
- `完整性`
  - 保证了接收到的数据未曾被修改过.
  - 所有人(有公钥的)都能看到签名内容, 所以不应该把私密信息作为签名发送
  - `你给我的公钥只能解开你的私钥加密生成的签名, 如果别人篡改了数据并重新加密, 即使算法一样, 没有你的私钥, 加密出来的新签名, 我也无法解析. 所以我解析出来的数据一定是你发送的数据` 

## 身份认证
- JWT: `header.payload.signature`
  - client输入username/password, server验证身份
  - server验证通过后, 通过私钥加密`header.payload`生成`signature`并拼装为`JWT`, 发送给clent;
  - client可以通过公钥解密, 拿到数据, 如账号角色, 权限等
  - client在后续请求时将`JWT`传给server.
  - server再次使用私钥加密`header.payload`, 得到`JWT`. 和client传入的`JWT`做对比. 如果一模一样, 就代表`header.payload`没有被篡改, 是可以信任的. 
  - 然后读取`payload`里的用户名, 就能确认请求者的身份了.