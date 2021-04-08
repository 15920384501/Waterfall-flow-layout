<template>
  <div class="flow-wrapper">
    <div
      class="flow-col"
      v-for="(item, index) in showData"
      :key="index"
      :ref="'flowCol' + index"
    >
      <div class="flow-row" v-for="(v, i) in item" :key="i">
        <!-- 默认插槽 内容自定义 -->
        <slot :item="v"> </slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    // 列数
    itemColumn: {
      type: Number,
      default: 2,
    },

    // 数据来源
    flowData: {
      type: Array,
      default: () => [],
    },
  },
  watch: {
    flowData: {
      deep: true,
      handler(newV) {
        // 提取为渲染数据，避免重复操作整个数据
        this.loadData(newV.slice(this.loadTotal));
      },
    },
  },
  data: () => ({
    showData: [], // 用于渲染的数组
    loadTotal: 0, // 已经渲染数据总数
  }),
  methods: {
    // 处理数据，生成渲染数组
    loadData(arr) {
      for (let i = 0; i < arr.length; i++) {
        // 预加载图片
        let newImage = new Image();
        newImage.src = arr[i].src;
        newImage.onload = () => {
          if (this.showData.length < this.itemColumn) {
            // 生成列数组
            let colArr = [];
            colArr.push(arr[i]);
            this.showData.push(colArr);
          } else {
            let minIndex = this.findMinHeightColIndex(this.itemColumn);

            // 将数据插入最小高度的列
            this.showData[minIndex].push(arr[i]);
          }
        };
      }

      this.loadTotal = this.flowData.length;
    },

    findMinHeightColIndex(num) {
      let minIndex = 0;

      //根据动态绑定获取各列高度
      let minHeight = this.$refs["flowCol0"][0].offsetHeight;
      for (let k = 0; k < num; k++) {
        if (minHeight > this.$refs["flowCol" + k][0].offsetHeight) {
          minHeight = this.$refs["flowCol" + k][0].offsetHeight;
          minIndex = k;
        }
      }

      return minIndex;
    },
  },
};
</script>

<style lang="scss" scoped>
.flow-wrapper {
  width: 100%;
  display: flex;
  align-items: flex-start;

  .flow-col {
    flex: 1;
    margin-right: 10px;

    &:last-child {
      margin: 0;
    }

    .flow-row {
      border: 1px solid rgba(7, 17, 27, 0.1);
      border-radius: 6px;
      overflow: hidden;
      margin-bottom: 10px;
      background-color: #fff;

      &:last-child {
        margin: 0;
      }
    }
  }
}
</style>
