# SQLite3

## 実装

```
require "sqlite3"

db = SQLite3::Database.new("calllog.sqlite3")

sql = <<SQL
create table items(
  id integer primary key autoincrement not null,
  name varchar,
  price integer
  );
SQL

db.execute(sql)

sql = "select id, name, price from items"
rows = @db.execute(sql)
rows.each do |row|
  puts "id=#{row[0]}"
  puts "name=#{row[1]}"
  puts "price=#{row[2]}"
end

@db.transaction do
  sql = "INSERT INTO items('name', 'price') VALUES(?,?)"
  @db.execute(sql, 'ballpoint pen', 100)
  sql = "UPDATE items SET price = ? where id=?"
  @db.execute(sql, 150, 1)
end

@db.execute("delete from items")
@db.execute("vacuum")

db.close
```
