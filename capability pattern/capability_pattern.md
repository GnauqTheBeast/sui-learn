# Capability Pattern

**Capability** là một kiểu pattern trong Sui Move. Nó là cách dễ dàng để phân quyền (authorize) cho một function nào đó được thực hiện.

***Tư tưởng***: cần 1 object (tạm gọi là: object key), mỗi hàm được gọi sẽ phải truyền parameter là object key (address khác không sở hữu object key này sẽ không thể call function)

***Yêu cầu***: tạo struct có key abilities => để xác định nó là duy nhất và nó không thể transfer cho bất kì address khác 

```
public struct AdminCap has key {     // key abilities cho phép object được lưu trữ trên global storage SUI
    id: UID,
}

fun init(ctx: &mut TxContext) {      // init sẽ được tự động call sau khi deploy contract
    transfer::transfer(AdminCap {    // cho address deploy object AdminCap khi init
        id: object::new(ctx),
    }, ctx.sender());   
}

// reference của object AdminCap đó và dùng underscore(_) để ký hiệu là tham số này không được sử dụng
public entry fun withdraw(wallet: &mut Wallet, _: &AdminCap, amount: u64, ctx: &mut TxContext) {   
    let coin = coin::take(&mut wallet.balance, amount, ctx);
    transfer::public_transfer(coin, ctx.sender());
}
```
### My opinion

Ta có thể tuỳ biến cách sử dụng tuỳ vào mục đích/ yêu cầu. 

Nếu muốn quyền AdminCap có thể trao cho người khác, và mình mất quyền đó đi (giống với việc thừa kế ngoài đời thật) thì thêm store abilities vào struct.

```
public struct AdminCap has key, store {  // store cho phép object chứa object, quan trọng là có thể transfer cho address khác
    id: UID,
}
```

Nếu muốn quyền AdminCap không bị mất đi và người khác cũng có

```
public struct AdminCap has key, store { 
    id: UID,
}

public fun add_additional_teacher(_: &AdminCap, recieve_address: address, ctx: &mut TxContext) { 
    transfer::transfer(                   
        AdminCap {
            id: object::new(ctx)
        },
    recieve_address
    )
}
```



