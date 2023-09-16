# Go Essential

Table of Contents
-----------------

* [Table of Contents](#table-of-contents)
   * [インストール](#インストール)
      * [Mac](#mac)
      * [Linux](#linux)
   * [操作](#操作)
   * [関数呼び出し](#関数呼び出し)
   * [変数](#変数)
      * [型](#型)
         * [型変換](#型変換)
            * [interface{} からの型変換(type assertion)](#interface-からの型変換type-assertion)
      * [使用](#使用)
   * [制御](#制御)
      * [for](#for)
      * [swtich](#swtich)
         * [Type Switches](#type-switches)
   * [ポインター](#ポインター)
   * [構造体](#構造体)
   * [コレクション](#コレクション)
      * [配列](#配列)
      * [Slice](#slice)
      * [Maps](#maps)
   * [関数](#関数)
   * [メソッド](#メソッド)
   * [インターフェース](#インターフェース)
   * [エラーハンドリング](#エラーハンドリング)
      * [エラー型判定](#エラー型判定)
   * [Goroutines, channel](#goroutines-channel)
   * [パッケージ](#パッケージ)
   * [日付・時刻](#日付時刻)
   * [ログ出力](#ログ出力)
   * [スリープ(待機)](#スリープ待機)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

## インストール
### Mac
`brew install go`
### Linux
```
yum install gcc
cd /usr/local/src/
curl -O https://dl.google.com/go/go1.12.4.linux-amd64.tar.gz
cd ..
tar zxvf src/go1.12.4.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
```

## 操作
* 実行  
`go run hello.go`
* プロジェクト開始(例: ワークスペース)
  ```
  # ワークスペース用ディレクトリ作成
  mkdir hoge
  cd hoge
  # main パッケージ用ディレクトリ作成
  mkdir main
  cd main
  go mod init hoge/main
  # (例) echo を使う場合
  go get github.com/labstack/echo/v4
  go mod tidy
  vi hoge.go
  cd ..
  # モジュールを module1 という名前で作成する場合
  mkdir module1
  cd module1
  go mod init hoge/module1
  vi module1.go
  cd ..
  go work init main module1
  # ビルド
  cd main
  go build hoge.go
  ```
* コンパイル  
`go build hello.go`
  * Linux 64bit用にコンパイル  
    `GOOS=linux GOARCH=amd64 go build hello.go`
  * Windows 32bit用にコンパイル  
    `GOOS=windows GOARCH=386 go build hello.go`
  * MacOS 64bit用にコンパイル  
    `GOOS= darwin GOARCH=amd64 go build hello.go`
  * CGO を有効にしてコンパイル
    * Linux 64bit用
      1. インストール  
         `brew install FiloSottile/musl-cross/musl-cross`
      1. コンパイル  
         * muslをスタティックリンクしてコンパイル(オススメ)  
           `GOOS=linux GOARCH=amd64 CGO_ENABLED=1 CC=/usr/local/bin/x86_64-linux-musl-cc go build --ldflags '-linkmode external -extldflags "-static"' -a -v -o hoge_linux hoge.go`
         * muslをダイナミックリンクしてコンパイル  
           1. 実行時依存ライブラリを Linux にアップロードしてシンボリックリンク作成  
              `scp -p /usr/local/Cellar/musl-cross/0.9.7_1/libexec/x86_64-linux-musl/lib/libc.so root@192.168.1.1:/usr/local/lib64/ld-musl-x86_64.so`  
              `ln -s /usr/local/lib64/ld-musl-x86_64.so /lib/ld-musl-x86_64.so.1`  
           1. コンパイル  
              `GOOS=linux GOARCH=amd64 CGO_ENABLED=1 CC=/usr/local/bin/x86_64-linux-musl-cc go build -a -v -o hoge_linux hoge.go`
    * Windows用
      1. インストール  
         `brew install mingw-w64`  
      1. コンパイル  
         `GOOS=windows GOARCH=amd64 CC=x86_64-w64-mingw32-gcc CGO_ENABLED=1 go build -a -v insert.go`
* cgo コンパイルする時の C/C++ コンパイル、リンクオプション指定  
  環境変数にオプションをセット  
  (参考) https://golang.org/cmd/go/
  * C: CGO_CFLAGS="\<options\>"
  * C++: CGO_CPPFLAGS="\<options\>"
  * リンク: CGO_LDFLAGS="\<options\>"
* cgo を使ったパッケージの事前コンパイル  
  Go のソースを編集した後 cgo のパッケージはリンクするだけなので、ビルドが速くなる  
  `go install`
* Shared Library としてビルド  
  * import "C" を記述し、他言語から呼び出したいファンクションの export 宣言を記述  
    main() ファンクションはコンパイルを通すために必要。
    ```
    package main

    import "C"

    //export toDouble
    func toDouble(value int) int {
        return value * 2
    }

    func main() {}
    ```
  * Shared Library としてビルド  
    `go build -buildmode=c-shared -o xxx.so xxx.go`
* 依存ライブラリダウンロード  
`go get xxx.org/x/xxx`  
~/go/ 配下にソースとバイナリがダウンロードされる。
* ソースチェック  
`go vet hello.go`

## 関数呼び出し
* 名前付き戻り値
  ```golang
  package main
  import "fmt"
  func split(sum int) (x, y int) {
      x = sum * 4 / 9
      y = sum - x
      return
  }
  func main() {
      fmt.Println(split(17))
  }
  ```

## 変数
### 型
* bool
* string
* int  int8  int16  int32  int64
* uint uint8 uint16 uint32 uint64 uintptr
* byte // alias for uint8
* rune // alias for int32  
     // represents a Unicode code point
* float32 float64
* complex64 complex128
----
* 定数  
  ```golang
  package main
  import "fmt"
  const Pi = 3.14             // 関数の外で定数を宣言
  func main() {
      fmt.Println(Pi)
      const name = "Masaru"   // 関数の中でも定数を宣言できる
      fmt.Println(name)
  }
  ```
* 型を表示  
  ```golang
  package main
  import "fmt"
  func main() {
      v := 42 // change me!
      fmt.Printf("v is of type %T\n", v)
  }
  ```

#### 型変換

##### interface{} からの型変換(type assertion)

interface{} はそのまま型付きの変数に代入できない。
```golang
var hoge interface{} = "hoge"
var str string = hoge
```
次のエラーが発生。  
> cannot use hoge (type interface {}) as type string in assignment: need type assertion

次のように型指定して変化する。

```golang
var hoge interface{} = "hoge"
var str string = hoge.(string)
fmt.Println(str)
```

変換の左辺でステータスを受け取り、型変換可能に成功した場合だけ処理実行する。  
このようにすると変数(hoge)が nil でも実行時エラーにならない(ok が false になる)。

```golang
var hoge interface{} = "hoge"
str, ok := hoge.(string)
if ok {
    fmt.Println(str)
}
```

### 使用
* 変数初期化
  ```golang
  package main
  import "fmt"
  var i, j int = 1, 2
  func main() {
      var c, python, java = true, false, "no!"
      k := 3
      fmt.Println(i, j, k, c, python, java)
  }
  ```
  「:=」による var を省略した代入は関数内だけ有効

## 制御
### for
* 例1  
  ```golang
  package main
  import "fmt"
  func main() {
      sum := 0
      for i := 0; i < 10; i++ {
          sum += i
      }
      fmt.Println(sum)
  }
  ```
* for の次の変数初期化は省略できる  
  ```golang
  package main
  import "fmt"
  func main() {
      sum := 1
      for ; sum < 1000; {
          sum += sum
      }
      fmt.Println(sum)
  }
  ```
* while として使える  
  ```golang
  package main
  import "fmt"
  func main() {
      sum := 1
      for sum < 1000 {
          sum += sum
      }
      fmt.Println(sum)
  }
  ```
* for の後ろを省略すると永久ループになる  
  ```golang
  package main
  func main() {
      for {
      }
  }
  ```

* for の中にある switch で break しても、switch は抜けられるが for は抜けならない  
=> ラベルを定義し、そのラベルに対して break する  
  ```golang
  package main

  import "fmt"

  func main() {
  LABEL:

      for i := 0; i < 10; i++ {
  	      fmt.Println(i)
          switch i {
          case 5:
              break LABEL
          }
      }
  }
  ```

### swtich
* 例
  ```golang
  package main
  import (
      "fmt"
      "runtime"
      "time"
  )
  func main() {
      fmt.Print("Go runs on ")
      switch os := runtime.GOOS; os {
      case "darwin":
          fmt.Println("OS X.")
      case "linux":
          fmt.Println("Linux.")
      default:
          // freebsd, openbsd,
          // plan9, windows...
          fmt.Printf("%s.", os)
      }
  
      // 計算式と比較
      fmt.Println("When's Saturday?")
      today := time.Now().Weekday()
      switch time.Saturday {
      case today + 0:
          fmt.Println("Today.")
      case today + 1:
          fmt.Println("Tomorrow.")
      case today + 2:
          fmt.Println("In two days.")
      default:
          fmt.Println("Too far away.")
      }
  
      // 値なし switch
      t := time.Now()
      switch {
      case t.Hour() < 12:
          fmt.Println("Good morning!")
      case t.Hour() < 17:
          fmt.Println("Good afternoon.")
      default:
          fmt.Println("Good evening.")
      }

      message := "hoge"
      switch {
      case len(message) == 4:
          fmt.Println("Message length is 4.")
      case message == "hoge":
          fmt.Println("Message is hoge.")
      default:
          fmt.Println("Other message.")
      }
  }
  ```

#### Type Switches
```golang
package main

import(
    "fmt"
)

func judge( v interface{} ) {
    switch x := v.(type) {
    case int:
        fmt.Printf( "int: %v, %v\n", x , v )
    case string:
        fmt.Printf( "string %q %v\n", x, v )
    default:
        fmt.Printf( "unknown type: %v %T\n", x, v )
    }
}

func main() {
    judge( 15 )
    judge( "hello" )
    judge( [3]int{ 1, 2, 3 })

    // interface{}型の変数に代入された値が、期待する型かどうかを判定する場合
    var v interface{} = 15
    v, ok := v.( int )
    if ok {
        fmt.Println( "int value is ", v )
    }
}
```

## ポインター
* 例  
  ```golang
  package main
  import "fmt"
  func main() {
      i, j := 42, 2701
  
      p := &i         // point to i
      fmt.Println(*p) // 42: read i through the pointer
      *p = 21         // set i through the pointer
      fmt.Println(i)  // 21: see the new value of i
  
      p = &j         // point to j
      *p = *p / 37   // divide j through the pointer
      fmt.Println(j) // 73: see the new value of j
      fmt.Println(i)  // 21: see the new value of i
  }
  ```

## 構造体
* 例  
  ```golang
  package main
  import "fmt"
  type Vertex struct {
      X int
      Y int
  }
  var (
      v1 = Vertex{1, 2}  // has type Vertex
      v2 = Vertex{X: 1}  // Y:0 is implicit
      v3 = Vertex{}      // X:0 and Y:0
      p  = &Vertex{1, 2} // has type *Vertex
  )
  func main() {
      fmt.Println(Vertex{1, 2})    // show will "{1 2}"

      v := Vertex{1, 2}
      v.X = 4
      fmt.Println(v.X)

      p := &v
      p.X = 1e9
      fmt.Println(v)

      v = Vertex{1, 2}
      v.X = 4

      fmt.Println(v1, p, v2, v3)
  }
  ```

## コレクション
### 配列
* 例  
  ```golang
  package main
  import "fmt"
  func main() {
      var a [2]string
      a[0] = "Hello"
      a[1] = "World"
      fmt.Println(a[0], a[1])  // "Hello world"
      fmt.Println(a)           // [Hello World]
  
      primes := [6]int{2, 3, 5, 7, 11, 13}
      // 配列のサイズをコンパイラにチェックさせる
      // primes := [...]int{2, 3, 5, 7, 11, 13}
      fmt.Println(primes)      // [2 3 5 7 11 13]
  }
  ```
* 配列サイズ取得  
`len(配列)`


### Slice
[] の中に要素数を指定すると Array、指定しないと Slice になる。
* 例  
  ```golang
  package main
  import "fmt"
  func main() {
      // 要素数を指定して初期化すると配列になる
      primes := [6]int{2, 3, 5, 7, 11, 13}
      var s []int = primes[1:4]
      fmt.Println(s)  // [3 5 7]
      names := [4]string{
          "John",
          "Paul",
          "George",
          "Ringo",
      }
      fmt.Println(names)  // [John Paul George Ringo]
  
      a := names[0:2]
      b := names[1:3]
      fmt.Println(a, b)   // [John Paul] [Paul George]
  
      b[0] = "XXX"
      fmt.Println(a, b)   // [John XXX] [XXX George]
      fmt.Println(names)  // [John XXX George Ringo]

      s := []struct {
          i int
          b bool
      }{
          {2, true},
          {3, false},
          {5, true},
          {7, true},
          {11, false},
          {13, true},
      }
      fmt.Println(s)  // [{2 true} {3 false} {5 true} {7 true} {11 false} {13 true}]
  }
  ```
* 可変容量  
  ```golang
  package main
  import "fmt"
  func main() {
      // 要素数を指定せず初期化するとスライスになる
      s := []int{2, 3, 5, 7, 11, 13}
      printSlice(s)  // len=6 cap=6 [2 3 5 7 11 13]
  
      // Slice the slice to give it zero length.
      s = s[:0]
      printSlice(s)  // len=0 cap=6 []
  
      // Extend its length.
      s = s[:4]
      printSlice(s)  // len=4 cap=6 [2 3 5 7]
  
      // Drop its first two values.
      s = s[2:]
      printSlice(s)  // len=2 cap=4 [5 7]
  }
  func printSlice(s []int) {
      fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
  }
  ```
* make で初期化  
  ```golang
  package main
  import "fmt"
  func main() {
      // makeを使って初期化するとスライスになる
      a := make([]int, 5)
      printSlice("a", a)  // a len=5 cap=5 [0 0 0 0 0]
      b := make([]int, 0, 5)
      printSlice("b", b)  // b len=0 cap=5 []
      c := b[:2]
      printSlice("c", c)  // c len=2 cap=5 [0 0]
      d := c[2:5]
      printSlice("d", d)  // d len=3 cap=3 [0 0 0]
  }

  func printSlice(s string, x []int) {
      fmt.Printf("%s len=%d cap=%d %v\n",
          s, len(x), cap(x), x)
  }
  ```
* appendで追加  
  ```golang
  package main
  import "fmt"
  func main() {
      var s []int
      printSlice(s)  // len=0 cap=0 []
      // append works on nil slices.
      s = append(s, 0)
      printSlice(s)  // len=1 cap=2 [0]
      // The slice grows as needed.
      s = append(s, 1)
      printSlice(s)  // len=2 cap=2 [0 1]
      // We can add more than one element at a time.
      s = append(s, 2, 3, 4)
      printSlice(s)  // len=5 cap=8 [0 1 2 3 4]
  }
  func printSlice(s []int) {
      fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
  }
  ```
* Slice を Range で回す  
  ```golang
  package main
  import "fmt"
  func main() {
      pow := make([]int, 10)
      for i := range pow {
          pow[i] = 1 << uint(i) // == 2**i
      }
      for _, value := range pow {
          fmt.Printf("%d\n", value)
      }
  }
  ```

### Maps
* 例  
  ```golang
  package main
  import "fmt"
  
  type Vertex struct {
      Lat, Long float64
  }
  
  var m1 map[string]Vertex
  
  var m2 = map[string]Vertex{
      "Bell Labs": Vertex{
          40.68433, -74.39967,
      },
      "Google": Vertex{
          37.42202, -122.08408,
      },
  }
  
  var m3 = map[string]Vertex{
      "Bell Labs": {40.68433, -74.39967},
      "Google":    {37.42202, -122.08408},
  }
  
  func main() {
      m1 = make(map[string]Vertex)
      m1["Bell Labs"] = Vertex{
          40.68433, -74.39967,
      }
      fmt.Println(m1["Bell Labs"])
      fmt.Println(m2)
      fmt.Println(m3)
      // map操作(追加、更新、削除)
      m := make(map[string]int)
      m["Answer"] = 42
      fmt.Println("The value:", m["Answer"])
      m["Answer"] = 48
      fmt.Println("The value:", m["Answer"])
      delete(m, "Answer")
      fmt.Println("The value:", m["Answer"])
      v, ok := m["Answer"]
      fmt.Println("The value:", v, "Present?", ok)
  
      // ループでまわす
      m4 := map[string]int {
          "Jack": 15,
          "John": 16,
          "Ana": 17,
      }
      for name, age := range(m4) {
          fmt.Println( name, "is", age, "old")
      }
  }
  ```

## 関数
* 例  
  ```golang
  package main

  import (
      "fmt"
      "math"
  )

  func compute(fn func(float64, float64) float64) float64 {
      return fn(3, 4)
  }

  func adder() func(int) int {
      sum := 0
      return func(x int) int {
          sum += x
          return sum
      }
  }

  func main() {
      hypot := func(x, y float64) float64 {
          return math.Sqrt(x*x + y*y)
      }
      fmt.Println(hypot(5, 12))       // 13
  
      fmt.Println(compute(hypot))     // 5
      fmt.Println(compute(math.Pow))  // 81
  
      pos, neg := adder(), adder()
      for i := 0; i < 10; i++ {
          fmt.Println(
              pos(i),
              neg(-2*i),
          )  // 0 0, 1 -2, 3 -6, 6 -12, 15 -30, 21 -42, 28 -56, 36 -72, 45 -90
      }
  }
  ```

## メソッド
* 例  
  ```golang
  package main

  import (
      "fmt"
      "math"
  )

  type Vertex struct {
      X, Y float64
  }
  // メソッド: 型の次に名前を定義
  func (v Vertex) Abs() float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  // 関数: 名前の次に引数を定義
  func Abs(v Vertex) float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }

  type MyFloat float64
  func (f MyFloat) Abs() float64 {
      if f < 0 {
          return float64(-f)
      }
      return float64(f)
  }

  func main() {
      v := Vertex{3, 4}
      fmt.Println(v.Abs())  // 5
      fmt.Println(Abs(v))   // 5
  
      f := MyFloat(-math.Sqrt(2)
      fmt.Println(f.Abs())
  }
  ```
* ポインター渡し  
  ```golang
  package main
  import (
      "fmt"
      "math"
  )
  type Vertex struct {
      X, Y float64
  }
  func (v Vertex) Abs() float64 {
      return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  func (v *Vertex) Scale(f float64) {
      v.X = v.X * f
      v.Y = v.Y * f
  }
  func (v Vertex) Scale2(f float64) {
      v.X = v.X * f
      v.Y = v.Y * f
  }
  func main() {
      v := Vertex{3, 4}
      v.Scale(10)             // v は自動的にポインターに変換され、Scale が呼び出される
      fmt.Println(v.Abs())    // 50(ポインター渡しなので中の値が10倍された)
      v2 := Vertex{3, 4}
      v2.Scale2(10)
      fmt.Println(v2.Abs())   // 5(コピーされた値渡しなので中の値はそのまま)
      v2p := &v2              // v2 のポインターを v2p にセット
      v2p.Scale2(10)
      fmt.Println(v2p.Abs())  // 5(ポインターは値(*v2p)に変換されてから、Scale2 が呼び出される)
      v2p.Scale(10)           // ポインターに対して Scale を呼び出しても、もちろん OK
      fmt.Println(v2p.Abs())
  }
  ```

* 値渡し  
次のような型をそのまま関数やメソッドに渡すと、値渡しになる
  * 数値
  * 構造体
  * 配列
* 参照渡し  
次のような型を関数やメソッドに渡すと、参照渡しになる
  * 文字列
  * インターフェース
  * スライス
  * マップ
  * チャネル
* 注意
  * map に構造体を格納し、取り出す時も値コピーされる。構造体の内容の変更がそのまま map に反映されるようにする場合は、map に構造体の参照を格納する。  
    (例)  
    ```
    persons := make(map[int]*Person)
    persons[1101] = &Person{Name: "hoge"}
    ```
  * slice もほほ同じ。slice から取り出した構造体に対して値を変更し、slice に反映されるようにする場合は、slice に構造体の参照を格納する。  
    (例)  
    ```
    persons := []*Person{ {Name: "hoge"}, {Name: "fuga"} }
    p := persons[0]
    p.Name = "hoge2"
    fmt.Println(persons[0].Name) // hoge2 が表示される。
    ```
    * ただし、slice にインデックスを指定して構造体の要素を変更する場合は、ポインターでなくても slice に反映される。  
      (例)  
      ```
      persons := []Person{ {Name: "hoge"}, {Name: "fuga"} }
      persons[0].Name = "hoge2"
      fmt.Println(persons[0].Name) // hoge2 が表示される。
      ```

## インターフェース
* 例  
  ```golang
  package main

  import (
      "fmt"
  )

  // インターフェースを定義
  type Runner interface {
      run()
  }

  // 型を定義
  type Dog struct {
      name string
  }

  // 型 Dog にインターフェース Runner を実装
  func (dog *Dog) run() {
      if dog == nil {
          fmt.Println( "dog is null")
      } else {
          fmt.Println(dog.name, "running")
      }
  }

  func do(i interface{}) {
      switch v := i.(type) {
      case int:
          fmt.Printf("Twice %v is %v\n", v, v*2)
      case string:
          fmt.Printf("%q is %v bytes long\n", v, len(v))
      default:
          fmt.Printf("I don't know about type %T!\n", v)
      }
  }

  func main() {
      var runner Runner
      var dog *Dog
      pochi := Dog{"Pochi"}
      masaru := Dog{"Masaru"}

      runner = &pochi
      runner.run()        // pochi running

      dog = &masaru
      dog.run()           // masaru running

      dog = nil
      runner = dog
      runner.run()        // dog is null(Dog型に nil をセットしても、run() が呼び出される)

      // runner = nil
      // runner.run()     // <-- runner の型を判定できないため実行時エラー(SIGSEGV)が発生

      // 型チェック(instanceof)
      var i interface{} = "hello"

      s, ok := i.(string)
      fmt.Println(s, ok)  // hello true

      f, ok := i.(float64)
      fmt.Println(f, ok)  // 0 false

      do(21)              // Twice 21 is 42
      do("hello")         // "hello" is 5 bytes long
      do(true)            // I don't know about type bool!
  }
  ```

## エラーハンドリング
### エラー型判定
```golang
package main

import (
    "os"
    "fmt"
)

func main() {
    file, err := os.Open("hoge.txt")
    if err != nil {
        fmt.Printf( "%T\n", err )       // *os.PathError
        fmt.Printf( "%v\n",   err )     // open hoge.txt: no such file or directory
        switch v := err.(type) {
        case *os.PathError:
            fmt.Println( "Path error:", v )    // Path error: open hoge.txt: no such file or directory
        default:
            fmt.Println( "Unknown error:", v )    // この行には分岐しない
        }
        return
    }

    defer file.Close()
}
```

## Goroutines, channel
```golang
package main

import (
    "fmt"
	  "sync"
    "time"
)

// チャネルへの通知で終了判断
func say(s string, id int, ch chan int) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
    ch <- id
}

// チャネルクローズで終了判断
func say2(s string, ch chan int) {
    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
    close(ch)
}

// WaitGroup で終了判断
func say3(s string, wg *sync.WaitGroup) {
    defer wg.Done()

    for i := 0; i < 5; i++ {
        time.Sleep(100 * time.Millisecond)
        fmt.Println(s)
    }
}

func main() {
    ch := make(chan int)
    go say("world", 1, ch)  // Goroutines で実行
    go say("hello", 2, ch)
    fmt.Println(<-ch)       // ch から受信するまで待機
    fmt.Println(<-ch)

    ch2 := make(chan int)
    go say2("ok", ch2)
    // for v := range ch2 { // channel を for でまわす普通の書き方
    //     fmt.Println( v ) // 今回はクローズを待機するだけなので、「v :=」は不要
    // }
    for range ch2 {}        // ch2 がクローズされるまで待機
    fmt.Println("exit")

    wg := sync.WaitGroup{}
    for i := 0; i<3; i++ {
        wg.Add(1)
        go say3("hoge", &wg)
    }
    wg.Wait()
}
```

* ループで channel からデータを受信する  
  ```golang
  for v := range ch {
      // ループ処理
  }
  ```
* ウェイトなしでのチャネルチェックと、クローズチェック  
次の対策  
(1) クローズしたチャネルからデータを取り出そうとすると、エラーで終了する。  
(2) チャネルにデータがない場合、データが追加されるまで待機する。  
  ```
  func hoge(ch chan int) {
    var ok bool
    var number int
    select {
    case number, ok <= ch:
      if !ok {
        fmt.Println("channels was closed")
      } else {
        fmt.PrintF("received number is %d\n", number)
      }
    default:
      fmt.Println("No data in channel")
      time.Sleep(5 * time.Second)
    }
  }
  ```

## パッケージ
* カレントディレクトリ以下のパッケージをインポートする  
`import "./hoge"
* 作成したパッケージに定義したファンクションをエクスポート(外から参照できるように)する  
ファンクション名を大文字で始める  
  ```golang
  package hoge
  func Hoge() int {
      return 1
  }
  ```

## 日付・時刻
* 現在時刻
  ```golang
  package main
  import (
      "fmt"
      "time"
  )
  func main() {
      fmt.Println(time.Now())
  }
  ```
* 解析・フォーマット  
次の値を使って解析・フォーマットする
  * 年=2006, 月=01, 日=02 時=15, 分=04, 秒=05  
  * 時差=-0700, タイムゾーン=MST
  
  ```golang
  package main
  
  import(
      "fmt"
      "time"
      "log"
  )
  
  func main() {
      var str string = "2010/03/12 09:15:30"
      // 年: 2006, 月: 1, 日: 2, 曜日: MON
      // 時: 15, 分: 4, 秒: 5
      // 時差: -0700, タイムゾーン: MST
      dateTime, err := time.Parse( "2006/01/02 15:04:05", str )
      if err != nil {
          log.Fatal( err )
      }
      fmt.Println( dateTime )
  
      fmt.Println( dateTime.Format( "01-02-2006 15:04" ))
      fmt.Println( dateTime.Format( "2006年01月02日 15時04分05秒 MON -0700 MST" ))
  
      if dateTime, err = time.Parse( "2006/01/02 15:04:05.000 MST", "2010/03/12 09:15:30.357 JST" ); err != nil {
          log.Fatal( err )
      }
      fmt.Println( dateTime.Format( "2006年01月02日 15時04分05.000秒 MON -0700 MST" ))
  }
  ```

## ログ出力
* ログ出力する処理が記述されたソースファイル名と行番号を出力する  
`log.SetFlags(log.Ldate | log.Lshortfile)`
* 上記に加えてタイムスタンプを出力する  
`log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile)`
* タイムスタンプをマイクロ秒まで出力する  
`log.SetFlags(log.Ldate | log.Ltime | log.Lmicroseconds | log.Lshortfile)`
* タイムスタンプを UTC で出力する  
`log.SetFlags(log.Ldate | log.Ltime | log.Lmicroseconds | log.LUTC | log.Lshortfile)`

## スリープ(待機)
* time.Sleep を使う  
`time.Sleep(3 * time.Second)`
* time.After を使う  
`<-time.After(3 * time.Second)`
