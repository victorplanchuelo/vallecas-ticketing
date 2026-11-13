# Vallecas Ticketing

**Plataforma de gestión de entradas, reservas y acceso a un estadio de fútbol.**

*Ingeniería de software al servicio de la experiencia del fútbol en Vallecas.*

![Estado](https://img.shields.io/badge/Estado-En%20desarrollo-2ea44f?style=flat-square)
![Proyecto](https://img.shields.io/badge/Proyecto-Portfolio-blue?style=flat-square)
[![PHP](https://img.shields.io/badge/PHP-8.5-777BB4?logo=php&logoColor=white&style=flat-square)](https://www.php.net/)
[![Laravel](https://img.shields.io/badge/Laravel-13-FF2D20?logo=laravel&logoColor=white&style=flat-square)](https://laravel.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-18-4169E1?logo=postgresql&logoColor=white&style=flat-square)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-8-DC382D?logo=redis&logoColor=white&style=flat-square)](https://redis.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?logo=docker&logoColor=white&style=flat-square)](https://www.docker.com/)
[![Pest](https://img.shields.io/badge/Pest-5-8A2BE2?style=flat-square)](https://pestphp.com/)
![React](https://img.shields.io/badge/React-Planificado-61DAFB?logo=react&logoColor=black&style=flat-square)
![RabbitMQ](https://img.shields.io/badge/RabbitMQ-Planificado-FF6600?logo=rabbitmq&logoColor=white&style=flat-square)

---

## 1. Visión del proyecto

Vallecas Ticketing es un proyecto personal de ingeniería de software que explora el diseño y desarrollo de una plataforma de ticketing para un club de fútbol profesional.

El proyecto toma como referencia el contexto del **Rayo Vallecano**, su estadio y la identidad futbolística y social de Vallecas. El objetivo es construir una solución técnica que contemple tanto la experiencia del aficionado como las necesidades operativas de gestión de un club.

No se trata únicamente de implementar una tienda de entradas. El propósito es abordar problemas de ingeniería propios de este tipo de sistemas: concurrencia, disponibilidad de localidades, reservas temporales, consistencia de datos, procesamiento de pagos y control de acceso.

Este proyecto es independiente y no está afiliado, patrocinado ni respaldado por el Rayo Vallecano.

## 2. Contexto: el fútbol en Vallecas

El Estadio de Vallecas es mucho más que el escenario de los partidos del Rayo Vallecano. Forma parte de la identidad del barrio y de una cultura futbolística con una personalidad muy marcada.

La afición, sus tradiciones y el ambiente que rodea cada encuentro constituyen el contexto que inspira este proyecto.

Dentro de esa identidad destaca también la presencia de los **Bukaneros**, grupo de animación vinculado históricamente a la grada del Rayo Vallecano. Su existencia forma parte del entorno cultural y social que hace singular al club.

Vallecas Ticketing utiliza este contexto como inspiración para plantear una plataforma que tenga en cuenta la relación entre el club, el estadio y sus aficionados.

## 3. Objetivos funcionales

La plataforma aspira a cubrir los principales procesos relacionados con la gestión de entradas y el acceso al estadio:

- Gestión de estadios, sectores, filas y localidades.
- Gestión de partidos y eventos.
- Administración del inventario de localidades.
- Consulta de disponibilidad y selección de asientos.
- Reservas temporales y venta de entradas.
- Gestión de abonados y sus derechos de acceso.
- Liberación de localidades de abonados para partidos concretos.
- Integración con proveedores de pago.
- Emisión y gestión de entradas digitales.
- Validación de entradas y control de acceso.
- Herramientas de administración y seguimiento de ventas.

## 4. Aplicaciones

La solución contempla dos canales principales:

### Fan Portal

Interfaz orientada al aficionado:

- Consulta de partidos y eventos.
- Visualización del mapa del estadio.
- Consulta de localidades disponibles.
- Selección y reserva de asientos.
- Compra y consulta de entradas.
- Gestión de entradas digitales.
- Consulta de información relacionada con abonos.

### Backoffice

Interfaz orientada a la operativa del club:

- Administración de partidos.
- Gestión del estadio y sus localidades.
- Configuración de inventario.
- Gestión de reservas y ventas.
- Administración de abonados.
- Seguimiento de ocupación y ventas.
- Operaciones relacionadas con el control de acceso.

Estos canales representan diferentes experiencias de usuario y no implican necesariamente que deban convertirse en aplicaciones o servicios independientes desde el punto de vista arquitectónico.

## 5. Arquitectura y decisiones técnicas

La solución parte de un **monolito modular**, organizado mediante principios de Domain-Driven Design, arquitectura hexagonal y CQRS.

La intención es mantener una separación clara entre dominio, aplicación e infraestructura, evitando introducir complejidad distribuida antes de que exista una necesidad real.

### Stack tecnológico

| Tecnología | Responsabilidad | Estado |
|---|---|---|
| PHP 8.5 | Lenguaje principal | Configurado |
| Laravel 13 | Framework backend | Configurado |
| PostgreSQL 18 | Persistencia principal | Configurado |
| Redis 8 | Caché, datos temporales y locks | Configurado |
| Docker Compose | Entorno local de infraestructura | Configurado |
| Pest 5 | Pruebas automatizadas | Configurado |
| React + TypeScript | Frontend | Planificado |
| RabbitMQ | Mensajería asíncrona | Planificado |
| GitHub Actions | Integración continua | Planificado |
| Larastan | Análisis estático | Planificado |

### Principios arquitectónicos

- **Domain-Driven Design:** modelado explícito del dominio y sus reglas de negocio.
- **Arquitectura hexagonal:** aislamiento del dominio respecto a frameworks, bases de datos y servicios externos.
- **CQRS:** separación entre operaciones de escritura y modelos de lectura cuando aporte valor.
- **Monolito modular:** límites claros entre módulos sin asumir el coste operativo de microservicios.
- **Consistencia transaccional:** PostgreSQL como fuente de verdad para las operaciones de negocio.
- **Idempotencia:** protección frente a la repetición de operaciones y notificaciones externas.

### Gestión de disponibilidad y reservas

Uno de los principales retos del proyecto es evitar que una misma localidad pueda venderse o reservarse simultáneamente para diferentes usuarios.

Se plantea separar dos conceptos:

1. **Selección temporal:** interacción del usuario con el mapa de asientos, gestionada mediante Redis y una caducidad corta.
2. **Reserva de negocio:** operación persistida en PostgreSQL, con un estado y una fecha de expiración propios.

Redis no será la fuente de verdad de la disponibilidad ni del derecho de reserva. La confirmación de una reserva deberá validarse transaccionalmente en PostgreSQL, protegiendo las localidades frente a operaciones concurrentes.

Esta separación permite distinguir entre una localidad que alguien está seleccionando y una localidad que realmente está reservada.

## 6. Calidad y fiabilidad

El proyecto prestará especial atención a los aspectos que pueden provocar problemas en una plataforma de ticketing:

- Prevención de sobreventa de localidades.
- Control de concurrencia y bloqueos transaccionales.
- Expiración de reservas.
- Idempotencia en operaciones de pago y webhooks.
- Consistencia entre pagos, reservas y emisión de entradas.
- Protección frente al doble uso de una entrada.
- Pruebas unitarias, de integración y de funcionalidades.
- Trazabilidad de operaciones y errores.

La complejidad de estas áreas se abordará progresivamente, conforme se implementen los distintos módulos.

## 7. Roadmap

El desarrollo se organizará en las siguientes etapas:

- [x] Preparación del entorno local de desarrollo.
- [x] Creación del proyecto Laravel.
- [x] Configuración inicial de PostgreSQL y Redis mediante Docker Compose.
- [x] Instalación y configuración inicial de Pest.
- [ ] Definición del dominio y sus límites.
- [ ] Fundación arquitectónica y convenciones del proyecto.
- [ ] Modelado del estadio, sectores y localidades.
- [ ] Gestión de partidos.
- [ ] Inventario y disponibilidad de localidades.
- [ ] Selección de asientos y reservas temporales.
- [ ] Integración de pagos.
- [ ] Emisión y gestión de entradas digitales.
- [ ] Gestión de abonados y liberación de localidades.
- [ ] Desarrollo del Fan Portal.
- [ ] Desarrollo del Backoffice.
- [ ] Control de acceso al estadio.
- [ ] Observabilidad, optimización y pruebas de carga.
- [ ] Integración continua y automatización de calidad.

El roadmap refleja objetivos de desarrollo, no funcionalidades ya disponibles. Se actualizará conforme avance la implementación.

## 8. Flujo de desarrollo e integración continua

El flujo de trabajo previsto seguirá un enfoque basado en ramas y Pull Requests:

1. Crear una rama para cada funcionalidad o corrección.
2. Implementar los cambios y añadir pruebas.
3. Subir la rama al repositorio remoto.
4. Abrir un Pull Request hacia `main`.
5. Ejecutar automáticamente las comprobaciones de calidad.
6. Revisar los cambios y resolver las observaciones.
7. Integrar los cambios una vez superadas las comprobaciones.

Cada nueva actualización de una rama asociada a un Pull Request actualizará ese mismo Pull Request. No se pretende generar un Pull Request nuevo por cada commit.

La configuración de GitHub Actions y las protecciones de la rama principal están pendientes de implementación.

## 9. Requisitos de desarrollo

Para ejecutar el proyecto localmente se necesita:

- Windows con WSL2 y una distribución Linux, o un entorno Linux equivalente.
- PHP 8.5.
- Composer.
- Docker Desktop con integración WSL2, o Docker Engine y Docker Compose.
- Node.js y npm.

Las extensiones PHP necesarias incluyen, entre otras, `pdo_pgsql`, `redis`, `mbstring`, `intl`, `bcmath`, `curl` y `zip`.

## 10. Instalación local

### 10.1. Clonar el repositorio

```bash
git clone <repository-url>
cd vallecas-ticketing
```

### 10.2. Instalar dependencias

```bash
composer install
npm ci
```

### 10.3. Configurar las variables de entorno

Crear los archivos locales a partir de los ejemplos:

```bash
cp .env.example .env
cp .env.docker.example .env.docker
```

Generar la clave de aplicación:

```bash
php artisan key:generate
```

El archivo `.env.example` ya contiene la configuración local prevista para PostgreSQL y Redis. Si personalizas la contraseña de PostgreSQL en `.env.docker`, recuerda utilizar la misma contraseña en `DB_PASSWORD` dentro de `.env`.

### 10.4. Levantar la infraestructura

```bash
docker compose --env-file .env.docker up -d
```

Comprobar el estado de los servicios:

```bash
docker compose --env-file .env.docker ps
```

PostgreSQL y Redis deben aparecer como servicios activos y saludables.

### 10.5. Ejecutar las migraciones

```bash
php artisan migrate
```

### 10.6. Arrancar la aplicación

En una terminal:

```bash
php artisan serve
```

En otra terminal:

```bash
npm run dev
```

La aplicación estará disponible en:

```text
http://localhost:8000
```

## 11. Pruebas

Para ejecutar la suite de pruebas:

```bash
php artisan test
```

El proyecto utiliza Pest como framework de testing sobre la infraestructura de pruebas de Laravel.

A medida que se implementen los módulos se incorporarán pruebas para las reglas de dominio, los casos de uso y los flujos de integración.

## 12. Propósito del proyecto

Vallecas Ticketing es un ejercicio de diseño y construcción de software orientado a explorar problemas reales de backend en un dominio con requisitos exigentes de consistencia, concurrencia y fiabilidad.

Más allá de completar funcionalidades, el objetivo es demostrar criterio técnico en:

- Modelado de dominios complejos.
- Diseño de arquitecturas mantenibles.
- Gestión de concurrencia y transacciones.
- Integración con sistemas externos.
- Estrategias de testing.
- Automatización de calidad.
- Observabilidad y operación.

---

**Vallecas Ticketing es un proyecto independiente de portfolio. No existe afiliación, patrocinio ni relación comercial con el Rayo Vallecano, el Estadio de Vallecas o grupos de aficionados.**