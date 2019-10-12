<template>
	<div class="a">
		
		<p>
			Balance: {{balance}} eth
		</p>
		<p>
			管理者:{{manager}}
		</p>
		<p>人数:{{players.length}}</p>
		<div style="width: 200px;margin-bottom: 20px">
			<el-input v-model="value" placeholder="请输入数量" type="number"></el-input>
			
		</div>
		<el-button type='primary' @click="send">提交</el-button>
		<div style="width: 300px;margin-top: 30px">

			<el-button type='primary' @click="pickwiner">开奖</el-button>

		</div>
		<el-dialog
  title="提示"
  :visible.sync="dialogVisible"
  width="30%">
  <span>{{msg}}</span>
  <span slot="footer" class="dialog-footer">
    <el-button @click="dialogVisible = false">取 消</el-button>
    <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
  </span>
</el-dialog>
	</div>
</template>
<script>
	export default{
		data(){
			return {
				msg:'hello',
				manager:'',
			    players:[],
			    balance:'',
			    value:'',
			    message:'',
			    dialogVisible:false
			}
		},
		created(){
			this.getBalance()
		},
		methods:{
			async getBalance(){
				let ba = await this.$web3.eth.getBalance(this.$lottery.options.address)
				this.balance = this.$web3.utils.fromWei(ba,'ether')
				this.manager = await this.$lottery.methods.manager().call()
				this.players = await this.$lottery.methods.getPlayers().call()
			},
			async send(){
				if(!this.value){
					this.$message.error('请输入数量')
					return
				}
				this.dialogVisible=true
				this.msg = '正在获取账户...'
				const accounts = await this.$web3.eth.getAccounts()
				this.msg = '正在发送交易...'
				await this.$lottery.methods.enter().send({from:accounts[0],value:this.$web3.utils.toWei(this.value,'ether')})
				this.msg = '发送完成.'
				this.value = ''
				this.getBalance()
			},
			async pickwiner(){
				this.dialogVisible=true
                 const accounts = await this.$web3.eth.getAccounts()
                 this.msg = '敬请期待...'
                 await this.$lottery.methods.pickwiner().send({from:accounts[0]});
                 this.msg ='祝你好运...'
                 this.getBalance()

			}

		}
	}
</script>
<style type="text/css">
	.a{
		color: #666
	}
</style>