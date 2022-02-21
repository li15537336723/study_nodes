# 第二天学习

### 分页查询功能

首先定义两个变量 `pageSize` 确定每一页放几个内容，`currentPage` 确定当前是第几页

在歌手总框架中设置一个 `:data` 属性，通过vue中特有的计算属性来确定页数以及内容 

```javascript
computed:{
    data(){                             // 计算开始位置                         就算结束位置
        return this.tableData.slice((this.currentPage - 1) * this.pageSize, this.currentPage * this.pageSize)
    }
},
```





































































































