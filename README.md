# Requerimientos API Bird.com para Integración PrestaShop - KOAJ.co

## Resumen Ejecutivo

Este documento especifica los **requerimientos técnicos estrictos** para configurar la API de PrestaShop de KOAJ.co de manera que Bird.com pueda realizar una integración transparente para ejecutar:

- ✅ **Marketing Journeys** automatizados
- ✅ **Triggers de eventos** en tiempo real
- ✅ **Sincronización de catálogo e inventario**
- ✅ **Chatbots de WhatsApp** con IA asistida
- ✅ **Agentes IA** configurados y entrenados en Bird.com

---

## 🎯 Objetivo de la Integración

**KOAJ.co** (desplegado en PrestaShop) necesita entregar datos estructurados y eventos en tiempo real a **Bird.com** para potenciar:

1. **Conversaciones de WhatsApp** con acceso a catálogo en vivo
2. **Marketing automation** basado en comportamiento del cliente
3. **Asistencia IA** con información de productos e inventario actualizada
4. **Customer journeys** personalizados y triggers automáticos

---

## 📋 Requerimientos Críticos para Ejecutivo PrestaShop

### 1. **API Webservice Configuration**

#### Configuración Obligatoria
```
✅ Webservice habilitado en PrestaShop
✅ Formato de salida: JSON (prioritario)
✅ Rate limit: Mínimo 1000 requests/hora
✅ SSL/HTTPS obligatorio
✅ API Key dedicada para Bird.com
```

#### Permisos de API Key Bird.com
| Recurso | View | Add | Edit | Delete |
|---------|------|-----|------|--------|
| **products** | ✅ | ❌ | ❌ | ❌ |
| **categories** | ✅ | ❌ | ❌ | ❌ |
| **stock_availables** | ✅ | ❌ | ✅ | ❌ |
| **combinations** | ✅ | ❌ | ❌ | ❌ |
| **customers** | ✅ | ✅ | ✅ | ❌ |
| **orders** | ✅ | ✅ | ✅ | ❌ |
| **order_details** | ✅ | ❌ | ❌ | ❌ |
| **addresses** | ✅ | ✅ | ✅ | ❌ |
| **carts** | ✅ | ❌ | ✅ | ✅ |
| **images** | ✅ | ❌ | ❌ | ❌ |

### 2. **Endpoints Críticos Requeridos**

#### URLs Base Obligatorias
```
https://koaj.co/api/products?output_format=JSON
https://koaj.co/api/stock_availables?output_format=JSON
https://koaj.co/api/categories?output_format=JSON
https://koaj.co/api/orders?output_format=JSON
https://koaj.co/api/customers?output_format=JSON
https://koaj.co/api/carts?output_format=JSON
```

#### Filtros Específicos Requeridos
```
# Productos activos con stock
/api/products?filter[active]=1&filter[stock]=>0&output_format=JSON

# Stock por producto específico
/api/stock_availables?filter[id_product]={id}&output_format=JSON

# Pedidos por estado
/api/orders?filter[current_state]={state_id}&output_format=JSON

# Carritos abandonados (últimas 24h)
/api/carts?filter[date_upd]={date_range}&output_format=JSON
```

---

## 🔄 Arquitectura de Datos - JSON Schemas Requeridos

### 📦 Producto para Bird Catalog API

#### Formato JSON Obligatorio
```json
{
  "externalProductId": "string", // ID único del producto
  "externalCatalogId": "koaj-catalog-{category}", // ID del catálogo
  "title": "string", // Nombre del producto
  "description": "string", // Descripción completa
  "price": {
    "amount": 0, // Precio en centavos (COP)
    "exponent": 2, // Número de decimales
    "currencyCode": "COP" // Moneda colombiana
  },
  "availability": "in_stock|out_of_stock|limited_stock", // Estado de stock
  "imageUrl": "https://cdn.koaj.co/image.jpg", // URL imagen principal
  "additionalImages": [
    "https://cdn.koaj.co/image2.jpg",
    "https://cdn.koaj.co/image3.jpg"
  ],
  "category": "string", // Categoría principal
  "subcategory": "string", // Subcategoría
  "brand": "KOAJ", // Marca
  "sku": "string", // SKU único
  "attributes": {
    "color": "string",
    "size": "string",
    "material": "string",
    "gender": "hombre|mujer|unisex"
  },
  "seoUrl": "https://koaj.co/producto/{slug}", // URL del producto
  "stockQuantity": 0, // Cantidad disponible
  "isActive": true, // Producto activo
  "lastUpdated": "2024-01-01T00:00:00Z" // Timestamp última actualización
}
```

### 👤 Cliente para Bird CRM

```json
{
  "externalCustomerId": "string", // ID único del cliente
  "email": "string", // Email principal
  "phone": "+57{number}", // Teléfono con código país
  "firstName": "string",
  "lastName": "string",
  "dateOfBirth": "YYYY-MM-DD",
  "gender": "M|F|U", // Masculino, Femenino, Unspecified
  "addresses": [
    {
      "type": "billing|shipping",
      "street": "string",
      "city": "string",
      "state": "string",
      "country": "CO", // Colombia
      "postalCode": "string",
      "isDefault": true
    }
  ],
  "preferences": {
    "communicationChannel": "whatsapp|email|sms",
    "language": "es-CO",
    "marketingOptIn": true
  },
  "customerSegment": "new|regular|vip|inactive",
  "totalOrders": 0,
  "totalSpent": {
    "amount": 0,
    "currencyCode": "COP"
  },
  "lastOrderDate": "2024-01-01T00:00:00Z",
  "registrationDate": "2024-01-01T00:00:00Z"
}
```

### 🛒 Pedido para Marketing Triggers

```json
{
  "externalOrderId": "string", // ID único del pedido
  "customerId": "string", // ID del cliente
  "orderStatus": "pending|processing|shipped|delivered|cancelled",
  "orderDate": "2024-01-01T00:00:00Z",
  "deliveryDate": "2024-01-01T00:00:00Z", // Fecha estimada/real
  "total": {
    "amount": 0, // Total en centavos
    "currencyCode": "COP"
  },
  "subtotal": {
    "amount": 0,
    "currencyCode": "COP"
  },
  "tax": {
    "amount": 0,
    "currencyCode": "COP"
  },
  "shipping": {
    "amount": 0,
    "currencyCode": "COP",
    "method": "string",
    "carrier": "string",
    "trackingNumber": "string"
  },
  "discount": {
    "amount": 0,
    "currencyCode": "COP",
    "couponCode": "string"
  },
  "products": [
    {
      "externalProductId": "string",
      "title": "string",
      "sku": "string",
      "quantity": 0,
      "unitPrice": {
        "amount": 0,
        "currencyCode": "COP"
      },
      "totalPrice": {
        "amount": 0,
        "currencyCode": "COP"
      },
      "imageUrl": "string"
    }
  ],
  "shippingAddress": {
    "firstName": "string",
    "lastName": "string",
    "street": "string",
    "city": "string",
    "state": "string",
    "country": "CO",
    "postalCode": "string",
    "phone": "+57{number}"
  },
  "paymentMethod": "credit_card|debit_card|pse|nequi|bancolombia|cash",
  "paymentStatus": "pending|paid|failed|refunded"
}
```

---

## 🔗 Webhooks Obligatorios para Bird.com

### Configuración de Webhooks

#### URL de Destino Bird.com
```
https://api.bird.com/webhooks/prestashop/{KOAJ_CLIENT_ID}
```

#### Headers Requeridos
```http
Content-Type: application/json
Authorization: Bearer {BIRD_API_KEY}
X-Webhook-Source: PrestaShop-KOAJ
X-Webhook-Timestamp: {unix_timestamp}
X-Webhook-Signature: {hmac_sha256_signature}
```

### Eventos Críticos que Bird.com Necesita

#### 1. **Gestión de Productos**
```http
POST /webhooks/prestashop/{client_id}
{
  "event": "product.updated",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "productId": "12345",
    "changes": ["price", "stock", "availability"],
    "currentStock": 15,
    "price": {
      "amount": 8999000, // $89,990 COP en centavos
      "currencyCode": "COP"
    },
    "availability": "in_stock"
  }
}
```

#### 2. **Eventos de Pedidos**
```http
# Nuevo pedido
POST /webhooks/prestashop/{client_id}
{
  "event": "order.created",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "orderId": "ORD-12345",
    "customerId": "CUST-67890",
    "orderTotal": {
      "amount": 12999000, // $129,990 COP
      "currencyCode": "COP"
    },
    "products": [...], // Array de productos
    "customerEmail": "cliente@email.com",
    "customerPhone": "+573001234567"
  }
}

# Cambio de estado de pedido
POST /webhooks/prestashop/{client_id}
{
  "event": "order.status_changed",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "orderId": "ORD-12345",
    "previousStatus": "processing",
    "newStatus": "shipped",
    "trackingNumber": "TRK123456789",
    "estimatedDelivery": "2024-01-05T00:00:00Z"
  }
}
```

#### 3. **Carrito Abandonado**
```http
POST /webhooks/prestashop/{client_id}
{
  "event": "cart.abandoned",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "cartId": "CART-12345",
    "customerId": "CUST-67890",
    "customerEmail": "cliente@email.com",
    "customerPhone": "+573001234567",
    "cartTotal": {
      "amount": 8999000,
      "currencyCode": "COP"
    },
    "products": [...], // Productos en el carrito
    "lastActivity": "2024-01-01T10:30:00Z",
    "abandonedAt": "2024-01-01T12:30:00Z" // 2 horas sin actividad
  }
}
```

#### 4. **Gestión de Clientes**
```http
# Nuevo registro
POST /webhooks/prestashop/{client_id}
{
  "event": "customer.registered",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "customerId": "CUST-67890",
    "email": "cliente@email.com",
    "phone": "+573001234567",
    "firstName": "Juan",
    "lastName": "Pérez",
    "registrationSource": "website|whatsapp|store",
    "optInMarketing": true
  }
}

# Actualización de perfil
POST /webhooks/prestashop/{client_id}
{
  "event": "customer.updated",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "customerId": "CUST-67890",
    "changes": ["phone", "address"],
    "newPhone": "+573009876543",
    "newAddress": {...}
  }
}
```

#### 5. **Stock y Inventario**
```http
POST /webhooks/prestashop/{client_id}
{
  "event": "stock.low_inventory",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "productId": "12345",
    "sku": "KOAJ-CAM-001",
    "currentStock": 3,
    "threshold": 5,
    "productName": "Camisa Manga Larga Azul",
    "category": "Camisas"
  }
}

POST /webhooks/prestashop/{client_id}
{
  "event": "stock.out_of_stock",
  "timestamp": "2024-01-01T00:00:00Z",
  "data": {
    "productId": "12345",
    "sku": "KOAJ-CAM-001",
    "productName": "Camisa Manga Larga Azul",
    "lastAvailableDate": "2024-01-01T08:30:00Z"
  }
}
```

---

## 🤖 Requerimientos WhatsApp Chatbot

### Product Catalog Format para WhatsApp

Bird.com necesita que PrestaShop exponga un endpoint específico para el catálogo de WhatsApp:

#### Endpoint Dedicado
```
GET https://koaj.co/api/bird/whatsapp-catalog
Authorization: Bearer {BIRD_API_KEY}
Accept: application/json
```

#### Respuesta Requerida
```json
{
  "catalog": {
    "id": "koaj-whatsapp-catalog",
    "name": "KOAJ Catálogo WhatsApp",
    "lastUpdated": "2024-01-01T00:00:00Z",
    "currency": "COP",
    "products": [
      {
        "id": "12345",
        "title": "Camisa Manga Larga Azul - KOAJ",
        "description": "Camisa de manga larga en algodón premium, color azul marino. Perfecto para looks casuales y formales.",
        "price": 89990, // Precio en centavos COP
        "imageUrl": "https://cdn.koaj.co/camisas/12345-main.jpg",
        "availability": "in_stock",
        "category": "Camisas",
        "subcategory": "Manga Larga",
        "attributes": {
          "color": "Azul Marino",
          "sizes": ["S", "M", "L", "XL"],
          "material": "100% Algodón",
          "gender": "Hombre"
        },
        "sku": "KOAJ-CAM-ML-AZ-001",
        "url": "https://koaj.co/hombre/camisas/camisa-manga-larga-azul",
        "stockQuantity": 15,
        "tags": ["nueva-coleccion", "algodón", "casual", "formal"]
      }
    ],
    "totalProducts": 150,
    "totalPages": 15,
    "currentPage": 1
  }
}
```

### Vector Store para IA Bird.com

Para entrenar los agentes IA, Bird.com necesita acceso a información estructurada:

#### Endpoint de Información de Productos
```
GET https://koaj.co/api/bird/product-info/{product_id}
```

#### Respuesta para Entrenamiento IA
```json
{
  "productInfo": {
    "id": "12345",
    "name": "Camisa Manga Larga Azul",
    "brand": "KOAJ",
    "description": "Descripción detallada del producto...",
    "features": [
      "100% algodón premium",
      "Manga larga con puños ajustables",
      "Cuello tradicional",
      "Corte slim fit",
      "Fácil cuidado - lavable en máquina"
    ],
    "useCases": [
      "Reuniones de trabajo",
      "Cenas casuales",
      "Eventos sociales",
      "Uso diario"
    ],
    "careInstructions": "Lavar en agua fría, no usar blanqueador, planchar a temperatura media",
    "sizing": {
      "guide": "https://koaj.co/guia-tallas",
      "measurements": {
        "S": {"chest": "90-94cm", "length": "72cm"},
        "M": {"chest": "95-99cm", "length": "74cm"},
        "L": {"chest": "100-104cm", "length": "76cm"},
        "XL": {"chest": "105-109cm", "length": "78cm"}
      }
    },
    "relatedProducts": ["12346", "12347", "12348"],
    "crossSell": ["67890", "67891"], // Accesorios recomendados
    "reviews": {
      "averageRating": 4.5,
      "totalReviews": 23,
      "highlights": ["Calidad excelente", "Talla perfecta", "Color vibrante"]
    }
  }
}
```

---

## ⚡ Marketing Journeys y Triggers

### Customer Journey Triggers

Bird.com necesita recibir estos eventos para activar journeys automáticos:

#### 1. **Welcome Journey** (Cliente Nuevo)
```json
{
  "trigger": "customer.registered",
  "journeyId": "welcome-new-customer",
  "delay": 0, // Inmediato
  "conditions": {
    "firstTimeCustomer": true,
    "optInMarketing": true
  }
}
```

#### 2. **Abandoned Cart Recovery**
```json
{
  "trigger": "cart.abandoned",
  "journeyId": "cart-recovery-sequence",
  "delay": 7200, // 2 horas después
  "conditions": {
    "cartValue": ">50000", // Más de $50,000 COP
    "hasEmail": true,
    "hasWhatsApp": true
  }
}
```

#### 3. **Post-Purchase Follow-up**
```json
{
  "trigger": "order.delivered",
  "journeyId": "post-purchase-experience",
  "delay": 172800, // 2 días después
  "conditions": {
    "orderValue": ">0",
    "deliveryConfirmed": true
  }
}
```

#### 4. **Win-Back Campaign**
```json
{
  "trigger": "customer.inactive",
  "journeyId": "win-back-sequence",
  "delay": 0,
  "conditions": {
    "daysSinceLastOrder": ">90",
    "totalPreviousOrders": ">0",
    "optInMarketing": true
  }
}
```

### Event Data for Journey Personalization

Cada trigger debe incluir datos para personalización:

```json
{
  "triggerData": {
    "customer": {
      "firstName": "string",
      "lastName": "string",
      "preferredName": "string",
      "email": "string",
      "phone": "string",
      "segment": "new|regular|vip",
      "preferredChannel": "whatsapp|email|sms"
    },
    "context": {
      "triggerEvent": "string",
      "timestamp": "string",
      "sessionData": {...},
      "browserData": {...},
      "deviceType": "mobile|desktop|tablet"
    },
    "recommendations": {
      "products": [...], // Productos recomendados
      "categories": [...], // Categorías de interés
      "personalizedOffers": [...] // Ofertas específicas
    }
  }
}
```

---

## 🔐 Configuración de Seguridad

### API Authentication

#### Para Endpoints Públicos (Catálogo)
```http
Authorization: Bearer {PRESTASHOP_API_KEY}
X-Client-ID: bird-com-koaj
X-Timestamp: {unix_timestamp}
```

#### Para Webhooks (Bird.com a PrestaShop)
```http
Content-Type: application/json
Authorization: Bearer {BIRD_API_KEY}
X-Webhook-Source: Bird-KOAJ
X-Webhook-Signature: sha256={hmac_signature}
X-Webhook-Timestamp: {unix_timestamp}
```

### Rate Limiting Requirements

```
📊 Minimum Rate Limits:
- Product Catalog API: 500 requests/hour
- Customer Data API: 1000 requests/hour  
- Order Management API: 2000 requests/hour
- Real-time Stock API: 10 requests/minute
- Webhook Delivery: 100 requests/minute
```

### Data Validation

Todos los endpoints deben validar:
- ✅ Estructura JSON válida
- ✅ Campos obligatorios presentes
- ✅ Tipos de datos correctos
- ✅ Valores dentro de rangos permitidos
- ✅ Timestamps en formato ISO 8601
- ✅ URLs válidas y accesibles
- ✅ Precios en formato correcto (centavos)

---

## 📊 Monitoreo y Reporting

### Métricas Obligatorias para Bird.com

PrestaShop debe exponer métricas en tiempo real:

#### Endpoint de Métricas
```
GET https://koaj.co/api/bird/metrics
Authorization: Bearer {BIRD_API_KEY}
```

#### Respuesta de Métricas
```json
{
  "metrics": {
    "timestamp": "2024-01-01T00:00:00Z",
    "catalog": {
      "totalProducts": 1500,
      "activeProducts": 1350,
      "outOfStockProducts": 45,
      "lowStockProducts": 23, // < 5 unidades
      "lastCatalogUpdate": "2024-01-01T09:30:00Z"
    },
    "orders": {
      "dailyOrders": 45,
      "weeklyOrders": 280,
      "monthlyOrders": 1200,
      "averageOrderValue": {
        "amount": 15999000, // $159,990 COP
        "currencyCode": "COP"
      }
    },
    "customers": {
      "totalCustomers": 5500,
      "activeCustomers": 2100, // Últimos 90 días
      "newCustomersToday": 12,
      "customerSegments": {
        "new": 1200,
        "regular": 3500,
        "vip": 800
      }
    },
    "inventory": {
      "totalSKUs": 1500,
      "lowStockAlerts": 23,
      "stockMovement": {
        "inbound": 150, // Productos recibidos hoy
        "outbound": 89  // Productos vendidos hoy
      }
    }
  }
}
```

### Error Tracking y Logs

#### Endpoint de Health Check
```
GET https://koaj.co/api/bird/health
```

#### Respuesta Health Check
```json
{
  "status": "healthy|degraded|unhealthy",
  "timestamp": "2024-01-01T00:00:00Z",
  "services": {
    "database": "up",
    "cache": "up",
    "cdn": "up",
    "paymentGateway": "up",
    "inventorySystem": "up"
  },
  "performance": {
    "averageResponseTime": 250, // ms
    "uptime": 99.9, // %
    "requestsPerMinute": 45
  },
  "lastError": {
    "timestamp": "2024-01-01T08:15:00Z",
    "type": "stock_sync_delay",
    "message": "Inventory sync delayed by 5 minutes",
    "resolved": true
  }
}
```

---

## 🚀 Configuración Paso a Paso

### Fase 1: Configuración Básica API

1. **Habilitar Webservice PrestaShop**
   ```
   - Admin Panel → Parámetros Avanzados → Webservice
   - Activar "Habilitar webservice de PrestaShop"
   - Generar API Key para Bird.com
   ```

2. **Configurar Permisos API Key**
   ```
   - Aplicar tabla de permisos especificada arriba
   - Configurar rate limiting a 1000 req/hora mínimo
   - Activar formato JSON por defecto
   ```

3. **Verificar Endpoints**
   ```bash
   # Test básico
   curl -X GET "https://koaj.co/api/products?output_format=JSON" \
        -H "Authorization: Bearer {API_KEY}"
   ```

### Fase 2: Implementación de Webhooks

1. **Instalar Módulo de Webhooks**
   ```php
   // Verificar que el módulo de webhooks está activo
   // Configurar URLs de destino Bird.com
   ```

2. **Configurar Eventos**
   ```
   - product.updated
   - order.created
   - order.status_changed
   - cart.abandoned
   - customer.registered
   - stock.low_inventory
   ```

3. **Testing de Webhooks**
   ```bash
   # Simular evento para verificar entrega
   curl -X POST "https://api.bird.com/webhooks/test" \
        -H "Content-Type: application/json" \
        -d '{"event":"product.updated","test":true}'
   ```

### Fase 3: Configuración WhatsApp Catalog

1. **Crear Endpoint Dedicado**
   ```
   GET /api/bird/whatsapp-catalog
   - Implementar paginación
   - Incluir filtros por categoría
   - Optimizar para rendimiento
   ```

2. **Configurar CDN para Imágenes**
   ```
   - Verificar URLs de imágenes accesibles
   - Configurar compresión automática
   - Implementar fallback para imágenes faltantes
   ```

### Fase 4: Testing y Validación

1. **Test de Integración**
   ```bash
   # Verificar todos los endpoints críticos
   npm run test:bird-integration
   ```

2. **Validación de Datos**
   ```
   - Verificar estructura JSON
   - Confirmar tipos de datos
   - Validar formatos de precio (centavos COP)
   ```

3. **Performance Testing**
   ```
   - Load testing con 1000 req/hora
   - Verificar tiempo de respuesta < 500ms
   - Confirmar estabilidad bajo carga
   ```

---

## 📞 Contactos y Soporte

### Equipo KOAJ - Neero
- **Technical Lead**: [Contacto técnico]
- **Project Manager**: [PM contact]
- **Bird.com Integration**: [Bird contact]

### Bird.com Support
- **API Documentation**: https://docs.bird.com/api
- **Technical Support**: [Bird support contact]
- **Integration Specialist**: [Specialist contact]

### PrestaShop Implementation
- **Developer Lead**: [PrestaShop dev contact]
- **API Specialist**: [API specialist]

---

## 🔍 Testing y Validación

### Checklist de Implementación

#### ✅ API Configuration
- [ ] Webservice habilitado
- [ ] API Key generada para Bird.com
- [ ] Permisos configurados según tabla
- [ ] Rate limiting configurado (1000+ req/hora)
- [ ] SSL/HTTPS activo
- [ ] Formato JSON por defecto

#### ✅ Endpoints Críticos
- [ ] `/api/products` funcionando
- [ ] `/api/stock_availables` funcionando
- [ ] `/api/orders` funcionando
- [ ] `/api/customers` funcionando
- [ ] `/api/bird/whatsapp-catalog` implementado
- [ ] `/api/bird/metrics` implementado
- [ ] `/api/bird/health` implementado

#### ✅ Webhooks
- [ ] Módulo de webhooks instalado
- [ ] URL Bird.com configurada
- [ ] Eventos críticos configurados
- [ ] Headers de seguridad implementados
- [ ] Testing de entrega exitoso

#### ✅ Data Format
- [ ] JSON schemas validados
- [ ] Precios en centavos COP
- [ ] Timestamps en ISO 8601
- [ ] URLs de imágenes accesibles
- [ ] Campos obligatorios presentes

#### ✅ Security
- [ ] API Keys configuradas
- [ ] HMAC signatures implementadas
- [ ] Rate limiting activo
- [ ] HTTPS obligatorio
- [ ] Input validation activa

#### ✅ Performance
- [ ] Tiempo respuesta < 500ms
- [ ] Load testing aprobado
- [ ] CDN configurado para imágenes
- [ ] Caché implementado
- [ ] Monitoring activo

---

## 📈 Métricas de Éxito

### KPIs de Integración

1. **API Performance**
   - Tiempo de respuesta promedio: < 300ms
   - Uptime: > 99.5%
   - Error rate: < 1%

2. **Data Synchronization**
   - Tiempo de sincronización catálogo: < 5 minutos
   - Precisión de stock: 99.9%
   - Latencia de webhooks: < 30 segundos

3. **WhatsApp Chatbot**
   - Tiempo de respuesta queries: < 2 segundos
   - Precisión de información: > 95%
   - Disponibilidad de catálogo: 24/7

4. **Marketing Automation**
   - Delivery rate webhooks: > 99%
   - Journey trigger accuracy: > 98%
   - Customer segmentation precision: > 95%

---

**🎯 Objetivo Final**: Integración transparente y en tiempo real entre KOAJ.co (PrestaShop) y Bird.com para potenciar la experiencia del cliente a través de WhatsApp y marketing automation inteligente.

---

*Documento creado por Neero para la integración KOAJ.co + Bird.com*  
*Última actualización: 2024-12-26*