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

Một transaction objet thường gồm
- Hash: Là mã định danh duy nhất của transaction
- From: Địa chỉ người gửi 
- To: Địa chỉ người nhận
- Value: số lượng giá trị gửi 
- Nonce: Vị trí của transaction
- Gas: Giới hạn lượng gas transaction được phép tiêu thụ
- GasPrice: Giá bạn sẵn sàng trả cho mỗi đơn vị gas
- Input: Yêu cầu cho smart contract, Gọi hàm nào, ruyền tham số gì
- Blocknumber: số thứ tự của block chứa transaction đó
- Signature: chữ ký số của người gửi transaction

## 3. Các loại Transaction
ransaction được chia thành nhiều loại khác nhau:

- ETH Transfer
- Contract call
- Contract Deployment

ETH Transfer

Dùng để chuyển ETH từ người này sang người khác, đơn giản nhất trong 3 loại

Không có smart contract nào được thực thi

Contract call

Đây là transaction gọi 1 hàm trong smart contract 

Có smart contract được thực thi và input chứa dữ liệu đã được ABI endcode 
```
{
    "from": "0xAlice",
    "to": "0xUSDT",
    "value": "0",
    "input": "0xa9059cbb..."
}
```

Contract Deployment

Đây là ransaction dùng để triển khai (Deploy) một Smart Contract mới lên blockchain
Đặc điểm:
Không gửi đến Contract đã tồn tại

```to``` = ```null```

input chứa Bytecode của Smart Contract

Sau khi thực thi sẽ sinh ra một địa chỉ Contract mới (contractAddress trong Receipt)

## 4. Transaction Lifecycle

Transaction Lifecycle mô tả quá trình một Transaction được tạo, truyền đi, xác thực và thực thi trên blockchain

Khi người dùng nhấn nút Send, giao dịch không được thực hiện ngay lập tức.

Thay vào đó, nó phải trải qua nhiều bước như:

Tạo Transaction

Ký bằng Private Key

Gửi lên mạng blockchain

Chờ Validator xử lý

Được ghi vào Block

Smart Contract thực thi

Blockchain trả về Receipt

Quy trình: 
```
Người dùng

↓

Wallet tạo Transaction

↓

Ký Transaction bằng Private Key

↓

Broadcast lên mạng Blockchain

↓

Node nhận Transaction

↓

Đưa vào Mempool

↓

Validator chọn Transaction

↓

Đưa vào Block

↓

EVM thực thi

↓

Sinh Transaction Receipt

↓

Receipt chứa Event Logs
```

Các bước trong Transaction Lifecycle

Bước 1. Người dùng tạo Transaction

Người dùng thực hiện một hành động thông qua ví (MetaMask, Rabby, Coinbase Wallet,...)

Ví dụ:

Chuyển ETH

Chuyển USDT

Swap Token

Mint NFT

Ví sẽ thu thập các thông tin cần thiết để tạo Transaction


