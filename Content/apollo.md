
# Apollo GraphQL 学习


## 一、学习过程

### 1. 下载代码

```bash
git clone https://github.com/apollographql/fullstack-tutorial.git
```

### 2. 安装Server

```bash
cd start/server && npm install
```

### 3. 编码

1. 修改文件 ```start/server/src/schema.js```

```javascript

const { gql } = require('apollo-server');

const typeDefs = gql `
    type Launch {
        id: ID!
        site: String
        mission: Mission
        rocket: Rocket
        isBooked: Boolean!
    }
    type Rocket {
        id: ID!
        name: String
        type: String
    }
    type User {
        id: ID!
        email: String!
        trips: [Launch]!
    }
    type Mission {
        name: String
        missionPatch(size: PatchSize): String
    }
    type TripUpdateResponse {
        success: Boolean!
        message: String
        launches: [Launch]
    }
    enum PatchSize {
        SMALL
        LARGE
    }
    type Query {
        launches: [Launch] !
        launch(id: ID!): Launch
        me: User
    }
    type Mutation {
        bookTrips(launchIds: [ID]!): TripUpdateResponse!
        cancelTrip(launchId: ID!): TripUpdateResponse!
        login(email: String): String
    }
`;

module.exports = typeDefs;

```


2. 修改文件 ```start/server/src/index.js```

```javascript

const { ApolloServer } = require('apollo-server');
const typeDefs = require('./schema');
const server = new ApolloServer({ typeDefs });
server.listen().then(({ url }) => {
    console.log(`sevrer ready at url : ${url}`);
});

```


### 4. 启动server

1. 在 ```start/server``` 目录下执行 ```npm start```

2. 就可以访问 ```http://localhost:4000/```

3. Apollo会判断 ```NODE_ENV``` 是否为 ```production```，如果是，则会关闭 introspect 功能


### 5. 数据源介绍

数据源可以是 database、service、API等等

#### 5.1 采用 RESTDataSource 连接 REST API数据源

1. 配置数据源：修改 ```start/server/src/datasources/launch.js```文件

```javascript

const { RESTDataSource } = require('apollo-datasource-rest');

class LaunchAPI extends RESTDataSource {
    constructor() {
        super();
        this.baseURL = 'https://api.spacexdata.com/v2/';
    }
}

module.exports = LaunchAPI;

```

2. 写方法调用 REST 数据，继续修改 ```start/server/src/datasources/launch.js``` 文件

```javascript

const { RESTDataSource } = require('apollo-datasource-rest');

class LaunchAPI extends RESTDataSource {
    constructor() {
        super();
        this.baseURL = 'https://api.spacexdata.com/v2/';
    }
    async getAllLaunches() {
        const response = await this.get('launches');
        return Array.isArray(response)
          ? response.map(launch => this.launchReducer(launch))
          : [];
    }
}

module.exports = LaunchAPI;

```

3. 处理 REST 数据烦恼会，继续修改 ```start/server/src/datasources/launch.js``` 文件

```javascript

const { RESTDataSource } = require('apollo-datasource-rest');

class LaunchAPI extends RESTDataSource {
    constructor() {
        super();
        this.baseURL = 'https://api.spacexdata.com/v2/';
    }
    async getAllLaunches() {
        const response = await this.get('launches');
        return Array.isArray(response)
          ? response.map(launch => this.launchReducer(launch))
          : [];
    }
    // 数据转换
    launchReducer(launch) {
        return {
          id: launch.flight_number || 0,
          cursor: `${launch.launch_date_unix}`,
          site: launch.launch_site && launch.launch_site.site_name,
          mission: {
            name: launch.mission_name,
            missionPatchSmall: launch.links.mission_patch_small,
            missionPatchLarge: launch.links.mission_patch,
          },
          rocket: {
            id: launch.rocket.rocket_id,
            name: launch.rocket.rocket_name,
            type: launch.rocket.rocket_type,
          },
        };
    }
    async getLaunchById({ launchId }) {
        const response = await this.get('launches', { flight_number: launchId });
        return this.launchReducer(response[0]);
    }
    getLaunchesByIds({ launchIds }) {
        return Promise.all(
            launchIds.map(launchId => this.getLaunchById({ launchId })),
        );
    }
}

module.exports = LaunchAPI;

```


#### 5.2 连接数据库

1. 官方示例中，```start/server/src/datasources/user.js``` 文件，会导出 ```UserAPI``` 模块，该模块初始化时，需要传入 ```store```。

2. ```store``` 是数据库操作相关的API，在后续的章节中，```store``` 是由 ```createStore``` 创建而来。

3. ```createStore``` 是从 ```start/server/src/utils.js``` 中导出的，该模块中定义了数据库的相关操作。



### 6. 给Server增加数据源

修改 ```start/server/src/index.js```文件如下

```javascript

const { ApolloServer } = require('apollo-server');
const typeDefs = require('./schema');
const LaunchAPI = require('./datasources/launch');
const UserAPI = require('./datasources/user');

// 这里定义了sqlite操作的相关API，在后续章节中介绍
const store = createStore();

const server = new ApolloServer({ 
    typeDefs,
    dataSources: () => ({
        launchAPI: new LaunchAPI(),
        userAPI: new UserAPI({ store })
    })
});
server.listen().then(({ url }) => {
    console.log(`sevrer ready at url : ${url}`);
});

```


### 7. 编写 ```query reslovers```

1. 方法签名描述

```javascript

// 暂时无法解释
// parent:
// args: 
// context:
// info 
fieldName: (parent, args, context, info) => data;

```

2. 修改文件：```start/server/src/resolvers.js```

```javascript

module.exports = {
    Query: {
        launches: (_, __, { dataSources }) => dataSources.launchAPI.getAllLaunches(),
        launch: (_, { id }, { dataSources }) => dataSources.launchAPI.getLaunchById({ launchId: id }),
        me: (_, __, { dataSources }) => dataSources.userAPI.findOrCreateUser()
    }
};

```


### 8. 给 ```server``` 添加 ```resolvers```

```javascript

const { ApolloServer } = require('apollo-server');
const typeDefs = require('./schema');
const { createStore } = require('./utils');
const resolvers = require('./resolvers');
const LaunchAPI = require('./datasources/launch');
const UserAPI = require('./datasources/user');

const store = createStore();

const server = new ApolloServer({
  typeDefs,
  resolvers,
  dataSources: () => ({
    launchAPI: new LaunchAPI(),
    userAPI: new UserAPI({ store })
  })
});

server.listen().then(({ url }) => {
  console.log(`🚀 Server ready at ${url}`);
});

```


### 9. 执行 ```npm start```，即可通过浏览器打开 ```http://localhost:4000```来访问 ```GraphQL Playground```

![Mou icon](../Images/28.png)


未完待续...


## 二、API总结

### 2.1 ApolloServer

1. 初始化参数：

```
typeDefs： 定义了scheme，类似于类型和方法声明
resolvers：定义了实现，一般typeDefs定义的方法，需要在resolvers中实现
dataSources：用于初始化数据源，比如调用各个系统的API，操作数据库等等
playground：是否开启graphQL的playground
introspection：是否开启模式自省，如果关闭的话，那么在playground中就无法查看scheme，但是playground仍然可用
context: 当前请求的上下文
mocks：是否开启模拟数据
schema：它将覆盖typeDefs、resolvers
debug：是否开启debug模式
cors：是否支持跨域请求

```

2. 方法

```
listen：开启server，并可以设置监听的端口号
```




