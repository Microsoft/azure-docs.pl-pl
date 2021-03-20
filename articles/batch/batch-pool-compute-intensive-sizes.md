---
title: Używanie maszyn wirtualnych platformy Azure intensywnie korzystających z obliczeń przy użyciu usługi Batch
description: Jak korzystać z systemu HPC i rozmiaru maszyny wirtualnej procesora GPU w pulach Azure Batch. Dowiedz się więcej o zależnościach systemu operacyjnego i zobacz kilka przykładów scenariusza.
ms.topic: how-to
ms.date: 12/17/2018
ms.openlocfilehash: 016da7669c9e6a6586a53d379f9665c9ea048b64
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147338"
---
# <a name="use-rdma-or-gpu-instances-in-batch-pools"></a>Używanie wystąpień RDMA lub GPU w pulach wsadowym

Do uruchamiania pewnych zadań wsadowych można korzystać z rozmiarów maszyn wirtualnych platformy Azure przeznaczonych do obliczeń na dużą skalę. Na przykład:

* Aby uruchomić wiele wystąpień [obciążeń MPI](batch-mpi.md), wybierz serię H lub inne rozmiary, które mają interfejs sieciowy do zdalnego bezpośredniego dostępu do pamięci (RDMA). Rozmiary te łączą się z siecią InfiniBand w celu komunikacji między węzłami, co umożliwia przyspieszenie aplikacji MPI. 

* W przypadku aplikacji CUDA Wybierz rozmiary serii N, które zawierają karty GPU (NVIDIA Tesla Graphics Processing Unit).

Ten artykuł zawiera wskazówki i przykłady dotyczące korzystania z niektórych specjalistycznych rozmiarów platformy Azure w pulach usługi Batch. Aby uzyskać specyfikacje i tło, zobacz:

* Rozmiary maszyn wirtualnych obliczeniowych o wysokiej wydajności ([Linux](../virtual-machines/sizes-hpc.md), [Windows](../virtual-machines/sizes-hpc.md)) 

* Rozmiary maszyn wirtualnych z obsługą procesora GPU ([Linux](../virtual-machines/sizes-gpu.md), [Windows](../virtual-machines/sizes-gpu.md)) 

> [!NOTE]
> Niektóre rozmiary maszyn wirtualnych mogą nie być dostępne w regionach, w których tworzysz konta w usłudze Batch. Aby sprawdzić, czy rozmiar jest dostępny, zobacz [dostępne produkty według regionów](https://azure.microsoft.com/regions/services/) i [Wybierz rozmiar maszyny wirtualnej dla puli usługi Batch](batch-pool-vm-sizes.md).

## <a name="dependencies"></a>Zależności

Możliwości RDMA lub GPU o rozmiarach intensywnie korzystających z obliczeń w usłudze Batch są obsługiwane tylko w niektórych systemach operacyjnych. (Lista obsługiwanych systemów operacyjnych jest podzbiorem obsługiwanych maszyn wirtualnych tworzonych w tych rozmiarach). W zależności od sposobu tworzenia puli partii może być konieczne zainstalowanie lub skonfigurowanie dodatkowego sterownika lub innego oprogramowania w węzłach. Poniższe tabele podsumowują te zależności. Aby uzyskać szczegółowe informacje, zobacz połączone artykuły. Aby uzyskać opcje konfigurowania pul usługi Batch, zobacz w dalszej części tego artykułu.

### <a name="linux-pools---virtual-machine-configuration"></a>Pule systemu Linux — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwość | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | -------- | ----- |  -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/linux/n-series-driver-setup.md#rdma-network-connectivity) | Dostęp RDMA | Ubuntu 16,04 LTS, lub<br/>CentOS HPC<br/>(Azure Marketplace) | Intel MPI 5<br/><br/>Sterowniki RDMA systemu Linux | Włącz komunikację między węzłami, wyłącz współbieżne wykonywanie zadań |
| [NC, NCv2, Seria NCV3, Seria NDv2](../virtual-machines/linux/n-series-driver-setup.md) | Procesor GPU NVIDIA Tesla (w zależności od serii) | Ubuntu 16,04 LTS, lub<br/>CentOS 7,3 lub 7,4<br/>(Azure Marketplace) | Sterowniki NVIDIA CUDA lub CUDA toolkit | Nie dotyczy | 
| [NV, Seria NVv2](../virtual-machines/linux/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Ubuntu 16,04 LTS, lub<br/>CentOS 7,3<br/>(Azure Marketplace) | Sterowniki sieci NVIDIA | Nie dotyczy |

<sup>*</sup>Rozmiary serii N obsługujące RDMA również obejmują procesory GPU Tesla NVIDIA

### <a name="windows-pools---virtual-machine-configuration"></a>Pule systemu Windows — konfiguracja maszyny wirtualnej

| Rozmiar | Możliwość | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------ | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md)<br/>[NC24r, NC24rs_v2, NC24rs_v3, ND24rs<sup>*</sup>](../virtual-machines/windows/n-series-driver-setup.md#rdma-network-connectivity) | Dostęp RDMA | Windows Server 2016, 2012 R2 lub<br/>2012 (Azure Marketplace) | Microsoft MPI 2012 R2 lub nowszy lub<br/> Intel MPI 5<br/><br/>Sterowniki RDMA systemu Windows | Włącz komunikację między węzłami, wyłącz współbieżne wykonywanie zadań |
| [NC, NCv2, Seria NCV3, ND, Seria NDv2](../virtual-machines/windows/n-series-driver-setup.md) | Procesor GPU NVIDIA Tesla (w zależności od serii) | Windows Server 2016 lub <br/>2012 R2 (Azure Marketplace) | Sterowniki NVIDIA CUDA lub CUDA toolkit| Nie dotyczy | 
| [NV, Seria NVv2](../virtual-machines/windows/n-series-driver-setup.md) | NVIDIA Tesla M60 GPU | Windows Server 2016 lub<br/>2012 R2 (Azure Marketplace) | Sterowniki sieci NVIDIA | Nie dotyczy |

<sup>*</sup>Rozmiary serii N obsługujące RDMA również obejmują procesory GPU Tesla NVIDIA

### <a name="windows-pools---cloud-services-configuration"></a>Pule systemu Windows — Konfiguracja usług Cloud Services

> [!NOTE]
> Rozmiary serii N nie są obsługiwane w pulach usługi Batch z konfiguracją usług w chmurze.
>

| Rozmiar | Możliwość | Systemy operacyjne | Wymagane oprogramowanie | Ustawienia puli |
| -------- | ------- | -------- | -------- | ----- |
| [H16r, H16mr, A8, A9](../virtual-machines/sizes-hpc.md) | Dostęp RDMA | Windows Server 2016, 2012 R2, 2012 lub<br/>2008 R2 (Rodzina systemów operacyjnych gościa) | Microsoft MPI 2012 R2 lub nowszy lub<br/>Intel MPI 5<br/><br/>Sterowniki RDMA systemu Windows | Włącz komunikację między węzłami,<br/> Wyłącz współbieżne wykonywanie zadań |

## <a name="pool-configuration-options"></a>Opcje konfiguracji puli

Aby skonfigurować wyspecjalizowany rozmiar maszyny wirtualnej dla puli usługi Batch, istnieje kilka opcji instalacji wymaganych programów lub sterowników:

* W przypadku pul w konfiguracji maszyny wirtualnej wybierz wstępnie skonfigurowany obraz maszyny wirtualnej w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) , który ma zainstalowane sterowniki i oprogramowanie. Przykłady: 

  * [CentOS 7,4 HPC](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) — obejmuje sterowniki RDMA i Intel MPI 5,1

  * [Data Science Virtual Machine](../machine-learning/data-science-virtual-machine/overview.md) dla systemu Linux lub Windows — obejmuje sterowniki NVIDIA CUDA

  * Obrazy systemu Linux dla obciążeń kontenera usługi Batch, które obejmują również sterowniki procesora GPU i RDMA:

    * [CentOS (ze sterownikami procesora GPU i RDMA) dla Azure Batch pul kontenerów](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.centos-container-rdma?tab=Overview)

    * [Serwer Ubuntu (ze sterownikami procesora GPU i RDMA) dla puli kontenerów Azure Batch](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-batch.ubuntu-server-container-rdma?tab=Overview)

* Utwórz [niestandardowy obraz maszyny wirtualnej z systemem Windows lub Linux](batch-sig-images.md) , na którym zainstalowano sterowniki, oprogramowanie lub inne ustawienia wymagane dla rozmiaru maszyny wirtualnej. 

* Utwórz [pakiet aplikacji](batch-application-packages.md) usługi Batch na podstawie skompresowanego sterownika lub Instalatora aplikacji, a następnie skonfiguruj usługę Batch do wdrożenia pakietu w węzłach puli i zainstaluj raz podczas tworzenia każdego węzła. Na przykład jeśli pakiet aplikacji jest instalatorem, Utwórz wiersz polecenia [Uruchom zadanie](jobs-and-tasks.md#start-task) , aby zainstalować aplikację w trybie dyskretnym. Rozważ użycie pakietu aplikacji i zadania uruchamiania puli, jeśli obciążenie zależy od określonej wersji sterownika.

  > [!NOTE] 
  > Zadanie uruchamiania musi działać z podniesionymi uprawnieniami (administratora) i musi oczekiwać na powodzenie. Długotrwałe zadania spowodują wydłużenie czasu na zainicjowanie puli wsadowej.
  >

* [Stocznie](https://github.com/Azure/batch-shipyard) w usłudze Batch automatycznie konfiguruje sterowniki procesora GPU i RDMA, aby działały w sposób przezroczysty przy użyciu obciążeń zwirtualizowanych w Azure Batch. Stocznie w usłudze Batch są całkowicie sterowane przy użyciu plików konfiguracyjnych. Dostępnych jest wiele przykładowych konfiguracji przepisów, które umożliwiają obciążenia procesora GPU i RDMA, takie jak [przepis procesora GPU CNTK](https://github.com/Azure/batch-shipyard/tree/master/recipes/CNTK-GPU-OpenMPI) , który wstępnie konfiguruje sterowniki procesora GPU na maszynach wirtualnych serii N i ładuje Microsoft Cognitive Toolkit oprogramowanie jako obraz platformy Docker.


## <a name="example-nvidia-gpu-drivers-on-windows-nc-vm-pool"></a>Przykład: Sterowniki procesora GPU NVIDIA w puli maszyn wirtualnych systemu Windows NC

Aby uruchamiać aplikacje CUDA w puli węzłów systemu Windows NC, należy zainstalować sterowniki procesora GPU NVDIA. W poniższych przykładowych krokach użyto pakietu aplikacji do zainstalowania sterowników procesora GPU firmy NVIDIA. Możesz wybrać tę opcję, jeśli obciążenie zależy od określonej wersji sterownika procesora GPU.

1. Pobierz pakiet instalacyjny dla sterowników procesora GPU w systemie Windows Server 2016 z [witryny NVIDIA](https://www.nvidia.com/Download/index.aspx) , na przykład, [wersja 411,82](https://us.download.nvidia.com/Windows/Quadro_Certified/411.82/411.82-tesla-desktop-winserver2016-international.exe). Zapisz plik lokalnie przy użyciu krótkiej nazwy, takiej jak *GPUDriverSetup.exe*.
2. Utwórz plik zip pakietu.
3. Przekaż pakiet na konto w usłudze Batch. Aby uzyskać instrukcje, zobacz Wskazówki dotyczące [pakietów aplikacji](batch-application-packages.md) . Określ identyfikator aplikacji, taki jak *GPUDriver*, oraz wersję, taką jak *411,82*.
1. Korzystając z interfejsów API usługi Batch lub Azure Portal, Utwórz pulę w konfiguracji maszyny wirtualnej z żądaną liczbą węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia, które umożliwiają ciche Instalowanie sterowników procesora GPU firmy NVIDIA przy użyciu zadania uruchamiania:

| Ustawienie | Wartość |
| ---- | ----- | 
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | MicrosoftWindowsServer |
| **Oferta** | WindowsServer |
| **Magazyn** | 2016 — centrum danych |
| **Rozmiar węzła** | Standard NC6 |
| **Odwołania do pakietu aplikacji** | GPUDriver, wersja 411,82 |
| **Uruchamianie zadania włączone** | Prawda<br>**Wiersz polecenia** - `cmd /c "%AZ_BATCH_APP_PACKAGE_GPUDriver#411.82%\\GPUDriverSetup.exe /s"`<br/>**Tożsamość użytkownika** — autoużytkownik puli, administrator<br/>**Oczekiwanie na powodzenie** — prawda

## <a name="example-nvidia-gpu-drivers-on-a-linux-nc-vm-pool"></a>Przykład: Sterowniki procesora GPU NVIDIA w puli maszyn wirtualnych z systemem Linux NC

Aby uruchamiać aplikacje CUDA w puli węzłów systemu Linux NC, należy zainstalować niezbędne sterowniki procesora GPU NVIDIA Tesla z poziomu zestawu narzędzi CUDA. Poniższe przykładowe kroki tworzą i wdrażają niestandardowy obraz Ubuntu 16,04 LTS z użyciem sterowników procesora GPU:

1. Wdróż maszynę wirtualną z serii NC platformy Azure z systemem Ubuntu 16,04 LTS. Na przykład utwórz maszynę wirtualną w regionie Południowo-środkowe stany USA. 
2. Dodaj [rozszerzenie sterowników procesora GPU NVIDIA](../virtual-machines/extensions/hpccompute-gpu-linux.md
) do maszyny wirtualnej przy użyciu Azure Portal, komputera klienckiego, który łączy się z subskrypcją platformy Azure lub Azure Cloud Shell. Alternatywnie wykonaj kroki, aby połączyć się z maszyną wirtualną i ręcznie [zainstalować sterowniki cuda](../virtual-machines/linux/n-series-driver-setup.md) .
3. Postępuj zgodnie z instrukcjami, aby utworzyć [obraz udostępnionej galerii obrazów](batch-sig-images.md) dla usługi Batch.
4. Utwórz konto w usłudze Batch w regionie, który obsługuje maszyny wirtualne NC.
5. Korzystając z interfejsów API usługi Batch lub Azure Portal, Utwórz pulę [przy użyciu obrazu niestandardowego](batch-sig-images.md) oraz żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli dla obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostka SKU agenta węzła** | Batch. Node. Ubuntu 16,04 |
| **Rozmiar węzła** | Standard NC6 |

## <a name="example-microsoft-mpi-on-a-windows-h16r-vm-pool"></a>Przykład: Microsoft MPI w puli maszyn wirtualnych z systemem Windows H16r

Aby uruchamiać aplikacje MPI systemu Windows w puli węzłów maszyny wirtualnej Azure H16r, należy skonfigurować rozszerzenie HpcVmDrivers i zainstalować [program Microsoft MPI](/message-passing-interface/microsoft-mpi). Poniżej przedstawiono przykładowe kroki służące do wdrażania niestandardowego obrazu systemu Windows Server 2016 przy użyciu niezbędnych sterowników i oprogramowania:

1. Wdróż maszynę wirtualną usługi Azure H16r z systemem Windows Server 2016. Na przykład utwórz maszynę wirtualną w regionie zachodnie stany USA. 
2. Dodaj rozszerzenie HpcVmDrivers do maszyny wirtualnej, [uruchamiając polecenie Azure PowerShell](../virtual-machines/sizes-hpc.md) z komputera klienckiego, który nawiązuje połączenie z subskrypcją platformy Azure lub korzystając z Azure Cloud Shell. 
1. Nawiązywanie połączenia Pulpit zdalny z maszyną wirtualną.
1. Pobierz [pakiet instalacyjny](https://www.microsoft.com/download/details.aspx?id=57467) (MSMpiSetup.exe) dla najnowszej wersji programu Microsoft MPI i zainstaluj program Microsoft MPI.
1. Postępuj zgodnie z instrukcjami, aby utworzyć [obraz udostępnionej galerii obrazów](batch-sig-images.md) dla usługi Batch.
1. Korzystając z interfejsów API usługi Batch lub Azure Portal, Utwórz pulę [przy użyciu galerii obrazów udostępnionych](batch-sig-images.md) i żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli dla obrazu:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Obraz niestandardowy |
| **Obraz niestandardowy** | *Nazwa obrazu* |
| **Jednostka SKU agenta węzła** | Batch. Node. Windows amd64 |
| **Rozmiar węzła** | Standard H16r |
| **Komunikacja między węzłami włączona** | Prawda |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="example-intel-mpi-on-a-linux-h16r-vm-pool"></a>Przykład: Intel MPI w puli maszyn wirtualnych z systemem Linux H16r

Aby uruchamiać aplikacje MPI w puli w węźle z systemem Linux w serii H, jedną z opcji jest użycie obrazu [7,4 HPC opartego na CentOS](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview) w portalu Azure Marketplace. Sterowniki RDMA systemu Linux i procesor Intel MPI są wstępnie zainstalowane. Ten obraz obsługuje również obciążenia kontenerów platformy Docker.

Korzystając z interfejsów API usługi Batch lub Azure Portal, Utwórz pulę przy użyciu tego obrazu oraz żądaną liczbę węzłów i skalowania. W poniższej tabeli przedstawiono przykładowe ustawienia puli:

| Ustawienie | Wartość |
| ---- | ---- |
| **Typ obrazu** | Witryna Marketplace (Linux/Windows) |
| **Wydawca** | OpenLogic |
| **Oferta** | CentOS — HPC |
| **Magazyn** | 7,4 |
| **Rozmiar węzła** | Standard H16r |
| **Komunikacja między węzłami włączona** | Prawda |
| **Maksymalna liczba zadań na węzeł** | 1 |

## <a name="next-steps"></a>Następne kroki

* Aby uruchamiać zadania MPI w puli Azure Batch, zobacz przykłady dla [systemu Windows](batch-mpi.md) lub [Linux](/archive/blogs/windowshpc/introducing-mpi-support-for-linux-on-azure-batch) .

* Aby zapoznać się z przykładami obciążeń procesora GPU w usłudze Batch, zobacz przepisy dotyczące [stoczni usługi Batch](https://github.com/Azure/batch-shipyard/) .
