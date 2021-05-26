# dapp
dapp的筆記，學習sodolity相關資訊，用此筆記來撰寫
## Function Modifiers
原本的合約為

```
	function setName(string _name) public returns (string) {
	    /* Only owner has the permission to modify its name. */
		if (msg.sender == owner) {
			name = _name;
		} else {
			revert("Permission denied.");
		}
		return name;
	}
  ```
  
 使用Function Modifiers後
 
 ```
 modifier onlyOwner() {
        require(msg.sender == owner, "Permission denied.");
        _;
    }
	function setName(string _name) public onlyOwner returns (string) {
	    /* Only owner has the permission to modify its name. */
		name = _name;
		return name;
	}
  ```
  
 ## 事件 (Events)&Logs
  在合約裡基本上都會聆聽事件，例如gas費地給予等等，每個transcation被送出後，會產生一個Receipt來儲存執行結果
  Receipt包含了：
  * 該Transaction的gas費
  * 該Transaction的Logs
  
### Logs裡面會包含
  * address:由哪個contract address所產生
  * blockHash, blockNumber, transactionHash, transactionIndex
  * LogIndex:第幾個Log
  * data:raw data (32 bytes為單位)
  * topics:識別值
  
### topics相關
  * 是一個特殊的識別欄位，能作為filter(篩選)的搜尋目標
  * 在每個log中只能有四個
  * 第一個欄位固定為log的identifier做keccak-256(第三代安全雜湊演算法SHA-3)後的值

#### multiple topics
  * 每個log可以有四個，但是第一個一定要為identifier，剩下的三個宣告方事是使用indexed modi0fier
  * 若有使用indexed modifier，此時argument就會做為一個topic存在receipt的topics中
  * 若儲存的型態為string或是bytes之類的，就會通過keccak-256儲存，會無法知道原本的樣子

### 宣告 : event name(arguments)
  * name宣告名稱
  * arguments可以有多個
  * ex. event ex(int a, int b)(裡面可以事任何你要的東西，連字串都可以) 
### 呼叫 : emit name(paremeters)
  * 要發送Log時需要使用emit語法
  * parameters為該log所需要的資訊

```
pragma solidity ^0.4.25;

contract Test {
    string information;
    uint balance;
    
    event LogCreate(string information, uint balance);
    event LogCreateIndex(string indexed information, uint indexed balance);
    
    constructor() public {
        information = "default";
        balance = 100;
        emit LogCreate(information, balance);
        emit LogCreateIndex(information, balance);
    }
}
```


