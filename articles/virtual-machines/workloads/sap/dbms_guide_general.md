---
title: Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP | Microsoft Docs
description: Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP
author: msjuergent
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 09/20/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: e978baa556f1bc1cfea0ccd5abbbf92dc77b1622
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504018"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Zagadnienia dotyczące wdrażania systemu Azure Virtual Machines DBMS dla obciążeń SAP
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



Ten przewodnik jest częścią dokumentacji dotyczącej sposobu wdrażania i wdrażania oprogramowania SAP na Microsoft Azure. Przed przeczytaniem tego przewodnika Przeczytaj [Przewodnik planowania i implementacji][planning-guide] oraz artykuły z przewodnikiem planowania. Ten dokument zawiera opis aspektów wdrożenia ogólnych systemów DBMS związanych z systemem SAP na Microsoft Azure maszynach wirtualnych za pomocą funkcji infrastruktury platformy Azure jako usługi (IaaS).

Ten dokument uzupełnia dokumentację instalacji SAP i uwagi SAP, które reprezentują podstawowe zasoby dotyczące instalacji i wdrożeń oprogramowania SAP na określonych platformach.

W tym dokumencie przedstawiono zagadnienia dotyczące uruchamiania systemów DBMS związanych z systemem SAP na maszynach wirtualnych platformy Azure. W tym rozdziale istnieje kilka odwołań do określonych systemów DBMS. Zamiast tego dokumentu w tym dokumencie są obsługiwane określone systemy DBMS.

## <a name="definitions"></a>Definicje
W całym dokumencie są używane następujące warunki:

* **IaaS**: infrastruktura jako usługa.
* **PaaS**: platforma jako usługa.
* **SaaS**: oprogramowanie jako usługa.
* **Składnik SAP**: indywidualna aplikacja SAP, taka jak ERP Central Component (ECC), Business Warehouse (BW), Menedżer rozwiązań lub Enterprise Portal (EP). Składniki SAP mogą opierać się na tradycyjnych technologiach ABAP lub Java lub w aplikacjach opartych na innej NetWeaver, takich jak obiekty biznesowe.
* **Środowisko SAP**: co najmniej jeden składnik SAP logicznie zgrupowany do wykonywania funkcji biznesowej, takich jak programowanie, gwarancja jakości, szkolenia, odzyskiwanie po awarii lub produkcja.
* **SAP krajobraz**: ten termin odnosi się do całego zasobu SAP w poziomie klienta IT. System SAP zawiera wszystkie środowiska produkcyjne i nieprodukcyjne.
* **System SAP**: kombinacja warstwy DBMS i warstwy aplikacji programu, na przykład systemu PROGRAMISTYCZNEGO SAP ERP, systemu testowego programu SAP Business Warehouse lub systemu produkcyjnego SAP CRM. W przypadku wdrożeń platformy Azure dzielenie tych dwóch warstw między środowiskiem lokalnym i platformą Azure nie jest obsługiwane. W efekcie system SAP został wdrożony lokalnie lub wdrożony na platformie Azure. Różne systemy oprogramowania SAP można wdrożyć na platformie Azure lub lokalnie. Można na przykład wdrożyć systemy deweloperskie i testowe oprogramowania SAP CRM na platformie Azure, ale wdrożyć system produkcyjny SAP CRM lokalnie.
* **Wiele** lokalizacji: opisuje scenariusz, w którym maszyny wirtualne są wdrażane w ramach subskrypcji platformy Azure, która ma łączność między lokacjami, w wielu lokacjach lub na platformie Azure ExpressRoute między lokalnymi centrami danych i platformą Azure. W typowej dokumentacji platformy Azure te rodzaje wdrożeń są również opisane jako scenariusze obejmujące wiele lokalizacji. 

    Przyczyną połączenia jest rozszerzonie domen lokalnych, lokalnych Active Directory i lokalnych nazw DNS na platformę Azure. Lokalne poziomy są rozszerzane do zasobów platformy Azure w ramach subskrypcji. Dzięki temu rozszerzeniu maszyny wirtualne mogą być częścią domeny lokalnej. Użytkownicy domeny lokalnej mogą uzyskiwać dostęp do serwerów i uruchamiać usługi na tych maszynach wirtualnych, takich jak usługi DBMS. Możliwa jest komunikacja i rozpoznawanie nazw między maszynami wirtualnymi wdrożonymi lokalnie i maszynami wirtualnymi wdrożonymi na platformie Azure. Ten scenariusz jest najpopularniejszym scenariuszem używanym do wdrażania zasobów SAP na platformie Azure. Aby uzyskać więcej informacji, zobacz [Planowanie i projektowanie dla bramy sieci VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md).

> [!NOTE]
> W przypadku wdrożeń obejmujących wiele lokalizacji systemów SAP usługi Azure Virtual Machines z systemami SAP są członkami domeny lokalnej i są obsługiwane przez produkcyjne systemy SAP. Konfiguracje obejmujące wiele lokalizacji są obsługiwane w przypadku wdrażania części lub kompletnego rozwiązania SAP Landscapes na platformie Azure. Nawet w przypadku korzystania z pełnej krajobrazu SAP na platformie Azure wymagane są te maszyny wirtualne, które będą częścią domeny lokalnej i Active Directory/LDAP. 
>
> W poprzednich wersjach dokumentacji zostały wymienione scenariusze hybrydowe. Termin " *hybrydowe* " jest głównym elementem w przypadku, gdy istnieje połączenie między środowiskiem lokalnym i platformą Azure. W tym przypadku hybrydowego oznacza również, że maszyny wirtualne na platformie Azure są częścią Active Directory lokalnego.
>
>

Niektóre dokumenty firmy Microsoft opisują scenariusze obejmujące wiele lokalizacji inaczej, szczególnie w przypadku konfiguracji o wysokiej dostępności w systemie DBMS. W przypadku dokumentów związanych z systemem SAP scenariusz obejmujący wiele lokalizacji jest zachodzący do połączenia typu lokacja-lokacja lub prywatnego [ExpressRoute](https://azure.microsoft.com/services/expressroute/) oraz oprogramowania SAP, które jest dystrybuowane między środowiskiem lokalnym i platformą Azure.

## <a name="resources"></a>Zasoby
Dostępne są inne artykuły dotyczące obciążeń SAP na platformie Azure. Zacznij od [obciążenia SAP na platformie Azure: Zacznij pracę](./get-started.md) , a następnie wybierz interesujący Cię obszar.

Poniższe uwagi dotyczące oprogramowania SAP są powiązane z oprogramowaniem SAP na platformie Azure w odniesieniu do obszaru objętego tym dokumentem.

| Numer notatki | Tytuł |
| --- | --- |
| [1928533] |Aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure |
| [2015553] |SAP on Microsoft Azure: wymagania wstępne dotyczące pomocy technicznej |
| [1999351] |Rozwiązywanie problemów z ulepszonym monitorowaniem platformy Azure dla oprogramowania SAP |
| [2178632] |Metryki monitorowania kluczy dla SAP na Microsoft Azure |
| [1409604] |Wirtualizacja w systemie Windows: ulepszone monitorowanie |
| [2191498] |SAP w systemie Linux z platformą Azure: ulepszone monitorowanie |
| [2039619] |Aplikacje SAP na Microsoft Azure przy użyciu bazy danych Oracle: obsługiwane produkty i wersje |
| [2233094] |DB6: Aplikacje SAP na platformie Azure korzystające z programu IBM DB2 dla systemów Linux, UNIX i Windows: informacje dodatkowe |
| [2243692] |Maszyna wirtualna z systemem Linux na Microsoft Azure (IaaS): problemy z licencją SAP |
| [1984787] |SUSE LINUX Enterprise Server 12: uwagi dotyczące instalacji |
| [2002167] |Red Hat Enterprise Linux 7. x: Instalowanie i uaktualnianie |
| [2069760] |Oracle Linux 7. x instalacja i uaktualnienie SAP |
| [1597355] |Zalecenie wymiany miejsca dla systemu Linux |
| [2171857] |Oracle Database 12c: obsługa systemu plików w systemie Linux |
| [1114181] |Oracle Database 11g: obsługa systemu plików w systemie Linux |


Aby uzyskać więcej informacji na temat wszystkich uwag SAP dla systemu Linux, zobacz witrynę [typu wiki społeczności SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Potrzebna jest praktyczna wiedza o architekturze Microsoft Azure i Microsoft Azure sposobie wdrażania i obsługi maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [dokumentację platformy Azure](../../../index.yml).

Ogólnie rzecz biorąc instalacja i konfiguracja systemu Windows, Linux i DBMS jest taka sama jak dowolna maszyna wirtualna lub komputer bez systemu operacyjnego instalowany lokalnie. Istnieją pewne decyzje dotyczące architektury i zarządzania systemem, które różnią się w przypadku korzystania z usługi Azure IaaS. W tym dokumencie wyjaśniono specyficzne różnice w zakresie architektury i zarządzania systemem, które należy przygotować w przypadku korzystania z usługi Azure IaaS.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Struktura magazynu maszyny wirtualnej na potrzeby wdrożeń RDBMS
Aby postępować zgodnie z tym rozdziałem, przeczytaj i zapoznaj się z informacjami przedstawionymi w temacie:

- [Planowanie i wdrażanie Virtual Machines platformy Azure dla oprogramowania SAP NetWeaver](./planning-guide.md)
- [Typy usługi Azure Storage dla obciążeń SAP](./planning-guide-storage.md)
- [Jakie oprogramowanie SAP jest obsługiwane w przypadku wdrożeń platformy Azure](./sap-supported-product-on-azure.md)
- [Obsługiwane scenariusze obciążenia SAP na maszynie wirtualnej na platformie Azure](./sap-planning-supported-configurations.md) 

Przed przeczytaniem tego rozdziału należy zrozumieć i poznać różne VM-Series i różnice między magazynem w warstwie Standardowa i Premium. 

W przypadku magazynu blokowego platformy Azure użycie usługi Azure Managed disks jest zdecydowanie zalecane. Aby uzyskać szczegółowe informacje na temat usługi Azure Managed disks, przeczytaj artykuł [wprowadzenie do dysków zarządzanych dla maszyn wirtualnych platformy Azure](../../managed-disks-overview.md).

W konfiguracji podstawowej zwykle zalecamy strukturę wdrożenia, w której system operacyjny, DBMS i ostateczne pliki binarne SAP są oddzielone od plików bazy danych. W przypadku zmiany wcześniejszych zaleceń zalecamy korzystanie z oddzielnych dysków platformy Azure dla:

- System operacyjny (podstawowy dysk VHD lub wirtualny dysk twardy systemu operacyjnego)
- Pliki wykonywalne systemu zarządzania bazami danych
- Pliki wykonywalne SAP, takie jak/usr/SAP

Konfiguracja, która oddziela te składniki na trzech różnych dyskach platformy Azure, może spowodować zwiększenie odporności, ponieważ nadmierne zapisy dziennika lub zrzutu za pomocą plików wykonywalnych systemu DBMS lub SAP nie zakłócają przydziałów dysku dysku system operacyjnego. 

Pliki dziennika danych i transakcji/ponownego wykonywania w systemie DBMS są przechowywane na platformie Azure obsługiwanej przez magazyn blokowy lub Azure NetApp Files. Są one przechowywane na oddzielnych dyskach i dołączane jako dyski logiczne do oryginalnej maszyny wirtualnej obrazu systemu operacyjnego platformy Azure. W przypadku wdrożeń systemu Linux są udokumentowane różne zalecenia, szczególnie w przypadku SAP HANA. Zapoznaj się z artykułem [typy magazynów platformy Azure dla obciążeń SAP](./planning-guide-storage.md) , aby uzyskać informacje o możliwościach i obsłudze różnych typów magazynu dla danego scenariusza. 

Podczas planowania układu dysku Znajdź najlepszą równowagę między tymi elementami:

* Liczba plików danych.
* Liczba dysków, które zawierają pliki.
* Przydziały operacji we/wy jednego dysku lub udziału NFS.
* Przepływność danych na dysk lub udział NFS.
* Liczba dodatkowych dysków danych możliwych dla rozmiaru maszyny wirtualnej.
* Ogólna przepustowość magazynu lub sieci możliwa do udostępnienia przez maszynę wirtualną.
* Możliwe jest opóźnienie różnych typów magazynu platformy Azure.
* Umowy SLA maszyny wirtualnej.

Platforma Azure wymusza przydział operacji we/wy na dysk danych lub udział NFS. Te przydziały różnią się w przypadku dysków hostowanych w różnych rozwiązaniach lub udziałach magazynu blokowego platformy Azure. Opóźnienie we/wy jest również różne dla różnych typów magazynów. 

Każdy z różnych typów maszyn wirtualnych ma ograniczoną liczbę dysków z danymi, które można dołączyć. Inne ograniczenie polega na tym, że tylko niektóre typy maszyn wirtualnych mogą korzystać z usługi Premium Storage. Zazwyczaj użytkownik zdecyduje się użyć określonego typu maszyny wirtualnej na podstawie wymagań procesora i pamięci. Można też uwzględnić wymagania operacji we/wy, opóźnienia i przepływności dysku, które zwykle są skalowane z liczbą dysków lub typem dysków magazynu w warstwie Premium. Liczba operacji we/wy na sekundę i przepływność do osiągnięcia przez każdy dysk może oznaczać rozmiar dysku, szczególnie w przypadku magazynu w warstwie Premium.

> [!NOTE]
> W przypadku wdrożeń w systemie DBMS zalecamy korzystanie z usługi Azure Premium Storage, Ultra Disk lub Azure NetApp Files opartych na systemie plików NFS (przeznaczonych wyłącznie dla SAP HANA) dla dowolnych danych, dzienników transakcji lub plików ponawiania. Nie ma znaczenia, czy chcesz wdrożyć systemy produkcyjne lub nieprodukcyjne.

> [!NOTE]
> Aby skorzystać z [umowy SLA dla jednej maszyny wirtualnej](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)platformy Azure, wszystkie dołączone dyski muszą być typu Azure Premium Storage lub Azure Ultra Disk, który obejmuje podstawowy dysk VHD (Azure Premium Storage).

> [!NOTE]
> Hostowanie plików głównych baz danych, takich jak pliki danych i dziennika, z baz danych SAP na sprzęcie magazynu znajdujących się w wspólnie zlokalizowanych centrach danych innych firm sąsiadujących z centrami danych platformy Azure nie jest obsługiwane. Magazyn udostępniony przez urządzenia działające na maszynach wirtualnych platformy Azure nie jest również obsługiwany w przypadku tego przypadku użycia. W przypadku obciążeń SAP DBMS tylko magazyn reprezentowany jako natywna usługa platformy Azure jest obsługiwany dla plików dziennika transakcji i baz danych SAP. Różne systemy DBMS mogą obsługiwać różne typy magazynów platformy Azure. Aby uzyskać więcej szczegółów, zobacz artykuł [typy magazynów platformy Azure dla obciążeń SAP](./planning-guide-storage.md)

Umieszczanie plików bazy danych oraz plików dziennika i wykonaj ponownie oraz typu używanej usługi Azure Storage jest zdefiniowane przez operacje we/wy, opóźnienia i przepływność. W przypadku usługi Azure Premium Storage w celu osiągnięcia wystarczającej liczby operacji we/wy można wymusić użycie wielu dysków lub użyć większego dysku magazynu Premium Storage. W przypadku korzystania z wielu dysków należy skompilować pakiet oprogramowania na dyskach zawierających pliki danych lub pliki dziennika i wykonaj ponownie. W takich przypadkach liczba operacji we/wy na sekundę (umowy SLA) dysków magazynu w warstwie Premium lub maksymalna liczba operacji we/wy na sekundę dysków magazynu w warstwie Standardowa jest kumulowana dla zestawu.

Jeśli wymaganie IOPS przekroczy wartość jednego wirtualnego dysku twardego, można zrównoważyć liczbę operacji we/wy, która jest wymagana dla plików bazy danych w ramach wielu wirtualnych dysków twardych. Najprostszym sposobem na rozproszenie obciążenia IOPS między dyskami jest skompilowanie oprogramowania na różnych dyskach. Następnie należy umieścić kilka plików danych systemu SAP DBMS na jednostkach LUN używać miejsca z paska oprogramowania. Liczba dysków w rozłożonej jest obsługiwana przez żądania IOPS, wymagania dotyczące przepływności dysku i zapotrzebowania na wolumin.


---
> ![Rozłożenie magazynu systemu Windows][Logo_Windows] Windows
>
> Zalecamy używanie funkcji miejsca do magazynowania systemu Windows do tworzenia zestawów rozłożonych w wielu wirtualnych dyskach twardych platformy Azure. Użyj co najmniej systemu Windows Server 2012 R2 lub Windows Server 2016.
>
> ![Rozłożenie magazynu systemu Linux][Logo_Linux] Linux
>
> Tylko MDADM i Logical Volume Manager (LVM) są obsługiwane do kompilowania oprogramowania RAID w systemie Linux. Aby uzyskać więcej informacji, zobacz:
>
> - [Konfigurowanie RAID oprogramowania w systemie Linux](/previous-versions/azure/virtual-machines/linux/configure-raid) przy użyciu MDADM
> - [Konfigurowanie LVM na maszynie wirtualnej z systemem Linux na platformie Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm) przy użyciu usługi LVM
>
>

---

W przypadku usługi Azure Ultra Disk rozłożenie nie jest konieczne, ponieważ można definiować operacje we/wy na dysku niezależnie od rozmiaru dysku.


> [!NOTE]
> Ponieważ usługa Azure Storage przechowuje trzy obrazy wirtualnych dysków twardych, nie ma sensu konfigurowania nadmiarowości podczas rozłożenia. Wystarczy tylko skonfigurować rozłożenie, aby zapewnić dystrybucję I/OS przy użyciu różnych wirtualnych dysków twardych.
>

### <a name="managed-or-nonmanaged-disks"></a>Dyski zarządzane lub niezarządzane
Konto usługi Azure Storage to konstrukcja administracyjna, która również podlega ograniczeniom. Ograniczenia różnią się w zależności od kont magazynu w warstwie Standardowa i kont magazynu w warstwie Premium. Aby uzyskać informacje o możliwościach i ograniczeniach, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../../../storage/common/scalability-targets-standard-account.md).

W przypadku magazynu w warstwie Standardowa należy pamiętać, że istnieje limit liczby operacji we/wy na konto magazynu. Zapoznaj się z wierszem zawierającym **łączną liczbę żądań** w artykule dotyczące [skalowalności i wydajności usługi Azure Storage](../../../storage/common/scalability-targets-standard-account.md). Istnieje również początkowy limit liczby kont magazynu na subskrypcję platformy Azure. Równoważ wirtualne dyski twarde dla większych poziomów oprogramowania SAP na różnych kontach magazynu, aby uniknąć osiągnięcia limitów tych kont magazynu. To żmudnym działa, gdy mówisz kilka tysięcy maszyn wirtualnych z więcej niż tysiące dysków VHD.

Nie zaleca się używania magazynu w warstwie Standardowa dla wdrożeń systemu DBMS w połączeniu z obciążeniem SAP. W związku z tym odwołania i zalecenia dotyczące magazynu w warstwie Standardowa są ograniczone do tego krótkiego [artykułu](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)

Aby uniknąć czynności administracyjnych planowania i wdrażania dysków VHD na różnych kontach usługi Azure Storage, firma Microsoft wprowadziła [Managed disks platformy Azure](https://azure.microsoft.com/services/managed-disks/) w 2017. Dyski zarządzane są dostępne dla magazynu w warstwie Standardowa i Premium Storage. Główne zalety dysków zarządzanych w porównaniu z niezarządzanymi dyskami są następujące:

- W przypadku dysków zarządzanych platforma Azure automatycznie dystrybuuje różne dyski VHD na różne konta magazynu w czasie wdrażania. W ten sposób limity konta magazynu dla ilości danych, przepływności we/wy i IOPS nie trafią.
- Korzystając z usługi Managed disks, usługa Azure Storage uwzględnia koncepcje zestawów dostępności platformy Azure. Jeśli maszyna wirtualna jest częścią zestawu dostępności platformy Azure, podstawowy dysk VHD i dołączony dysk maszyny wirtualnej są wdrażane w różnych domenach błędów i aktualizacji.


> [!IMPORTANT]
> Mając na względzie zalety platformy Azure Managed Disks, zdecydowanie zalecamy korzystanie z usługi Azure Managed Disks na potrzeby wdrożeń systemu DBMS i wdrożeń SAP.
>

Aby skonwertować z dysków niezarządzanych do usługi Managed disks, zobacz:

- [Przekonwertuj maszynę wirtualną z systemem Windows z dysków niezarządzanych na dyski zarządzane](../../windows/convert-unmanaged-to-managed-disks.md).
- [Przekonwertuj maszynę wirtualną z systemem Linux z dysków niezarządzanych na dyski zarządzane](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Buforowanie maszyn wirtualnych i dysków z danymi
Podczas instalowania dysków na maszynach wirtualnych można wybrać, czy ruch we/wy między maszyną wirtualną a tymi dyskami znajdującymi się w usłudze Azure Storage jest buforowany.

W następujących zaleceniach przyjęto założenie, że te cechy we/wy dla standardowego systemu DBMS:

- Jest to przede wszystkim obciążenie dotyczące plików danych bazy danych. Te odczyty mają krytyczne znaczenie dla systemu DBMS.
- Zapisywanie względem plików danych odbywa się na podstawie punktów kontrolnych lub strumienia stałego. Średnia w ciągu dnia obejmuje mniejszą liczbę operacji zapisu niż odczyt. Przeciwieństwem do odczytu z plików danych, te zapisy są asynchroniczne i nie przechowują żadnych transakcji użytkownika.
- Istnieją na stałe operacje odczytu z dziennika transakcji lub ponownego wykonywania plików. Wyjątki to duże operacje I/OS w przypadku wykonywania kopii zapasowych dziennika transakcji.
- Głównym obciążeniem dla transakcji lub plików dziennika ponownego wykonywania jest zapis. Zależnie od charakteru obciążenia można korzystać z operacji we/wy o rozmiarze 4 KB lub w innych przypadkach, w których rozmiary operacji wejścia/wyjścia wynoszą 1 MB lub więcej.
- Wszystkie zapisy muszą być utrwalone na dysku w niezawodny sposób.

W przypadku magazynu w warstwie Standardowa możliwe są następujące typy pamięci podręcznej:

* Brak
* Odczyt
* Odczyt/Zapis

Aby uzyskać spójną i deterministyczną wydajność, ustaw buforowanie w magazynie w warstwie Standardowa dla wszystkich dysków, które zawierają pliki danych związanych z systemem DBMS, Rejestruj i wykonaj ponownie pliki, a następnie wybierz pozycję **Brak**. Buforowanie podstawowego wirtualnego dysku twardego może pozostać z wartością domyślną.

W przypadku usługi Azure Premium Storage istnieją następujące opcje buforowania:

* Brak
* Odczyt
* Odczyt/zapis
* Brak i akcelerator zapisu, które są przeznaczone tylko dla maszyn wirtualnych z serii M platformy Azure
* Read + akcelerator zapisu, która jest tylko dla maszyn wirtualnych z serii M platformy Azure

W przypadku magazynu w warstwie Premium zalecamy użycie **buforowania odczytu plików danych** w bazie danych SAP i wybranie opcji **Brak buforowania dla dysków plików dziennika**.

W przypadku wdrożeń z serii M zalecamy używanie akcelerator zapisu platformy Azure do wdrożenia w systemie DBMS. Aby uzyskać szczegółowe informacje, ograniczenia i wdrożenie usługi Azure akcelerator zapisu, zobacz [włączanie akcelerator zapisu](../../how-to-enable-write-accelerator.md).

W przypadku Ultra Disk i Azure NetApp Files nie są oferowane żadne opcje buforowania.


### <a name="azure-nonpersistent-disks"></a>Dyski nietrwałe platformy Azure
Maszyny wirtualne platformy Azure oferują dyski nietrwałe po wdrożeniu maszyny wirtualnej. W przypadku ponownego uruchomienia maszyny wirtualnej cała zawartość na tych dyskach zostanie wyczyszczona. Ma to miejsce, że pliki danych i dziennik i wykonaj ponownie pliki baz danych nie powinny znajdować się na tych dyskach nieutrwalonych. Mogą istnieć wyjątki dla niektórych baz danych, w przypadku których te dyski nieutrwalone mogą być odpowiednie dla tempdb i tymczasowych tabel przewidzianych. Należy unikać używania tych dysków dla maszyn wirtualnych z serii A, ponieważ dyski nieutrwalone są ograniczone w przepływności z tą rodziną maszyn wirtualnych. 

Aby uzyskać więcej informacji, zobacz [opis dysku tymczasowego na maszynach wirtualnych z systemem Windows na platformie Azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines).

---
> ![Nieutrwalony dysk systemu Windows][Logo_Windows] Windows
>
> Dysk D na maszynie wirtualnej platformy Azure to nieutrwalony dysk, który jest obsługiwany przez niektóre dyski lokalne w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalona, wszelkie zmiany wprowadzone do zawartości na dysku D są tracone po ponownym uruchomieniu maszyny wirtualnej. Zmiany obejmują zapisane pliki, katalogi, które zostały utworzone, oraz zainstalowane aplikacje.
>
> ![Dysk Linuxnonpersisted][Logo_Linux] Linux
>
> Maszyny wirtualne platformy Azure z systemem Linux automatycznie instalują dysk w/mnt/Resource, który jest nieutrwalonym dyskiem z kopii zapasowej dysków lokalnych w węźle obliczeniowym platformy Azure. Ponieważ nie jest utrwalona, wszelkie zmiany wprowadzone w zawartości/mnt/Resource są tracone po ponownym uruchomieniu maszyny wirtualnej. Zmiany obejmują zapisane pliki, katalogi, które zostały utworzone, oraz zainstalowane aplikacje.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Odporność Microsoft Azure Storage
Microsoft Azure Storage przechowuje podstawowy wirtualny dysk twardy z systemem operacyjnym i dołączonymi dyskami lub obiektami BLOB na co najmniej trzech oddzielnych węzłach magazynu. Ten typ magazynu jest nazywany magazynem lokalnie nadmiarowy (LRS). LRS jest wartością domyślną dla wszystkich typów magazynów na platformie Azure.

Istnieją inne metody nadmiarowości. Aby uzyskać więcej informacji, zobacz [Replikacja usługi Azure Storage](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
> Azure Premium Storage, Ultra Disk i Azure NetApp Files (przeznaczone wyłącznie dla SAP HANA) są zalecanym typem magazynu dla maszyn wirtualnych systemu DBMS i dysków, które przechowują bazę danych i pliki dziennika i wykonaj ponownie. Jedyną dostępną metodą nadmiarowości dla tych typów magazynu jest LRS. W związku z tym należy skonfigurować metody bazy danych w celu włączenia replikacji danych bazy danych do innego regionu platformy Azure lub strefy dostępności. Metody bazy danych obejmują SQL Server zawsze włączone, Oracle Data Guard i HANA system Replication.


> [!NOTE]
> W przypadku wdrożeń systemu DBMS użycie magazynu geograficznie nadmiarowego (GRS) nie jest zalecane w przypadku magazynu w warstwie Standardowa. GRS istotnie wpływa na wydajność i nie uwzględnia kolejności zapisu w różnych dyskach VHD, które są dołączone do maszyny wirtualnej. Nieuznawanie kolejności zapisu w różnych dyskach VHD potencjalnie prowadzi do niespójnych baz danych na stronie docelowej replikacji. Ta sytuacja występuje, gdy baza danych i plik dziennika i wykonaj ponownie są rozłożone na wiele wirtualnych dysków twardych, tak jak zwykle w przypadku po stronie źródłowej maszyny wirtualnej.



## <a name="vm-node-resiliency"></a>Odporność węzła maszyny wirtualnej
Platforma Azure oferuje kilka różnych umowy SLA dla maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz Najnowsza wersja [umowy SLA dla Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Ponieważ warstwa DBMS ma krytyczne znaczenie dla dostępności w systemie SAP, należy zrozumieć zestawy dostępności, Strefy dostępności i zdarzenia konserwacji. Aby uzyskać więcej informacji na temat tych pojęć, zobacz [Zarządzanie dostępnością maszyn wirtualnych z systemem Windows na platformie Azure](../../availability.md) i [Zarządzanie dostępnością maszyn wirtualnych z systemem Linux na platformie Azure](../../availability.md).

Minimalnym zaleceniem dla produkcyjnych scenariuszy systemu DBMS z obciążeniem SAP jest:

- Wdróż dwie maszyny wirtualne w osobnym zestawie dostępności w tym samym regionie świadczenia usługi Azure.
- Uruchom te dwie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure i załączono karty sieciowe z tymi samymi podsieciami.
- Użyj metod baz danych, aby zachować rezerwę dynamiczną z drugą maszyną wirtualną. Metody mogą być SQL Server zawsze włączone, funkcja Oracle Data Guard lub replikacja systemu HANA.

Możesz również wdrożyć trzecią maszynę wirtualną w innym regionie świadczenia usługi Azure i użyć tych samych metod bazy danych, aby dostarczyć replikę asynchroniczną w innym regionie świadczenia usługi Azure.

Aby uzyskać informacje na temat konfigurowania zestawów dostępności platformy Azure, zobacz [ten samouczek](../../windows/tutorial-availability-sets.md).



## <a name="azure-network-considerations"></a>Zagadnienia dotyczące sieci platformy Azure
W przypadku wdrożeń SAP w dużej skali należy skorzystać z strategii [wirtualnego centrum danych platformy Azure](/azure/architecture/vdc/networking-virtual-datacenter). Służy do konfigurowania sieci wirtualnej i uprawnień oraz przypisywania ról do różnych części organizacji.

Te najlepsze rozwiązania są wynikiem setek wdrożeń klientów:

- Sieci wirtualne, w których wdrażana jest aplikacja SAP, nie mają dostępu do Internetu.
- Maszyny wirtualne bazy danych działają w tej samej sieci wirtualnej co warstwa aplikacji, oddzielone w innej podsieci od warstwy aplikacji SAP.
- Maszyny wirtualne w sieci wirtualnej mają statyczną alokację prywatnego adresu IP. Aby uzyskać więcej informacji, zobacz [typy adresów IP i metody alokacji na platformie Azure](../../../virtual-network/public-ip-addresses.md).
- Ograniczenia routingu do i z maszyn wirtualnych systemu DBMS *nie* są ustawiane za pomocą zapór zainstalowanych na lokalnych maszynach wirtualnych systemu DBMS. Zamiast tego Routing ruchu jest definiowany przy użyciu [sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../../virtual-network/network-security-groups-overview.md).
- Aby oddzielić ruch do maszyny wirtualnej systemu DBMS i go odizolować, przypisz do niej różne karty sieciowe. Każda karta sieciowa pobiera inny adres IP, a każda karta sieciowa jest przypisana do innej podsieci sieci wirtualnej. Każda podsieć ma inne reguły sieciowej grupy zabezpieczeń. Izolacja lub separacja ruchu sieciowego jest miarą routingu. Nie jest on używany do ustawiania przydziałów dla przepływności sieci.

> [!NOTE]
> Przypisywanie statycznych adresów IP za pośrednictwem platformy Azure oznacza przypisanie ich do poszczególnych wirtualnych kart sieciowych. Nie przypisuj statycznych adresów IP w systemie operacyjnym gościa do wirtualnej karty sieciowej. Niektóre usługi platformy Azure, takie jak Azure Backup, korzystają z faktu, że co najmniej podstawowa wirtualna karta sieciowa jest ustawiona na wartość DHCP, a nie na statyczne adresy IP. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z kopiami zapasowymi maszyny wirtualnej platformy Azure](../../../backup/backup-azure-vms-troubleshoot.md#networking). Aby przypisać wiele statycznych adresów IP do maszyny wirtualnej, przypisz wiele wirtualnych kart sieciowych do maszyny wirtualnej.
>


> [!WARNING]
> Konfigurowanie [sieciowych urządzeń wirtualnych](https://azure.microsoft.com/solutions/network-appliances/) w ścieżce komunikacji między aplikacją SAP a warstwą DBMS systemu SAP NetWeaver-, Hybris-lub S/4HANA nie jest obsługiwane. To ograniczenie dotyczy przyczyn związanych z funkcjonalnością i wydajnością. Ścieżka komunikacji między warstwą aplikacji SAP a warstwą DBMS musi być jedną z nich. Ograniczenie nie obejmuje [grupy zabezpieczeń aplikacji (ASG) i reguł sieciowej grupy zabezpieczeń](../../../virtual-network/network-security-groups-overview.md) , jeśli te reguły ASG i sieciowej grupy zabezpieczeń umożliwiają bezpośrednią ścieżkę komunikacji. 
>
> Inne scenariusze, w których nie są obsługiwane wirtualne urządzenia sieciowe, są następujące:
>
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure, które reprezentują węzły klastra Pacemaker systemu Linux i urządzenia SBD, zgodnie z opisem w temacie [wysoka dostępność dla oprogramowania SAP NetWeaver na maszynach wirtualnych platformy Azure na SUSE Linux Enterprise Server aplikacji SAP](./high-availability-guide-suse.md).
> * Ścieżki komunikacji między maszynami wirtualnymi platformy Azure a serwerem plików Scale-Out systemu Windows Server (SOFS) skonfigurowano zgodnie z opisem w artykule [klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Sieciowe urządzenia wirtualne w ścieżkach komunikacyjnych mogą łatwo podwójnie opóźnić opóźnienia sieci między dwoma partnerami komunikacji. Mogą również ograniczyć przepływność w ścieżkach krytycznych między warstwą aplikacji SAP a warstwą DBMS. W niektórych scenariuszach klientów wirtualne urządzenia sieciowe mogą spowodować awarię klastrów Pacemaker systemu Linux. Są to przypadki, w których komunikacja między węzłami klastra Pacemaker systemu Linux komunikuje się z urządzeniem SBD za pośrednictwem sieciowego urządzenia wirtualnego.
>

> [!IMPORTANT]
> Innym *nieobsługiwanym* projektem jest podział warstwy aplikacji SAP i warstwy DBMS na różne sieci wirtualne platformy Azure, które nie są połączone za pomocą [komunikacji równorzędnej](../../../virtual-network/virtual-network-peering-overview.md) . Zalecamy rozdzielenie warstwy aplikacji SAP i systemu DBMS przy użyciu podsieci w sieci wirtualnej platformy Azure, a nie za pomocą różnych sieci wirtualnych platformy Azure. 
>
> Jeśli zdecydujesz się nie przestrzegać rekomendacji, a zamiast tego rozdzielić dwie warstwy na różne sieci wirtualne, muszą być połączone za pomocą [komunikacji równorzędnej](../../../virtual-network/virtual-network-peering-overview.md). 
>
> Należy pamiętać, że ruch sieciowy między dwiema [równorzędnymi](../../../virtual-network/virtual-network-peering-overview.md) sieciami wirtualnymi platformy Azure podlega kosztom transferu. Duże ilości danych składające się z wielu terabajtów są wymieniane między warstwą aplikacji SAP a warstwą DBMS. W przypadku podzielenia między dwiema równorzędnymi sieciami wirtualnymi platformy Azure można zbierać istotne koszty.

Użyj dwóch maszyn wirtualnych do wdrożenia produkcyjnego w systemie DBMS w ramach zestawu dostępności platformy Azure lub między dwoma Strefy dostępności platformy Azure. Należy również użyć oddzielnego routingu dla warstwy aplikacji SAP oraz ruchu związanego z zarządzaniem i eksploatacją do dwóch maszyn wirtualnych systemu DBMS. Zobacz poniższy obraz:

![Diagram dwóch maszyn wirtualnych w dwóch podsieciach](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Użyj Azure Load Balancer, aby przekierować ruch
Używanie prywatnych wirtualnych adresów IP używanych w funkcjach, takich jak SQL Server zawsze włączone lub do replikacji systemu HANA, wymaga konfiguracji modułu równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia używa portów sond do określenia aktywnego węzła DBMS i skierowania ruchu wyłącznie do tego węzła aktywnej bazy danych. 

Jeśli istnieje przejście w tryb failover węzła bazy danych, nie ma potrzeby ponownego skonfigurowania aplikacji SAP. Zamiast tego najpopularniejsze architektury aplikacji SAP łączą się ponownie z prywatnym wirtualnym adresem IP. Tymczasem moduł równoważenia obciążenia odbędzie się do trybu failover węzła, przekierowując ruch do prywatnego wirtualnego adresu IP do drugiego węzła.

Platforma Azure oferuje dwie różne [jednostki SKU modułu równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md): podstawową jednostkę SKU i standardową jednostkę SKU. W oparciu o zalety instalacji i funkcjonalności należy używać standardowej jednostki SKU modułu równoważenia obciążenia platformy Azure. Jedną z dużych zalet standardowej wersji usługi równoważenia obciążenia jest to, że ruch danych nie jest kierowany przez sam moduł równoważenia obciążenia.

Przykład sposobu konfigurowania wewnętrznego modułu równoważenia obciążenia można znaleźć w artykule [Samouczek: Konfigurowanie grupy dostępności SQL Server na platformie Azure Virtual Machines ręcznie](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)

> [!NOTE]
> Istnieją różnice w zachowaniu podstawowej i standardowej jednostki SKU związanej z dostępem do publicznych adresów IP. Sposób obejścia ograniczeń standardowej jednostki SKU w celu uzyskania dostępu do publicznych adresów IP został opisany w dokumencie [łączność publicznego punktu końcowego dla Virtual Machines przy użyciu usługi Azure usługa Load Balancer w warstwie Standardowa w scenariuszach wysokiej dostępności SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)


### <a name="azure-accelerated-networking"></a>Usługa Azure przyspieszone sieci
Aby dodatkowo ograniczyć opóźnienie sieci między maszynami wirtualnymi platformy Azure, zalecamy wybranie [usługi Azure przyspieszonej sieci](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Należy jej używać podczas wdrażania maszyn wirtualnych platformy Azure dla obciążenia SAP, szczególnie w przypadku warstwy aplikacji SAP i warstwy SAP DBMS.

> [!NOTE]
> Nie wszystkie typy maszyn wirtualnych obsługują przyspieszone sieci. W poprzednim artykule wymieniono typy maszyn wirtualnych, które obsługują przyspieszone sieci.
>

---
> ![Przyspieszona sieć systemu Windows][Logo_Windows] Windows
>
> Aby dowiedzieć się, jak wdrażać maszyny wirtualne przy użyciu przyspieszonej sieci dla systemu Windows, zobacz [Tworzenie maszyny wirtualnej z systemem Windows przy użyciu przyspieszonej sieci](../../../virtual-network/create-vm-accelerated-networking-powershell.md).
>
> ![System Linux — przyspieszone sieci][Logo_Linux] Linux
>
> Aby uzyskać więcej informacji na temat dystrybucji systemu Linux, zobacz [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu przyspieszonej sieci](../../../virtual-network/create-vm-accelerated-networking-cli.md).
>
>

---

> [!NOTE]
> W przypadku systemu SUSE, Red Hat i Oracle Linux, przyspieszona sieć jest obsługiwana z najnowszymi wersjami. Starsze wersje, takie jak SLES 12 SP2 lub RHEL 7,2, nie obsługują przyspieszonej sieci platformy Azure.
>


## <a name="deployment-of-host-monitoring"></a>Wdrażanie monitorowania hosta
W przypadku produkcyjnych zastosowań aplikacji SAP w usłudze Azure Virtual Machines SAP wymaga możliwości uzyskiwania danych monitorowania hosta z hostów fizycznych, na których działają maszyny wirtualne platformy Azure. Wymagany jest określony poziom poprawek agenta hosta SAP, który umożliwia korzystanie z tej funkcji w agencie hosta SAPOSCOL i SAP. Dokładny poziom poprawek jest udokumentowany w programie SAP Note [1409604].

Aby uzyskać więcej informacji na temat wdrażania składników dostarczających dane hosta do SAPOSCOL i agenta hosta SAP oraz zarządzania cyklem życia tych składników, zobacz [Przewodnik wdrażania][deployment-guide].


## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat konkretnego systemu DBMS, zobacz:

- [Wdrażanie systemu DBMS usługi Azure Virtual Machines programu SQL Server dla obciążenia SAP](dbms_guide_sqlserver.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines oprogramowania Oracle dla obciążenia SAP](dbms_guide_oracle.md)
- [Wdrożenie programu IBM DB2 Azure Virtual Machines DBMS dla obciążenia SAP](dbms_guide_ibm.md)
- [Wdrażanie systemu DBMS usługi Azure Virtual Machines produktu SAP ESE dla obciążenia SAP](dbms_guide_sapase.md)
- [Wdrażanie oprogramowania SAP maxDB, pamięci podręcznej na żywo i serwera zawartości na platformie Azure](dbms_guide_maxdb.md)
- [Przewodnik obsługi oprogramowania SAP HANA na platformie Azure](hana-vm-operations.md)
- [SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure](sap-hana-availability-overview.md)
- [Przewodnik dotyczący tworzenia kopii zapasowych SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-backup-guide.md)