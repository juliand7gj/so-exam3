### Universidad Icesi <br />

# Informe Examen 2 <br />

**Nombre:** Luis Fernando Rosales Cadena <br />
**Código:** A00315320 <br />

**URL repositorio:** https://github.com/luisR0425/so-exam3 <br />

Para la instalación de sensu en el lado del servidor se uso la guia del repositorio https://github.com/ICESI/so-monitoring/tree/master/server donde se ejecutan los comandos del script install.sh y luego agregar o editar los archivos de configuración de la carpeta etc/sensu se debe tener en cuenta editar el archivo de uchiwa.json<br />

```json
{
    "sensu": [
        {
            "name": "sensu",
            "host": "localhost",
            "ssl": false,
            "port": 4567,
            "path": "",
            "timeout": 5000
        }
    ],
    "uchiwa": {
        "port": 3000,
        "stats": 10,
        "refresh": 10
    }
}
```

Del lado del cliente se deben ejecutar los siguientes comandos:

``` 
sudo yum install screen vim -y

echo '[sensu]
name=sensu
baseurl=https://sensu.global.ssl.fastly.net/yum/$releasever/$basearch/
gpgcheck=0
enabled=1' | sudo tee /etc/yum.repos.d/sensu.repo

yum install sensu -y
sensu-install -p sensu-plugin
yum install httpd -y
service sensu-client start
``` 
Luego editar el json client.json que esta en client/etc/sensu/conf.d/

```json
{
  "client": {
    "name": "centos-client1",
    "address": "192.168.1.17",
    "subscriptions": ["webservers"]
  }
}
```

Y el json rabbitmq.json del mismo archivo.

```json
{
  "rabbitmq": {
    "host": "192.168.1.14",
    "port": 5672,
    "vhost": "/sensu",
    "user": "sensu",
    "password": "password",
    "heartbeat": 10,
    "prefetch": 50
  }
}
```

Y agregamos el script de /etc/sensu/plugins/check-apache.rb

```
#!/usr/bin/env ruby

procs = `ps aux`
running = false
procs.each_line do |proc|
  running = true if proc.include?('httpd')
end
if running
  puts 'OK - Apache daemon is running'
  exit 0
else
  puts 'WARNING - Apache daemon is NOT running'
  exit 1
end
```

Se ejecuta el siguiente comando para que el cliente se ejecute en el servidor

```
service sensu-client start
```

Por parte del servidor, para ejecutar los servicios se tienen los siguientes comandos:

```
service sensu-client start
service sensu-server restart & service sensu-api restart & service uchiwa restart
```

Pruebas de la ejecución:

![][1] 
![][2] 
![][3] 
![][4] 

Para la instalacion de stack ELK(elasticsearch, logstash, kibana, filebeat en el client)) se ejecutaron los comandos de la guia https://gist.github.com/d4n13lbc/be1ad5039dff1c058b335482488d4965

Evidencias de la ejecución:

![][5] 
![][6] 


[1]: imagenes/imagen3.png
[2]: imagenes/imagen4.png
[3]: imagenes/imagen5.png
[4]: imagenes/imagen6.png
[5]: imagenes/imagen2.png
[6]: imagenes/imagen1.png

