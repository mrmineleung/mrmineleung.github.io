---
layout: post
title:  "Java Spring Retry Template"
date:   2020-11-03 11:51:37 +0800
categories: Java
tags: 
  - "Java"
  - "Spring"
  - "Spring Retry"
---

demo-spring.xml
{% highlight xml %}
<bean id="retryTemplate" class="com.xxxxxx.xxxxxxxxxxx.retry.RetryTemplate">
        <property name="retryEnabled" value="true"/>
        <property name="retryTimeInterval" value="1000"/>
        <property name="maxAttempt" value="2"/>
        <property name="includeExceptions">
            <util:map>
                <entry key="javax.xml.ws.WebServiceException" value="true"/>
            </util:map>
        </property>
    </bean>
{% endhighlight %}

RetryTemplate.java
{% highlight java %}

import com.xxxxxx.core.model.WebServiceConfigurationModel;
import org.springframework.retry.backoff.FixedBackOffPolicy;
import org.springframework.retry.policy.SimpleRetryPolicy;
import org.springframework.retry.support.RetryTemplate;

import java.util.Map;
import java.util.Optional;

public class RetryTemplate<CONFIG extends WebServiceConfigurationModel> {

    private static final int DEFAULT_BASE_ATTEMPT = 1;

    private int maxAttempt = 5;
    private long retryTimeInterval = 1000;
    private boolean retryEnabled = true;
    private Map<Class<? extends Throwable>, Boolean> includeExceptions;

    public RetryTemplate createRetryTemplate(CONFIG config) {

        int maxAttempt = DEFAULT_BASE_ATTEMPT + getMaxAttempt(config);
        long retryTimeInterval = getRetryTimeInterval(config);

        if (Boolean.FALSE.equals(isRetryEnabled(config))) {
            maxAttempt = DEFAULT_BASE_ATTEMPT;
            retryTimeInterval = 0;
        }


        SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy(maxAttempt, includeExceptions, false);

        FixedBackOffPolicy backOffPolicy = new FixedBackOffPolicy();
        backOffPolicy.setBackOffPeriod(retryTimeInterval);

        RetryTemplate template = new RetryTemplate();
        template.setRetryPolicy(retryPolicy);
        template.setBackOffPolicy(backOffPolicy);


        return template;
    }

    private Integer getMaxAttempt(CONFIG config) {
        return Optional.ofNullable(config)
                .map(WebServiceConfigurationModel::getMaxNumberOfRetries)
                .orElse(maxAttempt);
    }

    private Long getRetryTimeInterval(CONFIG config) {
        return Optional.ofNullable(config)
                .map(WebServiceConfigurationModel::getRetryDelay)
                .map(Integer::longValue)
                .orElse(retryTimeInterval);
    }

    private boolean isRetryEnabled(CONFIG config) {
        return Optional.ofNullable(config)
                .map(WebServiceConfigurationModel::isRetryEnabled)
                .orElse(retryEnabled);
    }

    public void setMaxAttempt(int maxAttempt) {
        this.maxAttempt = maxAttempt;
    }

    public void setRetryTimeInterval(long retryTimeInterval) {
        this.retryTimeInterval = retryTimeInterval;
    }

    public void setRetryEnabled(boolean retryEnabled) {
        this.retryEnabled = retryEnabled;
    }

    public void setIncludeExceptions(
            Map<Class<? extends Throwable>, Boolean> includeExceptions) {
        this.includeExceptions = includeExceptions;
    }
}
{% endhighlight %}
