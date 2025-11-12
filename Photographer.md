# Máquina Photographer
### Reconocimiento de la Ip de la máquina víctima

![alt text](image.png)

### Puertos abiertos

sudo nmap -sS --disable-arp-ping --min-rate 6000 -p- --open -vvv -Pn 192.168.5.193

![alt text](image-1.png)


### Servicios y versiones

sudo nmap -sVC --min-rate 6000 -p22,80 -vvv -Pn 192.168.5.193

![alt text](image-2.png)

### Fuzzing Web

- Directorios

feroxbuster --url http://192.168.5.193/ -w /usr/share/seclists/Discovery/Web-Content/big.txt

![alt text](image-3.png)

feroxbuster --url http://192.168.5.193/admin -w /usr/share/seclists/Discovery/Web-Content/big.txt -x pcap,php,txt,zip,htm,html,phar,sql.gz,sql.zip,sql,kdbx,db,sqlite,sqlite3,mdb,accdb,frm,ibd,dbf,bak,json,bson,couch,rdb,aof,nsf,leveldb,ldb,csv,xml,dump,md -d 5 --threads 50

![alt text](image-4.png)

analicé la redirección a index.php porque tiene peso diferente



### Explotación 

Utilizando burpsuite pude saltar la redirección de admin.php -> index.php

![alt text](image-5.png)

![alt text](image-6.png)

![alt text](image-7.png)

![alt text](image-8.png)

![alt text](image-9.png)

solo me permiten subir imagenes, entonces hago XXE injection en imágnes SVG

![alt text](image-10.png)

ahora trato de obtener el contenido del archivo db.php

![alt text](image-11.png)

es encriptación base64

![alt text](image-12.png)

me conecté por ssh con el usuario ethan

![alt text](image-13.png)

### Escalar Privilegios

![alt text](image-14.png)

guardo el id_rsa:

![alt text](image-15.png)

### user.txt

![alt text](image-16.png)

### root.txt

![alt text](image-17.png)