<template>
  <el-popover v-model="showPopover"
              placement="bottom"
              width="700"
              :disabled="disabled"
              popper-class="no-padding-popover"
              trigger="click">
    <crm-relative ref="crmrelative"
                  :crm-type="item.data.form_type"
                  :action="relationAction"
                  v-if="!disabled&&showSelectView"
                  @close="showPopover=false"
                  @changeCheckout="checkInfos"></crm-relative>
    <flexbox slot="reference"
             wrap="wrap"
             :class="[disabled ? 'is_disabled' : 'is_valid']"
             class="user-container"
             @click.native="contentClick">
      <div v-for="(aitem, aindex) in dataValue"
           :key="aindex"
           @click.stop="deleteinfo(aindex)"
           class="user-item">{{item.data.form_type==='contract' ? aitem.num : aitem.name}}
        <i class="delete-icon el-icon-close"></i>
      </div>
      <div class="add-item"
           v-if="dataValue.length == 0">+添加</div>
    </flexbox>
  </el-popover>
</template>
<script type="text/javascript">
import CrmRelative from './CrmRelative'
import arrayMixin from './arrayMixin'

export default {
  name: 'crm-relative-cell', // 相关模块CRMCell
  components: {
    CrmRelative
  },
  mixins: [arrayMixin],
  computed: {
    // 如果有相关ID  展示相关效果 例如客户下的商机和合同
    isRelationShow() {
      return this.item && this.item.data && this.item.data.relation_id
    }
  },
  watch: {
    relation: function(val) {
      if (val.form_type) {
        this.relationAction = { type: 'condition', data: val }
      } else {
        this.relationAction = { type: 'default' }
      }
    }
  },
  data() {
    return {
      showPopover: false, // 展示popover
      showSelectView: false, // 内容
      radio: true, // 是否单选
      relationAction: { type: 'default' }
    }
  },
  props: {
    relation: {
      // 相关ID
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  mounted() {
    if (this.relation && this.relation.form_type) {
      this.relationAction = { type: 'condition', data: this.relation }
    } else {
      this.relationAction = { type: 'default' }
    }
  },
  methods: {
    /** 选中 */
    checkInfos(data) {
      this.dataValue = data.data ? data.data : []
      this.$emit('value-change', {
        index: this.index,
        value: data.data
      })
    },
    /** 删除 */
    deleteinfo(index) {
      if (this.disabled) return
      if (this.radio && this.$refs.crmrelative) {
        // 如果单选告知删除
        this.$refs.crmrelative.clearAll()
      }
      if (this.dataValue.length === 1) {
        this.dataValue = []
      } else {
        this.dataValue.splice(index, 1)
      }

      this.$emit('value-change', {
        index: this.index,
        value: this.dataValue
      })
    },
    contentClick() {
      this.showSelectView = true
    }
  }
}
</script>
<style lang="scss" scoped>
.user-container {
  min-height: 34px;
  position: relative;
  border-radius: 3px;
  font-size: 12px;
  border: 1px solid #ddd;
  color: #333333;
  padding: 5px;
  line-height: 15px;
  .user-item {
    padding: 5px;
    background-color: #e2ebf9;
    border-radius: 3px;
    margin: 3px;
    cursor: pointer;
  }
  .add-item {
    padding: 5px;
    color: #3e84e9;
    cursor: pointer;
  }
  .delete-icon {
    color: #999;
    cursor: pointer;
  }
}

.user-container.is_disabled {
  background-color: #f5f7fa;
  border-color: #e4e7ed;
  cursor: not-allowed;
  .user-item {
    background-color: #f0f4f8ea;
    color: #c0c4cc;
    cursor: not-allowed;
  }
  .delete-icon {
    color: #c0c4cc;
    cursor: not-allowed;
  }
  .add-item {
    color: #c0c4cc;
    cursor: not-allowed;
  }
}

.user-container.is_valid:hover {
  border-color: #c0c4cc;
}
</style>
