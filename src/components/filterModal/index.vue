<template>
  <div class="content">
    <div class="filter-box">
      <div class="box" v-show="orderType === 'verify' && item.type !== 'type' || orderType === 'order' || orderType === 'table'"
        v-for="(item, index) in filterList" :key="index">
        <div class="title">{{ item.title }}</div>
        <div class="tabs-box">
          <div class="tabs" :class="searchData[item.type] === tab.type ? 'on' : ''"
            v-show="item.tabs.length && item.type !== 'staff_id'" v-for="(tab, indexs) in item.tabs" :key="indexs"
            @click="selectFilter(item, indexs)">
            <span>{{ tab.name }}</span>
          </div>
          <div class="tabs-time" v-if="item.type === 'time'">
            <DatePicker v-model="startTime" format="yyyy/MM/dd" type="date" placeholder="开始时间" style="width: 200px" :clearable="true"
              @on-change="changeStartTime" @on-open-change="timeStatus"></DatePicker>
            <span class="bl">~</span>
            <DatePicker v-model="endTime" format="yyyy/MM/dd" type="date" placeholder="结束时间" style="width: 200px" :clearable="true"
              @on-change="changeEndTime" @on-open-change="timeStatus"></DatePicker>
          </div>
          <div class="tabs-box" v-if="item.type === 'staff_id'">
            <div class="tabs" :class="searchData[item.type] === as.id ? 'on' : ''" v-for="(as) in dataList" :key="as.id"
              @click="selectFilter(item, as.id)">
              {{ as.staff_name }}
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="search-btn">
      <div class="btn reset" @click="init">重置</div>
      <div class="btn suc" @click="search">确定</div>
    </div>
  </div>

</template>

<script>
import {
  cashierList
} from "@/api/staff"

export default {
  name: "index",
  props: {
    orderType: {
      type: String,
      default: 'order'
    }
  },
  data() {
    return {
      startTime: '',
      endTime: '',
      filterList: [{
        title: '订单类型',
        type: 'type',
        tabs: [{
          name: '全部',
          type: ''
        }, {
          name: '核销订单',
          type: 5
        }, {
          name: '收银台订单',
          type: 6
        }, {
          name: '配送订单',
          type: 7
        }]
      }, {
        title: '订单状态',
        type: 'status',
        tabs: [{
          name: '全部',
          type: '',
        }, {
          name: '未支付',
          type: 0
        }, {
          name: '待评价',
          type: 3
        }, {
          name: '已完成',
          type: 4
        }, {
          name: '已删除',
          type: -4
        }]
      }, {
        title: '创建时间',
        type: 'time',
        tabs: [{
          name: '全部',
          type: ''
        }, {
          name: '今天',
          type: 'today'
        }, {
          name: '昨天',
          type: 'yesterday'
        }, {
          name: '近七天',
          type: 'lately7'
        }, {
          name: '近三十天',
          type: 'lately30'
        }, {
          name: '本月',
          type: 'month'
        }, {
          name: '本年',
          type: 'year'
        }]
      }, {
        title: '店员',
        type: 'staff_id',
        tabs: []
      }],
      dataList: [],
      searchData: {
        type: '',
        status: '',
        time: '',
        staff_id: 0,
      },
    }
  },
  created() {
    this.cashierList()
    if (this.orderType != 'table') {
      return;
    }
    let filterList = [];
    for (let i = 0; i < this.filterList.length; i++) {
      if (this.filterList[i].type == 'type') {
        continue;
      }
      if (this.filterList[i].type == 'status') {
        let tabs = [];
        for (let j = 0; j < this.filterList[i].tabs.length; j++) {
          if (this.filterList[i].tabs[j].type == -4) {
            continue;
          }
          tabs.push(this.filterList[i].tabs[j]);
        }
        this.filterList[i].tabs = tabs;
      }
      filterList.push(this.filterList[i]);
    }
    this.filterList = filterList;
  },
  methods: {
    init() {
      this.searchData = {
        type: '',
        status: '',
        time: '',
        staff_id: 0,
      }
      this.$nextTick(e => {
        this.startTime = ''
        this.endTime = ''
      })

    },
    changeStartTime(time) {
      this.searchData.time[0] = time
      this.startTime = time
    },
    changeEndTime(time) {
      this.searchData.time[1] = time
      this.endTime = time

    },
    timeStatus(status) {
      if (typeof (this.searchData.time) === 'string')
        this.searchData.time = ['', '']

    },
    cashierList() {
      cashierList().then(res => {
        let all = [{ staff_name: '全部', id: 0 }]
        this.dataList = [...all, ...res.data.staffList]
      })
    },
    selectFilter(item, index) {
      if (item.type == 'staff_id') {
        this.searchData[item.type] = index
      } else {
        this.searchData[item.type] = item.tabs[index].type
      }

    },
    search() {
      this.$emit('search', this.searchData)
    },
  }
}
</script>

<style lang="stylus" scoped>
.filter-box::-webkit-scrollbar {
  display: none;
}

.content {
  display: flex;
  flex-direction: column;
}

.filter-box {
  flex: 1;
  background: #FFFFFF;
  display: flex;
  flex-direction column;
  overflow-x: hidden;

  .box {
    margin: 20px 0;
    padding: 0px 30px 0 30px;

    .title {
      font-weight: 600;
      color: rgba(0, 0, 0, 0.85);
      font-size 16px
    }
  }

  .tabs-box {
    display: flex;
    flex-wrap wrap
    margin-top: 16px;
    margin-bottom: -20px;

    .tabs {
      background: #F2F3F5;
      border-radius: 20px;
      padding: 12px 24px;
      font-size: 15px;
      line-height: 16px;
      margin-right 14px
      margin-bottom: 20px;
      color: #303133;
      cursor pointer;
    }

    .tabs-time {
      .bl {
        padding: 0 10px;
      }

      /deep/ .ivu-input {
        padding-left: 18px;
        height: 40px;
        background: #F2F3F5;
        border-radius: 20px;
        color: #999999;
        line-height: 15px;
        border none

      }

      /deep/ .ivu-input-suffix {
        // display none
        height 38px
      }
      /deep/ .ivu-icon-ios-calendar-outline{
        display none
      }
      /deep/ .ivu-input-suffix i{
        // display none
        line-height 38px
      }

      /deep/ .ivu-date-picker {
        width: 150px !important;
      }
    }

    .on {
      color: #fff;
      background-color #1890FF
    }
  }

}

.search-btn {
  height: 88px;
  padding: 0 24px;
  border-radius: 0px 0px 20px 20px;
  background: #FFFFFF;
  box-shadow: 0px -1px 11px 0px rgba(0,0,0,0.06);
  display: flex;
  align-items center;
  justify-content: space-around;

  .btn {
    flex: 1;
    display flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    border-radius: 25px;
    cursor: pointer;
    font-weight: 500;
    font-size 18px;

    +.btn {
      margin-left: 15px;
    }
  }

  .reset {
    background: #F5F5F5;
    color: #333;
  }

  .suc {
    color: #fff;
    background-color: #1890FF;
  }
}
</style>