<template>
    <div>

    </div>
</template>

<script>
export default {
    name: 'wx',
    data() {
        return {
            timeoutObj: null,
        };
    },
    created() {
        this.initWebSocket();
    },
    destroyed() {
        this.websock.close(); //离开路由之后断开websocket连接
    },
    updated() {
        // 聊天定位到底部
        // setTimeout(function () {
        //     let ele = document.getElementById('chatRecord');
        //     ele.scrollTop = ele.scrollHeight;
        // }, 200);
    },
    mounted() {

    },
    methods: {
        initWebSocket() {
            //初始化weosocket
            let vm = this;
            var baseUrl = '';  // 后端给的初始化weosocket url
            switch (process.env.VUE_APP_CURENV) {
                case 'alpha':
                    baseUrl = "ws://1/"  //  正式
                    break
                case 'production':
                    baseUrl = "ws://2/"   // 测试
                    break
                default:
                    baseUrl = "ws://3/"  //这里是本地的请求url
            }
            console.log('ulr连接' + baseUrl);
            const wsuri = ''
            vm.websock = new WebSocket(wsuri);
            vm.websock.onmessage = vm.websocketonmessage;
            // vm.websock.onopen = vm.websocketonopen;
            vm.websock.onerror = vm.websocketonerror;
            vm.websock.onclose = vm.websocketclose;
        },

        //心跳检测/检测是否还连接
        start() {
            let vm = this;
            vm.timeoutObj && clearInterval(vm.timeoutObj);
            let actions = {
                type: 9, // 发送消息类型
            };
            vm.timeoutObj = setInterval(function () {
                //这里发送一个心跳，后端收到后，返回一个心跳消息，
                //onmessage拿到返回的心跳就说明连接正常
                let a = vm.websock.send(JSON.stringify(actions));
                console.log('ping!');
                console.log(a);
            }, 60000);
        },
        //连接建立失败重连
        websocketonerror() {
            this.initWebSocket();
        },
        //数据接收
        websocketonmessage(e) {
            let vm = this;
            vm.start(); // 这边可以每一分钟通知后端防止weosocket断线
            const redata = JSON.parse(e.data); // 后端返回数据
        },
        //数据发送
        websocketonopen(key, status) {
            let vm = this;
            let actions = {
                // 发送传参
            }
            vm.websocketsend(JSON.stringify(actions));
        },
        websocketsend(Data) {
            this.$message({
                message: '消息发送成功！',
                type: 'success'
            });
            this.websock.send(Data);
            // 发送后清除，防止冲突
        },
        // 关闭、断开连接
        websocketclose(e) {
            console.log('断开连接', e);
        },
        // 判断图片类型
        matchType(fileName) {
            // 后缀获取
            var suffix = '';
            // 获取类型结果
            var result = '';
            try {
                var flieArr = fileName.split('.');
                suffix = flieArr[flieArr.length - 1];
            } catch (err) {
                suffix = '';
            }
            // fileName无后缀返回 false
            if (!suffix) {
                result = false;
                return result;
            }
            // 图片格式
            var imglist = ['png', 'jpg', 'jpeg', 'bmp', 'gif'];
            // 进行图片匹配
            result = imglist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'image';
                return result;
            }
            // 匹配txt
            var txtlist = ['txt'];
            result = txtlist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'txt';
                return result;
            }
            // 匹配 excel
            var excelist = ['xls', 'xlsx'];
            result = excelist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'excel';
                return result;
            }
            // 匹配 word
            var wordlist = ['doc', 'docx'];
            result = wordlist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'word';
                return result;
            }
            // 匹配 pdf
            var pdflist = ['pdf'];
            result = pdflist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'pdf';
                return result;
            }
            // 匹配 ppt
            var pptlist = ['ppt'];
            result = pptlist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'ppt';
                return result;
            }
            // 匹配 视频
            var videolist = ['mp4', 'm2v', 'mkv'];
            result = videolist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'video';
                return result;
            }
            // 匹配 音频
            var radiolist = ['mp3', 'wav', 'wmv'];
            result = radiolist.some(function (item) {
                return item == suffix;
            });
            if (result) {
                result = 'radio';
                return result;
            }
            // 其他 文件类型
            result = 'other';
            return result;
        }
    }
};
</script>

<style lang="scss" scoped>

</style>



