---
layout: post
title:  "EhCache 3 XML Configuration"
date:   2020-11-03 11:51:37 +0800
categories: EhCache
---

demo-spring-cache.xml
{% highlight xml %}
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
           http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/cache
           http://www.springframework.org/schema/cache/spring-cache.xsd
           http://www.springframework.org/schema/aop
           http://www.springframework.org/schema/aop/spring-aop.xsd">


<bean id="cacheManager" class="org.springframework.cache.ehcache.EhCacheCacheManager">
        <property name="cacheManager" ref="ehcache"/>
    </bean>

    <bean id="ehcache" class="org.springframework.cache.ehcache.EhCacheManagerFactoryBean">
        <property name="configLocation" value="cache/ehcache.xml"/>
    </bean>
	
	<cache:advice id="cacheAdvice" cache-manager="cacheManager">
        <cache:caching cache="normalCache">
			<!-- Cache The Result Returned-->
            <cache:cacheable method="method1"
                             key="T(org.springframework.cache.interceptor.SimpleKeyGenerator).generateKey(#arg1, @beanService.getObject?.uid, #root.methodName)
							 <!-- condition="" -->
							 "/>
			<!-- Evict Cache By Key-->
			<cache:cache-evict method="method1"
                             key="T(org.springframework.cache.interceptor.SimpleKeyGenerator).generateKey(#arg1, @beanService.getObject?.uid, #root.methodName)
							 <!-- condition="" -->
							 "/>
        </cache:caching>
    </cache:advice>

    <aop:config>
        <aop:advisor advice-ref="cacheAdvice"
                     pointcut="execution(* com.example.service.impl.DemoService.*(..))"/>
    </aop:config>
	<!-- For Java Annotation -->
	<!-- <cache:annotation-driven cache-manager="cacheManager"/> -->
</beans>
{% endhighlight %}

cache/ehcache.xml
{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<ehcache xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:noNamespaceSchemaLocation="http://www.ehcache.org/ehcache.xsd"
         monitoring="on"
         name="demoEhCache">

    <cache
            name="normalCache"
            maxEntriesLocalHeap="1000"
            overflowToDisk="false"
            timeToLiveSeconds="30"
            memoryStoreEvictionPolicy="LFU"
            statistics="true"/>

</ehcache>
{% endhighlight %}
