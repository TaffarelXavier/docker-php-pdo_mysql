# docker-php-pdo_mysql
Docker com PHP, PDO e o driver do mysql
## 1 Passo)
Criar uma pasta qualquer.

```bash
sudo mkdir demopdo-mysql
```

## 2 Passo)
Criar o arquivo ``Dockerfile``

```yaml
FROM php:7.2-apache

RUN docker-php-ext-install mysqli pdo pdo_mysql

COPY ./www /var/www/html/
 ```
 ### ``www`` é uma pasta dentro da pasta ``demopdo-mysql``
 
 ## 3 Passo)
Criar o arquivo ``docker-compose.yml`` com o seguinte conteúdo:
``` bash
version: "3"
services:
  apache:
    build: .
    container_name: php
    restart: always
    ports:
      - "80:80"
    volumes:
      - ./www:/var/www/html
    depends_on:
      - mysqldb
    links:
      - mysqldb
  mysqldb:
    container_name: mysql
    image: mysql:5.6
    volumes:
      - ./data:/var/lib/mysql
    restart: always
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=chkdsk
      - MYSQL_DATABASE=ltai_db
      - MYSQL_USER=root
      - MYSQL_PASSWORD=chkdsk
```
- MYSQL_USER=root
- MYSQL_PASSWORD=chkdsk são necessários para criar um outro usuário.

É necessário criar as pastas ``./data`` e ``./www``

 ## 4 Passo)
 Executar os camandos: 
 ```docker build . && docker-compose build```
 
 ## 5 Passo)
 Criar, dentro da pasta ``www``, o arquivo ``index.php``;
 Dentro dele, adicione este conteúdo:
 ```php
 
 <?php
var_dump(PDO::getAvailableDrivers());

$dsn = "mysql:host=mysqldb;"; //mysqldb é encontrado no arquivo docker-compose.yml
$dbuser = "root";
$dbpass = "chkdsk";


try{
    $pdo = new PDO($dsn,$dbuser, $dbpass);
    echo "Conectado!";

}catch (PDOException $e){
    echo $e->getMessage();
}
 ```
 
### A saída deverá ser:
``` php
array(2) { [0]=> string(6) "sqlite" [1]=> string(5) "mysql" }
Conectado
```
...e as informações do phpinfo()
