---
title: Wdrożenie programu IBM DB2 Azure Virtual Machines DBMS dla obciążeń SAP | Microsoft Docs
description: Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, DB2, SAP, IBM
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5e401fb35e6524e8a32cbe09bf5b9c7feafbf5da
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974740"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Wdrażanie systemu DBMS usługi Azure Virtual Machines programu IBM Db2 dla obciążenia SAP

Za pomocą Microsoft Azure można migrować istniejącą aplikację SAP działającą w systemach IBM DB2 dla systemów Linux, UNIX i Windows (LUW) do maszyn wirtualnych platformy Azure. W przypadku oprogramowania SAP w programie IBM DB2 for LUW Administratorzy i deweloperzy mogą nadal korzystać z tych samych narzędzi programistycznych i administracyjnych, które są dostępne lokalnie.
Ogólne informacje o uruchamianiu programu SAP Business Suite w programie IBM DB2 for LUW można znaleźć w sieci społeczności SAP (SCN) pod adresem <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html> .

Aby uzyskać więcej informacji i aktualizacji dotyczących oprogramowania SAP w programie DB2 for LUW na platformie Azure, zobacz temat SAP Note [2233094]. 

Są to różne artykuły dotyczące obciążeń SAP na platformie Azure.  Zaleca się Rozpoczynanie [obciążeń SAP na platformie Azure — wprowadzenie](./get-started.md) i wybór obszaru zainteresowań

Poniższe uwagi dotyczące oprogramowania SAP są powiązane z oprogramowaniem SAP na platformie Azure w odniesieniu do obszaru objętego tym dokumentem:

| Numer notatki |Tytuł |
| --- |--- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP on Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z ulepszonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2178632] |Metryki monitorowania kluczy dla SAP na Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2191498] |SAP w systemie Linux z platformą Azure: ulepszone monitorowanie |
| [2233094] |DB6: Aplikacje SAP na platformie Azure korzystające z programu IBM DB2 dla systemów Linux, UNIX i Windows — dodatkowe informacje |
| [2243692] |Maszyna wirtualna z systemem Linux na Microsoft Azure (IaaS): problemy z licencją SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7. x: Instalowanie i uaktualnianie |
| [1597355] |Zalecenie wymiany miejsca dla systemu Linux |

Jako żądanie ściągnięcia do tego dokumentu należy przeczytać [zagadnienia dotyczące dokumentu dotyczące wdrożenia systemu azure Virtual Machines DBMS dotyczące obciążeń SAP](dbms_guide_general.md) oraz innych przewodników [dotyczących obciążeń SAP w dokumentacji platformy Azure](./get-started.md). 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Obsługa wersji IBM DB2 dla systemów Linux, UNIX i Windows
Oprogramowanie SAP w programie IBM DB2 for LUW w Microsoft Azure usługach maszyn wirtualnych jest obsługiwane w programie DB2 w wersji 10,5.

Aby uzyskać informacje o obsługiwanych produktach SAP i typach maszyn wirtualnych platformy Azure, zapoznaj się z uwagą do programu SAP [1928533].

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>Wytyczne dotyczące konfiguracji oprogramowania IBM DB2 dla systemów Linux, UNIX i Windows dla instalacji SAP na maszynach wirtualnych platformy Azure
### <a name="storage-configuration"></a>Konfiguracja usługi Storage
Aby zapoznać się z omówieniem typów magazynu platformy Azure dla obciążenia SAP, zapoznaj się z artykułem [typy magazynów platformy Azure dla obciążeń SAP](./planning-guide-storage.md) wszystkie pliki bazy danych muszą być przechowywane na zainstalowanych dyskach magazynu blokowego platformy Azure (Windows: NFFS, Linux: XFS, ext4 lub ext3). Dla plików bazy danych **nie** są obsługiwane żadne rodzaje dysków sieciowych ani udziałów zdalnych, takich jak następujące usługi platformy Azure: 

* [Usługa plików Microsoft Azure](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)

Korzystając z dysków opartych na usłudze Azure Page BLOB Storage lub Managed Disks, instrukcje dotyczące [wdrożenia systemu azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_general.md) dotyczą również wdrożeń z systemem DB2 DBMS.

Jak wyjaśniono wcześniej w ogólnej części dokumentu, istnieją przydziały przepływności operacji we/wy na sekundę dla dysków platformy Azure. Dokładne limity przydziału są zależne od używanego typu maszyny wirtualnej. Listę typów maszyn wirtualnych z ich przydziałami można znaleźć [tutaj (Linux)][virtual-machines-sizes-linux] i [tutaj (Windows)][virtual-machines-sizes-windows].

Tak długo, jak bieżąca liczba operacji we/wy na dysk jest wystarczająca, możliwe jest przechowywanie wszystkich plików bazy danych na jednym dysku zainstalowanym. Należy zawsze oddzielić pliki danych i pliki dziennika transakcji na różnych dyskach/VHD.

Zagadnienia dotyczące wydajności znajdują się również w rozdziale "zagadnienia dotyczące bezpieczeństwa danych i wydajności dla katalogów baz danych" w przewodnikach instalacji SAP.

Alternatywnie można użyć pul magazynu systemu Windows (dostępne tylko w systemie Windows Server 2012 lub nowszym), jak opisano w temacie [zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md) lub LVM lub mdadm w systemie Linux w celu utworzenia jednego dużego urządzenia logicznego na wielu dyskach.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

W przypadku dysków zawierających ścieżki magazynu DB2 dla `sapdata` katalogów i należy `saptmp` określić rozmiar sektora dysku fizycznego wynoszący 512 KB. W przypadku korzystania z pul magazynu systemu Windows należy ręcznie utworzyć pule magazynów za pośrednictwem interfejsu wiersza polecenia za pomocą parametru `-LogicalSectorSizeDefault` . Aby uzyskać więcej informacji, zobacz <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

W przypadku maszyny wirtualnej z serii M na platformie Azure opóźnienie zapisu w dziennikach transakcji można zmniejszyć o czynniki w porównaniu z wydajnością Premium Storage platformy Azure, korzystając z usługi Azure akcelerator zapisu. W związku z tym należy wdrożyć akcelerator zapisu platformy Azure dla dysków VHD, które tworzą wolumin dla dzienników transakcji bazy danych DB2. Szczegóły można odczytać w dokumencie [Akcelerator zapisu](../../how-to-enable-write-accelerator.md).

## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Zalecenie dotyczące maszyn wirtualnych i struktur dysków dla wdrożenia programu IBM DB2

Aplikacje IBM DB2 for SAP NetWeaver są obsługiwane na dowolnym typie maszyny wirtualnej wymienionym w temacie [1928533].  Zalecane rodziny maszyn wirtualnych do uruchamiania bazy danych programu IBM DB2 są Esd_v4/Eas_v4/Es_v3 i serii M/M_v2 dla dużych baz danych z obsługą wielu terabajtów. Wydajność zapisu na dysku dziennika transakcji IBM DB2 można ulepszyć, włączając akcelerator zapisu serii M. 

Poniżej znajduje się konfiguracja linii bazowej dla różnych rozmiarów i użycia oprogramowania SAP na wdrożeniach z bazy danych DB2 z małych i dużych. Lista jest oparta na usłudze Azure Premium Storage. Jednak platforma Azure Ultra Disk jest w pełni obsługiwana również w programie DB2 i może być również używana. Wystarczy użyć wartości wydajności, przepływności serii i operacji we/wy na sekundę, aby zdefiniować konfigurację Ultra Disk. Można ograniczyć liczbę operacji we/wy dla/DB2/ <SID> /log_dir na około 5000 operacji we/wy na sekundę. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Bardzo mały system SAP: rozmiar bazy danych 50-200 GB: przykładowy Menedżer rozwiązań
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji bazy danych DB2 |Dysk platformy Azure w warstwie Premium |Liczba dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Operacje we/wy na sekundę |Prz serii [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  ||  |
|vCPU: 4 |/DB2/ <SID> /sapdata |P10 |2 |1000  |200  |256  |7 000  |340  |256 KB |ReadOnly |
|Pamięć RAM: 32 GiB |/DB2/ <SID> /saptmp |P6 |1 |240  |50  |128  |3500  |170  | ||
| |/DB2/ <SID> /log_dir |P6 |2 |480  |100  |128  |7 000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Mały system SAP: rozmiar bazy danych 200-750 GB: Small Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji bazy danych DB2 |Dysk platformy Azure w warstwie Premium |Liczba dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Operacje we/wy na sekundę |Prz serii [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 16 |/DB2/ <SID> /sapdata |P15 |4 |4 400  |500  |1,024  |14 000  |680  |256 KB |ReadOnly |
|Pamięć RAM: 128 GiB |/DB2/ <SID> /saptmp |P6 |2 |480  |100  |128  |7 000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P15 |2 |2200  |250  |512  |7 000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Średni system SAP: rozmiar bazy danych 500-1000 GB: Small Business Suite
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji bazy danych DB2 |Dysk platformy Azure w warstwie Premium |Liczba dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Operacje we/wy na sekundę |Prz serii [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 32 |/DB2/ <SID> /sapdata |P30 |2 |10 000  |400  |2,048  |10 000  |400  |256 KB |ReadOnly |
|Pamięć RAM: 256 GiB |/DB2/ <SID> /saptmp |P10 |2 |1000  |200  |256  |7 000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P20 |2 |4 600  |300  |1,024  |7 000  |340  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P15 |1 |1,100  |125  |256  |3500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Duży system SAP: rozmiar bazy danych 750-2000 GB: zestaw biznesowy
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji bazy danych DB2 |Dysk platformy Azure w warstwie Premium |Liczba dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Operacje we/wy na sekundę |Prz serii [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 64 |/DB2/ <SID> /sapdata |P30 |4 |20 000  |800  |4,096  |20 000  |800  |256 KB |ReadOnly |
|Pamięć RAM: 504 GiB |/DB2/ <SID> /saptmp |P15 |2 |2200  |250  |512  |7 000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P20 |4 |9 200  |600  |2,048  |14 000  |680  |64 KB ||
| |/DB2/ <SID> /offline_log_dir |P20 |1 |2300  |150  |512  |3500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Duży system SAP z obsługą wielu terabajtów: baza danych o rozmiarze 2 TB +: system globalnego zestawu biznesowego
| Nazwa/rozmiar maszyny wirtualnej |Punkt instalacji bazy danych DB2 |Dysk platformy Azure w warstwie Premium |Liczba dysków |Liczba operacji we/wy na sekundę |Przepływność [MB/s] |Rozmiar [GB] |Operacje we/wy na sekundę |Prz serii [GB] | Rozmiar paska | Buforowanie |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3500  |170  || |
|vCPU: 128 |/DB2/ <SID> /sapdata |P40 |4 |30 000  |1,000  |8,192  |30 000  |1,000  |256 KB |ReadOnly |
|Pamięć RAM: 2048 GiB |/DB2/ <SID> /saptmp |P20 |2 |4 600  |300  |1,024  |7 000  |340  |128 KB ||
| |/DB2/ <SID> /log_dir |P30 |4 |20 000  |800  |4,096  |20 000  |800  |64 KB |WriteAccelerator |
| |/DB2/ <SID> /offline_log_dir |P30 |1 |5000  |200  |1,024  |5000  |200  || |


### <a name="backuprestore"></a>Tworzenie/przywracanie kopii zapasowych
Funkcja tworzenia kopii zapasowej/przywracania dla programu IBM DB2 for LUW jest obsługiwana w taki sam sposób jak w przypadku standardowych systemów operacyjnych Windows Server i funkcji Hyper-V.

Upewnij się, że masz prawidłową strategię tworzenia kopii zapasowych bazy danych. 

Podobnie jak w przypadku wdrożeń bez systemu operacyjnego, wydajność tworzenia kopii zapasowej/przywracania zależy od liczby woluminów, które mogą być jednocześnie odczytywane i co może być przepływność tych woluminów. Ponadto użycie procesora CPU używane przez kompresję kopii zapasowej może odgrywać znaczącą rolę na maszynach wirtualnych z maksymalnie ośmiu wątkami procesora. W związku z tym można założyć, że:

* Im mniejsza liczba dysków używanych do przechowywania urządzeń bazy danych, tym mniejsza ogólna przepływność podczas czytania
* Im mniejsza liczba wątków procesora w maszynie wirtualnej, tym trudniejsze jest wpływ kompresji kopii zapasowej.
* Mniejsza liczba elementów docelowych (katalogów rozłożonych, dysków), do których ma zostać zapisana kopia zapasowa, im niższa przepływność

Aby zwiększyć liczbę obiektów docelowych do zapisu, w zależności od potrzeb można użyć dwóch opcji/łącznie:

* Rozłożenie woluminu docelowego kopii zapasowej na wiele dysków w celu poprawienia przepływności operacji we/wy na woluminie rozłożonym
* Zapisywanie kopii zapasowej za pomocą więcej niż jednego katalogu docelowego

>[!NOTE]
>Program DB2 w systemie Windows nie obsługuje technologii VSS systemu Windows. W związku z tym nie można użyć kopii zapasowej maszyny wirtualnej spójnej na poziomie aplikacji Azure Backup usługi dla maszyn wirtualnych z systemem DB2 DBMS.

### <a name="high-availability-and-disaster-recovery"></a>Wysoka dostępność i odzyskiwanie po awarii

#### <a name="linux-pacemaker"></a>Pacemaker systemu Linux

Obsługiwane jest odzyskiwanie po awarii wysokiej dostępności bazy danych DB2 (HADR cluster) z Pacemaker. Obsługiwane są zarówno systemy operacyjne SLES, jak i RHEL. Ta konfiguracja umożliwia wysoką dostępność programu IBM DB2 dla oprogramowania SAP. Przewodniki wdrażania:
* SLES: [wysoka dostępność programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server z usługą Pacemaker](dbms-guide-ha-ibm.md) 
* RHEL: [wysoka dostępność programu IBM DB2 LUW na maszynach wirtualnych platformy Azure na serwerze Red Hat Enterprise Linux](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Serwer klastra systemu Windows

Serwer klastrów firmy Microsoft (MSCS) nie jest obsługiwany.

Obsługiwane jest odzyskiwanie po awarii o wysokiej dostępności (HADR cluster) programu DB2. Jeśli maszyny wirtualne w konfiguracji HA mają działające rozpoznawanie nazw, instalacja na platformie Azure nie różni się od żadnej instalacji wykonywanej lokalnie. Nie zaleca się korzystania tylko z rozpoznawania adresów IP.

Nie należy używać Geo-Replication dla kont magazynu przechowujących dyski bazy danych. Aby uzyskać więcej informacji, zobacz [zagadnienia dotyczące dokumentu dotyczące wdrażania platformy Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). 

### <a name="accelerated-networking"></a>Accelerated Networking
W przypadku wdrożeń programu DB2 w systemie Windows zdecydowanie zaleca się korzystanie z funkcji systemu Azure przyspieszonej sieci zgodnie z opisem w dokumencie " [przyspieszona sieć platformy Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Należy również wziąć pod uwagę zalecenia dotyczące [wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md). 


### <a name="specifics-for-linux-deployments"></a>Specyficzne dla wdrożeń systemu Linux
Tak długo, jak bieżąca liczba operacji we/wy na dysk jest wystarczająca, możliwe jest przechowywanie wszystkich plików bazy danych na jednym dysku. Należy zawsze oddzielić pliki danych i pliki dziennika transakcji na różnych dyskach/VHD.

Alternatywnie, jeśli przepustowość operacji we/wy na pojedynczym dysku VHD platformy Azure nie jest wystarczająca, można użyć LVM (Menedżer woluminów logicznych) lub MDADM, zgodnie z opisem w artykule [uwagi dotyczące wdrażania systemu azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md) w celu utworzenia jednego dużego urządzenia logicznego na wielu dyskach.
W przypadku dysków zawierających ścieżki magazynu DB2 dla katalogów sapdata i saptmp należy określić rozmiar sektora dysku fizycznego wynoszący 512 KB.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Inne
Wszystkie inne obszary ogólne, takie jak zestawy dostępności platformy Azure lub monitorowanie SAP, mają zastosowanie zgodnie z opisem w dokumencie [zagadnienia dotyczące wdrożenia systemu azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md) na potrzeby wdrożeń maszyn wirtualnych z bazą danych firmy IBM.

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
Zapoznaj się z artykułem 

- [Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP](dbms_guide_general.md)

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

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