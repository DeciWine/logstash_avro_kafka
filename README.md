# logstash\_avro\_kafka

## 1.项目说明
本项目是通过[logstash](https://www.elastic.co/cn/products/logstash)收集特定种类的日志并解析日志，随后进行[Avro](http://avro.apache.org/)序列化之后，将二进制流发送到[kafka](http://kafka.apache.org/)。

## 2.目录介绍
1.patterns 目录是 logstash 的[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)使用的模式文件（详细介绍在后面）

2.sample 目录是示例日志文件

3.schema 是示例日志文件经过解析之后对应的[Avro schema](https://avro.apache.org/docs/1.8.1/spec.html#schemas)

4.根目录下的conf文件是日志文件对应的logstash配置文件

5.logstash-codec-pnda-avro-3.1.1-java.gem 是需要安装的自定义插件，因为原生的解析avro序列的插件对二进制流输出支持不好，详见[pnda-avro](https://github.com/pndaproject/logstash-codec-pnda-avro)

6.kafka.rb是我修改了原生文件第199行的新文件，因为在kafka 6.2.3版本使用原来的文件会报“undefined methods”的错误。
## 3.项目原理
本项目是通过logstash收集相关日志（如sample/syslog），使用logstash的[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)，通过不同的配置文件（如syslog-pipeline.conf）和相关模式文件(如pattern/linux-syslog)解析不同种类的日志，并使用对应的avro schema(如schema/linux\_system\_log\_schema.avsc)将其序列化，并发送到kafka对应的topic.

## 4.项目运行
1. [下载logstash](https://www.elastic.co/cn/downloads/logstash).

2. 安装logstash的相关插件：logstash-codec-avro,logstash-filter-grok,logstash-filter-mutate,logstash-output-kafka.

```
bin/logstash-plugin install logstash-codec-avro logstash-filter-grok logstash-filter-mutate logstash-output-kafka

```
3.将项目所有文件拷贝至logstash文件夹中，安装自定义插件logstash-codec-pnda-avro-3.1.1-java.gem 
```
bin/logstash-plugin install logstash-codec-pnda-avro-3.1.1-java.gem
```

4.将kafka.rb 替换
``` 
{LogstashRoot}/vendor/bundle/jruby/2.3.0/gems/logstash-output-kafka-7.0.8/lib/logstash/outputs/kafka.rb
```

5.修改syslog-pipeline.conf中的kafka对应的参数，执行
```
 bin/logstash -f syslog-pipeline.conf --config.reload.automatic
 ```
 就会解析sample/syslog ，将其avro序列化后发送到你的kafka对应的topic中。
 
## 5.备注 
1.也可以解析其他种类的日志，只要修改配置文件，修改grok插件的参数，让logstash能顺利解析日志，并写出对应的avro schema即可。

2.input插件直接使用本地文件输入，配置文件中要填写文件的绝对路径。如果要采集其他设备上的日志，可以使用filebeat插件，参考[filebeat](https://www.elastic.co/guide/en/logstash/6.2/plugins-inputs-beats.html)

3.成功运行项目须要根据实际情况修改根目录下的conf文件，修改 日志文件绝对路径、kafka服务器地址、kafka Topic 等相关配置
 
 参考：
 
 1.[grok插件](https://www.elastic.co/guide/en/logstash/6.2/plugins-filters-grok.html#_getting_help_116)
 
 2.[一些常见日志的pattern](https://github.com/logstash-plugins/logstash-patterns-core/tree/master/patterns)
 
 3.[Avro schema](https://avro.apache.org/docs/1.8.1/spec.html#schemas)

4.[logstash avro codec plugin](https://www.elastic.co/guide/en/logstash/6.2/plugins-codecs-avro.html)

5.[logstash reference](https://www.elastic.co/guide/en/logstash/6.2/index.html)