# Autenticación API PrestaShop

## Métodos de Autenticación

### 1. Basic Authentication (Webservice Oficial)

La API Webservice de PrestaShop utiliza **HTTP Basic Authentication** con la clave de webservice (ws_key).

#### Formato de Autenticación
```
https://WS_KEY@dominio.com/api/
```

#### Ejemplo
```
https://ABCD1234EFGH5678IJKL9012MNOP3456@mitienda.com/api/products
```

### 2. Header Authorization

Alternativamente, se puede usar el header `Authorization`:

```http
GET /api/products HTTP/1.1
Host: mitienda.com
Authorization: Basic V1NfS0VZOg==
```

**Nota**: `V1NfS0VZOg==` es `WS_KEY:` codificado en Base64

## Generación de Claves de Acceso

### Crear Clave en PrestaShop

1. **Back Office** → **Parámetros Avanzados** → **Webservice**
2. Click **"Agregar nueva clave de webservice"**
3. Completar formulario:
   ```
   Key: [Generar automáticamente o introducir manualmente]
   Description: API Key para [propósito]
   Status: Habilitado
   ```

### Formato de Clave
- **Longitud**: 32 caracteres
- **Caracteres**: A-Z, 0-9
- **Ejemplo**: `ABCD1234EFGH5678IJKL9012MNOP3456`

## Configuración de Permisos

### Permisos por Recurso

Los permisos se configuran por cada recurso y operación:

```
┌─────────────────┬──────┬─────┬──────┬────────┐
│ Recurso         │ View │ Add │ Edit │ Delete │
├─────────────────┼──────┼─────┼──────┼────────┤
│ addresses       │  ✓   │  ✓  │  ✓   │   ✓    │
│ categories      │  ✓   │  ✓  │  ✓   │   ✓    │
│ customers       │  ✓   │  ✓  │  ✓   │   ✓    │
│ orders          │  ✓   │  ✓  │  ✓   │   ✓    │
│ products        │  ✓   │  ✓  │  ✓   │   ✓    │
└─────────────────┴──────┴─────┴──────┴────────┘
```

### Mejores Prácticas de Permisos

#### Solo Lectura (Catálogos)
```
products: View
categories: View
images: View
manufacturers: View
```

#### Gestión de Pedidos
```
orders: View, Add, Edit
customers: View
addresses: View
order_states: View
```

#### Gestión Completa
```
products: View, Add, Edit, Delete
categories: View, Add, Edit, Delete
customers: View, Add, Edit
orders: View, Add, Edit
```

## Ejemplos de Implementación

### cURL
```bash
# GET - Obtener productos
curl -X GET \
  "https://ABCD1234EFGH5678@mitienda.com/api/products" \
  -H "Accept: application/json"

# POST - Crear producto
curl -X POST \
  "https://ABCD1234EFGH5678@mitienda.com/api/products" \
  -H "Content-Type: application/xml" \
  -d '<prestashop><product><name>Nuevo Producto</name></product></prestashop>'
```

### PHP
```php
<?php
$ws_key = 'ABCD1234EFGH5678IJKL9012MNOP3456';
$shop_url = 'https://mitienda.com';

// Usando cURL
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $shop_url . '/api/products');
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_USERPWD, $ws_key . ':');
curl_setopt($ch, CURLOPT_HTTPHEADER, ['Accept: application/json']);

$response = curl_exec($ch);
curl_close($ch);

echo $response;
?>
```

### JavaScript (Node.js)
```javascript
const axios = require('axios');

const wsKey = 'ABCD1234EFGH5678IJKL9012MNOP3456';
const shopUrl = 'https://mitienda.com';

const config = {
  auth: {
    username: wsKey,
    password: ''
  },
  headers: {
    'Accept': 'application/json'
  }
};

axios.get(`${shopUrl}/api/products`, config)
  .then(response => {
    console.log(response.data);
  })
  .catch(error => {
    console.error('Error:', error.response.data);
  });
```

### Python
```python
import requests
from requests.auth import HTTPBasicAuth

ws_key = 'ABCD1234EFGH5678IJKL9012MNOP3456'
shop_url = 'https://mitienda.com'

response = requests.get(
    f'{shop_url}/api/products',
    auth=HTTPBasicAuth(ws_key, ''),
    headers={'Accept': 'application/json'}
)

if response.status_code == 200:
    print(response.json())
else:
    print(f'Error: {response.status_code}')
```

## Seguridad

### Mejores Prácticas

1. **HTTPS Obligatorio**
   - Nunca usar HTTP en producción
   - Certificado SSL válido

2. **Gestión de Claves**
   - Claves únicas por aplicación
   - Rotación periódica de claves
   - Almacenamiento seguro (variables de entorno)

3. **Principio de Menor Privilegio**
   - Solo permisos necesarios
   - Revisión periódica de permisos
   - Desactivar claves no utilizadas

4. **Monitoreo**
   - Logs de acceso a API
   - Alertas por intentos fallidos
   - Métricas de uso

### Variables de Entorno (Recomendado)

```bash
# .env
PRESTASHOP_WS_KEY=ABCD1234EFGH5678IJKL9012MNOP3456
PRESTASHOP_SHOP_URL=https://mitienda.com
```

```php
// PHP
$ws_key = $_ENV['PRESTASHOP_WS_KEY'];
$shop_url = $_ENV['PRESTASHOP_SHOP_URL'];
```

```javascript
// Node.js
const wsKey = process.env.PRESTASHOP_WS_KEY;
const shopUrl = process.env.PRESTASHOP_SHOP_URL;
```

## Troubleshooting

### Errores Comunes

#### 401 Unauthorized
```
Problem: Clave incorrecta o webservice deshabilitado
Solution: 
- Verificar ws_key
- Confirmar que webservice está habilitado
- Revisar permisos de la clave
```

#### 403 Forbidden
```
Problem: Permisos insuficientes
Solution:
- Revisar permisos del recurso
- Verificar operación permitida (GET/POST/PUT/DELETE)
```

#### 405 Method Not Allowed
```
Problem: Método HTTP no permitido
Solution:
- Verificar que el método está habilitado
- Revisar configuración de servidor web
```

### Verificación de Autenticación

```bash
# Probar conectividad básica
curl -I "https://WS_KEY@mitienda.com/api/"

# Respuesta esperada: HTTP/1.1 200 OK
```

---

**Importante**: 
- Mantener las claves seguras y privadas
- Usar HTTPS siempre en producción
- Implementar rate limiting en el cliente
- Manejar errores de autenticación apropiadamente