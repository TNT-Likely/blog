# parseInt的具体实现

```
function(string, [radix]) {}
```

- 如果string不为字符串类型, 则先将string转化为字符串类型
- string会忽略前后的空白
- 依次解析字符, 如果字符不是指定基数中的字符( 例如:2进制中的3、 10进制中的'f' )则停止解析( 首字符为'+'或'-'时除外 ), 返回已经解析好的整数
- 如果无法解析为整数, 则返回NaN
- radix 默认值不为10, 在radix为undefined、0或者没有指定时, 做以下处理
  - 如果string以'0X'或'0x'开头时, 基数为16
  - 如果string以'0'开头时, 基数为8或者10(每个浏览器的具体实现不同, es5规定此时为10)
  - 如果string以其他任何字符开头时, 基数为10
- radix的范围为2-36

## 不考虑进制radix

### 代码实现

```
const _parseInt = (str, radix) => {
  if (typeof str !== 'string') str = String(str)
  str = str.trim()
  const regex = /^(?<fuhao>[\+|\-]*)(?<num>\d+)/
  if (!regex.test(str)) return NaN
  const groups = str.match(regex).groups
  radix = 10
  const arr = groups.num.split('')
  const len = arr.length
  let result = 0
  for(let i = 0; i < len; i++) {
    const num = arr[i] * Math.pow(10, len - i - 1)
    if (isNaN(num)) break
    else result += num
  }
  return result * (groups.fuhao === '-' ? -1 : 1)
}
```

### 测试用例

```
const assert = require('assert')
assert.strictEqual(_parseInt(null), NaN)
assert.strictEqual(_parseInt('0e0'), 0)
assert.strictEqual(_parseInt('08'), 8)
assert.strictEqual(_parseInt(0.0000003), 3)
assert.strictEqual(_parseInt(0.00003), 0)
assert.strictEqual(_parseInt(-0.0000003), -3)
assert.strictEqual(_parseInt('6.022e23'), 6)
assert.strictEqual(_parseInt(6.022e2), 602)
```

## 考虑radix

### 代码实现
```
const _parseInt = (str, radix) => {
  // 不为string类型先转化为string 类型
  if (typeof str !== 'string') str = String(str)

  // 删除首尾空白
  str = str.trim()

  // 正则匹配[+|-]?[0]?[Xx]?[0-9a-fA-F]+
  const regex = /^(?<fuhao>[\+|\-]*)(?<radix>[0]?[Xx]?)(?<num>[0-9a-fA-F]+)/

  // 无法匹配返回NaN
  if (!regex.test(str)) return NaN

  // 匹配出符号、进制、数字三个分组
  const groups = str.match(regex).groups

  // radix的有效范围为 2-36
  if (radix && (radix < 2 || radix > 36)) return NaN

  // 如果没有指定radix, radix 会有以下默认值
  if (!radix) {
    if (groups.radix.toUpperCase() === '0X') radix = 16
    else if (groups.radix === '0') radix = 8
    else radix = 10
  }

  // 挨个字符串解析，如果遇到无法解析时则停止解析，返回已经解析好的整数
  let splitArr = groups.num.split('')
  const arr = []
  for(let i = 0; i < splitArr.length; i++) {
    // 根据charCode来做转行为实际数据, 0-9为[48-57],A-F为[65-70]
    const charCode = splitArr[i].toUpperCase().charCodeAt()
    let num 

    // 字符为[A-F]时, 实际数字为charCode -55
    if(charCode >= 65) num = charCode - 55

    // 字符为[0-9]时, 实际数字为charCode - 48
    else num = charCode - 48

    // 当实际数字大于radix时, 无法解析则停止字符串遍历
    if (num > radix) {
      break
    } else {
      arr.push(num)
    }
  }

  const len = arr.length
  // 当实际数字数组长度为0时, 返回NaN
  if(!len) return NaN
  let result = 0

  // 依次解析实际数字数组, 组合成真正的数字
  for(let i = 0; i < len; i++) {
    const num = arr[i] * Math.pow(radix, len - i - 1)
    result += num
  }

  // 算法匹配到的正负号
  return result * (groups.fuhao === '-' ? -1 : 1)
}

```

### 测试用例

```
const assert = require('assert')

// 以下返回15
assert.strictEqual(_parseInt('0xF', 16), 15)
assert.strictEqual(_parseInt('F', 16), 15)
assert.strictEqual(_parseInt('17', 8), 15)
assert.strictEqual(_parseInt(021, 8), 15)
assert.strictEqual(_parseInt('015', 10), 15)
assert.strictEqual(_parseInt(15.99, 10), 15)
assert.strictEqual(_parseInt('15,123', 10), 15)
assert.strictEqual(_parseInt('FXX123', 16), 15)
assert.strictEqual(_parseInt('1111', 2), 15)
assert.strictEqual(_parseInt('15 * 3', 10), 15)
assert.strictEqual(_parseInt('15e2', 10), 15)
assert.strictEqual(_parseInt('15px', 10), 15)
assert.strictEqual(_parseInt('12', 13), 15)

// 以下返回NaN
assert.strictEqual(_parseInt('Hello', 8), NaN)
assert.strictEqual(_parseInt('546', 2), NaN)  

// 以下返回-15
assert.strictEqual(_parseInt('-F', 16), -15)
assert.strictEqual(_parseInt('-0F', 16), -15)
assert.strictEqual(_parseInt('-0XF', 16), -15)
assert.strictEqual(_parseInt(-15.1, 10), -15)
assert.strictEqual(_parseInt(' -17', 8), -15)
assert.strictEqual(_parseInt(' -15', 10), -15)
assert.strictEqual(_parseInt('-1111', 2), -15)
assert.strictEqual(_parseInt('-15e1', 10), -15)
assert.strictEqual(_parseInt('-12', 13), -15)

// 以下返回4
assert.strictEqual(_parseInt(4.7, 10), 4)
assert.strictEqual(_parseInt(4.7 * 1e22, 10), 4)
assert.strictEqual(_parseInt(0.00000000000434, 10), 4)

// 以下返回224
assert.strictEqual(_parseInt('0e0', 16), 224)
```