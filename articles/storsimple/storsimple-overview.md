---
title: Omówienie rozwiązania z serii StorSimple 8000 | Microsoft Docs
description: Opisuje StorSimple warstwowe, urządzenie, urządzenia wirtualne, usługi i zarządzanie magazynem oraz przedstawia kluczowe terminy używane w StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/02/2021
ms.author: timlt
ms.openlocfilehash: e8fb6985da5046d0a1bad171cbc9742a15baf1af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643438"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Seria StorSimple 8000: rozwiązanie hybrydowego magazynu w chmurze

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie
Witamy w Microsoft Azure StorSimple, zintegrowanego rozwiązania do magazynowania, które zarządza zadaniami magazynu między urządzeniami lokalnymi i Microsoft Azure magazynu w chmurze. StorSimple to wydajne, ekonomiczne i łatwe w zarządzaniu rozwiązanie sieci magazynowania (SAN), które eliminuje wiele problemów i wydatków związanych z magazynem przedsiębiorstwa i ochroną danych. Używa ona zastrzeżonego urządzenia z serii StorSimple 8000, integruje się z usługami w chmurze i udostępnia zestaw narzędzi do zarządzania w celu zapewnienia bezproblemowego widoku wszystkich magazynów przedsiębiorstwa łącznie z magazynem w chmurze. (Informacje o wdrożeniu StorSimple opublikowane w witrynie sieci Web Microsoft Azure dotyczą tylko urządzeń z serii StorSimple 8000). Jeśli używasz urządzenia z serii StorSimple 5000/7000, przejdź do [pomocy StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple używa [warstw magazynowania](#automatic-storage-tiering) do zarządzania przechowywanymi danymi na różnych nośnikach magazynu. Bieżący zestaw roboczy jest przechowywany lokalnie na dyskach półprzewodnikowych (dysków SSD). Dane, które są rzadziej używane, są przechowywane na dyskach twardych (HDD), a dane archiwalne są wypychane do chmury. Ponadto StorSimple korzysta z deduplikacji i kompresji, aby zmniejszyć ilość miejsca zużywanego przez dane. Aby uzyskać więcej informacji, przejdź do [deduplikacji i kompresji](#deduplication-and-compression). Aby zapoznać się z definicjami innych kluczowych pojęć i koncepcji, które są używane w dokumentacji serii StorSimple 8000, przejdź do [terminologii StorSimple](#storsimple-terminology) na końcu tego artykułu.

Oprócz zarządzania magazynem funkcje ochrony danych StorSimple umożliwiają tworzenie kopii zapasowych na żądanie i zaplanowane, a następnie przechowywanie ich lokalnie lub w chmurze. Kopie zapasowe są tworzone w postaci migawek przyrostowych, co oznacza, że można je tworzyć i przywracać szybko. Migawki chmury mogą być krytyczne w scenariuszach odzyskiwania po awarii, ponieważ zastępują pomocnicze systemy magazynów (na przykład tworzenie kopii zapasowej na taśmach) i umożliwiają przywrócenie danych do centrum dane lub lokacji alternatywnych w razie potrzeby.

![ikona filmu wideo](./media/storsimple-overview/video_icon.png) Obejrzyj film wideo, aby zapoznać się z krótkim wprowadzeniem do Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Dlaczego warto używać StorSimple?
W poniższej tabeli opisano niektóre z najważniejszych korzyści zapewnianych przez Microsoft Azure StorSimple.

| Cecha | Korzyść |
| --- | --- |
| Integracja przezroczysta |Używa protokołu iSCSI do niewidocznego łączenia obiektów magazynu danych. Dane przechowywane w chmurze, w centrum danych lub na serwerach zdalnych, są przechowywane w jednej lokalizacji. |
| Zmniejszone koszty magazynowania |Przydziela wystarczającą ilość miejsca w magazynie lokalnym lub w chmurze w celu spełnienia bieżących wymagań i rozszerza magazyn w chmurze tylko w razie potrzeby. Znacznie zmniejsza wymagania dotyczące magazynu i wydatków, eliminując nadmiarowe wersje tych samych danych (deduplikacji) i przy użyciu kompresji. |
| Uproszczone zarządzanie magazynem |Program udostępnia narzędzia do administrowania systemem, które umożliwiają konfigurowanie danych przechowywanych lokalnie, na serwerze zdalnym i w chmurze oraz zarządzanie nimi. Ponadto można zarządzać funkcjami wykonywania kopii zapasowych i przywracania z przystawki programu Microsoft Management Console (MMC).|
| Udoskonalone odzyskiwanie po awarii i zgodność |Nie wymaga rozszerzonego czasu odzyskiwania. Zamiast tego przywraca dane w miarę potrzeby, aby normalne operacje mogły kontynuować minimalne zakłócenia. Ponadto można skonfigurować zasady, aby określić harmonogramy kopii zapasowych i przechowywanie danych. |
| Mobilność danych |Do danych przekazywanych do usług w chmurze Microsoft Azure można uzyskać dostęp z innych lokacji na potrzeby odzyskiwania i migracji. Ponadto można użyć usługi StorSimple do skonfigurowania urządzeń w chmurze StorSimple na maszynach wirtualnych działających w Microsoft Azure. Maszyny wirtualne mogą następnie używać urządzeń wirtualnych do uzyskiwania dostępu do danych przechowywanych na potrzeby testowania lub odzyskiwania. |
| Ciągłość działalności biznesowej |Umożliwia użytkownikom serii StorSimple 5000-7000 Migrowanie danych do urządzenia z serii StorSimple 8000. |
| Dostępność w portalu Azure Government |StorSimple jest dostępny w portalu Azure Government. Aby uzyskać więcej informacji, zobacz [wdrażanie lokalnego urządzenia StorSimple w portalu dla instytucji rządowych](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrona i dostępność danych |Seria StorSimple 8000 obsługuje magazyn strefowo nadmiarowy (ZRS), a także Magazyn lokalnie nadmiarowy (LRS) i magazyn Geograficznie nadmiarowy (GRS). Zapoznaj się z [tym artykułem dotyczącym opcji nadmiarowości usługi Azure Storage](../storage/common/storage-redundancy.md) , aby uzyskać szczegółowe informacje o ZRS. |
| Obsługa aplikacji o znaczeniu krytycznym |StorSimple pozwala identyfikować odpowiednie woluminy jako przypięte lokalnie, aby zapewnić, że dane wymagane przez krytyczne aplikacje nie są warstwowe w chmurze. Woluminy przypięte lokalnie nie są objęte opóźnieniami w chmurze lub problemami z łącznością. Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, zobacz [Używanie usługi StorSimple Device Manager do zarządzania woluminami](storsimple-8000-manage-volumes-u2.md). |
| Małe opóźnienia i wysoka wydajność |Możesz tworzyć urządzenia w chmurze wykorzystujące funkcje wysokiej wydajności i małych opóźnień w usłudze Azure Premium Storage. Aby uzyskać więcej informacji na temat urządzeń w chmurze StorSimple Premium, zobacz [wdrażanie i zarządzanie urządzeniem w chmurze StorSimple na platformie Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Składniki StorSimple
Rozwiązanie Microsoft Azure StorSimple obejmuje następujące składniki:

* **Urządzenie Microsoft Azure StorSimple** — tablica magazynów w lokalnej hybrydowej, która zawiera dysków SSD i HDD, wraz z nadmiarowymi kontrolerami i funkcją automatycznej pracy awaryjnej. Kontrolery zarządzają warstwami magazynowania, umieszczając aktualnie używane (lub gorącą) dane w lokalnym magazynie (na urządzeniach lub serwerach lokalnych), jednocześnie przenosząc rzadziej używane dane do chmury.
* **Urządzenie w chmurze StorSimple** — nazywane również urządzeniem wirtualnym StorSimple. Wersja oprogramowania urządzenia StorSimple, która replikuje architekturę i większość możliwości fizycznego hybrydowego urządzenia magazynującego. Urządzenie w chmurze StorSimple działa na jednym węźle na maszynie wirtualnej platformy Azure. Urządzenia wirtualne w warstwie Premium, które korzystają z usługi Azure Premium Storage, są dostępne w Update 2 i nowszych wersjach.
* **Usługa StorSimple Device Manager** — rozszerzenie Azure Portal, które pozwala zarządzać urządzeniem StorSimple lub StorSimple urządzeniu w chmurze z poziomu jednego interfejsu sieci Web. Usługi StorSimple Device Manager można użyć do tworzenia usług i zarządzania nimi, wyświetlania i zarządzania urządzeniami, wyświetlania alertów, zarządzania woluminami oraz wyświetlania i zarządzania zasadami tworzenia kopii zapasowych oraz wykazem kopii zapasowych.
* **Program Windows PowerShell dla usługi StorSimple** — interfejs wiersza polecenia, którego można użyć do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla usługi StorSimple zawiera funkcje, które umożliwiają rejestrację urządzenia z systemem StorSimple, Konfigurowanie interfejsu sieciowego na urządzeniu, instalowanie niektórych typów aktualizacji, rozwiązywanie problemów z urządzeniem przez uzyskanie dostępu do sesji pomocy technicznej oraz zmianę stanu urządzenia. Dostęp do program Windows PowerShell dla usługi StorSimple można uzyskać, łącząc się z konsolą szeregową lub przy użyciu komunikacji zdalnej programu Windows PowerShell.
* **Azure PowerShell polecenia cmdlet StorSimple** — kolekcja poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań na poziomie usług i migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat Azure PowerShell poleceń cmdlet dla StorSimple, przejdź do [dokumentacji poleceń cmdlet](/powershell/module/servicemanagement/azure.service/#azure).
* **StorSimple Snapshot Manager** — przystawka programu MMC, która używa grup woluminów i usługa kopiowania woluminów w tle Windows do generowania kopii zapasowych spójnych na poziomie aplikacji. Ponadto można użyć Snapshot Manager StorSimple do tworzenia harmonogramów tworzenia kopii zapasowych i klonowania lub przywracania woluminów.
* **StorSimple adapter for SharePoint** — narzędzie, które w sposób przezroczysty rozszerza Microsoft Azure StorSimple magazynu i ochrony danych do farmy serwerów programu SharePoint, jednocześnie umożliwiając Zarządzanie magazynem StorSimple w portalu administracji centralnej programu SharePoint.

Na poniższym diagramie przedstawiono ogólny widok architektury i składników Microsoft Azure StorSimple.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

W poniższych sekcjach szczegółowo opisano każdy z tych składników i wyjaśniono, w jaki sposób rozwiązanie rozmieszcza dane, przydziela magazyn i ułatwia zarządzanie magazynem i ochronę danych. Ostatnia sekcja zawiera definicje niektórych ważnych pojęć i koncepcji związanych ze składnikami StorSimple i ich zarządzaniem.

## <a name="storsimple-device"></a>Urządzenie StorSimple
Urządzenie Microsoft Azure StorSimple to lokalna hybrydowa macierz magazynowa, która zapewnia magazyn podstawowy i dostęp iSCSI do danych przechowywanych na tym serwerze. Zarządza on komunikacją z magazynem w chmurze i pomaga w zapewnianiu bezpieczeństwa i poufności wszystkich danych przechowywanych w rozwiązaniu Microsoft Azure StorSimple.

Urządzenie StorSimple obejmuje dysków SSD i stacje dysków twardych (HDD), a także obsługę klastrów i automatyczne przełączanie w tryb failover. Zawiera współużytkowany procesor, magazyn udostępniony i dwa dublowane kontrolery. Każdy kontroler udostępnia następujące elementy:

* Połączenie z komputerem-hostem
* Do sześciu portów sieciowych do nawiązania połączenia z siecią lokalną (LAN)
* Monitorowanie sprzętu
* Nietrwała pamięć RAM (Random Access Memory), która zachowuje informacje nawet wtedy, gdy moc została przerwana
* Aktualizacja typu cluster-aware do zarządzania aktualizacjami oprogramowania na serwerach w klastrze trybu failover, dzięki czemu aktualizacje mają minimalny wpływ na dostępność usługi
* Usługa klastrowania, która działa podobnie jak klaster zaplecza, zapewniając wysoką dostępność i minimalizując wszelkie niekorzystne skutki, które mogą wystąpić, jeśli dysk twardy lub dysk SSD ulegnie awarii lub zostanie przełączony w tryb offline

Tylko jeden kontroler jest aktywny w dowolnym momencie. Jeśli aktywny kontroler ulegnie awarii, drugi kontroler zostanie uaktywniony automatycznie.

Aby uzyskać więcej informacji, przejdź do [StorSimple sprzętowego i jego stanu](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>Urządzenie w chmurze StorSimple
Za pomocą StorSimple można utworzyć urządzenie w chmurze, które replikuje architekturę i możliwości fizycznego hybrydowego urządzenia magazynującego. Urządzenie w chmurze StorSimple (nazywane również urządzeniem wirtualnym StorSimple) działa w jednym węźle na maszynie wirtualnej platformy Azure. (Urządzenie w chmurze można utworzyć tylko na maszynie wirtualnej platformy Azure). Nie można go utworzyć na urządzeniu StorSimple ani na serwerze lokalnym.

Urządzenie w chmurze ma następujące funkcje:

* Działa jak urządzenie fizyczne i może oferować interfejs iSCSI maszynom wirtualnym w chmurze.
* W chmurze można utworzyć nieograniczoną liczbę urządzeń w chmurze oraz włączyć ją i wyłączyć w razie potrzeby.
* Może to pomóc w symulowaniu środowisk lokalnych w przypadku odzyskiwania po awarii, programowania i scenariuszy testowania, a także ułatwia pobieranie kopii zapasowych na poziomie elementu.

Urządzenie w chmurze StorSimple jest dostępne w dwóch modelach: urządzenie 8010 (wcześniej znane jako model 1100) i urządzenie 8020. Maksymalna pojemność urządzenia 8010 to 30 TB. Urządzenie 8020, które korzysta z usługi Azure Premium Storage, ma maksymalną pojemność wynoszącą 64 TB. (W warstwach lokalnych usługa Azure Premium Storage przechowuje dane w witrynie dysków SSD, a w przypadku magazynu w warstwie Standardowa są przechowywane dane HDD). Musisz mieć konto usługi Azure Premium Storage, aby korzystać z magazynu Premium Storage.

Aby uzyskać więcej informacji o urządzeniu w chmurze StorSimple, przejdź do [wdrażania i zarządzania urządzeniem StorSimple w chmurze na platformie Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple
Microsoft Azure StorSimple udostępnia interfejs użytkownika oparty na sieci Web (Usługa StorSimple Device Manager), który umożliwia centralne zarządzanie magazynem centrów danych i w chmurze. Za pomocą usługi StorSimple Device Manager można wykonywać następujące zadania:

* Skonfiguruj ustawienia systemowe dla urządzeń StorSimple.
* Skonfiguruj ustawienia zabezpieczeń dla urządzeń StorSimple i zarządzaj nimi.
* Skonfiguruj poświadczenia i właściwości chmury.
* Konfigurowanie woluminów na serwerze i zarządzanie nimi.
* Skonfiguruj grupy woluminów.
* Tworzenie kopii zapasowych i przywracanie danych.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemowe i zidentyfikuj możliwe problemy.

Za pomocą usługi StorSimple Device Manager można wykonywać wszystkie zadania administracyjne, z wyjątkiem zadań wymagających systemu, takich jak początkowa konfiguracja i instalacja aktualizacji.

Aby uzyskać więcej informacji, przejdź do [korzystania z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>program Windows PowerShell dla usługi StorSimple
Program Windows PowerShell dla usługi StorSimple udostępnia interfejs wiersza polecenia, którego można użyć do tworzenia usługi Microsoft Azure StorSimple i zarządzania nią oraz konfigurowania i monitorowania urządzeń StorSimple. Jest to oparty na interfejsie wiersza polecenia programu Windows PowerShell, który obejmuje dedykowane polecenia cmdlet służące do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla usługi StorSimple zawiera funkcje, które umożliwiają:

* Zarejestruj urządzenie.
* Skonfiguruj interfejs sieciowy na urządzeniu.
* Zainstalowanie niektórych typów aktualizacji.
* Rozwiązywanie problemów z urządzeniem przez uzyskanie dostępu do sesji pomocy technicznej.
* Zmień stan urządzenia.

Dostęp do program Windows PowerShell dla usługi StorSimple można uzyskać za pomocą konsoli szeregowej (na komputerze hosta połączonym bezpośrednio z urządzeniem) lub zdalnie przy użyciu komunikacji zdalnej programu Windows PowerShell. Niektóre program Windows PowerShell dla usługi StorSimple zadania, takie jak początkowa rejestracja urządzeń, można wykonać tylko w konsoli szeregowej.

Aby uzyskać więcej informacji, przejdź do obszaru [użycie program Windows PowerShell dla usługi StorSimple w celu administrowania urządzeniem](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell polecenia cmdlet StorSimple
Polecenia cmdlet programu Azure PowerShell StorSimple to zbiór poleceń cmdlet programu Windows PowerShell, które umożliwiają automatyzację zadań na poziomie usług i migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat Azure PowerShell poleceń cmdlet dla StorSimple, przejdź do [dokumentacji poleceń cmdlet](/powershell/module/servicemanagement/azure.service/).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager jest przystawką programu Microsoft Management Console (MMC), której można użyć do utworzenia spójnych kopii zapasowych danych lokalnych i w chmurze. Przystawka jest uruchamiana na hoście z systemem Windows Server. Snapshot Manager StorSimple można użyć do:

* Konfigurowanie, tworzenie kopii zapasowych i usuwanie woluminów.
* Skonfiguruj grupy woluminów, aby upewnić się, że kopia zapasowa danych jest spójna z aplikacją.
* Zarządzanie zasadami tworzenia kopii zapasowych, dzięki czemu kopie zapasowe danych są tworzone zgodnie z ustalonym harmonogramem i przechowywane w określonej lokalizacji (lokalnie lub w chmurze).
* Przywróć woluminy i poszczególne pliki.

Kopie zapasowe są przechwytywane jako migawki, które rejestrują tylko zmiany od momentu utworzenia ostatniej migawki i wymagają znacznie mniejszego miejsca w magazynie niż pełne kopie zapasowe. W razie potrzeby można tworzyć harmonogramy kopii zapasowych lub wykonywać natychmiastowe kopie zapasowe. Ponadto można użyć StorSimple Snapshot Manager do ustanowienia zasad przechowywania, które kontrolują, ile migawek zostanie zapisanych. Jeśli później będzie konieczne przywrócenie danych z kopii zapasowej, StorSimple Snapshot Manager umożliwia wybranie z wykazu migawek lokalnych lub chmurowych. 

Jeśli wystąpi awaria lub jeśli trzeba przywrócić dane z innego powodu, StorSimple Snapshot Manager przywraca je stopniowo, w miarę potrzeb. Przywracanie danych nie wymaga zamknięcia całego systemu podczas przywracania pliku, zastępowania sprzętu lub przenoszenia operacji do innej lokacji.

Aby uzyskać więcej informacji, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Adapter usługi StorSimple dla programu SharePoint
Microsoft Azure StorSimple obejmuje adapter StorSimple dla programu SharePoint, składnik opcjonalny, który w sposób przezroczysty rozszerza funkcje magazynu StorSimple i ochrony danych do farmy serwerów programu SharePoint. Karta współpracuje z dostawcą zdalnego Blob Storage (SPZ) i funkcją SQL Server SPZ, umożliwiając przenoszenie obiektów BLOB do serwera, którego kopia zapasowa jest wykonywana przez system Microsoft Azure StorSimple. Microsoft Azure StorSimple następnie przechowuje dane obiektów BLOB lokalnie lub w chmurze na podstawie użycia.

Adapter StorSimple dla programu SharePoint jest zarządzany z poziomu portalu administracji centralnej programu SharePoint. Zarządzanie programem SharePoint pozostaje scentralizowane, a wszystkie magazyny będą widoczne w farmie programu SharePoint.

Aby uzyskać więcej informacji, przejdź do [karty StorSimple dla programu SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie zarządzania magazynem
Oprócz dedykowanego urządzenia StorSimple, urządzenia wirtualnego i innych składników, Microsoft Azure StorSimple używa następujących technologii oprogramowania, aby zapewnić szybki dostęp do danych i ograniczyć użycie magazynu:

* [Automatyczne przechowywanie warstw magazynowania](#automatic-storage-tiering) 
* [Alokowanie elastyczne](#thin-provisioning) 
* [Deduplikacja i kompresja](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatyczne przechowywanie warstw magazynowania
Microsoft Azure StorSimple automatycznie rozmieszcza dane w warstwach logicznych na podstawie bieżącego użycia, wieku i relacji z innymi danymi. Dane, które są najbardziej aktywne są przechowywane lokalnie, podczas gdy mniej aktywne i nieaktywne dane są automatycznie migrowane do chmury. Na poniższym diagramie przedstawiono podejście do magazynu.

![Warstwy magazynu StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Aby włączyć szybki dostęp, StorSimple przechowuje bardzo aktywne dane (gorąca Data) na dysków SSD na urządzeniu StorSimple. Przechowuje dane, które są używane sporadycznie (grzane dane) na HDD na urządzeniu lub na serwerach w centrum danych. Przenosi dane nieaktywne, dane kopii zapasowej i dane przechowywane w celach archiwalnych lub zgodności do chmury. 

> [!NOTE]
> W wersji Update 2 lub nowszej można określić wolumin jako przypięty lokalnie, w tym przypadku dane pozostają na urządzeniu lokalnym i nie są warstwą chmury. 


StorSimple dostosowuje i zmienia przydziały danych i magazynu w miarę zmiany wzorców użytkowania. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. W miarę jak coraz mniej aktywności jest migrowane z dysków SSD do HDD, a następnie do chmury. Jeśli te same dane staną się ponownie aktywne, zostaną zmigrowane z powrotem do urządzenia magazynującego.

Proces obsługi warstw magazynowania odbywa się w następujący sposób:

1. Administrator systemu konfiguruje konto magazynu w chmurze Microsoft Azure.
2. Administrator korzysta z konsoli szeregowej i usługi StorSimple Device Manager (działającej w Azure Portal) w celu skonfigurowania urządzenia i serwera plików, tworzenia woluminów i zasad ochrony danych. Na maszynach lokalnych (takich jak serwery plików) do uzyskiwania dostępu do urządzenia StorSimple służy interfejs systemu Small Computer System (iSCSI) z Internetu.
3. Początkowo StorSimple przechowuje dane w szybkiej warstwie dysków SSD na urządzeniu.
4. Ponieważ warstwa dysków SSD zbliża się do pojemności, StorSimple deduplikowane i kompresuje najstarsze bloki danych i przenosi je do warstwy dysków twardych.
5. Ponieważ warstwa dysków twardych zbliża się do pojemności, StorSimple szyfruje najstarsze bloki danych i bezpiecznie wysyła je do konta magazynu Microsoft Azure za pośrednictwem protokołu HTTPS.
6. Microsoft Azure umożliwia utworzenie wielu replik danych w jego centrach i w zdalnym centrum danych, dzięki czemu można odzyskać dane w przypadku wystąpienia awarii.
7. Gdy serwer plików żąda danych przechowywanych w chmurze, Usługa StorSimple bezproblemowo zwraca ją i zapisuje kopię w warstwie SSD urządzenia StorSimple.

> [!IMPORTANT]
> W przypadku korzystania z StorSimple nie należy konwertować obiektów BLOB do archiwizacji, nawet jeśli urządzenie jest wycofywane. Aby można było pobrać dane z urządzenia, należy je ponownie odczytać z archiwum do typu gorąca lub chłodna, co skutkuje znaczącymi kosztami.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple zarządza danymi w chmurze

StorSimple deduplikuje dane klientów w ramach wszystkich migawek i danych głównych (dane zapisywane przez hosty). Podczas gdy Deduplikacja jest doskonałym rozwiązaniem w zakresie wydajności magazynu, warto zadać pomysł "co to jest w chmurze". Dane podstawowe warstwy i dane migawki nakładają się na siebie nawzajem. Pojedynczy fragment danych w chmurze może być używany jako dane podstawowe warstwy, a także być przywoływany przez kilka migawek. Każda migawka w chmurze gwarantuje, że kopia wszystkich danych w danym momencie jest zablokowana w chmurze do momentu usunięcia tej migawki.

Dane są usuwane tylko z chmury, gdy nie ma odwołań do tych danych. Jeśli na przykład wykonamy migawkę w chmurze dla wszystkich danych znajdujących się na urządzeniu StorSimple, a następnie usuniemy niektóre dane podstawowe, zostanie natychmiast wyświetlone _podstawowe dane_ upuszczania. _Dane w chmurze_, które obejmują dane warstwowe i kopie zapasowe, pozostają takie same, ponieważ migawka nadal odwołuje się do danych w chmurze. Po usunięciu migawki w chmurze (oraz wszelkich innych migawek, które odwołują się do tych samych danych), zużycie w chmurze zostanie porzucane. Przed usunięciem danych w chmurze sprawdzimy, że żadne migawki nadal odwołują się do tych danych. Ten proces jest nazywany _odzyskiwaniem pamięci_ i jest usługą w tle uruchomioną na urządzeniu. Usuwanie danych w chmurze nie jest natychmiastowe, ponieważ usługa odzyskiwania pamięci sprawdza inne odwołania do tych danych przed usunięciem. Szybkość odzyskiwania pamięci zależy od łącznej liczby migawek i całkowitej ilości danych. Zwykle dane w chmurze są czyszczone w czasie krótszym niż tydzień.


### <a name="thin-provisioning"></a>Alokowanie elastyczne
Alokowanie elastyczne jest technologią wirtualizacji, w której dostępny magazyn wydaje się przekroczyć zasoby fizyczne. Zamiast ponownie obsłużyć wystarczającą ilość miejsca w magazynie, StorSimple używa alokowania elastycznego do przydzielania wystarczającej ilości miejsca, aby spełnić bieżące wymagania. Elastyczna natura magazynu w chmurze ułatwia takie podejście, ponieważ Usługa StorSimple może zwiększyć lub zmniejszyć ilość miejsca w magazynie w chmurze, aby sprostać zmiennym potrzebom.

> [!NOTE]
> Woluminy przypięte lokalnie nie są alokowane elastycznie. Magazyn przydzielony do woluminu tylko lokalnie jest inicjowany w całości podczas tworzenia woluminu.


### <a name="deduplication-and-compression"></a>Deduplikacja i kompresja
Microsoft Azure StorSimple używa deduplikacji i kompresji danych, aby bardziej ograniczyć wymagania dotyczące magazynu.

Deduplikacja zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości w zestawie danych przechowywanych. Wraz ze zmianami informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko te zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikując i usuwając zbędne informacje. 

> [!NOTE]
> Dane na woluminach przypiętych lokalnie nie są deduplikowane lub skompresowane. Jednak kopie zapasowe woluminów przypiętych lokalnie są deduplikowane i kompresowane.


## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia StorSimple
Podsumowanie obsługiwanych obciążeń StorSimple znajduje się poniżej.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Współpraca |Udostępnianie plików |Tak | |Wszystkie wersje |
| Współpraca |Rozproszone udostępnianie plików |Tak | |Wszystkie wersje |
| Współpraca |SharePoint |Tak* |Obsługiwane tylko dla woluminów przypiętych lokalnie |Aktualizacja 2 lub nowsza |
| Archiwizacji |Proste archiwizowanie plików |Tak | |Wszystkie wersje |
| Wirtualizacja |Maszyny wirtualne |Tak* |Obsługiwane tylko dla woluminów przypiętych lokalnie |Aktualizacja 2 lub nowsza |
| baza danych |SQL |Tak* |Obsługiwane tylko dla woluminów przypiętych lokalnie |Aktualizacja 2 lub nowsza |
| Monitorowanie wideo |Monitorowanie wideo |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko dla tego obciążenia |Aktualizacja 2 lub nowsza |
| Backup |Podstawowa kopia zapasowa |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko dla tego obciążenia |Aktualizacja Update 3 lub nowsza |
| Backup |Pomocnicza kopia zapasowa |Tak* |Obsługiwane, gdy urządzenie StorSimple jest dedykowane tylko dla tego obciążenia |Aktualizacja Update 3 lub nowsza |

*Tak&#42; — należy zastosować wytyczne i ograniczenia dotyczące rozwiązań.*

Następujące obciążenia nie są obsługiwane przez urządzenia z serii StorSimple 8000. W przypadku wdrożenia w systemie StorSimple te obciążenia spowodują nieobsługiwaną konfigurację.

* Obraz medyczny
* Exchange
* Infrastruktura VDI
* Oracle
* SAP
* Dane big data
* Dystrybucja zawartości
* Rozruch z interfejsu SCSI

Poniżej znajduje się lista obsługiwanych składników infrastruktury StorSimple.

| Scenariusz | Obciążenie | Obsługiwane | Ograniczenia | Wersja |
| --- | --- | --- | --- | --- |
| Ogólne |Express Route |Tak | |Wszystkie wersje |
| Ogólne |Podstawowe funkcje FC |Tak* |Obsługiwane w programie DataCore SANsymphony |Wszystkie wersje |
| Ogólne |DFSR |Tak* |Obsługiwane tylko dla woluminów przypiętych lokalnie |Wszystkie wersje |
| Ogólne |Indeksowanie |Tak* |W przypadku woluminów warstwowych obsługiwane jest tylko indeksowanie metadanych (Brak danych).<br>Dla woluminów przypiętych lokalnie jest obsługiwane pełne indeksowanie. |Wszystkie wersje |
| Ogólne |Antywirusowe |Tak* |W przypadku woluminów warstwowych obsługiwane jest tylko skanowanie przy otwartym i zamknięciu.<br> W przypadku woluminów przypiętych lokalnie jest obsługiwana pełna skanowanie. |Wszystkie wersje |

*Tak&#42; — należy zastosować wytyczne i ograniczenia dotyczące rozwiązań.*

Poniżej znajduje się lista innych programów używanych z programem StorSimple do kompilowania rozwiązań.

| Typ obciążenia | Oprogramowanie używane z StorSimple | Obsługiwane wersje|Link do przewodnika po rozwiązaniu| 
| --- | --- | --- | --- |
| Miejsce docelowe kopii zapasowej |Veeam |Veeam v 9 i nowsze |[StorSimple jako miejsce docelowe kopii zapasowej za pomocą Veaam](storsimple-configure-backup-target-veeam.md)|
| Miejsce docelowe kopii zapasowej |Veritas Backup Exec |Backup Exec 16 i nowsze |[StorSimple jako miejsce docelowe kopii zapasowej przy użyciu programu Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Miejsce docelowe kopii zapasowej |VERITAS NetBackup |NetBackup 7.7. x i nowsze  |[StorSimple jako miejsce docelowe kopii zapasowej za pomocą NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globalne udostępnianie plików <br></br> Współpraca |Talon  |[StorSimple z Talon](https://www.theinfostride.com/talon-and-microsoft-to-host-azure-storsimple-web-conference-with-capita/) | |

## <a name="storsimple-terminology"></a>Terminologia StorSimple
Przed wdrożeniem rozwiązania Microsoft Azure StorSimple zalecamy zapoznanie się z poniższymi postanowieniami i definicjami.

### <a name="key-terms-and-definitions"></a>Najważniejsze terminy i definicje
| Termin (akronim lub skrót) | Opis |
| --- | --- |
| rekord kontroli dostępu (ACR) |Rekord skojarzony z woluminem na urządzeniu Microsoft Azure StorSimple, który określa, które hosty mogą się z nim połączyć. Ustalanie jest oparte na kwalifikowanej nazwie iSCSI (IQN) hostów (zawartej w ACR), które nawiązują połączenie z urządzeniem StorSimple. |
| AES 256 |Algorytm 256-bitowy Advanced Encryption Standard (AES) służący do szyfrowania danych w trakcie ich przenoszenia do i z chmury. |
| rozmiar jednostki alokacji (Australia) |Najmniejsza ilość miejsca na dysku, którą można przydzielić do przechowywania pliku w systemach plików Windows. Jeśli rozmiar pliku nie jest parzystym wielokrotnością rozmiaru klastra, należy użyć dodatkowego miejsca do przechowywania pliku (aż do następnej wielokrotności rozmiaru klastra), co spowodowało utratę miejsca i fragmentację dysku twardego. <br>Zalecana dla woluminów StorSimple platformy Azure to 64 KB, ponieważ dobrze działa z algorytmami deduplikacji. |
| automatyczne przechowywanie warstw magazynowania |Automatyczne przeniesienie mniej aktywnych danych z dysków SSD do HDD, a następnie do warstwy w chmurze, a następnie włączenie zarządzania wszystkimi magazynem za pomocą centralnego interfejsu użytkownika. |
| wykaz kopii zapasowych |Kolekcja kopii zapasowych, zwykle odnosząca się do używanego typu aplikacji. Ta kolekcja jest wyświetlana w bloku wykazu kopii zapasowych interfejsu użytkownika usługi StorSimple Device Manager. |
| plik wykazu kopii zapasowej |Plik zawierający listę dostępnych migawek obecnie przechowywanych w kopii zapasowej bazy danych StorSimple Snapshot Manager. |
| zasady tworzenia kopii zapasowych |Wybór woluminów, typ kopii zapasowej i harmonogram, który umożliwia tworzenie kopii zapasowych zgodnie ze wstępnie zdefiniowanym harmonogramem. |
| duże obiekty binarne (obiekty blob) |Kolekcja danych binarnych przechowywanych jako jedna jednostka w systemie zarządzania bazami danych. Obiekty blob są zwykle obrazami, dźwiękami lub innymi obiektami multimedialnymi, chociaż czasami binarny kod wykonywalny jest przechowywany jako obiekt BLOB. |
| Protokół uwierzytelniania typu Challenge Handshake (CHAP) |Protokół używany do uwierzytelniania elementu równorzędnego połączenia na podstawie równorzędnego udostępniania hasła lub klucza tajnego. Protokół CHAP może być jednokierunkowe lub wzajemnie. W przypadku protokołu CHAP jednokierunkowego element docelowy uwierzytelnia inicjator. Wzajemne uwierzytelnianie CHAP wymaga, aby element docelowy uwierzytelniał inicjator i inicjator uwierzytelniał element docelowy. |
| clone |Zduplikowana kopia woluminu. |
| Chmura jako warstwa (CaaT) |Magazyn w chmurze jest zintegrowany jako warstwa w ramach architektury magazynu, dzięki czemu cały magazyn jest częścią jednej sieci magazynu przedsiębiorstwa. |
| Dostawca usług w chmurze (CSP) |Dostawca usług chmury obliczeniowej. |
| migawka w chmurze |Kopia danych woluminu w danym momencie przechowywana w chmurze. Migawka w chmurze jest równoważna z migawką replikowaną w innym systemie magazynu poza lokacją. Migawki w chmurze są szczególnie przydatne w scenariuszach odzyskiwania po awarii. |
| klucz szyfrowania magazynu w chmurze |Hasło lub klucz używany przez urządzenie StorSimple do uzyskiwania dostępu do zaszyfrowanych danych wysyłanych przez urządzenie do chmury. |
| Aktualizowanie z uwzględnieniem klastrów |Zarządzanie aktualizacjami oprogramowania na serwerach w klastrze trybu failover, dzięki czemu aktualizacje mają minimalny wpływ na dostępność usługi lub nie mają na nie wpływu. |
| ścieżka datapath |Kolekcja jednostek funkcjonalnych, które wykonują operacje przetwarzania danych między połączeniami. |
| zdezaktywować |Stała akcja, która przerywa połączenie między urządzeniem StorSimple a skojarzoną usługą w chmurze. Migawki w chmurze urządzenia pozostają po tym procesie i mogą być klonowane lub używane do odzyskiwania po awarii. |
| dublowanie dysków |Replikacja woluminów dysku logicznego na oddzielnych dyskach twardych w czasie rzeczywistym w celu zapewnienia ciągłej dostępności. |
| dublowanie dysków dynamicznych |Replikacja woluminów dysku logicznego na dyskach dynamicznych. |
| dyski dynamiczne |Format woluminu dysku, który używa Menedżera dysków logicznych (LDM) do przechowywania danych i zarządzania nimi na wielu dyskach fizycznych. Dyski dynamiczne można zwiększyć w celu zapewnienia większej ilości wolnego miejsca. |
| Obudowa EBOD (Extended Pęczking disks) |Pomocnicza Obudowa urządzenia Microsoft Azure StorSimple, która zawiera dodatkowe dyski twarde na potrzeby dodatkowego magazynu. |
| Inicjowanie obsługi systemu FAT |Konwencjonalne Inicjowanie obsługi magazynu, w którym przydzielono miejsce do magazynowania w oparciu o przewidywane potrzeby (i zazwyczaj jest poza bieżącą potrzebą). Zobacz też *alokowanie elastyczne*. |
| Stacja dysków twardych (dysk twardy) |Dysk, który używa rotacji Platters do przechowywania danych. |
| Magazyn w chmurze hybrydowej |Architektura magazynu korzystająca z zasobów lokalnych i poza lokacją, w tym magazynu w chmurze. |
| Interfejs Internet Small Computer System Interface (iSCSI) |Standard sieci magazynowania oparty na protokole internetowym (IP), który służy do łączenia sprzętu lub urządzeń magazynu danych. |
| Inicjator iSCSI |Składnik oprogramowania, który umożliwia komputerowi hosta z systemem Windows łączenie się z zewnętrzną siecią magazynu opartą na interfejsie iSCSI. |
| Kwalifikowana nazwa inicjatora iSCSI (IQN) |Unikatowa nazwa identyfikująca obiekt docelowy iSCSI lub inicjator. |
| obiekt docelowy iSCSI |Składnik oprogramowania, który udostępnia scentralizowane podsystemy dysków iSCSI w sieciach San. |
| Archiwizowanie na żywo |Podejście magazynu, w którym dane archiwalne są dostępne przez cały czas (na przykład nie są przechowywane poza lokacją na taśmie). Microsoft Azure StorSimple używa usługi archiwizacja na żywo. |
| wolumin przypięty lokalnie |wolumin, który znajduje się na urządzeniu i nigdy nie jest warstwą chmury. |
| migawka lokalna |Kopia danych woluminu w danym momencie przechowywana na urządzeniu Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Zaawansowane rozwiązanie składające się z urządzenia magazynu centrum danych i oprogramowania, które umożliwia organizacjom IT Korzystanie z magazynu w chmurze, tak jakby był to magazyn centrów danych. StorSimple upraszcza ochronę danych i zarządzanie danymi przy jednoczesnym obniżeniu kosztów. Rozwiązanie konsoliduje podstawowe magazyny, archiwum, kopie zapasowe i odzyskiwanie po awarii (DR) przez bezproblemowe integrację z chmurą. Łącząc magazyn sieci SAN i zarządzanie danymi w chmurze na platformie klasy korporacyjnej, urządzenia StorSimple zapewniają szybkość, prostotę i niezawodność wszystkich potrzeb związanych z magazynem. |
| Moduł energetyczny i chłodzenia (PCM) |Składniki sprzętowe urządzenia StorSimple składające się z zasilaczy i dmuchawy chłodzącej; w związku z tym modułem nazwa elektryczności i chłodzenia. Podstawowa obudowa urządzenia ma dwie 764W PCMs, a obudowa EBOD ma dwa 580W PCMs. |
| Obudowa podstawowa |Główna Obudowa urządzenia StorSimple, która zawiera kontrolery platformy aplikacji. |
| cel czasu odzyskiwania (RTO) |Maksymalny czas, jaki należy wykonać, zanim proces biznesowy lub system zostanie całkowicie przywrócony po awarii. |
| Serial Attached SCSI (SAS) |Typ dysku twardego (dysk twardy). |
| klucz szyfrowania danych usługi |Klucz udostępniony każdemu nowemu urządzeniu StorSimple, które rejestruje się w usłudze StorSimple Device Manager. Dane konfiguracyjne transferowane między usługą StorSimple Device Manager i urządzeniem są szyfrowane przy użyciu klucza publicznego, a następnie można je odszyfrować tylko na urządzeniu przy użyciu klucza prywatnego. Klucz szyfrowania danych usługi umożliwia usłudze uzyskanie tego klucza prywatnego w celu odszyfrowania. |
| klucz rejestracji usługi |Klucz, który pomaga zarejestrować urządzenie z systemem StorSimple w usłudze StorSimple Device Manager, tak aby pojawiło się w Azure Portal do dalszych akcji zarządzania. |
| Small Computer System Interface (SCSI) |Zestaw standardów do fizycznego łączenia komputerów i przekazywania danych między nimi. |
| dysk półprzewodnikowy (SSD) |Dysk, który nie zawiera ruchomych części; na przykład dysk flash. |
| konto magazynu |Zestaw poświadczeń dostępu połączony z kontem magazynu dla danego dostawcy usług w chmurze. |
| Adapter usługi StorSimple dla programu SharePoint |Składnik Microsoft Azure StorSimple, który w sposób przezroczysty rozszerza Magazyn StorSimple i ochronę danych w farmach serwerów programu SharePoint. |
| Usługa Menedżer urządzeń StorSimple |Rozszerzenie Azure Portal umożliwiające zarządzanie urządzeniami lokalnymi i wirtualnymi platformy Azure StorSimple. |
| StorSimple Snapshot Manager |Przystawka programu Microsoft Management Console (MMC) służąca do zarządzania operacjami tworzenia kopii zapasowych i przywracania w programie Microsoft Azure StorSimple. |
| Utwórz kopię zapasową |Funkcja, która umożliwia użytkownikowi wykonywanie interaktywnej kopii zapasowej woluminu. Jest to alternatywna metoda ręcznego tworzenia kopii zapasowej woluminu w przeciwieństwie do tworzenia zautomatyzowanej kopii zapasowej za pomocą zdefiniowanych zasad. |
| Alokowanie elastyczne |Metoda optymalizacji wydajności, z którą jest używane miejsce do magazynowania w systemach magazynowania. W przypadku alokowania elastycznego magazyn jest przydzielany między wieloma użytkownikami na podstawie minimalnej ilości miejsca wymaganego przez każdego użytkownika w danym momencie. Zobacz również *Inicjowanie obsługi systemu FAT*. |
| warstw |Rozmieszczanie danych w grupach logicznych na podstawie bieżącego użycia, wieku i relacji z innymi danymi. StorSimple automatycznie rozmieszcza dane w warstwach. |
| wolumin |Obszary magazynu logicznego prezentowane w postaci dysków. Woluminy StorSimple odpowiadają woluminom zainstalowanym przez hosta, w tym woluminy wykryte za pomocą interfejsu iSCSI i urządzenia StorSimple. |
| kontener woluminów |Grupowanie woluminów i ustawienia, które mają do nich zastosowanie. Wszystkie woluminy na urządzeniu StorSimple są pogrupowane w kontenery woluminów. Ustawienia kontenera woluminów obejmują konta magazynu, ustawienia szyfrowania danych wysyłanych do chmury ze skojarzonymi kluczami szyfrowania, a także przepustowość zużywaną na potrzeby operacji obejmujących chmurę. |
| Grupa woluminów |W StorSimple Snapshot Manager Grupa woluminów to zbiór woluminów skonfigurowanych do ułatwienia przetwarzania kopii zapasowych. |
| Usługa kopiowania woluminów w tle (VSS) |Usługa systemu operacyjnego Windows Server, która ułatwia spójność aplikacji przez komunikowanie się z aplikacjami obsługującymi usługę VSS w celu koordynowania tworzenia migawek przyrostowych. Usługa VSS zapewnia, że aplikacje są tymczasowo nieaktywne podczas tworzenia migawek. |
| program Windows PowerShell dla usługi StorSimple |Interfejs wiersza polecenia oparty na programie Windows PowerShell służący do obsługi urządzenia StorSimple i zarządzania nim. Przy zachowaniu niektórych podstawowych możliwości środowiska Windows PowerShell ten interfejs ma dodatkowe dedykowane polecenia cmdlet, które są dostosowane do zarządzania urządzeniem StorSimple. |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [zabezpieczeniach StorSimple](storsimple-8000-security.md).
