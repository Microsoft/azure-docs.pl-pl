---
title: Konfigurowanie interfejsu przekazywania komunikatów dla HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak skonfigurować MPI dla HPC na platformie Azure.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/15/2019
ms.author: amverma
ms.openlocfilehash: 3ca9a21d105be6f17c1aa40ae1a0ab7f01c38184
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87083416"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Skonfiguruj interfejs przekazywania komunikatów dla HPC

Obciążenia interfejsu przekazywania komunikatów (MPI) są znaczną częścią tradycyjnych obciążeń HPC. Rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure zezwalają na użycie niemal wszystkich wersji MPI. 

Uruchamianie zadań MPI na maszynach wirtualnych wymaga skonfigurowania kluczy partycji (p-Keys) w dzierżawie. Postępuj zgodnie z instrukcjami w sekcji [odnajdywanie kluczy partycji](#discover-partition-keys) , aby uzyskać szczegółowe informacje na temat określania wartości p-Key.

## <a name="ucx"></a>UCX

[UCX](https://github.com/openucx/ucx) oferuje najlepszą wydajność w systemie IB i współpracuje z MPICH i OpenMPI.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

## <a name="openmpi"></a>OpenMPI

Zainstaluj UCX, jak opisano wcześniej.

```bash
sudo yum install –y openmpi
```

Kompilacja OpenMPI.

```bash
wget https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-4.0.0.tar.gz
tar -xvf openmpi-4.0.0.tar.gz
cd openmpi-4.0.0
./configure --with-ucx=<ucx-install-path> --prefix=<ompi-install-path>
make -j 8 && make install
```

Uruchom OpenMPI.

```bash
<ompi-install-path>/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="mpich"></a>MPICH

Zainstaluj UCX, jak opisano wcześniej.

Kompilacja MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=<ucx-install-path> --prefix=<mpich-install-path> --with-device=ch4:ucx
make -j 8 && make install
```

Uruchamianie MPICH.

```bash
<mpich-install-path>/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="mvapich2"></a>MVAPICH2

Kompilacja MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=<mvapich2-install-path>
make -j 8 && make install
```

Uruchamianie MVAPICH2.

```bash
<mvapich2-install-path>/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi-community-edition"></a>Platforma MPI Community Edition

Zainstaluj wymagane pakiety dla MPI platformy.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postępuj zgodnie z procesem instalacji.

## <a name="intel-mpi"></a>Intel MPI

[Pobierz procesor Intel MPI](https://software.intel.com/mpi-library/choose-download).

Zmień zmienną środowiskową I_MPI_FABRICS w zależności od wersji. W przypadku procesora Intel MPI 2018 Użyj programu `I_MPI_FABRICS=shm:ofa` i dla 2019, użyj `I_MPI_FABRICS=shm:ofi` .

Przypinanie procesów działa prawidłowo domyślnie dla 15, 30 i 60 PPN.

## <a name="osu-mpi-benchmarks"></a>OSU MPI — testy porównawcze

[Pobierz testy OSU MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) i untar.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

Tworzenie testów porównawczych przy użyciu określonej biblioteki MPI:

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI testów porównawczych znajdują się w `mpi/` folderze.


## <a name="discover-partition-keys"></a>Wykryj klucze partycji

Wykryj klucze partycji (p-Keys), aby komunikować się z innymi maszynami wirtualnymi w ramach tej samej dzierżawy (zestawu dostępności lub zestawu skalowania maszyn wirtualnych).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Większa z nich to klucz dzierżawy, który powinien być używany z MPI. Przykład: Jeśli następujące są klucze p, 0x800b należy używać z MPI.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Użyj partycji innej niż domyślna (0x7FFF). UCX wymaga wyczyszczenia MSBu p-Key. Na przykład ustaw UCX_IB_PKEY jako 0x000b dla 0x800b.

Należy również pamiętać, że dopóki dzierżawa (AVSet lub VMSS) istnieje, PKEYs pozostaje taka sama. Jest to prawdziwe nawet po dodaniu lub usunięciu węzłów. Nowi dzierżawcy uzyskują różne PKEYs.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurowanie limitów użytkowników dla MPI

Skonfiguruj limity użytkowników dla MPI.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```


## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurowanie kluczy SSH dla MPI

Skonfiguruj klucze SSH dla typów MPI, które tego wymagają.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

Powyższa składnia zakłada, że udostępniony katalog macierzysty, w przeciwnym razie należy skopiować katalog SSH do każdego węzła.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [HPC](/azure/architecture/topics/high-performance-computing/) na platformie Azure.
