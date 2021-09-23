<template>
  <div class="drawer-form-container">
    <el-drawer
      :title="formConfig.title"
      :size="500"
      :visible.sync="drawer"
      :before-close="close"
      :direction="direction"
    >
      <el-form
        :inline="true"
        :model="value"
        label-position="right"
        :label-width="formConfig.labelWidth"
        :rules="rules"
        ref="ruleForm"
        size="small"
      >
        <slot name="formItem" />
        <el-form-item
          v-for="(item, index) in formConfig.formItemList"
          :key="index"
          :label="item.label"
          :prop="item.prop"
          v-if="!item.hidden"
        >
          <el-input
            v-if="item.type == 'input'"
            v-model="value[item.prop]"
            :type="item.sType"
            :disabled="item.disabled"
            :clearable="true"
            :placeholder="item.placeholder"
            :maxlength="item.sType == 'textarea' && 100"
            show-word-limit
            :show-password ="item.sType == 'password'"
          ></el-input>
          <el-select
            :clearable="item.clearable"
            v-else-if="item.type == 'select'"
            v-model="value[item.prop]"
            :disabled="item.disabled"
            :placeholder="item.placeholder"
            :multiple="item.isMultiple"
            :collapse-tags="item.isCollapse"
            @change="item.prop == 'deptId' ? changeStyle($event,'organ') : ()=>{}"
          >
            <el-option
              v-for="(optItem) in item.optList || formConfig.optList"
              :key="optItem[item.optValue]"
              :label="optItem[item.optLabel]"
              :value="optItem[item.optValue]"
            ></el-option>
          </el-select>
          <el-radio-group v-else-if="item.type == 'radio'" v-model="value[item.prop]">
            <el-radio v-for="(optItem) in item.optList" :key="optItem.value" :label="optItem.value">{{optItem.label}}</el-radio>
          </el-radio-group>
          <el-cascader 
            v-else-if="item.type == 'cascader'" 
            :class="formConfig.type == 'edit' ? 'cascader' : 'init-cascader' " :placeholder="item.placeholder" ref="area" 
            :props="item.props" v-model="value[item.prop]" 
            @change="changeStyle($event,'cascader')">
          </el-cascader>
          <el-date-picker
            :value-format="item.dateFormate"
            v-else
            v-model="value[item.prop]"
            :type="item.type"
            :disabled="item.disabled"
            :clearable="true"
            :placeholder="item.placeholder"
          ></el-date-picker>
        </el-form-item>
        <div class="searchBtn">
          <el-button
            v-for="(item, index) in formConfig.operate"
            :key="index"
            size="small"
            :type="item.type"
            :icon="item.icon"
            @click="item.handleClick('ruleForm')"
            >{{ item.name }}
          </el-button>
          <slot name="operate"></slot>
        </div>
      </el-form>
    </el-drawer>
  </div>
</template>

<script>
export default {
  name: "Form",
  props: {
    formConfig: {
      type: Object,
      required: true,
    },
    value: {
      type: Object,
      required: true,
    },
    rules: {
      type: Object,
    },
  },
  data() {
    return {
      drawer: false,
      direction: "rtl",
    };
  },
  mounted() {},
  // watch: {
  //   "formConfig.optList"(newV) {
  //     console.log(newV);
  //   },
  // },
  methods: {
    open() {
      this.drawer = true;
    },
    close() {
      const operateValue = this.$parent.operateValue
      this.drawer = false;
      this.$refs.ruleForm.resetFields();
      for(let prop in operateValue) {
        operateValue[prop] = ''
      }
    },

    validate() {
      this.$refs.ruleForm.validate((valid) => {
        if (valid) {
          this.$emit("input");
        }
      });
    },

    changeStyle(val,type) {
      let nodeData
      if(type === 'cascader') {
        nodeData = this.$refs.area[0].getCheckedNodes()[0].data
      }
      this.$emit("changeStyle",{type,val,nodeData:nodeData})
    }

    // setDefaultValue() {
    //   const formData = { ...this.value };
    //   // 设置默认值
    //   this.formConfig.formItemList.forEach(({ key, value }) => {
    //     console.log(key,value)
    //     if (formData[key] === undefined || formData[key] === null) {
    //       formData[key] = value;
    //     }
    //   });
    //   this.$emit("input", formData);
    // },
  },
};
</script>
<style lang='scss' scoped>



</style>
