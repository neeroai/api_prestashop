# Configuración API Webservice PrestaShop

## Activación del Webservice

### 1. Habilitar Webservice en PrestaShop

1. Acceder al **Back Office** de PrestaShop
2. Navegar a **Parámetros Avanzados** → **Webservice**
3. Activar la opción **"Habilitar Webservice de PrestaShop"**
4. Guardar configuración

### 2. Crear Clave de Acceso (API Key)

1. En la sección **Webservice**, hacer clic en **"Agregar nueva clave de webservice"**
2. Configurar los siguientes parámetros:
   - **Key**: Generar o introducir una clave personalizada
   - **Description**: Descripción del propósito de la clave
   - **Status**: Habilitado
   - **Permissions**: Seleccionar los recursos y permisos necesarios

### 3. Configuración de Permisos

#### Permisos por Recurso
Configurar permisos específicos para cada recurso:

| Recurso | View | Add | Edit | Delete |
|---------|------|-----|------|--------|
| addresses | ✓ | ✓ | ✓ | ✓ |
| categories | ✓ | ✓ | ✓ | ✓ |
| customers | ✓ | ✓ | ✓ | ✓ |
| orders | ✓ | ✓ | ✓ | ✓ |
| products | ✓ | ✓ | ✓ | ✓ |

#### Permisos Recomendados por Caso de Uso

**Solo Lectura (Catálogos)**:
- products: View
- categories: View
- images: View

**E-commerce Completo**:
- products: View, Add, Edit
- orders: View, Add, Edit
- customers: View, Add, Edit
- addresses: View, Add, Edit

**Gestión de Inventario**:
- products: View, Edit
- stock_availables: View, Edit
- combinations: View, Edit

## Configuración de Servidor

### Requisitos del Servidor

- **Apache/Nginx**: Con mod_rewrite habilitado
- **PHP**: Versión 7.4+ (recomendado 8.0+)
- **MySQL**: 5.6+ o MariaDB equivalente
- **SSL**: Recomendado para producción

### Configuración Apache

```apache
# .htaccess para habilitar webservice
RewriteEngine On
RewriteCond %{REQUEST_METHOD} ^(HEAD|GET|POST|PUT|DELETE|OPTIONS)$
RewriteRule ^api/(.*)$ webservice/dispatcher.php?url=$1 [QSA,L]
```

### Configuración Nginx

```nginx
# Configuración para webservice
location /api/ {
    rewrite ^/api/(.*)$ /webservice/dispatcher.php?url=$1 last;
}
```

## URLs de Acceso

### Estructura de URL Base
```
https://tudominio.com/api/
```

### Formato de Autenticación
```
https://WS_KEY@tudominio.com/api/
```

### Ejemplo de URL Completa
```
https://ABCD1234EFGH5678@mitienda.com/api/products/1
```

## Prueba de Configuración

### 1. Verificar Conectividad
```bash
curl -X GET "https://TU_WS_KEY@tudominio.com/api/"
```

**Respuesta Esperada**: Lista de recursos disponibles en XML

### 2. Prueba de Recurso Específico
```bash
curl -X GET "https://TU_WS_KEY@tudominio.com/api/products/1"
```

**Respuesta Esperada**: Datos del producto con ID 1

### 3. Verificar Permisos
```bash
curl -X POST "https://TU_WS_KEY@tudominio.com/api/products" \
  -H "Content-Type: application/xml" \
  -d '<prestashop><product><name>Test</name></product></prestashop>'
```

## Solución de Problemas Comunes

### Error 401 - No Autorizado
- Verificar que la clave WS_KEY es correcta
- Confirmar que el webservice está habilitado
- Revisar permisos de la clave

### Error 404 - Recurso No Encontrado
- Verificar que la URL es correcta
- Confirmar que el recurso existe
- Revisar configuración del servidor web

### Error 500 - Error del Servidor
- Verificar logs de PHP
- Confirmar configuración de base de datos
- Revisar permisos de archivos

### Error de Formato XML
- Verificar estructura XML correcta
- Confirmar encoding UTF-8
- Revisar caracteres especiales

## Configuración Avanzada

### Múltiples Claves de Acceso
- Crear diferentes claves para diferentes aplicaciones
- Configurar permisos específicos por uso
- Implementar rotación de claves

### Límites de Velocidad
- Configurar en servidor web
- Implementar en aplicación cliente
- Monitorear uso de API

### Logging y Monitoreo
- Habilitar logs de webservice
- Configurar alertas por errores
- Implementar métricas de uso

---

**Notas Importantes**:
- Mantener las claves de API seguras
- Usar HTTPS en producción
- Implementar manejo de errores robusto
- Realizar pruebas en entorno de desarrollo primero