# linux-repair-guide
# 🛠️ Guía Profesional para Reparar Linux: GRUB, Particiones y Módulos del Kernel

## 📌 Introducción

Esta guía combina técnicas avanzadas para reparar sistemas Linux, desde problemas de arranque hasta ajustes de kernel en tiempo real. Ideal para administradores de sistemas, pentesters y usuarios avanzados que necesiten solucionar problemas críticos.

```markdown
## 🔍 Diagnóstico Inicial

### Identificar el problema
```bash
# Ver logs del kernel
dmesg | tail -30
journalctl -xb

# Ver estado de discos y particiones
lsblk -f
sudo fdisk -l
sudo blkid
```

### Comprobar salud del disco
```bash
sudo smartctl -a /dev/sda
```

## 🚀 Reparación de GRUB y Arranque

### Desde Live USB (Método Universal)
1. **Montar sistema instalado**:
```bash
sudo mount /dev/sdXn /mnt
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys
```

2. **Chroot y reinstalar GRUB**:
```bash
sudo chroot /mnt
grub-install /dev/sdX
update-grub
exit
```

3. **Para sistemas UEFI**:
```bash
sudo mount /dev/sdXn /mnt/boot/efi
```

### Reparación Rápida con Boot-Repair
```bash
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt update
sudo apt install boot-repair
boot-repair
```

## 🖥️ Reparación de Sistemas de Archivos

### EXT4
```bash
sudo umount /dev/sdXn
sudo fsck.ext4 -f -y /dev/sdXn
```

### Btrfs
```bash
sudo btrfs check --readonly /dev/sdXn
# Solo si es necesario:
sudo btrfs check --repair /dev/sdXn
```

### XFS
```bash
sudo umount /dev/sdXn
sudo xfs_repair /dev/sdXn
```

### ZFS
```bash
sudo zpool status
sudo zpool scrub pool_name
```

## ⚙️ Ajustes con inismod (Módulos del Kernel)

### Cambiar parámetros en tiempo real
```bash
# Ver parámetros disponibles
ls /sys/module/modulo/parameters/

# Ejemplo: Desactivar ahorro energía WiFi
echo 0 | sudo tee /sys/module/iwlwifi/parameters/power_save
```

### Hacer cambios persistentes
```bash
echo "options modulo parametro=valor" | sudo tee /etc/modprobe.d/modulo.conf
```

## 🛡️ Casos Comunes de Reparación

### Problemas de Red
```bash
# Reiniciar NetworkManager
sudo systemctl restart NetworkManager

# Recargar módulo de red
sudo modprobe -r modulo_red && sudo modprobe modulo_red
```

### Problemas Gráficos
```bash
# Forzar modo de renderizado (ejemplo para Intel)
echo 1 | sudo tee /sys/module/i915/parameters/enable_fbc
```

### Problemas de Audio
```bash
# Recargar ALSA
sudo alsa force-reload
```

## 📋 Checklist de Reparación

1. [ ] Verificar logs del sistema (`journalctl -xb`)
2. [ ] Comprobar estado de discos (`smartctl`)
3. [ ] Reparar GRUB si no arranca
4. [ ] Verificar y reparar sistemas de archivos
5. [ ] Ajustar parámetros críticos del kernel
6. [ ] Probar cambios antes de hacerlos persistentes

## 💡 Consejos Profesionales

- **Siempre haz backup** antes de realizar cambios críticos
- **Documenta** todos los cambios realizados
- **Prueba en entorno controlado** antes de producción
- Usa `nohup` para comandos largos en SSH
- Considera usar `screen` o `tmux` para sesiones persistentes

## 📚 Recursos Adicionales

- [Guía oficial de GRUB](https://www.gnu.org/software/grub/manual/grub/)
- [Documentación de Btrfs](https://btrfs.wiki.kernel.org/index.php/Main_Page)
- [Kernel Parameters](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)

