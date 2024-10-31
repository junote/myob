
### key_value

```

typedef std::pair<std::string, std::string> FieldValueTuple;

#define fvField std::get<0>

#define fvValue std::get<1>


```

```
typedef std::tuple<std::string, std::string, std::vector<FieldValueTuple> > KeyOpFieldsValuesTuple;

#define kfvKey    std::get<0>

#define kfvOp     std::get<1>

#define kfvFieldsValues std::get<2>
```


### redis_databases

use 'select id' to switch

| name               | id  | seperator |
| ------------------ | --- | --------- |
| APPL_DB            | 0   | :         |
| ASIC_DB            | 1   | :         |
| COUNTERS_DB        | 2   | :         |
| LOGLEVEL_DB        | 3   | :         |
| CONFIG_DB          | 4   | \|        |
| PFC_WD_DB          | 5   | :         |
| FLEX_COUNTER_DB    | 5   | :         |
| STATE_DB           | 6   | \|        |
| SNMP_OVERLAY_DB    | 7   | \|        |
| RESTAPI_DB         | 8   | \|        |
| GB_ASIC_DB         | 9   | \|        |
| GB_COUNTERS_DB     | 10  | \|        |
| GB_FLEX_COUNTER_DB | 11  | \|        |
| CHASSIS_APP_DB     | 12  | \|        |
| CHASSIS_STATE_DB   | 13  | \|        |
|                    |     |           |

### redis_connect

```

SonicV2Connector_Native::connect(m_db_name, retry_on);

auto& client = get_redis_client(m_db_name);

```

### redis_commands

```

publish
get

hmset
set
del

Evalsha 命令根据给定的 sha1 校验码，执行缓存在服务器中的脚本
Redis Sadd 命令将一个或多个成员元素加入到集合中

```


### table

use redis hashset


```

typedef std::map<std::string,std::string> TableMap;

typedef std::map<std::string,TableMap> TableDump;

Table(const DBConnector *db, const std::string &tableName);

Table(RedisPipeline *pipeline, const std::string &tableName, bool buffered);


    /* Set an entry in the DB directly (op not in use) */

    virtual void set(const std::string &key,

                     const std::vector<FieldValueTuple> &values,

                     const std::string &op = "",

                     const std::string &prefix = EMPTY_PREFIX);

    /* Delete an entry in the table */

    virtual void del(const std::string &key,

                     const std::string &op = "",

                     const std::string &prefix = EMPTY_PREFIX);



```




### json


```

nlohmann::json


buildJson
readJson
loadJsonFromFile

```


### logger

```

#define SWSS_LOG_ERROR(MSG, ...)       swss::Logger::getInstance().write(swss::Logger::SWSS_ERROR,  ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)

#define SWSS_LOG_WARN(MSG, ...)        swss::Logger::getInstance().write(swss::Logger::SWSS_WARN,   ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)

#define SWSS_LOG_NOTICE(MSG, ...)      swss::Logger::getInstance().write(swss::Logger::SWSS_NOTICE, ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)

#define SWSS_LOG_INFO(MSG, ...)        swss::Logger::getInstance().write(swss::Logger::SWSS_INFO,   ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)

#define SWSS_LOG_DEBUG(MSG, ...)       swss::Logger::getInstance().write(swss::Logger::SWSS_DEBUG,  ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)

  

#define SWSS_LOG_ENTER()               swss::Logger::ScopeLogger logger ## __LINE__ (__LINE__, __FUNCTION__)

#define SWSS_LOG_TIMER(msg, ...)       swss::Logger::ScopeTimer scopetimer ## __LINE__ (__LINE__, __FUNCTION__, msg, ##__VA_ARGS__)

  

#define SWSS_LOG_THROW(MSG, ...)       swss::Logger::getInstance().wthrow(swss::Logger::SWSS_ERROR,  ":- %s: " MSG, __FUNCTION__, ##__VA_ARGS__)


```


### select

```
Selectable
epoll_create1
void Select::addSelectable(Selectable *selectable)


SelectableEvent
eventfd
readData
notify

SelectableTimer
timerfd_create
start
stop


```


### producer

```
ProducerTable
void ProducerTable::set(const string &key, const vector<FieldValueTuple> &values, const string &op, const string &prefix)

void ProducerTable::del(const string &key, const string &op, const string &prefix)

ProducerStateTable

void ProducerStateTable::set(const string &key, const vector<FieldValueTuple> &values,const string &op /*= SET_COMMAND*/, const string &prefix)

void ProducerStateTable::del(const string &key, const string &op /*= DEL_COMMAND*/, const string &prefix)

clear


```

### consumer

```

ConsumerStateTable

void pops(std::deque<KeyOpFieldsValuesTuple> &vkco, const std::string &prefix = EMPTY_PREFIX);


ConsumerStateTable

pops(std::deque<KeyOpFieldsValuesTuple> &vkco, const std::string& /*prefix*/)

```

### notification
```
NotificationProducer

send(const std::string &op, const std::string &data, std::vector<FieldValueTuple> &values)

NotificationConsumer
subscribe
readData
processReply
pop
pops
```