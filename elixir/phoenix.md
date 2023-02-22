# Phoenix

```bash
mkdir -p ~/Documents/pgsql/db
cd ~/Documents/pgsql/db
initdb ./pgsql -E utf8
pg_ctl -D /Users/okumura/Documents/pgsql/db/pgsql -l logfile start

mkdir -p ~/Documents/elixir/phoenix
cd ~/Documents/elixir/phoenix
mix phoenix.new example
cd example/
npm install
mix ecto.create
mix phoenix.server
```
