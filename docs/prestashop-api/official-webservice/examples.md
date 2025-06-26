# Ejemplos de Uso API PrestaShop

## Configuración Inicial

### Variables de Entorno
```bash
# .env
PRESTASHOP_WS_KEY=ABCD1234EFGH5678IJKL9012MNOP3456
PRESTASHOP_SHOP_URL=https://mitienda.com
```

## Ejemplos por Lenguaje

### 🐘 PHP

#### Configuración Básica
```php
<?php
class PrestaShopAPI {
    private $ws_key;
    private $shop_url;
    
    public function __construct($ws_key, $shop_url) {
        $this->ws_key = $ws_key;
        $this->shop_url = rtrim($shop_url, '/');
    }
    
    public function get($resource, $id = null, $params = []) {
        $url = $this->shop_url . '/api/' . $resource;
        if ($id) $url .= '/' . $id;
        
        $params['output_format'] = 'JSON';
        if (!empty($params)) {
            $url .= '?' . http_build_query($params);
        }
        
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_USERPWD, $this->ws_key . ':');
        curl_setopt($ch, CURLOPT_HTTPHEADER, ['Accept: application/json']);
        
        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);
        
        if ($httpCode !== 200) {
            throw new Exception("API Error: HTTP $httpCode");
        }
        
        return json_decode($response, true);
    }
    
    public function post($resource, $data) {
        $url = $this->shop_url . '/api/' . $resource;
        
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_USERPWD, $this->ws_key . ':');
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        curl_setopt($ch, CURLOPT_HTTPHEADER, [
            'Content-Type: application/xml',
            'Accept: application/json'
        ]);
        
        $response = curl_exec($ch);
        $httpCode = curl_getinfo($ch, CURLINFO_HTTP_CODE);
        curl_close($ch);
        
        if ($httpCode !== 201) {
            throw new Exception("API Error: HTTP $httpCode - $response");
        }
        
        return json_decode($response, true);
    }
}\n\n// Uso\n$api = new PrestaShopAPI(\n    $_ENV['PRESTASHOP_WS_KEY'],\n    $_ENV['PRESTASHOP_SHOP_URL']\n);\n\n// Obtener todos los productos\n$products = $api->get('products');\necho json_encode($products, JSON_PRETTY_PRINT);\n\n// Obtener producto específico\n$product = $api->get('products', 1);\necho json_encode($product, JSON_PRETTY_PRINT);\n\n// Filtrar productos activos\n$activeProducts = $api->get('products', null, [\n    'filter[active]' => 1,\n    'limit' => 10\n]);\n?>\n```\n\n#### Crear Producto\n```php\n<?php\n$newProductXML = '\n<prestashop>\n    <product>\n        <name>\n            <language id=\"1\">Nuevo Producto PHP</language>\n        </name>\n        <description>\n            <language id=\"1\">Descripción del producto</language>\n        </description>\n        <price>29.99</price>\n        <active>1</active>\n        <id_category_default>2</id_category_default>\n        <categories>\n            <category>\n                <id>2</id>\n            </category>\n        </categories>\n    </product>\n</prestashop>';\n\ntry {\n    $result = $api->post('products', $newProductXML);\n    echo \"Producto creado con ID: \" . $result['product']['id'];\n} catch (Exception $e) {\n    echo \"Error: \" . $e->getMessage();\n}\n?>\n```\n\n### 🟨 JavaScript (Node.js)\n\n#### Configuración con Axios\n```javascript\nconst axios = require('axios');\nrequire('dotenv').config();\n\nclass PrestaShopAPI {\n    constructor(wsKey, shopUrl) {\n        this.wsKey = wsKey;\n        this.shopUrl = shopUrl.replace(/\\/$/, '');\n        \n        this.client = axios.create({\n            baseURL: `${this.shopUrl}/api/`,\n            auth: {\n                username: this.wsKey,\n                password: ''\n            },\n            headers: {\n                'Accept': 'application/json'\n            }\n        });\n    }\n    \n    async get(resource, id = null, params = {}) {\n        try {\n            params.output_format = 'JSON';\n            const url = id ? `${resource}/${id}` : resource;\n            const response = await this.client.get(url, { params });\n            return response.data;\n        } catch (error) {\n            throw new Error(`API Error: ${error.response?.status} - ${error.response?.data}`);\n        }\n    }\n    \n    async post(resource, data) {\n        try {\n            const response = await this.client.post(resource, data, {\n                headers: {\n                    'Content-Type': 'application/xml'\n                }\n            });\n            return response.data;\n        } catch (error) {\n            throw new Error(`API Error: ${error.response?.status} - ${error.response?.data}`);\n        }\n    }\n}\n\n// Uso\nconst api = new PrestaShopAPI(\n    process.env.PRESTASHOP_WS_KEY,\n    process.env.PRESTASHOP_SHOP_URL\n);\n\n// Ejemplos de uso\nasync function ejemplos() {\n    try {\n        // Obtener productos\n        const products = await api.get('products', null, {\n            limit: 5,\n            'filter[active]': 1\n        });\n        console.log('Productos:', JSON.stringify(products, null, 2));\n        \n        // Obtener producto específico\n        const product = await api.get('products', 1);\n        console.log('Producto 1:', JSON.stringify(product, null, 2));\n        \n        // Obtener pedidos recientes\n        const orders = await api.get('orders', null, {\n            sort: 'id_DESC',\n            limit: 10\n        });\n        console.log('Pedidos recientes:', JSON.stringify(orders, null, 2));\n        \n    } catch (error) {\n        console.error('Error:', error.message);\n    }\n}\n\nejemplos();\n```\n\n#### Crear Producto con Node.js\n```javascript\nasync function crearProducto() {\n    const newProductXML = `\n    <prestashop>\n        <product>\n            <name>\n                <language id=\"1\">Producto desde Node.js</language>\n            </name>\n            <description>\n                <language id=\"1\">Descripción del producto</language>\n            </description>\n            <price>39.99</price>\n            <active>1</active>\n            <id_category_default>2</id_category_default>\n        </product>\n    </prestashop>`;\n    \n    try {\n        const result = await api.post('products', newProductXML);\n        console.log('Producto creado:', result);\n    } catch (error) {\n        console.error('Error creando producto:', error.message);\n    }\n}\n\ncrearProducto();\n```\n\n### 🐍 Python\n\n#### Configuración con Requests\n```python\nimport requests\nfrom requests.auth import HTTPBasicAuth\nimport json\nimport os\nfrom dotenv import load_dotenv\n\nload_dotenv()\n\nclass PrestaShopAPI:\n    def __init__(self, ws_key, shop_url):\n        self.ws_key = ws_key\n        self.shop_url = shop_url.rstrip('/')\n        self.auth = HTTPBasicAuth(ws_key, '')\n        \n    def get(self, resource, resource_id=None, params=None):\n        if params is None:\n            params = {}\n        \n        params['output_format'] = 'JSON'\n        \n        url = f\"{self.shop_url}/api/{resource}\"\n        if resource_id:\n            url += f\"/{resource_id}\"\n            \n        response = requests.get(\n            url,\n            auth=self.auth,\n            params=params,\n            headers={'Accept': 'application/json'}\n        )\n        \n        if response.status_code != 200:\n            raise Exception(f\"API Error: {response.status_code} - {response.text}\")\n            \n        return response.json()\n    \n    def post(self, resource, data):\n        url = f\"{self.shop_url}/api/{resource}\"\n        \n        response = requests.post(\n            url,\n            auth=self.auth,\n            data=data,\n            headers={\n                'Content-Type': 'application/xml',\n                'Accept': 'application/json'\n            }\n        )\n        \n        if response.status_code != 201:\n            raise Exception(f\"API Error: {response.status_code} - {response.text}\")\n            \n        return response.json()\n\n# Uso\napi = PrestaShopAPI(\n    os.getenv('PRESTASHOP_WS_KEY'),\n    os.getenv('PRESTASHOP_SHOP_URL')\n)\n\n# Ejemplos\ntry:\n    # Obtener productos\n    products = api.get('products', params={\n        'filter[active]': 1,\n        'limit': 5\n    })\n    print(json.dumps(products, indent=2))\n    \n    # Obtener producto específico\n    product = api.get('products', 1)\n    print(json.dumps(product, indent=2))\n    \n    # Obtener clientes\n    customers = api.get('customers', params={'limit': 10})\n    print(json.dumps(customers, indent=2))\n    \nexcept Exception as e:\n    print(f\"Error: {e}\")\n```\n\n#### Crear Producto con Python\n```python\ndef crear_producto():\n    new_product_xml = \"\"\"\n    <prestashop>\n        <product>\n            <name>\n                <language id=\"1\">Producto desde Python</language>\n            </name>\n            <description>\n                <language id=\"1\">Descripción del producto</language>\n            </description>\n            <price>49.99</price>\n            <active>1</active>\n            <id_category_default>2</id_category_default>\n        </product>\n    </prestashop>\"\"\"\n    \n    try:\n        result = api.post('products', new_product_xml)\n        print(f\"Producto creado con ID: {result['product']['id']}\")\n        return result\n    except Exception as e:\n        print(f\"Error creando producto: {e}\")\n        return None\n\ncrear_producto()\n```\n\n## Ejemplos de Casos de Uso\n\n### 📦 Gestión de Inventario\n\n```php\n// PHP - Actualizar stock de productos\nfunction actualizarStock($api, $productId, $quantity) {\n    // Primero obtener el producto\n    $product = $api->get('products', $productId);\n    \n    // Actualizar cantidad\n    $updateXML = \"\n    <prestashop>\n        <product>\n            <id>$productId</id>\n            <quantity>$quantity</quantity>\n        </product>\n    </prestashop>\";\n    \n    return $api->put('products', $productId, $updateXML);\n}\n```\n\n### 🛒 Procesamiento de Pedidos\n\n```javascript\n// Node.js - Obtener pedidos pendientes\nasync function obtenerPedidosPendientes(api) {\n    try {\n        const orders = await api.get('orders', null, {\n            'filter[current_state]': 1, // Estado pendiente\n            sort: 'date_add_DESC',\n            limit: 50\n        });\n        \n        for (const order of orders.orders) {\n            console.log(`Pedido #${order.id} - Cliente: ${order.id_customer}`);\n            \n            // Obtener detalles del pedido\n            const orderDetails = await api.get('order_details', null, {\n                'filter[id_order]': order.id\n            });\n            \n            console.log('Productos:', orderDetails);\n        }\n        \n        return orders;\n    } catch (error) {\n        console.error('Error obteniendo pedidos:', error.message);\n    }\n}\n```\n\n### 👥 Gestión de Clientes\n\n```python\n# Python - Buscar clientes por email\ndef buscar_cliente_por_email(api, email):\n    try:\n        customers = api.get('customers', params={\n            'filter[email]': email\n        })\n        \n        if customers['customers']:\n            customer_id = customers['customers'][0]['id']\n            customer_detail = api.get('customers', customer_id)\n            return customer_detail\n        else:\n            return None\n            \n    except Exception as e:\n        print(f\"Error buscando cliente: {e}\")\n        return None\n\n# Uso\ncliente = buscar_cliente_por_email(api, 'cliente@ejemplo.com')\nif cliente:\n    print(f\"Cliente encontrado: {cliente['customer']['firstname']} {cliente['customer']['lastname']}\")\nelse:\n    print(\"Cliente no encontrado\")\n```\n\n## Manejo de Errores\n\n### PHP\n```php\ntry {\n    $products = $api->get('products');\n} catch (Exception $e) {\n    switch (true) {\n        case strpos($e->getMessage(), '401') !== false:\n            echo \"Error de autenticación - Verificar ws_key\";\n            break;\n        case strpos($e->getMessage(), '403') !== false:\n            echo \"Permisos insuficientes\";\n            break;\n        case strpos($e->getMessage(), '404') !== false:\n            echo \"Recurso no encontrado\";\n            break;\n        default:\n            echo \"Error desconocido: \" . $e->getMessage();\n    }\n}\n```\n\n### JavaScript\n```javascript\ntry {\n    const products = await api.get('products');\n} catch (error) {\n    if (error.message.includes('401')) {\n        console.error('Error de autenticación - Verificar ws_key');\n    } else if (error.message.includes('403')) {\n        console.error('Permisos insuficientes');\n    } else if (error.message.includes('404')) {\n        console.error('Recurso no encontrado');\n    } else {\n        console.error('Error desconocido:', error.message);\n    }\n}\n```\n\n## Mejores Prácticas\n\n### 1. Rate Limiting\n```javascript\n// Implementar delay entre requests\nconst delay = ms => new Promise(resolve => setTimeout(resolve, ms));\n\nasync function processProductsBatch(products) {\n    for (const product of products) {\n        await processProduct(product);\n        await delay(100); // 100ms entre requests\n    }\n}\n```\n\n### 2. Caché Local\n```php\n// PHP - Implementar caché simple\nclass CachedPrestaShopAPI extends PrestaShopAPI {\n    private $cache = [];\n    private $cacheTime = 300; // 5 minutos\n    \n    public function get($resource, $id = null, $params = []) {\n        $cacheKey = md5($resource . serialize($id) . serialize($params));\n        \n        if (isset($this->cache[$cacheKey]) && \n            (time() - $this->cache[$cacheKey]['time']) < $this->cacheTime) {\n            return $this->cache[$cacheKey]['data'];\n        }\n        \n        $data = parent::get($resource, $id, $params);\n        $this->cache[$cacheKey] = [\n            'data' => $data,\n            'time' => time()\n        ];\n        \n        return $data;\n    }\n}\n```\n\n### 3. Logging\n```python\nimport logging\n\nlogging.basicConfig(\n    level=logging.INFO,\n    format='%(asctime)s - %(levelname)s - %(message)s',\n    filename='prestashop_api.log'\n)\n\nclass LoggedPrestaShopAPI(PrestaShopAPI):\n    def get(self, resource, resource_id=None, params=None):\n        logging.info(f\"GET {resource}/{resource_id or ''} - Params: {params}\")\n        try:\n            result = super().get(resource, resource_id, params)\n            logging.info(f\"GET {resource} - Success\")\n            return result\n        except Exception as e:\n            logging.error(f\"GET {resource} - Error: {e}\")\n            raise\n```\n\n---\n\n**Nota**: Todos los ejemplos asumen que las variables de entorno están configuradas correctamente y que el webservice está habilitado en PrestaShop.