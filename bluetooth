<template>
	<view>
		<view class="comm-content"  v-if="radar">
			<view class="radar"></view>
		</view>
		<u-empty text="暂无蓝牙设备列表" mode="search" iconSize="150" margin-top="40" v-show="empty" ></u-empty>
		

<!-- 		<u-button type="primary" @click="initBluetooth()">蓝牙初始化</u-button> -->

<!-- 		<u-button type="warning" @click="printGo(MacAddress,printText)">开始打印</u-button> -->
		<view class="searchBtn">
			<u-button type="success" @click="searchBluetooth()">搜索蓝牙设备</u-button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				found:'',
				printText:'汉皇重色思倾国，御宇多年求不得。\n杨家有女初长成，养在深闺人未识。\n天生丽质难自弃，一朝选在君王侧。\n回眸一笑百媚生，六宫粉黛无颜色。\n春寒赐浴华清池，温泉水滑洗凝脂。\n侍儿扶起娇无力，始是新承恩泽时。\n云鬓花颜金步摇，芙蓉帐暖度春宵。\n春宵苦短日高起，从此君王不早朝。',
				MacAddress:'00:1F:B7:03:3D:31',
				radar:false,
				empty:true
			}
		},
		methods: {
			//初始化蓝牙,引入java类BluetoothAdapter,调用其静态方法getDefaultAdapter()获取蓝牙适配器;
			initBluetooth(){
				let BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter"),
					BAdapter = new BluetoothAdapter.getDefaultAdapter();
				console.log("适配器"+BAdapter)
				if(BAdapter == null){
					uni.showToast({
						title:'该硬件平台不支持蓝牙适配器',
						icon:"error",
						duration:1500
					})
				}else{
					//判断当前环境下蓝牙是否开启,如果没有开启显示对话框。
					if(!BAdapter.isEnabled()){
						//调用安卓系统自身的对话框。回调函数通知点击按钮的索引值,1为取消0为确认;
						plus.nativeUI.confirm("该应用需要打开蓝牙,是否打开蓝牙",function(e){
							if(e.index == 0){
								BAdapter.enable()
							}
						},"蓝牙权限")
					
					}else{
						uni.showToast({
							title:'蓝牙已打开',
							icon:'success',
							duration:1500
						})
					}
				}
			},
			
			searchBluetooth(){
				//APP必须给位置权限，否则无法扫描到设备
				this.radar = true;
				this.empty = false
				console.log("搜索事件执行")
				let _this = this;
				let BluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter"),
					BAdapter = new BluetoothAdapter.getDefaultAdapter(),
					IntentFilter = plus.android.importClass('android.content.IntentFilter'),
					main = plus.android.runtimeMainActivity();
				let discoveryFilter = new IntentFilter(),
					BluetoothDevice = plus.android.importClass('android.bluetooth.BluetoothDevice'),
					bleDevice = new BluetoothDevice();
				//如果当前没有处于搜索过程,那么开始一个新的搜索过程;
				if(!BAdapter.isDiscovering ()){
					console.log(BAdapter.isDiscovering ())
					BAdapter.startDiscovery()
				}
		
				//监听广播活动相关的常量值
				discoveryFilter.addAction(BAdapter.ACTION_DISCOVERY_STARTED)
				console.log("监听常量")
				discoveryFilter.addAction(BAdapter.ACTION_STATE_CHANGED)
				discoveryFilter.addAction(BAdapter.ACTION_DISCOVERY_FINISHED)
				discoveryFilter.addAction(bleDevice.ACTION_FOUND) 
				discoveryFilter.addAction(bleDevice.ACTION_BOND_STATE_CHANGED)
				
				let receiver = plus.android.implements('io.dcloud.android.content.BroadcastReceiver',{
						onReceive:function(context,intent){
							plus.android.importClass(intent);
							console.log("action:"+intent.getAction());
							console.log(intent.getAction() === 'android.bluetooth.adapter.action.DISCOVERY_FINISHED')
							if(intent.getAction() === 'android.bluetooth.adapter.action.DISCOVERY_FINISHED'){
								BAdapter.cancelDiscovery()
								console.log(BAdapter.isDiscovering())
								main.unregisterReceiver(receiver)
								console.log("搜索结束")
								_this.radar = false
							}
							
							
							
							if(intent.getAction() === BAdapter.ACTION_DISCOVERY_STARTED){
								console.log("搜索开始")
							}
							
							let device = intent.getParcelableExtra(BluetoothDevice.EXTRA_DEVICE),
								deviceName = device.getName(),
								deviceAddress = device.getAddress();
							
							// console.log("绑定信息:"+BAdapter.getBondedDevices ())
							
							
							if(intent.getAction() === BluetoothDevice.ACTION_FOUND && deviceName !=null && deviceAddress != null){
								console.log("已发现蓝牙设备：" + deviceName + '    ' + deviceAddress);
							}
							
							
							if(device.getBondState() === BluetoothDevice.BOND_BONDED && deviceName !=null && deviceAddress != null){
								console.log("已匹配蓝牙设备：" + deviceName + '    ' + deviceAddress);
								console.log("MAC验证:"+BAdapter.checkBluetoothAddress(deviceAddress))
							}else if (device.getBondState() === BluetoothDevice.BOND_NONE && deviceName !=null && deviceAddress != null){
								console.log("未匹配蓝牙设备：" + deviceName + '    ' + deviceAddress);
							}
							
							
							
							

							

						}
					})

				main.registerReceiver(receiver,discoveryFilter)
				
				
				//注册广播接收器
				
		
			},
			
			//连接设备前，请先关闭扫描蓝牙，否则连接成功后，再次扫描会发生阻塞，扫描不到设备。  

			
			//未配对转为已配对。执行creadBond，系统会发起会话，需要用户进行同意。
			// BluetoothDevice remoteDevice = bluetoothAdapter.getRemoteDevice(address);获取地址
			// Method createBond = BluetoothDevice.class.getMethod("createBond");进行配对
			// createBond.invoke(remoteDevice);
			
			
			
			//连接蓝牙打印机打印
			printGo(deviceAddress,byteStr){
				//deviceAddress 配对设备的MAC地址
				//byteStr 需要打印的内容
				
				console.log("接收打印参数中:"+deviceAddress,byteStr)
				let plusMain  = plus.android.runtimeMainActivity(),
					bluetoothAdapter = plus.android.importClass("android.bluetooth.BluetoothAdapter"),
					UUID = plus.android.importClass('java.util.UUID'),
					//蓝牙串行端口基于SPP协议(Serial Port Profile),能在蓝牙设备之间创建串口进行数据传输
					//SPP的UUID：00001101-0000-1000-8000-00805F9B34FB
					uuid = UUID.fromString('00001101-0000-1000-8000-00805F9B34FB'),
					BAdapter = bluetoothAdapter.getDefaultAdapter(),
					device = BAdapter.getRemoteDevice(deviceAddress);
					
					
					plus.android.importClass(device);
					console.log('打印模块初始化中')
					
					//建议socket通信连接蓝牙
					let bluetoothSocket = device. createRfcommSocketToServiceRecord(uuid);
					plus.android.importClass(bluetoothSocket);
					
					if(!bluetoothSocket.isConnected()){
						bluetoothSocket.connect();
						console.log("连接设备连接完毕,打印机就绪....");
					}
					
					let outputStream = bluetoothSocket.getOutputStream();
					plus.android.importClass(outputStream);
					let bytes = plus.android.invoke(byteStr,'getBytes','gbk');
					outputStream.write(bytes);
					outputStream.flush();
					device = null
					console.log("打印完毕")
					
			}
		}
	}
</script>

<style lang="scss">
	.comm-content {
		width: 100%;
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		flex-direction: column;
	}
	
	
	.radar {
		margin-top: 50rpx;
		background: -webkit-radial-gradient(center,
			rgba(255, 0, 0, 1.0) 0%, rgba(32, 255, 77, 0) 2%),
			-webkit-repeating-radial-gradient(rgba(122, 99, 255, 0.0) 1%,
			rgba(32, 255, 77, 0) 18%, #12C98A 18.6%,
			rgba(32, 255, 77, 0) 18.9%);
		width: 75vw;
		height: 75vw;
		max-height: 75vh;
		max-width: 75vh;
		position: relative;
		border-radius: 50%;
		border: 1rpx solid #12C98A;
		overflow: hidden;
		top: 150rpx;
	}
	
	
	.radar:after {
		content: ' ';
		display: block;
		background-image: linear-gradient(45deg, rgba(0, 255, 51, 0) 50%, #12C98A 100%);
		width: 50%;
		height: 50%;
		position: absolute;
		top: 0;
		left: 0;
		animation: radar-beam 5s infinite;
		animation-timing-function: linear;
		transform-origin: bottom right;
		border-radius: 100% 0 0 0;
	}
	
	@keyframes radar-beam {
		0% {
			transform: rotate(0deg);
		}
	
		100% {
			transform: rotate(360deg);
		}
	}
	
	.searchBtn{
		bottom: 0rpx;
		position: absolute;
		width: 100%;
	}

</style>
