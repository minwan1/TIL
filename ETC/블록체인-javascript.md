## 블록체인

```javascript
class Block{
  constructor(index,timestamp,data,previousHash =''){

    this.index = index;
    this.timestamp = timestamp;
    this.data = data;
    this.previousHash = previousHash;

    this.hash = this.calculateHash();
    this.noce = 0; // 난이도
  }

  // 해시키 생성하여 리턴해줌
  // 이것으로 이 해당 블록을 찾음
  calculateHash(){
    return SHA256(this.index + this.previousHash + this.timestamp + JSON.stringfy(this.data)+this.noce).toString();
  }

  mineBlock(difficulty){
    while(this.hash.substring(0, difficulty) !== Array(difficulty +1).join("0")){
      this.nonce++;
      this.hash = this.calculateHash();

    }
    console.log("Block mined: " + this.hash);
  }

}

class Blockchain{
  constructor(){
    this.chain = [this.createGenesisBlock()];
    this.difficulty = 2;
  }

  createGenesisBlock(){
    return new Block(0, "01/01/2017", "Genesis block","0");
  }

  getLatestBlock(){
    return this.chain[this.chain.length -1];
  }

  isChainValid(){
    for(let i = 1; i < this.chain.length; i++){
      const currentBlock = this.chain[i];
      const previousBlock = this.chain[i - 1];

      if(currentBlock !== currentBlock.calculateHash()){
        return false;
      }

      if(currentBlockpreviousHash !== previousBlock.hash){
        return false;
      }

      return true;

    }
  }

  addBlock(newBlock){
    newBlock.previousHash = this.getLatestBlock().hash();
    newBlock.hash = newBlock.calculateHash();
    this.chain.push(newBlock)

  }

}


let savjeeCoin = new Blockchain();
savjeeCoin.addBlock(new Block(0, "01/01/2017", {amount:7}));
savjeeCoin.addBlock(new Block(0, "01/01/2017", {amount:10}));

console.log(JSON.stringfy(savjeeCoin,null,4);
```

index 옵션
timestamp 블록체인이 만들어진시간
data 추가하기를 원하는 데이터 (얼마가 저장되고, 누구한테 보냈는지)
previousHash 는 이전의 해쉬값이다. 이것은 중요하다 블록체인의 도덕성을 보장한다.
