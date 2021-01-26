

# go问题

## Golang开发新手常犯的50个错误

https://blog.csdn.net/gezhonglei2007/article/details/52237582

## interface转为struct结构体

https://www.codercto.com/a/54645.html

Go语言是门强类型语言，因此也导致了非常多的问题, interface{} 任意类型 不能随意的转换为其他类型

若要进行类型转换,需要进行类型的断言



我们可以对interface使用 `.()` 并在括号中传入想要解析的类型(如结构体)，形如

```
// 如果转换失败ok=false，转换成功ok=true
res, ok := anyInterface.(someType)
```

并且我们并不确定interface类型时候，也可以使用 `anyInterface.(type)` 结合 `switch case` 来做判断。

```go
package main
import (
    "fmt"
)
type User struct{
    Name string
}
func main() {
    any := User{
        Name: "fidding",
    }
    test(any)
}
func test(value interface{}) {
    switch v := value.(type) {
        case string:
            fmt.Println(v)
        case int32, int64:
            fmt.Println(v)
        case User:
            // 可以看到op即为将interface转为User struct类型，并使用其Name对象
        	op, ok := value.(User)  
        // 已确定是User类型，可直接使用op:= value.(User)或 var User op;op=value
            fmt.Println(op.Name, ok)
        default:
            fmt.Println("unknown")
    }
}

        	
```

## url解析

https://studygolang.com/articles/2876

```go
package main
import "fmt"
import "net/url"
import "strings"
func main() {
//我们将解析这个 URL 示例，它包含了一个 scheme，认证信息，主机名，端口，路径，查询参数和片段。
    s := "postgres://user:pass@host.com:5432/path?k=v#f"
//解析这个 URL 并确保解析没有出错。
    u, err := url.Parse(s)
    if err != nil {
        panic(err)
    }
//直接访问 scheme。
    fmt.Println(u.Scheme)
//User 包含了所有的认证信息，这里调用 Username和 Password 来获取独立值。
    fmt.Println(u.User)
    fmt.Println(u.User.Username())
    p, _ := u.User.Password()
    fmt.Println(p)
//Host 同时包括主机名和端口信息，如过端口存在的话，使用 strings.Split() 从 Host 中手动提取端口。
//或者，u.HostName, u.Port直接获取
    fmt.Println(u.Host)
    h := strings.Split(u.Host, ":")
    fmt.Println(h[0])
    fmt.Println(h[1])
//这里我们提出路径和查询片段信息。
    fmt.Println(u.Path)
    fmt.Println(u.Fragment)
//要得到字符串中的 k=v 这种格式的查询参数，可以使用 RawQuery 函数。你也可以将查询参数解析为一个map。已解析的查询参数 map 以查询字符串为键，对应值字符串切片为值，所以如何只想得到一个键对应的第一个值，将索引位置设置为 [0] 就行了。
    fmt.Println(u.RawQuery)
    m, _ := url.ParseQuery(u.RawQuery)
    fmt.Println(m)
    fmt.Println(m["k"][0])
}
//运行我们的 URL 解析程序，显示全部我们提取的 URL 的不同数据块。
$ go run url-parsing.go 
postgres
user:pass
user
pass
host.com:5432
host.com
5432
/path
f
k=v
map[k:[v]]
v
```

## 命令行获取参数

https://blog.csdn.net/skh2015java/article/details/78492384

### os库

*os*包以跨平台的方式，提供了一些与操作系统交互的函数和变量。

*os.Args* 获取命令行参数*,*返回一个字符串数组，第一个参数就是执行文件本身，后面的参数是从命令行输入的参数

```go
 func main() {
       for i := range os.Args {
              fmt.Println(os.Args[i])
       }
  }
```

```go
$  go run testOs.go"aaaaa"  "bbbbb"
执行结果:
 	 ……\testOs.ext
	aaaaa
	bbbbb
```

### flag包

*func t(namestring, value bool, usage string) \*t*

参数说明：*name*为*flag*名称，*value*为默认值，*usage*使用说明

返回指针



*func tVar(p \*int, name string, value int, usage string)*

作用：绑定*flag*到指定的变量*p*



使用*flag*来操作命令行参数，支持的格式如下：

```go
-id=1
--id=1
-id  1
--id 1
```

当所有*flag*声明完成后使用 *flag.Parse()*来解析命令行参数到指定的*flag*

```go
func main() {
       ok :=flag.Bool("ok",false,"is ok")
       id :=flag.Int("id",0,"id")
       port :=flag.String("port","8080","http listen port")
       var name string
       flag.StringVar(&name,"name","123","name")
       flag.Parse()
 
       fmt.Println("ok:",*ok)
       fmt.Println("id:",*id)
       fmt.Println("port:",*port)
       fmt.Println("name:",name)
 }
 
在命令行运行testFlag.go文件
$ go run testFlag.go -id=2 -name="tom"
执行结果：
      	ok: false
	id: 2
	port: 8080
	name: tom
```

## interface{}

为什么在Go语言中要慎用interface{}  https://juejin.im/post/6844903591149322253

原来这才是 Go Interfacehttps://juejin.im/post/6844903950911553550

## 协程

[Golang 之协程详解](https://www.cnblogs.com/liang1101/p/7285955.html)

GoLang之协程 https://www.cnblogs.com/chenny7/p/4498322.html

用一个简单的web服务探究golang的高并发原理  https://juejin.im/post/6844903871890849806

Go 并发编程 https://iswade.github.io/articles/go_concurrency/

## golang 字符串、json、map之间的转换

https://blog.csdn.net/qq_769932247/article/details/85769658

```go
import (
    "encoding/json"
    "fmt"
    "os"
)
 
type ConfigStruct struct {
    Host              string   `json:"host"`
    Port              int      `json:"port"`
    AnalyticsFile     string   `json:"analytics_file"`
    StaticFileVersion int      `json:"static_file_version"`
    StaticDir         string   `json:"static_dir"`
    TemplatesDir      string   `json:"templates_dir"`
    SerTcpSocketHost  string   `json:"serTcpSocketHost"`
    SerTcpSocketPort  int      `json:"serTcpSocketPort"`
    Fruits            []string `json:"fruits"`
}
 
type Other struct {
    SerTcpSocketHost string   `json:"serTcpSocketHost"`
    SerTcpSocketPort int      `json:"serTcpSocketPort"`
    Fruits           []string `json:"fruits"`
}
 
func main() { 
    jsonStr := `{"host": "http://localhost:9090","port": 9090,"analytics_file": "","static_file_version": 1,"static_dir": "E:/Project/goTest/src/","templates_dir": "E:/Project/goTest/src/templates/","serTcpSocketHost": ":12340","serTcpSocketPort": 12340,"fruits": ["apple", "peach"]}`
 
    //json str 转map
    var dat map[string]interface{}
    if err := json.Unmarshal([]byte(jsonStr), &dat); err == nil {
        fmt.Println("==============json str 转map=======================")
        fmt.Println(dat)
        fmt.Println(dat["host"])
    }
 
    //json str 转struct
    var config ConfigStruct
    if err := json.Unmarshal([]byte(jsonStr), &config); err == nil {
        fmt.Println("================json str 转struct==")
        fmt.Println(config)
        fmt.Println(config.Host)
    }
 
    //json str 转struct(部份字段)
    var part Other
    if err := json.Unmarshal([]byte(jsonStr), &part); err == nil {
        fmt.Println("================json str 转struct==")
        fmt.Println(part)
        fmt.Println(part.SerTcpSocketPort)
    }
 
    //struct 到json str
    if b, err := json.Marshal(config); err == nil {
        fmt.Println("================struct 到json str==")
        fmt.Println(string(b))
    }
 
    //map 到json str
    fmt.Println("================map 到json str=====================")
    enc := json.NewEncoder(os.Stdout)
    enc.Encode(dat)
 
    //array 到 json str
    arr := []string{"hello", "apple", "python", "golang", "base", "peach", "pear"}
    lang, err := json.Marshal(arr)
    if err == nil {
        fmt.Println("================array 到 json str==")
        fmt.Println(string(lang))
    }
 
    //json 到 []string
    var wo []string
    if err := json.Unmarshal(lang, &wo); err == nil {
        fmt.Println("================json 到 []string==")
        fmt.Println(wo)
    }
}
```

## 格式化输出json

json.Mara

```json
// 输出格式化的json串（bytes为[]byte 类型）
var buf bytes.Buffer
err = json.Indent(&buf, bytes, "", "    ")
if err == nil {
    log.Info("Greeting:\n%s\n", buf.Bytes())
}
```

```go
// 输出格式化的json串(struct为结构体，第一个参数表示每行输出的前缀字符串，第二个参数定义缩进的字符串)
data, err := json.MarshaIndent(structData, "", "   ")
if err == nil {
    log.Info("Greeting:\n%s\n", data)
}
```

## Slice 和 Map那么常见的坑

https://blog.csdn.net/weixin_40165163/article/details/90707593

## 深拷贝和浅拷贝

https://blog.csdn.net/weixin_40165163/article/details/90680466

在了解原型设计模式之前我们需要新知道Golang的深拷贝与浅拷贝之间的区别。

github:https://github.com/zhumengyifang/GolangDesignPatterns

```go
//基于序列化和反序列化来实现对象的深度拷贝
func deepCopy(dst, src interface{}) error {
	var buf bytes.Buffer
	if err := gob.NewEncoder(&buf).Encode(src); err != nil {
		return err
	}
	return gob.NewDecoder(bytes.NewBuffer(buf.Bytes())).Decode(dst)
}
```

## golang通过type定义函数类型

https://blog.csdn.net/zhangxw872196/article/details/103822652

## 时间操作

https://blog.csdn.net/HYZX_9987/article/details/99947688

### 获取当前时间及其秒、毫秒、纳秒数

```go

now := time.Now() //获取当前时间
==>2019-08-21 11:30:51.2470317 +0800 CST m=+0.004501101
fmt.Printf("时间戳（秒）：%v;\n", time.Now().Unix())        //10位
fmt.Printf("时间戳（纳秒）：%v;\n",time.Now().UnixNano())    //19位
fmt.Printf("时间戳（毫秒）：%v;\n",time.Now().UnixNano() / 1e6)        //或者秒*1000也可
fmt.Printf("时间戳（纳秒-->秒）：%v;\n",time.Now().UnixNano() / 1e9)
```

### 获取指定时间前的时间

```go
// 获取50秒前的时间，方式1
st,_ := time.ParseDuration("-50s")
fmt.Println("50秒前的时间：",time.Now().Add(st))
 
// 获取1分钟前的时间，n秒前则是time.Second * -n，方式2
t := time.Now().Add(time.Minute * -1) 
fmt.Println("一分钟前的时间：",t)
 
//获取1小时前的时间
sth,_ := time.ParseDuration("-1h")
fmt.Println("1小时前的时间：",time.Now().Add(sth))
 
// 获取2天前的时间
oldTime := time.Now().AddDate(0, 0, -2)
 
//获取两个月前的时间
oldTime := time.Now().AddDate(0, -2, 0)
```

### 获取指定时间后的时间

```go
// 获取50秒后的时间，方式1
st,_ := time.ParseDuration("50s")
fmt.Println("50秒之后的时间：",time.Now().Add(st))
 
// 获取1分钟后的时间，n秒前则是time.Second * n，方式2
t := time.Now().Add(time.Minute * 1) 
fmt.Println("一分钟后的时间：",t)
 
//获取1小时后的时间
sth,_ := time.ParseDuration("1h")
fmt.Println("1小时之后的时间：",time.Now().Add(sth))
 
// 获取当前时间2天后的时间
newTime := time.Now().AddDate(0, 0, 2)
//newTime 的结果为时间time类型
 
//获取当前时间2月后的时间
newTime := time.Now().AddDate(0, 2, 0)
```

### 获取两个时间点的时间差

```go

t1 := time.Now()
//设置期间经历了50秒时间
t2 := time.Now().Add(time.Second * 50)
fmt.Println("t2与t1相差：",t2.Sub(t1))		//t2与t1相差： 50s
```

## Go继承的方法重写，继承抽象类实现

https://blog.csdn.net/zhbinary/article/details/89418195

## 【Go语言】基本类型排序和 slice 排序

https://itimetraveler.github.io/2016/09/07/%E3%80%90Go%E8%AF%AD%E8%A8%80%E3%80%91%E5%9F%BA%E6%9C%AC%E7%B1%BB%E5%9E%8B%E6%8E%92%E5%BA%8F%E5%92%8C%20slice%20%E6%8E%92%E5%BA%8F/

## [go的异常处理机制](https://segmentfault.com/a/1190000010203475)

https://segmentfault.com/a/1190000010203475

## Golang 推荐的命名规范

https://juejin.cn/post/6844903779175759885

# 加密

GO语言的进阶之路-网络安全之proxyhttps://www.cnblogs.com/yinzhengjie/p/7368030.html（包含des和aes加密）

## DES加密

变体：3DES

加密模式：CBC  ECB    CFB  OFB

Java默认DES算法使用DES/ECB/PKCS5Padding工作方式，在GO语言中因为ECB的脆弱性，DES的ECB模式是故意不放出来的，但实际情况中有时我们并不需要那么安全。

Golang DES加解密 https://blog.csdn.net/benben_2015/article/details/81254023

GO语言DES加密解密  https://blog.csdn.net/weixin_42117918/article/details/82871566

**GO加密解密之DES** http://blog.studygolang.com/2013/01/go%E5%8A%A0%E5%AF%86%E8%A7%A3%E5%AF%86%E4%B9%8Bdes/

**DES--------Golang对称加密之模式问题实战**  https://blog.51cto.com/lisea/2065657

### 和其他语言交互：加解密

这次，我写了PHP、Java的版本，具体代码放在github上。这里说明一下，Java中，默认模式是ECB，且没有用”\0″填充的情况，只有NoPadding和PKCS5Padding；而PHP中（mcrypt扩展），默认填充方式是”\0″，而且，当数据长度刚好是block size的整数倍时，默认不会填充”\0″，这样，如果数据刚好是block size的整数倍且结尾字符是”\0″，会有问题。

综上，跨语言加密解密，应该使用PKCS5Padding填充。

### 原生库

"crypto/cipher"
"crypto/des"

```go
package common

import (
	"bytes"
	"crypto/cipher"
	"crypto/des"
	"encoding/base64"
	"errors"
	"fmt"
)

func Mydes(data ,key []byte) string{
	key = key[:8] //由于des的加密模式的特点，只会有8个字节生效。
	result, err := DesECBEncrypt(data, key) //我们进行加密操作，如要输入的字符串都是"[]byte"类型。最终我们会拿到切片数组。
	if err != nil {
		panic(err)
	}
	fmt.Println("加密后的样子：",base64.StdEncoding.EncodeToString(result))
	buf, err := DesECBDecrypt(result, key)  //这是进行解密操作，将加密的数据和key传入进去进行解密操作。
	if err != nil {
		panic(err)
	}
	fmt.Println("解密后的样子：",string(buf))
	return base64.StdEncoding.EncodeToString(result)
}

// ECB加密模式
func DesECBEncrypt(data, key []byte)([]byte, error) {
	block, err := des.NewCipher(key) //定义一个加密器“block”,把我们的秘钥传进去就OK拉！
	if err != nil {
		return nil, err
	}
	bs := block.BlockSize()
	data = PKCS5Padding(data, bs)
	if len(data)%bs != 0 {
		return nil, errors.New("Need a multiple of the blocksize")
	}
	out := make([]byte, len(data))
	dst := out
	for len(data) > 0 {
		block.Encrypt(dst, data[:bs])
		data = data[bs:]
		dst = dst[bs:]
	}
	return out, nil
}

// CBC加密模式
func DesCBCEncrypt(origData, key []byte) ([]byte, error) {
	block, err := des.NewCipher(key)
	if err != nil {
		return nil, err
	}
	origData = PKCS5Padding(origData, block.BlockSize())
	// origData = ZeroPadding(origData, block.BlockSize())
	blockMode := cipher.NewCBCEncrypter(block, key)//对块数据进行加密操作。
	crypted := make([]byte, len(origData))
	// 根据CryptBlocks方法的说明，如下方式初始化crypted也可以
	// crypted := origData
	blockMode.CryptBlocks(crypted, origData) //将date进行加密生成crypted,其实我们也可以原地进行加密，就是直接对源数据进行修改吗。可以节省内存，根据的需求来。
	return crypted, nil //将加密后的数据crypte返回给用户。
}


func PKCS5Padding(ciphertext []byte, blockSize int) []byte {
	padding := blockSize - len(ciphertext)%blockSize
	padtext := bytes.Repeat([]byte{byte(padding)}, padding)
	return append(ciphertext, padtext...)
}


func DesECBDecrypt(data, key []byte)([]byte, error) {
	block, err := des.NewCipher(key)
	if err != nil {
		return nil, err
	}
	bs := block.BlockSize()
	if len(data)%bs != 0 {
		return nil, errors.New("crypto/cipher: input not full blocks")
	}
	out := make([]byte, len(data))
	dst := out
	for len(data) > 0 {
		block.Decrypt(dst, data[:bs])
		data = data[bs:]
		dst = dst[bs:]
	}
	out = PKCS5UnPadding(out)
	return out, nil
}

func DesCBCDecrypt(crypted, key []byte) ([]byte, error) {
	block, err := des.NewCipher(key) //定义一个加密器“block”,把我们的秘钥传进去就OK拉！
	if err != nil {
		return nil, err
	}
	blockMode := cipher.NewCBCDecrypter(block, key) //对块数据进行解密操作。
	//origData := make([]byte, len(crypted))
	origData := crypted //当然，我们也可以这样写“origData := make([]byte, len(crypted))”
	blockMode.CryptBlocks(origData, crypted) //对块数据进行解密操作
	//origData = PKCS5UnPadding(origData)

	origData = PKCS5UnPadding(origData)
	return origData, nil
}

func PKCS5UnPadding(origData []byte) []byte {
	length := len(origData)
	unpadding := int(origData[length-1])
	return origData[:(length - unpadding)]
}
```

### 使用第三方库

`github.com/marspere/goencrypt`包实现了多种加密算法，包括对称加密和非对称加密等。

```go
package main

import (
	"fmt"
	"github.com/marspere/goencrypt"
)

func main() {
	// key为12345678
	// iv为空
	// 采用ECB分组模式
	// 采用pkcs5padding填充模式
	// 输出结果使用base64进行加密
	cipher := goencrypt.NewDESCipher([]byte("12345678"), []byte(""), goencrypt.ECBMode, goencrypt.Pkcs5, goencrypt.PrintBase64)
	cipherText, err := cipher.DESEncrypt([]byte("hello world"))
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Println(cipherText)
}
```



# HTTP

Golang之发送http请求，GET、POST  https://juejin.im/post/6844903859312132103

## golang http client 连接池

https://blog.csdn.net/qq_21514303/article/details/87794750



# 设计模式

## 装饰器模式

https://cloudsjhan.github.io/2019/11/30/golang%E8%AE%BE%E8%AE%A1%E6%A8%A1%E5%BC%8F%E4%B9%8B%E8%A3%85%E9%A5%B0%E5%99%A8%E6%A8%A1%E5%BC%8F/





# 测试用例

https://books.studygolang.com/The-Golang-Standard-Library-by-Example/chapter09/09.1.html

https://juejin.cn/post/6844903933278683149



