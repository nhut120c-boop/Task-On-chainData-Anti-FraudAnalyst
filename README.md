## Tìm hiểu cấu trúc transaction/object trên EVM logs.

---

## 1.Transaction trên EVM:

Transaction là 1 yêu cầu gửi lên blocktrain để yêu cầu blockchain thực hiện 1 hành động nào đó 

Khi nào cần transaction:

Khi chuyển ETH

Khi chuyển USDT

Khi Mint NFT

Gọi Smart contract

## 2.Transaction objet

Transaction thường biểu diễn thành 1 object 
ví dụ
```
{
    "hash":"0x12...",
    "from":"0xAlice",
    "to":"0xBob",
    "value":"1000000000000000000",
    "gas":"21000",
    "nonce":15
}
```
Nó mô tả thông tin giao dịch
