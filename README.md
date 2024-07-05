Por supuesto, aquí tienes todo el texto en formato Markdown sin cortes, listo para copiar y pegar en GitHub:

```markdown
# StellarCore Blog API Server

Este proyecto es un servidor API para un blog, que se configura dinámicamente utilizando un archivo YAML. La aplicación utiliza Gorm para la gestión de la base de datos, Gorilla Mux para la gestión de rutas, y QUIC para el transporte. La autenticación se maneja mediante Paseto.

## Características

- **Configuración Dinámica:** Configura la base de datos, tablas y rutas mediante un archivo YAML.
- **Autenticación Segura:** Utiliza Paseto para la autenticación de usuarios.
- **Almacenamiento Seguro de Contraseñas:** Las contraseñas se almacenan de forma segura utilizando bcrypt.
- **Transporte Rápido y Seguro:** Utiliza QUIC y HTTP/3 para un rendimiento y seguridad mejorados.
- **Gestión de Base de Datos con Gorm:** Utiliza Gorm para la creación y gestión de la base de datos.
- **Gestión de Rutas con Gorilla Mux:** Define y maneja rutas de manera eficiente.

## Configuración

### Archivo YAML (`/mods/blog.yaml`)

El archivo YAML define la estructura de la base de datos y las rutas. Aquí hay un ejemplo de cómo debería lucir:

```yaml
database: blog
tables:
  - name: users
    fields:
      - name: id
        type: INTEGER PRIMARY KEY AUTOINCREMENT
      - name: username
        type: TEXT
        unique: true
        not_null: true
      - name: password
        type: TEXT
        not_null: true
  - name: posts
    fields:
      - name: id
        type: INTEGER PRIMARY KEY AUTOINCREMENT
      - name: title
        type: TEXT
        unique: true
        not_null: true
      - name: content
        type: TEXT
        not_null: true
      - name: image
        type: TEXT
      - name: created_at
        type: DATETIME
        default: CURRENT_TIMESTAMP
      - name: category_id
        type: INTEGER
        references: categories(id)
    indexes:
      - name: idx_posts_title
        columns: [title]
      - name: idx_posts_category_id
        columns: [category_id]
  - name: categories
    fields:
      - name: id
        type: INTEGER PRIMARY KEY AUTOINCREMENT
      - name: name
        type: TEXT
        unique: true
        not_null: true
    indexes:
      - name: idx_categories_name
        columns: [name]
routes:
  - path: /posts
    table: posts
    operations:
      - type: GET
        description: Retrieve all posts
        pagination: cursor
      - type: POST
        description: Create a new post
        roles: [admin, editor]
  - path: /categories
    table: categories
    operations:
      - type: GET
        description: Retrieve all categories
        cache: true
      - type: POST
        description: Create a new category
        roles: [admin]
  - path: /posts/{id}
    table: posts
    operations:
      - type: GET
        description: Retrieve a post by ID
      - type: PUT
        description: Update a post by ID
        roles: [admin, editor]
      - type: DELETE
        description: Delete a post by ID
        roles: [admin]
pagination:
  enabled: true
  type: cursor
search:
  enabled: true
  tables:
    - name: posts
      fields: [title, content]
cache:
  enabled: true
  strategies:
    - type: in-memory
      duration: 60m
roles:
  admin:
    description: Administrator with full access
  editor:
    description: Editor with rights to modify content
  viewer:
    description: Viewer with read-only access
```

## Requisitos

- Go 1.16 o superior
- SQLite
- Certificado SSL para QUIC/HTTP3

## Instalación

1. Clona el repositorio:

```sh
git clone https://github.com/tu_usuario/blog-api-server.git
cd blog-api-server
```

2. Instala las dependencias:

```sh
go mod tidy
```

3. Configura el archivo YAML en `/mods/blog.yaml` según tus necesidades.

4. Ejecuta la aplicación:

```sh
go run main.go
```

## Uso

### Endpoints

#### Registro de Usuario
- **Endpoint:** `/register`
- **Método:** POST
- **Descripción:** Registra un nuevo usuario.
- **Cuerpo de la Solicitud:**
```json
{
  "username": "nuevo_usuario",
  "password": "contraseña_segura"
}
```

#### Inicio de Sesión
- **Endpoint:** `/login`
- **Método:** POST
- **Descripción:** Autentica un usuario y devuelve un token Paseto.
- **Cuerpo de la Solicitud:**
```json
{
  "username": "usuario",
  "password": "contraseña"
}
```

#### Obtener Posts
- **Endpoint:** `/posts`
- **Método:** GET
- **Descripción:** Recupera todos los posts (requiere autenticación).

#### Crear Post
- **Endpoint:** `/posts`
- **Método:** POST
- **Descripción:** Crea un nuevo post (requiere autenticación).
- **Cuerpo de la Solicitud:**
```json
{
  "title": "Nuevo Post",
  "content": "Contenido del post",
  "image": "URL de la imagen",
  "category_id": 1
}
```

#### Obtener Categorías
- **Endpoint:** `/categories`
- **Método:** GET
- **Descripción:** Recupera todas las categorías (requiere autenticación).

#### Crear Categoría
- **Endpoint:** `/categories`
- **Método:** POST
- **Descripción:** Crea una nueva categoría (requiere autenticación).
- **Cuerpo de la Solicitud:**
```json
{
  "name": "Nueva Categoría"
}
```

### Autenticación

Todas las rutas protegidas requieren un token Paseto en el encabezado de autorización:

```http
Authorization: Bearer {token}
```

## Contribuir

1. Haz un fork del repositorio.
2. Crea una rama para tu nueva característica (`git checkout -b feature/nueva-caracteristica`).
3. Haz tus cambios y commitea (`git commit -am 'Agrega nueva característica'`).
4. Sube tu rama (`git push origin feature/nueva-caracteristica`).
5. Crea un nuevo Pull Request.

## Licencia

Este proyecto está bajo la Licencia MIT. Consulta el archivo LICENSE para más detalles.
