# Documentación API PrestaShop

Esta carpeta contiene documentación completa sobre las APIs de PrestaShop para consultas posteriores y desarrollo de integraciones.

## Estructura de Documentación

### 📁 Official Webservice
Documentación de la API Webservice oficial de PrestaShop
- [`authentication.md`](./official-webservice/authentication.md) - Métodos de autenticación
- [`endpoints.md`](./official-webservice/endpoints.md) - Lista completa de endpoints
- [`examples.md`](./official-webservice/examples.md) - Ejemplos de uso prácticos
- [`configuration.md`](./official-webservice/configuration.md) - Configuración paso a paso

### 📁 Third Party Modules
Documentación de módulos de terceros populares
- [`binshops-rest-api.md`](./third-party-modules/binshops-rest-api.md) - Módulo REST API de Binshops
- [`other-modules.md`](./third-party-modules/other-modules.md) - Otros módulos disponibles

### 📁 Integration Framework
APIs modernas del framework de integración
- [`oauth2-jwt.md`](./integration-framework/oauth2-jwt.md) - Autenticación OAuth2/JWT
- [`cloudsync-apis.md`](./integration-framework/cloudsync-apis.md) - APIs CloudSync y Billing

## Resumen Rápido

### API Webservice Oficial
- **URL Base**: `https://tudominio.com/api/`
- **Autenticación**: Basic Authentication con ws_key
- **Formato**: XML (por defecto) o JSON
- **Recursos**: 50+ tipos (productos, pedidos, clientes, etc.)

### Principales Alternativas
- **Binshops REST**: Módulo más completo con mejor documentación
- **Integration Framework**: APIs modernas con OAuth2/JWT
- **Módulos personalizados**: Para necesidades específicas

## Enlaces Útiles

- [Documentación Oficial PrestaShop](https://devdocs.prestashop-project.org/8/webservice/)
- [GitHub - Binshops REST](https://github.com/binshops/prestashop-rest)
- [PostMan Collection](https://documenter.getpostman.com/view/1491681/TzkyP1UC)
- [Foros PrestaShop](https://www.prestashop.com/forums/)

## Notas de Desarrollo

Esta documentación está organizada para facilitar:
- Consultas rápidas durante desarrollo
- Comparación entre diferentes métodos de API
- Ejemplos de implementación listos para usar
- Troubleshooting común

---

**Última actualización**: $(date +%Y-%m-%d)
**Versión PrestaShop**: 8.x compatible