# Configuración de una Pila de Servicios IDS ( SNORT-Elastic-Logstash-Kibana basado en Debian 11 Bullseye ) #

## Instalación Debian 11 Bullseye ##

**Obtener Instalación Debian**

Descargamos la Imagen de Instalación desde la WEB Oficial de Debian. [Enlace Directo](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.2.0-amd64-netinst.iso)

**Configuración de la Máquina Virtual en VirtualBOX**

IMPORTANTE: Recomendables 8GB de RAM y 4 CPU.

2 Interfaces de RED ( Conveniente configurar con direccionamiento Estático )
 - Red Analista ( Puente )  ( Acceso a Kibana desde el Equipo HOST )
 - Red Monitorización ( Interna ) ( Perímetro a Monitorizar )

Configuración utilizada en este manual: ( */etc/network/interfaces* )

```
#Perimetro interno a monitorizar
allow-hotplug enp0s3
iface enp0s3 inet static
address 192.168.254.254
netmask 255.255.255.0

#Configurar en Estática dependiendo del entorno local
allow-hotplug enp0s8
iface enp0s8 inet static
address 192.168.1.254
netmask 255.255.255.0
gateway 192.168.1.1
```

**Instalar Debian sobre VirtualBOX**

Para optimizar el rendimiento de la Pila de Servicios realizaremos una instalación básica sin entorno gráfico.

Configurar HOSTNAME + DOMNINIO ( Importante anotarlo para configurar la PILA de Servicios más adelante )

**En Debian NO se instala SUDO por defecto**

Para habilitar *sudo* en Debian habrá que ejecutar los siguientes comandos ( cambiando a root con el comando *su* ):

```
apt update
apt install sudo
usermod -aG sudo *nombre_usuario*
```

**Dependencias para la instalación de la Pila de Servicios**

Será conveniente instalar las siguientes dependencias previamente a la instalación de la Pila SELK (sin sudo) :

```
apt update
apt install default-jdk-headless libjffi-java libjffi-jni gnupg2 apt-transport-https
```

**Instalación de SNORT**

```
apt install snort
```

Cuando el proceso de instalación de SNORT nos solicite una red a monitorizar es conveniente cambiar el direccionamiento por la red interna que queramos monitorizar. ( Por ejemplo: 192.168.254.0/24 )

Una vez instalado SNORT agregamos en */etc/snort/rules/local.rules* la siguiente alerta:

```
alert icmp any any -> any any (msg:"ICMP Packet detected"; sid:3000001;)
```

Se trata de una regla genérica para detectar PINGs contra el propio SNORT.

**Configuración del repositorio Oficial de Elastic para versión 7 ( Elastic , Logstash , Kibana )**

[Fuente original] (https://www.elastic.co/guide/en/elasticsearch/reference/7.16/deb.html#deb-repo)

Instalaremos la clave pública del repositorio oficial de Elastic:

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
apt update
```

**Pasos de instalación y configuración de la Pila ELK:**

- Instalación del Servicio ElasticSearch:

```
apt install elasticsearch
```

Una vez instalado **elasticsearch** procedemos a su configuración editando el archivo */etc/elasticsearch/elasticsearch.yml* :

cluster.name: siem.ejemplo.net 
node.name: host.siem.ejemplo.net
network.host: [192.168.1.254]  <- Ejemplo. Direccion IP que será accesible por parte del Analista.
discovery.type: single-node
xpack.ml.enabled: false

Y finalizada la configuración, habilitamos el Servicio de Elastic:

```
systemctl enable elasticsearch
systemctl start elascticsearch
```

Ahora podremos comprobar su correcto funcionamiento accediendo desde el Navegador del Analista a la dirección ( http://192.168.1.254:9200 )

- Instalación del Servicio LogStash:

```
apt install logstash
```

Una vez instalado **logstash** procedemos a su configuración añadiendo al archivo */etc/logstash/logstash.yml* :

node.name: siem.ejemplo.net
http.host: "[192.168.1.254]"
xpack.monitoring.enabled: false
xpack.management.enabled: false

Y finalizada su configuración, habilitamos el Servicio LogStash:

```
systemctl enable logstash 
systemctl start logstash
```
