# Configuración de una Pila de Servicios IDS ( SNORT-Elastic-Logstash-Kibana basado en Debian 11 Bullseye ) #

## Instalación Debian 11 Bullseye ##

**Obtener Instalación Debian**

Descargamos la Imagen de Instalación desde la WEB Oficial de Debian. [Enlace Directo](https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-11.2.0-amd64-netinst.iso)

**Configuración de la Máquina Virtual en VirtualBOX**

IMPORTANTE: Recomendables 8GB de RAM y 4 CPU.

2 Interfaces de RED ( Conveniente configurar con direccionamiento Estático )
 - Red Analista ( Puente )  ( Acceso a Kibana desde el Equipo HOST )
 - Red Monitorización ( Interna ) ( Perímetro a Monitorizar )

**Instalar Debian sobre VirtualBOX**

Para optimizar el rendimiento de la Pila de Servicios realizaremos una instalación básica sin entorno gráfico.

Configurar HOSTNAME + DOMNINIO ( Importante anotarlo para configurar la PILA de Servicios más adelante )

**En Debian NO se instala SUDO por defecto**

Para habilitar *sudo* en Debian habrá que ejecutar los siguientes comandos ( cambiando a root con el comando *su* ):

```
apt update
apt install sudo
usermod -aG nombre_usuario sudo
```

**Dependencias para la instalación de la Pila de Servicios**
