install 
npm i mecore

overview
mecore là một cấu trúc folder cho các dự án api của Mecorp, mecore bao gồm các package thiết yếu cho các dự án của payme từ kết nối cơ sở dữ liệu, bảo mật, etc... 

hướng dẫn sử dụng 
Sau khi đã install mecore trên commandline, vào nodemodule kéo example/demo.comra ngoài folder dự án chính, trong thư mục example/demo.com là một ví dụ về cách tổ chức file của một dự án thông thường

![Mecore Example](/images/MecoreFolder.png)

AutoLoad: 
Bao gồm các file khởi chạy ngay khi vừa start/restart dự án 
(insert pic autoload folder) 
Mỗi file khi tạo ở thư mục này cần có đuôi Autoload, 
isActive được set default false khi cần tắt chức năng của file này. 

config 
Bao gồm những file cấu hình của dự án từ cơ sở dữ liệu cho đến các công nghệ đi kèm. 
(insert pic config folder)
config/env sẽ bao gồm các môi trường trong dự án thực tế như Dev, Sandbox, ...etc

constants 
(insert pic constants folder) 
Bao gồm những biến không đổi được dùng lại nhiều lần 

graphql
(insert pic graphql folder) 
Bao gồm những nhưng api làm theo cấu trúc graphql 
Để biết thêm thông tin chi tiết bấm vào đây (redirect to Grapql docs)

hapi 
(insert pic hapi folder 
Bao gồm những api làm theo cấu trúc Restful 
api bao gồm những api chia theo từng chức năng nhỏ của một dự án 
Mỗi chức năng bao gồm Module/Route, nếu tạo thiếu 1 folder, filehound trong mecore sẽ detect về lỗi thiếu file 
 - Cấu trúc của module phải đi theo ví dụ sau đây (insert pic module) 
 - Cấu trúc của route phải đi theo ví dụ sau đây (insert pic route) 
Nếu cấu trúc file không đúng filehound trong mecore cũng sẽ detect về lỗi

hapi/auth 
Mỗi khi có jwt gửi từ header flow sẽ chạy qua tùy file theo config trong route, và sẽ kiểm tra tính hợp lệ của những token đó 

hooks
plugins
public 
views 

Locals
(insert pic locales folder) 

Models
(insert pic models folder) 
Đây là nới define các model của dự án, quy tắc đặt tên là phải có đuôi Model cuối mỗi file, 

Task 
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





 




