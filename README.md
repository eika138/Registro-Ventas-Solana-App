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

- Venta con total: `evidencia/[archivo].png` · [transacción](https://explorer.solana.com/tx/[HASH]?cluster=devnet)
- Corregir importe: `evidencia/[archivo].png`
- Devolución: `evidencia/[archivo].png`
- Rechazo `MontoInvalido`: `evidencia/[archivo].png` (antes, error, después)

## Notas

- Al cambiar el struct `Cliente`, las cuentas creadas con la versión anterior no son compatibles; se usa un negocio nuevo.
- Usé Claude como asistente para entender y escribir los cambios, y revisé y probé cada uno.