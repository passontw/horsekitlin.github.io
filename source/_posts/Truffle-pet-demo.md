---
title: truffle pet demo
tags:
  - Truffle
  - Sodility
date: 2018-05-05 13:54:23
categories: Block Chain
---

# 設定步驟

1. 設定開發者相關環境變數
2. 建立一個 Truffle 專案
3. 寫一個基本的合約
4. 編譯與部屬你的合約
5. 測試你的合約
6. 建立一個介面來跟你的合約溝通
7. 放到瀏覽器開工囉

## 後端

### 1. 設定開發者相關環境變數

#### 安裝基本環境

[Nodejs](https://nodejs.org/en/)

[Git](https://git-scm.com/)

[Ganache](http://truffleframework.com/ganache/)

個人的以太區塊鏈的測試環境

```
  $ npm install -g truffle
  $ truffle --version // 檢查是否安裝成功以及版本
  $ mkdir pet-shop-tutorial && cd pet-shop-tutorial
```
### 2. 建立一個 Truffle 專案

Truffle 有提供一些套件協助建立 `DAPP`

[Truffle Boxes](http://truffleframework.com/boxes/)

建立一個 `pet-shop` 的 basic project

```
  $ truffle unbox pet-shop
```

```
  Truffle initial 有很多方式， `truffle init` 會幫你建立一個空的 Truffle 專案
```

[Truffle init](http://truffleframework.com/docs/getting_started/project)

#### 檔案結構

```
  .
  ├── contracts
  ├── migrations
  ├── src
  │   ├── css
  │   ├── fonts
  │   ├── images
  │   └── js
  └── test
```
* contracts : `Solidity` 的檔案，在這個範例中有一個 `Migrations.sol` 的範例檔案

* migrations : Truffle 利用 `migrations system` 來處理開發環境，A migration is an additional special smart contract that keeps track of changes.

* test : 測試檔案

* truffle.js : Truffle 的設定檔案

### 3. 寫一個基本的合約

在 `constracts` 中建立一個檔案 `Adoption.sol`

Adoption.sol

```solidity
pragma solidity ^0.4.17;

contract Adoption {

}
```

* pragma 定義使用 `solidity` 編譯器的版本

#### 變數

`Solidity` 有一種特別的變數 `adress`，這是代表  `Ethereum` 的位址

儲存了 20 個 byte 得值， 每一個帳號和合約在 `Ethereum block chain` 都擁有一個 `adress`

在 `Adoption.sol` 中宣告一個 `adress`

```solidity
pragma solidity ^0.4.17;

contract Adoption {
  address[16] public adopters;
}
```

* 宣告一個變數 `adopters` 這是一個陣列儲存 `Ethereum` 的 address
Array 內有一種類型，在這裡的長度設定為 `16`

* `adopters` 設定是 public， public 有 `getter method` 回傳一個值
但是因為在這個範例 `adopters` 是一個陣列，所以等等會寫一個 function 回傳整個陣列

### First Function

* In Solidity the types of both the function parameters and output must be specified. In this case we'll be taking in a petId (integer) and returning an integer.

* We are checking to make sure petId is in range of our adopters array. Arrays in Solidity are indexed from 0, so the ID value will need to be between 0 and 15. We use the require() statement to ensure the ID is within range.

* If the ID is in range, we then add the address that made the call to our adopters array. The address of the person or smart contract who called this function is denoted by msg.sender.

* Finally, we return the petId provided as a confirmation.

### Second Function

array getter 只能回傳一個值，但是但是 16 個 API 不是很實際，所以我們需要一個 API 來回傳所有的寵物列表

```
pragma solidity ^0.4.17;

contract Adoption {
  address[16] public adopters;
  function adopt(uint petId) public returns (uint) {
    require(petId >= 0 && petId <= 15);

    adopters[petId] = msg.sender;
    return petId;
  }

  function getAdopters () public view returns (address[16]) {
    return adopters;
  }
}
```

在這邊我們將回傳之前宣告的 `adopters` 定義類型為 address[16]

### 編譯並部署智能合約

Truffle 有一個開發者控制台， 他會生成一個開發區塊鏈，可以利用他測試部署合同

他可以直接在控制台運行，本範例大部分將會使用它完成

#### 編譯

`Solidity` 是一個編譯語言，所以需要一個編譯器先編譯之後才能執行，

```
  $ truffle compile
```

#### 部署

A migration is a deployment script meant to alter the state of your application's contracts, moving it from one state to the next. For the first migration, you might just be deploying new code, but over time, other migrations might move data around or replace a contract with a new one.

你會在 `migrations/` 裡面有一個 `1_initial_migration.js` 這是負責部署 `Migrations.sol` 這份合約的範例

This handles deploying the Migrations.sol contract to observe subsequent smart contract migrations, and ensures we don't double-migrate unchanged contracts in the future

現在我們需要新增一個屬於我們自己的部署 script

```js
const Adoption = artifacts.require("Adoption");

module.exports = function (deployer){
  deployer.deploy(Adoption);
}
```

在部署之前要先執行 `Ganache` 他會有一個本地的區塊鏈在 `port 7575`

若您尚未下載 [download Ganache](http://truffleframework.com/ganache)

![image](http://truffleframework.com/tutorials/images/pet-shop/ganache-initial.png)

然後回到終端機

```
  $ truffle migrate
  Using network 'development'.

Running migration: 2_deploy_contracts.js
  Deploying Adoption...
  ... 0x05ea7e5292d02a2ffb78a0a6e905a7a2a083d3b3bca6eef300ce9a0e8c0e714a
  Adoption: 0xac30aad46a83f8e8de3f452b0d4c175a1173a54b
Saving successful migration to network...
  ... 0x176cef7ecc425a8952f86020e76701c934a2a7ae06a0ac26a57867450a42b6bd
Saving artifacts...
```

在 `Ganache` 中 blockchain 的狀態改變了從原本的 0 改變為 4 ，之後會再討論到交易成本

寫好第一個合約並且部署上了區塊鏈，接下來要開始測試一下你的合約

#### 測試你的合約

在 `test` 資料夾裡建立一個 `TestAdoption.sol` 的檔案

TestAdoption.sol

```
pragma solidity ^0.4.17;
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  Adoption adoption = Adption(DeployedAddresses.Adoption());
}
```

這裡有 `import` 三個檔案

* `Assert.sol`: 可以在測試中檢察值，然後送出相對的 assert 方便檢查哪一部分有錯誤[Here's a full list of the assertions included with Truffle.](https://github.com/trufflesuite/truffle-core/blob/master/lib/testing/Assert.sol)
* `DeployedAddresses.sol`: 測試的時候會部署一個測試的合約，測試的時候會部署一個測試的 `contract`，然後透過這個 可以取得一個位址
* 要測試的 `contract`

`Assert.sol` 和 `DeployedAddresses.sol` 是在 truffle 套件中，而不是在資料夾內

#### 測試 adopt() function

To test the adopt() function, recall that upon success it returns the given petId. We can ensure an ID was returned and that it's correct by comparing the return value of adopt() to the ID we passed in

TestAdoption.sol

```
pragma solidity ^0.4.17;
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  Adoption adoption = Adption(DeployedAddresses.Adoption());

  function testUserCanAdoptPet() public {
    uint returnedId = adoption.adopt(8);
    uint expected = 8;

    Assert.equal(returnedId, expected, "Adoption of pet ID 8 should be recorded.");
  }
}
```

* 先宣告一個領養八號寵物的合約
* 宣告一個預想中領養八號寵物的結果
* Assert.equal() 做兩個的比對

#### 測試恢復一個領養的合約

TestAdoption.sol

```
pragma solidity ^0.4.17;
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  Adoption adoption = Adption(DeployedAddresses.Adoption());

  function testUserCanAdoptPet() public {
    uint returnedId = adoption.adopt(8);
    uint expected = 8;

    Assert.equal(returnedId, expected, "Adoption of pet ID 8 should be recorded.");
  }

  function testGetAdopterAddressByPetId() public {
    address expected = this;
    address adopter = adoption.adopters(8);
    Assert.equal(adopter, expected, "Owner of pet ID 8 should be recorded.");
  }
}
```

`TestAdoption` 會發送一個真實的交易，我們會設定預期的結果為 `this` 會比對兩個的位址是否相同

#### 檢查所有寵物

TestAdoption.sol

```
pragma solidity ^0.4.17;
import "truffle/Assert.sol";
import "truffle/DeployedAddresses.sol";
import "../contracts/Adoption.sol";

contract TestAdoption {
  Adoption adoption = Adoption(DeployedAddresses.Adoption());

  function testUserCanAdoptPet() public {
    uint returnedId = adoption.adopt(8);
    uint expected = 8;

    Assert.equal(returnedId, expected, "Adoption of pet ID 8 should be recorded.");
  }

  function testGetAdopterAddressByPetId() public {
    address expected = this;
    address adopter = adoption.adopters(8);
    Assert.equal(adopter, expected, "Owner of pet ID 8 should be recorded.");
  }

  function testGetAdopterAdderssByPetIdInArray() public {
    address expected = this;

    address[16] memory adopters = adoption.getAdopters();

    Assert.equal(adopters[8], expected, "Owner of pet ID 8 should be recorded.");
  }
}
```

`memory` 表示 adopters 的值存在記憶體裡，確定 `8` 是否在陣列之中

#### Running test

```
  $ truffle test
  Using network 'development'.

  Compiling ./contracts/Adoption.sol...
  Compiling ./test/TestAdoption.sol...
  Compiling truffle/Assert.sol...
  Compiling truffle/DeployedAddresses.sol...

  TestAdoption
    ✓ testUserCanAdoptPet (75ms)
    ✓ testGetAdopterAddressByPetId (64ms)
    ✓ testGetAdopterAdderssByPetIdInArray (138ms)
```

### 建立一個 User Interface

# 參考資料

[Tutorial](http://truffleframework.com/tutorials/pet-shop)