# Import the repository signing key:
```bash
sudo apt install curl ca-certificates
sudo install -d /usr/share/postgresql-common/pgdg
sudo curl -o /usr/share/postgresql-common/pgdg/apt.postgresql.org.asc --fail https://www.postgresql.org/media/keys/ACCC4CF8.asc
```
# Create the repository configuration file:

```bash
. /etc/os-release
sudo sh -c "echo 'deb [signed-by=/usr/share/postgresql-common/pgdg/apt.postgresql.org.asc] https://apt.postgresql.org/pub/repos/apt $VERSION_CODENAME-pgdg main' > /etc/apt/sources.list.d/pgdg.list"
```

# Update the package lists and install:
```bash 
sudo apt update
sudo apt install postgresql-client-17
```

# Export
```bash
pg_dump "postgresql://<u>:<p>@<ip>:5432/<db>" -t <table> -Fc -v > dump.custom
```
___Note: for multiple table `-t <table> -t <table>`___

# To Check Dump File
```bash
pg_restore --list dump.custom
```
# Import
```bash
pg_restore --host=<ip> --port=5432 --username=postgres --dbname=<database> --verbose --jobs=4 dump.custom
```
