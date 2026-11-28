# bda-ut1-pentaho-hotel

Práctica integradora UT1 - Big Data Aplicado (IES Ataúlfo Argenta).

## Autor y código
Daniel Mourelo — Código de paquete **A005** (`cierre_aula_01`).

## Objetivo
Generar un informe de cobros por hotel y canal (CSV) y un listado de reservas sin cobro, mediante Pentaho Data Integration (PDI) 9.4.0.0-343. El procedimiento debe poder repetirse y avisar si no puede generar el informe.

## Entorno
- PDI 9.4.0.0-343, Java 23 (jdk-23).
- Ejecución: Pan/Kitchen desde `data-integration/`.

## Estructura del repositorio
```
bda-ut1-pentaho-hotel/
├── README.md
├── .gitignore
├── asignacion.json
├── entrada/        # CSV originales (reservas, cobros, hoteles)
├── procesos/       # .ktr y .kjb
├── temporal/       # Resultados provisionales (gitignore)
├── salida/         # Resultados publicados
└── evidencias/     # (vacío; evidencias en este README)
```

## Ejecución
```
cd data-integration
pan.bat /file=".../procesos/informe_cobros.ktr" /level:Basic
pan.bat /file=".../procesos/reservas_sin_cobro.ktr" /level:Basic
kitchen.bat /file=".../procesos/cierre_hotel.kjb" /level:Basic
```

## Datos (paquete A005)
- `reservas_pdi.csv` (coma, 20 filas): `id_reserva, id_hotel, canal, noches, importe`
- `cobros_pdi.csv` (punto y coma, 15 filas): `id_reserva, medio, cobrado`
- `hoteles_pdi.csv` (punto y coma, 5 filas): `id_hotel, hotel, comarca, id_cadena`

## Resultados esperados
- Informe: **13 grupos, 15 reservas, 3965.50 €**.
- Sin cobro: **5 reservas** (ids 4, 9, 11, 15, 18).

## Diseño
- Recorrido: Reservas + cobros + hoteles → lectura y comprobación de tipos/claves → informe por hotel/canal + listado sin cobro (provisionales) → validación de los dos archivos → publicación en salida y registro del resultado.
- Uniones: INNER reservas-cobros por `id_reserva`; LEFT OUTER para detectar sin cobro (renombrando `id_reserva` → `id_reserva_cobro`).
- Se suma `cobrado` (no `importe`). CSV por portabilidad (sep `;`, UTF-8, 2 decimales).
- `cierre_aula_01`: fecha/hora de generación en README; importes en euros; no se calcula ocupación (faltan habitaciones).

## Comprobación manual (esperado / obtenido / conclusión)

| Comprobación | Esperado | Obtenido | Conclusión |
|---|---|---|---|
| Reservas / cobros / hoteles leídos | 20 / 15 / 5 | 20 / 15 / 5 | OK |
| Claves de cada origen | Sin duplicados | Sin duplicados | OK |
| Filas tras unir reservas y cobros | 15 | 15 | OK |
| Filas tras añadir hoteles | 15 | 15 | OK |
| Filas del informe (sin cabecera) | 13 grupos | 13 | OK |
| Suma de n_reservas | 15 | 15 | OK |
| Suma de cobrado_total | 3965.50 € | 3965.50 € | OK |
| Laredo, canal web | 2 reservas; 496.50 € | 2; 496.50 € | OK |
| Reservas sin cobro | 5: ids 4, 9, 11, 15, 18 | 5: ids 4, 9, 11, 15, 18 | OK |
| Reconciliación | 15 + 5 = 20 | 15 + 5 = 20 | OK |

## Pruebas (apartado 8)

| Prueba | Resultado |
|---|---|
| Primera ejecución | Dos salidas correctas, estado éxito |
| Repetición | Mismos resultados, sin duplicados |
| Falta de entrada | Job con error, no se publica informe nuevo |
| Salida incompleta | Validación con error |
| Total incorrecto | Validación con error |
| Recuperación | Se generan, validan y publican salidas correctas |

## Sesiones de trabajo

- **Sesión 1**: recibido paquete, creado repositorio, diseñado recorrido, construido `informe_cobros.ktr`. Pan OK (13 grupos, 15 reservas, 3965.50 €).
- **Sesión 2**: corregido `reservas_sin_cobro.ktr` (Filter `id_reserva_cobro IS NULL`). Construidas `validar_informe.ktr` y `validar_sin_cobro.ktr`. Tabla esperado/obtenido completada.
- **Sesión 3**: construido `cierre_hotel.kjb`. Ejecutadas las 6 pruebas. Kitchen OK.

## Fuentes y ayudas
- Talleres 1.8 de Pentaho (referencia técnica).
- Clases de BDA.
- Guía de la práctica (RA1, criterios de evaluación).

## Para gerencia
El cierre `cierre_aula_01` resume 15 reservas con cobro por un total de 3965.50 €, agrupadas en 13 combinaciones de hotel y canal. Quedan 5 registros sin cobro asociado (ids 4, 9, 11, 15, 18) que requieren revisión; no demuestran por sí solos un impago (podría haber pago pendiente o plazo). No se calcula ocupación: faltarían habitaciones disponibles y ocupadas por periodo. Para responder a esa pregunta pediríamos datos de disponibilidad y ocupación por periodo.
```