<template>
   <!-- Page Content -->
        <div id="page-wrapper">
            <div class="container-fluid">
                <div class="row">
                    <div class="col-lg-12">
                        <h1 class="page-header">Launcher</h1>
                    </div>
                    <!-- /.col-lg-12 -->
                </div>
                <!-- /.row -->
                <div class="row">
                  <div class="col-lg-12 form-group">
                    <div class="row">
                      <div class="col-md-4">
                        <label>Files
                          <input type="file" id="files" ref="files" multiple v-on:change="handleFilesUpload()"/>
                        </label>
                      </div>
                      <div class="col-md-4">
                        <div class="panel panel-default">
                          <div class="panel-heading">
                              ABI
                          </div>
                          <div class="panel-body">
                            <p>{{contract.abi ? contract.abi : "Please Upload a contract"}}</p>
                          </div>
                        </div>
                      </div>
                      <div class="col-md-4">
                        <div class="panel panel-default">
                          <div class="panel-heading">
                              Bytecode
                          </div>
                          <div class="panel-body">
                            <p>{{contract.bytecode ? contract.bytecode : "Please Upload a contract"}}</p>
                          </div>
                        </div>
                      </div>
                    </div>
                    <div class="row" style="margin-top: 20px;">
                      <div class="col-md-3">
                        <label>Select Provider</label>
                        <select class="form-control" v-model="provider">
                          <option value="http://127.0.0.1:8545">Ganache UI</option>
                          <option value="http://127.0.0.1:7545">Ganache CLI</option>
                          <option value="https://node2.blockcerts.com:6111">
                            BCBC DEV Node2
                          </option>
                          <option value="rinkeby">Rinkeby</option>
                          <option value="ropsten">Ropsten</option>
                          <option value="homestead">homestead-mainnet</option>
                          <option value="https://kovan.infura.io/v3/6fd2fd8e1b334661b0c38556bd48b257">Kovan Testnet Network</option>
                        </select>
                      </div>
                    </div>
                    <div class="row" style="margin-top: 20px;">
                      <div class="col-md-3">
                        <label>Private Key</label>
                        <input class="form-control" placeholder="Enter Private Key" v-model="privateKey">
                      </div>
                    </div>
                  </div>
                </div>
                <div class="row">
                  <div class="col-md-3">
                    <button class="btn btn-primary" v-on:click="deploy()">Deploy Smart Contract</button>
                  </div>
                  <div class="col-md-6">
                    <div class="panel panel-default">
                      <div class="panel-heading">
                          Logs
                      </div>
                      <div class="panel-body">
                        <p>{{logs}}</p>
                      </div>
                    </div>
                  </div>
                </div>
            </div>
            <!-- /.container-fluid -->
        </div>
</template>

<script>
import { ethers } from "ethers";
export default {
  name: "Launcher",
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
      files: [],
      contract: {},
      provider: "http://127.0.0.1:8545",
      privateKey: "",
      logs: ""
    };
  },
  beforeMount() {},
  methods: {
    getData(file) {
      return new Promise(function(resolve, reject) {
        var reader = new FileReader();
        reader.onload = function() {
          resolve(reader.result);
        };
        reader.onerror = reject;
        reader.readAsText(file);
      });
    },
    async handleFilesUpload() {
      var files = this.$refs.files.files;
      var promise = this.getData(files[0]);
      var contractData = promise.then(function(result) {
        return result;
      });
      this.contract = JSON.parse(await contractData);
    },
    async deploy() {
      //TODO ethers deployment
      let provider = new ethers.getDefaultProvider(this.provider);
      let wallet = new ethers.Wallet(this.privateKey, provider);
      let factory = new ethers.ContractFactory(this.contract.abi,
        this.contract.bytecode,
        wallet
      );

      factory
        .deploy()
        .then(contract => {
          this.logs =
            "contract address - " +
            contract.address +
            " -- hash - " +
          contract.deployTransaction.hash;
        })
        .catch(error => {
          console.log("Error", error);
          this.logs = error;
        });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
  white-space: pre-wrap;
  overflow-wrap: break-word;
}
</style>
