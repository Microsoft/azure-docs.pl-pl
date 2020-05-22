---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 57b6bf06e34068b5560829838eb9ee1315df6cde
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83778189"
---
Azure Shared disks (wersja zapoznawcza) to nowa funkcja dysków zarządzanych przez platformę Azure, która umożliwia równoczesne dołączanie dysku zarządzanego do wielu maszyn wirtualnych. Dołączenie dysku zarządzanego do wielu maszyn wirtualnych pozwala wdrożyć nowe lub migrować istniejące aplikacje klastrowane na platformę Azure.

## <a name="how-it-works"></a>Jak to działa

Maszyny wirtualne w klastrze mogą odczytywać lub zapisywać na dołączonym dysku na podstawie rezerwacji wybranych przez aplikację klastrowaną przy użyciu [rezerwacji trwałych SCSI](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR to standardowy branża, wykorzystywana przez aplikacje działające w sieci magazynowania (SAN) w środowisku lokalnym. Włączenie żądania ściągnięcia SCSI na dysku zarządzanym umożliwia migrowanie tych aplikacji na platformę Azure.

Udostępnianie dysków zarządzanych oferuje udostępniony magazyn blokowy, do którego można uzyskać dostęp z wielu maszyn wirtualnych, są one udostępniane jako numery jednostek logicznych (LUN). Jednostki LUN są następnie prezentowane inicjatorowi (VM) z miejsca docelowego (dysku). Te jednostki LUN wyglądają jak bezpośrednio dołączone do magazynu (DAS) lub dysk lokalny na maszynę wirtualną.

Udostępnione dyski zarządzane nie oferują natywnie w pełni zarządzanego systemu plików, do którego można uzyskać dostęp przy użyciu protokołu SMB/NFS. Należy użyć Menedżera klastra, takiego jak Windows Server failover Cluster (WSFC) lub Pacemaker, który obsługuje komunikację węzłów klastra, a także blokowanie zapisu.

## <a name="limitations"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Rozmiary dysków

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Przykładowe obciążenia

### <a name="windows"></a>Windows

Większość klastrów opartych na systemie Windows kompiluje się w usłudze WSFC, która obsługuje wszystkie podstawowe infrastruktury komunikacji węzłów klastra, co umożliwia aplikacjom korzystanie z wzorców dostępu równoległego. W zależności od używanej wersji systemu Windows Server usługi WSFC umożliwiają zarówno używanie woluminów CSV, jak i nieopartych na woluminach CSV. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie klastra trybu failover](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Niektóre popularne aplikacje działające w usłudze WSFC obejmują:

- SQL Server wystąpienia klastra trybu failover (FCI)
- Serwer plików skalowalny w poziomie (SoFS)
- Serwer plików do użytku ogólnego (obciążenie IW)
- Dysk profilu użytkownika serwera Pulpit zdalny (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Klastry systemu Linux mogą korzystać z menedżerów klastra, takich jak [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker kompiluje w [Corosync](http://corosync.github.io/corosync/), co umożliwia komunikację klastra w przypadku aplikacji wdrożonych w środowiskach o wysokiej dostępności. Niektóre typowe klastrowane systemy plików to [OCFS2](https://oss.oracle.com/projects/ocfs2/) i [GFS2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Można manipulować rezerwacjami i rejestracjami przy użyciu narzędzi, takich jak [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) i [sg_persist](https://linux.die.net/man/8/sg_persist).

#### <a name="ubuntu"></a>Ubuntu

Aby uzyskać informacje na temat sposobu konfigurowania wysokiej dostępności usługi Ubuntu z użyciem Corosync i Pacemaker na dyskach udostępnionych na platformie Azure, zobacz [Ubuntu Community](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)informing.

## <a name="persistent-reservation-flow"></a>Trwały przepływ rezerwacji

Na poniższym diagramie przedstawiono przykładową klastrowaną aplikację bazy danych 2-węzłową, która wykorzystuje funkcję SCSI PR do przełączania trybu failover z jednego węzła do drugiego.

![Klaster z dwoma węzłami. Aplikacja działająca w klastrze obsługuje dostęp do dysku](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

Przepływ jest następujący:

1. Klastrowana aplikacja działająca na platformie Azure VM1 i VM2 rejestruje swój cel odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji w witrynie VM1 pobiera wyłączną rezerwację do zapisu na dysku.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure, a baza danych może teraz zapisywać dane na dysku. Wszystkie zapisy z wystąpienia aplikacji w systemie VM2 nie powiodą się.
1. Jeśli wystąpienie aplikacji w witrynie VM1 ulegnie awarii, wystąpienie w usłudze VM2 może teraz inicjować pracę w trybie failover bazy danych i przejmowanie dysku.
1. Ta rezerwacja jest teraz wymuszana na dysku platformy Azure, a dysk nie akceptuje już operacji zapisu z VM1. Akceptuje tylko zapisy z VM2.
1. Klastrowana aplikacja może zakończyć pracę w trybie failover bazy danych i obsłużyć żądania z VM2.

Na poniższym diagramie przedstawiono inne typowe obciążenie klastrowane składające się z wielu węzłów odczytywania danych z dysku na potrzeby uruchamiania procesów równoległych, takich jak szkolenie modeli uczenia maszynowego.

![Klaster z czterema węzłami VM, każdy węzeł rejestruje przeznaczenie do zapisu, aplikacja pobiera wyłączną rezerwację, aby prawidłowo obsługiwać wyniki zapisu](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

Przepływ jest następujący:

1. Aplikacja klastrowana uruchomiona na wszystkich maszynach wirtualnych rejestruje zamiar odczytu lub zapisu na dysku.
1. Wystąpienie aplikacji na VM1 pobiera wyłączną rezerwację do zapisu na dysku podczas otwierania odczytów na dysku z innych maszyn wirtualnych.
1. Ta rezerwacja jest wymuszana na dysku platformy Azure.
1. Wszystkie węzły w klastrze mogą teraz odczytywać z dysku. Tylko jeden węzeł zapisuje wyniki z powrotem na dysku w imieniu wszystkich węzłów w klastrze.

### <a name="ultra-disks-reservation-flow"></a>Przepływ rezerwacji Ultra disks

Ultra disks oferuje dodatkową przepustowość w przypadku łącznej liczby dwóch przepustnic. Ze względu na to, przepływ rezerwacji Ultra disks może współdziałać zgodnie z opisem w poprzedniej sekcji lub bardziej szczegółowym Ograniczeniem i dystrybucją wydajności.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text=" ":::

## <a name="ultra-disk-performance-throttles"></a>Ograniczenia wydajności Ultra Disk

Funkcja Ultra disks ma unikatową funkcję umożliwiającą ustawienie wydajności przez ujawnienie modyfikowalnych atrybutów i umożliwienie ich modyfikacji. Domyślnie istnieją tylko dwa modyfikowalne atrybuty, ale współużytkowane Ultra disks mają dwa dodatkowe atrybuty.


|Atrybut  |Opis  |
|---------|---------|
|DiskIOPSReadWrite     |Całkowita liczba operacji we/wy dozwolonych dla wszystkich maszyn wirtualnych instalujących udział dysku z dostępem do zapisu.         |
|DiskMBpsReadWrite     |Całkowita przepływność (MB/s) dozwolona przez wszystkie maszyny wirtualne instalująca dysk udostępniony z dostępem do zapisu.         |
|DiskIOPSReadOnly*     |Całkowita liczba operacji we/wy dozwolonych dla wszystkich maszyn wirtualnych instalujących dysk udostępniony jako tylko do odczytu.         |
|DiskMBpsReadOnly*     |Całkowita przepływność (MB/s) dozwolona przez wszystkie maszyny wirtualne instalująca dysk udostępniony jako tylko do odczytu.         |

\*Dotyczy tylko współużytkowanych dysków o najwyższej rozdzielczości

Poniższe wzory wyjaśniają, jak można ustawić atrybuty wydajności, ponieważ są one modyfikowane przez użytkowników:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limity IOPS 300 IOPS/GiB, maksymalnie 160K operacji we/wy na dysk
    - Co najmniej 100 operacji we/wy na sekundę
    - DiskIOPSReadWrite + DiskIOPSReadOnly to co najmniej 2 IOPS/GiB
- DiskMBpsRead zapisu/DiskMBpsReadOnly:
    - Limit przepływności pojedynczego dysku to 256 KiB/s dla każdej z zainicjowanych operacji we/wy, maksymalnie 2000 s MB na dysk
    - Minimalną gwarantowaną przepływność na dysk to 4KiB/s dla każdej zainicjowanej operacji we/wy, a ogólna podstawowa linia bazowa wynosi 1 MB/s

### <a name="examples"></a>Przykłady

W poniższych przykładach przedstawiono kilka scenariuszy, które pokazują, jak ograniczenie może współdziałać z udostępnionymi dyskami o najwyższej wydajności.

#### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Klaster z dwoma węzłami przy użyciu udostępnionych woluminów klastra

Poniżej znajduje się przykład 2-węzłowego usługi WSFC korzystającej z udostępnionych woluminów klastra. W przypadku tej konfiguracji obie maszyny wirtualne mają jednoczesny dostęp do zapisu do dysku, co powoduje, że ograniczenie ReadWrite jest dzielone na dwie maszyny wirtualne i nie jest używane ograniczenie tylko do odczytu.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Przykładowy plik CSV dwa węzły":::

:::image-end:::

#### <a name="two-node-cluster-without-cluster-share-volumes"></a>Klaster z dwoma węzłami bez woluminów udziałów klastra

Poniżej znajduje się przykład 2-węzłowego usługi WSFC, która nie korzysta z udostępnionych woluminów klastra. W przypadku tej konfiguracji tylko jedna maszyna wirtualna ma dostęp do zapisu na dysku. Powoduje to, że ograniczenie ReadWrite jest używane wyłącznie dla podstawowej maszyny wirtualnej i ograniczenie tylko do odczytu używane przez pomocniczą.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Dwa węzły w formacie CSV — przykład braku dysku CSV":::

:::image-end:::

#### <a name="four-node-linux-cluster"></a>Klaster z systemem Linux z czterema węzłami

Poniżej znajduje się przykładowy klaster z systemem Linux z 4 węzłami z pojedynczym modułem zapisywania i trzema czytnikami skalowalnymi w poziomie. W przypadku tej konfiguracji tylko jedna maszyna wirtualna ma dostęp do zapisu na dysku. Powoduje to, że ograniczenie ReadWrite jest używane wyłącznie dla podstawowej maszyny wirtualnej i ograniczenie tylko do odczytu przez pomocnicze maszyny wirtualne.

:::image type="complex" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Przykład o czterech węzłach Ultra":::

:::image-end:::
