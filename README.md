#logstash\_avro\_kafka

##1.项目说明
本项目是通过[logstash](https://www.elastic.co/cn/products/logstash)收集特定种类的日志并解析日志，随后进行[Avro](http://avro.apache.org/)序列化之后，将二进制流发送到[kafka](http://kafka.apache.org/)。

##2.目录介绍
1.pattern 目录是 logstash 的[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)使用的模式文件（详细介绍在后面）

2.sample 目录是示例日志文件

3.schema 是示例日志文件经过解析之后对应的[Avro schema](https://avro.apache.org/docs/1.8.1/spec.html#schemas)

4.根目录下的conf文件是日志文件对应的logstash配置文件

##3.项目原理
本项目是通过logstash收集相关日志（如sample/syslog），使用logstash的[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)，通过不同的配置文件（如syslog-pipeline.conf）和相关模式文件(如pattern/linux-syslog)解析不同种类的日志，并使用对应的avro schema(如schema/linux\_system\_log\_schema.avsc)将其序列化，并发送到kafka对应的topic.

##4.项目运行
[下载logstash](https://www.elastic.co/cn/downloads/logstash)，将项目所有文件拷贝至logstash文件夹中，修改syslog-pipeline.conf中的kafka对应的参数，执行
```
 bin/logstash -f syslog-pipeline.conf --config.reload.automatic
 ```
 就会解析sample/syslog ，将其avro序列化后发送到你的kafka对应的topic中。
 
 你也可以解析其他种类的日志，只要修改配置文件，修改grok插件的参数，让logstash能顺利解析日志，并写出对应的avro schema即可。
 
 参考：
 
 1.[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)
 
 2.[一些常见日志的pattern](https://github.com/logstash-plugins/logstash-patterns-core/tree/master/patterns)
 
 3.[Avro schema](https://avro.apache.org/docs/1.8.1/spec.html#schemas)

4.[logstash avro codec plugin](https://www.elastic.co/guide/en/logstash/6.2/plugins-codecs-avro.html)

5.[logstash reference](https://www.elastic.co/guide/en/logstash/6.2/index.html)