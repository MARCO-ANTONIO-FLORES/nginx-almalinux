# IMPLEMENTACIÓN DE SERVIDOR NGINX COMPILADO DESDE CÓDIGO FUENTE

---

# Carátula

##  Universidad
[TECNOLOGICO DE ESTUDIOS SUPERIORES DE ORIENTE DEL ESTADO DEM MEXICO]

## Carrera
Ingeniería en Sistemas Computacionales

## Materia
[TALLER DE SISTEMAS OPERATIVOS]

## Proyecto
Implementación de servidor NGINX compilado desde código fuente

## Docente
[Gustavo Moisés Romero González]

## Integrantes
- MARCO ANTONIO SOLIS FLORES
- RIVERA CONTRERAS CHRISTIAN GERARDO 

## Fecha
25 de mayo de 2026

---

# Objetivo General

Implementar un servidor web NGINX compilado desde código fuente en AlmaLinux, configurando el servicio mediante SystemD y asegurando su ejecución automática durante el arranque del sistema operativo.

---

# Objetivos Específicos

- Instalar las dependencias necesarias para la compilación de NGINX.
- Descargar y compilar la versión 1.31.x de NGINX desde código fuente.
- Configurar el prefijo de instalación en `/srv/nginx`.
- Crear el usuario y grupo del sistema `NGINX`.
- Configurar el servicio mediante SystemD.
- Habilitar el autoarranque del servicio.
- Validar el funcionamiento del servidor web mediante pruebas locales.

---

# Desarrollo del Proyecto

## Instalación de dependencias

Se instalaron las herramientas necesarias para compilar NGINX desde código fuente:

```bash
dnf groupinstall "Development Tools" -y
dnf install pcre pcre-devel zlib zlib-devel openssl openssl-devel wget tar gcc make libcap -y
```

---

## Creación del usuario y grupo NGINX

Se creó el usuario y grupo del sistema para ejecutar el servicio:

```bash
groupadd NGINX
useradd -r -g NGINX -s /sbin/nologin NGINX
```

---

## Descarga de NGINX

Se descargó la versión 1.31.0 desde el sitio oficial:

```bash
wget https://nginx.org/download/nginx-1.31.0.tar.gz
tar -xvzf nginx-1.31.0.tar.gz
cd nginx-1.31.0
```

---

## Compilación e instalación

Se compiló NGINX utilizando el prefijo `/srv/nginx`:

```bash
./configure --prefix=/srv/nginx \
--user=NGINX \
--group=NGINX \
--with-http_ssl_module

make
make install
```

---

## Configuración de permisos

Se crearon los directorios de logs y se asignaron permisos:

```bash
mkdir -p /srv/nginx/logs

touch /srv/nginx/logs/error.log
touch /srv/nginx/logs/access.log

chown -R NGINX:NGINX /srv/nginx
chmod -R 755 /srv/nginx
```

---

## Configuración de capacidades para el puerto 80

Se asignó la capacidad necesaria al binario de NGINX:

```bash
setcap 'cap_net_bind_service=+ep' /srv/nginx/sbin/nginx
```

---

## Configuración del servicio SystemD

Se creó el archivo:

```text
/etc/systemd/system/nginx.service
```

Contenido:

```ini
[Unit]
Description=NGINX Web Server
After=network.target

[Service]
Type=forking
PIDFile=/srv/nginx/logs/nginx.pid

ExecStartPre=/srv/nginx/sbin/nginx -t
ExecStart=/srv/nginx/sbin/nginx
ExecReload=/srv/nginx/sbin/nginx -s reload
ExecStop=/srv/nginx/sbin/nginx -s quit

PrivateTmp=true

[Install]
WantedBy=multi-user.target
WantedBy=graphical.target
```

---

## Habilitación del servicio

```bash
systemctl daemon-reload
systemctl enable nginx
systemctl start nginx
```

---

## Validación del servicio

Verificación del estado:

```bash
systemctl status nginx
```

Validación del puerto 80:

```bash
ss -tulpn | grep :80
```

Prueba local:

```bash
curl http://localhost
```

---

# Resultados

El servidor NGINX fue implementado exitosamente en AlmaLinux utilizando compilación desde código fuente. El servicio quedó registrado en SystemD y configurado para iniciar automáticamente durante el arranque del sistema operativo.

---

# Conclusiones

La implementación de NGINX desde código fuente permitió comprender el proceso de compilación, configuración y administración de servicios en Linux. Además, se reforzaron conocimientos sobre permisos, SystemD, capacidades de red y administración de servidores web en entornos empresariales.

---

# Bibliografía

NGINX. (2026). *NGINX Documentation*. Recuperado de https://nginx.org/en/docs/

Red Hat. (2026). *Systemd Documentation*. Recuperado de https://access.redhat.com/documentation/

The Linux Foundation. (2026). *Linux Manual Pages*. Recuperado de https://man7.org/linux/man-pages/

AlmaLinux OS Foundation. (2026). *AlmaLinux Documentation*. Recuperado de https://wiki.almalinux.org/
