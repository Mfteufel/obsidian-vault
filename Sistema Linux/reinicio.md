
Antes de apagar optimizamos el arranque de la PC. Línea base: 43,7 s en total (20,7 s firmware + 3 s loader + 2,1 s kernel + 17,9 s userspace). El cuello de botella eran docker (32,8 s), NetworkManager-wait-online (9,5 s), vboxdrv (7,7 s) y plymouth-quit-wait (8 s).

Deshabilité con systemctl disable --now: NetworkManager-wait-online, cloud-init-local/main/network, cloud-config, cloud-final, kdump-tools, ModemManager, docker, containerd, mssql-server, postgresql, vboxweb-service, vboxautostart-service y qemu-kvm. docker.socket quedó activo, así que docker se levanta solo cuando uso un comando docker.

Ahora ejecutá systemd-analyze, systemd-analyze blame y systemd-analyze critical-chain, comparalo con la línea base y decime cuánto mejoró y qué sigue siendo lento. Revisá también si la RAM libre al inicio mejoró (free -h).


---

Contexto de lo que ya hice en mi PC (ThinkPad L15 Gen 4, Ubuntu, usuario marco) el 2026-10-10/11:

OPTIMIZACIÓN DE ARRANQUE
- Línea base: 43,7 s (20,7 firmware + 3 loader + 2,1 kernel + 17,9 userspace).
- Deshabilité con systemctl disable --now: NetworkManager-wait-online, cloud-init (local/main/network), cloud-config, cloud-final, kdump-tools, ModemManager, docker, containerd, mssql-server, postgresql, vboxweb-service, vboxautostart-service y qemu-kvm. docker.socket sigue activo.
- Resultado: 36,8 s (20,4 firmware + 2,9 loader + 2,0 kernel + 11,5 userspace). RAM tras arrancar: 13 GiB total, 3,4 usados, 6,0 libres, 10 disponibles.
- Pendiente lento: firmware 20 s (solo se arregla en BIOS), plymouth-quit-wait 5,2 s (se puede quitar "splash" de GRUB), vboxdrv 4,7 s (sigue enabled), snapd.seeded y muchos montajes snap (revisiones viejas con snap list --all), boot-efi.mount 2,3 s.

DOCKER
- Descubrí que docker.service se levantaba igual (18 s) porque docker-desktop.service (unidad de usuario, graphical-session.target) estaba enabled. Lo deshabilité con systemctl --user disable docker-desktop.service. NO lo detuve; sigue hasta reiniciar.
- QUIERO seguir usando Docker Desktop (contexto desktop-linux, socket ~/.docker/desktop/docker.sock). Se abre a mano o con systemctl --user start docker-desktop. Desktop y el docker del sistema (contexto default, /var/run/docker.sock) son daemons distintos con contenedores separados.
- En el daemon del sistema (default) borré: geekcards_db, geekcards_pma, geekcards_api, geek_mysql, geek_phpmyadmin, portainer, los 2 hello-world, los volúmenes back_db_data, geek-docker_geek_mysql_data y portainer_data, y todas las imágenes sin uso (2,16 GB).
- Lo ÚNICO que uso y conservo: contenedor code-parkloop-db-1 (postgres:16-alpine, puerto 5433, restart unless-stopped) con su volumen code-parkloop_parkloop_db_data. No lo borres.

PENDIENTE
- Reiniciar y comparar con: systemd-analyze, systemd-analyze blame | head, systemd-analyze critical-chain, free -h. Ver si docker.service desaparece de blame.
- Decidir si quito splash de GRUB y deshabilito vboxdrv.

Reglas: antes de borrar datos, volúmenes o contenedores, pedime confirmación explícita. Respondeme en español.

