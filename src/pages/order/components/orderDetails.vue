<template>
  <div class="detail">
    <div v-if="orderDatalist.orderInfo && orderDatalist.orderInfo.type != 10" class="section">
      <div class="title">收货信息</div>
      <ul class="list">
        <!-- <li class="item">
            <div>用户昵称：</div>
            <div class="value">{{orderDatalist.userInfo.uid?orderDatalist.userInfo.nickname:'游客'}}</div>
        </li> -->
        <li class="item">
          <div>收货人：</div>
          <div class="value">{{ orderDatalist.orderInfo.real_name }}</div>
        </li>
        <li class="item">
          <div>收货电话：</div>
          <div class="value">{{ orderDatalist.orderInfo.user_phone }}</div>
        </li>
        <li class="item">
          <div>收货地址：</div>
          <div class="value">{{ orderDatalist.orderInfo.user_address }}</div>
        </li>
      </ul>
    </div>
    <div class="section">
      <div class="title">用户信息</div>
      <ul class="list">
        <li class="item">
          <div>用户昵称：</div>
          <div class="value">
            {{
              orderDatalist.userInfo.uid
                ? orderDatalist.userInfo.nickname
                : "游客"
            }}
          </div>
        </li>
        <li class="item">
          <div>绑定电话：</div>
          <div class="value">{{ orderDatalist.userInfo.phone }}</div>
        </li>
      </ul>
    </div>
    <div class="section">
      <div class="title">订单信息</div>
      <ul class="list">
        <li class="item">
          <div>创建时间：</div>
          <div class="value">{{ orderDatalist.orderInfo._add_time }}</div>
        </li>
        <li class="item">
          <div>商品总数：</div>
          <div class="value">{{ orderDatalist.orderInfo.total_num }}</div>
        </li>
        <li class="item">
          <div>商品总价：</div>
          <div class="value">￥{{ orderDatalist.orderInfo.total_price }}</div>
        </li>
        <li class="item">
          <div>优惠券金额：</div>
          <div class="value">￥{{ orderDatalist.orderInfo.coupon_price }}</div>
        </li>
        <li class="item">
          <div>积分抵扣：</div>
          <div class="value">
            ￥{{ orderDatalist.orderInfo.deduction_price || 0.0 }}
          </div>
        </li>
        <li
          class="item"
          v-if="parseFloat(orderDatalist.orderInfo.use_integral)"
        >
          <div>使用积分：</div>
          <div class="value">
            {{ parseFloat(orderDatalist.orderInfo.use_integral) }}
          </div>
        </li>
        <li class="item">
          <div>支付邮费：</div>
          <div class="value">￥{{ orderDatalist.orderInfo.pay_postage }}</div>
        </li>
        <li class="item">
          <div>会员商品优惠：</div>
          <div class="value">
            ￥{{ orderDatalist.orderInfo.vip_true_price || 0.0 }}
          </div>
        </li>
        <li v-if="orderDatalist.orderInfo.first_order_price != 0" class="item">
          <div>新人首单优惠：</div>
          <div class="value">
            ￥{{ orderDatalist.orderInfo.first_order_price }}
          </div>
        </li>
        <li
          class="item"
          v-if="
            orderDatalist.orderInfo.shipping_type === 2 &&
              orderDatalist.orderInfo.refund_status === 0 &&
              orderDatalist.orderInfo.paid === 1
          "
        >
          <div>门店名称：</div>
          <div class="value">{{ orderDatalist.orderInfo._store_name }}</div>
        </li>
        <li
          class="item"
          v-if="
            orderDatalist.orderInfo.shipping_type === 2 &&
              orderDatalist.orderInfo.refund_status === 0 &&
              orderDatalist.orderInfo.paid === 1
          "
        >
          <div>核销码：</div>
          <div class="value">{{ orderDatalist.orderInfo.verify_code }}</div>
        </li>
        <li class="item">
          <div>推广人：</div>
          <div class="value">
            {{ orderDatalist.userInfo.spread_name }}/ID:{{
              orderDatalist.userInfo.spread_uid
            }}
          </div>
        </li>
        <li class="item">
          <div>支付时间：</div>
          <div class="value">{{ orderDatalist.orderInfo._pay_time }}</div>
        </li>
        <li class="item">
          <div>支付方式：</div>
          <div class="value">
            {{ orderDatalist.orderInfo._status._payType }}
          </div>
        </li>
        <li class="item" v-if="orderDatalist.orderInfo.store_order_sn">
          <div>原订单号：</div>
          <div class="value">{{ orderDatalist.orderInfo.store_order_sn }}</div>
        </li>
      </ul>
    </div>
    <div
      class="section"
      v-if="orderDatalist.orderInfo.promotions_detail.length"
    >
      <div class="title">活动信息</div>
      <ul class="list">
        <li
          class="item"
          v-for="(item, index) in orderDatalist.orderInfo.promotions_detail"
          :key="index"
        >
          <div>{{ item.title }}：</div>
          <div class="value">￥{{ item.promotions_price }}</div>
        </li>
      </ul>
    </div>
    <div v-if="orderDatalist.orderInfo.custom_form.length" class="section">
      <div class="title">自定义留言</div>
      <ul class="list">
        <li
          v-for="(item, index) in orderDatalist.orderInfo.custom_form"
          :key="index"
          class="item"
          v-show="(item.value && ['uploadPicture','dateranges'].indexOf(item.name) == -1) || (item.value.length && ['uploadPicture','dateranges'].indexOf(item.name) != -1)"
        >
          <div class="txtVal">{{ item.titleConfig.value }}：</div>
          <div v-if="item.name === 'dateranges'" class="value">{{ item.value[0]+'/'+item.value[1] }}</div>
          <div v-else-if="item.name === 'uploadPicture'" class="value">
            <div v-for="(img, i) in item.value" :key="i" class="image" v-viewer>
              <img v-lazy="img" />
            </div>
          </div>
          <div v-else class="value">{{ item.value || "-" }}</div>
        </li>
      </ul>
    </div>
    <div class="section" v-if="orderDatalist.orderInfo.mark">
      <div class="title">买家留言</div>
      <ul class="list">
        <li class="item mark">
          <div>备注：</div>
          <div class="value">{{ orderDatalist.orderInfo.mark || "-" }}</div>
        </li>
      </ul>
    </div>

    <div class="section" v-if="orderDatalist.orderInfo.remark">
      <div class="title">订单备注</div>
      <ul class="list">
        <li class="item mark">
          <div>备注：</div>
          <div class="value">{{ orderDatalist.orderInfo.remark || "-" }}</div>
        </li>
      </ul>
    </div>
    <div
      class="section"
      v-if="orderDatalist.orderInfo.delivery_type === 'express'"
    >
      <div class="title">物流信息</div>
      <ul class="list">
        <li class="item">
          <div>快递公司：</div>
          <div class="value">
            {{ orderDatalist.orderInfo.delivery_name || "-" }}
          </div>
        </li>
        <li class="item">
          <div>快递单号：</div>
          <div class="value">
            {{ orderDatalist.orderInfo.delivery_id }}
          </div>
        </li>
      </ul>
    </div>
    <div
      class="section"
      v-if="orderDatalist.orderInfo.delivery_type === 'send'"
    >
      <div class="title">配送信息</div>
      <ul class="list">
        <li class="item">
          <div>送货人姓名：</div>
          <div class="value">
            {{ orderDatalist.orderInfo.delivery_name || "-" }}
          </div>
        </li>
        <li class="item">
          <div>送货人电话：</div>
          <div class="value">{{ orderDatalist.orderInfo.delivery_id }}</div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  name: "orderDetails",
  props: {
    orderDatalist: {
      type: Object,
    },
  },
  methods: {},
};
</script>

<style scoped lang="stylus">
.detail .section .list .item .txtVal{
  max-width 100px;
}
ul, li {
  list-style: none;
}

.detail {
  .section {
    border-bottom: 1px dashed #EEEEEE;
    padding: 25px 0;

    &:first-child {
      padding-top: 0;
    }

    .title {
      font-size: 16px;
      color: #303133;
      margin-bottom: 16px;
      font-weight: 600;
    }

    .list {
      display: flex;
      flex-wrap: wrap;
      margin: 0 -16px -16px 0;
      color: #606266;
      font-size: 14px;
      font-weight: 400;

      .item {
        flex: 0 0 calc((100% - 48px) / 3);
        display: flex;
        margin: 0 16px 16px 0;
      }
      .item.mark{
        flex: 1;
      }
      .value {
        flex: 1;
        word-break: break-all;

        .image {
          display: inline-block;
          width: 40px;
          height: 40px;
          margin: 0 12px 12px 0;
          vertical-align: middle;
        }

        img {
          width: 100%;
          height: 100%;
        }
      }
    }
  }
}
</style>
