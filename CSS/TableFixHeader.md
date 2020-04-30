# 表格固定表头

这里只探讨纯css实现的方式

## 思路

- 表头和内容拆分为两个表格

## 代码实操

```html
<div class="container">
    <div class="twrap">
        <table>
            <thead>
                xxxx
            </thead>
        </table>
        <div class="tbody">
            <table>
                <tbody>
                    xxx
                </tbody>
            </table>
        </div>
    </div>
</div>
```

```css
.container {
    width: 500px;
    overflow-x: scroll;

    .twrap {
        width: 700px;
        
        table {
            width: 100%;
        }
    
        .tbody {
            height: 200px;
            overflow: scroll;
        }
    }    
}

```