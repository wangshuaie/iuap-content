**1:RestAPI签名**

**1.1:在请求端，根据URL参数（含ts值）、请求端IP地址、表单参数以及Content-Length的大小进行生成签名sign**

	SignProp prop = SignPropGenerator.genSignProp(带ts的完整URL);
	signProp.setIp(请求端IP地址);
	/************post请求时需要**************/
	signProp.setPostParamsStr(PostParamsHelper.genParamsStrByMap(POST表单参数Map));
	signProp.setContentLength(Content-Length的值);
	/***************************************/
	String sign = ClientSignFactory.getSigner().sign(prop);


**1.2:在服务端，首先比较当前时间ts’与ts的大小，如果其差值不大于DEFAULT_EXPIRED，则从HTTP请求中获得时间戳ts、URL参数、请求的来源IP地址、表单参数以及ContentLength的大小生成sign’。此时如果sign与sign’相同，则签名验证通过。**

	SignProp prop = SignPropGenerator.genSignProp(带ts的完整URL);
	signProp.setIp(请求端IP地址);
	/************post请求时需要**************/
	signProp.setPostParamsStr(PostParamsHelper.genParamsStrByMap(HttpServletRequest对象));
	signProp.setContentLength(HttpServletRequest中取得的Content-Length的值);
	/***************************************/
	DemoServerVirifyFactory factory = new DemoServerVirifyFactory();
	Boolean result = factory.getVerifier(appid).verify(sign, prop);


**1.3:请求端的签名凭证由服务器端生成：对不同的请求端，都由服务端为其生成唯一的签名凭证，服务端维护一份，客户端维护一份。在请求端，签名凭证的存储位置需要在application.properties中进行配置**

	#客户端身份文件路径
	client.credential.path=/authfile.txt


**1.4:由于服务端不止维护一个请求端的签名凭证，所以当签名的请求到来时，需要根据appId得到与之对应的请求端签名凭证。即：在验签时，使用者需继承ServerVerifyFactory，重写获取某一请求端签名凭证的逻辑genCredential()**

	/**
	 * 
	 * 服务端必须实现ServerVerifyFactory，用来获取某个请求端的签名凭证
	 *
	 */
	class DemoServerVirifyFactory extends ServerVerifyFactory {
		@Override
		protected Credential genCredential(String appId) {
			try {
				// 应该根据appid查询并构造
				return ClientCredentialGenerator
						.loadCredential(PropertyUtil
								.getPropertyByKey(AuthConstants.CLIENT_CREDENTIAL_PATH));
			} catch (UAPSecurityException e) {
				e.printStackTrace();
			}
			return null;
		}
	}

**2:编码和解码、加密和解密**

**2.1:数据编码分为多种形式，如html编码、javascript编码、css编码、sql编码等，它们主要是用来解决各类注入问题：xss注入、sql注入。除此之外，还有Base64编码，常用来将非ASCII字符的数据转换成ASCII字符**

	String safe =IUAPESAPI.encoder().htmlEncode( “input”);
	String safe =IUAPESAPI.encoder().htmlAttributeEncode ( “input”);
	String safe =IUAPESAPI.encoder().javaScriptEncode ( “input”);
	String safe =IUAPESAPI.encoder().urlEncode ( “input”);
	String safe =IUAPESAPI.encoder().cssEncode ( “input”);
	String queryCondition = IUAPESAPI.encoder().sqlPreparedString(conditionStr,
				paras, DatabaseCodec.ORACLE);
	String base64Text = IUAPESAPI.encoder().encodeForBase64(bytes);

**2.2:对称加密算法可以用作存储加密，即将数据持久化前对数据进行加密**

	String plainText = "我是被加密的信息!";
	System.out.println("明文：" + plainText);

	String info = IUAPESAPI.encryptor().encrypt(plainText);
	System.err.println("密文：" + info);

	String decryptorInfo = IUAPESAPI.encryptor().decrypt(info);
	System.err.println("解密后得到明文：" + decryptorInfo);


**2.3:非对称加密算法可以用作传输加密，即数据从请求端发送到服务端时，对敏感数据用公钥进行加密，在服务端用私钥进行解密，这里我们提供了RSAUtils工具类进行RSA加密**

	String password = "123456";
	KeyPair gk = RSAUtils.generateKeyPair();
	System.out.println(gk.toString());
		
	RSAPublicKey publicKey = RSAUtils.getDefaultPublicKey();
	String hexM = Hex.encodeHexString(publicKey.getModulus().toByteArray());
	String hexE = Hex.encodeHexString(publicKey.getPublicExponent().toByteArray());
		
	System.out.println("hexM:" + hexM);
	System.out.println("hexE:" + hexE);
		
		
	byte[] encryptBytes = RSAUtils.encrypt(publicKey, password.getBytes());
	String encryptStr = new String(Hex.encodeHex(encryptBytes));
	System.out.println("加密结果1：" + encryptStr);
		
	RSAPublicKey publicKey2 = RSAUtils.getRSAPublidKey(hexM, hexE);
	byte[] encryptBytes2 = RSAUtils.encrypt(publicKey2, password.getBytes());
	String encryptStr2 = new String(Hex.encodeHex(encryptBytes2));
	System.out.println("加密结果2：" + encryptStr2);

**3:更加详细的配置和示例请参考示例工程(DevTool/examples/example_iuap_security)和官网文档**