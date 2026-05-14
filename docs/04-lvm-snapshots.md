# Snapshots LVM en Ubuntu Server (servidor físico)

## Concepto clave

Un snapshot LVM NO es una copia completa del disco.  
Es una copia por diferencia (**Copy-On-Write**).

### ¿Qué significa esto?

- En el momento del snapshot:
  - El volumen original queda “congelado lógicamente”
- A partir de ese momento:
  - Cada bloque que se modifica en el volumen original
  - Se guarda primero en el snapshot
- El snapshot solo almacena los cambios, no todo el sistema

### Implicancias

- El snapshot puede ser mucho más chico  
- Si se llena, se invalida  

---

## Escenario actual

- Volume Group: `ubuntu-vg`  
- Logical Volume raíz: `ubuntu-lv` montado en `/`  
- Amplio espacio libre en el VG  
- Servidor limpio  

👉 Escenario ideal para snapshots LVM

---

## Crear snapshots

### Regla general

Cada snapshot:

- Depende del LV original  
- Es independiente de otros snapshots  
- Consume espacio solo si hay cambios  

---

### Crear un snapshot

```bash
lvcreate -L 20G -s -n snap_pre_app1 /dev/ubuntu-vg/ubuntu-lv
```

- `-L 20G` → espacio máximo para cambios  
- `-s` → indica snapshot  
- `-n snap_pre_app1` → nombre del snapshot  
- `/dev/ubuntu-vg/ubuntu-lv` → volumen origen  

Resultado:

- El sistema sigue funcionando  
- No hay corte  
- No requiere reboot  

---

### Crear otro snapshot

```bash
lvcreate -L 20G -s -n snap_pre_app2 /dev/ubuntu-vg/ubuntu-lv
```

✔ Se pueden tener múltiples snapshots del mismo LV  
✔ Cada uno representa un punto distinto en el tiempo  

---

## Ver snapshots existentes

```bash
lvs
```

Ejemplo:

```
LV             VG         Attr       LSize
ubuntu-lv      ubuntu-vg  -wi-ao---- 100,00g
snap_pre_app1  ubuntu-vg  swi-a-s--- 20,00g
snap_pre_app2  ubuntu-vg  swi-a-s--- 20,00g
```

---

## Monitorear uso del snapshot

```bash
lvs -o +data_percent
```

Ejemplo:

```
LV             Data%
snap_pre_app1  12.34
```

⚠️ Si `Data%` llega a 100%:
- El snapshot se invalida  
- No sirve para rollback  

---

## Restaurar (rollback)

### Concepto clave

LVM NO restaura en caliente el filesystem raíz.  
El rollback se aplica en el próximo arranque.

---

### Paso 1 — Marcar snapshot para restore

```bash
lvconvert --merge /dev/ubuntu-vg/snap_pre_app1
```

Esto indica:
- Que el snapshot será aplicado
- En el próximo boot

---

### Paso 2 — Reiniciar

```bash
reboot
```

Durante el arranque:

- LVM realiza el merge  
- El snapshot desaparece  
- El sistema vuelve a ese estado  

✔ Paquetes  
✔ Configuración  
✔ Binarios  
✔ Estado completo  

---

## Impacto en otros snapshots

⚠️ Importante:

- Si restaurás `snap_pre_app1`:
  - Los snapshots posteriores quedan inválidos  
  - La línea de tiempo cambia  

### Buenas prácticas

- Usar un snapshot por cambio crítico  
- Eliminar snapshots luego de validar cambios  

---

## Eliminar snapshots

```bash
lvremove /dev/ubuntu-vg/snap_pre_app1
```

Beneficios:

- Libera espacio  
- Reduce overhead  
- Mejora el mantenimiento del sistema  
