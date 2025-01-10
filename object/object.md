# Object trong SUI

## Object
SUI lưu trữ thông tin thông qua object. Object trong SUI có thể là 1 package hoặc object chứa các metadata.


### Transaction
Khác với blockchain khác, thay vì balance được lưu theo từng account dạng key-value và ghi giao dịch với ledger. 
SUI có cơ chế merge hoặc split.

***Example:*** Ở hệ EVM: Alice gửi tiền cho Bob 1 ETH, ledger sẽ là: Alice -1 ETH, Bob +1 ETH.
Với SUI, Alice muốn chuyển cho Bob 1 SUI. Gỉa sử Alice đang có 2 SUI ở trong 1 ojbect X và Bob có 1 SUI ở 1 object Y. 
Alice split 1 SUI từ X để tạo 1 object X' rồi gửi tx cho Bob. Bob có thể merge X' vào Y (để tạo ra Y' 2 SUI) hoặc không.
