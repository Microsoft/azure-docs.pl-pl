---
title: Rozwiązywanie znanych problemów z maszynami wirtualnymi HPC i GPU — Azure Virtual Machines | Microsoft Docs
description: Informacje o rozwiązywaniu znanych problemów z rozmiarami maszyn wirtualnych HPC i procesora GPU na platformie Azure.
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 10/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a96042045eaab440d099f96782c020067eedfa18
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92282118"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>Znane problemy z maszynami wirtualnymi z serii H i N

W tym artykule przedstawiono najczęstsze [problemy i rozwiązania](../../sizes-gpu.md) dotyczące korzystania z [serii H](../../sizes-hpc.md) i maszyn wirtualnych procesora GPU.

## <a name="infiniband-driver-installation-on-n-series-vms"></a>Instalacja sterownika InfiniBand na maszynach wirtualnych z serii N

NC24r_v3 i ND40r_v2 są włączone SR-IOV, gdy NC24r i NC24r_v2 nie obsługują funkcji SR-IOV. Niektóre szczegóły [dotyczące bifurcation.](../../sizes-hpc.md#rdma-capable-instances)
InfiniBand (IB) można skonfigurować na rozmiarach maszyn wirtualnych z włączoną funkcją SR-IOV przy użyciu sterowników OFED, natomiast rozmiary maszyn wirtualnych bez wirtualizacji SR-IOV wymagają sterowników ND. Ta pomoc techniczna jest dostępna odpowiednio w [CentOS-HPC VMIs](configure.md). W przypadku usługi Ubuntu zapoznaj się z [instrukcjami](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) dotyczącymi instalowania zarówno sterowników OFED, jak i ND, [zgodnie z opisem w dokumentacji.](enable-infiniband.md#vm-images-with-infiniband-drivers)

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>Duplikowanie komputerów MAC z usługą Cloud-init z Ubuntu na maszynach wirtualnych serii H i N

Istnieje znany problem z obsługą funkcji Cloud-init w obrazach maszyn wirtualnych Ubuntu, ponieważ próbuje on wywołać interfejs IB. Może to być spowodowane ponownym uruchomieniem maszyny wirtualnej lub przy próbie utworzenia obrazu maszyny wirtualnej po uogólnieniu. Dzienniki rozruchowe maszyny wirtualnej mogą zawierać błąd podobny do tego: "Uruchamianie usługi sieciowej... RuntimeError: znaleziono zduplikowany adres MAC. zarówno "eth1", jak i "ib0" mają komputery Mac ".

Jest to znany problem z tym "zduplikowanym komputerem MAC z chmurą Ubuntu". Obejście to:
1) Wdrażanie obrazu maszyny wirtualnej z maszyną wirtualną (Ubuntu 18,04)
2) Zainstaluj wymagane pakiety oprogramowania, aby włączyć system IB ([instrukcje tutaj](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) Edytuj waagent. conf, aby zmienić EnableRDMA = y
4) Wyłączanie sieci w chmurze — init
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Edytuj plik konfiguracji sieciowej planu sieciowego wygenerowany przez funkcję Cloud-init, aby usunąć komputer MAC
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series"></a>Pamięci DRAM w serii HB

Maszyny wirtualne z serii HB mogą teraz uwidocznić 228 GB pamięci RAM na maszynach wirtualnych gościa. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, aby zapobiec przypisaniu stron do lokalnej pamięci DRAM w systemie AMD CCX (domeny NUMA) zarezerwowanych dla maszyny wirtualnej gościa.

## <a name="accelerated-networking"></a>Accelerated Networking

W tej chwili nie włączono usługi Azure przyspieszonej sieci na platformie HPC z włączonymi PROCESORAmi i maszynami wirtualnymi. Klienci będą powiadamiani o tym, gdy ta funkcja jest obsługiwana.

## <a name="qp0-access-restriction"></a>qp0 ograniczenia dostępu

Aby zapobiec dostępowi do sprzętu niskiego poziomu, który może spowodować powstanie luk w zabezpieczeniach, para kolejki 0 nie jest dostępna dla maszyn wirtualnych gościa. Powinno to mieć wpływ tylko na akcje zwykle skojarzone z administracją karty sieciowej ConnectX-5 i uruchamianie niektórych diagnostyki InfiniBand, takich jak ibdiagnet, ale nie samych aplikacji użytkowników końcowych.

## <a name="gss-proxy"></a>Serwer proxy GSS

Serwer proxy GSS ma znaną usterkę w CentOS/RHEL 7,5, która może zależeć od znacznej wydajności i szybkości reakcji w przypadku korzystania z systemu plików NFS. Można to ograniczyć przy użyciu:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Czyszczenie pamięci podręcznej

W systemach HPC często warto oczyścić pamięć po zakończeniu zadania przed przypisaniem kolejnego użytkownika do tego samego węzła. Po uruchomieniu aplikacji w systemie Linux może się okazać, że dostępna pamięć zmniejszy się, gdy pamięć buforu rośnie, mimo że nie są uruchomione żadne aplikacje.

![Zrzut ekranu przedstawiający wiersz polecenia przed czyszczeniem](./media/known-issues/cache-cleaning-1.png)

Przy użyciu programu `numactl -H` zostaną wyświetlone, które NUMAnode pamięci są buforowane (ewentualnie wszystkie). W systemie Linux użytkownicy mogą czyścić pamięć podręczną w trzech sposobach, aby przywrócić pamięć buforowaną lub buforowaną w pamięci podręcznej. Musisz być elementem głównym lub mieć uprawnienia sudo.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Zrzut ekranu przedstawiający wiersz polecenia po czyszczeniu](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Ostrzeżenia jądra

Podczas uruchamiania maszyny wirtualnej z serii HB w systemie Linux można zignorować następujące komunikaty ostrzegawcze jądra. Jest to spowodowane znanym ograniczeniem funkcji hypervisor platformy Azure, które będzie dotyczyło w miarę upływu czasu.

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

- Zapoznaj się z [omówieniem HB-Series](hb-series-overview.md) i [omówieniem z serii HC](hc-series-overview.md) , aby dowiedzieć się więcej o optymalnym konfigurowaniu obciążeń dotyczących wydajności i skalowalności.
- Przeczytaj o najnowszych anonsach i niektórych przykładach HPC oraz wyniki na [blogach społecznościowych usługi Azure COMPUTE](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Aby zapoznać się z ogólnym widokiem architektury uruchamiania obciążeń HPC, zobacz [wysoka wydajność obliczeń (HPC) na platformie Azure](/azure/architecture/topics/high-performance-computing/).
