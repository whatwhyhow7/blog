---
layout: post
title: Java 中发送 http 请求
tags: [Back-End]
---

**摘要：**在 Java 中发送 http 请求是一个很常见的需求，但是经常会有各种限制和条件，比如：在 https 协议下发送、使用 json 格式发送等等。这里把之前用过的一些场景的实现记录一下，以免去再次查找之苦。
{:.message}

用到的 jar：

[httpclient-4.2.1.jar](/blog/assets/jar/httpclient-4.2.1.jar)

[httpcore-4.2.1.jar](/blog/assets/jar/httpcore-4.2.1.jar)

具体的实现：

```java
public class HttpUtil {

    private static final String APPLICATION_JSON = "application/json";

    // 使用 Https 请求的 HttpClient 类
    private class SSLClient extends DefaultHttpClient {

        public SSLClient() throws Exception {
            super();
            SSLContext ctx = SSLContext.getInstance("TLS");
            X509TrustManager tm = new X509TrustManager() {
                @Override
                public void checkClientTrusted(X509Certificate[] chain, String authType) throws CertificateException {}

                @Override
                public void checkServerTrusted(X509Certificate[] chain, String authType) throws CertificateException {}

                @Override
                public X509Certificate[] getAcceptedIssuers() {
                    return null;
                }
            };
            ctx.init(null, new TrustManager[]{tm}, null);
            SSLSocketFactory ssf = new SSLSocketFactory(ctx,SSLSocketFactory.ALLOW_ALL_HOSTNAME_VERIFIER);
            ClientConnectionManager ccm = this.getConnectionManager();
            SchemeRegistry sr = ccm.getSchemeRegistry();
            sr.register(new Scheme("https", 443, ssf));
        }
    }

    // 使用普通格式发送 POST 请求
    private HttpPost setMapHttpPost(HttpPost httpPost, Map<String,String> map, String charset) throws Exception {
        List<NameValuePair> list = new ArrayList<NameValuePair>();
        Iterator iterator = map.entrySet().iterator();
        while (iterator.hasNext()) {
            Entry<String, String> elem = (Entry<String, String>) iterator.next();
            list.add(new BasicNameValuePair(elem.getKey(), elem.getValue()));
        }
        if (list.size() > 0) {
            UrlEncodedFormEntity entity = new UrlEncodedFormEntity(list, charset);
            httpPost.setEntity(entity);
        }
        return httpPost;
    }

    // 使用 json 格式发送 POST 请求
    private HttpPost setJsonHttpPost(HttpPost httpPost, JSONObject jsonObj) throws Exception {
        StringEntity entity = new StringEntity(jsonObj.toString());
        entity.setContentType(APPLICATION_JSON);
        httpPost.addHeader(HTTP.CONTENT_TYPE, APPLICATION_JSON);
        httpPost.setEntity(entity);
        return httpPost;
    }

    // 使用普通格式或 json 格式发送 POST 请求
    public String doPost(String url, Map<String,String> map, JSONObject jsonObj, String charset) {
        String result = null;
        try {
            HttpPost httpPost = new HttpPost(url);
            if (map != null && jsonObj == null) {
                httpPost = setMapHttpPost(httpPost, map, charset);
            } else if (map == null && jsonObj != null) {
                httpPost = setJsonHttpPost(httpPost, jsonObj);
            } else {
                return result;
            }

            HttpClient httpClient = new SSLClient();
            HttpResponse response = httpClient.execute(httpPost);
            if (response != null && response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                if (entity != null) {
                    result = EntityUtils.toString(entity, charset);
                }
            }
        } catch(Exception e) {
            e.printStackTrace();
        }
        return result;
    }

    // 发送 GET 请求
    public String doGet(String url, String charset) {
        String result = null;
        try {
            HttpParams params = new BasicHttpParams();
            HttpConnectionParams.setConnectionTimeout(params, 5000);
            HttpConnectionParams.setSoTimeout(params, 5000);
            HttpClient httpclient = new DefaultHttpClient(params);
            HttpGet httpget = new HttpGet(url);
            HttpResponse response = httpclient.execute(httpget);
            if (response != null && response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                if (entity != null) {
                    result = EntityUtils.toString(entity, charset);
                }
            }
        } catch(Exception e) {
            e.printStackTrace();
        }
        return result;
    }
}
```