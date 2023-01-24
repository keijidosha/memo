# Ruby: MySQL/MariaDB

## 準備

`gem install mysql2`

## 実装

```
require "mysql2"

# DB Connect
db = Mysql2::Client.new(host: "localhost", username: "testuser", password: "test", database: "testdb")

# Query
sql = %q{SELECT * FROM items}
result = db.query(sql)
result.each{|row|
  p row
}

# Insert with transaction
sql = %q{INSERT INTO items(item_id, item_name, created_at, updated_at) VALUES(?,?,?,?)}
begin
  db.query("START TRANSACTION")
  statement = db.prepare(sql)
  ret = statement.execute(10111, "book", Time.now, Time.now)
rescue
  db.query("ROLLBACK")
else
  db.query("COMMIT")
end
```
