# Bot conversacional de WhatsApp para solicitudes y reclamos

Asistente multi-agente que guía por WhatsApp solicitudes de repuestos y reclamos de garantía de una empresa, valida los datos contra sus sistemas y deriva a un asesor cuando corresponde.

## Problema

Las solicitudes de repuestos y los reclamos de garantía los atendía un asesor de forma manual: identificar al cliente, entender qué necesitaba, revisar la factura en el ERP, consultar inventario y registrar el caso en el CRM. Es un proceso repetitivo, con muchos datos que pedir y validar, y errores fáciles de cometer (datos faltantes, factura inválida, falta de evidencia del defecto).

## Solución de extremo a extremo

1. El cliente escribe por WhatsApp. En el primer mensaje de la sesión acepta la política de tratamiento de datos; si no la acepta, la sesión se cierra.
2. El bot lo identifica y entiende su necesidad. En una solicitud de repuesto pide los datos necesarios con validaciones deterministas, consulta existencias y ofrece una alternativa si hace falta.
3. En un reclamo de garantía pide la referencia del producto, valida la factura en el ERP, recoge la descripción del defecto y exige una foto, que se analiza con un modelo de visión.
4. Con los datos completos, un agente arma el caso y se registra en el CRM. Si el CRM falla, el caso queda en una cola de reintentos.
5. Las compras directas, las piezas fuera de catálogo, los reclamos sin factura o sin foto y los usuarios frustrados se derivan a un asesor. Mientras espera, el estado de la conversación intercepta los mensajes siguientes.

## Arquitectura

El cliente escribe por WhatsApp a través de un proveedor; una API recibe el mensaje y lo pasa a un orquestador que funciona como máquina de estados y coordina agentes especializados que usan modelos de lenguaje y de visión. El orquestador consulta los sistemas de la empresa, registra el caso con reintentos y deriva a un asesor cuando corresponde. Un panel de monitoreo en tiempo real muestra lo que ocurre.

## Decisiones de diseño de IA

- **Multi-agente.** Agentes especializados por tarea, coordinados por una máquina de estados explícita. Cada agente tiene una tarea acotada.
- **Puertas deterministas antes del modelo.** El menú, las validaciones de datos y las reglas de derivación funcionan por reglas y palabras clave, sin LLM. El modelo se usa para extraer e interpretar información, no para decidir el flujo.
- **Derivación a humano.** Por petición explícita, frustración repetida, compra, pieza fuera de catálogo o falta de requisitos. Es parte del diseño, no un fallo.
- **Imágenes y voz.** Las fotos se analizan con visión como evidencia o referencia; las notas de voz se transcriben. Si la visión falla, el flujo continúa y se avisa al asesor.
- **Reclamos con evidencia.** La foto es obligatoria, la factura se valida contra el ERP y, si el CRM no responde, el caso se conserva con reintentos y registro de fallos.
- **Lo verificado.** El comportamiento descrito está leído en el código y la documentación de los repositorios. No hay medición de precisión del bot.

## Ingeniería

- **Monitor en tiempo real.** Un panel web (Next.js y React) muestra ejecuciones con línea de tiempo de pasos, sesiones con chat, casos creados y estadísticas, alimentado por WebSocket.
- **Pruebas.** Suite de pruebas en el backend y pruebas con vitest en el frontend.
- **CI/CD y despliegue.** GitHub Actions con flujos de integración y despliegue, imágenes Docker por servicio y orquestación con Docker Compose.
- **Datos.** SQLAlchemy asíncrono con migraciones y bases separadas para la operación y el monitoreo.

## Mi rol

Soy autor del backend y del monitor originales; lo verifiqué con el historial de git (131 commits sin merge entre ambos repositorios, todos míos). Hice la migración inicial desde un flujo de automatización a FastAPI multi-agente, las sesiones, los flujos, el monitor y una auditoría de casos. El desarrollo fue asistido por IA. Versiones posteriores del proyecto fueron mantenidas por otra persona y no las presento como mías.

## Lo que aprendí y qué cambiaría

- Separar lo determinista de lo probabilístico simplifica las pruebas y hace predecible el comportamiento.
- Los orquestadores crecen rápido; dividiría el orquestador en módulos más pequeños y reduciría el código duplicado entre repositorios.
- Añadiría una evaluación sistemática de conversaciones (casos de referencia y métricas por flujo) para medir calidad en lugar de depender de revisiones puntuales.
- Como siguiente paso, incorporaría avisos proactivos con plantillas aprobadas, que hoy el bot no tiene: solo responde.

Estado: proyecto desarrollado; no afirmo clientes, volumen ni resultados medidos.

## Stack

Python, FastAPI, SQLAlchemy, Alembic, SQLite/PostgreSQL, WebSocket, OpenAI (texto, visión, transcripción), Next.js, React, TypeScript, vitest, Docker, GitHub Actions.
