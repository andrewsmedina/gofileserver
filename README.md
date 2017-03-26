# gofileserver
Simple RESTFUL API demo server written on Go (golang) user Aws S3, postgresql
Authorization and authentication with token
Api designed to upload, download files to your local server and then download 
to cloud servers, the first to be implemented was aws s3
Call authorization required, Upload, Download, register new user

* It implements the `Upload files` interface so it is compatible with the standard `http.ServeMux`.
* It Implements the Download files based on URL host


## Used libraries:
- https://github.com/lib/pq - Sql Database.
- https://gopkg.in/gcfg.v1 - Text-based configuration files with "name=value" pairs grouped into sections (gcfg files).
- https://github.com/aws/aws-sdk-go/aws - Package sdk Aws Amazon
- https://github.com/gorilla/mux - Implements a request router and dispatcher for matching incoming requests

---

* [Install](#install)
* [Structure](#structure)
* [Run](#runprogram)
* [Examples Client](#examples-client)
* [Register User](#register-user)
* [Upload File](#upload-files)
* [Download Files](#download-files)

## Install

With a [correctly configured](https://golang.org/doc/install#testing) Go toolchain:

```sh
go get -u github.com/lib/pq
go get -u gopkg.in/gcfg.v1
go get -u github.com/aws/aws-sdk-go/aws
go get -u github.com/aws/aws-sdk-go/service
go get -u github.com/gorilla/mux
go get -u github.com/jeffotoni/gofileserver
```

Configuring the environment to run sdk amazon API

```sh
mkdir ~/.aws/
vim ~/.aws/config
[default]
region = us-east-1
output = 

vim ~/.aws/credentials
[default]
aws_access_key_id = Put your Id here
aws_secret_access_key = Put your key 
```

Creating postgresql database

```sh
create database ukkobox -U postgres -O user -E UTF-8 -T template0
psql ukkobox -U postgres -f tables/ukkobox.sql
```

Edit the configuration file

```sh
vim config/config.gcfg
```

## Structure of the program
```go
- gofileserver
	- config
		- config.go
		- config.gcfg
	- libs
		- gcheck.go
	- postgres
		- connection
			connection.go
	- tables
		ukkobox.sql
	- uploads
	- views
		index.html

	Server for receiving and sending files	
	gofileserver.go

	Scans all local structure and sends the files to servers in the cloud: Cloud Google, Amazon, DigitalOcean
	gofileupload.go

	Scrolls the structure checks whether the files are safe in the cloud and removes the location
	gofileremove.go
	
```
## Run the program

```go
go run gofileserver.go 

Conect port : 4001
Conect database:  ukkobox
Database User:  ukkobox
Instance /register
Instance /token
Instance /upload
Instance /download

```

```go
go build gofileserver.go 
```

## Examples client

Register user and receive access key 

Using Curl - Sending in json format

```sh
curl -X POST --data '{"name":"jeff","email":"mail@your.com","password":"321"}' -H "Content-Type:application/json" http://localhost:4001/register
```

Using Curl - Access token

```sh
curl -X POST --data '{"email":"jeff1@gmail.com","password":"321"}' -H "Content-Type:application/json" http://localhost:4001/token
```

Uploading with Authorization
```sh
curl -H 'Authorization:bc8ca54ebabc6f3da724e923fef79238' --form fileupload=@nameFile.bz2 http://localhost:4001/upload
```

Uploading with acesskey

```sh
curl -F 'acesskey:bc8ca54ebabc6f3da724e923fef79238' --form fileupload=@nameFile.bz2 http://localhost:4001/upload
```

Download only Authorization

```sh
curl -H 'Authorization:bc8ca54ebabc6f3da724e923fef79238' -O http://localhost:4001/download/nameFile.bz2
```
