# Endpoints y Recursos API PrestaShop

## Lista Completa de Recursos

La API Webservice de PrestaShop proporciona acceso a más de 50 recursos diferentes a través de endpoints RESTful.

### URL Base
```
https://tudominio.com/api/
```

### Formato de Endpoint
```
https://WS_KEY@tudominio.com/api/{resource}
https://WS_KEY@tudominio.com/api/{resource}/{id}
```

## Recursos Principales

### 🛍️ Catálogo y Productos

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **products** | `/api/products` | Productos del catálogo |
| **categories** | `/api/categories` | Categorías de productos |
| **combinations** | `/api/combinations` | Variantes de productos |
| **images** | `/api/images` | Imágenes de productos |
| **manufacturers** | `/api/manufacturers` | Fabricantes/Marcas |
| **product_features** | `/api/product_features` | Características de productos |
| **product_feature_values** | `/api/product_feature_values` | Valores de características |
| **product_option_values** | `/api/product_option_values` | Valores de opciones |
| **product_customization_fields** | `/api/product_customization_fields` | Campos de personalización |

### 👥 Clientes y Direcciones

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **customers** | `/api/customers` | Clientes registrados |
| **addresses** | `/api/addresses` | Direcciones de clientes |
| **groups** | `/api/groups` | Grupos de clientes |
| **guests** | `/api/guests` | Visitantes no registrados |

### 📦 Pedidos y Ventas

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **orders** | `/api/orders` | Pedidos realizados |
| **order_details** | `/api/order_details` | Detalles de pedidos |
| **order_histories** | `/api/order_histories` | Historial de pedidos |
| **order_invoices** | `/api/order_invoices` | Facturas de pedidos |
| **order_payments** | `/api/order_payments` | Pagos de pedidos |
| **order_carriers** | `/api/order_carriers` | Transportistas de pedidos |
| **order_states** | `/api/order_states` | Estados de pedidos |
| **order_slip** | `/api/order_slip` | Notas de crédito |

### 🛒 Carrito de Compras

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **carts** | `/api/carts` | Carritos de compra |
| **cart_rules** | `/api/cart_rules` | Reglas de carrito (descuentos) |

### 🚚 Envío y Entrega

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **carriers** | `/api/carriers` | Transportistas |
| **deliveries** | `/api/deliveries` | Entregas |
| **price_ranges** | `/api/price_ranges` | Rangos de precios de envío |

### ⚙️ Configuración

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **configurations** | `/api/configurations` | Configuraciones de la tienda |
| **languages** | `/api/languages` | Idiomas disponibles |
| **currencies** | `/api/currencies` | Monedas soportadas |
| **countries** | `/api/countries` | Países |
| **image_types** | `/api/image_types` | Tipos de imagen |

### 💬 Comunicación

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **messages** | `/api/messages` | Mensajes |
| **customer_messages** | `/api/customer_messages` | Mensajes de clientes |
| **customer_threads** | `/api/customer_threads` | Hilos de conversación |
| **contacts** | `/api/contacts` | Contactos |

### 👨‍💼 Administración

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **employees** | `/api/employees` | Empleados |
| **customizations** | `/api/customizations` | Personalizaciones |

### 📄 Contenido

| Recurso | Endpoint | Descripción |
|---------|----------|-------------|
| **content_management_system** | `/api/content_management_system` | Sistema de gestión de contenido |

## Operaciones CRUD

### GET - Leer Datos

#### Obtener Lista de Recursos
```http
GET /api/products
```

#### Obtener Recurso Específico
```http
GET /api/products/1
```

#### Filtros y Parámetros
```http
GET /api/products?filter[active]=1
GET /api/products?sort=id_DESC
GET /api/products?limit=10
GET /api/products?display=[id,name,price]
```

### POST - Crear Recursos
```http
POST /api/products
Content-Type: application/xml

<prestashop>
  <product>
    <name>Nuevo Producto</name>
    <price>29.99</price>
  </product>
</prestashop>
```

### PUT - Actualizar Recursos
```http
PUT /api/products/1
Content-Type: application/xml

<prestashop>
  <product>
    <id>1</id>
    <name>Producto Actualizado</name>
  </product>
</prestashop>
```

### DELETE - Eliminar Recursos
```http
DELETE /api/products/1
```

## Parámetros de Consulta

### Filtros
```
?filter[campo]=valor
?filter[active]=1
?filter[id_category]=5
?filter[price]=[10,50]  // Rango
```

### Ordenamiento
```
?sort=campo_ASC
?sort=campo_DESC
?sort=id_DESC
?sort=name_ASC
```

### Paginación
```
?limit=20              // Número de resultados
?limit=20,40           // Offset y límite
```

### Campos Específicos
```
?display=[id,name,price]
?display=full
```

### Formato de Respuesta
```
?output_format=JSON
?output_format=XML
```

## Ejemplos de URLs Completas

### Productos
```bash
# Todos los productos
GET https://WSKEY@tienda.com/api/products?output_format=JSON

# Producto específico
GET https://WSKEY@tienda.com/api/products/1?output_format=JSON

# Productos activos
GET https://WSKEY@tienda.com/api/products?filter[active]=1&output_format=JSON

# Productos de categoría específica
GET https://WSKEY@tienda.com/api/products?filter[id_category_default]=5&output_format=JSON
```

### Pedidos
```bash
# Todos los pedidos
GET https://WSKEY@tienda.com/api/orders?output_format=JSON

# Pedidos por estado
GET https://WSKEY@tienda.com/api/orders?filter[current_state]=2&output_format=JSON

# Pedidos por cliente
GET https://WSKEY@tienda.com/api/orders?filter[id_customer]=10&output_format=JSON
```

### Clientes
```bash
# Todos los clientes
GET https://WSKEY@tienda.com/api/customers?output_format=JSON

# Cliente específico
GET https://WSKEY@tienda.com/api/customers/1?output_format=JSON

# Clientes activos
GET https://WSKEY@tienda.com/api/customers?filter[active]=1&output_format=JSON
```

## Códigos de Estado HTTP

| Código | Significado | Descripción |
|--------|-------------|-------------|
| **200** | OK | Solicitud exitosa |
| **201** | Created | Recurso creado exitosamente |
| **400** | Bad Request | Solicitud malformada |
| **401** | Unauthorized | Credenciales inválidas |
| **403** | Forbidden | Permisos insuficientes |
| **404** | Not Found | Recurso no encontrado |
| **405** | Method Not Allowed | Método HTTP no permitido |
| **500** | Internal Server Error | Error del servidor |

## Límites y Consideraciones

### Límites de Velocidad
- Implementar rate limiting en el cliente
- Usar delays entre requests masivos
- Monitorear respuestas 429 (Too Many Requests)

### Límites de Datos
- Máximo 100 productos por request (usar paginación)
- Timeout por defecto: 30 segundos
- Tamaño máximo de respuesta: Variable según hosting

### Mejores Prácticas
1. Usar `output_format=JSON` para mejor rendimiento
2. Filtrar campos con `display` para reducir payload
3. Implementar caché local para datos estáticos
4. Usar filtros para reducir transferencia de datos
5. Manejar errores y reintentos apropiadamente

---

**Nota**: La disponibilidad de algunos recursos puede variar según la versión de PrestaShop y los módulos instalados.