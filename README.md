**Install**
```shell
npm i mecore
```

**Overview**

mecore là một cấu trúc folder cho các dự án api của Mecorp, mecore bao gồm các package thiết yếu cho các dự án của payme từ kết nối cơ sở dữ liệu, bảo mật, etc...

**Hướng dẫn sử dụng**

Sau khi đã install mecore trên commandline, vào nodemodule kéo example/demo.comra ngoài folder dự án chính, trong thư mục example/demo.com là một ví dụ về cách tổ chức file của một dự án thông thường

![Mecore Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/MecoreFolder.PNG)

**AutoLoad**

Những file AutoLoad sẽ khởi chạy khi start/restart dự án

```javascript
module.exports = {
  isActive: false, //Tắt/mở function bằng cách thay đổi isActive 
  onLoad: () => {
    //Viết Code ở đây
    const app = require('..').getInstance();
    const logger = app.log4js.getLogger('default');
    logger.info('Test autoload');
    const __ = app.i18n.__;
    console.log(__('Test'));
  }
}; 
```
------
*Rule <br/>
Mỗi file khi tạo ở thư mục này cần có đuôi Autoload <br/>
Ví dụ: **TestAutoLoad.js**

**Config**

Bao gồm những file cấu hình của dự án. <br/>

![Config Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/ConfigFolder.PNG)

**Constants**

Bao gồm những file Constants 

**Graphql**

Bao gồm những những api hoạt động theo cấu trúc graphql <br/>

![Graphql Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/GraphqlFolder.PNG)

*Rule <br/>
Trong folder **graphql/v1/** phải bao gồm 2 folder typeDefs, resolvers

Thư mục | Quy tắc đặt tên
------------ | -------------
typeDefs | Các file định nghĩa type trong graphql phải là kiểu **.gql**
resolvers | Các phải trong resolver phải là kiểu **.js**

Tài liệu tham khảo về Grapql [link to Graphql!](https://graphql.org/learn/)

**Hapi**

Bao gồm những api làm theo cấu trúc Restful

![Hapi Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/HapiFolder.PNG)

Cấu trúc của hapi là chia nhỏ từng chức năng

*Chức năng
Khi định nghĩa một chức năng bất kì, folder sẽ bao gồm 2 file Route/Module <br/>

![Function Folder](https://github.com/DinhMinhQuang/demo.com/blob/master/images/FunctionFolder.PNG)

Ví dụ: <br/>
Định nghĩa một file Route

```javascript
const Joi = require('mecore').Joi;

const ResponseCode = require('../../../../../config/ResponseCode');

module.exports = [
  {
    method: 'POST', // định nghĩa phương thức
    path: '/v1/testSecurity', // định nghĩa endpoint
    handler: require('./Module'), //gọi module xử lý dữ liệu gửi lên api
    options: {
      // auth: {  
      //   strategy: 'DefaultWithPayload', // có thể thay đổi strategy để auth
      //   payload: true
      // },
      auth: false, //Có thể bật/tắt auth bằng cách thay đổi giá trị
      validate: {
        payload: Joi.object({
          username: Joi.string().min(1).max(20).example('11').description('test')
        }).label('PAYLOAD_TEST') // sử dụng joi để validate các dữ liệu gửi lên api có hợp lệ
      },
      tags: ['api', 'internal', 'v1'], //định nghĩa các name tag trong swagger
      response: { 
        status: {
          [ResponseCode.REQUEST_SUCCESS]: Joi.object({
            test: Joi.string()
          }).label('TEST_SUCCESS') // định nghĩa các message trả về người dùng
        }
      }
    }
  }
];
```

Định nghĩa một file Module

``` javascript
module.exports = (request, reply) => {
    //Viết code ở đây
  const payload = request.payload; //định nghĩa payload
  const version = request.pre.apiVersion; 
  console.log(version);
  return reply.api({
    test: request.i18n.__('Client IP {{clientIp}}', { 
      clientIp: request.clientIp
    })
  }).code(1000); //định nghĩa message trả về theo đúng định dạng i18n
}
;
```

**hapi/auth**

Mỗi khi có jwt gửi từ header flow sẽ chạy qua tùy file theo config trong route, và sẽ kiểm tra tính hợp lệ của những token đó

**Locals**

(insert pic locales folder)

**Models**

(insert pic models folder)
Đây là nới define các model của dự án, quy tắc đặt tên là phải có đuôi Model cuối mỗi file,

**Task**

Bao gồm những tác vụ chạy định kì theo 1 khoảng thời gian nhất định
(insert pic Task folder)

Cấu trúc 1 file task như sau
(insert pic TestTask.js)
Quy tắc đặc tên phải bao gồm Task ở cuối mỗi file
Có thể tắt mở chắc năng bằng isActive
expresstion sẽ là thời gian tùy theo mình định nghĩa (cách định nghĩa có thể xem ở đây)
options {
timeZone: thời gian của vùng muốn hiện thị
runOnInit: false //true Tùy chọn tắt/mở với tác vụ muốn chạy ngay khi start dự án
waitingFinish: false//true Tùy chọn đợi tác vụ chạy xong, hay chạy song song
onTick: () => {
đây là nơi định nghĩa các tác vụ
}

index.js là file để start thử một dự án
