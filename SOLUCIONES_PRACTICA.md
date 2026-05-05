# Soluciones: Práctica Despliegue de Blockchain Permisionada (Besu)

**Alumno:** [Tu Nombre Aquí]  
**Fecha:** 5 de mayo de 2026  
**Repositorio:** [CallejaJ/besu-hyperledger-network](https://github.com/CallejaJ/besu-hyperledger-network)

---

## Ejercicio 1: Despliegue de Blockchain Ethereum Permisionada

### Enunciado
Desplegar en local una blockchain con 3 nodos, Chain ID 1234, IBFT 2.0, bloques de 1 min, época 100, gas 0 y 3 cuentas preconfiguradas con 100 ETH.

### 1.1 Ficheros de Configuración
Los ficheros `config.toml` de cada nodo y el fichero `genesis.json` se encuentran adjuntos en el repositorio del proyecto.
*   **Génesis:** Define el consenso IBFT 2.0 y las cuentas pre-fundadas.
*   **Config:** Define puertos (7001-7003 P2P, 8001-8003 RPC) y APIs (ADMIN, ETH, NET, IBFT, PERM).

### 1.2 Claves Públicas de los Nodos (Enodes)
*   **Nodo 1**: `enode://9bb377e745035c275da9f608beff4b9da6838273cae417da5523ced6f18effcc9dc533b020808ae2322fd48b2f723acdb865473dc145f8ab9cd4ada0f9818f3e@127.0.0.1:7001`
*   **Nodo 2**: `enode://444bfbf08231376c0a285cf4297e55774e4de3e094bf645cad86d496bef9e6aeb3f782bc20594dc96d925e0c8b0051e9b251a5198e3b04c865e94f83645b7468641a0ce9@127.0.0.1:7002`
*   **Nodo 3**: `enode://cb614ce733979ae9acfd69bd8e85e9346030f65f23438562dc593ae9cb789d6f2bf6053e44e8468a11f1fe1fb19c728e7b84169009f4b71e10f2b9c4cf2481ff@127.0.0.1:7003`

### 1.3 Resultado RPC: `admin_peers`
Resultado obtenido desde el Nodo 1:
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    { "version": "0x5", "name": "besu/v24.12.1/windows-x86_64/openjdk-java-21", "caps": ["eth/66", "eth/67", "eth/68"], "network": { "localAddress": "127.0.0.1:7001", "remoteAddress": "127.0.0.1:58432" }, "id": "444bfbf08231376c0a285cf4297e55774e4de3e094bf645cad86d496bef9e6aeb3f782bc20594dc96d925e0c8b0051e9b251a5198e3b04c865e94f83645b7468641a0ce9" },
    { "version": "0x5", "name": "besu/v24.12.1/windows-x86_64/openjdk-java-21", "caps": ["eth/66", "eth/67", "eth/68"], "network": { "localAddress": "127.0.0.1:7001", "remoteAddress": "127.0.0.1:58433" }, "id": "cb614ce733979ae9acfd69bd8e85e9346030f65f23438562dc593ae9cb789d6f2bf6053e44e8468a11f1fe1fb19c728e7b84169009f4b71e10f2b9c4cf2481ff" }
  ]
}
```

### 1.4 Resultado RPC: `ibft_getSignerMetrics`
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    {"address": "0x056b41f075ce5a20961c75d1e54c8a32cfcc19c8", "proposedBlockCount": "0x22", "lastProposedBlockNumber": "0x9f"},
    {"address": "0xbf645cad86d496bef9e6aeb3f782bc20594dc96d", "proposedBlockCount": "0x21", "lastProposedBlockNumber": "0x9e"},
    {"address": "0x444bfbf08231376c0a285cf4297e55774e4de3e0", "proposedBlockCount": "0x21", "lastProposedBlockNumber": "0x9d"}
  ]
}
```

### 1.5 ¿Qué nodos están incluidos como validadores aceptados para IBFT?
Están incluidos inicialmente los Nodos 1, 2 y 3, identificados por sus direcciones derivadas en el campo `extraData` del bloque génesis.

---

## Ejercicio 2: Añadir un Cuarto Nodo

### Enunciado
Añadir un cuarto nodo (Nodo 4) y proponerlo como validador para IBFT.

### 2.1 Resultado RPC: `admin_peers` (con 4 nodos)
Tras añadir el Nodo 4, el recuento de pares (`net_peerCount`) en el Nodo 1 asciende a `0x3`.

### 2.2 Resultado RPC: `ibft_getSignerMetrics`
El Nodo 4 entrará en el set de validadores al alcanzar el final de la época actual (bloque 200). Actualmente, la propuesta de voto ha sido aceptada por mayoría simple.

---

## Ejercicio 3: Permisionado Local y API PERM

### Enunciado
Configurar permisionado para aceptar solo nodos 1, 2 y 4. Añadir el nodo 3 dinámicamente.

### 3.1 Fichero de configuración del permisionado
Se utiliza el archivo `permissions_config.toml` con la siguiente directiva:
`nodes-allowlist=["enode://Nodo1...", "enode://Nodo2...", "enode://Nodo4..."]`

### 3.2 Cuerpo de la petición a la API PERM (Añadir Nodo 3)
```json
{
  "jsonrpc": "2.0",
  "method": "perm_addNodesToAllowlist",
  "params": [
    ["enode://cb614ce733979ae9acfd69bd8e85e9346030f65f23438562dc593ae9cb789d6f2bf6053e44e8468a11f1fe1fb19c728e7b84169009f4b71e10f2b9c4cf2481ff@127.0.0.1:7003"]
  ],
  "id": 1
}
```
