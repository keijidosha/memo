# Kotlin: 数値

* バイトを符号なし数値として扱う  
`val int = byte and 0xff`
* バイト配列の指定されたバイト位置から指定された桁数を整数に変換する
  ```
  fun ByteArray.toIntBigEndian( start: Int, length: Int ): Int {
      var num = 0
      for( idx in start..( start + length - 1 )) {
          num = num shl 8
          num = num or ( this[idx].toInt() and 255 )
      }
  
      return num
  }
  
  fun ByteArray.toIntLittleEndian( start: Int, length: Int ): Int {
      var num = 0
      var keta = 0
      for( idx in start..( start + length - 1 )) {
          num = num or (( this[idx].toInt() and 255 ) shl ( keta * 8 ))
          keta++
      }
  
      return num
  }
  ```

