---
title: Konfigurowanie usługi Message Passing Interface (MPI) dla HPC — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak skonfigurować mpi dla HPC na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f43fc94174ebdcfdf447d3635a696193959849fa
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600310"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>Konfigurowanie Message Passing Interface hpc

Biblioteka [Message Passing Interface (MPI) jest](https://en.wikipedia.org/wiki/Message_Passing_Interface) otwartą biblioteką i de facto standardem przetwarzania równoległego pamięci rozproszonej. Jest on często używany w wielu obciążeniach HPC. Obciążenia HPC na maszyny wirtualne serii H i [N](../../sizes-gpu.md) z serii [RDMA](../../sizes-hpc.md#rdma-capable-instances) [](../../sizes-hpc.md) mogą używać interfejsu MPI do komunikowania się za pośrednictwem sieci InfiniBand o małych opóźnieniach i dużej przepustowości.
- Rozmiary maszyn wirtualnych z włączoną obsługą funkcji SR-IOV na platformie Azure umożliwiają korzystanie z niemal wszystkich odmian MPI w przypadku aplikacji Mellanox OFED.
- Na maszynach wirtualnych bez obsługi funkcji SR-IOV obsługiwane implementacje MPI używają interfejsu ND (Microsoft Network Direct) do komunikacji między maszynami wirtualnych. W związku z tym obsługiwane są tylko wersje Microsoft MPI (MS-MPI) 2012 R2 lub nowsze oraz Intel MPI 5.x. Nowsze wersje (2017, 2018) biblioteki środowiska uruchomieniowego Intel MPI mogą być lub nie być zgodne ze sterownikami RDMA platformy Azure.

W przypadku maszyn wirtualnych z obsługą [funkcji RDMA](../../sizes-hpc.md#rdma-capable-instances)z obsługą funkcji SR-IOV odpowiednie są obrazy maszyn wirtualnych [CentOS-HPC](configure.md#centos-hpc-vm-images) w wersji 7.6 lub nowszej. Te obrazy maszyn wirtualnych są zoptymalizowane i wstępnie załadowane ze sterownikami OFED dla RDMA i różnymi powszechnie używanymi bibliotekami MPI oraz pakietami obliczeniowymi naukowymi i są najprostszym sposobem rozpoczęcia pracy.

Chociaż przykłady w tym miejscu są dla RHEL/CentOS, ale kroki są ogólne i mogą być używane dla dowolnego zgodnego systemu operacyjnego Linux, takiego jak Ubuntu (16.04, 18.04 19.04, 20.04) i SLES (12 SP4 i 15). Więcej przykładów konfigurowania innych implementacji MPI dla innych dystrybucji znajduje się w [repocie apc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

> [!NOTE]
> Uruchamianie zadań MPI na maszynach wirtualnych z obsługą funkcji SR-IOV z niektórymi bibliotekami MPI (takimi jak MPI platformy) może wymagać skonfigurowania kluczy partycji (kluczy P) w dzierżawie w celu zachowania izolacji i bezpieczeństwa. Postępuj zgodnie z instrukcjami w sekcji [Odnajdywanie](#discover-partition-keys) kluczy partycji, aby uzyskać szczegółowe informacje na temat określania wartości klucza p i poprawnego ustawiania ich dla zadania MPI przy użyciu tej biblioteki MPI.

> [!NOTE]
> Poniższe fragmenty kodu są przykładami. Zalecamy używanie najnowszych stabilnych wersji pakietów lub odwoływanie się do [repo azhpc-images](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh).

## <a name="choosing-mpi-library"></a>Wybieranie biblioteki MPI
Jeśli aplikacja HPC zaleca określoną bibliotekę MPI, najpierw wypróbuj tę wersję. Jeśli masz elastyczność w zakresie tego, który mpI możesz wybrać, i chcesz uzyskać najlepszą wydajność, wypróbuj HPC-X. Ogólnie rzecz ujmuje się, że interfejs MPI HPC-X działa najlepiej przy użyciu struktury UCX dla interfejsu InfiniBand i wykorzystuje wszystkie możliwości sprzętowe i programowe Mellanox InfiniBand. Ponadto rozwiązania HPC-X i OpenMPI są zgodne z ABI, dzięki czemu można dynamicznie uruchamiać aplikację HPC z hpc-X sbudowaną za pomocą openMPI. Podobnie rozwiązania Intel MPI, MVAPICH i MPICH są zgodne z ABI.

Na poniższej ilustracji przedstawiono architekturę popularnych bibliotek MPI.

![Architektura popularnych bibliotek MPI](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[Unified Communication X (UCX)](https://github.com/openucx/ucx) to framework interfejsów API komunikacji dla HPC. Jest zoptymalizowany pod kątem komunikacji MPI za pośrednictwem infiniBand i działa z wieloma implementacjami MPI, takimi jak OpenMPI i MPICH.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> Ostatnie kompilacje UCX rozwiązały problem, który sprawiał, że w obecności wielu interfejsów kart sieciowych wybierany jest właściwy interfejs InfiniBand. [](https://github.com/openucx/ucx/pull/5965) Więcej szczegółów [na temat uruchamiania](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2) mpi za pośrednictwem infiniBand po włączeniu przyspieszonej sieci na maszynie wirtualnej.

## <a name="hpc-x"></a>HPC-X

Zestaw [narzędzi oprogramowania HPC-X zawiera](https://www.mellanox.com/products/hpc-x-toolkit) narzędzia UCX i HCOLL, które można budowaną pod względem technologii UCX.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
get --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

Poniższe polecenie ilustruje kilka zalecanych argumentów mpirun dla HPC-X i OpenMPI.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
gdzie:

|Parametr|Opis                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |Określa liczbę procesów MPI. Na przykład: `-n 16`.|
|`$HOSTFILE`|Określa plik zawierający nazwę hosta lub adres IP, aby wskazać lokalizację, w której będą uruchamiane procesy MPI. Na przykład: `--hostfile hosts`.|
|`$NUMBER_PROCESSES_PER_NUMA`   |Określa liczbę procesów MPI, które będą uruchamiane w każdej domenie NUMA. Aby na przykład określić cztery procesy MPI dla procesów NUMA, należy `--map-by ppr:4:numa:pe=1` użyć .|
|`$NUMBER_THREADS_PER_PROCESS`  |Określa liczbę wątków na proces MPI. Aby na przykład określić jeden proces MPI i cztery wątki na numa, należy `--map-by ppr:1:numa:pe=4` użyć .|
|`-report-bindings` |Drukuje mapowanie procesów MPI na rdzenie, co jest przydatne do sprawdzenia, czy przypinanie procesu MPI jest poprawne.|
|`$MPI_EXECUTABLE`  |Określa plik wykonywalny MPI wbudowane łączenie w bibliotekach MPI. Otoki kompilatora MPI robią to automatycznie. Na przykład: `mpicc` lub `mpif90` .|

Przykład uruchamiania mikrobenchmarka opóźnienia OSU jest następujący:
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>Optymalizacja zbiorów MPI

Wspólne typy pierwotne komunikacji MPI oferują elastyczny, przenośny sposób implementowania operacji komunikacji grupowej. Są one powszechnie używane w różnych równoległych aplikacjach naukowych i mają znaczący wpływ na ogólną wydajność aplikacji. Aby uzyskać szczegółowe informacje na temat parametrów konfiguracji w celu zoptymalizowania wydajności komunikacji zbiorczej przy użyciu bibliotek HPC-X i HCOLL do komunikacji zbiorczej, zapoznaj się z artykułem TechCo w witrynie [TechCo w](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740) celu zoptymalizowania wydajności komunikacji zbiorczej.

Jeśli na przykład podejrzewasz, że ściśle sprzężona aplikacja MPI robi nadmierną ilość komunikacji zbiorczej, możesz spróbować w celu włączenia kolektywów hierarchicznych (HCOLL). Aby włączyć te funkcje, użyj następujących parametrów.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> W przypadku wersji HPC-X 2.7.4 lub nowsza może być konieczne jawne LD_LIBRARY_PATH, jeśli wersja UCX na mofed różni się od tej w HPC-X.

## <a name="openmpi"></a>OpenMPI

Zainstaluj UCX zgodnie z powyższym opisem. HcOLL jest częścią zestawu [narzędzi oprogramowania HPC-X](https://www.mellanox.com/products/hpc-x-toolkit) i nie wymaga specjalnej instalacji.

OpenMPI można zainstalować z pakietów dostępnych w tym repo.

```bash
sudo yum install –y openmpi
```

Zalecamy tworzenie najnowszej, stabilnej wersji openmpi z UCX.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

Aby uzyskać optymalną wydajność, uruchom openMPI z i `ucx` `hcoll` . Zobacz również przykład z [HPC-X.](#hpc-x)

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

Sprawdź klucz partycji, jak wspomniano powyżej.

## <a name="intel-mpi"></a>Intel MPI

Pobierz do wyboru wersję rozwiązania [Intel MPI.](https://software.intel.com/mpi-library/choose-download) Wersja Intel MPI 2019 przesłoniła platformę Open Fabrics Alliance (OFA) na platformę Open Fabrics Interfaces (OFI) i obecnie obsługuje bibliotekę libfabric. Istnieją dwaj dostawcy obsługi infiniBand: mlx i czasowniki.
Zmień I_MPI_FABRICS środowiskową w zależności od wersji.
- Intel MPI 2019 i 2021: użyj `I_MPI_FABRICS=shm:ofi` , `I_MPI_OFI_PROVIDER=mlx` . Dostawca `mlx` używa UCX. Użycie czasowników okazało się niestabilne i mniej performantne. Aby uzyskać więcej [informacji, zobacz artykuł TechCo nie](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149) tylko.
- Intel MPI 2018: użyj `I_MPI_FABRICS=shm:ofa`
- Intel MPI 2016: użyj `I_MPI_DAPL_PROVIDER=ofa-v2-ib0`

Oto kilka sugerowanych argumentów mpirun dla intel MPI 2019 update 5+.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
gdzie:

|Parametr|Opis                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |Określa, który dostawca biblioteki ma być używany, co będzie miało wpływ na interfejs API, protokół i używaną sieć. Czasowniki to kolejna opcja, ale ogólnie rzecz biorąc, mlx zapewnia lepszą wydajność.|
|`I_MPI_DEBUG`|Określa poziom dodatkowych danych wyjściowych debugowania, które mogą dostarczać szczegółowe informacje o tym, gdzie są przypięte procesy oraz który protokół i sieć są używane.|
|`I_MPI_PIN_DOMAIN` |Określa sposób przypinania procesów. Na przykład można przypiąć do rdzeni, gniazd lub domen NUMA. W tym przykładzie ustawisz tę zmienną środowiskową na wartość numa, co oznacza, że procesy zostaną przypięte do domen węzłów NUMA.|

### <a name="optimizing-mpi-collectives"></a>Optymalizacja zbiorów MPI

Istnieją inne opcje, które można wypróbować, zwłaszcza jeśli operacje zbiorcze zużywają znaczną ilość czasu. Intel MPI 2019 Update 5+ obsługuje platformę mlx i używa platformy UCX do komunikowania się z infiniBand. Obsługuje również rozwiązanie HCOLL.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>Maszyny wirtualne inne niż SR-IOV

W przypadku maszyn wirtualnych innych niż SR-IOV przykład pobierania bezpłatnej wersji ewaluacyjnej środowiska uruchomieniowego 5.x [jest](https://registrationcenter.intel.com/en/forms/?productid=1740) następujący:
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
Kroki instalacji można znaleźć w [temacie Intel MPI Library Installation Guide (Przewodnik instalacji biblioteki Intel MPI).](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)
Opcjonalnie można włączyć funkcję ptrace dla procesów innych niż główne procesy nie debugera (wymagane w przypadku najnowszych wersji intel MPI).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
W przypadku wersji obrazu maszyny wirtualnej w wersji SUSE Linux Enterprise Server — SLES 12 SP3 dla HPC, SLES 12 SP3 dla HPC (Premium), SLES 12 SP1 dla HPC, SLES 12 SP1 dla HPC (Premium), SLES 12 SP4 i SLES 15, sterowniki RDMA i pakiety MPI firmy Intel są dystrybuowane na maszynie wirtualnej. Zainstaluj pakiet Intel MPI, uruchamiając następujące polecenie:
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

Poniżej przedstawiono przykład tworzenia aplikacji MVAPICH2. Pamiętaj, że mogą być dostępne nowsze wersje niż te, które są używane poniżej.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

Przykład uruchamiania mikrobenchmarka opóźnienia OSU jest następujący:
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

Na poniższej liście przedstawiono kilka zalecanych `mpirun` argumentów.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
gdzie:

|Parametr|Opis                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |Określa, których zasad powiązań użyć, co będzie miało wpływ na sposób przypinania procesów do podstawowych identyfikatorów. W tym przypadku należy określić wykres punktowy, dzięki czemu procesy będą równomiernie rozpraszane między domenami NUMA.|
|`MV2_CPU_BINDING_LEVEL`|Określa miejsce przypinania procesów. W tym przypadku należy ustawić ją na numanode, co oznacza, że procesy są przypinane do jednostek domen NUMA.|
|`MV2_SHOW_CPU_BINDING` |Określa, czy chcesz uzyskać informacje debugowania o tym, gdzie procesy są przypięte.|
|`MV2_SHOW_HCA_BINDING` |Określa, czy chcesz uzyskać informacje debugowania o tym, której karty kanału hosta używa każdy proces.|

## <a name="platform-mpi"></a>MpI platformy

Zainstaluj wymagane pakiety dla platformy MPI Community Edition.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

Postępuj zgodnie z procesem instalacji.

Poniższe polecenia to przykłady uruchamiania poleceń pingpong i allreduce mpi przy użyciu platformy MPI na maszynach wirtualnych HBv3 przy użyciu obrazów maszyn wirtualnych CentOS-HPC 7.6, 7.8 i 8.1.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

Zainstaluj UCX zgodnie z powyższym opisem. Skompilowanie pliku MPICH.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

Uruchamianie mpich.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

Sprawdź klucz partycji, jak wspomniano powyżej.

## <a name="osu-mpi-benchmarks"></a>Testy porównawcze OSU MPI

Pobierz [testy porównawcze OSU MPI](http://mvapich.cse.ohio-state.edu/benchmarks/) i untar.

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

Testy porównawcze MPI znajdują się `mpi/` w folderze .


## <a name="discover-partition-keys"></a>Odnajdywanie kluczy partycji

Odnajdowanie kluczy partycji (kluczy P) do komunikacji z innymi maszynami wirtualnymi w ramach tej samej dzierżawy (zestawem dostępności lub zestawem skalowania maszyn wirtualnych).

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

Większa z nich to klucz dzierżawy, który powinien być używany z mpi. Przykład: jeśli poniżej znajdują się klucze p, 0x800b należy używać z mpi.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

Użyj partycji innej niż domyślny (0x7fff) klucza partycji. UCX wymaga wyczyszczenia klucza MSB klucza P. Na przykład ustaw UCX_IB_PKEY jako 0x000b dla 0x800b.

Należy również pamiętać, że dopóki istnieje dzierżawa (zestaw dostępności lub zestaw skalowania maszyn wirtualnych), pkeys pozostają bez zmian. Dzieje się tak nawet wtedy, gdy węzły są dodawane/usuwane. Nowi dzierżawcy zyskują różne pkey.


## <a name="set-up-user-limits-for-mpi"></a>Konfigurowanie limitów użytkowników dla interfejsu MPI

Skonfiguruj limity użytkowników dla mpi.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>Konfigurowanie kluczy SSH dla mpi

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

W powyższej składni przyjęto założenie, że jest udostępniony katalog macierzysty, a w innym przypadku katalog SSH musi zostać skopiowany do każdego węzła.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat maszyn wirtualnych serii [H](../../sizes-hpc.md) i [N](../../sizes-gpu.md) z włączoną obsługą technologii [InfiniBand](../../sizes-hpc.md#rdma-capable-instances)
- Zapoznaj się [z omówieniem serii HBv3](hbv3-series-overview.md) i [omówieniem serii HC.](hc-series-overview.md)
- Aby zapoznać się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności, zobacz blogi społeczności technicznej Azure Compute [Tech Community.](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)
- Aby uzyskać wyższego poziomu widok architektury uruchamiania obciążeń HPC, zobacz Obliczenia o wysokiej wydajności [(HPC) na platformie Azure.](/azure/architecture/topics/high-performance-computing/)
