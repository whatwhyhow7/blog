---
layout: post
title: Java 中发送 http 请求
tags: [Back-End]
---

**摘要：**在 Java 中发送 http 请求是一个很常见的需求，但是经常会有各种限制和条件，比如：在 https 协议下发送、使用 json 格式发送等等。这里把之前用过的一些场景的实现记录一下，包含 `HttpClient``、HttpClient 连接池`以及 `URLConnection` 的实现，以免去再次查找之苦。
{:.message}

用到的 jar：

<a href="/blog/assets/jar/httpclient-4.2.1.jar" target="_blank">httpclient-4.2.1.jar</a>

<a href="/blog/assets/jar/httpcore-4.2.1.jar" target="_blank">httpcore-4.2.1.jar</a>

具体的实现：

```java
public class HttpUtil {

    private static final String APPLICATION_JSON = "application/json";

    // HttpClient 连接池
    private class HttpClientPool {
        private static PoolingClientConnectionManager cm;
        private static DefaultHttpClient httpClient;
        private static final int CONNECTION_TIMEOUT = 20; // 连接超时时间
        private static final int SO_TIMEOUT = 20; // 连接成功后的等待超时时间
        private static final long CONN_MANAGER_TIMEOUT = 500L; // 连接不够用的时候等待超时时间，一定要设置，而且不能太大

        static {
            SchemeRegistry schemeRegistry = new SchemeRegistry();
            schemeRegistry.register(new Scheme("http", 80, PlainSocketFactory.getSocketFactory()));
            cm = new PoolingClientConnectionManager(schemeRegistry);
            cm.setMaxTotal(10000); // 设置整个连接池最大连接数
            cm.setDefaultMaxPerRoute(100); // 每个 route 默认的连接数

            IdleConnectionMonitorThread connEvictor = new IdleConnectionMonitorThread(cm);
            connEvictor.start();

            HttpParams params = new BasicHttpParams();
            params.setIntParameter(CoreConnectionPNames.CONNECTION_TIMEOUT, CONNECTION_TIMEOUT);
            params.setIntParameter(CoreConnectionPNames.SO_TIMEOUT, SO_TIMEOUT);
            params.setLongParameter(ClientPNames.CONN_MANAGER_TIMEOUT, CONN_MANAGER_TIMEOUT);
            params.setBooleanParameter(CoreConnectionPNames.STALE_CONNECTION_CHECK, true);
            httpClient = new DefaultHttpClient(cm,params);
            httpClient.setHttpRequestRetryHandler(new DefaultHttpRequestRetryHandler(0, false));
        }

        public static HttpClient getHttpClient() {
            return httpClient;
        }
    }

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

    // 使用 HttpClient 发送 POST 请求，支持普通格式或 json 格式
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

            // https 协议使用 new SSLClient()，http 协议 使用 new DefaultHttpClient()。连接池使用 HttpClientPool.getHttpClient()
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

    // 使用 HttpClient 发送 GET 请求
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

    // 使用 URLConnection 发送 post 请求
    public String postWithURLConnection(String address, String param) {
        PrintWriter out = null;
        BufferedReader in = null;
        String result = "";
        try {
            URL url = new URL(address);
            URLConnection conn = url.openConnection();
            conn.setRequestProperty("accept", "*/*");
            conn.setRequestProperty("connection", "Keep-Alive");
            conn.setConnectTimeout(20);
            conn.setReadTimeout(20);
            conn.setDoOutput(true);
            conn.setDoInput(true);
            out = new PrintWriter(conn.getOutputStream());
            out.print(param);
            out.flush();
            in = new BufferedReader(new InputStreamReader(conn.getInputStream()));
            String line;
            while ((line = in.readLine()) != null) {
                result += line;
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                if (out != null) {
                    out.close();
                }
                if (in != null) {
                    in.close();
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
        return result;
    }
}
```