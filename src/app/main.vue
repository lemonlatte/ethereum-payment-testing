<style>
</style>

<template>
<div class="jumbotron">
  <h1>Ethereum payment contract testing</h1>

  <p> Artist Account (seller) : <a href="https://ropsten.etherscan.io/address/0x009Db0C44212221c7849B76A529a7b9A30C2b2EA">0x009Db0C44212221c7849B76A529a7b9A30C2b2EA</a></p>
  <p> Contract Creator (us) : <a href="https://ropsten.etherscan.io/address/0x00e14d9832AeF01f60b9C0C0901571c0e4883dDa">0x00e14d9832AeF01f60b9C0C0901571c0e4883dDa</a></p>
  <p> Contract Address: <a href="https://ropsten.etherscan.io/address/0xfaD3a9dBcB6Bb0156eF08A2C50b4652512759E00">0xfaD3a9dBcB6Bb0156eF08A2C50b4652512759E00</a></p>
  <p> When someone buy this artwork, we get 1/10 of the fund as a fee. The rest of fund is sent to the artist</p>
  <p> <b> For each transaction, please set the gas limit greater than 120000 </b> </p>

  <p>
    <label>Bitmark Payment Id: </label><input v-model="paymentId">
  </p>
  <p>
    <label>Price: </label><input type="number" v-model="price">
  </p>
  <p>
    <button @click="pay" :disabled="!testnet">Buy with minial 0.01 ether </button>
    <span v-if="!testnet"> Only support Ropsten testnet !!!</span>
  </p>
  <p v-if="tx">Successfully submitted. <a :href="'https://ropsten.etherscan.io/tx/' + tx">{{ tx }}</a></p>
  <p><a href="https://chrome.google.com/webstore/detail/metamask/nkbihfbeogaeaoehlefnkodbefgpgknn?hl=en">
    <img width="300px" src="https://raw.githubusercontent.com/MetaMask/faq/master/images/download-metamask.png"></a></p>
</div>
</template>

<script>

const Eth = require('ethjs');
const eth = new Eth(window.web3.currentProvider);

const contractABI = [
  {
    "constant": true,
    "inputs": [
      {
        "name": "",
        "type": "uint256"
      }
    ],
    "name": "payIds",
    "outputs": [
      {
        "name": "",
        "type": "string"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "bitmarkId",
    "outputs": [
      {
        "name": "",
        "type": "bytes32"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "authorAddr",
    "outputs": [
      {
        "name": "",
        "type": "address"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "owner",
    "outputs": [
      {
        "name": "",
        "type": "address"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "constant": false,
    "inputs": [
      {
        "name": "payId",
        "type": "string"
      }
    ],
    "name": "Pay",
    "outputs": [],
    "payable": true,
    "type": "function"
  },
  {
    "constant": true,
    "inputs": [],
    "name": "price",
    "outputs": [
      {
        "name": "",
        "type": "uint256"
      }
    ],
    "payable": false,
    "type": "function"
  },
  {
    "inputs": [
      {
        "name": "_price",
        "type": "uint256"
      },
      {
        "name": "_authorAddr",
        "type": "address"
      },
      {
        "name": "_bitmarkId",
        "type": "bytes32"
      }
    ],
    "payable": false,
    "type": "constructor"
  },
  {
    "payable": false,
    "type": "fallback"
  },
  {
    "anonymous": false,
    "inputs": [
      {
        "indexed": false,
        "name": "payId",
        "type": "string"
      }
    ],
    "name": "Paid",
    "type": "event"
  }
]



export default {
  created () {
    web3.version.getNetwork((err, netId) => {
      switch (netId) {
        case "3":
          console.log('This is the ropsten test network.')
          this.testnet = true
          break
        default:
          console.log('not supported network.')
      }
    })
  },

  methods: {
    pay (){
      if (this.paymentId === "") {
        alert("please input a payment id")
        return
      }
      eth.accounts().then((accounts) => {
        let contract = eth.contract(contractABI, '0x', {
          from: accounts[0],
          value: Eth.toWei(this.price, "ether"),
          gas: 120000
        }).at("0xfaD3a9dBcB6Bb0156eF08A2C50b4652512759E00")
        contract.Pay(this.paymentId).then((result) => {
          this.tx = result;
        })
      })
    }
  },

  data () {
    return {
      "paymentId": "test",
      "price": 0.01,
      "testnet": false,
      "tx": ""
    }
  }
}
</script>
