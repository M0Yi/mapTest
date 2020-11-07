<template>
    <view>
        <view
            style="
        position: fixed;
        z-index: 1;
        bottom: 0;
        right: 0;"
        >
            <image @click="location" src="../../static/location.png" style="width: 40px;height: 40px;"></image>
        </view>
        <map
            style="width: 100%; height: 100vh;"
            :controls="map.controls"
            :scale="map.scale"
            :latitude="map.latitude"
            :longitude="map.longitude"
            :markers="covers"
            @callouttap="de"
            @markertap="de"
        ></map>
    </view>
</template>

<script>
// location.png
export default {
    data() {
        return {
            map: {
                latitude: 39.909,
                longitude: 116.39742,
                scale: 16,
                controls: []
            },
            covers: [
                {
                    latitude: 39.909,
                    longitude: 116.39742,
                    position: {
                        bottom: 0
                    }
                }
            ],
            list: []
        };
    },
    onLoad() {
        this.location();
    },

    methods: {
        location() {
            console.log('开始获取地址');
            uni.getLocation({
                success: res => {
                    // console.log('地址获取成功,切换到目标点nvue', res);
                    this.map.latitude = res.latitude;
                    this.map.longitude = res.longitude;
                    uni.request({
                        data: {
                            latitude: res.latitude,
                            longitude: res.longitude
                        },
                        method: 'POST',
                        url: 'https://api.mymoyi.cn/api/moyicosmic/test/testList',
                        success: res => {
                            res = res.data.data;
                            uni.showToast({
                                title: '成功获取' + res.list.length + '条数据,正在加载',
                                icon: 'none'
                            });
                            var arr = [];
                            for (var i = 0; i < res.list.length; i++) {
                                this.list = res.list;
                                arr.push({
                                    id: i,
                                    callout: {
                                        content: res.list[i].text.substr(0, 10),
                                        color: '#000',
                                        textAlign: 'center',
                                        display: 'ALWAYS'
                                    },
                                    latitude: res.list[i].latitude,
                                    longitude: res.list[i].longitude,
                                    iconPath: res.list[i].avatar,
                                    width: 20,
                                    height: 20
                                });
                            }
                            this.covers = arr;
                        }
                    });
                },
                fail: () => {
                    console.log('地理位置获取失败');
                    uni.showToast({
                        title: '地理位置获取失败',
                        icon: 'none'
                    });
                }
            });
        },
        de(i) {
            let a = this.list[i.detail.markerId];
            uni.showModal({
                title: '用户：' + a.nickname,
                content: a.text
            });
        }
    }
};
</script>

<style></style>
