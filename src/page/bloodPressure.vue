<template>
    <div>
        <van-nav-bar
            title="编辑血压"
            left-text="返回"
            left-arrow
            @click-left="onClickLeft"
        />
        <div class="editeBloodPressure">
            <div class="editeHeader">
                <p>请填写血压测量结果</p>
            </div>
            <van-field
                v-model="HighPressure"
                name="高压"
                label="高压"
                placeholder="90~140 mmHg"
                validate-trigger="on"
                @blur="validateHigh"
            />
            <van-field
                v-model="LowPressure"
                name="低压"
                label="低压"
                placeholder="60~90 mmHg"
                @blur="validateLow"
            />
            <van-field
                v-model="HeartRate"
                name="心率"
                label="心率"
                placeholder="请输入心率"
            />
            <van-field
                v-model="CheckTime"
                name="测量时间"
                label="测量时间"
                placeholder="请选择测量时间"
            />
            <van-icon :name="arrow_name" size="15" class="arrowIcon" @click="moreData" ref="arrow" />
            <!-- <van-divider  :style="{ color: 'grey', borderColor: '#1989fa', padding: '0 16px' }"/> -->
            <van-cell-group v-if="showMore">
                <van-field v-model="Weight" 
                    label="体重" 
                    placeholder="请输入体重" 
                />
                <van-field v-model="Note" 
                    label="备注" 
                    placeholder=" " 
                />
            </van-cell-group>
            <van-button round color="#FF8000" @click="submitInfo" style="float:right;margin-right:10px;margin-bottom:5px" v-if="showSubmit">
                提交
            </van-button>
            <van-button round color="#FF8000" @click="changeInfo" style="float:right;margin-right:10px;margin-bottom:5px" v-else>
                修改
            </van-button>
            <div class="clear"></div>
            
        </div>
        <div class="display">

        </div>
    </div>
</template>
<script>
export default{
    data(){
        return{
            showSubmit:true,
            arrow_name:'arrow',
            CheckTime:this.timeFormat(),
            showMore:false,
            HighPressure:'',
            LowPressure:'',
            HeartRate:'',
            Weight:'',
            Note:'',
            ID:'',
            MobilePhone:window.localStorage.getItem("MobilePhone"),
        }
    },
    methods:{
        onClickLeft(){
            // this.$router.push({path:'/toolspage'})
            this.$router.go(-1);
        },
        submitInfo(){
            this.$http({
                method:'post',
                url:'gout/api/bld/create',
                data:{
                    phone:this.MobilePhone,
                    BloodPressure1:this.HighPressure,
                    BloodPressure2:this.LowPressure,
                    HeartRate:this.HeartRate,
                    CheckTime:this.CheckTime,
                    Weight:this.Weight,
                    Other:'',
                }
            })
            .then(res=>{
                console.log(res.data.data.id)
                console.log(res.data.data)
                this.$toast.success("提交成功")
            })
            .catch(error=>{
                alert('error');
            })
        },
        changeInfo(){
            this.$http({
                method:'post',
                url:'gout/api/bld/update',
                data:{
                    ID:this.ID,
                    phone:this.MobilePhone,
                    BloodPressure1:this.HighPressure,
                    BloodPressure2:this.LowPressure,
                    HeartRate:this.HeartRate,
                    Weight:this.Weight,
                    Other:'',
                }
            })
            .then(res=>{
                console.log(res)
                this.$toast.success("修改成功")
            })
            .catch(error=>{
                console.log(error)
            })
        },
        timeFormat() { 
            var today=new Date();
            var h=today.getFullYear();
            var m=today.getMonth()+1;
            var d=today.getDate();
            var hours = today.getHours();
                    var minutes = today.getMinutes();
                    var seconds = today.getSeconds();
            m= m<10?"0"+m:m;   //  这里判断月份是否<10,如果是在月份前面加'0'
            d= d<10?"0"+d:d;        //  这里判断日期是否<10,如果是在日期前面加'0'
            minutes=minutes<10?"0"+minutes:minutes;
            seconds=seconds<10?"0"+seconds:seconds;
            return h+"-"+m+"-"+d+" "+hours+":"+minutes+":"+seconds;
        },
        moreData(){
            // this.$refs.arrow.style.transform="rotate(90deg)";
            this.showMore=!this.showMore;
            if(this.showMore)
                this.arrow_name="arrow-down";
            else
                this.arrow_name="arrow";

        },
        validateHigh(){
            var _this=this;
            var high_pressure=parseInt(this.HighPressure);
            if(high_pressure<90)
                _this.$toast('收缩压偏低');
            if(high_pressure>140)
                _this.$toast("收缩压偏高");
        },
        validateLow(){
            var _this=this;
            var low_pressure=parseInt(this.LowPressure);
            if(low_pressure<60)
                _this.$toast('舒张压偏低');
            if(low_pressure>90)
                _this.$toast("舒张压偏高");
        }

    },
    created(){
        // this.CheckTime=this.$route.query.testTime;
        this.ID=this.$route.query.ID;
        if(this.ID){
            this.CheckTime="";
            this.showSubmit=false;
        }
        this.HighPressure=this.$route.query.highPressure;
        this.LowPressure=this.$route.query.lowPressure;
        this.Weight=this.$route.query.weight;
    }
}
</script>

<style scoped>
.editeBloodPressure{
    width: 95%;
    background-color: white;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
    border-radius: 4px;
    margin: 0  auto;
    margin-top: 5px;
}
.editeBloodPressure p{
    font-family: 'Microsoft YaHei', serif;
    font-size: 20px;
    text-align:center;
    color: dimgrey;
    margin-bottom: 20px;
}
.clear{
    clear: both;
}
.arrowIcon{
    display: block;
    padding-left: 10px;
}
</style>>