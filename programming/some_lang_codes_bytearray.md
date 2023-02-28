# コード比較: バイナリ(バイト配列)

## 整数をバイト配列に変換
### Kotlin
#### Little Endian
```
 fun IntToByteArrayLittleEndian( value: Int, length: Int ): ByteArray {
     val buf = ByteArray( length )
     var remain = value
 
     repeat( length ) { idx ->
         val value = remain % 256
         buf[idx] = value.toByte()
         remain = remain shr 8
     }
 
     return buf
 }
```

* value は変換元の整数
* length は変換後のバイト配列の長さ

#### BigEndian
```
fun intToByteArrayBigEndian( value: Int, length: Int ): ByteArray {
    val buf = ByteArray( length )
    var remain = value
    val maxIdx = length - 1

    repeat( length ) { idx ->
        val value = remain % 256
        buf[maxIdx - idx] = value.toByte()
        remain = remain shr 8
    }

    return buf
}
```

* value は変換元の整数
* length は変換後のバイト配列の長さ

### Go
#### Little Endian
##### 符号なし整数
```
func unsignedIntToByteArrayLittleEndian(intVal uint, len int) []byte {
    buf := make([]byte, len)
    var ii int
    remain := intVal
    for ii = 0; ii < len; ii++ {
        buf[ii] = byte(remain & 0xff)
        remain >>= 8
    }

    return buf
}
```

## バイト配列を符号付整数に変換
### Kotlin
#### Little Endian
```
fun byteArrayToSignedIntLittleEndian( buf: ByteArray, start: Int, length: Int ): Int {
    var num = 0
    var keta = 0
    for( idx in start..( start + length - 1 )) {
        num = num or if( idx == start + length - 1 ) {
            (( buf[idx].toInt()         ) shl ( keta * 8 ))
        } else {
            (( buf[idx].toInt() and 255 ) shl ( keta * 8 ))
        }
        keta++
    }

    return num
}
```

#### Big Endian
```
fun byteArrayToUnsignedIntBigEndian( buf: ByteArray, start: Int, length: Int ): Int {
    var num = 0
    for( idx in start..( start + length - 1 )) {
        num = num shl 8
        num = num or ( buf[idx].toInt() and 255 )
    }

    return num
}
```

## バイト配列を符号なし整数に変換
### Kotlin
#### Little Endian
```
fun byteArrayToUnsignedIntLittleEndian( buf: ByteArray, start: Int, length: Int ): Int {
    var num = 0
    var keta = 0
    for( idx in start..( start + length - 1 )) {
        num = num or (( buf[idx].toInt() and 255 ) shl ( keta * 8 ))
        keta++
    }

    return num
}
```

#### Big Endian
```
fun byteArrayToUnsignedIntBigEndian( buf: ByteArray, start: Int, length: Int ): Int {
    var num = 0
    for( idx in start..( start + length - 1 )) {
        num = num shl 8
        num = num or ( buf[idx].toInt() and 255 )
    }

    return num
}
```

### Go
#### LittleEndian
```
func byteArrayToUnsignedIntLittleEngian(buf []byte, start int, len int) uint {
    var sum uint = 0
    max := start + len
    var pos uint = 0
    for idx := start; idx < max; idx++ {
        sum += uint(buf[idx]) << (pos * 8)
        pos++
    }
    return sum
}
```
