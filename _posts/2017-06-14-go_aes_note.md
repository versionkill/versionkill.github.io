---
layout: post
title: golang AES 加解密
tags: go
comments: true
---

密码学中的高级加密标准（Advanced Encryption Standard，AES），又称Rijndael加密法，这个标准用来替代原先的DES。

AES加密数据块分组长度必须为128bit，密钥长度可以是128bit、192bit、256bit中的任意一个。



```go
func NewCipher(key []byte) (cipher.Block, error)
```

创建一个cipher.Block接口。参数key为密钥，长度只能是16、24、32字节，用以选择AES-128、AES-192、AES-256。


```go
func NewCFBEncrypter(block Block, iv []byte) Stream
```
返回一个密码反馈模式的、底层用block加密的Stream接口，初始向量iv的长度必须等于block的块尺寸。

```go
func NewCFBDecrypter(block Block, iv []byte) Stream
```
返回一个密码反馈模式的、底层用block解密的Stream接口，初始向量iv必须和加密时使用的iv相同。

```go
type Stream interface {
    // 从加密器的key流和src中依次取出字节二者xor后写入dst，src和dst可指向同一内存地址
    XORKeyStream(dst, src []byte)
}
```
处理获取的所有切片。


实例：

```go
package main
 
import (
    "crypto/aes"
    "crypto/cipher"
    "fmt"
    "encoding/hex"
)
 
const (
    key_text = "YWpgaFMJmH4FeFFIiHhLsHgRMa9zwXgk"
)
 
var msg string
 
func main() {
 
    b := "weloveyouchinese11"
    b_info := encode(b)
    fmt.Println(b_info)
	
    ciphertext,_:=hex.DecodeString(b_info)
    fmt.Println(ciphertext)
    response:=decode(ciphertext)
    fmt.Println("result",string(response))
}
func showMessage(s string) {
    fmt.Println(s)
}
 
func decode(bytes []byte) (plaintextCopy []byte) {
    var commonIV = []byte{0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0a, 0x0b, 0x0c, 0x0d, 0x0e, 0x0f}
 
    //makeaes
    c, err := aes.NewCipher([]byte(key_text))
    if err != nil {
        fmt.Printf("Error:NewCipher(%dbytes)=%s", len(key_text), err)
        return
    }
 
    //decode
    cfbdec := cipher.NewCFBDecrypter(c, commonIV)
    plaintextCopy = make([]byte, len(bytes))
    cfbdec.XORKeyStream(plaintextCopy, bytes)
    fmt.Printf("decode%x=>%s\n", bytes, plaintextCopy)
 
    return plaintextCopy
}
 
func encode(str string) string {
    var commonIV = []byte{0x00, 0x01, 0x02, 0x03, 0x04, 0x05, 0x06, 0x07, 0x08, 0x09, 0x0a, 0x0b, 0x0c, 0x0d, 0x0e, 0x0f}
    var plaintext = []byte(str)
    
    var msg string
 
    c, err := aes.NewCipher([]byte(key_text))
    if err != nil {
        msg = fmt.Sprintf("Error:NewCipher(%dbytes)=%s", len(key_text), err)
        showMessage(msg)
        return ""
    }
 
    plen := len(plaintext)
 
    cfb := cipher.NewCFBEncrypter(c, commonIV)
    ciphertext := make([]byte, plen)
    cfb.XORKeyStream(ciphertext, plaintext)
 
    result := fmt.Sprintf("%x", ciphertext)

    return result
}
```
