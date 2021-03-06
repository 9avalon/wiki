---
title: ActiveMQ
date: 2016-7-21 09:15:06
collection: MQ
---

[TOC]

## 安装ActiveMQ

从官网上下载最新的版本，解压后进入bin文件夹，会看到win64（64位系统），点击进入然后执行里面的activemq.bat文件。

其默认监听port为61616

web端口为8163

## Spring 中 JMS 模块实现

maven依赖

```xml
<!--消息队列-->
<dependency>
    <groupId>org.apache.activemq</groupId>
    <artifactId>activemq-core</artifactId>
    <version>5.7.0</version>
</dependency>
```

spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:jms="http://www.springframework.org/schema/jms"
       xsi:schemaLocation="
            http://www.springframework.org/schema/beans
            http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context
            http://www.springframework.org/schema/context/spring-context.xsd
            http://www.springframework.org/schema/jms
            http://www.springframework.org/schema/jms/spring-jms.xsd">

    <!-- Spring 管理 ConectionFactory -->
    <bean id="connectionFactory" class="org.springframework.jms.connection.SingleConnectionFactory" >
        <property name="targetConnectionFactory" ref="targetConnectionFactory" />
    </bean>

    <!--真正可以产生conneciton的ConnectionFactory-->
    <bean id="targetConnectionFactory" class="org.apache.activemq.ActiveMQConnectionFactory">
        <property name="brokerURL" value="tcp://localhost:61616" />
    </bean>

    <!-- Spring提供的JMS工具类，它可以进行消息发送、接收等 -->
    <bean id="jmsTemplate" class="org.springframework.jms.core.JmsTemplate">
        <!-- 这个connectionFactory对应的是我们定义的Spring提供的那个ConnectionFactory对象 -->
        <property name="connectionFactory" ref="connectionFactory"/>
    </bean>

    <!--这个是队列目的地，点对点的-->
    <bean id="queueDestination" class="org.apache.activemq.command.ActiveMQQueue">
        <constructor-arg>
            <value>queue</value>
        </constructor-arg>
    </bean>

    <!-- 消息监听器 -->
    <bean id="consumerMessageListener" class="com.houmingjian.practice.service.ConsumerMessageListener"/>

    <!-- 消息监听容器 -->
    <bean id="jmsContainer" class="org.springframework.jms.listener.DefaultMessageListenerContainer">
        <property name="connectionFactory" ref="connectionFactory" />
        <property name="destination" ref="queueDestination" />
        <property name="messageListener" ref="consumerMessageListener" />
    </bean>
</beans>
```

生产者类

```java
/**
* 生产者
*/
@Component
public class ProducerServiceImpl {

    @Resource
    private JmsTemplate jmsTemplate;

    public void sendMessage(Destination destination, final String message){
        System.out.println("---------------生产者发送消息-----------------");
        System.out.println("---------------生产者发了一个消息：" + message);

        jmsTemplate.send(destination, new MessageCreator() {
            @Override
            public Message createMessage(Session session) throws JMSException {
                return session.createTextMessage(message);
            }
        });
    }

}
```

消费者类

```java
/**
 * 消费者
 *
 * @author: 侯铭健  Date: 2016/7/20 Time: 17:55
 */
public class ConsumerMessageListener implements MessageListener {

    @Override
    public void onMessage(Message message) {
        //这里我们知道生产者发送的就是一个纯文本消息，所以这里可以直接进行强制转换
        TextMessage textMsg = (TextMessage) message;

        System.out.println("listen1 接收到一个纯文本消息。");
        try {
            System.out.println("消息内容是：" + textMsg.getText());
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }

}
```