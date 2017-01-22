# Kibana playground

Users

- kibana/changeme
- elastic/changeme

# Demo data

https://www.elastic.co/guide/en/kibana/current/getting-started.html

- shakespeare.json
- accounts.json 
- logs.jsonl

## Sample Data

### shakespeare

```javascript
{
    "line_id": INT,
    "play_name": "String",
    "speech_number": INT,
    "line_number": "String",
    "speaker": "String",
    "text_entry": "String",
}
```

### accounts

```javascript
{
    "account_number": INT,
    "balance": INT,
    "firstname": "String",
    "lastname": "String",
    "age": INT,
    "gender": "M or F",
    "address": "String",
    "employer": "String",
    "email": "String",
    "city": "String",
    "state": "String"
}
```

### logs.jsonl

```javascript
{
    "memory": INT,
    "geo.coordinates": "geo_point"
    "@timestamp": "date"
}
```

## Change password

```bash
curl -XPUT -u elastic 'localhost:9200/_xpack/security/user/elastic/_password' -d '{
  "password" : "elasticpassword"
}'

curl -XPUT -u kibana 'localhost:9200/_xpack/security/user/kibana/_password' -d '{
  "password" : "kibanapassword"
}'

# test
curl -u kibana:kibanapassword http://localhost:9200
```

More about security https://www.elastic.co/guide/en/x-pack/current/security-getting-started.html#security-getting-started


## Mapping dataset

```bash
curl -u elastic:changeme -XPUT http://localhost:9200/shakespeare -d '
{
 "mappings" : {
  "_default_" : {
   "properties" : {
    "speaker" : {"type": "string", "index" : "not_analyzed" },
    "play_name" : {"type": "string", "index" : "not_analyzed" },
    "line_id" : { "type" : "integer" },
    "speech_number" : { "type" : "integer" }
   }
  }
 }
}
';

curl -u elastic:changeme -XPUT http://localhost:9200/logstash-2015.05.18 -d '
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}

';
curl -u elastic:changeme -XPUT http://localhost:9200/logstash-2015.05.19 -d '
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}

';
curl -u elastic:changeme -XPUT http://localhost:9200/logstash-2015.05.20 -d '
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}
';
```

### Upload data

```bash
curl -u elastic:changeme -XPOST 'localhost:9200/bank/account/_bulk?pretty' \
       --data-binary @./sample-data/accounts.json
curl -u elastic:changeme -XPOST 'localhost:9200/shakespeare/_bulk?pretty' \
       --data-binary @./sample-data/shakespeare.json
curl -u elastic:changeme -XPOST 'localhost:9200/_bulk?pretty' \
       --data-binary @./sample-data/logs.jsonl

# check
curl -u elastic:changeme 'localhost:9200/_cat/indices?v'
curl -u elastic:changeme 'localhost:9200/_cluster/health?pretty=true'
```

## Defining Your Index Patterns


