# despligue-full-stack

## 1. crear cuenta aws
## 2. crear instancia de Lightsail
	francia
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
	configurar docker al inicio
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
	Powershell
	 	.\mvnw.cmd package -Dmaven.test.skip
## 10. añadir el war resultante a /despliegue, en nuestra instancia
	Powershell
		scp -i [clave ssh].pem [api compilada].war ec2-user@[ip-instancia]:~/despliegue/[api compilada].war
## 11. descargar docker-compose
	sudo curl -L https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
## 12. ajustar permisos de ejecución de docker-compose
	sudo chmod +x /usr/local/bin/docker-compose
	comprobar que funciona
		docker-compose version
## 13. subir httpd.conf a /httpd
	Powershell
		scp -i [clave ssh].pem ./httpd.conf ec2-user@[ip-instancia]:~/httpd/httpd.conf
## 14. subir el front a /www
	Powershell
		scp -r -i [clave ssh].pem ./www/ ec2-user@[ip-instancia]:~/www/
## 15. añadir el docker-compose.yml a la home de ec2-user
	revisar el nombre del .war antes de subirlo
	scp -i [clave ssh].pem ./docker-compose.yml ec2-user@[ip-instancia]:~/docker-compose.yml
## 16. levantar los servicios
	docker-compose up -d
