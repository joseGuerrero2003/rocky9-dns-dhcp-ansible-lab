# Rocky Linux 9 DHCP + DNS (BIND) Ansible Lab

Este repositorio automatiza la instalación y configuración de **DHCP (dhcpd)** y **DNS (BIND/named)** en **Rocky Linux 9** usando **Ansible**. El laboratorio se implementa en tres máquinas virtuales con **Vagrant**:

- **ansible**: nodo de control donde se ejecutan los playbooks.
- **server**: servidor que corre **dhcpd** y **named**.
- **client**: cliente que obtiene IP por DHCP y resuelve DNS usando el servidor.

## Arquitectura del laboratorio

El entorno está compuesto por tres VMs conectadas a una red interna:

- **ansible**: ejecuta los playbooks (`site.yml`). No ofrece servicios.
- **server**: ejecuta servicio DNS (BIND/named) y DHCP (dhcpd). Se configura automáticamente.
- **client**: obtiene configuración de red vía DHCP y usa el DNS del servidor para resolución.

## Requisitos previos

Asegúrate de tener instalados:

- **Vagrant** (>= 2.2)
- **VirtualBox** (o proveedor compatible con Vagrant)
- **Git**

## Levantar el laboratorio

```sh
git clone https://github.com/joseGuerrero2003/rocky9-dns-dhcp-ansible-lab.git
cd rocky9-dns-dhcp-ansible-lab
vagrant up
```

## Conexión a las máquinas

```sh
vagrant ssh ansible
vagrant ssh server
vagrant ssh client
```

## Ejecución del playbook de Ansible

Desde la VM **ansible** (o desde tu host si tienes Ansible instalado):

```sh
cd /vagrant  # dentro de la VM ansible
ansible-playbook site.yml --check
ansible-playbook site.yml
```

## Verificación de servicios en el servidor

En la VM **server** (o mediante `vagrant ssh server`):

```sh
systemctl status named
systemctl status dhcpd
ss -tulnp | grep :53
ss -u -l | grep :67
```

## Verificación de DHCP desde el cliente

En la VM **client**:

```sh
sudo dhclient -v -r
sudo dhclient -v
ip a
cat /etc/resolv.conf
```

## Verificación de DNS

Desde **client** (puede usarse también en server):

```sh
dig @192.168.56.10 example.local
nslookup example.local 192.168.56.10
host example.local 192.168.56.10
```

> Ajusta el dominio (`example.local`) según tu configuración en `group_vars/all.yml`.

## Demostración completa paso a paso

```sh
# 1) Levantar infra
vagrant up

# 2) Conectar al nodo ansible
vagrant ssh ansible
cd /vagrant

# 3) Validar playbook antes de aplicar
ansible-playbook site.yml --check

# 4) Aplicar configuración
ansible-playbook site.yml

# 5) Verificar servicios en el servidor
vagrant ssh server
systemctl status named
systemctl status dhcpd
ss -tulnp | grep :53
ss -u -l | grep :67

# 6) Verificar cliente obtiene IP y DNS funciona
vagrant ssh client
sudo dhclient -v -r
sudo dhclient -v
ip a
cat /etc/resolv.conf

# 7) Probar resolución DNS
dig @192.168.56.10 example.local
```

## Estructura del repositorio

```
ansible.cfg
inventory/hosts.ini
group_vars/all.yml
site.yml
Vagrantfile
README.md
.gitignore
.github/workflows/ci.yml
roles/
  dns_bind/
    defaults/main.yml
    handlers/main.yml
    tasks/main.yml
    templates/named.conf.j2
    templates/zone.forward.j2
    templates/zone.reverse.j2
  dhcpd/
    defaults/main.yml
    handlers/main.yml
    tasks/main.yml
    templates/dhcpd.conf.j2
```

## Limpieza del laboratorio

```sh
vagrant destroy -f
```
