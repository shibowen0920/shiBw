#### HttpsUtil，代码发送https请求工具类

```java
package com.nantian.utils;

import org.apache.http.HttpEntity;
import org.apache.http.HttpStatus;
import org.apache.http.StatusLine;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.params.ClientPNames;
import org.apache.http.client.params.CookiePolicy;
import org.apache.http.conn.scheme.Scheme;
import org.apache.http.conn.ssl.SSLSocketFactory;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.params.CoreProtocolPNames;
import org.apache.http.util.EntityUtils;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;

import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.security.KeyStore;
import java.security.cert.CertificateFactory;
import java.security.cert.X509Certificate;

@Component
public class HttpsUtil {

    @Value("${Blockchain.certUrl}")
    private String certUrl;


    public String post(String url, String params) throws Exception {
        System.out.println("util-certUrl" + certUrl);
        System.out.println("util-url" + url);
        System.out.println("util-params" + params);
        CloseableHttpClient client = new DefaultHttpClient();
        client.getParams().setParameter(ClientPNames.COOKIE_POLICY, CookiePolicy.BROWSER_COMPATIBILITY);
        client.getParams().setParameter(CoreProtocolPNames.USER_AGENT,
                "    Mozilla/5.0 (Windows NT 6.2; rv:18.0) Gecko/20100101 Firefox/18.0");

        // 获得密匙库
        KeyStore trustStore = KeyStore.getInstance(KeyStore.getDefaultType());
        FileInputStream instream = new FileInputStream(new File(certUrl));
        // 密匙库的密码
        trustStore.load(instream, "123456".toCharArray());
        // 注册密匙库
        SSLSocketFactory socketFactory = new SSLSocketFactory(trustStore);
        // 不校验域名
        socketFactory.setHostnameVerifier(SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
        Scheme sch = new Scheme("https", 443, socketFactory);
        client.getConnectionManager().getSchemeRegistry().register(sch);

        HttpPost httpPost = new HttpPost(url);
        httpPost.setHeader("Accept", "application/json");
        httpPost.setHeader("Content-Type", "application/json");
        httpPost.setHeader("Authorization", params);
        String charSet = "UTF-8";
        StringEntity entity = new StringEntity(params, charSet);
        httpPost.setEntity(entity);
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpPost);
            StatusLine status = response.getStatusLine();
            int state = status.getStatusCode();
            if (state == HttpStatus.SC_OK) {
                HttpEntity responseEntity = response.getEntity();
                String jsonString = EntityUtils.toString(responseEntity);
                return jsonString;
            }
        } finally {
            if (response != null) {
                try {
                    response.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        return null;
    }

    public static X509Certificate getX509CerCate(String cerPath) throws Exception {
        X509Certificate x509Certificate = null;
        CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
        FileInputStream fileInputStream = new FileInputStream(cerPath);
        x509Certificate = (X509Certificate) certificateFactory.generateCertificate(fileInputStream);
        fileInputStream.close();
        System.out.println("读取Cer证书信息...");
        System.out.println("x509Certificate_SerialNumber_序列号___:" + x509Certificate.getSerialNumber());
        System.out.println("x509Certificate_getIssuerDN_发布方标识名___:" + x509Certificate.getIssuerDN());
        System.out.println("x509Certificate_getSubjectDN_主体标识___:" + x509Certificate.getSubjectDN());
        System.out.println("x509Certificate_getSigAlgOID_证书算法OID字符串___:" + x509Certificate.getSigAlgOID());
        System.out.println("x509Certificate_getNotBefore_证书有效期___:" + x509Certificate.getNotAfter());
        System.out.println("x509Certificate_getSigAlgName_签名算法___:" + x509Certificate.getSigAlgName());
        System.out.println("x509Certificate_getVersion_版本号___:" + x509Certificate.getVersion());
        System.out.println("x509Certificate_getPublicKey_公钥___:" + x509Certificate.getPublicKey());
        return x509Certificate;
    }

    public static void main(String[] args) throws Exception {
        getX509CerCate("D:\\\\cert\\\\server.crt");
    }

}
```

