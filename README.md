# ceph-infrastructure-docs

Guías prácticas para desplegar y operar Ceph sobre Ubuntu.

Documentación armada en base a laboratorio real, enfocada en ir directo al punto: instalar, configurar y que funcione.

---

## Stack

- Ceph Squid 19.2.3  
- Ubuntu Server 22.04 LTS  
- Deploy: single node  

---

## Qué incluye

- Instalación completa de Ceph (cephadm)
- Configuración de bonding (LACP 802.3ad)
- Preparación de discos + creación de OSD
- iSCSI gateway
- Snapshots con LVM
- Notas y troubleshooting básico

---

## Docs

- [01 - Instalación Ceph](docs/01-instalacion-ceph.md)
- [02 - Limpieza de disco + OSD](docs/02-disk-cleanup-osd-runbook.md)
- [03 - Bonding de red](docs/03-network-bonding.md)
- [04 - Snapshots LVM](docs/04-lvm-snapshots.md)

---

## Cómo usar esto
 
No saltear pasos.  
Validar cada fase antes de seguir.

---

## Notas

- Esto está pensado para lab o testing
- No es HA (un solo nodo)
- Si algo falla, revisar:
  - `ceph -s`
  - `ceph orch ps`
  - `ceph orch device ls`

---

## Compatibilidad

✔ Ubuntu 22.04 (Jammy)  
✖ Ubuntu 24.04 (Noble) → evitar  

---

## Estructura

```
ceph-infrastructure-docs/
└── docs/
    ├── 01-instalacion-ceph.md
    ├── 02-disk-cleanup-osd-runbook.md
    ├── 03-network-bonding.md
    └── 04-lvm-snapshots.md
```
