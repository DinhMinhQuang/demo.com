**Install**

```shell
npm i mecore
```

**Overview**

Mecore là 1 package sử dụng Hapi là core chính để phát triển, Mecore giúp cho việc tổ chức code, quản lý dự án, thư mục, plugins một cách dễ dàng hơn 

**Hướng dẫn sử dụng**

Sau khi đã install mecore, vào node_module kéo example/demo.com trong mecore ra ngoài folder dự án chính, trong thư mục example/demo.com là một ví dụ về cách tổ chức file của một dự án thông thường.

![Mecore Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/MecoreFolder.PNG)

**AutoLoad**

Các file được định nghĩa trong AutoLoad sẽ khởi chạy khi start/restart dự án <br/>

Ví dụ:

```javascript
module.exports = {
  isActive: false, //Tắt/mở function bằng cách thay đổi giá trị isActive
  onLoad: () => {
    //Viết Code ở đây
    const app = require("..").getInstance();
    const logger = app.log4js.getLogger("default");
    logger.info("Test autoload");
    const __ = app.i18n.__;
    console.log(__("Test"));
  },
};
```

*Rule <br/>
Mỗi file khi được định nghĩa cần có đuôi Autoload <br/>
Ví dụ: **TestAutoload.js**

**Config**

Bao gồm những file cấu hình của dự án. <br/>

![Config Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/ConfigFolder.PNG)

#**Mongoose.js**
```javascript 
module.exports = {
  default: {
    uri: 'mongodb://localhost:27017/test',
    options: {
      useNewUrlParser: true,
      useUnifiedTopology: true,
      useFindAndModify: false,
      useCreateIndex: true
    }
  },
  common: {
    uri: 'mongodb://localhost:27017/test1',
    options: {
      useNewUrlParser: true,
      useUnifiedTopology: true,
      useFindAndModify: false,
      useCreateIndex: true
    }
  }
};

```

#**Hapi.js**
```javascript
const ApolloValidation = require('@profusion/apollo-validation-directives');

module.exports = {
  isActive: true, 
  server: {
    port: 3000, //Config port cho server
    host: '0.0.0.0', //host
    routes: {
      validate: {
        failAction: (request, h, err) => {
          throw err; //trả về lỗi 
        }
      }
    }
  },
  plugins: { //Các plugin của hapi
    cors: {
      isActive: true,
      options: {
        headers: []
      }
    },
    jwt: {
      isActive: true,
      options: {
        inject: ['Default', 'Local', 'DefaultWithPayload'], //Các lựa chọn phương thức xác thực khác
        default: { 
          strategy: 'Default', //Đặt phương thức xác thực default cho api
          payload: true
        }
      }
    },
    swagger: { //Config swagger
      isActive: true,
      options: {
        info: {
          title: 'Test API Documentation', //Tiêu đề của api
          version: '1.0.1'// phiên bản swagger
        },
        grouping: 'tags',
        tags: [
          // {
          //   name: 'external-v2',
          //   description: 'API nội bộ version 2'
          // }
        ],
        tagsGroupingFilter: (tag) => {
          return true;
        },
        securityDefinitions: { //Định nghĩa các phương thức bảo mật dành cho swagger
          jwt: {
            type: 'apiKey',
            name: 'Authorization',
            in: 'header'
          },
          checksum: {
            type: 'apiKey',
            name: 'Checksum',
            in: 'header'
          }
        },
        security: [
          {
            jwt: [],
            checksum: []
          }
        ]
      }
    },
    apiReply: {
      isActive: true,
      options: {
        handleException: (error, request, reply) => {
          const project = require('../').getInstance();
          const logger = project.log4js.getLogger('system');
          logger.error(error);
          return true;
        },
        message: {
          401: 'Thông tin xác thực không hợp lệ',
          404: 'Api không tồn tại',
          500: 'Dịch vụ đang gặp gián đoạn. Vui lòng quay lại sau'
        }
      }
    },
    apiSecurity: {
      isActive: false,
      options: {
        disableSecurity: { //Tắt phương thức bảo mật 
          allow: true,
          secretKey: 'sandbox'
        },
        excludeVersion: [],
        client: {//Định nghĩa key cho client
          app: {
            publicKey: '-----BEGIN PUBLIC KEY-----\n' +
              'MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAKWcehEELB4GdQ4cTLLQroLqnD3AhdKi\n' +
              'wIhTJpAi1XnbfOSrW/Ebw6h1485GOAvuG/OwB+ScsfPJBoNJeNFU6J0CAwEAAQ==\n' +
              '-----END PUBLIC KEY-----',
            privateKey: '-----BEGIN RSA PRIVATE KEY-----\n' +
              'MIIBPAIBAAJBAKWcehEELB4GdQ4cTLLQroLqnD3AhdKiwIhTJpAi1XnbfOSrW/Eb\n' +
              'w6h1485GOAvuG/OwB+ScsfPJBoNJeNFU6J0CAwEAAQJBAJSfTrSCqAzyAo59Ox+m\n' +
              'Q1ZdsYWBhxc2084DwTHM8QN/TZiyF4fbVYtjvyhG8ydJ37CiG7d9FY1smvNG3iDC\n' +
              'dwECIQDygv2UOuR1ifLTDo4YxOs2cK3+dAUy6s54mSuGwUeo4QIhAK7SiYDyGwGo\n' +
              'CwqjOdgOsQkJTGoUkDs8MST0MtmPAAs9AiEAjLT1/nBhJ9V/X3f9eF+g/bhJK+8T\n' +
              'KSTV4WE1wP0Z3+ECIA9E3DWi77DpWG2JbBfu0I+VfFMXkLFbxH8RxQ8zajGRAiEA\n' +
              '8Ly1xJ7UW3up25h9aa9SILBpGqWtJlNQgfVKBoabzsU=\n' +
              '-----END RSA PRIVATE KEY-----'
          }
        }
      }
    },
    apiVersion: { //Phiên bản của api
      isActive: true,
      options: {
        validVersions: [1, 2, 3],
        defaultVersion: 1,
        vendorName: 'me'
      }
    },
    apiResponseTime: {//Cấu hình thời gian response
      isActive: true,
      options: {}
    },
    clientIp: {
      isActive: true,
      options: {}
    },
    i18n: {//Cấu hình i18n
      isActive: true,
      options: {}
    },
    graphql: { //Cấu hình graphQl
      v1: {
        isActive: true,
        options: {
          applyMiddleware: {
            path: '/graphql', //Đường dấn để mở playground
            route: {
              // auth: {  
              //   strategy: 'Default',
              //   payload: false
              // }, // Cài đặt phương thức xác thực
              auth: false,
              cors: true
            }
          },
          apolloServer: { //Cấu hình apolloServer
            typeDefs: [
              ...ApolloValidation.listLength.getTypeDefs(),
              ...ApolloValidation.stringLength.getTypeDefs()
            ],
            resolvers: [],
            schemaDirectives: {
              listLength: ApolloValidation.listLength,
              stringLength: ApolloValidation.stringLength
            },
            context: (hapi) => {
              return {
                auth: hapi.request.auth
              };
            }
          }
        }
      }
    }
  }
};

```

**Constants**

Bao gồm những file Constants

**Graphql**

Bao gồm những những api hoạt động theo cấu trúc graphql <br/>

![Graphql Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/GraphqlFolder.PNG)

\*Rule <br/>
Trong folder **graphql/v1/** phải bao gồm 2 folder typeDefs, resolvers

| Thư mục   | Quy tắc đặt tên                                                 |
| --------- | --------------------------------------------------------------- |
| typeDefs  | Các file định nghĩa type trong graphql phải là kiểu **.gql**    |
| resolvers | Các file định nghĩa resolver trong graphql phải là kiểu **.js** |

Tài liệu tham khảo về Graphql [link to Graphql!](https://graphql.org/learn/)

**Hapi**

Bao gồm những api làm theo cấu trúc Restful

![Hapi Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/HapiFolder.PNG)

Mỗi folder được định nghĩa trong **hapi/api/v1/external/** là một chức năng

Khi định nghĩa một chức năng bất kì, folder phải bao gồm 2 file Route/Module <br/>

![Function Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/FunctionFolder.PNG)

Ví dụ: <br/>
Định nghĩa một file Route

```javascript
const Joi = require("mecore").Joi;

const ResponseCode = require("../../../../../config/ResponseCode");

module.exports = [
  {
    method: "POST", // Định nghĩa phương thức
    path: "/v1/testSecurity", // Định nghĩa endpoint
    handler: require("./Module"), //Gọi module xử lý dữ liệu gửi lên api
    options: {
      // auth: {
      //   strategy: 'DefaultWithPayload', // Có thể thay đổi strategy để auth
      //   payload: true
      // },
      auth: false, //Bật/Tắt api có cần xác thực hay không
      validate: {
        payload: Joi.object({
          username: Joi.string()
            .min(1)
            .max(20)
            .example("11")
            .description("test"),
        }).label("PAYLOAD_TEST"), // Sử dụng joi để validate các dữ liệu gửi lên api
      },
      tags: ["api", "internal", "v1"], //Định nghĩa các name tag trong swagger
      response: {
        status: {
          [ResponseCode.REQUEST_SUCCESS]: Joi.object({
            test: Joi.string(),
          }).label("TEST_SUCCESS"), // Định nghĩa các message trả về phía client
        },
      },
    },
  },
];
```

Định nghĩa một file Module

```javascript
module.exports = (request, reply) => {
  //Viết code ở đây
  const payload = request.payload;
  const version = request.pre.apiVersion;
  console.log(version);
  return reply
    .api({
      test: request.i18n.__("Client IP {{clientIp}}", {
        clientIp: request.clientIp,
      }),
    })
    .code(1000); //định nghĩa message trả về theo đúng định dạng i18n
};
```

\*Rule <br/>
Trong folder **hapi/api/v1/external/**, mỗi khi định nghĩa một chức năng, trong mỗi folder chỉ bao gồm 2 file Route và Module

Tài liệu tham khảo về Hapi [link to Hapi!](https://hapi.dev/api/)

**hapi/auth**

Khi option của Route được chọn là

```javascript
options: {
      auth: {
        strategy: 'DefaultAuth',
        payload: true
      }
    }
```

Trước khi luồng đi tới Module, sẽ trỏ xuống **strategy** tương ứng trong **hapi/auth**, và kiểm tra tính hợp lệ của JWT

**Locales**

Các message trả về có định dạng

```javascript
return reply
  .api({
    test: request.i18n.__("Client IP {{clientIp}}", {
      clientIp: request.clientIp,
    }),
  })
  .code(1000);
```

Message trả về sẽ lưu vào file trong locales

**Models**

Đây là nơi định nghĩa các model của dự án <br/>

![Model Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/ModelFolder.PNG)

Ví dụ: <br/>
```javascript
const Schema = require('mecore').Mongoose.Schema;

const Model = {
  connection: 'default', //Định nghĩa kết nối
  tableName: 'Test', //Tên bảng
  autoIncrement: {
    id: {
      startAt: 1,
      incrementBy: 1
    }
  },//Định nghĩa id tự động tăng 
  attributes: new Schema({
    description: { //Định nghĩa 1 trường trong bảng
      type: String
    }
  }, {
    timestamps: true
  })
};

module.exports = Model;

```

\*Rule

Các file model phải có đuôi **Model** ở cuối mỗi file

**Task**

Bao gồm những tác vụ chạy đình kì theo một khoảng thời gian do người dùng định nghĩa

Ví dụ: <br/>

```javascript
// # expression
// # ┌────────────── second (optional)
// # │ ┌──────────── minute
// # │ │ ┌────────── hour
// # │ │ │ ┌──────── day of month
// # │ │ │ │ ┌────── month
// # │ │ │ │ │ ┌──── day of week
// # │ │ │ │ │ │
// # │ │ │ │ │ │
// # * * * * * *

module.exports = {
  isActive: false, //Tắt mở module bằng cách thay đổi giá trị isActive
  expression: "* * * * * */5", //Thời gian định kì được định nghĩa
  options: {
    timeZone: "Asia/Ho_Chi_Minh", //Thời gian theo vùng
    runOnInit: false, //Tắt/Mở chức năng chạy khi khởi tạo dự án
    waitingFinish: false, //Tắt/Mở chức năng chờ đợi chức năng
  },
  onTick: () => {
    //Viết code ở đây
    const app = require("..").getInstance();
    const logger = app.log4js.getLogger("default");
    logger.info("Test Task");
  },
};
```

*Rule <br/>
Mỗi file được định nghĩa trong tasks phải bao gồm **Task** ở cuối mỗi tên file

Ví dụ: <br/>
**TestTask.js**

**Khởi chạy dự án**

```shell 
cd examples/demo.com 
node index.js 
```
Đường dẫn của dự án demo <br/>

```url 
domain:port/documentation
```


