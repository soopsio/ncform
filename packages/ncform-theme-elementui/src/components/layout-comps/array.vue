<template>

  <div class="__array-form-item">

    <legend v-if="schema.ui.legend && schema.ui.showLegend" @click="collapse()">
      {{schema.ui.legend}}
      <i v-if="!mergeConfig.disableCollapse" class="el-collapse-item__arrow" :class="{'el-icon-arrow-up': !mergeConfig.collapsed, 'el-icon-arrow-down': mergeConfig.collapsed}"></i>
    </legend>

    <div v-show="!mergeConfig.collapsed" v-for="(dataItem, idx) in schema.value" :key="dataItem.__dataSchema.__id" class="list-item">

      <div class="list-item-label">
        <label>{{dataItem.__dataSchema.ui.label}} {{idx + 1}}</label>

        <!-- 项控制按钮 -->
        <div class="el-button-group">
          <button @click="delItem(idx)" v-if="!mergeConfig.disableDel" type="button" class="el-button el-button--danger el-button--mini"><i class="el-icon-remove"></i></button>
          <button @click="itemUp(idx)" v-show="idx !== 0" v-if="!mergeConfig.disableReorder" type="button" class="el-button el-button--mini"><i class="el-icon-sort-up"></i></button>
          <button @click="itemDown(idx)" v-show="idx !== schema.value.length - 1" v-if="!mergeConfig.disableReorder" type="button" class="el-button el-button--mini"><i class="el-icon-sort-down"></i></button>
        </div>
      </div>

      <!-- array item 是 正常的 object 类型 -->
      <template v-if="isNormalObjSchema(dataItem.__dataSchema)">
        <ncform-object :schema="dataItem.__dataSchema" :form-data="formData" :idx-chain="(idxChain ? idxChain + ',' : '') + idx" :config="dataItem.__dataSchema.ui.widgetConfig" :show-legend="false">

          <template v-for="(fieldSchema, fieldName) in (dataItem.__dataSchema.properties || {__notObjItem: dataItem.__dataSchema})" :slot="fieldName"><!-- 注意：__notObjItem 这个Key为与form-item约定好的值，其它名字不生效 -->
            <slot :name="fieldName" :schema="fieldSchema" :idx="idx"></slot>
          </template>

        </ncform-object>
      </template>

      <!-- array item 是 非正常的 object 类型 以及 其它类型 -->
      <div v-else class="normal-item">
        <slot name="__notObjItem" :schema="dataItem.__dataSchema" :idx="idx"></slot> <!-- 注意：__notObjItem 和 __dataSchema 都是约定好的值，其它名字不生效 -->
      </div>

    </div>

    <!-- 列表控制按钮 -->
    <div v-show="!mergeConfig.collapsed" class="el-button-group" v-if="!mergeConfig.disableAdd || !mergeConfig.disableDel">
      <button @click="addItem()" v-if="!mergeConfig.disableAdd" type="button" class="el-button el-button--mini"><i class="el-icon-circle-plus-outline"></i> {{mergeConfig.addTxt}}</button>
      <button @click="delAllItems()" v-if="!mergeConfig.disableDel" type="button" class="el-button el-button--danger el-button--mini"><i class="el-icon-remove"></i> {{mergeConfig.delAllTxt}}</button>
    </div>

  </div>

</template>

<style lang="scss">
  .__array-form-item {

    // margin-top: 8px;

    & > legend {
      border-left: 6px solid #878D99;
      padding: 6px;
      background-color: #d8dce5;
      color: #5a5e66;
      font-size: 14px;
      margin-bottom: 0px;
      border-radius: 4px 4px 0 0;

      .el-collapse-item__arrow {
        line-height: 22px;
        cursor: pointer;
      }
    }

    .list-item {
      // border: 1px solid #d8dce5;
      margin: 0px 0px 8px;
      .list-item-label {
        padding: 8px 8px 4px 8px;
        label {
          font-size: 14px;
        }
      }
    }
    .normal-item {
      padding: 4px 8px 8px 8px;
    }
  }
</style>

<script>

  import ncformCommon from '@ncform/ncform-common';

  const layoutArrayMixin = ncformCommon.mixins.vue.layoutArrayMixin;

  export default {

    mixins: [layoutArrayMixin],

  }
</script>
