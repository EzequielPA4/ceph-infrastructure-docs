# Configuración de Bonding 802.3ad (LACP) — Ubuntu Server

## ¿Qué es bonding 802.3ad?

802.3ad (LACP) permite:

- Redundancia: si cae una NIC, el tráfico continúa  
- Agregación de enlaces: múltiples interfaces físicas funcionan como una sola lógica  
- Balanceo: distribución de tráfico según hash (IP/MAC/puertos)  

⚠️ Importante: el switch DEBE soportar LACP y estar configurado acorde.

## Requisitos previos

### En el servidor Linux

- Kernel con soporte bonding  
- Interfaces físicas disponibles (ej: ens6f0, ens6f1)  
- Acceso root  

Ver interfaces:

```bash
ip link
```

### En el switch (obligatorio)

- Crear un Port-Channel / LAG  
- Configurar modo LACP active  
- Mismas VLANs en todos los puertos  
- Misma velocidad y duplex  

## Parámetros recomendados para 802.3ad

| Parámetro             | Valor recomendado |
|----------------------|------------------|
| mode                 | 802.3ad          |
| lacp-rate            | fast             |
| transmit-hash-policy | layer3+4         |
| miimon               | 100 ms           |

## Configuración en Ubuntu Server (netplan)

### Escenario de ejemplo

- NIC 1: ens6f0  
- NIC 2: ens6f1  
- Bond: bond0  
- IP estática: 172.31.13.14/24  
- Gateway: 172.31.13.1  
- DNS: 8.8.8.8, 1.1.1.1  

### Editar archivo

```bash
sudo nano /etc/netplan/00-installer-config.yaml
```

### Configuración completa

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens6f0:
      dhcp4: no
    ens6f1:
      dhcp4: no
  bonds:
    bond0:
      interfaces:
        - ens6f0
        - ens6f1
      addresses:
        - 172.31.13.14/24
      gateway4: 172.31.13.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 1.1.1.1
      parameters:
        mode: 802.3ad
        lacp-rate: fast
        transmit-hash-policy: layer3+4
        mii-monitor-interval: 100
```

## Aplicar cambios

```bash
sudo netplan apply
```

⚠️ Si estás conectado por SSH, aplicar con precaución (preferible consola/ILO).

## Verificación

### Estado del bond

```bash
cat /proc/net/bonding/bond0
```

Deberías ver:
- Bonding Mode: IEEE 802.3ad  
- Ambos slaves en estado up  
- Aggregator ID igual  

### Interfaces

```bash
ip addr show bond0
ip link show
```
