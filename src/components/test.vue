<template>
    <el-container>
        <el-header style="height: ">
            <div id="topheader">
                <el-col :span="10" :offset="4">
                    <div class="topheader_box">
                        <div id="topheader_a">
                            <el-image style="margin-right: 10px" src="/images/logoShort.svg"></el-image>
                            <h2 style="margin-right: 15px;">安卓沙箱</h2>
                        </div>
                        <div class="topheader_box inputbox">
                            <i class="searchico"></i>
                            <input placeholder="搜索文件Hash(MD5)" class="" type="text" value="">
                        </div>
                         <el-button  style="margin-left: 10px;" @click="status.dialogVisible = true">上传文件</el-button>
                    </div>
                </el-col>
                <el-col :span="10" >
                   
                    <el-dialog title="提示" :visible.sync="status.dialogVisible" width="30%">
                        <el-upload class="upload-demo" drag action="https://jsonplaceholder.typicode.com/posts/" multiple>
                            <i class="el-icon-upload"></i>
                            <div class="el-upload__text">将文件拖到此处，或<em>点击上传</em></div>
                            <div class="el-upload__tip" slot="tip">只能上传apk文件</div>
                        </el-upload>
                    </el-dialog>
                </el-col>
            </div>
        </el-header>
        <el-main>
            <el-row :gutter="20" class="parent-row">
                <el-col :span="4">
                    <!-- 第一个 el-row，最左侧 -->
                    <el-row type="flex" class="row-bg">
                        <el-col :span="24" style="">
                            <el-card class="custom-card" shadow="hover">
                                <el-image :src="status.filetype === 'evil' ? src.filetype.evil : (status.filetype === 'suspicious' ? src.filetype.suspicious : src.filetype.safe)">
                                </el-image>
                                <p :class="this.status.filetype">{{ status.filetype === 'evil' ? '恶意' : (status.filetype === 'suspicious' ? '可疑' : '安全') }}</p>
                                <el-divider id="dividerbottom"></el-divider>
                                <div ref="pieChart" style="width: 100%; height: 150px;"></div>
                            </el-card>
                        </el-col>
                    </el-row>
                </el-col>
                <el-col :span="20" class="right-col">
                    <!-- 右侧两个 el-row，上下排列 -->
                    <el-row type="flex" class="row-bg right-content" :gutter="20">
                        <el-col :span="5">
                            <el-card class="custom-card" shadow="hover">
                                <div class="card-content">
                                    <div class="color-bar" id="color-bar_1"></div>
                                    <div class="ico_bar">
                                        <el-image :src="src.permission_data"></el-image>
                                        <P style="font-size: 12px;">权限信息</P>
                                    </div>
                                    <div class="numbers">
                                        <div class="numDescipt1">敏感权限</div>
                                        <div class="number1">
                                            <el-statistic id="number1_span" :value="cardcount_data.permission_data[0].statValue" :target="cardcount_data.permission_data[0].targetValue" :precision="0"></el-statistic>
                                        </div>
                                        <el-divider></el-divider>
                                        <div class="number2">
                                            <div class="numDescipt2">权限总数：</div>
                                            <el-statistic id="number2_span" :value="cardcount_data.permission_data[1].statValue" :target="cardcount_data.permission_data[1].targetValue" :precision="0"></el-statistic>
                                        </div>
                                    </div>
                                </div>
                            </el-card>
                        </el-col>
                        <el-col :span="6">
                            <el-card class="custom-card" shadow="hover">
                                <div class="card-content">
                                    <div class="color-bar" id="color-bar_3"></div>
                                    <div class="ico_bar">
                                        <el-image :src="src.sandbox_data"></el-image>
                                        <P style="font-size: 12px;">行为信息</P>
                                    </div>
                                    <div class="numbers">
                                        <div class="timeline-container" ref="timelineContainer">
                                            <div class="timeline-wrapper" :style="{ transform: `translateY(-${actiontimeline.currentPosition}px)` }">
                                                <el-timeline class="timeline" ref="timeline">
                                                    <el-timeline-item id="el-timeline-item__content" v-for="(item, index) in actiontimeline.visibleItems" :key="index">
                                                        <span>
                                                            <el-tag>{{ formatTimestamp(item.timestamp) }}</el-tag>
                                                        </span>
                                                        <span>
                                                            <h4>{{ item.action }}</h4>
                                                        </span>
                                                    </el-timeline-item>
                                                </el-timeline>
                                            </div>
                                        </div>
                                    </div>
                                </div>
                            </el-card>
                        </el-col>
                        <el-col :span="14">
                            <el-card class="custom-card" shadow="hover">
                                <div class="card-content">
                                    <div class="color-bar" id="color-bar_2"></div>
                                    <div class="ico_bar">
                                        <el-image :src="src.network_data"></el-image>
                                        <P style="font-size: 12px;">网络行为</P>
                                    </div>
                                    <div class="numbers">
                                        <div class="scrollablediv" style="display: flex;">
                                            <span>远程IP</span>
                                            <span>远程端口</span>
                                            <span>请求时间</span>
                                        </div>
                                        <el-divider id="dividertop"></el-divider>
                                        <div style="height: 100px; overflow: hidden;" @mouseenter="pauseCarousel" @mouseleave="resumeCarousel">
                                            <el-carousel direction="vertical" :interval="4000" arrow="never" ref="carousel" @change="handleCarouselChange" style="height: 100%;">
                                                <el-carousel-item v-for="(item, index) in network_data.detail" :key="index" style="height: 100%; display: flex; align-items: center; justify-content: center;">
                                                    <!-- 这里是列表项的内容 -->
                                                    <div class="scrollablediv" style="display: flex; ">
                                                        <el-tag type="success">{{ item.destip }}</el-tag>
                                                        <el-tag type="success">{{ item.destport }}</el-tag>
                                                        <el-tag type="success">{{ formatTimestamp(item.timestamp) }}</el-tag>
                                                    </div>
                                                </el-carousel-item>
                                            </el-carousel>
                                        </div>
                                        <el-divider id="dividerbottom"></el-divider>
                                    </div>
                                </div>
                            </el-card>
                        </el-col>
                    </el-row>
                    <el-row class="row-bg right-content" id="filetype" style="margin-top: 20px;">
                        <el-col :span="24">
                            <el-card class="box-card" shadow="hover">
                                <div slot="header" class="clearfix">
                                    <span>文件信息</span>
                                </div>
                                <el-descriptions>
                                    <el-descriptions-item label="文件名">{{this.baseinfo_data.filename}}</el-descriptions-item>
                                    <el-descriptions-item label="hash值">
                                        <el-tag size="small">{{this.baseinfo_data.md5}}</el-tag>
                                    </el-descriptions-item>
                                    <el-descriptions-item label="packageName">
                                        <el-tag size="small">{{this.baseinfo_data.packageName}}</el-tag>
                                    </el-descriptions-item>
                                    <el-descriptions-item label="上传时间">
                                        <el-tag size="small">{{this.formatTimestamp(this.baseinfo_data.time)}}</el-tag>
                                    </el-descriptions-item>
                                </el-descriptions>
                            </el-card>
                        </el-col>
                    </el-row>
                </el-col>
            </el-row>
            <el-row class="row-bg">
                <el-col :span="24">
                    <div class="grid-content bg-purple">
                        <el-card class="box-card" shadow="hover">
                            <el-tabs type="border-card">
                                <el-tab-pane>
                                    <span slot="label">
                                        <i class="el-icon-help"></i>
                                        <span>权限信息</span></span>
                                    <div class="grid-content bg-purple">
                                        <el-table :data="permission_data.data" max-height="500" :default-sort="{prop:'type', order:'descending'}" :cell-style="rowStyle">
                                            <el-table-column align="center" prop="permissionname" label="获取权限名" width="180">
                                            </el-table-column>
                                            <el-table-column align="center" prop="permissiondis" label="描述">
                                            </el-table-column>
                                            <el-table-column align="center" prop="type" label="权限类型" sortable>
                                                <template slot-scope="scope">
                                                    <el-tag :type="scope.row.type === '0' ? 'success' : (scope.row.type === '1' ? 'warning' : 'danger')">
                                                        {{ scope.row.type === '0' ? '安全' : (scope.row.type === '1' ? '可疑' : '敏感') }}
                                                    </el-tag>
                                                </template>
                                            </el-table-column>
                                        </el-table>
                                    </div>
                                </el-tab-pane>
                                <el-tab-pane>
                                    <span slot="label">
                                        <i class="el-icon-help"></i>
                                        <span>网络行为</span></span>
                                    <div class="grid-content bg-purple">
                                        <el-table :data="network_data.data" :fit="true">
                                            <el-table-column align="center" prop="destip" label="远程IP" width="180">
                                            </el-table-column>
                                            <el-table-column align="center" prop="destport" label="端口">
                                            </el-table-column>
                                            <el-table-column align="center" prop="count" label="统计">
                                                <template slot-scope="scope">
                                                    <el-collapse>
                                                        <el-collapse-item>
                                                            <template slot="title">
                                                                <div class="running">{{ scope.row.count }} </div>
                                                            </template>
                                                            <span class="collapse_detail" v-for="(detail, index) in scope.row.details" :key="index">{{ detail }}</span>
                                                        </el-collapse-item>
                                                    </el-collapse>
                                                </template>
                                            </el-table-column>
                                        </el-table>
                                    </div>
                                </el-tab-pane>
                                <el-tab-pane>
                                    <span slot="label">
                                        <i class="el-icon-help"></i>
                                        <span>漏洞沙箱</span></span>
                                    <div class="grid-content bg-purple">
                                        <el-table :data="vulsandbox_data.data" max-height="300">
                                            <el-table-column align="center" prop="vulname" label="漏洞名称" width="180">
                                            </el-table-column>
                                            <el-table-column align="center" prop="detail" label="漏洞详情">
                                                <template slot-scope="scope">
                                                    <div v-if="scope.row.detailtype === 'txt'">{{ scope.row.detail }}</div>
                                                    <div v-else-if="scope.row.detailtype === 'image'">
                                                        <img :src="scope.row.detail" alt="Vulnerability Image">
                                                    </div>
                                                </template>
                                            </el-table-column>
                                        </el-table>
                                    </div>
                                </el-tab-pane>
                                <el-tab-pane label="行为沙箱" @click.native="showDrawer">
                                    <el-row :gutter="20">
                                        <el-col :span="24"><canvas ref="lineChart" width="1000" height="400"></canvas></el-col>
                                    </el-row>
                                </el-tab-pane>
                                <el-drawer :visible.sync="status.drawerVisible" title="行为记录">
                                    <el-timeline>
                                        <el-timeline-item v-for="(item, index) in sandbox_data.data" :key="index" :timestamp="formatTimestamp(item.timestamp)" placement="top">
                                            <el-card style=" text-align: left;" shadow="hover">
                                                <h4 style="margin-top: -2%;  color: #1296db;">{{ item.action }}</h4>
                                                <div v-if="item.detail.length > 0">
                                                    <li v-for="(detail, i) in item.detail" :key="i">{{ detail }}</li>
                                                </div>
                                            </el-card>
                                        </el-timeline-item>
                                    </el-timeline>
                                </el-drawer>
                            </el-tabs>
                        </el-card>
                    </div>
                </el-col>
            </el-row>
        </el-main>
    </el-container>
</template>
<script>
import '/public/css/mainpage.css';
// import axios from 'axios';
import * as echarts from 'echarts';
export default {
    name: 'initbypass',
    data() {
        return {
            status: { filetype: "", drawerVisible: false, dialogVisible: false },

            pieChartData: [],
            src: {
                logoshort: "/images/logoshort.svg",
                filetype: {
                    evil: "/images/malicious.svg",
                    safe: "/images/safe.svg",
                    suspicious: "/images/suspicious.svg"
                },
                permission_data: "/images/danger.svg",
                network_data: "/images/netaction.svg",
                sandbox_data: "/images/action.svg",
            },
            baseinfo_data: {
                filename: '测试.apk',
                packageName: 'xxx',
                md5: 'xxx',
                time: '1699308523'
            },
            permission_data: {
                data: [
                    { "permissionname": "相机", "permissiondis": "允许应用访问相机拍照", "type": "2" },
                    { "permissionname": "位置信息", "permissiondis": "获取用户精确位置信息", "type": "2" },
                    { "permissionname": "麦克风", "permissiondis": "允许应用录制音频", "type": "2" },
                    { "permissionname": "联系人", "permissiondis": "访问用户联系人列表", "type": "1" },
                    { "permissionname": "存储空间", "permissiondis": "访问设备存储以保存文件", "type": "0" },
                    { "permissionname": "相机", "permissiondis": "允许应用访问相机拍照", "type": "2" },
                    { "permissionname": "位置信息", "permissiondis": "获取用户精确位置信息", "type": "2" },
                    { "permissionname": "麦克风", "permissiondis": "允许应用录制音频", "type": "2" },
                    { "permissionname": "联系人", "permissiondis": "访问用户联系人列表", "type": "1" },
                    { "permissionname": "存储空间", "permissiondis": "访问设备存储以保存文件", "type": "0" },

                ]
            },            
            network_data: {
                //DATA由JS统计
                data: [],
                detail: [
                    { 'destip': '107.75.58.149', 'destport': 80, 'timestamp': '1699308523' },
                    { 'destip': '107.75.58.149', 'destport': 80, 'timestamp': '1394737692' },
                    { 'destip': '107.75.58.149', 'destport': 81, 'timestamp': '1521817190' },
                    { 'destip': '66.171.186.10', 'destport': 11335, 'timestamp': '1432362339' },
                    { 'destip': '83.12.76.248', 'destport': 25478, 'timestamp': '1605774223' },
                    { 'destip': '248.167.171.131', 'destport': 31010, 'timestamp': '1413473101' },
                    { 'destip': '65.242.124.58', 'destport': 5045, 'timestamp': '1567484522' },
                    { 'destip': '16.72.133.79', 'destport': 27214, 'timestamp': '1516647261' },
                    { 'destip': '5.155.255.76', 'destport': 29935, 'timestamp': '1441781506' },
                    { 'destip': '235.28.190.197', 'destport': 45514, 'timestamp': '1661912286' },
                    { "destip": "192.168.5.55", "destport": 8083, "timestamp": "1616161616" }
                ]
            },
            vulsandbox_data: {
                data: [{
                        "vulname": "权限绕过漏洞",
                        "detailtype": "txt",
                        "detail": "漏洞描述文本",
                        "type": "1"
                    },
                    {
                        "vulname": "数据泄露",
                        "detailtype": "image",
                        "detail": "https://example.com/vulnerability_image.jpg",
                        "type": "2"
                    },
                    {
                        "vulname": "远程代码执行",
                        "detailtype": "txt",
                        "detail": "漏洞描述文本",
                        "type": "2"
                    },
                    {
                        "vulname": "拒绝服务攻击",
                        "detailtype": "image",
                        "detail": "https://example.com/vulnerability_image.jpg",
                        "type": "1"
                    },
                    {
                        "vulname": "权限绕过漏洞",
                        "detailtype": "txt",
                        "detail": "漏洞描述文本",
                        "type": "2"
                    },
                    {
                        "vulname": "数据泄露",
                        "detailtype": "txt",
                        "detail": "漏洞描述文本",
                        "type": "0"
                    }
                    // 可以根据需要添加更多数据条目
                ],
            },
            sandbox_data: {
                data: [{
                        "action": "访问设备文件",
                        "detail": ["捕捉到访问文件/xxx/xxx/xxx/1.txt"],
                        "timestamp": "1701421702",
                        "hideTimestamp": false
                    }
                ]
            },
            cardcount_data: {
                permission_data: [{ statValue: 0, targetValue: 0 }, { statValue: 0, targetValue: 0 }]
            },
            scrollable_data: {
                currentIndex: 0,
                isPaused: false
            },
            actiontimeline: {
                visibleItems: [], // 存放当前可见的时间线数据
                scrollInterval: null, // 滚动定时器
                scrollSpeed: 4000, // 滚动速度
                currentPosition: 0, // 当前滚动位置
                itemHeight: 200, // 每个时间线项的高度
            }
        };
    },
    components: {

    },
    created() {
  this.websocket = new WebSocket('ws://api/getinfo'); // 替换成你的后端 WebSocket 地址

  this.websocket.onopen = () => {
    // 连接建立后，向后端发送请求
    this.websocket.send('Request Data');
  };

  this.websocket.onmessage = (event) => {
    // 处理后端发送的数据
    const data = JSON.parse(event.data);
    // 在这里对接收到的数据进行处理和赋值给相应的数据对象
    // 例如：
    this.permission_data.data = data.permission_data;
    this.network_data.data = data.network_data;
    this.vulsandbox_data.data = data.vulsandbox_data;
    this.sandbox_data.data = data.sandbox_data;
  };

  this.websocket.onclose = () => {
    // 连接关闭时的操作
    console.log('WebSocket connection closed');
  };

  this.websocket.onerror = (error) => {
    // 处理错误
    console.error('WebSocket error:', error);
  };
},

    computed: {},
    mounted() {
        this.typeTwoCount();
        //app类型判断
        this.Getsfiletypetatus();
        //统计数据
        this.getnetworkdata();

        // 设置定时器实现自动滚动
        setInterval(() => {
            if (!this.scrollable_data.isPaused) {

                this.scrollable_data.currentIndex++;
                if (this.scrollable_data.currentIndex === this.network_data.detail.length) {
                    this.scrollable_data.currentIndex = 0;
                }
                // this.$refs.carousel.setActiveItem(this.scrollable_data.currentIndex);
            }
        }, 2000); // 设置滚动时间间隔，单位为毫秒
        this.initPieChart();
        this.renderLineChart();
        // 获取时间线容器高度
        this.containerHeight = this.$refs.timelineContainer.offsetHeight;
        // 启动循环滚动
        this.startAutoScroll();
    },
    methods: {
        showDrawer() {
            // 点击 el-tab-pane 时显示 el-drawer
            this.status.drawerVisible = true;
        },

        //折线图
        renderLineChart() {
            const timestamps = this.sandbox_data.data.map(item => new Date(item.timestamp * 1000));
            const actions = this.sandbox_data.data.map(item => item.action);
            const chartDom = this.$refs.lineChart;
            const myChart = echarts.init(chartDom);
            const option = {
                tooltip: {
                    trigger: 'axis',
                    axisPointer: {
                        type: 'cross'
                    }
                },
                xAxis: {
                    type: 'time',
                    boundaryGap: false
                },
                yAxis: {
                    type: 'category',
                    data: actions
                },
                series: [{
                    type: 'line',
                    data: timestamps.map((time, index) => [time, index]),
                    symbol: 'circle', // You can change the symbol if needed
                    symbolSize: 8 // You can adjust symbol size as well
                }]
            };
            myChart.setOption(option);
        },
        typeTwoCount() {
            this.cardcount_data.permission_data[0].targetValue = this.permission_data.data.filter(item => item.type === "2").length;
            this.cardcount_data.permission_data[1].targetValue = this.permission_data.data.length;
            this.startAnimation(this.cardcount_data.permission_data[0]);
            this.startAnimation(this.cardcount_data.permission_data[1]);
        },
        Getsfiletypetatus() {
            if (this.vulsandbox_data.data.length > 0) {
                this.status.filetype = 'evil';
            } else if (this.vulsandbox_data.data.length === 0 && this.permission_data.data.some(item => item.type === '2')) {
                this.status.filetype = 'suspicious';
            } else {
                this.status.filetype = 'safe';
            }
        },
        getnetworkdata() {
            // 使用reduce来转换和统计数据
            this.network_data.data = Object.values(this.network_data.detail.reduce((acc, item) => {
                const key = item.destip + '-' + item.destport;
                if (!acc[key]) {
                    acc[key] = {
                        'destip': item.destip,
                        'destport': item.destport,
                        'count': 0,
                        'details': []
                    };
                }
                acc[key].count++;
                // 将统计到的数据添加到details中
                acc[key].details.push(`DestIP: ${item.destip}, DestPort: ${item.destport}, Timestamp: ${this.formatTimestamp(item.timestamp)}\r\n`);
                return acc;
            }, {}));
        },
        //时间戳转换
        formatTimestamp(timestamp) {
            const date = new Date(timestamp * 1000); // 乘以1000将秒数转换为毫秒
            const year = date.getFullYear();
            const month = ('0' + (date.getMonth() + 1)).slice(-2);
            const day = ('0' + date.getDate()).slice(-2);
            const hours = ('0' + date.getHours()).slice(-2);
            const minutes = ('0' + date.getMinutes()).slice(-2);
            const seconds = ('0' + date.getSeconds()).slice(-2);

            return `${year}-${month}-${day} ${hours}:${minutes}:${seconds}`;
        },
        //循环滚动
        handleCarouselChange(val) {
            const len = this.network_data.detail.length;
            // 获取当前索引，并实现循环滚动
            if (val === len) {
                this.$refs.carousel.setActiveItem(0);
            } else if (val === -1) {
                this.$refs.carousel.setActiveItem(len - 1);
            }
        },
        pauseCarousel() {
            this.scrollable_data.isPaused = true;
            // this.$refs.carousel.pause();
        },
        resumeCarousel() {
            if (this.scrollable_data.isPaused) {
                this.scrollable_data.isPaused = false;
                // this.$refs.carousel.resume();
            }
        },
        //内容居中
        rowStyle() {
            return "text-align:center";
        },
        //数字增长动画
        startAnimation(dataObject) {
            const animationDuration = 500; // 动画持续时间，单位毫秒
            const animationInterval = 100; // 动画更新间隔，单位毫秒
            const totalIterations = animationDuration / animationInterval;
            const increment = ((dataObject.targetValue * 10 - dataObject.statValue * 10) / totalIterations) / 10;
            let iteration = 0;
            const animation = setInterval(() => {
                if (iteration < totalIterations) {
                    dataObject.statValue = parseFloat((dataObject.statValue + increment).toFixed());
                    iteration++;
                } else {
                    clearInterval(animation);
                    dataObject.statValue = dataObject.targetValue; // 确保最终值与目标值一致
                }
            }, animationInterval);
        },
        //echarts
        initPieChart() {
            if (this.status.filetype === 'evil') {
                const vulnerabilityCount = {
                    "低危": 0,
                    "中危": 0,
                    "高危": 0
                };
                this.vulsandbox_data.data.forEach(vulnerability => {
                    switch (vulnerability.type) {
                        case "0":
                            vulnerabilityCount["低危"]++;
                            break;
                        case "1":
                            vulnerabilityCount["中危"]++;
                            break;
                        case "2":
                            vulnerabilityCount["高危"]++;
                            break;
                        default:
                            break;
                    }
                });
                const pieChartData = [];
                for (const [type, count] of Object.entries(vulnerabilityCount)) {
                    pieChartData.push({ value: count, name: type });
                }
                const chartDom = this.$refs.pieChart;
                const myChart = echarts.init(chartDom);
                const option = {
                    title: {
                        text: ''
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: '{a} <br/>{b}: {c} ({d}%)'
                    },
                    legend: {
                        orient: 'vertical',
                        left: 10,
                        data: pieChartData.map(item => item.name)
                    },
                    series: [{
                        name: '漏洞危害等级',
                        type: 'pie',
                        radius: ['50%', '70%'],
                        avoidLabelOverlap: false,
                        label: {
                            show: false,
                            position: 'center'
                        },
                        emphasis: {
                            label: {
                                show: true,
                                fontSize: '30',
                                fontWeight: 'bold'
                            }
                        },
                        labelLine: {
                            show: false
                        },
                        data: pieChartData,
                        color: ['#FF9999', '#66CCCC', '#D81414']
                    }]
                };
                myChart.setOption(option);

            } else if (this.status.filetype === 'suspicious') {
                const vulnerabilityCount = {
                    "安全": 0,
                    "可疑": 0,
                    "敏感": 0
                };
                this.permission_data.data.forEach(vulnerability => {
                    switch (vulnerability.type) {
                        case "0":
                            vulnerabilityCount["安全"]++;
                            break;
                        case "1":
                            vulnerabilityCount["可疑"]++;
                            break;
                        case "2":
                            vulnerabilityCount["敏感"]++;
                            break;
                        default:
                            break;
                    }
                });
                const pieChartData = [];
                for (const [type, count] of Object.entries(vulnerabilityCount)) {
                    pieChartData.push({ value: count, name: type });
                }
                const chartDom = this.$refs.pieChart;
                const myChart = echarts.init(chartDom);
                const option = {
                    title: {
                        text: ''
                    },
                    tooltip: {
                        trigger: 'item',
                        formatter: '{a} <br/>{b}: {c} ({d}%)'
                    },
                    legend: {
                        orient: 'vertical',
                        left: 10,
                        data: pieChartData.map(item => item.name)
                    },
                    series: [{
                        name: '权限类别',
                        type: 'pie',
                        radius: ['50%', '70%'],
                        avoidLabelOverlap: false,
                        label: {
                            show: false,
                            position: 'center'
                        },
                        emphasis: {
                            label: {
                                show: true,
                                fontSize: '30',
                                fontWeight: 'bold'
                            }
                        },
                        labelLine: {
                            show: false
                        },
                        data: pieChartData,
                        color: ['#67c23a', '#e6a23c', '#D81414']
                    }]
                };
                myChart.setOption(option);

            }

        },
        // 更新当前可见的时间线数据
        updateVisibleItems() {
            const timelineItems = Math.ceil(this.containerHeight / this.actiontimeline.itemHeight);
            this.actiontimeline.visibleItems = this.sandbox_data.data.slice(this.actiontimeline.currentPosition, this.actiontimeline.currentPosition + timelineItems);

        },

        // 启动自动循环滚动
        startAutoScroll() {
            this.actiontimeline.scrollInterval = setInterval(() => {
                this.actiontimeline.currentPosition++;
                if (this.actiontimeline.currentPosition * this.actiontimeline.itemHeight >= this.sandbox_data.data.length * this.actiontimeline.itemHeight) {
                    this.actiontimeline.currentPosition = 0;
                }

                this.updateVisibleItems();
            }, this.actiontimeline.scrollSpeed);
        }
    },
    beforeDestroy() {
        // 清除滚动定时器
        if (this.actiontimeline.scrollInterval) {
            clearInterval(this.actiontimeline.scrollInterval);
        }
    }
}
</script>
<style>
</style>