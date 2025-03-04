# archinstall SIN DINERO
Cómo Instalar Archlinux  sin gastar un centavo en Cuba.


Aquí tienes una guía profesional y detallada en español para instalar Arch Linux, escrita en formato Markdown. Puedes copiarla directamente a tu repositorio de GitHub.


# Guía de Instalación de Arch Linux

Esta guía te ayudará a instalar Arch Linux desde cero. Asegúrate de seguir cada paso cuidadosamente.

---

## 1. Verificar el modo de arranque (UEFI o BIOS)

Primero, verifica si tu sistema arranca en modo **UEFI** o **BIOS**. Esto es importante porque afecta cómo se configuran las particiones y el gestor de arranque.

```bash
ls /sys/firmware/efi/efivars
```

- **Si el comando muestra archivos**: Estás en modo **UEFI**.
- **Si no muestra nada**: Estás en modo **BIOS**.

> **Nota**: Si estás en modo BIOS, puedes seguir esta guía, pero omite los pasos relacionados con la partición EFI.

---

## 2. Particionar el disco

Usa `lsblk` para ver los discos disponibles:

```bash
lsblk
```

Luego, usa `cfdisk` para particionar el disco:

```bash
cfdisk /dev/sdx
```

> **Nota**: Reemplaza `/dev/sdx` con el disco correcto (por ejemplo, `/dev/sda`).

### Ejemplo de tabla de particiones:

| Dispositivo | Tamaño       | Tipo                |
|-------------|--------------|---------------------|
| `/dev/sdx1` | 550M         | EFI System          |
| `/dev/sdx2` | 2x RAM       | Swap (opcional)     |
| `/dev/sdx3` | Restante     | Linux filesystem    |

- **EFI System**: Necesaria para sistemas UEFI. 550M es suficiente para la mayoría de los casos.
- **Swap**: Opcional, pero recomendado. Usa el doble de tu RAM si planeas hibernar, o igual a tu RAM si no.
- **Linux filesystem**: Aquí se instalará Arch Linux.

> **Nota**: Si estás en modo BIOS, no necesitas la partición EFI.

---

## 3. Formatear las particiones

### Partición EFI (solo UEFI):

```bash
mkfs.fat -F32 /dev/sdx1
```

### Partición Swap (opcional):

```bash
mkswap /dev/sdx2
swapon /dev/sdx2
```

### Partición raíz:

```bash
mkfs.ext4 /dev/sdx3
```

---

## 4. Montar las particiones

### Montar la partición raíz:

```bash
mount /dev/sdx3 /mnt
```

### Montar la partición EFI (solo UEFI):

```bash
mkdir -p /mnt/boot/efi
mount /dev/sdx1 /mnt/boot/efi
```

---

## 5. Configurar las claves de pacman

Inicializa y configura las claves de pacman:

```bash
pacman-key --init
pacman-key --populate archlinux
pacman -S archlinux-keyring
```

---

## 6. Eliminar reflector (opcional)

Reflector puede interferir durante la instalación. Si no lo necesitas, elimínalo:

```bash
pacman -R reflector
systemctl stop reflector.service
rm -R /etc/systemd/system/reflector.service.d/
```

---

## 7. Configurar el mirrorlist (opcional para Cuba)

Si estás en Cuba, añade el repositorio de la UCI al archivo `mirrorlist`:

```bash
nano /etc/pacman.d/mirrorlist
```

Añade esta línea al principio:

```plaintext
Server = http://repos.uo.edu.cu/archlinux/$repo/os/$arch
```

---

## 8. Instalar los paquetes base

Instala los paquetes esenciales:

```bash
pacstrap /mnt base linux linux-firmware git nano
```

---

## 9. Generar el fstab

Genera el archivo `fstab` para montar las particiones automáticamente:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

---

## 10. Cambiar al entorno chroot

Cambia al entorno recién instalado:

```bash
arch-chroot /mnt
```

---

## 11. Configurar la hora y el idioma

### Configurar la hora:

```bash
timedatectl set-ntp true
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
```

> **Ejemplo**: Para Cuba, usa `America/Havana`.

### Configurar el idioma:

Edita `/etc/locale.gen` y descomenta los idiomas que necesites (por ejemplo, `es_ES.UTF-8 UTF-8`).

Luego, genera las localizaciones:

```bash
locale-gen
echo LANG=es_ES.UTF-8 > /etc/locale.conf
```

---

## 12. Configurar el hostname y los hosts

### Establecer el hostname:

```bash
echo nombre_del_equipo > /etc/hostname
```

### Configurar `/etc/hosts`:

```bash
nano /etc/hosts
```

Añade estas líneas:

```plaintext
127.0.0.1   localhost
::1         localhost
127.0.1.1   nombre_del_equipo.localdomain nombre_del_equipo
```

---

## 13. Configurar la red

Instala y habilita NetworkManager:

```bash
pacman -S networkmanager
systemctl enable NetworkManager
```

---

## 14. Establecer la contraseña de root

Establece una contraseña para el usuario root:

```bash
passwd
```

---

## 15. Instalar el gestor de arranque (GRUB)

### Para UEFI:

```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

### Para BIOS:

```bash
pacman -S grub
grub-install --target=i386-pc /dev/sdx
grub-mkconfig -o /boot/grub/grub.cfg
```

> **Nota**: Reemplaza `/dev/sdx` con tu disco (por ejemplo, `/dev/sda`).

---

## 16. Finalizar la instalación

### Salir del entorno chroot:

```bash
exit
```

### Desmontar las particiones:

```bash
umount -R /mnt
```

### Reiniciar el sistema:

```bash
reboot
```

---

¡Felicidades! Has instalado Arch Linux correctamente. Ahora puedes iniciar sesión y comenzar a personalizar tu sistema.
```

### Instrucciones para GitHub:
1. Crea un archivo llamado `README.md` en tu repositorio.
2. Copia y pega el contenido anterior en el archivo.
3. Haz commit y push a tu repositorio.

¡Listo! Ahora tienes una guía profesional y detallada para instalar Arch Linux en tu GitHub. 😊
