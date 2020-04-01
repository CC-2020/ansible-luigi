# Tarea Cloud Computing - Asible


- Crear una máquina virtual
- Instalar Ansible
- Crear dos máquinas virtuales
- Crear un inventario con esas máquinas
- Crear un PlayBook para instalar Apache, PHP y MySQL


## Instalar Ansible sobre la maquina master


```bash
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt install ansible
```

## Crear dos máquinas virtuales y configuración

Desde la plataforma __Google GCP__ - __Compute Engine__, se han creado dos máquinas, en la red de default : 

- __client1-tarea__: ubuntu18.04, us-central1-a, private ip: 10.128.*.*, public ip: 34.68.181.**
- __client2-tarea__: debian-9, us-central1-a, private ip: 10.128.*.* , public ip: 35.239.24.***

## Creación de la clave SSH

Desde la máquina master, procedemos con la creación de la clave SSH, que nos permitirá de controlar las máquinas __client1-tarea__, __client2-tarea__.

```bash
$ ssh-keygen
```
enter tres veces, por simplicidad, sin establecer una contraseña; entonces creamos la clave pública y la clave privada.
Desde cat, ver y copiar la clave pública:

```bash
$ cd .ssh
$ cat id_rsa.pub
``` 

Desde __Google Cloud Platform__ -> __Compute Engine__ -> __Instancias__ -> hacer clic en una de las dos instancias creadas anteriormente, p.e. __cliente1-tarea__, luego en "__editar__", en "__clave SSH__", pegamos la clave pública de la máquina master.

Lo mismo para la otra maquina.

Podemos verificar la conexión / configuración correcta, intentando conectar, desde la máquina master, a la máquina client1-tarea o client2-tarea:


```bash
$ ssh 34.68.181.**
``` 

## Crear un inventario con esas máquinas

Podemos modificar el fichero __/etc/ansible/hosts__ o crear un archivo __"hosts-dev"__, con:

```bash
# Por defecto /etc/ansible/hosts
# hosts-dev

[webservers]
webapp1 ansible_host=34.68.181.**
webapp2 ansible_host=35.239.24.***

[local]
control ansible_connection=local
``` 


aquí indicamos la dirección pública de las dos máquinas que controlaremos.

Podemos verificar la conexión / configuración correcta haciendo un ping:


```bash
$ ansible webservers -m ping
``` 

Asì veremos el mensaje con la respuesta de las dos máquinas.


## Crear un PlayBook para instalar Apache, PHP y MySQL

- Creemos el archivo __apache_php_mysql.yml__ (desde CLI o cloud shell editor):


```bash
---
- hosts: webservers
  tasks:
    - name: apache_inst
      become: true
      apt: name=apache2
    - name: mysql_inst
      become: true
      apt: name=mysql-server
    - name: php_inst
      become: true
      apt: name=php
    - name: php-mysql_inst
      become: true
      apt: name=php-mysql
```


- Por último, ejecutar : 

```bash
$ ansible-playbook apache_php_mysql.yml
```

Así, habremos instalado Apache, PHP y MYSQL sobre las dos máquinas.