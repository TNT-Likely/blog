# 水平垂直居中

- 绝对定位+自动margin
  ```
  .child {
    position: absloute;
    left: 0;
    right:0;
    top: 0;
    bottom: 0;
    margin: auto;
  }
  ```
- 绝对定位+负margin(仅适用固定宽高)
  ```
  .child {
    position: absloute;
    top: 50%;
    left: 50%;
    margin-top: - 1/2 * width;
  }
  ```
- 绝对定位+transform
```
.child {
  position: absloute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```
- flex布局
```
.parent {
  display: flex;
  align-items: center;
  justify-content: center;
}
```
- inline-block的vertical-align
```
.parent {
  text-align:center;
}
.parent:after {
  content: '';
  display: inline-block;
  vertical-align: middle;
  height: 100%;
}
.child {
  display: inline-block;
  vertical-align: center;
}
```
- table-cell
```
.parent {
  display: tabel-cell;
  text-align: center;
  vertical-align: center;
}
.child {
  display: inline-block;
}
```

***前面几种是开发种常见的方式，当然了还会有其他的奇技淫巧的使用方式...***