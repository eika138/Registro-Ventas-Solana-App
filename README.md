# Registro de Ventas en Solana · Proyecto final Advanced

**Ruta:** proyecto guiado con mejora funcional.
**Versión base:** [Registro-Ventas-Solana-PDA](https://github.com/WayLearnLatam/Registro-Ventas-Solana-PDA) (programa) y [Registro-Ventas-Solana-App](https://github.com/WayLearnLatam/Registro-Ventas-Solana-App) (frontend).
**Program ID (Devnet):** `9tphV3N1Xb3uLFiuG4FG9pNVAfgpTtm96oN1BgYFiUyC`
[Ver en Explorer](https://explorer.solana.com/address/9tphV3N1Xb3uLFiuG4FG9pNVAfgpTtm96oN1BgYFiUyC?cluster=devnet)

## Mi aporte

1. **Nuevo campo `total_gastado` (u32) en `Cliente`**: suma lo que ha gastado cada cliente.
   - `registrar_venta`: suma el monto (`checked_add`).
   - `cambiar_monto`: resta el monto viejo y suma el nuevo.
   - `hacer_devolucion`: resta el monto de la venta devuelta (`checked_sub`).
2. **Validación de negocio en Rust:** `registrar_venta` y `cambiar_monto` rechazan montos en 0 con el error `MontoInvalido` (6002).
3. **Error `TotalInvalido` (6003)** si el total se desborda o quedaría negativo (aritmética segura).
4. `cliente` pasó a `mut` en el contexto `ModificarVenta`, porque ahora se modifica.
5. **Frontend:** muestra el total gastado de cada cliente. Archivos: `src/solana/lecturas.ts`, `src/components/Registro.tsx`, y `scripts/contrato-idl.mjs` (validador del IDL, con el campo nuevo).

Código del programa: `programa/src/lib.rs`. IDL y cliente regenerados con mi despliegue (`idl.json`, `clients/js`).

## Cómo verificar

**En el frontend** (Codespaces o local con `npm install` y `npm run dev`, wallet en Devnet):
1. Crear un negocio, un cliente y una venta con importe mayor a 0: aparece `Total gastado`.
2. Corregir el importe: el total se actualiza.
3. Hacer la devolución: el total baja.
4. El frontend ya bloquea el 0 antes de firmar. Por eso la regla de Rust se prueba directo en el Playground.

**Caso de rechazo (Playground):** `registrarVenta(nombre_negocio, 1, "Papas", "prueba", 0)` falla con `MontoInvalido` (6002) y la venta no se crea.

## Evidencia

- Venta con total: <img width="1079" height="588" alt="Screenshot 2026-11-22 11 52 45 PM" src="https://github.com/user-attachments/assets/2088d7bd-60a4-4bdf-8a14-484d714f523a" />  [transacción](https://explorer.solana.com/tx/[HASH]?cluster=devnet)
- Corregir importe: <img width="1063" height="609" alt="Screenshot 2026-11-23 12 06 37 AM" src="https://github.com/user-attachments/assets/715a0aec-9a8e-4db1-ba02-91dbe5f5cd6d" />

- Devolución: <img width="989" height="555" alt="Screenshot 2026-11-23 12 38 51 AM" src="https://github.com/user-attachments/assets/9220327c-7575-4a5e-b3c0-b8520e4d07d3" />

- Rechazo <img width="783" height="605" alt="Screenshot 2026-11-23 12 31 14 AM" src="https://github.com/user-attachments/assets/4222866a-a063-4887-bc70-d7e51659df94" />
<img width="783" height="605" alt="Screenshot 2026-11-23 12 34 08 AM" src="https://github.com/user-attachments/assets/14a297dc-e608-4ed0-9eb9-68fabfdfa46a" />
<img width="783" height="605" alt="Screenshot 2026-11-23 12 35 29 AM" src="https://github.com/user-attachments/assets/9c18e556-3591-48f8-b6a3-2f3d856241a1" />
 (antes, error, después)

## Notas

- Al cambiar el struct `Cliente`, las cuentas creadas con la versión anterior no son compatibles; se usa un negocio nuevo.
- Usé Claude como asistente para entender y escribir los cambios, y revisé y probé cada uno.
