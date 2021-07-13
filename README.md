# Rotulus

 The aim of this project is to provide a database schema and a seeder to store leaked database the most efficient way.

- If the leaked database contains hashed password it will try to find the corresponding clear text password in the database.

- It creates an error file containing all the records which it haven't been able to parse.

## Pre-requisites
### 0. Install requirements
#### Postgresql
```bash
sudo apt install postgresql libpq-dev
```

#### Python 
```bash
sudo apt install python3 python3-pip
```

#### Python modules
```bash
pip3 install -r requirements.txt
```

### 1. Databse setup

Depending on the data drive, add one of the `conf` files from the `conf` directory to
Postgres' `conf.d` dir.

It's recommended to modify the configuration to feet your hardware.

```bash
sudo cp conf/16gb_4cpu_ssd.conf /etc/postgresql/12/main/conf.d/dump.conf
sudo systemctl restart postgresql@12-main.service
```

### 2. Create PostgreSQL user and database

```bash
sudo -u postgres psql
```

```psql
create database rotulus;
create user rotulususer with encrypted password 'rotuluspassword';
grant all privileges on database rotulus to rotulususer;
```

### 3. Install Rotulus

```bash
pip3 install .
```

### 4. Create Rotulus database configuration file

#### Assisted

```bash
rotulus database -d init
```

#### Manually

A Rotulus database configuration example file is at `conf/rotulus.yml`.

Modify it to fit your database configuration and, copy it where you want to store it.

Then you have to create an environment variable named `ROTULUS_CONF_PATH` with the file path as value.

## Usage

### Databse
`database` module try to read rotulus database configuration file path from `ROTULUS_CONF_PATH` environment variable or read rotulus database configuration from `~/.rotulus.yml`.

#### Create

```bash
rotulus database -d create
```

#### Drop

```bash
rotulus database -d drop
```

#### Reset

```bash
rotulus database -d reset
```

It's like making a drop and a create (`python3 database.py -d drop && python3 database.py -d create`).

### Seeding

#### Input file format
Dump entries should be in the format:

```
email@domain.com<separator>[password|hash]
```

For example:

- With clear text password
```
00zzzz00@mail.ru:alenuska1
```

- With hashed password
```
00zz@mail.ru:503c84b04c107ed207e9b5e07d3fac46
```

#### CLI

- Insert data from one file with `:` separator
```bash
rotulus seeder -f dbleak.txt -s :
```

- Insert data from all file in a directory with `;` separator
```bash
rotulus seeder -f dbleaks/* -s ;
```

- Insert data from one file with `:` separator and hashed password without knowing the type of hash
```bash
rotulus seeder -f dbleak_with_hashed_passwords.txt -s : -a
```
Using a modified version of [hashID](https://github.com/psypanda/hashID) it'll identify for you the type of hash used. **Without a 100% accuracy**.

- Insert data from one file with `:` separator and md5 hashed passwords
```bash
rotulus seeder -f dbleak_with_hashed_passwords.txt -s : -c md5
```

### Query

#### Select usernames which are equal to ...

```bash
rotulus query -s username -e foo
```

#### Select passwords which are equal to ...

```bash
rotulus query -s password -e foo
```

#### Select usernames which contains ...

```bash
rotulus query -s username -c foo
```

#### Select passwords which contains ...

```bash
rotulus query -s password -c foo
```

### Swell

The `swell` module allows:
- Hash all clear password using all hash types present in the database
- Find clear password corresponding to hashed one

#### Hash all clear password using hashes types present in the database
```bash
rotulus swell -a
```

#### Find clear password of a password hash
```bash
rotulus swell -f
```

#### Use case
You have imported a leaked database containing md5 password hashes.

Then, you obtain another leaked database with clear text passwords.

Using `swell` module, you will be able to hash all clear text passwords, from the second leak, to md5.

If md5 password compute from the second leak is found in the first one, you will have the corresponding clear text password !

## Coming soon ...

- [ ] Database migration
- [ ] API
- [ ] Rotulus frontend
- [ ] Cracking passwords autonomously

## Authors
- [**op1um**](https://github.com/0p1um)
- [**w1ld3r**](https://github.com/w1ld3r)

## Support us !
```
BTC: 3KRs9A7CqJChHQfNEeKU2hGCaDYWCzcT7S
XMR: 8BQBQ6hDNKcXzTggFMugLqeoENdA9GgYfXMdp1Lxxtw5ECmzkUTsPYhVMS6oxzYBHU4AdgotDnuTp2RTj98ozdkfKVGBLxa
```
