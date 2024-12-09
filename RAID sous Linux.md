## Configuration d'un RAID 1
### Installation de mdadm
`sudo apt install mdadm -y`

### Listing des disques
`lsblk`  
`sudo fdisk -l`

### Partition des disques
`sudo fdisk /dev/sdb`  
`lsblk`  
`sudo fdisk /dev/sdc`  
`lsblk`  

### Création d'un RAID 1
`sudo mdadm --create /dev/md0 --level 1 --raid-devices 2 /dev/sdb1 /dev/sdc1`

### Vérification l'état du RAID
`cat /proc/mdstat`  
`sudo mdadm --detail /dev/md0`

### Formatage et montage du RAID
`sudo mkfs.ext4 /dev/md0 -L "PersonalData"`  
`sudo mkdir -p /home/yohann/Data-RAID1`  
`sudo mount /dev/md0 /home/yohann/Data-RAID1/`

### Configuration du montage
`sudo nano /etc/fstab`

### Configuration mdadm
`sudo mdadm --detail --scan`  
`sudo nano /etc/mdadm/mdadm.conf`

### Mise à jour de initramfs
`sudo update-initramfs -u`

### Redémarrage du système
`reboot`

### Création d'un fichier avec permission
`sudo touch /home/yohann/Data-RAID1/test.txt`  
`sudo chown yohann:yohann /home/yohann/Data-RAID1/test.txt`

### Simulation d'une panne et réparation du RAID
`cat /proc/mdstat`  
`sudo mdadm /dev/md0 --fail /dev/sdb1`  
`sudo mdadm --detail /dev/md0`

### Ajout d'un disque de remplacement
`sudo fdisk /dev/sdd`  
`lsblk`  
`sudo mdadm --manage /dev/md0 --add /dev/sdd1`  
`sudo mdadm --detail /dev/md0`  


## Configuration d'un RAID 5
### Installation de mdadm
`sudo apt install mdadm -y`

### Listing des disques
`lsblk`  
`sudo fdisk -l`

### Partition des disques
`sudo fdisk /dev/sdb`  
`lsblk`  
`sudo fdisk /dev/sdc`  
`lsblk`
`sudo fdisk /dev/sdd`

### Création d'un RAID 5
`sudo mdadm --create /dev/md0 --level 5 --raid-devices 3 /dev/sdb1 /dev/sdc1 /dev/sdd1`

### Vérification de l'état du RAID
`cat /proc/mdstat`  
`sudo mdadm --detail /dev/md0`

### Formatage et montage du RAID
`sudo mkfs.ext4 /dev/md0 -L "PersonalData"`  
`sudo mkdir -p /home/yohann/Data-RAID5`  
`sudo mount /dev/md0 /home/yohann/Data-RAID5/`

### Configuration du montage
`sudo nano /etc/fstab`

### Configuration de mdadm
`sudo mdadm --detail --scan`
`sudo nano /etc/mdadm/mdadm.conf`

### Mise à jour de initramfs
`sudo update-initramfs -u`

### Redémarrage du système
`reboot`

### Création d'un fichier avec permission
`sudo touch /home/yohann/Data-RAID5/test.txt`  
`sudo chown yohann:yohann /home/yohann/Data-RAID5/test.txt`

### Simulation d'une panne et réparation du RAID
`cat /proc/mdstat`  
`sudo mdadm /dev/md0 --fail /dev/sdb1`  
`sudo mdadm --detail /dev/md0`

### Ajout d'un disque de remplacement
`sudo fdisk /dev/sde`  
`lsblk`  
`sudo mdadm --manage /dev/md0 --add /dev/sde1`  
`sudo mdadm --detail /dev/md0`


