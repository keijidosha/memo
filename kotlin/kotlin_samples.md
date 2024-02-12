## Kotlin Samples

- Table of Content  
{:toc}

### イテレーター

Kotlin で BufferedReader から 1行ずつ読み込むイテレーター

```koltin
private class LinesSequence(private val reader: BufferedReader) : Sequence<String> {
    override public fun iterator(): Iterator<String> {
        return object : Iterator<String> {
            private var nextValue: String? = null
            private var done = false

            override public fun hasNext(): Boolean {
                if (nextValue == null && !done) {
                    nextValue = reader.readLine()
                    if (nextValue == null) done = true
                }
                return nextValue != null
            }

            override public fun next(): String {
                if (!hasNext()) {
                    throw NoSuchElementException()
                }
                val answer = nextValue
                nextValue = null
                return answer!!
            }
        }
    }
}
```

File.forEachLine() から呼び出された先で実装されている kotlin.io.LinesSequence からの引用  
[lineSequence - Kotlin Programming Language](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/java.io.-buffered-reader/line-sequence.html)
