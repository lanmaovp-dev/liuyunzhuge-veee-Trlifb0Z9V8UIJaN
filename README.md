

> “ 知行合一 ” —— 王阳明


在开发项目中，我们时常会用到表格，许多需求可能会要求自定义特定的行或列。
接下来，我们将探讨在实际开发中如何应对这一挑战。


本文案例采用的技术：




| 名称 | 版本 |
| --- | --- |
| Vue3 | ^3\.5\.12 |
| element\-plus | ^2\.8\.8 |


# 知识点


我们先来复习下2个知识点，来自[element\-plus文档 table](https://github.com)：


## 1、自定义表头


通过设置 slot 来自定义表头。（只贴出重点代码）



```
<el-table :data="filterTableData" style="width: 100%">
    <el-table-column label="Date" prop="date" />
    <el-table-column label="Name" prop="name" />
    <el-table-column align="right">
      <template #header>
        <el-input v-model="search" size="small" placeholder="Type to search" />
      template>
      <template #default="scope">
        <el-button size="small" @click="handleEdit(scope.$index, scope.row)">
          Edit
        el-button>
        <el-button
          size="small"
          type="danger"
          @click="handleDelete(scope.$index, scope.row)"
        >
          Delete
        el-button>
      template>
    el-table-column>
  el-table>

```

其中可以看出，通过  自定义表头， 自定义内容。


## 2、合并行或列


多行或多列共用一个数据时，可以合并行或列。
通过给 table 传入span\-method方法可以实现合并行或列， 方法的参数是一个对象，里面包含当前行 row、当前列 column、当前行号 rowIndex、当前列号 columnIndex 四个属性。 该函数可以返回一个包含两个元素的数组，第一个元素代表 rowspan，第二个元素代表 colspan。 也可以返回一个键名为 rowspan 和 colspan 的对象。



```
 <el-table
      :data="tableData"
      :span-method="arraySpanMethod"
      border
      style="width: 100%"
    >
      <el-table-column prop="id" label="ID" width="180" />
      <el-table-column prop="name" label="Name" />
      <el-table-column prop="amount1" sortable label="Amount 1" />
      <el-table-column prop="amount2" sortable label="Amount 2" />
      <el-table-column prop="amount3" sortable label="Amount 3" />
    el-table>

<script lang="ts" setup>
// 省略其他代码...
const arraySpanMethod = ({
  row,
  column,
  rowIndex,
  columnIndex,
}: SpanMethodProps) => {
  if (rowIndex % 2 === 0) {
    if (columnIndex === 0) {
      return [1, 2]
    } else if (columnIndex === 1) {
      return [0, 0]
    }
  }
}
script>

```

# 实战开发


假设一个需求：在最后一行新增一条自定义的行数据。
结合上述2个知识点，我们可以进行改进：



```
<template>
  <el-table :data="tableData" :span-method="arraySpanMethod" border style="width: 500px">
    <el-table-column prop="id" label="ID" width="100">
      <template #default="scope">
        <span v-if="scope.$index === tableData.length - 1">
          <span>是否确认信息：span>
          <el-radio-group v-model="scope.row.confirmFlag">
            <el-radio :value="true">确认el-radio>
            <el-radio :value="false">未确认el-radio>
          el-radio-group>
        span>
        <template v-else>{{ scope.row.id }}template>
      template>
    el-table-column>
    <el-table-column prop="name" label="姓名" />
    <el-table-column prop="age" label="年龄" />
  el-table>
template>

<script setup lang="ts">
import type { TableColumnCtx } from 'element-plus'

interface User {
  id?: string
  name?: string
  age?: number
  confirmFlag?: boolean
}

interface SpanMethodProps {
  row: User
  column: TableColumnCtx<User>
  rowIndex: number
  columnIndex: number
}

const arraySpanMethod = ({ row, column, rowIndex, columnIndex }: SpanMethodProps) => {
   // 最后一行
  if (rowIndex === tableData.length - 1) {
  // [1,3] 占一行三列
    return [1, 3]
  }
}

const tableData: User[] = [
  {
    id: '1',
    name: 'Tom1',
    age: 20,
  },
  {
    id: '2',
    name: 'Tom2',
    age: 30,
  },
  {
    id: '3',
    name: 'Tom3',
    age: 40,
  },
  // 新增一条自定义的数据
  {
    confirmFlag: true,
  },
]
script>

<style scoped>style>

```

![](https://img2024.cnblogs.com/blog/3564427/202412/3564427-20241201191734935-119658866.png)


# 结语


根据文档中所提供的2个知识点，可以结合进行其他自定义操作。
还有其他好玩的操作，小伙伴可以留言哈\~


 本博客参考[豆荚加速器官网](https://baitenghuo.com)。转载请注明出处！
