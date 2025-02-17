## Change NCN Image Root Password and SSH Keys

Customize the NCN image by changing the root password or adding different ssh keys for the root account.
This procedure shows this process being done on the PIT node during a first time installation of the CSM
software.

This process should be done for the "Kubernetes" image used by master and worker nodes and then repeated for the "ceph" image used by the utility storage nodes.

### Kubernetes Image

The Kubernetes image is used by the master and worker nodes.

1. Open the image.

   The Kubernetes image will be of the form "kubernetes-0.1.69.squashfs" in /var/www/ephemeral/data/k8s, but the version number may be different.

   ```bash
   pit# cd /var/www/ephemeral/data/k8s
   pit# unsquashfs kubernetes-0.1.69.squashfs
   ```
1. Chroot into the image root

   ```bash
   pit# chroot ./squashfs-root
   ```

1. Change the password

   ```bash
   chroot-pit# passwd
   ```

1. Replace the ssh keys

   ```bash
   chroot-pit# cd root
   ```

1. Replace the default root public and private ssh keys with your own or generate a new pair with `ssh-keygen(1)`

   ```bash
   chroot-pit# mknod /dev/urandom c 1 9
   chroot-pit# ssh-keygen <options>
   chroot-pit# rm /dev/urandom
   ```

1. Create the new SquashFS artifact

   ```bash
   chroot-pit# /srv/cray/scripts/common/create-kis-artifacts.sh
   ```

1. Exit the chroot

   ```bash
   chroot-pit# exit
   ```

1. Clean up the SquashFS creation

   The Kubernetes image directory is /var/www/ephemeral/data/k8s.

   ```bash
   pit# umount -v /var/www/ephemeral/data/k8s/squashfs-root/mnt/squashfs
   ```

1. Save old SquashFS image.

   ```bash
   pit# mkdir -v old
   pit# mv -v *squashfs old
   ```

1. Move new SquashFS image, kernel, and initrd into place.

   ```bash
   pit# mv -v squashfs-root/squashfs/* .
   ```

1. Update file permissions on initrd

   ```bash
   pit# chmod -v 644 initrd.img.xz
   ```

1. Set the boot links.

   ```bash
   pit# cd
   pit# set-sqfs-links.sh
   ```

The Kubernetes image will have the new password for the next boot.

### Ceph Image

The Ceph image is used by the utility storage nodes.

1. Open the image.

   The Ceph image will be of the form "storage-ceph-0.1.69.squashfs" in /var/www/ephemeral/data/ceph, but the version number may be different.

   ```bash
   pit# cd /var/www/ephemeral/data/ceph
   pit# unsquashfs storage-ceph-0.1.69.squashfs
   ```

1. Change into the image root

   ```bash
   pit# chroot ./squashfs-root
   ```

1. Change the password

   ```bash
   chroot-pit# passwd
   ```

1. Replace the ssh keys

   ```bash
   chroot-pit# cd root
   ```

1. Replace the default root public and private ssh keys with your own or generate a new pair with `ssh-keygen(1)`

1. Create the new SquashFS artifact

   ```bash
   chroot-pit# /srv/cray/scripts/common/create-kis-artifacts.sh
   ```

1. Exit the chroot

   ```bash
   chroot-pit# exit
   ```

1. Clean up the SquashFS creation

   The Ceph image directory is /var/www/ephemeral/data/ceph.

   ```bash
   pit# umount -v /var/www/ephemeral/data/ceph/squashfs-root/mnt/squashfs
   ```

1. Save old SquashFS image.

   ```bash
   pit# mkdir -v old
   pit# mv -v *squashfs old
   ```

1. Move new SquashFS image, kernel, and initrd into place.

   ```bash
   pit# mv -v squashfs-root/squashfs/* .
   ```

1. Update file permissions on initrd

   ```bash
   pit# chmod -v 644 initrd.img.xz
   ```

1. Set the boot links.

   ```bash
   pit# cd
   pit# set-sqfs-links.sh
   ```

The Ceph image will have the new password for the next boot.
