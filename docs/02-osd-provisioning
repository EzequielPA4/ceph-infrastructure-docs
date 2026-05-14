# Limpieza de disco y creación de OSD en Ceph (cephadm)

## Objetivo

Dejar un disco completamente limpio (sin filesystem, LVM ni particiones) y crear un OSD dedicado utilizando Ceph Orchestrator (cephadm).

---

## Alcance

- Aplica a entornos Ceph administrados con cephadm  
- Discos previamente utilizados (no vírgenes)  
- No aplica al disco donde está instalado el sistema operativo  

---

## ⚠️ Advertencia

Este procedimiento elimina todos los datos del disco seleccionado.  
Verificar cuidadosamente el dispositivo antes de ejecutar los comandos.

---

## Convenciones

| Elemento     | Significado                         |
|--------------|-----------------------------------|
| /dev/sdX     | Disco de datos a limpiar           |
| \<HOSTNAME>  | Nombre del host Ceph              |
| \<VG>        | Volume Group detectado en el disco|

---

## Paso 1 — Identificar discos

```bash
lsblk -o NAME,SIZE,FSTYPE,MOUNTPOINT
```

Criterios:
- El disco del SO tiene `/` como MOUNTPOINT  
- `/dev/sdX` **NO debe tener MOUNTPOINT**  

Si el disco está montado, **no continuar**.

---

## Paso 2 — Inspeccionar estado

```bash
lsblk -f /dev/sdX
wipefs /dev/sdX
```

Permite identificar:
- Filesystem existente  
- Firmas previas  
- Uso anterior  

---

## Paso 3 — Verificar LVM

```bash
pvs | grep -w sdX || true
vgs
lvs
```

- Si aparece como PV → ir a Paso 4  
- Si no → continuar en Paso 5  

---

## Paso 4 — Liberar LVM (si aplica)

### 4.1 Desactivar VG

```bash
vgchange -an <VG>
```

### 4.2 Eliminar PV

```bash
pvremove -ff /dev/sdX
```

---

## Paso 5 — Eliminar filesystem

```bash
wipefs -a /dev/sdX
```

Si aparece `Device or resource busy`, verificar:
- Montajes activos  
- Uso por LVM  
- Locks del sistema  

---

## Paso 6 — Eliminar particiones

```bash
sgdisk --zap-all /dev/sdX
```

---

## Paso 7 — Limpieza profunda

```bash
dd if=/dev/zero of=/dev/sdX bs=1M count=50 status=progress
```

Elimina:
- Superblocks residuales  
- Firmas persistentes  

---

## Paso 8 — Refrescar kernel

```bash
partprobe /dev/sdX
udevadm settle
```

Opcional: reiniciar el host

---

## Paso 9 — Validación final

```bash
wipefs /dev/sdX
lsblk -f /dev/sdX
```

Resultado esperado:
- Sin filesystem  
- Sin particiones  
- Sin firmas  

---

## Paso 10 — Validar en Ceph

```bash
ceph orch device ls | grep -w sdX
```

Estado esperado:
- Available: Yes  
- Sin errores  

---

## Paso 11 — Crear OSD

```bash
cephadm shell -- ceph orch daemon add osd <HOSTNAME>:/dev/sdX
```

Notas:
- Usa solo el disco indicado  
- No afecta otros dispositivos  
- Evita errores de spec  

---

## Paso 12 — Verificación

```bash
ceph -s
ceph osd tree
ceph osd df
```

---

## Resultado esperado

- Disco asignado a `osd.X`  
- Capacidad adicional disponible  
- Cluster en estado saludable  

---

## Observaciones

- Ceph rechaza discos con FS/LVM activos  
- Errores comunes evitados:
  - CEPHADM_APPLY_SPEC_FAIL  
  - Can't open device exclusively  

Siempre validar antes:

```bash
ceph orch device ls
```
