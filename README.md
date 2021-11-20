## 饿了么鼠标移入单元格变色

背景颜色实现效果：

![1637412789345](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1637412789345.png)

代码：

```js
::v-deep .el-table .el-table__body tr td:hover{
  background-color: #d4b021 !important;
}
```

边框实现效果：

![1637412881404](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1637412881404.png)

代码：

```js
::v-deep .el-table .el-table__body tr td {
  border: 2px solid transparent !important;
}
::v-deep .el-table .el-table__body tr td:hover {
  border: 2px solid red !important;
}
```

鼠标移入更换整行的背景颜色，实现效果：

![1637413819990](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\1637413819990.png)

代码：

```js
 ::v-deep .el-table--enable-row-hover .el-table__body tr:hover > td {
  background-color: #212e3e !important;
}
```

demo地址：
