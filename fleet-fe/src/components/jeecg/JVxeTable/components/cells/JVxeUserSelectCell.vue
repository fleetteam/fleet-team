<template>
  <div>
    <a-input
      v-show="!userIds"
      @click="openSelect"
      placeholder="请选择用户"
      v-model="userNames"
      readOnly
      class="jvxe-select-input"
      :disabled="componentDisabled">
      <a-icon slot="prefix" type="user" title="用户选择控件"/>
    </a-input>
    <j-select-user-by-dep-modal
      ref="selectModal"
      :modal-width="modalWidth"
      :multi="multi"
      :user-ids="userIds"
      @ok="selectOK"
      @initComp="initComp"/>
    <span style="display: inline-block;height:100%;padding-left:14px" v-if="userIds" >
      <span @click="openSelect" style="display: inline-block;vertical-align: middle">{{ userNames }}</span>
      <a-icon style="margin-left:5px;vertical-align: middle" type="close-circle" @click="handleEmpty" title="清空"/>
    </span>
  </div>

<!--
<j-select-user-by-dep
    v-bind="custProps"
    @change="handleChange"
    :trigger-change="true">
</j-select-user-by-dep>
-->
</template>

<script>
  import JVxeCellMixins, { dispatchEvent } from '@/components/jeecg/JVxeTable/mixins/JVxeCellMixins'
  import JSelectUserByDepModal from '@/components/jeecgbiz/modal/JSelectUserByDepModal'

  export default {
    name: 'JVxeUserSelectCell',
    mixins: [JVxeCellMixins],
    components: { JSelectUserByDepModal },
    data() {
      return {
        userIds:'',
        userNames:'',
        innerUserValue: '',
        selectedOptions: []
      }
    },
    computed: {
      custProps() {
        const {userIds, originColumn: col, caseId, cellProps} = this
        return {
          ...cellProps,
          value: userIds,
          field: col.field || col.key,
          groupId: caseId,
          class: 'jvxe-select'
        }
      },
      componentDisabled(){
        console.log('333',this.cellProps)
        if(this.cellProps.disabled==true){
          return true
        }
        return false
      },
      modalWidth(){
        if(this.cellProps.modalWidth){
          return this.cellProps.modalWidth
        }else{
          return 1250
        }
      },
      multi(){
        if(this.cellProps.multi==false){
          return false
        }else{
          return true
        }
      }
    },
    watch: {
      innerValue: {
        immediate: true,
        handler(val) {
          if (val == null || val === '') {
            this.userIds = ''
          } else {
            this.userIds = val
          }
        }
      }
    },
    methods: {
      openSelect() {
        this.$refs.selectModal.showModal()
      },
      selectOK(rows, idstr) {
        console.log("当前选中用户", rows)
        console.log("当前选中用户ID", idstr)
        if (!rows) {
          this.userNames = ''
          this.userIds = ''
        } else {
          let temp = ''
          for (let item of rows) {
            temp += ',' + item.realname
          }
          this.userNames = temp.substring(1)
          this.userIds = idstr
        }
        this.handleChangeCommon(this.userIds)
      },
      handleEmpty(){
        this.selectOK('')
      },
      initComp(userNames) {
        this.userNames = userNames
      },
    },
    enhanced: {
      switches: {
        visible: true
      },
      translate: {
        enabled: false
      }
    }
  }
</script>

<style scoped>
  /deep/ .jvxe-select-input .ant-input {
    border: none !important;
  }
</style>