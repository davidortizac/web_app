# Aplicación Web con Balanceo de Carga usando A10 vThunder para entrenamiento del team Gamma

Este proyecto demuestra una aplicación web desplegada en múltiples contenedores Docker, utilizando un balanceador de carga A10 vThunder para distribuir el tráfico entre las instancias. Cada instancia de la aplicación web está configurada con un color único para facilitar su identificación. A10 vThunder se ha configurado para distribuir el tráfico hacia los servidores backend usando el método de *Least Connections*.

## Características

- **Aplicación Web en Docker**: Desplegada en cuatro contenedores Docker, cada instancia sirve la misma aplicación con un color único.
- **Balanceo de Carga con A10 vThunder**: Distribuye las solicitudes entrantes al servidor backend que tiene menos conexiones activas.
- **Codificación de Color Dinámica en Cada Servidor**: Cada servidor muestra un color único para facilitar las pruebas y la verificación.

## Requisitos Previos

- [Docker](https://docs.docker.com/get-docker/) y [Docker Compose](https://docs.docker.com/compose/install/) instalados en la máquina anfitriona.
- Una instancia de [A10 vThunder](https://www.a10networks.com/) configurada y accesible.

## Instrucciones de Configuración

### 1. Clona el Repositorio

```bash
git clone https://github.com/tu-usuario/tu-repo.git
cd tu-repo
```

### 2. Configura el Entorno Docker

Este proyecto usa Docker Compose para construir y desplegar cuatro instancias de la aplicación web. Cada instancia está configurada con un color único.

### 3. Configuración de Docker Compose

En el archivo `docker-compose.yml`, cada servicio (instancia de la aplicación web) está definido con un color único:

```yaml
version: '3'
services:
  web1:
    build:
      context: ./frontend
    ports:
      - "8081:80"
    environment:
      - SERVER_COLOR=red

  web2:
    build:
      context: ./frontend
    ports:
      - "8082:80"
    environment:
      - SERVER_COLOR=green

  web3:
    build:
      context: ./frontend
    ports:
      - "8083:80"
    environment:
      - SERVER_COLOR=blue

  web4:
    build:
      context: ./frontend
    ports:
      - "8084:80"
    environment:
      - SERVER_COLOR=yellow
```

### 4. Construye e Inicia los Contenedores

Ejecuta los siguientes comandos para construir e iniciar todos los servicios:

```bash
docker-compose up -d --build
```

### 5. Configuración de A10 vThunder

#### Instrucciones Paso a Paso

1. **Accede a A10 vThunder** a través de la CLI o GUI.
2. **Crea un grupo de servicio** que incluya los servidores backend y agrega cada instancia web:
   ```plaintext
   slb service-group web_app_group tcp
   slb server web1 <IP_WEB1>
   port 80 tcp
   member web_app_group
   slb server web2 <IP_WEB2>
   port 80 tcp
   member web_app_group
   ```
3. **Configura el algoritmo de balanceo**:
   ```plaintext
   slb service-group web_app_group
   method least-connection
   ```
4. **Crea un servidor virtual (VIP)**:
   ```plaintext
   slb virtual-server web_app_vip <VIP_IP>
   port 80 tcp
   service-group web_app_group
   ```

Reemplaza `<IP_WEB1>`, `<IP_WEB2>`, etc., con las direcciones IP específicas de cada contenedor o la IP de la máquina anfitriona si usas puertos distintos para cada uno.

5. **Guarda la Configuración** en A10 vThunder:
   ```plaintext
   write memory
   ```

### 6. Accede a la Aplicación

Una vez todo configurado, accede a la aplicación web usando la IP virtual (VIP) de A10 vThunder:

```plaintext
http://<VIP_IP>
```

El A10 vThunder distribuirá las solicitudes entrantes a los servidores backend en función del método de *Least Connections*, asegurando un balanceo de carga eficiente entre las instancias.

## Estructura del Proyecto

```plaintext
.
├── frontend
│   ├── Dockerfile               # Dockerfile para cada instancia web
│   ├── index.html               # Archivo HTML principal, configurado con soporte de color dinámico
│   └── style.css                # CSS para el estilo de color y diseño
├── nginx
│   └── nginx.conf               # Configuración opcional para pruebas locales con NGINX
└── docker-compose.yml           # Archivo de Docker Compose para construir y desplegar contenedores
```

## Solución de Problemas

- **Incapaz de Conectarse a la VIP**: Asegúrate de que la configuración de A10 vThunder esté guardada y de que la VIP sea accesible.
- **El Color No Se Muestra Correctamente**: Verifica que cada contenedor tenga su variable de entorno `SERVER_COLOR` configurada correctamente.

## Licencia

Este proyecto está licenciado bajo la Licencia MIT - consulta el archivo [LICENSE](LICENSE) para más detalles.
```

---

### Notas Finales
- Cambia `https://github.com/tu-usuario/tu-repo.git` al enlace real de tu repositorio de GitHub.
- Asegúrate de reemplazar los marcadores de posición `<VIP_IP>`, `<IP_WEB1>`, etc., con las IP correspondientes en tu entorno.

Este `README.md` en español proporciona una descripción completa del proyecto, incluyendo los pasos de configuración y solución de problemas, ideal para compartir en GitHub.
