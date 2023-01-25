# model/ActiveRecord

## 作成・削除

* 作成  
`rails generate model Post user:string href:string description:text hash:string tag:string time:datetime`
* 削除  
`rails destroy model Post`

## modelで指定可能なデータ型

* binary
* boolean
* date
* datetime
* decimal
* float
* integer
* primary_key
* string
* text
* time
* timestamp

# マイグレーション

* カラム追加  
  ```
  rails generate migration add_<カラム名>_to_<テーブル名> <カラム名>:<データ型>
  または
  rails generate migration Add<カラム名>To<テーブル名> <カラム名>:<データ型>
  ```
  (例)  
  `rails g migration add_name_to_users name:string`
* カラム削除  
`rails generate migration remove_<カラム名>_to_<テーブル名> <カラム名>`  
(例)  
`rails generate migration remove_name_to_users name`
* カラムのデータ型変更  
`rails generate migration change_datatype_<カラム名>_of_<テーブル名>`  
(例)  
`rails generate migration change_datatype_name_of_users`  
* カラムのリネーム  
`rails generate migration Rename<カラム名>To<テーブル名>`  
rails generate migration RenameAddressToUser  
マイグレーション作成後にエディターで開いて編集し、変更後のカラム名を記述  
`rename_column :<テーブル名>, :<カラム名>, :<変更後のカラム名>`  
* 反映(実行)  
`rake db:migrate`  
Production環境に反映  
`rake db:migrate RAILS_ENV=production`  
* 履歴確認  
`rake db:migrate:status`  
* 履歴確認  
`rake db:version`  
* その他  
rake db:migrate:up VERSION=[バージョン番号]  
rake db:migrate:down VERSION=[バージョン番号]  
* ロールバック  
  * 1つだけロールバック  
    `rake db:rollback`  
  * 3つロールバック  
    `rake db:rollback STEP=3`  
  * 履歴を確認  
    `rake db:version`  
  * バージョン3に戻す  
    `rake db:rollback VERSION=3`  
* DB再構築  
  * scheme.rb を使って一気に再構築  
    `rake db:reset`  
  * migrate ディレクトリにある履歴を使って順に再構築  
    `rake db:migrate:reset`  


## MySQLでの型指定

```
class CreateTest < ActiveRecord::Migration
  def change
    create_table :tests do |t|
      t.integer :status, limit: 1
    end
  end
end
```

|   |   |
|---|---|
| TINYINT   | 1 |
| SMALLINT  | 2 |
| MEDIUMINT | 3 |
| INT       | 4〜6 |
| BIGINT    | 5〜8 |

| Migration | MySQL | Ruby |
|-----------|-------|------|
| integer   | int(11) | Fixnum |
| decimal   | decimal(10,0) | BigDecimal |
| float     | float | Float |
| string    | varchar(255) | String |
| text      | text | String |
| binary    | blob | String |
| date      | date | Date |
| datetime  | datetime | Time |
| timestamp | datetime | Time |
| time      | time | Time |
| boolean   | tinyint(1) | TrueClass/FalseClass |

## Association

### 多対多

(例)  
Customer -> Order -> Item  
~~青文字は Customer から Item を参照するために設定~~  
~~背景黄色は Item から Customer を参照するために追加設定~~  
Customer  
```
class Customer < ActiveRecord::Base
 has_many :orders                     // Customer => Item
 has_many :items, through: :orders    // Customer => Item

 scope :has_item_id, -> item_id {              // Item => Customer
 joins(:items).where('items.id = ?', item_id)  // Item => Customer
 }
end
```

Order
```
class Order < ActiveRecord::Base
  belongs_to :customer  // Customer => Item
  belongs_to :item      // Customer => Item
end
```

Item
```
class Item < ActiveRecord::Base
  has_many :orders                        // Customer => Item
  has_many :customers, through: :orders   // Customer => Item
  delegate :customer, to: :order          // Item => Customer
end
```


## Validator

* Validator を自作する  
  Rails.root 直下に lib/validates/ ディレクトリを作成  
  `mkdir lib/validates`  
  config/application.rb にパスを追加  
  `config.autoload_paths << Rails.root.join('lib', 'validates')`  
  lib/validates/ にバリデートクラスを作成
  ```
  class HakakuValidator < ActiveModel::EachValidator
    def validate_each(record, attribute, value)
      unless value.to_s =~ /^[ -~]*$/
        record.errors[attribute] << "は半角文字で入力してください。"
      end
    end
  end
  ```
  モデルクラスでバリデーターを適用。クラス名の 1文字目を小文字にして、Validator は付けない。2つ目の大文字はアンダースコアで区切って小文字にする。  
  `validates :name, hankaku: true`  
* 別の Validator を使ってチェックする  
  別の Validator を new する時、引数に options を渡しますが、options は freeze されているため、merge して渡す。  
  ```
  class ItemValidator < ActiveModel::EachValidator
    def validate_each(record, attribute, value)
      validator = HogeValidator.new(options.merge(attributes: :name))
      validator.validate_each(record, attribute, value.to_s)
    end
  end
  ```
* 条件指定する  
`validates :address, unless: Proc.new{ |a| a.name.blank? }`  
http://edgeguides.rubyonrails.org/active_record_validations.html#using-a-symbol-with-if-and-unless

