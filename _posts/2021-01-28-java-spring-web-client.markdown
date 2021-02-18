---
layout: post
title:  "Java Spring Web Client"
date:   2021-01-28 00:00:00 +0800
categories: Java
tags: 
  - "Java"
  - "Spring"
  - "Web Client"
---

Libraries needed

{% highlight xml %}
<dependency>
			<groupId>io.projectreactor.netty</groupId>
			<artifactId>reactor-netty</artifactId>
			<version>0.8.3.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>io.projectreactor</groupId>
			<artifactId>reactor-core</artifactId>
			<version>3.2.3.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webflux</artifactId>
			<version>5.2.10.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>io.projectreactor.addons</groupId>
			<artifactId>reactor-extra</artifactId>
			<version>3.2.0.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>io.netty</groupId>
			<artifactId>netty-all</artifactId>
			<version>4.1.53.Final</version>
		</dependency>
{% endhighlight %}

WebClientWebServiceClientFactory.java
{% highlight java %}

    protected WebClient createConcreteClient(C model) {
        HttpClient httpClient = HttpClient.create();
        try {
            SslContext sslContext =
                    SslContextBuilder.forClient().trustManager(InsecureTrustManagerFactory.INSTANCE).build();
            httpClient = httpClient.secure(s -> s.sslContext(sslContext));
        } catch (SSLException e) {
            LOG.error("SSLException thrown. ", e);
        }
        httpClient = httpClient
                .tcpConfiguration(
                        tcpClient -> tcpClient.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, getConnectionTimeout(model))
                )
                .keepAlive(false)
                .wiretap(true);

        return WebClient.builder().baseUrl(getUrl(model))
                .clientConnector(new ReactorClientHttpConnector(httpClient))
                .build();
    }
{% endhighlight %}

ApiService.java
{% highlight java %}

        Object failedResponseData = new Object();
        failedResponseData.setResCode("0000");

        LOG.debug("Sending request to {} ; Request Body {}", path, formData.toString());
        Object response = webServiceClientFactory.createClient(getConfiguration())
                .post()
                .uri(uriBuilder -> uriBuilder.path(path).build())
                .body(BodyInserters.fromFormData(formData))
                .retrieve()
                .onStatus(httpStatus ->
                                Boolean.TRUE.equals(httpStatus.is4xxClientError()) ||
                                        Boolean.TRUE.equals(httpStatus.is5xxServerError()),
                        clientResponse -> {
                            LOG.error("Error Response data : {}", clientResponse.bodyToMono(String.class).block());
                            return Mono.error(new Exception("Failed to call API."));
                        })
                .bodyToMono(Object.class)
                .doOnError(e -> LOG.error("Unexpected error occurs. ", e))
                .doOnNext(d -> LOG.debug("Response data - {}", serialize(d)))
                .retryWhen(
                        Retry.any().fixedBackoff(Duration.ofMillis(getRetryDelay())).retryMax(getMaxNumberOfRetries()))
                .onErrorReturn(failedResponseData)
                .block();


{% endhighlight %}
