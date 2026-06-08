
On PostgreSQL database to change user password postgres
write command
before that we need to connect to psql using
default password for postgres user is  `postgres`

```shell
psql -h localhost -U postgres
```

```sql
alter user postgres with password 'new_password';
```
