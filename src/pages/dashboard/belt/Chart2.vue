<template>
	<div ref="testLine" id="testLine" style="width:600px; height:300px"></div>
</template>


<script>
	export default {
		name: 'DisplayDraw',
		data() {
			return {
				path: "url", //修改地址
				ws: {}, //保存websocket对象
				ydata: [0.5, 0.6, 0.2, 0.3, 0.7, 0.2, 0.6],
				xdata: [0, 5, 10, 15, 20, 25, 30],
				i: 35,
				myChart: null,
				option: {
					title:{
						text:"皮带减压机油压曲线",
						padding:[5,250]
					},
					xAxis: {
						type: 'category',
						data: this.xdata,
						name: '时间'
					},
					yAxis: {
						type: 'value',
						name: '油压/Mpa'
					},
					series: [{
						data: this.ydata,
						showSysbol: false,
						type: 'line'
					}],
				}
			}
		},
		methods: {
			addData: function() {
				this.xdata.shift();
				this.xdata.push(this.i);
				this.i = this.i + 5;
				this.ydata.shift();
				this.ydata.push(Math.random(1));
				this.option.xAxis.data = this.xdata;
				this.option.series[0].data = this.ydata;
				this.myChart.setOption(this.option);
			},
			//websocket连接后端数据
			addData_websocket:function() {
				this.ws = new WebSocket(this.path);
				//监听是否连接成功
				this.ws.onopen = () => {
					console.log("ws连接状态：" + this.ws.readyState);
				};
				//接听服务器发回的信息并处理展示
				this.ws.onmessage = (res) => {
					console.log("接收到来自服务器的消息：");
					console.log(res.data)
					this.xdata.shift();
					this.xdata.push(this.i++);
					this.ydata.shift();
					this.wsData = eval("(" + res.data + ")");
					this.ydata.push(this.wsData.data[0]);
					this.option.xAxis.data = this.xdata;
					this.option.series[0].data = this.ydata;
					this.myChart.setOption(this.option);
					console.log(this.ydata);
				};
				//监听连接关闭事件
				this.ws.onclose = () => {
					//监听整个过程中websocket的状态
					console.log("ws连接状态：" + this.ws.readyState);
				};
				//监听并处理error事件
				this.ws.onerror = function(error) {
					console.log(error);
				};
			},
		},
		mounted() {
			this.myChart = this.$echarts.init(this.$refs.testLine)
			this.myChart.setOption(this.option)
			setInterval(this.addData, 1000)
		},

	}
</script>

<style>
</style>
