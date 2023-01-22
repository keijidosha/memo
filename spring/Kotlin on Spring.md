# Kotlin on Spring

Table of Contents
=================

* [Tips](#tips)
    * [コンストラクタ・インジェクション](#コンストラクタインジェクション)
    * [モデルを data class として作成する](#モデルを-data-class-として作成する)
    * [モデルにPKなど自動採番するフィールドがあり、データ追加時に次のようにコンストラクタにパラメータ指定できない場合](#モデルにpkなど自動採番するフィールドがありデータ追加時に次のようにコンストラクタにパラメータ指定できない場合)
    * [Model.addAttribute() を map への代入のように書く。](#modeladdattribute-を-map-への代入のように書く)
* [Trouble Shooting](#trouble-shooting)
    * [data class のコンストラクターに指定したパラメーターが @OneToMany、@ManyToOne で Entity を相互参照していると、無限ループで Stack Overflow が発生する。](#data-class-のコンストラクターに指定したパラメーターが-onetomanymanytoone-で-entity-を相互参照していると無限ループで-stack-overflow-が発生する)

Created by [gh-md-toc](https://github.com/ekalinin/github-markdown-toc)

### Tips
#### コンストラクタ・インジェクション
普通にプライマリ・コンストラクタに必要なクラスをパラメータ指定すればよさそう。  
@Component を指定しておくと、自動的に AutoWired してくれる?
```
import org.springframework.stereotype.Component

@Component
class HogeComponent(val repository: HogeRepository) {
    // 処理記述
}
```

#### モデルを data class として作成する
コンストラクタのパラメーターにアノテーションを指定する場合は、フィールドに対するアノテーションであることを示すため @field を使う。  
Nullable であることを示すため、型指定に ? を付ける。  
=> Nullable でないと「NullPointerException: Parameter specified as non-null is null」というエラーがスローされる。
```
data class Person(
    @field: NotBlank
    @field: Size(max = 16)
    val name: String?,
    @field: Min(0)
    val age: Int?
)
```

#### モデルにPKなど自動採番するフィールドがあり、データ追加時に次のようにコンストラクタにパラメータ指定できない場合

```
var person = Person()
person.name = "太郎"
repository.save(person)
```

(案1) プライマリコンストラクタには普通にフィールドを宣言し、引数なしのセカンダリコンストラクタを用意してプライマリコンストラクタに null を渡す。
```
@Entity
data class Person(
    @field: Id
    @field: GeneratedValue
    var id: Long?,
    @field: NotBlank
    @field: Size(max = 16)
    var name: String?
) {
    constructor(): this(null, null)
}
```

(案2) data class をあきらめ、普通のクラスを用意する。
```
@Entity
class Person {
    @Id
    @GeneratedValue
    var id: Long? = null

    @NotBlank
    @Size(max = 40)
    var name: String? = null
}
```

#### Model.addAttribute() を map への代入のように書く。
Java で次のように書くところを
```
model.addAttribute("Persons", repository.findAll());
```
Kotlin で次のように書くには
```
model["comments"] = repository.findAll()
```
次のインポート文を追加する。
```
import org.springframework.ui.set
```

### Trouble Shooting
#### data class のコンストラクターに指定したパラメーターが @OneToMany、@ManyToOne で Entity を相互参照していると、無限ループで Stack Overflow が発生する。
Department.kt
```
@Entity
data class Department(
    @field: Id
    @field: GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long?,
    @field: NotBlank
    @field: Size(max = 40)
    val name: String?,
    @field: OneToMany(mappedBy = "department")
    val employees: List<Employee>?,
)
```

Employee.kt
```
@Entity
data class Employee(
    @field: Id
    @field: GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long?,
    @field: NotBlank
    @field: Size(max = 40)
    val name: String?,
    @field: ManyToOne
    var department: Department?
)
```

回避策 => @ManyToOne しているフィールドを data class のコンストラクタのパラメーターからはずす。
```
@Entity
data class Employee(
    @field: Id
    @field: GeneratedValue(strategy = GenerationType.IDENTITY)
    val id: Long?,
    @field: NotBlank
    @field: Size(max = 40)
    val name: String?,
) {
    @ManyToOne
    var department: Department? = null
}
```
これにより、Kotlin コンパイラーが自動的に getter を生成しなくなり、無限ループが回避される?  
いっそ data class を諦め、普通の class にした方がよい?
```
@Entity
class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    var id: Long? = null
    @NotBlank
    @Size(max = 40)
    var name: String? = null
    @ManyToOne
    var department: Department? = null
}
```

(参考)  
[Spring Data JPAのEntityの相互参照とその注意点](https://qiita.com/frost_star/items/855e7fb52dca9de7566e#循環参照の無限展開回避)  
[@OneToManyで相互参照したEntityをThymeleafを使ってJavaScript内で呼び出すとStackOverFlowする件](https://qawsedrftgyhujiko.hatenablog.com/entry/2015/09/26/005935)  
[One to Many / Many to one relationship isn't saved in Kotlin, while it is in Java](https://stackoverflow.com/questions/73415885/one-to-many-many-to-one-relationship-isnt-saved-in-kotlin-while-it-is-in-jav)  
[Kotlin - Data class entity throws StackOverflowError](https://stackoverflow.com/questions/48926704/kotlin-data-class-entity-throws-stackoverflowerror)
