本文档是基于ES7.8

# 7.x的重大改进

elasticsearch7.0有哪些重大改进

1. 彻底废弃多type支持，包括api层面，之前版本可在一个索引库下创建多个type。
2. 彻底废弃_all字段支持，为提升性能默认不再支持全文检索，即7.0之后版本进行该项配置会报错。
3. 新增应用程序主动监测功能，搭配对应的kibana版本，用户可监测应用服务的健康状态，并在出现问题后及时发出通知。
4. 取消query结果中hits count的支持（聚合查询除外），使得查询性能大幅提升（3x-7x faster）。这意味着，每次查询后将不能得到精确的结果集数量。
   5.新增intervals query ,用户可设置多字符串在文档中出现的先后顺序进行检索。
5. 新增script_core ，通过此操作用户可以精确控制返回结果的score分值。
6. 优化集群协调子系统，缩减配置项提升稳定性。
7. 新增 alias、date_nanos、features、vector等数据类型。
8. 7.0自带java环境，所以我们在安装es时不再需要单独下载和配置java_home。
9. 7.0将不会再有OOM的情况，JVM引入了新的circuit breaker（熔断）机制，当查询或聚合的数据量超出单机处理的最大内存限制时会被截断，并抛出异常（有点类似clickhouse）。

# 基本概念

## 索引(index)

相当于关系性数据库的库

## 类型(type)

相当于关系型数据库的表，在7.8版本之前可以指定，7.8版本固定为_doc，到8.x版本将完全取代，新手要注意这一点，网上找的很多资料都是6.x版本的，在7.x上很多api运行不通

## 文档(document)

相当于关系数据库一行数据

## 映射(mapping)

mapping 是用来定义文档及其字段的存储方式、索引方式的手段，例如利用mapping 来定义以下内容：

- 哪些字段需要被定义为全文检索类型
- 哪些字段包含number、date类型等
- 格式化时间格式
- 自定义规则，用于控制动态添加字段的映射

### Mapping Type

每个索引都拥有唯一的 mapping type，用来决定文档将如何被索引。mapping type由下面两部分组成

- Meta-fields

元字段用于自定义如何处理文档的相关元数据。 元字段的示例包括文档的_index，_type，_id和_source字段。

- Fields or properties

映射类型包含与文档相关的字段或属性的列表

## 主分片（primary shard）

⼀个索引可以存储超出单个结点硬件限制的⼤量数据。⽐如，⼀个具有10亿⽂档的索引占据1TB的磁盘 空间，⽽任⼀节点都没有这样⼤的磁盘空间；或者单个节点处理搜索请求，响应太慢。为了解决这个问 题，Elasticsearch提供了将索引划分成多份的能⼒，这些份就叫做分⽚。当你创建⼀个索引的时候，你 可以指定你想要的分⽚的数量。每个分⽚本身也是⼀个功能完善并且独⽴的“索引”，这个“索引”可以被 放置到集群中的任何节点上。

分片很重要，主要有两⽅⾯的原因：

1）允许你⽔平分割/扩展你的内容容 量。

2）允许你在分⽚（潜在地，位于多个节点上）之上进行分布式的、并行的操作，进而提⾼性能/吞吐量。


# ES与mysql的对比

| Relational DB      | Elasticsearch          |
| ------------------ | ---------------------- |
| 数据库（database） | 索引（indices）        |
| 表（tables）       | types（es8以后将弃用） |
| 行（rows）         | documents              |
| 字段（columns）    | fields                 |

# 使用场景

1. 全文检索
2. 同义词处理
3. 相关度排名
4. 复杂数据分析
5. 海量数据近实时处理


## 全文检索

## 同义词处理

**方案一**  
通过配置文件的方式  
**方案二**  
通过接口的方式

## 相关度排名

**java代码实现**

```
QueryBuilder  queryBuilder = QueryBuilders.boolQuery()
            .should(QueryBuilders.matchQuery("name", "米").boost(0.8f))
            .should(QueryBuilders.matchQuery("brandName", "米").boost(0.6f))
            .should(QueryBuilders.matchQuery("labelName", "米").boost(0.6f))
            .should(QueryBuilders.matchQuery("menuCategoryNamePath", "米").boost(0.2f))
            .should(QueryBuilders.matchQuery("description", "米").boost(0.4f));
FieldValueFactorFunctionBuilder factorFunctionBuilder = new FieldValueFactorFunctionBuilder("num");
factorFunctionBuilder.factor(0.1f);
factorFunctionBuilder.modifier(FieldValueFactorFunction.Modifier.LOG1P);
FunctionScoreQueryBuilder boostMode = QueryBuilders
        .functionScoreQuery(queryBuilder, factorFunctionBuilder)
        .boostMode(CombineFunction.SUM);
SearchRequestBuilder requestBuilder = client.prepareSearch(ESConstant.PRODUCT_INDEX)
        .setTypes(ESConstant.PRODUCT_TYPE);
requestBuilder.setQuery(boostMode);
```

**等同于**

```
GET prod/demo/_search
{
  "query":{
    "function_score": {
      "query": {
          "bool": {
      "should": [
        {
          "match": {
            "name": {"query": "大米","boost":0.8}
          }
        },
        {
          "match": {
            "brandName": {"query": "大米","boost":0.6}
          }
        },
        {
          "match": {
            "labelName": {"query": "大米","boost":0.6}
          }
        },
        {
          "match": {
            "description": {"query": "大米","boost":0.5}
          },
        {
          "match": {
            "menuCategoryNamePath": {"query": "大米","boost":0.2}
          }
        }
      ]
    }
      },
      "field_value_factor": {
        "field": "num",
        "modifier": "log1p",
        "factor": 0.1
      },
      "boost_mode": "sum"
    }
  }
}
```

# setting

```
"index.max_result_window":20000   #控制单次查询返回的最大结果数
"index.merge.scheduler.max_thread_count": 2   #segment段合并时可使用的最大线程数，为避免过度的io操作，该值一般不超过2
"index.routing.allocation.total_shards_per_node":10    #分配到单个节点的最大分片数
"index.refresh_interval":"-1"    #index刷新频率，频繁刷新会降低性能，一般设置为30s-60s；-1表示禁用刷新
"index.translog.durability":"async"    #translog刷新方式，如果对数据安全性要求不算太高，可设置为async以提升性能
"index.translog.flush_threshold_size":"1024mb"    #translog刷新字节条件，超过1g才会刷新
"index.translog.sync_interval":"120s"    #translog刷新时间条件，超过120s才会刷新
"index.unassigned.node_left.delayed_timeout":"1d"    #当有节点宕机后索引库多久触发副本balance操作
"index.search.slowlog.threshold.query.info":"1s"    #超过1s的查询会被记录到慢查询日志里
"index.store.type":"niofs"    #网络通信协议
"index.number_of_replicas":0    #index分片的副本数
"index.number_of_shards":8    #index分片数，需要注意的是es7.0默认索引分片数调整为1了
"index.codec":"best_compression"    #index数据的压缩方式，best_compression压缩可节省4-8倍的存储空间
```

# 分词器最佳实践

因为后续的keyword和text设计分词问题，这里给出分词最佳实践。即索引时用ik_max_word，搜索时分词器用ik_smart，这样索引时最大化的将内容分词，搜索时更精确的搜索到想要的结果。

例如我想搜索的是小米手机，我此时的想法是想搜索出小米手机的商品，而不是小米音响、小米洗衣机等其他产品，也就是说商品信息中必须只有小米手机这个词。


# ES数据类型

## 核心数据类型

1. 字符串类型： text, keyword。其中text是会参加分词、keyword不会参与分词  
2. 数字类型：`long`, `integer`, `short`, `byte`, `double`, `float`, `half_float`, `scaled_float`  
3. 日期：date  
4. 日期 纳秒：date_nanos  
5. 布尔型：boolean  
6. Binary：binary  
7. Range: `integer_range`, `float_range`, `long_range`, `double_range`, `date_range`

针对同一字段支持多种字段类型可以更好地满足我们的搜索需求，例如一个string类型的字段可以设置为text来支持全文检索，与此同时也可以让这个字段拥有keyword类型来做排序和聚合，另外我们也可以为字段单独配置分词方式，例如"analyzer": "ik_max_word",

### text 类型

text类型的字段用来做全文检索，例如邮件的主题、淘宝京东中商品的描述等。这种字段在被索引存储前先进行分词，存储的是分词后的结果，而不是完整的字段。text字段不适合做排序和聚合。如果是一些结构化字段，分词后无意义的字段建议使用keyword类型，例如邮箱地址、主机名、商品标签等。

常有参数包含以下

- analyzer：用来分词，包含索引存储阶段和搜索阶段（其中查询阶段可以被search_analyzer参数覆盖），该参数默认设置为index的analyzer设置或者standard analyzer
- index：是否可以被搜索到。默认是true
- fields：Multi-fields允许同一个字符串值同时被不同的方式索引，例如用不同的analyzer使一个field用来排序和聚类，另一个同样的string用来分析和全文检索。下面会做详细的说明
- search_analyzer：这个字段用来指定搜索阶段时使用的分词器，默认使用analyzer的设置
- search_quote_analyzer：搜索遇到短语时使用的分词器，默认使用search_analyzer的设置

### keyword 类型

keyword用于索引结构化内容（例如电子邮件地址，主机名，状态代码，邮政编码或标签）的字段，这些字段被拆分后不具有意义，所以在es中应索引完整的字段，而不是分词后的结果。

通常用于过滤（例如在博客中根据发布状态来查询所有已发布文章），排序和聚合。keyword只能按照字段精确搜索，例如根据文章id查询文章详情。如果想根据本字段进行全文检索相关词汇，可以使用text类型。

```
PUT my_index
{
  "mappings": {
    "properties": {
      "tags": {
        "type":  "keyword"
      }
    }
  }
}
```

- index：是否可以被搜索到。默认是true
- fields：Multi-fields允许同一个字符串值同时被不同的方式索引，例如用不同的analyzer使一个field用来排序和聚类，另一个同样的string用来分析和全文检索。下面会做详细的说明
- null_value：如果该字段为空，设置的默认值，默认为null
- ignore_above：设置索引字段大小的阈值。该字段不会索引大小超过该属性设置的值，默认为2147483647，代表着可以接收任意大小的值。但是这一值可以被PUT Mapping Api中新设置的ignore_above来覆盖这一值。

### date类型

支持排序，且可以通过format字段对时间格式进行格式化。

json中没有时间类型，所以在es在规定可以是以下的形式：

- 一段格式化的字符串，例如"2015-01-01"或者"2015/01/01 12:10:30"
- 一段long类型的数字，指距某个时间的毫秒数，例如1420070400001
- 一段integer类型的数字，指距某个时间的秒数

### object类型

mapping中不用特意指定field为object类型，因为这是它的默认类型。

json类型天生具有层级的概念，文档内部还可以包含object类型进行嵌套。例如：

```
PUT my_index/_doc/1
{ 
  "region": "US",
  "manager": { 
    "age":     30,
    "name": { 
      "first": "John",
      "last":  "Smith"
    }
  }
}
```

在es中上述对象会被按照以下的形式进行索引：

```
{
  "region":             "US",
  "manager.age":        30,
  "manager.name.first": "John",
  "manager.name.last":  "Smith"
}
```

mapping可以对不同字段进行不同的设置

```
PUT my_index
{
  "mappings": {
    "properties": { 
      "region": {
        "type": "keyword"
      },
      "manager": { 
        "properties": {
          "age":  { "type": "integer" },
          "name": { 
            "properties": {
              "first": { "type": "text" },
              "last":  { "type": "text" }
            }
          }
        }
      }
    }
  }
}
```

### range类型

支持以下范围类型

| 类型          | 范围                                 |
| ------------- | ------------------------------------ |
| integer_range | -2的31次 到 2的31次-1.               |
| float_range   | 32位单精度浮点数                     |
| long_range    | -2的63次 到 2的63次-1                |
| double_range  | 64位双精度浮点数                     |
| date_range    | unsigned 64-bit integer milliseconds |
| ip_range      | ipv4和ipv6或者两者的混合             |

使用范例为：

```
PUT range_index
{
  "settings": {
    "number_of_shards": 2
  },
  "mappings": {
    "properties": {
      "age_range": {
        "type": "integer_range"
      },
      "time_frame": {
        "type": "date_range", 
        "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
      }
    }
  }
}

PUT range_index/_doc/1?refresh
{
  "age_range" : { 
    "gte" : 10,
    "lte" : 20
  },
  "time_frame" : { 
    "gte" : "2015-10-31 12:00:00", 
    "lte" : "2015-11-01"
  }
}
```

## 复杂数据类型

1. Object: 嵌套类型，不支持数组。 
2. Nested: 嵌套类型，一种特殊的object类型，存储object数组，可检索内部子项。

nested类型是一种特殊的object类型，它允许object可以以数组形式被索引，而且数组中的某一项都可以被独立检索。

而且es中没有内部类的概念，而是通过简单的列表来实现nest效果，例如下列结构的文档：

```
PUT my_index/_doc/1
{
  "group" : "fans",
  "user" : [ 
    {
      "first" : "John",
      "last" :  "Smith"
    },
    {
      "first" : "Alice",
      "last" :  "White"
    }
  ]
}
```


## 地理数据类型

1. Geo-point：geo_point （for lat/lon points）
2. Geo-shape: geo_shape (for complex shapes like polygons)

### 特殊数据类型

1. IP: ip (IPv4 和 IPv6 地址)
2. Completion类型：completion （to provide auto-complete suggestions）
3. Token count：token_count (to count the number of tokens in a string)
4. mapper-murmur3：murmur3(to compute hashes of values at index-time and store them in the index)
5. mapper-annotated-text：annotated-text （to index text containing special markup (typically used for identifying named entities)）
6. Percolator：（Accepts queries from the query-dsl）
7. Join：（Defines parent/child relation for documents within the same index）
8. Alias：（Defines an alias to an existing field.）
9. Rank feature：（Record numeric feature to boost hits at query time.）
10. Rank features：（Record numeric features to boost hits at query time.）
11. Dense vector：（Record dense vectors of float values.）
12. Sparse vector：（Record sparse vectors of float values.）
13. Search-as-you-type：（A text-like field optimized for queries to implement as-you-type completion）

## 数组类型

在Elasticsearch中，数组不需要一个特定的数据类型，任何字段都默认可以包含一个或多个值，当然，这多个值都必须是字段指定的数据类型。

## Multi-fields

Multi-fields 通常用来以不同的方式或目的索引同一个字段。比如，一个字符串类型字段可以同时被映射为 text 类型以用于全文检索、 keyword字段用于排序或聚合。又或者，你可以用standard分析器、english分析器和french分析器来索引同一个 text字段。

# Rest风格说明

| method | url地址                                         | 描述                       |
| ------ | ----------------------------------------------- | -------------------------- |
| PUT    | localhost:9200/索引名称/类型名称/文档Id         | 创建文档(指定文档ID)       |
| POST   | localhost:9200/索引名称/类型名称                | 创建文档(随机文档)         |
| POST   | localhost:9200/索引名称/类型名称/文档Id/_update | 修改文档                   |
| DELETE | localhost:9200/索引名称/类型名称/文档Id         | 删除文档                   |
| GET    | localhost:9200/索引名称/类型名称/文档Id         | 查询文档(通过指定的文档ID) |
| POST   | localhost:9200/索引名称/类型名称/_search        | 查询所有文档               |

# 索引操作

## 创建index

```
PUT /test
```

## 查看索引

```
GET /test
```

## 删除索引

```
DELETE /test
```

## 指定索引数据类型

```
PUT /test
{
  "mappings": {
    "properties": {
      "price":{
        "type": "integer"
      },
      "color":{
        "type": "keyword"
      },
      "make":{
        "type": "keyword"
      },
      "sold":{
        "type": "keyword"
      }
    }
  }
}
```

## 新增mapping字段

```
PUT index_test/_mapping
{
  "properties": {
    "name": { 
      "type":"keyword"
    }
  }
}
```

## 创建Object类型mapping

```
    
```

## 创建nested类型mapping

```
PUT /pigg_test_nested/_mapping/
{
    "properties":{
        "class":{
            "type":"keyword"
        },
        "student":{
            "type":"nested",
            "properties":{
                "name":{
                    "type":"keyword"
                },
                "sex":{
                    "type":"keyword"
                },
                "age": {
                    "type":"integer"
                }
            }
        }
    }
}
```


## 创建父子文档类型mapping

```
PUT mall_order
{
    "mappings":{
        "properties":{
            "id":{
                "type":"long"
            },
            "orderId":{
                "type":"keyword"
            },
            "orderIdExt":{
                "type":"keyword"
            },
            "insuranceName":{
                "type":"keyword"
            },
            "insuranceId":{
                "type":"keyword"
            },
            "productId":{
                "type":"keyword"
            },
            "loginUserId":{
                "type":"keyword"
            },
            "applicantId":{
                "type":"keyword"
            },
            "policyNo":{
                "type":"keyword"
            },
            "policyUrl":{
                "type":"keyword"
            },
            "billNo":{
                "type":"keyword"
            },
            "policyDetailUrl":{
                "type":"keyword"
            },
            "effectiveTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "expireTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "insureTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "surrenderTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "signTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "unsignTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "uneffectiveTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "claimTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "vendorId":{
                "type":"keyword"
            },
            "companyId":{
                "type":"integer"
            },
            "firstPremium":{
                "type":"scaled_float",
                "scaling_factor":100
            },
            "perPremium":{
                "type":"scaled_float",
                "scaling_factor":100
            },
            "premium":{
                "type":"scaled_float",
                "scaling_factor":100
            },
            "premiumDetail":{
                "type":"keyword"
            },
            "refundAmount":{
                "type":"scaled_float",
                "scaling_factor":100
            },
            "amount":{
                "type":"scaled_float",
                "scaling_factor":100
            },
            "orderStatus":{
                "type":"integer"
            },
            "callStatus":{
                "type":"integer"
            },
            "versionId":{
                "type":"integer"
            },
            "insurePeriod":{
                "type":"keyword"
            },
            "payUrl":{
                "type":"keyword"
            },
            "payFreq":{
                "type":"keyword"
            },
            "payPeriod":{
                "type":"keyword"
            },
            "bankNo":{
                "type":"keyword"
            },
            "bankName":{
                "type":"keyword"
            },
            "domain":{
                "type":"keyword"
            },
            "checkFailedReason":{
                "type":"keyword"
            },
            "extraParam":{
                "type":"keyword"
            },
            "signStatus":{
                "type":"integer"
            },
            "insuranceType":{
                "type":"integer"
            },
            "limitType":{
                "type":"integer"
            },
            "accountId":{
                "type":"keyword"
            },
            "extra":{
                "type":"keyword"
            },
            "channelId":{
                "type":"keyword"
            },
            "type":{
                "type":"integer"
            },
            "isDel":{
                "type":"integer"
            },
            "createTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "updateTime":{
                "type":"date",
                "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
            },
            "esRelation":{
                "type":"join",
                "relations":{
                    "policy_order":[
                        "policy_order_extend",
                        "policy_user",
                        "payment_plan",
                        "active_renewal"
                    ]
                }
            },
            "policy_order_extend":{
                "properties":{
                    "id":{
                        "type":"long"
                    },
                    "orderId":{
                        "type":"keyword"
                    },
                    "surrenderStatus":{
                        "type":"keyword"
                    },
                    "surrenderReason":{
                        "type":"keyword"
                    },
                    "endorNo":{
                        "type":"keyword"
                    },
                    "endorDate":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "checkTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "endorPeriod":{
                        "type":"keyword"
                    },
                    "renewalOrderStatus":{
                        "type":"integer"
                    },
                    "renewalOrderId":{
                        "type":"keyword"
                    },
                    "newOrderId":{
                        "type":"keyword"
                    },
                    "renewalInsType":{
                        "type":"keyword"
                    },
                    "signpksubId":{
                        "type":"keyword"
                    },
                    "tradeNo":{
                        "type":"keyword"
                    },
                    "tuiguangId":{
                        "type":"keyword"
                    },
                    "traceId":{
                        "type":"keyword"
                    },
                    "appId":{
                        "type":"keyword"
                    },
                    "extra":{
                        "type":"keyword"
                    },
                    "isDel":{
                        "type":"long"
                    },
                    "createTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "updateTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    }
                }
            },
            "policy_user":{
                "properties":{
                    "id":{
                        "type":"long"
                    },
                    "loginUserId":{
                        "type":"keyword"
                    },
                    "orderId":{
                        "type":"keyword"
                    },
                    "userId":{
                        "type":"keyword"
                    },
                    "isOrganization":{
                        "type":"long"
                    },
                    "type":{
                        "type":"long"
                    },
                    "sex":{
                        "type":"integer"
                    },
                    "birthday":{
                        "type":"date",
                        "format":"yyyy-MM-dd"
                    },
                    "name":{
                        "type":"keyword"
                    },
                    "phone":{
                        "type":"keyword"
                    },
                    "certType":{
                        "type":"long"
                    },
                    "certNo":{
                        "type":"keyword"
                    },
                    "preCertValidate":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "postCertValidate":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "income":{
                        "type":"half_float"
                    },
                    "job":{
                        "type":"keyword"
                    },
                    "height":{
                        "type":"long"
                    },
                    "weight":{
                        "type":"double"
                    },
                    "email":{
                        "type":"keyword"
                    },
                    "address":{
                        "type":"keyword"
                    },
                    "postCode":{
                        "type":"keyword"
                    },
                    "hasSocialInsurance":{
                        "type":"long"
                    },
                    "isDel":{
                        "type":"long"
                    },
                    "extra":{
                        "type":"keyword"
                    },
                    "createTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "updateTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "relation":{
                        "type":"long"
                    }
                }
            },
            "payment_plan":{
                "properties":{
                    "id":{
                        "type":"long"
                    },
                    "payId":{
                        "type":"keyword"
                    },
                    "loginUserId":{
                        "type":"keyword"
                    },
                    "orderId":{
                        "type":"keyword"
                    },
                    "policyNo":{
                        "type":"keyword"
                    },
                    "payNo":{
                        "type":"integer"
                    },
                    "signpksubId":{
                        "type":"keyword"
                    },
                    "tradeNo":{
                        "type":"keyword"
                    },
                    "payStatus":{
                        "type":"integer"
                    },
                    "remindStatus":{
                        "type":"integer"
                    },
                    "expectPayTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "actualPayTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "expectAmount":{
                        "type":"scaled_float",
                        "scaling_factor":100
                    },
                    "actualAmount":{
                        "type":"scaled_float",
                        "scaling_factor":100
                    },
                    "refundAmount":{
                        "type":"scaled_float",
                        "scaling_factor":100
                    },
                    "refundNo":{
                        "type":"keyword"
                    },
                    "graceDate;":{
                        "type":"integer"
                    },
                    "message":{
                        "type":"keyword"
                    },
                    "type":{
                        "type":"integer"
                    },
                    "payChannel":{
                        "type":"integer"
                    },
                    "isDel":{
                        "type":"integer"
                    },
                    "extra":{
                        "type":"keyword"
                    },
                    "createTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "updateTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "graceBeginTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "graceEndTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "insuranceId":{
                        "type":"keyword"
                    }
                }
            },
            "active_renewal":{
                "properties":{
                    "id":{
                        "type":"long"
                    },
                    "renewalId":{
                        "type":"keyword"
                    },
                    "renewalType":{
                        "type":"integer"
                    },
                    "renewalStatus":{
                        "type":"integer"
                    },
                    "startPayNo":{
                        "type":"integer"
                    },
                    "endPayNo":{
                        "type":"integer"
                    },
                    "premium":{
                        "type":"scaled_float",
                        "scaling_factor":100
                    },
                    "orderId":{
                        "type":"keyword"
                    },
                    "companyId":{
                        "type":"integer"
                    },
                    "policyNo":{
                        "type":"keyword"
                    },
                    "renewalUrl":{
                        "type":"keyword"
                    },
                    "signpksubId":{
                        "type":"keyword"
                    },
                    "outTradeNo":{
                        "type":"keyword"
                    },
                    "billNo":{
                        "type":"keyword"
                    },
                    "payJournalId":{
                        "type":"keyword"
                    },
                    "endorNo":{
                        "type":"keyword"
                    },
                    "endorDate":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "validDate":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "changePremium":{
                        "type":"scaled_float",
                        "scaling_factor":100
                    },
                    "accountId":{
                        "type":"keyword"
                    },
                    "channelId":{
                        "type":"keyword"
                    },
                    "redPacket":{
                        "type":"integer"
                    },
                    "extra":{
                        "type":"keyword"
                    },
                    "isDel":{
                        "type":"integer"
                    },
                    "createTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    },
                    "updateTime":{
                        "type":"date",
                        "format":"yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
                    }
                }
            }
        }
    }
}
```


## 创建

# 文档操作

## 创建文档

```
POST /test/_doc/
{ 
"price" : 10000, 
"color" : "red",
"make" : "honda", 
"sold" : "2014-10-28" 
}
```

## 创建父子文档

### 创建一对一关系

```
PUT /hotel-index
{
  "mappings": {
    "properties": {
      "my_join_field": {
        "type": "join",
        "relations": {
          "hotel": "goods"
        }
      }
    }
  }
}

```

### 创建一对多关系

```
PUT /hotel-index
{
  "mappings": {
    "properties": {
      "my_join_field": {
        "type": "join",
        "relations": {
          "hotel": ["room","goods"]
        }
      }
    }
  }
}
```

### 创建多层关联

```
PUT /hotel-index
{
  "mappings": {
    "properties": {
      "my_join_field": {
        "type": "join",
        "relations": {
          "hotel": "goods",  
          "goods": "price" 
        }
      }
    }
  }
}
```

### 添加父文档

```
PUT hotel-index/_doc/1?refresh
{
  "my_id": "1",
  "text": "This is a question",
  "name":"青年旅社",
  "my_join_field": {
    "name": "hotel" 
  }
}
```

### 添加子文档

```
PUT hotel-index/_doc/3?routing=1&refresh 
{
  "my_id": "3",
  "title":"【无早餐】双人房",
  "room_type":1,
  "my_join_field": {
    "name": "goods", 
    "parent": "1" 
  }
}
```

注意，必须制定routing，保证父子文档在一个分片上

## 批量插入

```
POST /test/_doc/_bulk
{ "index": {}}
{ "price" : 10000, "color" : "red", "make" : "honda", "sold" : "2014-10-28" }
{ "index": {}}
{ "price" : 20000, "color" : "red", "make" : "honda", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 30000, "color" : "green", "make" : "ford", "sold" : "2014-05-18" }
{ "index": {}}
{ "price" : 15000, "color" : "blue", "make" : "toyota", "sold" : "2014-07-02" }
{ "index": {}}
{ "price" : 12000, "color" : "green", "make" : "toyota", "sold" : "2014-08-19" }
{ "index": {}}
{ "price" : 20000, "color" : "red", "make" : "honda", "sold" : "2014-11-05" }
{ "index": {}}
{ "price" : 80000, "color" : "red", "make" : "bmw", "sold" : "2014-01-01" }
{ "index": {}}
{ "price" : 25000, "color" : "blue", "make" : "ford", "sold" : "2014-02-12" }
```

# 查询

## 获取索引mapping信息

```
GET /account/_mapping
```

## 获取所有文档

```
GET /account/_search
```

## 查询那个颜色的汽车销量最好?

```
GET /test/_search
{
    "size" : 0,//不需要返回文档,所以直接设置为0.可以提高查询速度
    "aggs" : { //这个是aggregations的缩写,这边用户随意,可以写全称也可以缩写
        "popular_colors" : { //定义一个聚合的名字,与java的方法命名类似,建议用'_'线来分隔单词
            "terms" : { //定义单个桶(集合)的类型为 terms
              "field" : "color"(字段颜色进行分类,类似于sql中的group by color)
            }
        }
    }
}
```

返回结果

```
{
...
   "hits": {
      "hits": [] //因为我们设置了返回的文档数量为0,所以在这个文档里面是不会包含具体的文档的
   },
   "aggregations": {
      "popular_colors": { 
         "buckets": [
            {
               "key": "red", 
               "doc_count": 4 //在红色车子集合的数量
            },
            {
               "key": "blue",
               "doc_count": 2
            },
            {
               "key": "green",
               "doc_count": 2
            }
         ]
      }
   }
}
```

## 在上面的聚合基础上添加一些指标—>’average‘平均价格

```
GET /test/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": { //为指标新增aggs层
            "avg_price": { //指定指标的名字,在返回的结果中也是用这个变量名来储存数值的
               "avg": {//指标参数:平均值
                  "field": "price" //明确求平均值的字段为'price'
               }
            }
         }
      }
   }
}
```

返回结果

```
{
...
   "aggregations": {
      "colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4,//这个指标是不需要设置都会带上的
               "avg_price": { //这个是我们在上面自定义的一个指标的名字
                  "value": 32500
               }
            },
            {
               "key": "blue",
               "doc_count": 2,
               "avg_price": {
                  "value": 20000
               }
            },
            {
               "key": "green",
               "doc_count": 2,
               "avg_price": {
                  "value": 21000
               }
            }
         ]
      }
   }
...
}
```

## 桶/集合(Buckets)的嵌套,在上面的基础上,先按照颜色划分—>再汽车按照厂商划分

```
GET /test/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": { 
               "avg": {
                  "field": "price"
               }
            },
            "make": { //命名子集合的名字
                "terms": {
                    "field": "make" //按照字段'make'再次进行分类
                }
            }
         }
      }
   }
}
```

返回结果

```
{
...
   "aggregations": {
      "colors": {
         "buckets": [
            {
               "key": "red",
               "doc_count": 4,
               "make": { //子集合的名字
                  "buckets": [
                     {
                        "key": "honda", 
                        "doc_count": 3
                     },
                     {
                        "key": "bmw",
                        "doc_count": 1
                     }
                  ]
               },
               "avg_price": {
                  "value": 32500 
               }
            },

...
}
```

## 在上面的结果基础上,在增加一个指标,就是查询出每个制造商生产的最贵和最便宜的车子的价格分别是多少

```
GET /test/_search
{
   "size" : 0,
   "aggs": {
      "colors": {
         "terms": {
            "field": "color"
         },
         "aggs": {
            "avg_price": { "avg": { "field": "price" }
            },
            "make" : {
                "terms" : {
                    "field" : "make"
                },
                "aggs" : { 
                    "min_price" : { //自定义变量名字
                        "min": { //参数-最小值
                            "field": "price"
                            } 
                        }, 
                    "max_price" : {
                         "max": { //参数-最大值
                                 "field": "price"
                                 } 
                         } 
                }
            }
         }
      }
   }
}
```

# 查询语法

```
{
    "query":{
        
    },
    "sort":[
    
    ],
    "from":1,
    "size":10,
    "_source":["age","name"]
}
```

## query

会计算score,会根据score去排序

## filter

只是简单的过滤出想要的数据，不计算score,也不排序

## constant_score

不考虑文档分数

## term query

对搜索文本不分词，直接拿去倒排索引中匹配，你输入的是什么，就去匹配什么

```
{
    "query":{
        "term":{
            "title":"你好"
        }
    }
}
```

## terms query

```
{
    'query':{
        'terms':{
            'tag':["search",'nosql','hello']
        }
    }
}
```

## match query

```
{
   "query": {
     "match": {
       "__type": "info"
     }
   },
   "sort": [
     {
       "campaign_end_time": {
         "order": "desc"
       }
     }
   ]
}
```

## match_all

```
{
    "query":{
        "match_all":{
            "title":"标题一样"
        }
    }
}
```

## multi match

多值匹配查询

```
{
  "query": {
    "multi_match": {
      "query": "运动 上衣",
      "fields": [
        "brandName^100",
        "brandName.brandName_pinyin^100",
        "brandName.brandName_keyword^100",
        "sortName^80",
        "sortName.sortName_pinyin^80",
        "productName^60",
        "productKeyword^20"
      ],
      "type": <multi-match-type>,
      "operator": "AND"
    }
  }
}
```

## Bool query

bool查询包含四个子句，must，filter，should，must_not

```
{
    'query':{
        'bool':{
            'must':[{
                'term':{
                    '_type':{
                        'value':'age'
                    }
                }
             },{
                 'term':{
                     'account_grade':{
                         'value':'23'
                     }
                 }
             }
           ]
            
        }
    }
    
}

{
	"bool":{
            "must":{
                "term":{"user":"lucy"}
            },
            "filter":{
                "term":{"tag":"teach"}	
            },
            "should":[
              	{"term":{"tag":"wow"}},
                {"term":{"tag":"elasticsearch"}}
            ],
           	"mininum_should_match":1,
           	"boost":1.0  		            
        }
}
```

## Filter query

query和filter的区别：query查询的时候，会先比较查询条件，然后计算分值，最后返回文档结果；而filter是先判断是否满足查询条件，如果不满足会缓存查询结果（记录该文档不满足结果），满足的话，就直接缓存结果

filter快在：对结果进行缓存，避免计算分值

```
{
    "query": {
      "bool": {
        "must": [
          {"match_all": {}}
        ],
        "filter": {
          "range": {
            "create_admin_id": {
              "gte": 10,
              "lte": 20
            }
          }
        }
      }
    }
}
```

## range query

```
{
	'query':{
    	'range':{
            'age':{
                'gte':'30',
                'lte':'20'
            }
    	}
	}
}
```

## 通配符查询

```
{
    'query':{
        'wildcard':{
            'title':'cr?me'
        }
    }
    
}

```

## 正则表达式查询

```
{
    'query':{
        'regex':{
            'title':{
                'value':'cr.m[ae]',
                'boost':10.0
            }
        }
    }
}
```

## 前缀查询

```
{
    'query':{
        'match_phrase_prefix':{
            'title':{
                'query':'crime punish',
                'slop':1
            }
        }
    }
}
```

## query_string

```
{
    'query':{
        'query_string':{
            'query':'title:crime^10 +title:punishment -otitle:cat +author:(+Fyodor +dostoevsky)'
        }
    }
}
```

# 聚合查询

聚合提供了用户进行分组和数理统计的能力，可以把聚合理解成SQL中的GROUP BY和分组函数

指标聚合/桶聚合

Metrics（度量/指标）：简单的对过滤出来的数据集进行avg，max操作，是一个单一的数值

Bucket（桶）：将过滤出来的数据集按条件分成多个小数据集，然后Metrics会分别作用在这些小数据集上

## max/min/avg/sum/stats

```
{
    'aggs':{
        'group_sum':{
            'sum':{
                'field':'money'
            }
        }
    }
}

{
   "aggs":{
      "avg_fees":{
      		"avg":{
      			"field":"fees"
      		}
      	}
   }
}
```

## terms聚合

terms根据字段值项分组聚合.field按什么字段分组,size指定返回多少个分组,shard_size指定每个分片上返回多少个分组,order排序方式.可以指定include和exclude正则筛选表达式的值,指定missing设置缺省值


```
{
    'aggs':{
        'group_by_type':{
            'terms':{
                'field':'_type'
            }
        }
    }
}

{
    "size": 0, 
    "aggs": {
      "terms":{
        "terms": {
          "field": "__type",
          "size": 10
        }
      }
    }
}
{
    "size": 0, 
    "aggs": {
      "terms":{
        "terms": {
          "field": "__type",
          "size": 10,
          "order": {
            "_count": "asc"
          }
        }
      }
    }
}
{
    "size": 0, 
    "aggs": {
      "agg_terms": {
        "terms": {
          "field": "cost",
          "order": {
            "_count": "asc"
          }
        },
        "aggs": {
          "max_balance": {
            "max": {
              "field": "cost"
            }
          }
        }
      }
    }
}
{
    "size": 0, 
    "aggs": {
      "agg_terms": {
        "terms": {
          "field": "cost",
          "include": ".*",
          "exclude": ".*"
        }
      }
    }
}
```

## cardinality去重

```
{
    "size": 0, 
    "aggs": {
      "count_type": {
        "cardinality": {
          "field": "__type"
        }
      }
    }
}
```

## percentiles百分比

percentiles对指定字段（脚本）的值按从小到大累计每个值对应的文档数的占比（占所有命中文档数的百分比），返回指定占比比例对应的值。默认返回[ 1, 5, 25, 50, 75, 95, 99 ]分位上的值

```
{
    "size": 0, 
    "aggs": {
      "age_percents":{
        "percentiles": {
          "field": "age",
          "percents": [
            1,
            5,
            25,
            50,
            75,
            95,
            99
          ]
        }
      }
       
    }
}


{
  "size": 0,
  "aggs": {
    "states": {
      "terms": {
        "field": "gender"
      },
      "aggs": {
        "banlances": {
          "percentile_ranks": {
            "field": "balance",
            "values": [
              20000,
              40000
            ]
          }
        }
      }
    }
  }
```

## percentiles rank

统计小于等于指定值得文档比

```
{
    "size": 0, 
    "aggs": {
      "tests": {
        "percentile_ranks": {
          "field": "age",
          "values": [
            10,
            15
          ]
        }
      }
    }
}
```

## filter聚合

filter对满足过滤查询的文档进行聚合计算,在查询命中的文档中选取过滤条件的文档进行聚合,先过滤在聚合

```
{
    "size": 0, 
    "aggs": {
      "agg_filter":{
        "filter": {
          "match":{"gender":"F"}
        },
        "aggs": {
          "avgs": {
            "avg": {
              "field": "age"
            }
          }
        }
      }
    }
}
```

## filtters聚合

多个过滤组聚合计算

```
{
    "size": 0, 
    "aggs": {
      "message": {
        "filters": {
          
          "filters": {
            "errors": {
              "exists": {
                "field": "__type"
              }
            },
            "warring":{
              "term": {
                "__type": "info"
              }
            }
          }
        }
      }
    }
}
```

## range聚合

```
{
    "aggs": {
      "agg_range": {
        "range": {
          "field": "cost",
          "ranges": [
            {
              "from": 50,
              "to": 70
            },
            {
              "from": 100
            }
          ]
        },
        "aggs": {
          "bmax": {
            "max": {
              "field": "cost"
            }
          }
        }
      }
    }
}
```

## date_range聚合

```
{
     "aggs": {
       "date_aggrs": {
         "date_range": {
           "field": "accepted_time",
           "format": "MM-yyy", 
           "ranges": [
             {
               "from": "now-10d/d",
               "to": "now"
             }
           ]
         }
       }
     }
}
```

## date_histogram

时间直方图聚合,就是按天、月、年等进行聚合统计。可按 year (1y), quarter (1q), month (1M), week (1w), day (1d), hour (1h), minute (1m), second (1s) 间隔聚合或指定的时间间隔聚合

```
{ 
  "aggs": {
    "sales_over_time": {
      "date_histogram": {
        "field": "accepted_time",
        "interval": "quarter",
        "min_doc_count" : 0, //可以返回没有数据的月份
        "extended_bounds" : { //强制返回数据的范围
           "min" : "2014-01-01",
           "max" : "2014-12-31"
        }
      }
    }
  }
}
```

## missing聚合

```
{ 
  
  "aggs": {
    "account_missing": {
      "missing": {
        "field": "__type"
      }
    }
  }
}
```

# 嵌套和父子文档

ElasticSerch 的连接查询有两种方式实现

- nested
- parent和child关联查询

## nested嵌套对象

```
PUT /my_index/_doc/1
{
  "title": "Nest eggs",
  "body":  "Making your money work...",
  "tags":  [ "cash", "shares" ],
  "comments": [ 
    {
      "name":    "John Smith",
      "comment": "Great article",
      "age":     28,
      "stars":   4,
      "date":    "2014-09-01"
    },
    {
      "name":    "Alice White",
      "comment": "More like this please",
      "age":     31,
      "stars":   5,
      "date":    "2014-10-22"
    }
  ]
}
```

如果我们依赖字段自动映射,那么 comments 字段会自动映射为 object 类型。

由于所有的信息都在一个文档中,当我们查询时就没有必要去联合文章和评论文档,查询效率就很高。

但是当我们使用如下查询时,上面的文档也会被当做是符合条件的结果：

```
GET /_search
{
  "query": {
    "bool": {
      "must": [
        { "match": { "name": "Alice" }},
        { "match": { "age":  28      }} 
      ]
    }
  }
}
```

Alice实际是31岁,不是28!但是仍然被认为是复合条件的

正如我们在 对象数组 中讨论的一样,出现上面这种问题的原因是 JSON 格式的文档被处理成如下的扁平式键值对的结构。

```
{
  "title":            [ eggs, nest ],
  "body":             [ making, money, work, your ],
  "tags":             [ cash, shares ],
  "comments.name":    [ alice, john, smith, white ],
  "comments.comment": [ article, great, like, more, please, this ],
  "comments.age":     [ 28, 31 ],
  "comments.stars":   [ 4, 5 ],
  "comments.date":    [ 2014-09-01, 2014-10-22 ]
}

```

Alice 和 31 、 John 和 2014-09-01 之间的相关性信息不再存在。虽然 object 类型 (参见 内部对象) 在存储 单一对象 时非常有用,但对于对象数组的搜索而言,毫无用处。

嵌套对象 就是来解决这个问题的。将 comments 字段类型设置为 nested 而不是 object 后,每一个嵌套对象都会被索引为一个 隐藏的独立文档 ,举例如下:

第一个 嵌套文档

第二个 嵌套文档

根文档 或者也可称为父文档

嵌套对象 就是来解决这个问题的。将 comments 字段类型设置为 nested 而不是 object 后,每一个嵌套对象都会被索引为一个 隐藏的独立文档 ,举例如下:

在独立索引每一个嵌套对象后,对象中每个字段的相关性得以保留。我们查询时,也仅仅返回那些真正符合条件的文档。

不仅如此,由于嵌套文档直接存储在文档内部,查询时嵌套文档和根文档联合成本很低,速度和单独存储几乎一样。

嵌套文档是隐藏存储的,我们不能直接获取。如果要增删改一个嵌套对象,我们必须把整个文档重新索引才可以。值得注意的是,查询的时候返回的是整个文档,而不是嵌套文档本身。


## 父子文档

父-子关系文档 在实质上类似于 nested model ：允许将一个对象实体和另外一个对象实体关联起来。 而这两种类型的主要区别是：在 nested objects 文档中，所有对象都是在同一个文档中，而在父-子关系文档中，父对象和子对象都是完全独立的文档。

父-子关系的主要作用是允许把一个 type 的文档和另外一个 type 的文档关联起来，构成一对多的关系：一个父文档可以对应多个子文档 。与 nested objects 相比，父-子关系的主要优势有：

更新父文档时，不会重新索引子文档。
创建，修改或删除子文档时，不会影响父文档或其他子文档。这一点在这种场景下尤其有用：子文档数量较多，并且子文档创建和修改的频率高时。
子文档可以作为搜索结果独立返回。
Elasticsearch 维护了一个父文档和子文档的映射关系，得益于这个映射，父-子文档关联查询操作非常快。但是这个映射也对父-子文档关系有个限制条件：父文档和其所有子文档，都必须要存储在同一个分片中。

父-子文档ID映射存储在 Doc Values 中。当映射完全在内存中时， Doc Values 提供对映射的快速处理能力，另一方面当映射非常大时，可以通过溢出到磁盘提供足够的扩展能力。

 

当文档索引性能远比查询性能重要 的时候，父子关系是非常有用的，但是它也是有巨大代价的。其查询速度会比同等的嵌套查询慢5到10倍!

### 创建父子文档语法

首先看一下如何建立父子文档，明显和网上”_parent”的方式不一样，说明es后期版本已经修改了语法

```
PUT my_index
{
  "mappings": {
    "properties": {
      "my_join_field": { 
        "type": "join",
        "relations": {
          "question": "answer" 
        }
      }
    }
  }
}
```

这段代码建立了一个my_index的索引，其中my_join_field是一个用于join的字段，type为join，关系relations为：父为question, 子为answer
至于建立一父多子关系，只需要改为数组即可："question": ["answer", "comment"]

### 插入数据

插入两个父文档，语法如下

```
PUT my_index/_doc/1?refresh
{
  "text": "This is a question",
  "my_join_field": {
    "name": "question" 
  }
}
```

同时也可以省略name

```
PUT my_index/_doc/1?refresh
{
  "text": "This is a question",
  "my_join_field": "question"
}

```

### 插入子文档

子文档的插入语法如下，注意routing是父文档的id，平时我们插入文档时routing的默认就是id
此时name为answer，表示这是个子文档

```
PUT /my_index/_doc/3?routing=1
{
  "text": "This is an answer",
  "my_join_field": {
    "name": "answer", 
    "parent": "1" 
  }
```

### 通过parent_id查询子文档

通过parent_id query传入父文档id即可

```
GET my_index/_search
{
  "query": {
    "parent_id": { 
      "type": "answer",
      "id": "1"
    }
  }
}
```

### 父-子文档的性能及限制性

父-子文档主要适用于一对多的实体关系，将其反范式存入文档中

父-子文档主要由以下特性：

- 每个索引只能有一个join字段
- 父-子文档必须在同一个分片上，也就是说增删改查一个子文档，必须使用和父文档一样的routing key(默认是id)
- 每个元素可以有多个子，但只有一个父
- 可以为一个已存在的join字段添加新的关联关系
- 可以在一个元素已经是父的情况下添加一个子

### 总结

es中通过父子文档来实现join，但在一个索引中只能有一个一父多子的join

### 关系字段

es会自动生成一个额外的用于表示关系的字段：field#parent
我们可以通过以下方式查询

```
POST my_index/_search
{
 "script_fields": {
    "parent": {
      "script": {
         "source": "doc['my_join_field#question']" 
      }
    }
  }
}

```

部分响应为

```
{
"_index" : "my_index",
"_type" : "_doc",
"_id" : "8",
"_score" : 1.0,
"fields" : {
  "parent" : [
    "8"
  ]
}
},
{
"_index" : "my_index",
"_type" : "_doc",
"_id" : "4",
"_score" : 1.0,
"_routing" : "10",
"fields" : {
  "parent" : [
    "10"
  ]
}
}
```

有_routing字段的说明是子文档，它的parent字段是父文档id，如果没有_routing就是父文档，它的parent指向当前id

### 全局序列

父-子文档的join查询使用一种叫做全局序列(Global ordinals)的技术来加速查询，它采用预加载的方式构建，防止在第一次查询或聚合时出现太长时间的延迟，但在索引元数据改变时重建，父文档越多，构建时间就越长，重建在refresh时进行，这会造成refresh大量延迟时间(在refresh时也是预加载).
如果join字段很少用，可以关闭这种预加载模式:"eager_global_ordinals": false

#### 全局序列的监控

```
# 每个索引
curl -X GET "localhost:9200/_stats/fielddata?human&fields=my_join_field#question&pretty"
# 每个节点上的每个索引
curl -X GET "localhost:9200/_nodes/stats/indices/fielddata?human&fields=my_join_field#question&pretty"
```

### 一父多子的祖孙结构

#### 考虑以下结构

```
 question
    /    \
   /      \
comment  answer
           |
           |
          vote
```

#### 建立索引

```
PUT my_index
{
  "mappings": {
    "properties": {
      "my_join_field": {
        "type": "join",
        "relations": {
          "question": ["answer", "comment"],  
          "answer": "vote" 
        }
      }
    }
  }
}
```

#### 插入孙子节点

注意这里的routing和parent值不一样,routing指的是祖父字段，即question,而parent指的就是字面意思answer

```
PUT my_index/_doc/3?routing=1&refresh 
{
  "text": "This is a vote",
  "my_join_field": {
    "name": "vote",
    "parent": "2" 
  }
}
```


#### has-child查询

查询包含特定子文档的父文档，这是一种很耗性能的查询，尽量少用。它的查询标准格式如下

```
GET my_index/_search
{
    "query": {
        "has_child" : {
            "type" : "child",
            "query" : {
                "match_all" : {}
            },
            "max_children": 10, //可选，符合查询条件的子文档最大返回数
            "min_children": 2, //可选，符合查询条件的子文档最小返回数
            "score_mode" : "min"
        }
    }
}
```

# 实战

## 同时使用keyword和text类型

注：term是查询时对关键字不分词，keyword是索引时不分词

上述我们讲解过keyword和text一个不分词索引，一个是分词后索引，我们利用他们的fields属性来让当前字段同时具备keyword和text类型。

首先我们创建索引并指定mapping，为title同时设置keyword和text属性

```
PUT /idx_item/
{
  "settings": {
    "index": {
        "number_of_shards" : "2",
        "number_of_replicas" : "0"
    }
  },
  "mappings": {
    "properties": {
      "itemId" : {
        "type": "keyword",
        "ignore_above": 64
      },
      "title" : {
        "type": "text",
        "analyzer": "ik_max_word", 
        "search_analyzer": "ik_smart", 
        "fields": {
          "keyword" : {"ignore_above" : 256, "type" : "keyword"}
        }
      },
      "desc" : {"type": "text", "analyzer": "ik_max_word"},
      "num" : {"type": "integer"},
      "price" : {"type": "long"}
    }
  }
}
```

我们已经往es中插入以下数据

```
PUT /idx_item/_doc/1
{"itemId":"1","title":"厨房能手威猛先生","desc":"你煲粥，我洗锅","num":1,"price":100}

PUT /idx_item/_doc/2
{"itemId":"2","title":"厨房能手威猛先生","desc":"你煲粥，我洗锅","num":1,"price":100}

PUT /idx_item/_doc/3
{"itemId":"1","title":"苏泊尔煮饭SL3200","desc":"让煮饭更简单，让生活更快乐","num":1,"price":200}
```

title=”苏泊尔煮饭SL3200“ 根据text以及最细粒度分词设置"analyzer": "ik_max_word"，在es中按照以下形式进行索引存储

```
{ "苏泊尔","煮饭", "sl3200", "sl","3200"}
```

title.keyword=”苏泊尔煮饭SL3200因为不分词，所以在es中索引存储形式为

```
苏泊尔煮饭SL3200
```

我们首先对title.keyword进行搜索，只能搜索到第一条数据，因为match搜索会将关键字分词然后去搜索，分词后的结果包含"苏泊尔煮饭SL3200"所以搜索成功，我们将搜索关键字改为苏泊尔、煮饭等都不会查到数据。

```
GET idx_item/_search
{
  "query": {
    "bool": {
      "must": {
        "match": {"title.keyword": "苏泊尔煮饭SL3200"}
        }
    }
  }
}
```

我们改用term搜索，他搜索不会分词，正好与es中的数据精准匹配，也只有第一条数据，我们将搜索关键字改为苏泊尔、煮饭等都不会查到数据。

```
GET idx_item/_search
{
  "query": {
    "bool": {
      "must": {
        "term": {"title.keyword": "苏泊尔煮饭SL3200"}
        }
    }
  }
}
```

我们继续对title使用match进行查询，结果查到了第一条和第三条数据，因为它们在es中被索引的数据包含苏泊尔关键字

```
GET idx_item/_search
{
  "query": {
    "bool": {
      "must": {"match": {"title": "苏泊尔"}
        }
    }
  }
}
```

我们如果搜索苏泊尔煮饭SL3200会发现没有返回数据，因为title在索引时没有苏泊尔煮饭SL3200这一项，而term时搜索关键字也不分词，所以无法匹配到数据。但是我们将内容改为苏泊尔时，就可以搜索到第一条和第三条内容，因为第一条和第三条的title被分词后的索引包含苏泊尔字段，所以可以查出第一三条。

```
"term": {"title": "苏泊尔煮饭SL3200"}
```

## 实战：格式化时间、以及按照时间排序

我们创建索引idx_pro，将mytimestamp和createTime字段分别格式化成两种时间格式

```
PUT /idx_pro/
{
  "settings": {
    "index": {
        "number_of_shards" : "2",
        "number_of_replicas" : "0"
    }
  },
  "mappings": {
    "properties": {
      "proId" : {
        "type": "keyword",
        "ignore_above": 64
      },
      "name" : {
        "type": "text",
        "analyzer": "ik_max_word", 
        "search_analyzer": "ik_smart", 
        "fields": {
          "keyword" : {"ignore_above" : 256, "type" : "keyword"}
        }
      },
      "mytimestamp" : {
        "type": "date",
        "format": "epoch_millis"
      },
      "createTime" : {
        "type": "date",
        "format": "yyyy-MM-dd HH:mm:ss"
      }
    }
  }
}
```

插入四组样本数据

```
POST idx_pro/_doc/1
{
  "proId" : "1",
  "name" : "冬日工装裤",
  "timestamp" : 1576312053946,
  "createTime" : "2019-12-12 12:56:56"
}
POST idx_pro/_doc/2
{
  "proId" : "2",
  "name" : "冬日羽绒服",
  "timestamp" : 1576313210024,
  "createTime" : "2019-12-10 10:50:50"
}
POST idx_pro/_doc/3
{
  "proId" : "3",
  "name" : "花花公子外套",
  "timestamp" : 1576313239816,
  "createTime" : "2019-12-19 12:50:50"
}
POST idx_pro/_doc/4
{
  "proId" : "4",
  "name" : "花花公子羽绒服",
  "timestamp" : 1576313264391,
  "createTime" : "2019-12-12 11:56:56"
}
```

我们可以使用sort参数来进行排序，并且支持数组形式，即同时使用多字段排序，只要改为[]就行

```
GET idx_pro/_search
{
  "sort":{"createTime": {"order": "asc"}}, 
  "query": {
    "bool": {
      "must": {"match_all": {}}
    }
  }
}
```

我们也可以使用range参数来搜索指定时间范围内的数据，当然range也支持integer、long等类型

```
GET idx_pro/_search
{
  "query": {
    "bool": {
      "must": {
        "range": {
          "timestamp": {
            "gt": "1576313210024",
            "lt": "1576313264391"
          }
        }
      }
    }
  }
}
```

# ES性能调优


# 写入性能优化

1. 用bulk批量写入  
   你如果要往es里面灌入数据的话，那么根据你的业务场景来。如果你的业务场景可以支持，可以做到，让你将一批数据聚合起来，一次性写入ES,那么就尽量采用bulk的方式，每次批量写入几百条
   bulk批量写入的性能比你一条一条写入大量的document的性能要号很多，但是如果要知道一个bulk请求最佳的大小，需要对单个ES的node单个shard做压测，来测试性能
2. 使用多线程写入es  
   单线程发送bulk请求是无法最大化es集群写入的吞吐亮的。如果要利用集群的所有资源，就需要使用多线程并打将数据bulk写入集群，这样多线程并发写入，可以减少每次底层磁盘fsync的次数和开销。对单个es节点的某个shard做压测，每次线程数量倍增，一旦发现es返回TOO MANY REQUESTS的错误，javaClient也就是EsRejectedExecutionException,此时就说明es已经到了一个并发写入的最大瓶颈了，
3. 增加refresh间隔  
   默认的refresh间隔是1s,用index.refresh_interval参数可以设置，这样会强迫es每秒中都将内存中的数据写入磁盘中，创建一个新segment file,正是这个间隔，让我们每次写入数据后，1s以后才能看到，但是如果我们将这个间隔调大，比如30s,可以接受写入的数据30s后才能看到，那么我们就可以获取最大的写入吞吐
4. 禁止refresh和replis  
   如果我们要一次性的加载大批量数据进es，可以禁止refresh和replis复制，将index.refresh_interval设置为-1，将index.number_of_replicas设置为0即可，这可能会导致我们数据丢失，因为没有redresh和replica机制了。此时写入的数独会很快，一旦写完之后，可以将refresh和replica修改回正常的状态
5. 给filesystem cach更多的内存  
   filesystem cach被用来执行更多操作，如果能给filesystem cache更多的内存资源，那么es的写入性能会好很多
6. 使用自动生成的id  
   如果我们手动给es的document设置一个id，那么es需要梅西都去确认一下那个id是否存在，这个过程是比较耗费时间的，如果我们使用自动生成的id，那么es就可以提阿果这个步骤，写入性能会更好
7. index buffer  
   如果我们要进行非常重的高并发写入操作，那么最好将index buffer设置的大一点，indeices.memory.index_buffer_size,这个可以调节大一些，设置的这个index buffer大小，是所有的sherd公用的，但是如果除以shard数量以后，算出平均每个shard使用内存大小，一般建议，但是对于每个shard来说，最多给512M因为在打性能就没有提升了，es会将这个设置作为每个shard共享的index buffer,那些特别活跃的shard会更多的使用这个buffer,默认这个参数值是10%，也就是jvm heap的10%，如果我们给JVM分配10g内存，那么index buffer就是1g，对于两个shard共享来说，足够了

# 搜索性能优化