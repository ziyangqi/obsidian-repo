
使用OkHttp默认的超时时间是10s


``` JAVA
OkHttpClient client = new OkHttpClient();
  client = new OkHttpClient.Builder()
                .connectTimeout(10, TimeUnit.SECONDS)
                .readTimeout(20, TimeUnit.SECONDS)
                .build();
```