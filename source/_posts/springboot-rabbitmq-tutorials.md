---
title: springboot-rabbitmq-tutorials
date: 2020-11-20 08:24:17
tags: rabbitmq
---

# 一. 背景

*  `springboot`项目整合`rabbitmq `基础代码实现系统间解耦合，削峰填谷
<!--more-->
# 二. 原理

## 死信队列 听上去像 消息“死”了     其实也有点这个意思，死信队列  是 当消息在一个队列 因为下列原因：

* 消息被拒绝（basic.reject/ basic.nack）并且不再重新投递 requeue=false
* 消息超期 (rabbitmq  Time-To-Live -> messageProperties.setExpiration()) 
* 队列超载
# 三. 实现
## 配置队列和exchange的绑定关系 RabbitmqConfig
```java
package com.codewindy.mongodb.config;

import cn.hutool.core.map.MapUtil;
import com.codewindy.common.utils.RabbitmqConstant;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.*;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;

/**
 * @author codewindy
 * @date 2020-11-09 10:55 PM
 * @since 1.0.0
 */
@Configuration
@Slf4j
public class RabbitmqConfig {
    /* -------------------------------定义oder_queue order_exchange的绑定关系-----------------------------------------------*/

    @Bean(name = "orderQueue")
    public Queue orderQueue() {
        HashMap<String, Object> params = MapUtil.newHashMap(16);
        params.put(RabbitmqConstant.X_DEAD_LETTER_EXCHANGE, RabbitmqConstant.DLX_EXCHANGE_NAME);
        params.put(RabbitmqConstant.X_DEAD_LETTER_ROUTIN_KEY, RabbitmqConstant.DLX_ROUTING_ORDER_CANCEL);
        //params.put(CommonConstant.X_EXPIRES,5000);
        params.put(RabbitmqConstant.X_MAX_LENGTH, 10);
        params.put(RabbitmqConstant.X_MESSAGE_TTL, 5000);
        //params.put(CommonConstant.X_MAX_PRIORITY,4);
        return QueueBuilder.durable(RabbitmqConstant.QUEUE_NAME).withArguments(params).build();
    }

    @Bean(name = "orderExchange")
    public TopicExchange orderExchange() {
        return ExchangeBuilder.topicExchange(RabbitmqConstant.EXCHANGE_NAME).durable(true).build();

    }

    @Bean
    public Binding orderTopicExchangeAndQueue(
            @Qualifier("orderExchange") TopicExchange orderExchange,
            @Qualifier("orderQueue") Queue orderQueue) {
        return BindingBuilder.bind(orderQueue).to(orderExchange).with(RabbitmqConstant.ROUTING_KEY_NAME);
    }

    /* -------------------------------定义死信队列dlx_oder_queue dlx_order_exchange的绑定关系----------------------------------*/

    @Bean(name = "dlxOrderQueue")
    public Queue dlxOrderQueue() {
        return QueueBuilder.durable(RabbitmqConstant.DLX_QUEUE_NAME).build();
    }

    @Bean(name = "dlxOrderExchange")
    public TopicExchange dlxOrderExchange() {
        return ExchangeBuilder.topicExchange(RabbitmqConstant.DLX_EXCHANGE_NAME).durable(true).build();
    }

    @Bean
    public Binding dlxOrderTopicExchangeAndQueue(
            @Qualifier("dlxOrderExchange") TopicExchange dlxOrderExchange,
            @Qualifier("dlxOrderQueue") Queue dlxOrderQueue) {
        return BindingBuilder.bind(dlxOrderQueue).to(dlxOrderExchange).with(RabbitmqConstant.DLX_ROUTING_KEY_NAME);
    }


}

```
## RabbitmqConstant 常量定义防止取错参数
```java
package com.codewindy.common.utils;

public interface RabbitmqConstant {
    String  X_MESSAGE_TTL = "x-message-ttl";
    String  X_EXPIRES = "x-expires";
    String  X_MAX_LENGTH = "x-max-length";
    String  X_MAX_PRIORITY = "x-max-priority";
    /**
     * 死信队列 交换机标识符
     */
    String  X_DEAD_LETTER_EXCHANGE = "x-dead-letter-exchange";
    /**
     * 死信队列交换机绑定键标识符
     */
    String  X_DEAD_LETTER_ROUTIN_KEY = "x-dead-letter-routing-key";

    String DLX_EXCHANGE_NAME ="dlx_order_exchange";
    String DLX_QUEUE_NAME ="dlx_order_queue";
    String DLX_ROUTING_KEY_NAME ="dlx.order.#";

    String DLX_ROUTING_ORDER_CANCEL ="dlx.order.cancel";


    String EXCHANGE_NAME ="order_exchange";
    String QUEUE_NAME ="order_queue";
    String ROUTING_KEY_NAME ="order.#";
    String ROUTING_ORDER_CANCEL ="order.cancel";
}

```
## RabbitmqUtil 对rabbitmqTemplate封装
```java
package com.codewindy.mongodb.utils;

import cn.hutool.core.util.IdUtil;
import com.codewindy.mongodb.service.impl.ConfirmCallbackServiceImpl;
import com.codewindy.mongodb.service.impl.ReturnCallbackServiceImpl;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.MessageDeliveryMode;
import org.springframework.amqp.rabbit.connection.CorrelationData;
import org.springframework.amqp.rabbit.connection.PublisherCallbackChannel;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.amqp.support.converter.Jackson2JsonMessageConverter;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;
import org.springframework.util.Assert;

import java.nio.charset.StandardCharsets;

/**
 * @author codewindy
 * @date 2020-11-09 11:28 PM
 * @since 1.0.0
 */
@Slf4j
@Component
public class RabbitmqUtil implements InitializingBean {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void sendMessage(String exchange, String routingKey, Object msg) {

        /**
         * 确保消息发送失败后可以重新返回到队列中
         * 注意：yml需要配置 publisher-returns: true
         */
        rabbitTemplate.setMandatory(true);
        //rabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
        /**
         * 消费者确认收到消息后，手动ack回执回调处理
         */
        //rabbitTemplate.setConfirmCallback(confirmCallbackService);

        /**
         * 消息投递到队列失败回调处理
         */
       // rabbitTemplate.setReturnCallback(returnCallbackService);
        /**
         * 发送消息
         */
        rabbitTemplate.convertAndSend(exchange, routingKey, msg,
                message -> {
                    message.getMessageProperties().setDeliveryMode(MessageDeliveryMode.PERSISTENT);
                    //message.getMessageProperties().setExpiration("5000");
                    message.getMessageProperties().setContentEncoding(StandardCharsets.UTF_8.name());
                    //message.getMessageProperties().setPriority(4);
                    return message;
                },
                new CorrelationData(IdUtil.fastSimpleUUID()));
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        rabbitTemplate.setConfirmCallback(new ConfirmCallbackServiceImpl());
        rabbitTemplate.setReturnCallback(new ReturnCallbackServiceImpl());
    }
}
```
## 生产者发送消息producer 业务代码

```java
rabbitmqUtil.sendMessage(RabbitmqConstant.EXCHANGE_NAME,RabbitmqConstant.ROUTING_ORDER_CANCEL,"我是消息, i miss u");

```

## 消费者接收消息consumer 业务代码

```java
package com.codewindy.mysql;
import com.codewindy.common.utils.RabbitmqConstant;
import com.rabbitmq.client.Channel;
import lombok.extern.slf4j.Slf4j;
import org.springframework.amqp.core.ExchangeTypes;
import org.springframework.amqp.core.Message;
import org.springframework.amqp.rabbit.annotation.*;
import org.springframework.stereotype.Component;

import java.io.IOException;

/**
 * @author codewindy
 * @date 2020-11-09 11:42 PM
 * @since 1.0.0
 */
@Slf4j
@Component
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = RabbitmqConstant.DLX_QUEUE_NAME, durable = "true"),
        exchange = @Exchange(name = RabbitmqConstant.DLX_EXCHANGE_NAME,
                type = ExchangeTypes.TOPIC,
                ignoreDeclarationExceptions = "true"),
        key = {RabbitmqConstant.DLX_ROUTING_ORDER_CANCEL}
))
public class ConsumerMessageHandler {

    @RabbitHandler
    public void messageHandler(String msg, Channel channel, Message message) throws IOException {

        try {
            log.info("消费者收到消息：{}", msg);

            //TODO 具体业务
            //int i = 1/0;

            channel.basicAck(message.getMessageProperties().getDeliveryTag(), false);

        }  catch (Exception e) {

            if (message.getMessageProperties().getRedelivered()) {

                log.error("消息已重复处理失败,拒绝再次接收...");
                // 拒绝消息
                channel.basicReject(message.getMessageProperties().getDeliveryTag(), false);
            } else {

                log.error("消息即将再次返回队列处理...");

                channel.basicNack(message.getMessageProperties().getDeliveryTag(), false, true);
            }
        }
    }
}
```
# 四. docker 安装rabbitmq相关配置
## 安装rabbitmq以及相关操作
* `sudo systemctl restart docker`
* `docker pull rabbitmq:3.7.8-management`
* ` docker run -d --name rabbitmq3.8.9 -p 5672:5672 -p 15672:15672  --hostname rabbitmq  -e RABBITMQ_DEFAULT_VHOST=/ -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin 镜像id`
* docker ps 查看已启动的服务  `docker ps -a `查看所有
* `docker exec -it  容器id bash` 进入rabbitmq容器内部
* `rabbitmqctl reset` 重置服务
* `rabbitmqctl stop_app` 停止服务   `rabbitmqctl start_app` 启动服务
# 五. 参考
* [springboot rabbitmq 之死信队列 延迟消费消息](https://my.oschina.net/10000000000/blog/1626278)
* [springboot 集成rabbitmq](https://my.oschina.net/10000000000/blog/1624963)
* [rabbitmq_ppt_reference](https://github.com/codewindy/mac_soft/blob/master/rabbitmq_ppt.zip)
* [JavaTutorials](https://github.com/codewindy/JavaTutorials)
