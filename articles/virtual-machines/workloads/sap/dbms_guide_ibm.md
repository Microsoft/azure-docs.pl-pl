---
title: Wdrażanie systemu DBMS programu IBM Db2 Virtual Machines Azure dla obciążeń SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, Db2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/20/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e9030558779be3e417383f9f32612ee3e834a1c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788083"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP

Za Microsoft Azure można migrować istniejącą aplikację SAP z systemem IBM Db2 dla systemów Linux, UNIX i Windows (LUW) do maszyn wirtualnych platformy Azure. Dzięki systemowi SAP w programie IBM Db2 for LUW administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi deweloperskich i administracyjnych, które są dostępne lokalnie.
Ogólne informacje na temat uruchamiania pakietu SAP Business Suite w programie IBM Db2 dla programu LUW można znaleźć w tece SAP Community Network (SCN) na stronie <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html> .

Aby uzyskać więcej informacji i aktualizacji dotyczących oprogramowania SAP na platformie Db2 for LUW na platformie Azure, zobacz Uwaga [2233094]oprogramowania SAP. 

W różnych artykułach dotyczących obciążenia SAP na platformie Azure zostały wydane różne artykuły.  Zaleca się rozpoczęcie od obciążenia [SAP na platformie Azure Rozpocznij](./get-started.md) a następnie wybór obszaru zainteresowań

Następujące uwagi dotyczące systemu SAP są SAP na platformie Azure dotyczące obszaru objętego tym dokumentem:

| Numer notatki |Tytuł |
| --- |--- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |Oprogramowanie SAP na Microsoft Azure: wymagania wstępne pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z rozszerzonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2178632] |Kluczowe metryki monitorowania oprogramowania SAP na platformie Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: rozszerzone monitorowanie |
| [2191498] |Oprogramowanie SAP w systemie Linux z platformą Azure: rozszerzone monitorowanie |
| [2233094] |DB6: aplikacje SAP na platformie Azure korzystające z programu IBM DB2 dla systemów Linux, UNIX i Windows — dodatkowe informacje |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP license issues (Linux on Microsoft Azure (IaaS) VM: SAP license issues (Linux on Microsoft Azure (IaaS) VM: SAP license issues |
| [1984787] |SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7.x: instalacja i uaktualnianie |
| [1597355] |Zalecenie dotyczące zamiany miejsca dla systemu Linux |

Jako pr-read do tego dokumentu, należy przeczytać dokument [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Zagadnienia dotyczące wdrażania systemu DBMS usługi Azure Virtual Machines dbms dla obciążenia SAP) oraz inne przewodniki w dokumentacji dotyczącej obciążenia SAP na [platformie Azure.](./get-started.md) 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Obsługa wersji programu IBM Db2 dla systemów Linux, UNIX i Windows
Oprogramowanie SAP w programie IBM Db2 dla programu LUW Microsoft Azure Virtual Machine Services jest obsługiwane od wersji Db2 10.5.

Aby uzyskać informacje o obsługiwanych produktach SAP i typach maszyn wirtualnych platformy Azure, zobacz Uwaga [1928533 oprogramowania]SAP.

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Ibm Db2 for Linux, UNIX i Windows Configuration Guidelines for SAP Installations in Azure VMs (Wytyczne dotyczące konfiguracji systemu IBM Db2 dla systemów Linux, UNIX i Windows dla instalacji SAP na maszynach wirtualnych platformy Azure)
### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Omówienie typów magazynu platformy Azure dla obciążenia SAP można znaleźć w artykule Azure Storage types for SAP workload (Typy usługi Azure Storage dla obciążenia [SAP)](./planning-guide-storage.md) Wszystkie pliki bazy danych muszą być przechowywane na zainstalowanych dyskach magazynu blokowego platformy Azure (Windows: NTFS, Linux: xfs lub ext3). Żaden rodzaj dysków sieciowych lub udziałów zdalnych, takich jak następujące usługi platformy **Azure, NIE jest** obsługiwany w przypadku plików bazy danych: 

* [Microsoft Azure usługi plików](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)

Korzystając z dysków opartych na usłudze Azure Page BLOB Storage lub usłudze Dyski zarządzane, instrukcje z tematu [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md) (Zagadnienia dotyczące wdrażania systemu DBMS w usłudze Azure Virtual Machines DBMS dla obciążenia SAP) dotyczą również wdrożeń z systemami DBMS Db2.

Jak wyjaśniono wcześniej w ogólnej części dokumentu, istnieją limity przydziału przepływności IOPS dla dysków platformy Azure. Dokładne przydziały zależą od używanego typu maszyny wirtualnej. Listę typów maszyn wirtualnych z ich przydziałami można znaleźć [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows).][virtual-machines-sizes-windows]

Tak długo, jak bieżący limit przydziału liczby we/wy na sekundę na dysk jest wystarczający, możliwe jest przechowywanie wszystkich plików bazy danych na jednym zainstalowanym dysku. Podczas gdy zawsze należy oddzielić pliki danych i pliki dziennika transakcji na różnych dyskach/dyskach VHD.

Zagadnienia dotyczące wydajności można również znaleźć w rozdziale "Data Safety and Performance Considerations for Database Directories" (Zagadnienia dotyczące bezpieczeństwa danych i wydajności katalogów baz danych) w przewodnikach instalacji sap.

Alternatywnie można użyć pul magazynu systemu Windows (dostępnych tylko w systemie Windows Server 2012 i wyższych) zgodnie z opisem Uwagi dotyczące wdrażania systemu [DBMS](dbms_guide_general.md) usługi Azure Virtual Machines dla obciążenia SAP lub LVM lub mdadm w systemie Linux, aby utworzyć jedno duże urządzenie logiczne na wielu dyskach.

<!-- log_dir, sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

W przypadku maszyn wirtualnych z serii Azure M opóźnienie zapisu w dziennikach transakcji można zmniejszyć za pomocą czynników w porównaniu z wydajnością usługi Azure Premium Storage podczas korzystania z usługi Azure akcelerator zapisu. W związku z tym należy wdrożyć usługę Azure akcelerator zapisu dla dysków VHD, które tworzą wolumin dla dzienników transakcji Db2. Szczegółowe informacje można znaleźć w dokumencie w [akcelerator zapisu](../../how-to-enable-write-accelerator.md).

Firma IBM Db2 LUW 11.5 wydała obsługę rozmiaru sektora 4 KB. W starszych wersjach db2 należy użyć rozmiaru sektora 512-bajtowego. SSD w warstwie Premium są natywne dla 4 KB i mają emulację 512-bajtową. Dysk w kącie Ultra domyślnie używa rozmiaru sektora 4 KB. Rozmiar sektora 512-bajtowego można włączyć podczas tworzenia dysku w kącie Ultra. Szczegółowe informacje są dostępne [za pomocą dysków w warstwie Ultra platformy Azure.](../../disks-enable-ultra-ssd.md#deploy-an-ultra-disk---512-byte-sector-size) Ten 512-bajtowy rozmiar sektora jest wymaganie wstępne dla programu IBM Db2 LUW w wersji niższej niż 11.5.

W systemie Windows używającym pul magazynu dla ścieżek magazynu Db2 dla programu i katalogów należy określić rozmiar sektora dysku fizycznego `log_dir` `sapdata` `saptmp` 512 KB. W przypadku korzystania z pul magazynu systemu Windows należy ręcznie utworzyć pule magazynów za pomocą interfejsu wiersza polecenia przy użyciu parametru `-LogicalSectorSizeDefault` . Aby uzyskać więcej informacji, zobacz <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.


## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Zalecenie dotyczące struktury maszyn wirtualnych i dysków dla wdrożenia programu IBM Db2

Aplikacje IBM Db2 for SAP NetWeaver są obsługiwane na dowolnym typie maszyny wirtualnej wymienionej w uwagach [1928533]do pomocy technicznej oprogramowania SAP.  Zalecane rodziny maszyn wirtualnych do uruchamiania bazy danych IBM Db2 to Esd_v4/Eas_v4/Es_v3 i M/M_v2 dla dużych baz danych o wielu terabajtach. Wydajność zapisu na dysku dziennika transakcji IBM Db2 można poprawić, włączając serwer z serii M akcelerator zapisu. 

Poniżej przedstawiono konfigurację linii bazowej dla różnych rozmiarów i zastosowań oprogramowania SAP we wdrożeniach Db2 — od małych do dużych. Lista jest oparta na usłudze Azure Premium Storage. Jednak dysk w chmurze Azure Ultra jest również w pełni obsługiwany z bazą danych Db2 i może być również używany. Użyj wartości dla pojemności, zwiększenia przepływności i zwiększenia wartości IOPS, aby zdefiniować konfigurację dysku Ultra. Można ograniczyć liczbę IOPS dla /db2/ <SID> /log_dir około 5000 IOPS. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Bardzo mały system SAP: rozmiar bazy danych 50–200 GB: przykładowy menedżer rozwiązań
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji Db2 |Dysk platformy Azure w warstwie Premium |Nr dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Burst IOPS |Burst Thr [GB] | Rozmiar paska rozłożonego | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  ||  |
|Procesor vCPU: 4 |/db2/ <SID> /sapdata |P10 |2 |1000  |200  |256  |7,000  |340  |256 KB |ReadOnly |
|RAM: 32 GiB |/db2/ <SID> /saptmp |P6 |1 |240  |50  |128  |3500  |170  | ||
| |/db2/ <SID> /log_dir |P6 |2 |480  |100  |128  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Mały system SAP: rozmiar bazy danych 200–750 GB: mały pakiet Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji Db2 |Dysk platformy Azure w warstwie Premium |Nr dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Burst IOPS |Burst Thr [GB] | Rozmiar paska rozłożonego | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|Procesor vCPU: 16 |/db2/ <SID> /sapdata |P15 |4 |4,400  |500  |1.024  |14,000  |680  |256 KB |ReadOnly |
|RAM: 128 GiB |/db2/ <SID> /saptmp |P6 |2 |480  |100  |128  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P15 |2 |2200  |250  |512  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Średni system SAP: rozmiar bazy danych 500–1000 GB: mały pakiet Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji Db2 |Dysk platformy Azure w warstwie Premium |Nr dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Burst IOPS |Burst Thr [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|Procesory wirtualne: 32 |/db2/ <SID> /sapdata |P30 |2 |10 000  |400  |2.048  |10 000  |400  |256 KB |ReadOnly |
|RAM: 256 GiB |/db2/ <SID> /saptmp |P10 |2 |1000  |200  |256  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P20 |2 |4,600  |300  |1.024  |7,000  |340  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P15 |1 |1,100  |125  |256  |3500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Duży system SAP: rozmiar bazy danych 750–2000 GB: Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji Db2 |Dysk platformy Azure w warstwie Premium |Nr dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Burst IOPS |Burst Thr [GB] | Rozmiar paska rozłożonego | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|Procesor vCPU: 64 |/db2/ <SID> /sapdata |P30 |4 |20 000  |800  |4.096  |20 000  |800  |256 KB |ReadOnly |
|RAM: 504 GiB |/db2/ <SID> /saptmp |P15 |2 |2200  |250  |512  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P20 |4 |9,200  |600  |2.048  |14,000  |680  |64 KB ||
| |/db2/ <SID> /offline_log_dir |P20 |1 |2300  |150  |512  |3500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Duży, terabajtowy system SAP: rozmiar bazy danych 2 TB+: system Global Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji Db2 |Dysk platformy Azure w warstwie Premium |Nr dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Burst IOPS |Burst Thr [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3500  |170  || |
|Procesory wirtualne: 128 |/db2/ <SID> /sapdata |P40 |4 |30,000  |1.000  |8.192  |30,000  |1.000  |256 KB |ReadOnly |
|RAM: 2048 GiB |/db2/ <SID> /saptmp |P20 |2 |4,600  |300  |1.024  |7,000  |340  |128 KB ||
| |/db2/ <SID> /log_dir |P30 |4 |20 000  |800  |4.096  |20 000  |800  |64 KB |WriteAccelerator |
| |/db2/ <SID> /offline_log_dir |P30 |1 |5000  |200  |1.024  |5000  |200  || |


### <a name="backuprestore"></a>Tworzenie/przywracanie kopii zapasowych
Funkcja tworzenia/przywracania kopii zapasowych programu IBM Db2 dla programu LUW jest obsługiwana w taki sam sposób jak w standardowych systemach operacyjnych Windows Server i funkcji Hyper-V.

Upewnij się, że masz prawidłową strategię tworzenia kopii zapasowych bazy danych. 

Podobnie jak w przypadku wdrożeń bez systemu operacyjnego wydajność tworzenia/przywracania kopii zapasowych zależy od liczby woluminów, które mogą być odczytywane równolegle, oraz od przepływności tych woluminów. Ponadto użycie procesora używane przez kompresję kopii zapasowej może odgrywać znaczącą rolę na maszyny wirtualne z maksymalnie ośmioma wątkami procesora CPU. W związku z tym można założyć, że:

* Im mniejsza liczba dysków używanych do przechowywania urządzeń baz danych, tym mniejsza ogólna przepływność podczas odczytywania
* Im mniejsza liczba wątków procesora CPU na maszynie wirtualnej, tym większy wpływ kompresji kopii zapasowej
* Mniejsza liczba elementów docelowych (katalogi usługi Stripe, dyski) do zapisu kopii zapasowej, tym niższa przepływność

Aby zwiększyć liczbę obiektów docelowych do zapisu, można użyć/połączyć dwie opcje w zależności od potrzeb:

* Rozłożone docelowego woluminu kopii zapasowej na wielu dyskach w celu zwiększenia przepływności liczby IOPS na tym woluminie rozłożonym
* Używanie więcej niż jednego katalogu docelowego do zapisu kopii zapasowej w

>[!NOTE]
>Db2 w systemie Windows nie obsługuje technologii VSS systemu Windows. W związku z tym spójnej z aplikacją kopii zapasowej maszyny wirtualnej usługi Azure Backup Service nie można wykorzystać dla maszyn wirtualnych, w których wdrożono system DBMS Db2.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

#### <a name="linux-pacemaker"></a>Linux Pacemaker

Obsługiwane jest odzyskiwanie po awarii db2 o wysokiej dostępności (HADR) za pomocą pacemaker. Obsługiwane są systemy operacyjne SLES i RHEL. Ta konfiguracja zapewnia wysoką dostępność programu IBM Db2 for SAP. Przewodniki wdrażania:
* SLES: [Wysoka dostępność programu IBM Db2 LUW na](dbms-guide-ha-ibm.md) maszyny wirtualne platformy Azure na platformie SUSE Linux Enterprise Server pacemaker 
* RHEL: [Wysoka dostępność programu IBM Db2 LUW](high-availability-guide-rhel-ibm-db2-luw.md) na maszyny wirtualne platformy Azure na Red Hat Enterprise Linux Server

#### <a name="windows-cluster-server"></a>Windows Cluster Server

Program Microsoft Cluster Server (MSCS) nie jest obsługiwany.

Obsługiwane jest odzyskiwanie po awarii db2 o wysokiej dostępności (HADR). Jeśli maszyny wirtualne konfiguracji wysokiej jakości mają rozwiązanie nazwy roboczej, konfiguracja na platformie Azure nie różni się od konfiguracji wykonywanej lokalnie. Nie zaleca się polegania tylko na rozpoznawania adresów IP.

Nie używaj Geo-Replication dla kont magazynu, które przechowują dyski bazy danych. Aby uzyskać więcej informacji, zobacz dokument [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md)(Zagadnienia dotyczące wdrażania usługi Azure Virtual Machines DBMS dla obciążenia SAP). 

### <a name="accelerated-networking"></a>Accelerated Networking
W przypadku wdrożeń Db2 w systemie Windows zdecydowanie zaleca się korzystanie z funkcji przyspieszonej sieci na platformie Azure, zgodnie z opisem w dokumencie Azure Accelerated Networking ( Przyspieszona sieć [platformy Azure).](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Należy również wziąć pod uwagę zalecenia zawarte w tesłudze [Considerations for Azure Virtual Machines DBMS deployment for SAP workload](dbms_guide_general.md)(Zagadnienia dotyczące wdrażania systemu DBMS usługi Azure Virtual Machines dla obciążenia SAP). 


### <a name="specifics-for-linux-deployments"></a>Szczegółowe informacje dotyczące wdrożeń systemu Linux
Tak długo, jak bieżący limit przydziału liczby we/wy na sekundę na dysk jest wystarczający, można przechowywać wszystkie pliki bazy danych na jednym dysku. Podczas gdy zawsze należy oddzielić pliki danych i pliki dziennika transakcji na różnych dyskach/dyskach VHD.

Alternatywnie, jeśli przepływność we/wy lub liczby operacje we/wy pojedynczego wirtualnego dysku twardego platformy Azure nie jest wystarczająca, możesz użyć menedżera woluminów logicznych LVM (Logical Volume Manager) lub MDADM zgodnie z opisem w dokumencie [Considerations for Azure Virtual Machines DBMS deployment for SAP](dbms_guide_general.md) workload (Zagadnienia dotyczące wdrażania systemu DBMS platformy Azure dla obciążenia SAP) w celu utworzenia jednego dużego urządzenia logicznego na wielu dyskach.
W przypadku dysków zawierających ścieżki magazynu Db2 dla katalogów sapdata i saptmp należy określić rozmiar sektora dysku fizycznego o rozmiarze 512 KB.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Inne
Wszystkie inne ogólne obszary, takie jak zestawy dostępności platformy Azure lub monitorowanie SAP, mają zastosowanie zgodnie z opisem w dokumencie [Considerations for Azure Virtual Machines DBMS deployment for SAP workload for deployments of VMs](dbms_guide_general.md) with the IBM Database as well (Zagadnienia dotyczące wdrażania systemu DBMS usługi Azure Virtual Machines dla obciążeń SAP w przypadku wdrożeń maszyn wirtualnych z bazą danych IBM Database).

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692


## <a name="next-steps"></a>Następne kroki
Przeczytaj artykuł 

- [Zagadnienia dotyczące wdrażania usługi Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md)



[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md