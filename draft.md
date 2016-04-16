# New drive of 20GB

# Device
ls /dev/sdb*
/dev/sdb

wipefs -a /dev/sdb


# 5.2. Four primary partitions
http://www.tldp.org/HOWTO/html_single/Partition/

    fdisk p

Device     Boot   Start      End  Sectors  Size Id Type
/dev/sdb1          2048  1050623  1048576  512M 83 Linux
/dev/sdb2       1050624  9439231  8388608    4G 82 Linux swap / Solaris
/dev/sdb3       9439232 41943039 32503808 15,5G 83 Linux

    ls -l /dev/sdb*
brw-rw---- 1 root disk 8, 16 avril 16 18:17 /dev/sdb
brw-rw---- 1 root disk 8, 17 avril 16 18:17 /dev/sdb1
brw-rw---- 1 root disk 8, 18 avril 16 18:17 /dev/sdb2
brw-rw---- 1 root disk 8, 19 avril 16 18:17 /dev/sdb3



# Formating


    mkfs -v -t ext4 /dev/sdb1 -F
    mkswap /dev/sdb2
    /sbin/swapon /dev/sdb2

    mkfs -v -t ext4 /dev/sdb3 -F

# Mount


    export LFS=/mnt/lfs
    mkdir -pv $LFS
    mount -v -t ext4 /dev/sdb3 $LFS

    mkdir -v $LFS/boot
    mount -v -t ext4 /dev/sdb1 $LFS/boot


# Packages

    mkdir -v $LFS/sources
    chmod -v a+wt $LFS/sources

    cd $LFS/sources
    wget --input-file=wget-list --continue --directory-prefix=$LFS/sources
    md5sum -c md5sums

# Tools


    mkdir -v $LFS/tools
    ln -sv $LFS/tools /


# Dedicated user

    groupadd lfs
    useradd -s /bin/bash -g lfs -m -k /dev/null lfs

Interactive passwd:

    passwd lfs

    chown -v lfs $LFS/tools && chown -v lfs $LFS/sources

As LFS user:

    su - lfs
    cat > ~/.bash_profile << EOF
    exec env -i HOME=$HOME TERM=$TERM PS1='\u:\w\$ ' /bin/bash
    EOF

http://www.linuxfromscratch.org/lfs/view/stable/chapter05/generalinstructions.html

    cat > ~/.bashrc << "EOF"
    set +h
    umask 022
    LFS=/mnt/lfs
    LC_ALL=POSIX
    LFS_TGT=$(uname -m)-lfs-linux-gnu
    PATH=/tools/bin:/bin:/usr/bin
    export LFS LC_ALL LFS_TGT PATH
    EOF

