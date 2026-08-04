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

Bước 2. Wallet ký Transaction

Sau khi tạo Transaction, ví sẽ dùng Private Key của người dùng để ký Transaction
```
Transaction

+

Private Key

↓

Digital Signature
```
Sau khi ký, Transaction sẽ có chữ ký số (v, r, s) để chứng minh người gửi là chủ sở hữu của ví

Nếu chữ ký không hợp lệ, blockchain sẽ từ chối Transaction

Bước 3. Broadcast Transaction

Sau khi ký, Wallet gửi Transaction đến một Node thông qua RPC

Ví dụ:
```
MetaMask

↓

RPC Node

↓

Ethereum Network
```
Quá trình này gọi là Broadcast Transaction

Từ thời điểm này, Transaction đã được gửi lên mạng blockchain nhưng chưa được xác nhận

Bước 4. Node kiểm tra Transaction

Node nhận được Transaction sẽ thực hiện một số kiểm tra ban đầu:
```
Chữ ký có hợp lệ không?
Nonce có đúng không?
Người gửi có đủ ETH để trả Gas không
Transaction có đúng định dạng không
```
Nếu hợp lệ, Node sẽ lưu Transaction vào Mempool

Bước 5. Mempool

Khái niệm

Mempool là khu vực lưu trữ tạm thời các Transaction đang chờ được đưa vào Block

Có thể hiểu Mempool giống như:

Phòng chờ của blockchain

Ví dụ:
```
Transaction A

Transaction B

Transaction C

Transaction D
```

Tất cả đều đang chờ Validator lựa chọn

Nếu mạng blockchain đang đông, Transaction có thể phải chờ vài giây hoặc vài phút

Bước 6. Validator chọn Transaction

Validator sẽ chọn các Transaction trong Mempool để đưa vào Block

Thông thường, các Transaction trả Gas cao hơn sẽ được ưu tiên xử lý trước

Ví dụ:
```
Tx A
Gas = 100

Tx B
Gas = 30

Tx C
Gas = 50
```
Validator có thể chọn:
```
Tx A

↓

Tx C

↓

Tx B
```

Bước 7. Đưa Transaction vào Block

Sau khi được chọn, Transaction sẽ được ghi vào một Block mới.

Ví dụ:
```
Block #23567891

├── Tx1
├── Tx2
├── Tx3
└── Tx4
```
Lúc này Transaction đã có:

blockNumber

blockHash

transactionIndex

Bước 8. EVM thực thi Transaction

Khi Block được tạo, EVM sẽ bắt đầu thực thi từng Transaction

Ví dụ:
```
transfer(Bob,100)
```
EVM sẽ:

Đọc dữ liệu trong input
Giải mã để xác định hàm cần gọi
Kiểm tra điều kiện
Cập nhật Storage
Phát sinh Event (nếu có)

Nếu có lỗi (ví dụ require(false)), Transaction sẽ bị Revert

Bước 9. Sinh Transaction Receipt

Sau khi EVM thực thi xong, blockchain tạo ra một Transaction Receipt

Receipt chứa các thông tin như:

Transaction thành công hay thất bại (status)
Đã tiêu tốn bao nhiêu Gas (gasUsed)
Danh sách Event (logs)
Block chứa Transaction

Receipt chính là "biên lai" của Transaction

Bước 10. Event Logs

Nếu Smart Contract sử dụng emit, các Event sẽ được lưu trong Receipt dưới dạng Logs.

Ví dụ:

emit Transfer(from, to, amount);

Receipt sẽ có:

logs

↓

Transfer Event

Backend hoặc ứng dụng có thể lắng nghe các Event này để cập nhật dữ liệu theo thời gian thực

## 5. Transaction Receipt

Khái niệm

Transaction Receipt là kết quả mà blockchain trả về sau khi một Transaction được thực thi

Nếu:

Transaction là yêu cầu gửi lên blockchain
Receipt là kết quả sau khi blockchain xử lý yêu cầu đó

Ví dụ:
```
Transaction

↓

Blockchain Execute

↓

Receipt
```
Receipt Object

Receipt cũng được biểu diễn dưới dạng một object.

Ví dụ:
```
{
    "transactionHash": "0xabc...",
    "status": 1,
    "gasUsed": 52341,
    "blockNumber": 23567891,
    "logs": [...]
}
```
Receipt giúp chúng ta biết:


Transaction có thành công hay không

Đã tiêu tốn bao nhiêu Gas

Transaction nằm trong Block nà
o
Có những Event (Logs) nào được tạo ra

5.3 Các field quan trọng

Field	Ý nghĩa

transactionHash	Hash của Transaction

status	Trạng thái thực thi (1: thành công, 0: thất bại)

gasUsed	Lượng Gas đã tiêu thụ

blockNumber	Block chứa Transaction

logs	Danh sách Event được Smart Contract phát ra

Quan hệ giữa Transaction và Receipt

Transaction
│
├── from
├── to
├── input
└── value

        │
        ▼

Blockchain Execute

        │
        ▼

Receipt
│
├── status
├── gasUsed
└── logs

Receipt được tạo sau khi Transaction được thực thi, và là nơi chứa Logs

5.5 Chuyển sang EVM Logs

Trong Receipt, field quan trọng nhất là:
```
logs
```
Đây là nơi lưu các Event mà Smart Contract phát ra trong quá trình thực thi

## 6. EVM Logs (Log Object)

Khái niệm

EVM Log là dữ liệu mà Smart Contract tạo ra trong quá trình thực thi Transaction để ghi lại những event đã xảy ra

Ví dụ:
```
Alice chuyển 100 USDT cho Bob.

Alice

↓

Transaction

↓

USDT Smart Contract

↓

Transfer thành công

↓

Tạo Log
```
Log được tạo trong lúc Smart Contract thực thi

Ví dụ:
```
function transfer(...) {

    ...

    emit Transfer(from,to,amount);

}
```
Quá trình sẽ là:
```
Transaction

↓

EVM Execute

↓

emit Transfer()

↓

Tạo Log

↓

Lưu vào Receipt
```
Nếu Smart Contract không dùng emit thì sẽ không có Log nào được tạo

6.4 Quan hệ giữa Event và Log


Event là thứ lập trình viên khai báo trong Smart Contract.

Ví dụ:
```
event Transfer(
    address from,
    address to,
    uint256 amount
);
```
Khi chạy:

emit Transfer(from,to,amount);

Blockchain sẽ tạo ra:
```
Log

Có thể hiểu:

Event

↓

emit

↓

EVM

↓

Log
```
Hay nói cách khác:

Event là định nghĩa trong code

Log là dữ liệu thực tế được ghi lên blockchain sau khi emit

Log không nằm trong Transaction

Log cũng không nằm trong Block
```
Log được lưu bên trong Transaction Receipt
```
```
Transaction

↓

Blockchain Execute

↓

Receipt

↓

logs
```
Ví dụ:
```
{
    "status": 1,
    "gasUsed": 51234,
    "logs": [
        ...
    ]
}
```
 Một Transaction có bao nhiêu Log?

Có thể:

Không có Log
Transaction

↓

Không emit

↓

0 Log
Một Log
emit Transfer(...);

↓

1 Log
Nhiều Log
emit Transfer(...);

emit Approval(...);

emit Deposit(...);

↓

3 Logs

Một Transaction có thể tạo ra rất nhiều Log


6.7 Ví dụ thực tế

Smart Contract:
```
event Transfer(
    address from,
    address to,
    uint amount
);

function transfer(...) {

    ...

    emit Transfer(msg.sender,to,amount);

}
```
Người dùng gọi:

transfer(Bob,100)

Sau khi Transaction hoàn thành.

Receipt sẽ có:
```
Receipt

↓

logs

↓

Transfer Log
```
Frontend hoặc Backend chỉ cần đọc Log là biết:

Ai gửi
Ai nhận
Bao nhiêu token

## 7. Log Object

Khái niệm

Mỗi khi smart contract thực hiện câu lệnh ```emit```, EVM sẽ tạo ra Log object

Log Object là dữ liệu lưu thông tin của một Event và được lưu trong trường logs của Transaction Receipt.

Ví dụ:
```
event Transfer(
    address indexed from,
    address indexed to,
    uint256 amount
);

emit Transfer(msg.sender, to, amount);
```
Sau khi Transaction hoàn thành:
```
Transaction

↓

Receipt

↓

logs

↓

Log Object
```
 Ví dụ Log Object
 ```
{
    "address": "0xA0b86991...",
    "topics": [
        "0xddf252ad...",
        "0x000000000000000000000000Alice",
        "0x000000000000000000000000Bob"
    ],
    "data": "0x00000000000000000000000000000064",
    "logIndex": 0,
    "transactionHash": "0xabc...",
    "blockNumber": 23567891
}
```
Các field quan trọng 

<img width="591" height="270" alt="image" src="https://github.com/user-attachments/assets/805bb3e0-e43d-4c94-859a-01bbb15f6f69" />

Quan hệ giữa Event và Log
```
event
    │
    ▼
emit
    │
    ▼
EVM
    │
    ▼
Log Object
    │
    ▼
Receipt.logs
```
Event là định nghĩa
emit là phát Event
Log Object là dữ liệu thực tế được blockchain tạo ra

---
## Tìm hiểu Rule Engine / Heuristic detection (ngưỡng giao dịch, tần suất, địa chỉ lạ).

## 1.Rule Engine 

Rule Engine là 1 hệ thống kiểm tra dữ liệu theo 1 quy định được cài đặc trước

Ví dụ
```
IF (Điều kiện đúng)

THEN (Thực hiện hành động)
```
```
IF

Điểm >= 5

THEN

Đậu
```
```
IF

Tuổi <18

THEN

Rớt
```
Rule Engine chỉ việc kiểm tra điều kiện

Đúng thì thực hiện

Sai thì bỏ qua


