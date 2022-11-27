# despligue-full-stack

## Aclaraciones

### Los pasos
Estos pasos están planteados como apoyo. Pueden no ser suficiente o necesitar de aclaraciones para seguirlos por tu cuenta.

### Los comandos
Si no se especifica, los comandos son para ejecutar en el terminadl de la instancia.

Cuando nos referimos al terminal local (el de tu PC), aparece indicado.

### Para el despliegue
Se recomienda crear una carpeta para el despliegue con todo lo necesario y abrir el terminal en esa carpeta. Así no habrá que escribir rutas largas y se puede usar simplemente ./nombredearchivo.

La carpeta debería contener:
docker-compose.yml
httpd.conf
api-banco.war
carpeta www (con el contenido del frontend: HTML + CSS + JS). El index debe estar justo en www/index.html.
clave .pem de la instancia Lightsail (Accound > Account> SSH keys)

### La clave
La clave pem solo debe ser accesible por ti. Puede que necesites ajustar los permisos del fichero .pem o utilizar sudo su (linux/mac) o abrir la terminal en administrador (windows).

En Windows hay que usar Powershell como terminal.

### Los servicios

Si solo quieres desplegar la API puedes eliminar del docker-compose el servicio de front (httpd)

### Los puertos

Deberás habilitar los puertos necesarios (8080, 8081 y 3306) en la pestaña Networking de tu instancia.

El 80 (HTTP) y 22 (SSH) ya vienen habilitados para que puedas conectarte.



Pasos: 

## 1. crear cuenta aws
## 2. crear instancia de Lightsail
	región: París
	solo os
	amazon linux 2
	descargar clave ssh (.pem)
## 3. conectarse por terminal
## 4. actualizar repositorio actualizaciones
	sudo yum update -y
## 5. instalar docker
	sudo amazon-linux-extras install docker
	indicar "y" para confirmar
	iniciar docker
		sudo service docker start
## 6. configurar docker al arranque
	sudo systemctl enable docker
## 7. añadir usuario ec2-user al grupo de docker
	sudo usermod -a -G docker ec2-user
	comprobar que funciona sin sudo
		docker info
## 8. crear directorios /despliegue /httpd y /www en home de ec2-user
	cd
	mkdir despliegue httpd www
## 9. compilar la API como .war con mvmw
	Terminal local:
	 	.\mvnw package -Dmaven.test.skip
## 10. añadir el war resultante a /despliegue, en nuestra instancia
	Terminal local:
		scp -i [clave ssh].pem [api compilada].war ec2-user@[ip-instancia]:~/despliegue/[api compilada].war
## 11. descargar docker-compose
	sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
## 12. ajustar permisos de ejecución de docker-compose
	sudo chmod +x /usr/local/bin/docker-compose
	comprobar que funciona
		docker-compose version
## 13. subir httpd.conf a /httpd
	Terminal local:
		scp -i [clave ssh].pem ./httpd.conf ec2-user@[ip-instancia]:~/httpd/httpd.conf
## 14. subir el front a /www
	Terminal local:
		scp -r -i [clave ssh].pem ./www ec2-user@[ip-instancia]:~/www
## 15. añadir el docker-compose.yml a la home de ec2-user
	revisar el nombre del .war antes de subirlo
	Terminal local:
		scp -i [clave ssh].pem ./docker-compose.yml ec2-user@[ip-instancia]:~/docker-compose.yml
## 16. levantar los servicios
	docker-compose up -d
