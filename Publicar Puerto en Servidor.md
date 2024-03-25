## Si tienes un servidor y quieres publicar proyecto web funcionando en un docker
 Ubuntu 22.04
Suponiendo que el servicio está activo en el contenedor.

#### Obtener la IP del contenedor: 
```
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nombre_del_contenedor
```

#### Dar autorización al contenedor (con la IP obtenida antes) OJO que en este ejemplo puse puerto 9001, 
porque justamente estaba tratando de publicar el contenedor que sale por el puerto 9001. Obviamente, tú puedes 
poner el puerto que desees o de acuerdo a la disponibilidad de puertos de tu servidor. 
```
sudo iptables -A INPUT -p tcp --dport 9001 -m iprange --src-range direccion_IP_contenedor -j ACCEPT
```

#### Guardar los cambios en iptables:
```
sudo iptables-save > /etc/iptables/rules.v4
```

### Si no tienes el directorio iptables lo creas y vuelves a guardar. aquí los comandos
```
sudo mkdir -p /etc/iptables/
sudo touch /etc/iptables/rules.v4
```

#### Anexo:
 El proyecto web lo puse en un docker y lo serví con nginx. Dejo el Dockerfile 
 El proyecto contiene archivos js, css, html e imágenes png

######### DOCKERFILE ################
                                                               
#### # usa imagen base con servidor nginx
```
FROM nginx:latest
```

#### # copia archivos del proyecto al directorio de trabajo en el contenedor
```
COPY . /usr/share/nginx/html
```

#### # expone el puerto 9001 para que el contenedor pueda ser accedido desde fuera. desde dentro el servidor trabaja con el puerto 80. 
```
EXPOSE 9001
```

#######################################

#### Finalmente, para crear la imagen
```
docker build -t nombre_imagen .
```

#### levantar el contenedor en el puerto 9001
```
docker run -d -p 9001:80 id_contenedor
```

