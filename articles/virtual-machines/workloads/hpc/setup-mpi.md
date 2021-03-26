---
title: Konfigurowanie interfejsu przekazywania komunikatów (MPI) dla HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak skonfigurować MPI dla HPC na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 03/18/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 66de34c43ab1b3a6b4245f77196793bf9ad8530c
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606644"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Skonfiguruj interfejs przekazywania komunikatów dla HPC

[Interfejs przekazywania komunikatów (MPI)](https://en.wikipedia.org/wiki/Message_Passing_Interface) to otwarta biblioteka i de-facto Standard dla przetwarzanie równoległe pamięci rozproszonej. Jest on często używany w wielu obciążeniach HPC. Obciążenia HPC w [seriach H](../../sizes-hpc.md) z [obsługą funkcji RDMA](../../sizes-hpc.md#rdma-capable-instances) i maszyn wirtualnych [serii N](../../sizes-gpu.md) mogą używać MPI do komunikacji za pośrednictwem sieci InfiniBand o małym opóźnieniu i wysokiej przepustowości.
- Rozmiary maszyn wirtualnych z obsługą wirtualizacji SR-IOV na platformie Azure zezwalają niemal dowolnym wersja MPI do użycia z interfejsem Mellanox OFED.
- W przypadku maszyn wirtualnych z obsługą funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu Microsoft Network Direct (ND) do komunikacji między maszynami wirtualnymi. W związku z tym obsługiwane są tylko programy Microsoft MPI (MS-MPI) 2012 R2 lub nowsze i Intel MPI 5. x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być niezgodne ze sterownikami usługi Azure RDMA.

W przypadku funkcji SR-IOV obsługujących [funkcję RDMA](../../sizes-hpc.md#rdma-capable-instances)można używać [obrazów maszyn wirtualnych CentOS-HPC](configure.md#centos-hpc-vm-images) w wersji 7,6 i nowszych. Te obrazy maszyn wirtualnych są zoptymalizowane i wstępnie załadowane za pomocą sterowników OFED dla funkcji RDMA oraz różnych powszechnie używanych bibliotek MPI i naukowych pakietów obliczeniowych. najłatwiej rozpocząć pracę.

Przykładowe przykłady dotyczą RHEL/CentOS, ale czynności są ogólne i mogą być używane w przypadku dowolnego zgodnego systemu operacyjnego Linux, takiego jak Ubuntu (16,04, 18,04 19,04, 20,04) i SLES (12 SP4 i 15). Więcej przykładów konfigurowania innych implementacji MPI na innych dystrybucje znajduje się w [repozytorium azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Uruchamianie zadań MPI na maszynach wirtualnych z obsługą funkcji SR-IOV z niektórymi bibliotekami MPI (takimi jak platforma MPI) może wymagać skonfigurowania kluczy partycji (p-Keys) w ramach dzierżawy w celu odizolowania i zabezpieczenia. Postępuj zgodnie z instrukcjami w sekcji [odnajdywanie kluczy partycji](#discover-partition-keys) , aby uzyskać szczegółowe informacje na temat określania wartości p-Key i ustawiania ich prawidłowo dla zadania MPI z tą biblioteką MPI.

> [!NOTE]
> Poniższe fragmenty kodu są przykładami. Zalecamy używanie najnowszych stabilnych wersji pakietów lub odwoływanie się do [repozytorium azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="ucx"></a>UCX

[Ujednolicona komunikacja X (UCX)](https://github.com/openucx/ucx) to struktura interfejsów API komunikacji dla HPC. Jest zoptymalizowany pod kątem komunikacji MPI przez InfiniBand i współpracuje z wieloma implementacjami MPI, takimi jak OpenMPI i MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Ostatnie kompilacje UCX rozwiązały [problem](https://github.com/openucx/ucx/pull/5965) , w którym w obecności wielu interfejsów karty interfejsu sieciowego wybrano odpowiedni interfejs InfiniBand. Więcej szczegółowych informacji na temat uruchamiania MPI przez InfiniBand po włączeniu przyspieszonej [sieci na maszynie](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) wirtualnej.

## <a name="hpc-x"></a>HPC-X

[Zestaw narzędzi HPC-X oprogramowania](https://www.mellanox.com/products/hpc-x-toolkit) zawiera UCX i HCOLL i można go skompilować z UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Uruchom HPC-X

```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optymalizacja zbiorów MPI

Elementy podstawowe komunikacji MPI zapewniają elastyczny, przenośny sposób implementacji operacji komunikacji grupowej. Są one szeroko wykorzystywane w różnych naukowych aplikacjach równoległych i mają znaczny wpływ na ogólną wydajność aplikacji. Zapoznaj się z [artykułem TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) , aby uzyskać szczegółowe informacje dotyczące parametrów konfiguracji w celu optymalizacji zbiorczej wydajności komunikacji przy użyciu biblioteki HPC-X i HCOLL w celu zapewnienia komunikacji zbiorowej.

> [!NOTE] 
> Przy użyciu HPC-X 2.7.4 +, może być konieczne jawne przekazanie LD_LIBRARY_PATH, jeśli wersja UCX w MOFED a, która w HPC-X jest inna.

## <a name="openmpi"></a>OpenMPI

Zainstaluj UCX zgodnie z powyższym opisem. HCOLL jest częścią [zestawu narzędzi HPC-X oprogramowania](https://www.mellanox.com/products/hpc-x-toolkit) i nie wymaga specjalnej instalacji.

OpenMPI można zainstalować z pakietów dostępnych w repozytorium.

```bash
sudo yum install –y openmpi
```

Zalecamy utworzenie najnowszej, stabilnej wersji OpenMPI z UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Aby uzyskać optymalną wydajność, uruchom program OpenMPI z systemami `ucx` i `hcoll` .

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="intel-mpi"></a>Intel MPI

Pobierz wybraną wersję platformy [Intel MPI](https://software.intel.com/mpi-library/choose-download). Zmień zmienną środowiskową I_MPI_FABRICS w zależności od wersji.
- Intel MPI 2019 i 2021: Użyj `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . `mlx`Dostawca używa UCX. Użycie czasowników okazało się niestabilne i mniej wydajne. Aby uzyskać więcej informacji, zobacz [artykuł TechCommunity](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) .
- Intel MPI 2018: Użyj `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: Użyj `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

### <a name="non-sr-iov-vms"></a>Maszyny wirtualne bez wirtualizacji SR-IOV
W przypadku maszyn wirtualnych z wirtualizacją SR-IOV przykładem pobierania [wersji ewaluacyjnej](https://registrationcenter.intel.com/en/forms/?productid=1740) programu 5. x w środowisku uruchomieniowym jest następująca:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Kroki instalacji można znaleźć w [podręczniku instalacji biblioteki MPI firmy Intel](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).
Opcjonalnie można włączyć ptrace dla niegłównych procesów niezwiązanych z debugerem (wymaganych przez najnowsze wersje technologii Intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
W przypadku SUSE Linux Enterprise Server wersji obrazu maszyny wirtualnej — SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15, Sterowniki RDMA są instalowane i pakiety Intel MPI są dystrybuowane na maszynie wirtualnej. Zainstaluj procesor Intel MPI, uruchamiając następujące polecenie:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich2"></a>MVAPICH2

Kompilacja MVAPICH2.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Uruchamianie MVAPICH2.

```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

## <a name="platform-mpi"></a>MPI platformy

Zainstaluj wymagane pakiety dla platformy platform MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postępuj zgodnie z procesem instalacji.

Następujące polecenia są przykładami uruchamiania MPI pingpong i allreduce za pomocą platformy MPI na maszynach wirtualnych HBv3 przy użyciu CentOS-HPC 7,6, 7,8 i 8,1 obrazów maszyn wirtualnych.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Zainstaluj UCX zgodnie z powyższym opisem. Kompilacja MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Uruchamianie MPICH.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Sprawdź swój klucz partycji, jak wspomniano powyżej.

## <a name="osu-mpi-benchmarks"></a>OSU MPI — testy porównawcze

Pobierz [testy OSU MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) i untar.

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

Należy również pamiętać, że o ile istnieje dzierżawca (zestaw dostępności lub zestaw skalowania maszyn wirtualnych), PKEYs pozostaje taka sama. Jest to prawdziwe nawet po dodaniu lub usunięciu węzłów. Nowi dzierżawcy uzyskują różne PKEYs.


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

- Dowiedz się więcej na temat maszyn wirtualnych z [obsługą](../../sizes-hpc.md#rdma-capable-instances) urządzeń z serii [H](../../sizes-hpc.md) i [serii N](../../sizes-gpu.md)
- Zapoznaj się z [omówieniem HBv3](hbv3-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md).
- Przeczytaj o najnowszych anonsach, przykładach obciążeń HPC i wynikach wydajności na [blogach społecznościowych usługi Azure COMPUTE Tech](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z widokiem architektury w przypadku uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
