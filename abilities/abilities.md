# Abilities trong Sui: Rules để sử dụng các Data Types

## Key Abilities

**Key abilities** được sử dụng để tạo ra các object duy nhất và có thể tìm kiếm object đó trong **global storage**.  
Objects với ability `key` có thể được sở hữu, chuyển giao và lưu trữ trên blockchain của Sui.

### Example: `sui_learn::key_only`

```move
module sui_learn::key_only {
    use sui::tx_context::{Self, TxContext, sender};
    use sui::object;

    // Định nghĩa một struct có ability 'key'
    struct KeyObject has key {
        id: UID,
    }

    // Hàm tạo một KeyObject và chuyển giao quyền sở hữu
    public fun create_key_only(ctx: &mut TxContext) {
        let obj = KeyObject {
            id: object::new(ctx),
        };

        // Chuyển giao object tới địa chỉ của người gửi
        transfer::transfer(obj, sender(ctx));
    }
}
```

## Store Abilities

**Store abilities** giải quyết về giới hạn transfer. Store cũng cho phép object chứa object.

### Example:
```
struct NestedStruct has store {
    value: u64,
}

struct Container has key {
    id: UID,
    nested: NestedStruct,
}
```

#### Notice: các objects chỉ có duy nhất key hoặc bao gôm cả key và store thì sẽ khác nhau như thế nào?
Đó là nếu chỉ có key ability thì bạn chỉ có thể store trên global storage nhưng lại không thể transferred. Thứ hai là vì dùng key ability chỉ để xác định duy nhất các đối tượng, chẳng hạn như các tài khoản (account) hoặc các resources khác.

Còn một object bao gồm cả key và store sẽ vừa có thể lưu trữ trên global storage. Có thể được chuyển và quản lý một cách tự do bởi chủ sở hữu, cho phép di chuyển giữa các tài khoản hoặc smart contracts khác nhau.

## Copy Abilities

**Copy ability** cho phép một struct được "sao chép", nghĩa là tạo ra một instance mới của struct với các giá trị trường hoàn toàn giống nhau.

***Lưu ý:*** Các object structs (những struct có key ability) không thể có copy ability vì struct UID không có khả năng copy.

### Example
```
public struct CopyableStruct has copy {
    value: u64,
}

fun copy_example(original: &mut CopyableStruct) {
    let copy_test = original;

    copy_test.value = 1;
    let copy_copy_test = copy_test;
    copy_copy_test.value = 2;

    let c = *copy_copy_test.value;        
}
```
## Drop Abilities

Các ngôn ngữ lập trình khác, máy tính sẽ tự động dọn dẹp dữ liệu không sử dụng. Nhưng Move yêu cầu dev phải tự xử lý việc dọn dẹp. Nó giúp phát hiện sớm các vấn đề ngay trong quá trình viết code, thay vì đợi đến lúc chương trình đang chạy.

Nếu không dùng ***drop*** thì phải tự ***unpack*** 1 object ra.

### Example
```
module sui_learn::drop_ability_example {
    use std::string::{ utf8, String };

    public struct DropMe has drop {
        id: u8,
        name: String,
    }
    public struct NoDrop {
        name: String,
    }

    #[test]
    fun test_ignore() {
        let droppable = DropMe { id: 1, name: utf8(b"sui_learn") }; // valid

        let no_drop = NoDrop { name: utf8(b"quangnguyen") };                
        // Nếu không có drop thì bạn cần phải unpack
        let NoDrop { name: _ } = no_drop;  // also valid
    }
}
```
## Hot Potato
**Hot potato** là kiểu struct không có abilitie. Toàn bộ logic sẽ được thực hiện trong phạm vi một transaction.
