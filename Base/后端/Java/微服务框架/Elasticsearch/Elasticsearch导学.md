1. [es入门](分布式搜索引擎01.md)

   > 1. es介绍安装
   > 2. [ik分词器的安装](安装elasticsearch/安装elasticsearch.md)
   > 3. 索引库CRUD
   > 4. 文档的CRUD

2. [es搜索](分布式搜索引擎02.md)

   > 1. DSL的各个类型
   >    - 全文查询（单字段、多字段）
   >    - 精确查询（term、range）
   >    - 地理坐标
   >    - 复合查询（算分、布尔）
   > 2. 搜索结果处理
   >    - 排序（顺逆、地理坐标）
   >    - 分页
   >    - 高亮

3. [es聚合](分布式搜索引擎03.md)

   > 1. 聚合
   > 2. 聚合和搜索共同使用（1.3）
   > 3. 拼音分词器（2.5）
   > 4. 自动补全功能
   > 5. 数据库和es数据同步(mq版本)
   > 6. es集群问题[集群安装](安装elasticsearch/安装elasticsearch.md)



- 相关es代码

```
GET _search
{
  "query": {
    "match_all": {}
  }
}

# 模拟请求
GET /_analyze
{
  "text": "在这个宁静的夜晚，白嫖,the scholar immersed himself in ancient books, exploring the mysteries of time. His candle flickered on the desk, casting wavering shadows. The ancient scrolls unfolded, revealing secrets of bygone eras.",
  "analyzer": "ik_max_word"
}

# 创建索引库
PUT /test
{
  "mappings": {
    "properties": {
      "info": {
        "type": "text",
        "analyzer": "ik_smart"
      },
      "email": {
        "type": "keyword",
        "index": false
      },
      "name": {
        "type": "object",
        "properties": {
          "firstName": {
            "type": "keyword"
          },
          "lastName": {
            "type": "keyword"
          }
        }
      }
    }
  }
}

# 修改索引库
PUT /test/_mapping
{
  "properties": {
    "age": {
      "type": "integer"
    }
  }
}

# 查看索引库
GET /test

# 删除索引库
DELETE /test


# 新增文档
POST /test/_doc/1
{
  "info": "夜深人静，星空闪烁，moonlit night，窗外传来虫鸣声，静谧中流淌着悠扬的琴音。",
  "email": "congmu@email",
  "name": {
    "firstName": "云",
    "lastName": "间"
  }
}


# 查询文档
GET /test/_doc/1


# 删除文档
DELETE /test/_doc/1

# 修改文档
# 全量修改
PUT /test/_doc/1
{
  "info": "夜深人静",
  "email": "congmu@email",
  "name": {
    "firstName": "云",
    "lastName": "间"
  }
}

# 增量修改
POST /test/_update/1
{
  "doc": {
    "email": "qq.com@qng"
  }
}


# 酒店的mapping
PUT /hotel
{
  "mappings": {
    "properties": {
      "id": {
        "type": "keyword"
      },
      "name":{
        "type": "text",
        "analyzer": "ik_max_word",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword",
        "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword",
        "copy_to": "all"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword"
      },
      "location":{
        "type": "geo_point"
      },
      "pic":{
        "type": "keyword",
        "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "ik_max_word"
      }
    }
  }
}

DELETE /hotel

GET /hotel

GET /hotel/_doc/61083

GET /hotel/_search



# 查询所有
GET /hotel/_search
{
  "query": {
    "match_all": {}
  }
}

# match查询
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "外滩如家"
    }
  }
}

# match查询
GET /hotel/_search
{
  "query": {
    "multi_match": {
      "query": "外滩如家",
      "fields": ["brand", "name", "business"]
    }
  }
}


# term查询
GET /hotel/_search
{
  "query": {
    "term": {
      "city": {
        "value": "上海"
      }
    }
  }
}

# range查询
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "gte": 1000,
        "lte": 3000
      }
    }
  }
}

# distance查询
GET /hotel/_search
{
  "query": {
    "geo_distance": {
      "distance": "15km",
      "location": "31.21, 121.5"
    }
  }
}

# function score查询
GET /hotel/_search
{
  "query": {
    "function_score": {
      "query": {
        "match": {
          "all": "外滩"
        }
      },
      "functions": [
        {
          "filter": {
            "term": {
              "brand": "如家"
            }
          },
          "weight": 10
        }
      ]
    }
  }
}

# bool查询
GET /hotel/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "name": "如家"
          }
        }
      ],
      "must_not": [
        {
          "range": {"price": {"gt": 400}}
        }
      ],
      "filter": [
        {
          "geo_distance": {
            "distance": "10km",
            "location": {
              "lat": 31.21,
              "lon": 121.5
            }
          }
        }
      ]
    }
  }
}


# sort排序
GET /hotel/_search
{
  "query": {
    "match_all":{}
  },
  "sort": [
    {
      "score": "desc"
    },
    {
      "price": "asc"
    }
  ]
}

# 查询地址121.48941,31.40527周围的
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "sort": [
    {
      "_geo_distance": {
        "location": {
          "lat": 31.40527,
          "lon": 121.48941
        },
        "order": "asc",
        "unit": "km"
      }
    }
  ]
}

# 分页查询
GET /hotel/_search
{
  "query": {
    "match_all":{}
  },
  "sort": [
    {
      "score": "desc"
    },
    {
      "price": "asc"
    }
  ],
  "from": 0,
  "size": 20
}


# 高亮查询
GET /hotel/_search
{
  "query": {
    "match": {
      "all": "如家"
    }
  },
  "highlight": {
    "fields": {
      "name": {
        "require_field_match": "false"
      }
    }
  }
}


# 增加广告
POST /hotel/_update/1902197537
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/2056126831
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/1989806195
{
    "doc": {
        "isAD": true
    }
}
POST /hotel/_update/2056105938
{
    "doc": {
        "isAD": true
    }
}

# 聚合功能
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 10
      }
    }
  }
}


# 聚合功能, 自定义排序规则
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 20,
        "order": {
          "_count": "asc"
        }
      }
    }
  }
}

# 聚合功能, 限定聚合范围
GET /hotel/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 200
      }
    }
  },
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 20
      }
    }
  }
}

# 嵌套聚合metric
GET /hotel/_search
{
  "size": 0,
  "aggs": {
    "brandAgg": {
      "terms": {
        "field": "brand",
        "size": 20,
        "order": {
          "scoreAgg.avg": "desc"
        }
      },
      "aggs": {
        "scoreAgg": {
          "stats": {
            "field": "score"
          }
        }
      }
    }
  }
}

# 拼音分词器
POST /test/_analyze
{
  "text": ["如家酒店还不错"],
  "analyzer": "my_analyzer"
}

PUT /test
{
  "settings": {
    "analysis": {
      "analyzer": { 
        "my_analyzer": {  
          "tokenizer": "ik_max_word",
          "filter": "py"
        }
      },
      "filter": { 
        "py": { 
          "type": "pinyin", 
		  "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "name": {
        "type": "text",
        "analyzer": "my_analyzer",
        "search_analyzer": "ik_smart"
      }
    }
  }
}

DELETE /test

# 创建索引库
PUT test2
{
  "mappings":  {
      "properties":  {
          "title":{
              "type":  "completion"
          }
      }
  }
}

# 示例数据
POST test2/_doc
{
    "title":  ["Sony",  "WH-1000XM3"]
}
POST  test2/_doc
{
    "title":  ["SK-II",  "PITERA"]
}
POST  test2/_doc
{
    "title":  ["Nintendo",  "switch"]
}

# 自动补全查询
GET /test2/_search
{
  "suggest": {
    "titleSuggest": {
      "text": "s",
      "completion": {
        "field": "title",
        "skip_duplicates": true,
        "size": 10
      }
    }
  }
}

# 查看酒店数据类型
GET /hotel/_mapping


# 删除索引库
DELETE /hotel

# 酒店数据索引库
PUT /hotel
{
  "settings": {
    "analysis": {
      "analyzer": {
        "text_anlyzer": {
          "tokenizer": "ik_max_word",
          "filter": "py"
        },
        "completion_analyzer": {
          "tokenizer": "keyword",
          "filter": "py"
        }
      },
      "filter": {
        "py": {
          "type": "pinyin",
          "keep_full_pinyin": false,
          "keep_joined_full_pinyin": true,
          "keep_original": true,
          "limit_first_letter_length": 16,
          "remove_duplicated_term": true,
          "none_chinese_pinyin_tokenize": false
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "id":{
        "type": "keyword"
      },
      "name":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart",
        "copy_to": "all"
      },
      "address":{
        "type": "keyword",
        "index": false
      },
      "price":{
        "type": "integer"
      },
      "score":{
        "type": "integer"
      },
      "brand":{
        "type": "keyword",
        "copy_to": "all"
      },
      "city":{
        "type": "keyword"
      },
      "starName":{
        "type": "keyword"
      },
      "business":{
        "type": "keyword",
        "copy_to": "all"
      },
      "location":{
        "type": "geo_point"
      },
      "pic":{
        "type": "keyword",
        "index": false
      },
      "all":{
        "type": "text",
        "analyzer": "text_anlyzer",
        "search_analyzer": "ik_smart"
      },
      "suggestion":{
          "type": "completion",
          "analyzer": "completion_analyzer"
      }
    }
  }
}

# 查询
GET /hotel/_search
{
  "query": {
    "match_all": {}
  },
  "size": 20
}


# 测试分词

GET /hotel/_search
{
  "suggest": {
    "suggestions": {
      "text": "h",
      "completion": {
        "field": "suggestion",
        "skip_duplicates": true,
        "size": 10
      }
    }
  }
}
```

