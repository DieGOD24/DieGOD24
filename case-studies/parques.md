# Parqués distribuido: juego multijugador en red

Parqués para hasta cuatro jugadores con servidor autoritativo y un cliente web también empaquetado para Android.

*Trabajo académico: proyecto final de Sistemas Distribuidos.*

## Problema

El enunciado pedía un parqués jugable en red desde la máquina de cada participante, como si fuera centralizado, aplicando concurrencia, sincronización y comunicación en tiempo real.

## Solución de extremo a extremo

Un servidor en Python gestiona las partidas y un cliente Next.js ofrece registro, lobby, tablero, dados, chat y estadísticas. El mismo cliente se empaqueta como APK con Capacitor. Incluye bots de tres niveles, recomendación de jugadas con explicación y documentación de arquitectura.

## Arquitectura

Los clientes web y Android se conectan por WebSocket y API REST a un motor de juego que corre en el servidor, el único que valida reglas y turnos. El motor usa bots y guarda el estado en una base de datos.

## Decisiones técnicas

- **Servidor autoritativo:** reglas, turnos y dados viven en el servidor (FastAPI); los clientes envían acciones y el servidor las valida.
- **Sincronización de estado:** cada partida es una sala WebSocket y el servidor difunde los cambios. Los turnos se serializan con semáforos y bloqueos, y se sincronizan relojes con el algoritmo de Berkeley.
- **Cliente único:** Next.js con Zustand y tablero SVG; Capacitor lo lleva a Android sin reescribir la interfaz.
- **IA:** el bot difícil consulta gpt-4o-mini de OpenAI para elegir jugada; si falla, tarda o no hay clave, recurre a un minimax con poda alfa-beta. El nivel medio usa ese minimax y el fácil decide al azar.

## Mi rol

Autor del 100 % de los commits del repositorio, verificado en git. Era un trabajo de asignatura pensado para grupos, y el documento de créditos declara el uso de asistentes de IA.

## Qué aprendí y qué cambiaría

- Validar en un solo lugar mantiene el comportamiento predecible con varios clientes.
- Una IA externa dentro del bucle de juego exige tiempo límite y plan de respaldo.
- Reemplazaría el difusor de salas por un bus de mensajes para escalar a varias instancias, y añadiría números de secuencia para reconectar jugadores con una instantánea del estado.

## Stack

Python, FastAPI, WebSocket, SQLAlchemy, Alembic, PostgreSQL, Next.js, React, TypeScript, Zustand, Capacitor, Docker, pytest, Vitest, Playwright.
