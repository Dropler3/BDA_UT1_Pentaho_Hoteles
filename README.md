# bda-ut1-pentaho-hotel

Práctica integradora UT1 - Big Data Aplicado (IES Ataúlfo Argenta).

## Autor
Daniel Mourelo - Código de paquete A005 (cierre_aula_01).

## Objetivo
Generar un informe de cobros por hotel y canal (CSV) y un listado de reservas sin cobro, mediante Pentaho Data Integration (PDI) 9.4.0.0-343.

## Entorno
- PDI 9.4.0.0-343, Java 23 (jdk-23).
- Ejecución: Pan/Kitchen desde `data-integration/`.

## Estructura
```
bda-ut1-pentaho-hotel/
├── README.md
├── .gitignore
├── asignacion.json
├── entrada/        # CSV originales (reservas, cobros, hoteles)
├── procesos/       # .ktr y .kjb
├── temporal/       # Resultados provisionales (gitignore)
├── salida/         # Resultados publicados
└── evidencias/     # Logs y comprobaciones
```

## Ejecución
```
cd data-integration
pan.bat /file=".../procesos/informe_cobros.ktr" /level:Basic
kitchen.bat /file=".../procesos/cierre_hotel.kjb" /level:Basic
```

## Datos
- reservas_pdi.csv (coma, 20 filas): id_reserva, id_hotel, canal, noches, importe
- cobros_pdi.csv (punto y coma, 15 filas): id_reserva, medio, cobrado
- hoteles_pdi.csv (punto y coma, 5 filas): id_hotel, hotel, comarca, id_cadena

## Resultados esperados
- Informe: 13 grupos, 15 reservas, 3965.50 €.
- Sin cobro: 5 reservas (ids 4, 9, 11, 15, 18).

## Procedencia y decisiones
- Unión INNER reservas-cobros por id_reserva; LEFT OUTER para detectar sin cobro.
- Se suma `cobrado` (no `importe`). CSV por portabilidad.
- cierre_aula_01: fecha/hora de generación en el README; importes en euros.

## Ayudas
- Talleres 1.8 de Pentaho (referencia técnica).
- Clases de BDA.
```