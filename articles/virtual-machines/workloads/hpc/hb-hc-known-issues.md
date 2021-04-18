---
title: Rozwiązywanie znanych problemów z maszynami wirtualnych HPC i GPU — Azure Virtual Machines | Microsoft Docs
description: Dowiedz się więcej na temat rozwiązywania znanych problemów z rozmiarami maszyn wirtualnych HPC i GPU na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f5bdae17126048da153f70bf27609bcc4b92fe21
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599591"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Znane problemy z maszynami wirtualnymi z serii H i N

W tym artykule próbujemy wyświetlić listę najnowszych typowych problemów [](../../sizes-gpu.md) i ich rozwiązań podczas korzystania z maszyn wirtualnych HPC i GPU serii [H](../../sizes-hpc.md) i N.

## <a name="qp0-access-restriction"></a>Ograniczenie dostępu qp0

Aby zapobiec dostępowi sprzętowemu niskiego poziomu, który może spowodować luki w zabezpieczeniach, para kolejek 0 nie jest dostępna dla maszyn wirtualnych gościa. Powinno to mieć wpływ tylko na akcje zwykle skojarzone z administrowaniem kartą sieciową ConnectX InfiniBand i uruchamianiem niektórych diagnostyki InfiniBand, takich jak ibdiagnet, ale nie aplikacji użytkownika końcowego.

## <a name="mofed-installation-on-ubuntu"></a>Instalacja mofed w systemie Ubuntu
W przypadku obrazów maszyn wirtualnych z witryny Marketplace opartych na systemie Ubuntu-18.04 z wersją jądra i nowszą niektóre starsze pliki Mellanox OFED są niezgodne, co w niektórych przypadkach powoduje wzrost czasu rozruchu maszyny wirtualnej do `5.4.0-1039-azure #42` 30 minut. Zgłoszono to zarówno dla wersji Mellanox OFED 5.2-1.0.4.0, jak i 5.2-2.2.0.0. Problem został rozwiązany za pomocą mellanox OFED 5.3-1.0.0.1.
Jeśli konieczne jest użycie niezgodnej wersji OFED, rozwiązaniem jest użycie obrazu maszyny wirtualnej **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** z witryny Marketplace lub starszego i nie zaktualizowanie jądra.

## <a name="mpi-qp-creation-errors"></a>Błędy tworzenia mpi QP
Jeśli podczas uruchamiania obciążeń MPI są zgłaszane błędy tworzenia infiniBand QP, jak pokazano poniżej, sugerujemy ponowne uruchomienie maszyny wirtualnej i ponowną próbę uruchomienia obciążenia. Ten problem zostanie rozwiązany w przyszłości.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

Możesz sprawdzić wartości maksymalnej liczby par kolejek, gdy problem zostanie zaobserwowany w następujący sposób.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>Przyspieszona sieć na hb, HC, HBv2 i NDv2

[Usługa Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) jest teraz dostępna w przypadku maszyn wirtualnych z obsługą rdMA i InfiniBand oraz rozmiarów maszyn wirtualnych z obsługą funkcji SR-IOV [HB,](../../hb-series.md) [HC,](../../hc-series.md) [HBv2](../../hbv2-series.md) [i NDv2.](../../ndv2-series.md) Ta funkcja umożliwia teraz rozbudowę (do 30 Gb/s) i opóźnienia w sieci Azure Ethernet. Chociaż jest to niezależne od funkcji RDMA za pośrednictwem sieci InfiniBand, niektóre zmiany platformy dla tej funkcji mogą mieć wpływ na zachowanie niektórych implementacji MPI podczas uruchamiania zadań za pośrednictwem infiniBand. W szczególności interfejs InfiniBand na niektórych maszynach wirtualnych może mieć nieco inną nazwę (mlx5_1 w przeciwieństwie do wcześniejszego interfejsu mlx5_0), co może wymagać dostrojenia wierszy poleceń MPI, szczególnie w przypadku korzystania z interfejsu UCX (często z openMPI i HPC-X). Najprostszym rozwiązaniem obecnie może być użycie najnowszego zestawu HPC-X na obrazach maszyn wirtualnych CentOS-HPC lub wyłączenie przyspieszonej sieci, jeśli nie jest to wymagane.
Więcej szczegółowych informacji na ten temat można znaleźć w tym [artykule TechCo w artykule Dotyczącym skojarzeń](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965) z instrukcjami dotyczącymi sposobu rozwiązania wszelkich zaobserwowanych problemów.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>Instalacja sterownika InfiniBand na maszynach wirtualnych innych niż SR-IOV

Obecnie H16r, H16mr i NC24r nie są włączone SR-IOV. Szczegółowe informacje na temat ujednoznania stosu InfiniBand można [znaleźć tutaj.](../../sizes-hpc.md#rdma-capable-instances)
InfiniBand można skonfigurować dla rozmiarów maszyn wirtualnych z włączoną obsługą funkcji SR-IOV za pomocą sterowników OFED, natomiast rozmiary maszyn wirtualnych inne niż SR-IOV wymagają sterowników ND. Ta obsługa IB jest dostępna odpowiednio dla [centOS, RHEL i Ubuntu.](configure.md)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplikowanie adresów MAC za pomocą cloud-init z systemem Ubuntu na maszynach wirtualnych serii H i N

Istnieje znany problem z plikiem cloud-init na obrazach maszyn wirtualnych z systemem Ubuntu, gdy próbuje on uruchomić interfejs IB. Może się to zdarzyć podczas ponownego uruchamiania maszyny wirtualnej lub próby utworzenia obrazu maszyny wirtualnej po uogólnieniu. Dzienniki rozruchu maszyny wirtualnej mogą zawierać błąd podobny do tego:
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

Ten "zduplikowany komputer MAC z cloud-init w systemie Ubuntu" jest znanym problemem. Zostanie to rozwiązane w przypadku nowszego jądra. Jeśli problem zostanie napotkany, obejście jest następujące:
1) Wdrażanie obrazu maszyny wirtualnej z witryny Marketplace (Ubuntu 18.04)
2) Zainstaluj niezbędne pakiety oprogramowania, aby włączyć funkcję IB[(instrukcja tutaj)](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)
3) Edytuj waagent.conf, aby zmienić wartość EnableRDMA=y
4) Wyłączanie sieci w chmurze init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Edytuj plik konfiguracji sieci netplan wygenerowany przez cloud-init, aby usunąć komputer MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>Pamięć DRAM na maszyn wirtualnych z serii HB

Obecnie maszyny wirtualne z serii HB mogą uwidocznić tylko 228 GB pamięci RAM dla maszyn wirtualnych gościa. Podobnie 458 GB na HBv2 i 448 GB na maszyny wirtualne HBv3. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, które uniemożliwia przypisywanie stron do lokalnego pamięci DRAM (domen NUMA) firmy AMD (domen NUMA) zarezerwowanych dla maszyny wirtualnej gościa.

## <a name="gss-proxy"></a>GSS Proxy

Serwer proxy GSS ma znaną usterkę w systemie CentOS/RHEL 7.5, która w przypadku korzystania z systemu plików NFS może oznaczać znaczącą wydajność i szybkość reakcji. Można temu pomóc:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Czyszczenie pamięci podręcznej

W systemach HPC często warto wyczyścić pamięć po zakończeniu zadania, zanim następny użytkownik zostanie przypisany do tego samego węzła. Po uruchomieniu aplikacji w systemie Linux może się okazać, że ilość dostępnej pamięci zmniejsza się w czasie, gdy pamięć buforu zwiększa się, mimo że nie są uruchomione żadne aplikacje.

![Zrzut ekranu przedstawiający wiersz polecenia przed czyszczeniem](./media/known-issues/cache-cleaning-1.png)

Użycie funkcji spowoduje pokazanie, za pomocą których `numactl -H` NUMAnode (prawdopodobnie wszystkich) pamięć jest buforowana. W systemie Linux użytkownicy mogą czyścić pamięci podręczne na trzy sposoby, aby przywrócić buforowane lub buforowane pamięci do "wolnej". Musisz mieć uprawnienia root lub sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Zrzut ekranu przedstawiający wiersz polecenia po wyczyszczeniu](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Ostrzeżenia jądra

Podczas rozruchu maszyny wirtualnej z serii HB w systemie Linux można zignorować następujące komunikaty ostrzegawcze jądra. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, które zostanie rozwiązane w czasie.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się [z omówieniem serii HB](hb-series-overview.md) i [omówieniem serii HC,](hc-series-overview.md) aby dowiedzieć się więcej na temat optymalnego konfigurowania obciążeń pod kątem wydajności i skalowalności.
- Zapoznaj się z najnowszymi ogłoszeniami, przykładami obciążeń HPC i wynikami wydajności na Azure Compute [Tech Community Blogi](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby uzyskać wyższego poziomu widok architektury uruchamiania obciążeń HPC, zobacz Obliczenia o wysokiej wydajności [(HPC) na platformie Azure.](/azure/architecture/topics/high-performance-computing/)
