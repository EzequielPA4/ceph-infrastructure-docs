# ceph-infrastructure-docs

Runbooks and practical guides for deploying and managing Ceph on Ubuntu.

---

## 📌 Overview

Guía completa para implementación y operación de Ceph en entornos **single-node**, incluyendo:

- Instalación base del cluster
- Configuración de networking (bonding)
- Preparación de discos y creación de OSD
- Implementación de iSCSI
- Snapshots LVM
- Troubleshooting operativo

**Versión validada:** Ceph Squid 19.2.3  
**Sistema operativo:** Ubuntu Server 22.04 LTS  

---

## 📚 Documentación

### 🚀 Instalación y despliegue

- [Instalación Ceph](docs/01-instalacion-ceph.md)

---

### 💽 Storage

- [Limpieza de discos y creación de OSD](docs/02-disk-cleanup-osd-runbook.md)
- [Snapshots LVM](docs/04-lvm-snapshots.md)

---

### 🌐 Networking

- [Bonding 802.3ad (LACP)](docs/03-network-bonding.md)

---

## 🧠 Notas importantes

- Este repositorio está orientado a entornos de laboratorio o validación.
- No contempla alta disponibilidad (multi-node).
- Se recomienda validar configuraciones antes de aplicar en producción.

---

## ⚠️ Compatibilidad

- ✔ Ubuntu 22.04 (Jammy) → validado  
- ❌ Ubuntu 24.04 (Noble) → no recomendado  

---

## 📁 Estructura del repositorio

```
ceph-infrastructure-docs/
├── README.md
└── docs/
    ├── 01-instalacion-ceph.md
    ├── 02-disk-cleanup-osd-runbook.md
    ├── 03-network-bonding.md
    └── 04-lvm-snapshots.md
```

---

## 🛠️ Uso

1. Validar cada fase antes de continuar
2. Aplicar cambios de forma controlada

---

## 📌 Autor

Documentación técnica orientada a operaciones de infraestructura, almacenamiento y virtualización.
