# LSortByDrag

## example
```
    <LSortByDrag
        :drag="true"
        width="400"
        item-width="80"
        item-height="80"
        :data="list"
        @sort="onSort"
      >
      
        <template v-slot="{ scope }">
      
         ...你的item内容
        
        </template>
        
    </LSortByDrag>
```

## 其他说明
当鼠标移除你设置的范围，就视为鼠标松开

## 参数说明

|参数| 说明      | 类型      | 默认值   |
|---|---------|---------|-------|
|drag| 是否允许拖拽  | Boolean | false |
|data| 需要排列的列表 | Array   | [ ]   |
|itemWidth| 单项的宽    | Number  | 60    |
|itemHeight| 单项的高    | Number  | 60    |
|width| 整个区域的宽  | String  | 240px |


## 事件说明

| 事件   | 说明     | 回调            |
|------|--------|---------------|
| sort | 排序之后触发 | list(排序之后的数组) |
