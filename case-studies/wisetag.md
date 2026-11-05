# WiseTAG: tags QR y NFC con perfil de emergencia

Plataforma web que vincula un tag físico (QR o NFC) con un perfil digital de emergencia que se abre al escanearlo.

## Problema

Quien encuentra una mascota extraviada, o a una persona que no puede comunicarse, necesita contactar rápido a alguien. Una placa grabada ofrece poco espacio y no se puede actualizar.

## Solución de extremo a extremo

Cada tag lleva una URL corta con un código único. Al escanearla se abre un perfil público con datos clave, foto, audio y contactos de emergencia con acceso a WhatsApp. El dueño reclama el tag, edita el perfil desde un panel y consulta cuándo fue leído. Construí el backend del ciclo completo (códigos, lotes para imprimir, API) y buena parte del panel y de la página pública.

## Arquitectura

El tag lleva una URL corta que abre el perfil público. El panel del dueño y la página pública usan la misma API REST, que guarda los perfiles, los archivos y el registro de escaneos.

## Decisiones técnicas

- **Código corto:** 8 caracteres con generador criptográfico y alfabeto sin caracteres ambiguos (0/O, 1/I/L).
- **Activación por reclamo:** los tags se fabrican sin dueño y el usuario los reclama con su código. La relación con el dueño se libera si se borra la cuenta, así el código impreso sobrevive. Un tag sin reclamar no expone datos personales.
- **Lotes de QR:** un comando genera lotes y exporta un CSV con código y URL, más un PDF de verificación con los QR.
- **Registro de escaneos:** guarda fecha, navegador, sistema y ubicación aproximada; el GPS es opcional.
- JWT, permisos por propiedad del recurso y documentación OpenAPI generada.

## Mi rol

Autor del 100 % de los commits sin merge del backend (24 de 24) y del 73 % del frontend (35 de 48), verificado en git. El resto del frontend (13 commits) es de otro colaborador. Usé asistentes de IA durante el desarrollo.

## Qué aprendí y qué cambiaría

- Modelar los datos alrededor del objeto físico (el código impreso) simplifica reclamo y reasignación.
- Cambiaría la migración al arranque por un paso de despliegue explícito, añadiría paginación al historial de escaneos y automatizaría el empaquetado del frontend en vez de versionar la salida compilada.
- Retiraría las dependencias heredadas de la plantilla inicial y mantendría la documentación al día.

## Stack

Python, Django, Django REST Framework, PostgreSQL, JWT, Docker, React, TypeScript, Vite, Tailwind, Vitest, Playwright, GitHub Actions.
