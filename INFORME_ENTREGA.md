# Informe de Entrega - Práctica Besu Hyperledger

Este documento contiene los resultados técnicos necesarios para la entrega de la práctica de despliegue de la red permisionada.

## Ejercicio 1: Despliegue de la Red (3 Nodos)

### Identidades de los Nodos (Enodes)
*   **Nodo 1**: `enode://9bb377e745035c275da9f608beff4b9da6838273cae417da5523ced6f18effcc9dc533b020808ae2322fd48b2f723acdb865473dc145f8ab9cd4ada0f9818f3e@127.0.0.1:7001`
*   **Nodo 2**: `enode://444bfbf08231376c0a285cf4297e55774e4de3e094bf645cad86d496bef9e6aeb3f782bc20594dc96d925e0c8b0051e9b251a5198e3b04c865e94f83645b7468641a0ce9@127.0.0.1:7002`
*   **Nodo 3**: `enode://cb614ce733979ae9acfd69bd8e85e9346030f65f23438562dc593ae9cb789d6f2bf6053e44e8468a11f1fe1fb19c728e7b84169009f4b71e10f2b9c4cf2481ff@127.0.0.1:7003`

### Validadores Iniciales (IBFT 2.0)
1. `0x056b41F075Ce5A20961C75D1E54C8a32cfCc19c8` (Nodo 1)
2. `0x444bFbf08231376c0A285Cf4297e55774e4DE3e0` (Nodo 2)
3. `0xBF645CAD86D496BEF9e6aEB3f782bC20594dc96d` (Nodo 3)

### Resultado RPC: ibft_getSignerMetrics
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": [
    {
      "address": "0x056b41f075ce5a20961c75d1e54c8a32cfcc19c8",
      "proposedBlockCount": "0x22",
      "lastProposedBlockNumber": "0x9f"
    },
    {
      "address": "0xbf645cad86d496bef9e6aeb3f782bc20594dc96d",
      "proposedBlockCount": "0x21",
      "lastProposedBlockNumber": "0x9e"
    },
    {
      "address": "0x444bfbf08231376c0a285cf4297e55774e4de3e0",
      "proposedBlockCount": "0x21",
      "lastProposedBlockNumber": "0x9d"
    }
  ]
}
```

---

## Ejercicio 2: Añadir el Nodo 4

### Datos del Nodo 4
*   **Dirección**: `0x814C7a105918F34B453BAAfa3b93C45Ff489Ad09`
*   **P2P Port**: 7004
*   **RPC Port**: 8004

### Comando de Voto (IBFT Propose)
```bash
curl -X POST --data '{"jsonrpc":"2.0","method":"ibft_proposeValidatorVote","params":["0x814C7a105918F34B453BAAfa3b93C45Ff489Ad09", true],"id":1}' http://localhost:8001
```

---

## Ejercicio 3: Permisionado Dinámico

### Configuración del Permisionado (Local)
Archivo: `permissions_config.toml`
```toml
nodes-allowlist=["enode://9bb377...7001", "enode://444bfb...7002", "enode://cb614c...7004"]
```

### Petición a la API PERM (Añadir Nodo 3)
*   **Método**: `perm_addNodesToAllowlist`
*   **Cuerpo (JSON)**:
```json
{
  "jsonrpc": "2.0",
  "method": "perm_addNodesToAllowlist",
  "params": [
    [
      "enode://cb614ce733979ae9acfd69bd8e85e9346030f65f23438562dc593ae9cb789d6f2bf6053e44e8468a11f1fe1fb19c728e7b84169009f4b71e10f2b9c4cf2481ff@127.0.0.1:7003"
    ]
  ],
  "id": 1
}
```
