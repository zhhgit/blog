---
layout: post
title: "常用加密算法"
description: 常用加密算法
modified: 2019-08-22
category: Algorithm
tags: [Algorithm]
---

# 一、加密算法

AES，3DES两种对称加密算法

    package cn.zhanghao90.test;

    import org.apache.commons.codec.binary.Base64;
    import javax.crypto.Cipher;
    import javax.crypto.spec.SecretKeySpec;

    public class SymmetricUtil {
    	public static String encrypt3DES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = value.getBytes();
    		byte[] sl = encrypt3DES(valueByte, BytesUtil.hexToBytes(key));
    		String result = Base64.encodeBase64String(sl);

    		return result;
    	}

    	public static byte[] encrypt3DES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("DESede/ECB/PKCS5Padding");
    		c.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key, "DESede"));
    		return c.doFinal(input);
    	}

    	public static String decrypt3DES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = Base64.decodeBase64(value);
    		byte[] sl = decrypt3DES(valueByte, BytesUtil.hexToBytes(key));
    		String result = new String(sl);
    		return result;
    	}

    	public static byte[] decrypt3DES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("DESede/ECB/PKCS5Padding");
    		c.init(Cipher.DECRYPT_MODE, new SecretKeySpec(key, "DESede"));
    		return c.doFinal(input);
    	}

    	public static String encryptAES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = value.getBytes();
    		byte[] sl = encryptAES(valueByte, BytesUtil.hexToBytes(key));
    		String result = Base64.encodeBase64String(sl);

    		return result;
    	}

    	public static byte[] encryptAES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("AES/ECB/PKCS5Padding");
    		c.init(Cipher.ENCRYPT_MODE, new SecretKeySpec(key, "AES"));
    		return c.doFinal(input);
    	}

    	public static String decryptAES(String value, String key) throws Exception {
    		if (null == value || "".equals(value)) {
    			return "";
    		}

    		byte[] valueByte = Base64.decodeBase64(value);
    		byte[] sl = decryptAES(valueByte, BytesUtil.hexToBytes(key));
    		String result = new String(sl);
    		return result;
    	}

    	public static byte[] decryptAES(byte[] input, byte[] key) throws Exception {
    		Cipher c = Cipher.getInstance("AES/ECB/PKCS5Padding");
    		c.init(Cipher.DECRYPT_MODE, new SecretKeySpec(key, "AES"));
    		return c.doFinal(input);
    	}
    	
    }

RSA非对称算法：

    package cn.zhanghao90.test;

    import org.apache.commons.codec.binary.Base64;
    import org.bouncycastle.jce.provider.BouncyCastleProvider;
    import javax.crypto.Cipher;
    import java.io.ByteArrayOutputStream;
    import java.io.UnsupportedEncodingException;
    import java.security.Key;
    import java.security.KeyFactory;
    import java.security.KeyPair;
    import java.security.KeyPairGenerator;
    import java.security.PrivateKey;
    import java.security.PublicKey;
    import java.security.Security;
    import java.security.Signature;
    import java.security.interfaces.RSAPrivateKey;
    import java.security.interfaces.RSAPublicKey;
    import java.security.spec.PKCS8EncodedKeySpec;
    import java.security.spec.X509EncodedKeySpec;
    import java.util.HashMap;
    import java.util.Map;

    public class RSAUtils {

        /**
         * 加密算法RSA
         */
        public static final String RSA_ALGORITHM = "RSA";
        public static final String RSA_ECB_OAEP_PADDING = "RSA/ECB/OAEPPadding";
        public static final String BC_PROVIDER = "BC";

        /**
         * 签名算法
         */
        public static final String SIGNATURE_ALGORITHM = "MD5withRSA";

        /**
         * 获取公钥的key
         */
        public static final String PUBLIC_KEY = "RSAPublicKey";
        /**
         * 获取私钥的key
         */
        public static final String PRIVATE_KEY = "RSAPrivateKey";

        /**
         * 签名长度
         */
        private static final int KEY_SIZE = 2048;
        /**
         * RSA最大加密明文大小
         */
        private static final int MAX_ENCRYPT_BLOCK = 245;
        /**
         * RSA最大解密密文大小
         */
        private static final int MAX_DECRYPT_BLOCK = 256;

        static {
            Security.addProvider(new BouncyCastleProvider());
        }

        /**
         * <P>
         * 私钥解密2048
         * </p>
         *
         * @param encryptedData 已加密数据
         * @param privateKey    私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] decryptByPrivateKey2048(byte[] encryptedData,
                                                     String privateKey) throws Exception {
            byte[] keyBytes = decode64(privateKey);
            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            RSAPrivateKey privateK = (RSAPrivateKey) keyFactory
                    .generatePrivate(pkcs8KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.DECRYPT_MODE, privateK);

            int inputLen = encryptedData.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            for (int i = 0; i < inputLen - offSet; offSet = i * MAX_DECRYPT_BLOCK) {
                byte[] cache;
                if (inputLen - offSet > MAX_DECRYPT_BLOCK) {
                    cache = cipher
                            .doFinal(encryptedData, offSet, MAX_DECRYPT_BLOCK);
                } else {
                    cache = cipher
                            .doFinal(encryptedData, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                ++i;
            }

            byte[] output = out.toByteArray();
            out.close();
            return output;
        }

        /**
         * <p>
         * 公钥解密2048
         * </p>
         *
         * @param encryptedData 已加密数据
         * @param publicKey     公钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] decryptByPublicKey2048(byte[] encryptedData,
                                                    String publicKey) throws Exception {
            byte[] keyBytes = decode64(publicKey);
            X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key publicK = keyFactory.generatePublic(x509KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.DECRYPT_MODE, publicK);

            int inputLen = encryptedData.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段解密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_DECRYPT_BLOCK) {
                    cache = cipher
                            .doFinal(encryptedData, offSet, MAX_DECRYPT_BLOCK);
                } else {
                    cache = cipher
                            .doFinal(encryptedData, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_DECRYPT_BLOCK;
            }
            byte[] decryptedData = out.toByteArray();
            out.close();
            return decryptedData;
        }

        /**
         * <p>
         * 私钥加密2048
         * </p>
         *
         * @param data       源数据
         * @param privateKey 私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] encryptByPrivateKey2048(byte[] data, String privateKey)
                throws Exception {
            byte[] keyBytes = decode64(privateKey);
            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key privateK = keyFactory.generatePrivate(pkcs8KeySpec);
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.ENCRYPT_MODE, privateK);

            int inputLen = data.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段加密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_ENCRYPT_BLOCK) {
                    cache = cipher.doFinal(data, offSet, MAX_ENCRYPT_BLOCK);
                } else {
                    cache = cipher.doFinal(data, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_ENCRYPT_BLOCK;
            }
            byte[] encryptedData = out.toByteArray();
            out.close();
            return encryptedData;
        }

        /**
         * <p>
         * 公钥加密2048
         * </p>
         *
         * @param data      源数据
         * @param publicKey 公钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static byte[] encryptByPublicKey2048(byte[] data, String publicKey)
                throws Exception {
            byte[] keyBytes = decode64(publicKey);
            X509EncodedKeySpec x509KeySpec = new X509EncodedKeySpec(keyBytes);

            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            Key publicK = keyFactory.generatePublic(x509KeySpec);
            // 对数据加密
            Cipher cipher = Cipher.getInstance(RSA_ECB_OAEP_PADDING, BC_PROVIDER);
            cipher.init(Cipher.ENCRYPT_MODE, publicK);

            int inputLen = data.length;
            ByteArrayOutputStream out = new ByteArrayOutputStream();
            int offSet = 0;
            byte[] cache;
            int i = 0;
            // 对数据分段加密
            while (inputLen - offSet > 0) {
                if (inputLen - offSet > MAX_ENCRYPT_BLOCK) {
                    cache = cipher.doFinal(data, offSet, MAX_ENCRYPT_BLOCK);
                } else {
                    cache = cipher.doFinal(data, offSet, inputLen - offSet);
                }
                out.write(cache, 0, cache.length);
                i++;
                offSet = i * MAX_ENCRYPT_BLOCK;
            }
            byte[] encryptedData = out.toByteArray();
            out.close();
            return encryptedData;
        }

        /**
         * <p>
         * 用私钥对信息生成数字签名
         * </p>
         *
         * @param data       已加密数据
         * @param privateKey 私钥(BASE64编码)
         * @return
         * @throws Exception
         */
        public static String sign(byte[] data, String privateKey) throws Exception {
            byte[] keyBytes = decode64(privateKey);

            PKCS8EncodedKeySpec pkcs8KeySpec = new PKCS8EncodedKeySpec(keyBytes);
            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);

            PrivateKey privateK = keyFactory.generatePrivate(pkcs8KeySpec);
            Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM,
                    BC_PROVIDER);
            signature.initSign(privateK);
            signature.update(data);
            return encode64(signature.sign());
        }

        /**
         * <p>
         * 校验数字签名
         * </p>
         *
         * @param data      已加密数据
         * @param publicKey 公钥(BASE64编码)
         * @param sign      数字签名
         * @return
         * @throws Exception
         */
        public static boolean verify(byte[] data, String publicKey, String sign)
                throws Exception {
            byte[] keyBytes = decode64(publicKey);

            X509EncodedKeySpec keySpec = new X509EncodedKeySpec(keyBytes);
            KeyFactory keyFactory = KeyFactory.getInstance(RSA_ALGORITHM,
                    BC_PROVIDER);
            PublicKey publicK = keyFactory.generatePublic(keySpec);
            Signature signature = Signature.getInstance(SIGNATURE_ALGORITHM,
                    BC_PROVIDER);

            signature.initVerify(publicK);
            signature.update(data);
            return signature.verify(decode64(sign));
        }

        /**
         * Created on 2013-5-31
         * <p>
         * Discription:编码
         * </p>
         *
         * @return String
         */
        public static String encode64(byte[] b) {
            return Base64.encodeBase64String(b);
        }

        /**
         * Created on 2013-5-31
         * <p>
         * Discription:解码
         * </p>
         *
         * @return byte[]
         */
        public static byte[] decode64(String s) {
            return Base64.decodeBase64(s);
        }

        /**
         * <p>
         * 获取私钥
         * </p>
         *
         * @param keyMap 密钥对
         * @return
         * @throws Exception
         */
        public static String getPrivateKey(Map<String, Object> keyMap) {
            Key key = (Key) keyMap.get(PRIVATE_KEY);
            return encode64(key.getEncoded());
        }

        /**
         * <p>
         * 获取公钥
         * </p>
         *
         * @param keyMap 密钥对
         * @return
         * @throws Exception
         */
        public static String getPublicKey(Map<String, Object> keyMap) {
            Key key = (Key) keyMap.get(PUBLIC_KEY);
            return encode64(key.getEncoded());
        }

        /**
         * <p>
         * 生成密钥对(公钥和私钥)
         * </p>
         *
         * @return
         * @throws Exception
         */
        public static Map<String, Object> genKeyPair() throws Exception {
            KeyPairGenerator keyPairGen = KeyPairGenerator
                    .getInstance(RSA_ALGORITHM);
            keyPairGen.initialize(KEY_SIZE);

            KeyPair keyPair = keyPairGen.generateKeyPair();
            RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
            RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();

            Map<String, Object> keyMap = new HashMap<String, Object>(2);
            keyMap.put(PUBLIC_KEY, publicKey);
            keyMap.put(PRIVATE_KEY, privateKey);
            return keyMap;
        }

        public static void main(String[] args) throws UnsupportedEncodingException {
            Map<String, Object> keyPair;
            try {
                keyPair = genKeyPair();
                String publicStr = RSAUtils.getPublicKey(keyPair);
                String privateStr = RSAUtils.getPrivateKey(keyPair);

                System.out.println("pub: " + publicStr);
                System.out.println("pri: " + privateStr);

                String text = "aaa";
                byte[] encryped = RSAUtils.encryptByPublicKey2048(text.getBytes(),
                        publicStr);
                byte[] decryped = RSAUtils.decryptByPrivateKey2048(encryped,
                        privateStr);
                System.out.println("text: " + text + ", decry: "
                        + new String(decryped));

                String text2 = "bbb";
                byte[] encryped2 = RSAUtils.encryptByPrivateKey2048(
                        text2.getBytes(), privateStr);
                byte[] decryped2 = RSAUtils.decryptByPublicKey2048(encryped2,
                        publicStr);
                System.out.println("text: " + text2 + ", decry: "
                        + new String(decryped2));

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

    }

# 二、性能测试比较

| 解密方式 | 短字符串（长度4）平均用时（ms）   | 长字符串（长度10000）平均用时（ms） |
| ----- | --------- | ----------- |
| RSA | 82 | 2504 | 
| AES  | 31 | 62 | 
| 3DES  | 30 | 48 |
| 不解密  | 29 | 39 |

# 三、参考

1.[使用Jmeter进行http接口性能测试](https://www.cnblogs.com/star91/p/5059222.html)