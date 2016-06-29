#Postgres

different username & database for each project

## Run
`psql`

create user & database for each new project

create user:
`CREATE USER simple-user WITH PASSWORD 'simple-pass';`

create db:
`create database simple_db with owner simple_user;`

list users:
`\du`

list databases
`\db`

change password
`ALTER USER "mhw" with password 'BURAGmELIA';`

edit authentication methods for postgres server
`vi /Users/mhw/Library/Application\ Support/Postgres/var-9.5/pg_hba.conf`

after doing this, you need to restart the server:

```bash
brew services stop postgresql
brew services start postgres
```

or:

```bash
brew services restart postgresql
```


create config files/data directory:
`initdb -D`

list databases:
`\list`

