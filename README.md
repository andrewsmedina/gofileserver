# gofileserver
The objective of this project is purely didactic, it is an attempt to solve the problem of competition and parallelism that the project will assume with uploading, reading and downloading files on disk and for the cloud.

Simple RESTFUL API demo server written in Go (golang) in order to solve problems of recording, uploading and downloading files to disk and the Amazon cloud using S3, Google Cloud or any other cloud service that you want to implement, using postgresql as Database and token authentication.
The services of amazon s3 were first implemented.

## Used libraries:
- https://github.com/lib/pq - Sql Database.
- https://gopkg.in/gcfg.v1 - Text-based configuration files with "name=value" pairs grouped into sections (gcfg files).
- https://github.com/aws/aws-sdk-go/aws - Package sdk Aws Amazon
- https://github.com/gorilla/mux - Implements a request router and dispatcher for matching incoming requests

---
* [A small summary](#summary)
* [Install](#install)
* [Structure](#structure)
* [Run](#runprogram)
* [Examples Client](#examples-client)
* [Register User](#register-user)
* [Upload File](#upload-files)
* [Download Files](#download-files)

## A small summary operating system

* [Gofileserver.go]

This program is responsible for managing and controlling our APIS.

The upload is done on the local server always, there is a configuration file to determine some features of the config.gcfg system.
Everything is recorded in the database.
Authentication for upload upload was done in 2 ways.

The download checks if the file is local or if it has already been sent to the cloud, if it is still on the local server the system will download locally, otherwise it checks on which cloud server it is to download from the cloud.

The register and token is responsible for creating users and receiving the access token, and also with the possibility to view the tokem with username and password.


* [Gofileupload.go]

This program is responsible for downloading the files that are found on the local server to the cloud, the system evaluates the availability and uploads to the cloud.

It is not deleted files locally, they are deleted in a second time for security, they are checked if it really is in the cloud before physically removes them.

* [Gofileremove.go]

This program is responsible for monitoring the files on disk so they can be removed, it checks in the cloud if the object is actually there, and if it does remove it from the disk physically.

* [Goserversite.go]

This program is just an attempt to create an interface to simulate what the client will see how the files on our platform are.

## Install

With a [correctly configured](https://golang.org/doc/code.html#Workspaces) Go toolchain:

```sh
go get -u github.com/lib/pq
go get -u gopkg.in/gcfg.v1
go get -u github.com/aws/aws-sdk-go/aws
go get -u github.com/gorilla/mux
go get -u github.com/jeffotoni/gofileserver
```

Configuring the environment to run sdk amazon API
[`SDK Examples`] (https://github.com/aws/aws-sdk-go/tree/master/example)

```
mkdir ~/.aws/
vim ~/.aws/config

[default]
region = us-east-1
output = 

vim ~/.aws/credentials

[default]
aws_access_key_id = AKIX1234567890
aws_secret_access_key = MY-SECRET-KEY
```

Creating postgresql database

```sh
createuser ukkobox -U postgres
psql -d template1 -U postgres
psql=> alter user ukkobox password 'pass123'
createdb ukkobox -U postgres -O ukkobox -E UTF-8 -T template0
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

	gofileserver.go	
	Server to register users, receive and send files to the file server
	

	gofileupload.go
	Scans all local structure and sends the files to servers in the 
	cloud: Cloud Google, Amazon, DigitalOcean
	

	gofileremove.go
	Scrolls the structure checks whether the files are safe in the 
	cloud and removes the location
	
	goserversite.go
	A template under construction so customers can manage, view, download 
	their uploaded as an online bucket
	
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



