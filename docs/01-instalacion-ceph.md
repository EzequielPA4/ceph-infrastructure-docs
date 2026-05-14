# Instalación Ceph iSCSI — Ubuntu 22.04 LTS

Todas las direcciones IP utilizadas en este instructivo (por ejemplo, 172.31.13.14) son referenciales y deben ser reemplazadas por la IP correspondiente al entorno.  
Este procedimiento está diseñado para una implementación single node (un solo servidor).

## FASE 0 — Instalación del SO

- Descargar Ubuntu 22.04 (Jammy) Server: https://releases.ubuntu.com/22.04/  (No se recomienda el uso de Ubuntu 24.04 (Noble) para este procedimiento.)
- Instalación mínima
- IP estática: 172.31.13.14
- Hostname: ceph

## FASE 1 — Prerequisitos

```bash
apt update && apt upgrade -y
sudo apt install -y curl lsb-release ca-certificates gnupg2

sudo timedatectl set-timezone America/Argentina/Buenos_Aires
sudo timedatectl set-ntp true

timedatectl
date

apt install -y curl wget podman
```

## FASE 2 — Instalar cephadm

```bash
curl --silent --remote-name --location https://download.ceph.com/rpm-squid/el9/noarch/cephadm
chmod +x cephadm
mv cephadm /usr/local/bin/

cephadm install ceph-common
```

## FASE 3 — Bootstrap del cluster

```bash
cephadm bootstrap \
  --mon-ip 172.31.13.14 \
  --single-host-defaults \
  --allow-fqdn-hostname
```

Verificación:

```bash
ceph -s
ceph orch ls
```

## FASE 4 — Agregar OSDs

```bash
ceph orch apply osd --all-available-devices
```

Verificación:

```bash
watch ceph osd tree
```

## FASE 5 — Pool RBD

```bash
ceph osd pool create rbd 32
rbd pool init rbd
```

## FASE 6 — Instalar paquetes iSCSI

```bash
apt install -y ceph-iscsi targetcli-fb tcmu-runner
```

## FASE 7 — Configurar gateway

```bash
cat > /etc/ceph/iscsi-gateway.cfg << 'EOF'
[config]
cluster_name = ceph
gateway_keyring = /etc/ceph/ceph.client.admin.keyring
api_secure = False
api_user = admin
api_password = admin
api_port = 5000
trusted_ip_list = 172.31.13.14
pool = rbd
minimum_gateways = 1
EOF
```

## FASE 8 — Levantar servicios iSCSI

```bash
systemctl enable --now tcmu-runner
systemctl enable --now rbd-target-gw
systemctl enable --now rbd-target-api

systemctl status tcmu-runner rbd-target-gw rbd-target-api --no-pager
```

## FASE 9 — Registrar gateway en el dashboard

```bash
echo "http://admin:admin@172.31.13.14:5000" > /tmp/gateway.url

ceph dashboard iscsi-gateway-add -i /tmp/gateway.url
ceph dashboard set-iscsi-api-ssl-verification false
```

Verificación:

```bash
curl -u admin:admin http://172.31.13.14:5000/api/_ping
```
