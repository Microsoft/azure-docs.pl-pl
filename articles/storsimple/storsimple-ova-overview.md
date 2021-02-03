---
title: Omówienie Microsoft Azure StorSimple macierzy wirtualnej
description: Opisuje macierz wirtualną StorSimple, zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalną tablicą wirtualną a Microsoft Azure magazynem w chmurze.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: alkohli
ms.openlocfilehash: af48e1f415e0ca0b1027d277f70c3f0f1a11e687
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526769"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Wprowadzenie do macierzy wirtualnej StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Omówienie

Wirtualna macierz Microsoft Azure StorSimple to zintegrowane rozwiązanie magazynu, które zarządza zadaniami magazynu między lokalną tablicą wirtualną działającą w funkcji hypervisor i Microsoft Azure magazynu w chmurze. Wirtualna macierz to wydajne, ekonomiczne i łatwe w użyciu rozwiązanie serwera plików lub serwera iSCSI, które eliminuje wiele problemów i wydatków związanych z magazynem przedsiębiorstwa i ochroną danych. Macierz wirtualna jest szczególnie przydatna do przechowywania rzadko używanych danych archiwalnych.

Ten artykuł zawiera omówienie macierzy wirtualnej — Oto kilka innych zasobów:

* Najlepsze rozwiązania można znaleźć w temacie [StorSimple Virtual Array Best Practices](storsimple-ova-best-practices.md).
* Aby zapoznać się z omówieniem urządzeń z serii StorSimple 8000, przejdź do [serii StorSimple 8000: rozwiązanie w chmurze hybrydowej](storsimple-overview.md).
* Aby uzyskać informacje na temat urządzeń z serii StorSimple 5000/7000, przejdź do [StorSimple online](http://onlinehelp.storsimple.com/).

Macierz wirtualna obsługuje protokół iSCSI lub SMB (Server Message Block). Jest ona uruchamiana w istniejącej infrastrukturze funkcji hypervisor i zapewnia obsługę warstw w chmurze, kopii zapasowej w chmurze, szybkiej przywracania, odzyskiwaniu na poziomie elementu i funkcji odzyskiwania po awarii.

Poniższa tabela zawiera podsumowanie ważnych funkcji macierzy wirtualnej StorSimple.

| Cechy | Macierz wirtualna usługi StorSimple |
| --- | --- |
| Wymagania dotyczące instalacji |Używa infrastruktury wirtualizacji (Hyper-V lub VMware) |
| Dostępność |Jeden węzeł |
| Całkowita pojemność (łącznie z chmurą) |Do 64 TB użytecznej pojemności na macierz wirtualną |
| Pojemność lokalna |390 GB do 6,4 TB użytecznej pojemności na tablicę wirtualną (należy zastanowić się nad 500 GB do 8 TB miejsca na dysku) |
| Protokoły natywne |iSCSI lub SMB |
| Cel czasu odzyskiwania (recovery time objective, RTO) |iSCSI: mniej niż 2 minuty, niezależnie od rozmiaru |
| Cel punktu odzyskiwania (recovery point objective, RPO) |Codzienne kopie zapasowe i kopie zapasowe na żądanie |
| Obsługa warstw magazynowania |Używa mapowania ciepła do określania, jakie dane powinny być warstwowe lub wychodzące |
| Pomoc techniczna |Infrastruktura wirtualizacji obsługiwana przez dostawcę |
| Wydajność |Różni się w zależności od podstawowej infrastruktury |
| Mobilność danych |Można przywrócić na to samo urządzenie lub odzyskiwanie na poziomie elementu (serwer plików) |
| Warstwy magazynowania |Lokalny magazyn funkcji hypervisor i chmura |
| Rozmiar udziału |Warstwowy: do 20 TB; przypięty lokalnie: do 2 TB |
| Rozmiar woluminu |Warstwowy: 500 GB do 5 TB; przypięty lokalnie: 50 GB do 200 GB <br> Maksymalna rezerwacja lokalna dla woluminów warstwowych wynosi 200 GB. |
| Migawki |Spójność na poziomie awarii |
| Odzyskiwanie na poziomie elementu |Opcję Użytkownicy mogą przywracać z udziałów |

## <a name="why-use-storsimple"></a>Dlaczego warto używać StorSimple?

StorSimple łączy użytkowników i serwery z usługą Azure Storage w kilka minut bez modyfikacji aplikacji.

W poniższej tabeli opisano niektóre z najważniejszych korzyści oferowanych przez rozwiązanie StorSimple Virtual Array.

| Cechy | Korzyść |
| --- | --- |
| Integracja przezroczysta |Macierz wirtualna obsługuje protokół iSCSI lub SMB. Przenoszenie danych między warstwą lokalną a warstwą chmury jest bezproblemowe i niewidoczne dla użytkownika. |
| Zmniejszone koszty magazynowania |W przypadku usługi StorSimple należy zapewnić wystarczającą ilość miejsca w magazynie lokalnym, aby zaspokoić bieżące wymagania dotyczące najczęściej używanych danych. W miarę wzrostu potrzeb magazynu StorSimple warstwy zimne dane do ekonomicznego magazynu w chmurze. Dane są deduplikowane i kompresowane przed wysłaniem do chmury, aby jeszcze bardziej ograniczyć wymagania i koszty magazynu. |
| Uproszczone zarządzanie magazynem |Usługa StorSimple udostępnia scentralizowane zarządzanie w chmurze za pomocą usługi StorSimple Device Manager do zarządzania wieloma urządzeniami. |
| Udoskonalone odzyskiwanie po awarii i zgodność |StorSimple umożliwia szybsze odzyskiwanie po awarii przez przywrócenie metadanych natychmiast i przywrócenie danych zgodnie z wymaganiami. Normalne operacje mogą kontynuować z minimalnym zakłóceniem. |
| Mobilność danych |Dostęp do danych warstwowych do chmury można uzyskać z innych lokacji na potrzeby odzyskiwania i migracji. Dane można przywrócić tylko do oryginalnej macierzy wirtualnej. Należy jednak użyć funkcji odzyskiwania po awarii, aby przywrócić całą tablicę wirtualną do innej macierzy wirtualnej. |



## <a name="storsimple-workload-summary"></a>Podsumowanie obciążenia StorSimple

Podsumowanie obsługiwanych obciążeń StorSimple znajduje się poniżej.

|Scenariusz     |Obciążenie     |Obsługiwane      |Ograniczenia               | Odpowiednie wersje|
|-------------|-------------|---------------|---------------------------|--------------------|
|Zdalne biuro/oddziały (ROBO)  |Udostępnianie plików     |Tak      |Zobacz [maksymalne limity dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dla obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |
|Archiwizowanie w chmurze  |Udostępnianie plików archiwalnych     |Tak      |Zobacz [maksymalne limity dla serwera plików](storsimple-ova-limits.md).<br></br>Zobacz [wymagania systemowe dla obsługiwanych wersji protokołu SMB](storsimple-ova-system-requirements.md).| Wszystkie wersje     |

Wirtualna macierz StorSimple najlepiej nadaje się w przypadku rzadko używanych danych. Gdy Macierz wirtualna ma lokalną pamięć podręczną, aby zwiększyć wydajność, użytkownicy powinni założyć, że pliki usług urządzenia znajdują się w najniższej warstwie magazynu (w chmurze). Każda Macierz wirtualna może zapisywać i odczytywać dane w usłudze Azure Storage o około 100 MB/s. Ten link jest współużytkowany przez wszystkie żądania przesyłane do urządzenia i może stać się wąskim gardłem, jak pokazano na poniższym diagramie.

![Archiwizowanie w chmurze](./media/storsimple-ova-overview/cloud-archiving.png)

Gdy wielu współbieżnych użytkowników uzyskują dostęp do macierzy wirtualnej, wszystkie one współdzielą połączenie z platformą Azure, co prowadzi do obniżenia wydajności. Nie ma żadnej gwarantowanej wydajności na użytkownika, a urządzenie przetwarza indywidualne żądania po ich nadejściu.

StorSimple Virtual Array nie jest odpowiednia dla obciążeń wymagających wysokiej dostępności. Macierz wirtualna to Jednowęzłowe urządzenie, które jest nieprzerwane podczas instalowania aktualizacji oprogramowania. Administratorzy powinni zaplanować okno obsługi o 30 minut 3-4 razy na rok.

## <a name="workflows"></a>Przepływy pracy

Wirtualna macierz StorSimple jest szczególnie odpowiednia dla następujących przepływów pracy:

* [Zarządzanie magazynem w chmurze](#cloud-based-storage-management)
* [Niezależna od lokalizacji kopia zapasowa](#location-independent-backup)
* [Ochrona danych i odzyskiwanie po awarii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Zarządzanie magazynem w chmurze
Można użyć usługi StorSimple Device Manager działającej w Azure Portal do zarządzania danymi przechowywanymi na wielu urządzeniach i w wielu lokalizacjach. Jest to szczególnie przydatne w scenariuszach rozproszonej gałęzi. Aby zarządzać macierzami wirtualnymi i fizycznymi urządzeniami StorSimple, należy utworzyć osobne wystąpienia usługi StorSimple Device Manager. Macierz wirtualna teraz używa nowego Azure Portal zamiast klasycznego portalu Azure.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![Zarządzanie magazynem w chmurze](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Niezależna od lokalizacji kopia zapasowa
W przypadku macierzy wirtualnej migawki w chmurze oferują niezależną od lokalizacji kopię woluminu lub udziału. Migawki w chmurze są domyślnie włączone i nie można ich wyłączyć. Kopie zapasowe wszystkich woluminów i udziałów są wykonywane w tym samym czasie za pomocą jednej codziennej zasady tworzenia kopii zapasowych i w razie potrzeby można tworzyć dodatkowe kopie zapasowe ad hoc.

### <a name="data-protection-and-disaster-recovery"></a>Ochrona danych i odzyskiwanie po awarii
Macierz wirtualna obsługuje następujące scenariusze ochrony danych i odzyskiwania po awarii:

* **Przywracanie woluminu** lub udziału — Użyj funkcji Przywróć jako nowy przepływ pracy, aby odzyskać wolumin lub udział. Użyj tej metody do odzyskania całego woluminu lub udziału.
* **Odzyskiwanie na poziomie elementu** — udziały umożliwiają uproszczony dostęp do najnowszych kopii zapasowych. Można łatwo odzyskać pojedynczy plik z specjalnego folderu *kopii zapasowej* dostępnego w chmurze. Ta możliwość przywracania jest oparta na użytkowniku i nie jest wymagana interwencja administracyjna.
* **Odzyskiwanie po awarii** — Użyj funkcji przełączania do trybu failover, aby odzyskać wszystkie woluminy lub udziały do nowej macierzy wirtualnej. Tworzysz nową tablicę wirtualną i zarejestrujesz ją w usłudze StorSimple Device Manager, a następnie przeniesiesz do trybu failover oryginalną tablicę wirtualną. W nowej macierzy wirtualnej zostanie założono, że zasoby są inicjowane.

## <a name="storsimple-virtual-array-components"></a>Składniki macierzy wirtualnej StorSimple

Macierz wirtualna obejmuje następujące składniki:

* [Array Virtual](#virtual-array) — hybrydowe urządzenie magazynujące w chmurze oparte na maszynie wirtualnej zainicjowanej w środowisku zwirtualizowanym lub funkcji hypervisor.
* [StorSimple Device Manager](#storsimple-device-manager-service) — rozszerzenie Azure Portal, które umożliwia zarządzanie jednym lub wieloma urządzeniami StorSimple z poziomu jednego interfejsu sieci Web, do którego można uzyskać dostęp z różnych lokalizacji geograficznych. Usługi StorSimple Device Manager można używać do tworzenia usług i zarządzania nimi, wyświetlania urządzeń i alertów oraz zarządzania nimi oraz zarządzania woluminami, udziałami i istniejącymi migawkami.
* [Lokalny interfejs użytkownika sieci Web](#local-web-user-interface) — oparty na sieci Web interfejs użytkownika służący do konfigurowania urządzenia, aby mógł nawiązać połączenie z siecią lokalną, a następnie zarejestrować urządzenie w usłudze StorSimple Device Manager. 
* [Interfejs wiersza polecenia](#command-line-interface) — interfejs programu Windows PowerShell, którego można użyć do uruchomienia sesji obsługi w macierzy wirtualnej.
  W poniższych sekcjach opisano każdy składnik bardziej szczegółowo i wyjaśniono, w jaki sposób rozwiązanie rozmieszcza dane, przydziela magazyn i ułatwia zarządzanie magazynem i ochronę danych.

### <a name="virtual-array"></a>Macierz wirtualna

Macierz wirtualna to rozwiązanie magazynu z jednym węzłem, które zapewnia magazyn podstawowy, zarządza komunikacją z magazynem w chmurze i pomaga zapewnić bezpieczeństwo i poufność wszystkich danych przechowywanych na urządzeniu.

Macierz wirtualna jest dostępna w jednym modelu, który jest dostępny do pobrania. Macierz wirtualna ma maksymalną pojemność wynoszącą 6,4 TB na urządzeniu (z podstawowym wymaganiem magazynu wynoszącym 8 TB) i 64 TB, w tym magazynu w chmurze.

Macierz wirtualna ma następujące funkcje:

* Jest to ekonomiczne. Korzysta ona z istniejącej infrastruktury wirtualizacji i można ją wdrożyć w istniejącej funkcji hypervisor Hyper-V lub VMware.
* Znajduje się w centrum danych i można go skonfigurować jako serwer iSCSI lub serwer plików.
* Jest ona zintegrowana z chmurą.
* Kopie zapasowe są przechowywane w chmurze, co może ułatwić odzyskiwanie po awarii i upraszcza odzyskiwanie na poziomie elementu (ILR).
* Aktualizacje macierzy wirtualnej można zastosować tak samo jak na urządzeniu fizycznym.

> [!NOTE]
> Nie można rozwinąć macierzy wirtualnej. W związku z tym ważne jest, aby zapewnić odpowiednią ilość miejsca podczas tworzenia macierzy wirtualnej.

### <a name="storsimple-device-manager-service"></a>Usługa Menedżer urządzeń StorSimple

Microsoft Azure StorSimple udostępnia interfejs użytkownika oparty na sieci Web, usługę StorSimple Device Manager, która umożliwia centralne zarządzanie magazynem StorSimple. Za pomocą usługi StorSimple Device Manager można wykonywać następujące zadania:

* Zarządzaj wieloma wirtualnymi macierzami StorSimple z poziomu pojedynczej usługi.
* Skonfiguruj ustawienia zabezpieczeń dla wirtualnych tablic StorSimple i zarządzaj nimi. (Szyfrowanie w chmurze jest zależne od Microsoft Azure interfejsów API).
* Skonfiguruj poświadczenia i właściwości konta magazynu.
* Skonfiguruj woluminy lub udziały oraz zarządzaj nimi.
* Tworzenie kopii zapasowych i przywracanie danych na woluminach lub udziałach.
* Monitorowanie wydajności.
* Przejrzyj ustawienia systemowe i zidentyfikuj możliwe problemy.

Usługa StorSimple Device Manager umożliwia codzienne administrowanie macierzą wirtualną.

Aby uzyskać więcej informacji, przejdź do [korzystania z usługi StorSimple Device Manager w celu administrowania urządzeniem StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Lokalny interfejs użytkownika sieci Web

Macierz wirtualna obejmuje interfejs użytkownika oparty na sieci Web, który jest używany do jednorazowej konfiguracji i rejestracji urządzenia za pomocą usługi StorSimple Device Manager. Można go użyć do zamknięcia i ponownego uruchomienia macierzy wirtualnej, uruchomienia testów diagnostycznych, aktualizacji oprogramowania, zmiany hasła administratora urządzenia, wyświetlenia dzienników systemowych i kontaktu pomoc techniczna firmy Microsoft w celu zarejestrowania żądania obsługi.

Aby uzyskać informacje o korzystaniu z interfejsu użytkownika opartego na sieci Web, przejdź do strony [sieci Web za pomocą interfejsu użytkownika do administrowania wirtualną tablicą StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interfejs wiersza polecenia

Dołączony interfejs programu Windows PowerShell umożliwia zainicjowanie sesji pomocy technicznej z pomoc techniczna firmy Microsoft, dzięki czemu mogą one pomóc w rozwiązywaniu problemów i rozwiązywaniu problemów, które mogą wystąpić w macierzy wirtualnej.

## <a name="storage-management-technologies"></a>Technologie zarządzania magazynem

Oprócz macierzy wirtualnej i innych składników rozwiązanie StorSimple korzysta z następujących technologii oprogramowania w celu zapewnienia szybkiego dostępu do ważnych danych, zmniejszenia zużycia magazynu oraz ochrony danych przechowywanych w macierzy wirtualnej:

* [Automatyczne przechowywanie warstw magazynowania](#automatic-storage-tiering) 
* [Przypięte lokalnie udziały i woluminy](#locally-pinned-shares-and-volumes)
* Deduplikacja i kompresja dla danych warstwowych lub kopii zapasowych w chmurze 
* [Zaplanowane i na żądanie kopie zapasowe](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatyczne przechowywanie warstw magazynowania
Macierz wirtualna używa nowego mechanizmu warstwowego do zarządzania przechowywanymi danymi w macierzy wirtualnej i w chmurze. Istnieją tylko dwie warstwy: lokalna Macierz wirtualna i magazyn w chmurze platformy Azure. Wirtualna macierz StorSimple automatycznie rozmieszcza dane w warstwach na podstawie mapy cieplnej, która śledzi bieżące użycie, wiek i relacje z innymi danymi. Dane, które są najbardziej aktywne (okienko) są przechowywane lokalnie, natomiast mniej aktywne i nieaktywne dane są automatycznie migrowane do chmury. (Wszystkie kopie zapasowe są przechowywane w chmurze). StorSimple dostosowuje i zmienia przydziały danych i magazynu w miarę zmiany wzorców użytkowania. Na przykład niektóre informacje mogą stać się mniej aktywne w czasie. Tak jak w przypadku coraz mniejszej aktywności, jest ona warstwą w chmurze. Jeśli te same dane znów staną się aktywne, jest warstwą w macierzy magazynowej.

Dane dotyczące konkretnego udziału warstwowego lub woluminu są gwarantowane w lokalnej przestrzeni warstwowej (około 10% całkowitego miejsca udostępnionego dla tego udziału lub woluminu). Chociaż zmniejsza dostępny magazyn macierzy wirtualnej dla tego udziału lub woluminu, gwarantuje to, że nie będzie to miało wpływ na warstwowe potrzeby innych udziałów lub woluminów. W ten sposób bardzo zajęte obciążenie jednego udziału lub woluminu nie może wymusić wszystkich innych obciążeń w chmurze.

Woluminy warstwowe utworzone dla iSCSI mają maksymalną rezerwację lokalną 200 GB, niezależnie od rozmiaru woluminu.

![automatyczne przechowywanie warstw magazynowania](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Możesz określić wolumin jako przypięty lokalnie, w tym przypadku dane pozostają w macierzy wirtualnej i nigdy nie są warstwowe w chmurze. Aby uzyskać więcej informacji, przejdź do [lokalnie przypiętych udziałów i woluminów](#locally-pinned-shares-and-volumes).

> [!IMPORTANT]
> W przypadku korzystania z StorSimple nie należy konwertować obiektów BLOB do archiwizacji, nawet jeśli urządzenie jest wycofywane. Aby można było pobrać dane z urządzenia, należy je ponownie odczytać z archiwum do typu gorąca lub chłodna, co skutkuje znaczącymi kosztami.


### <a name="locally-pinned-shares-and-volumes"></a>Przypięte lokalnie udziały i woluminy

Odpowiednie udziały i woluminy można utworzyć jako przypięte lokalnie. Ta funkcja zapewnia, że dane wymagane przez krytyczne aplikacje pozostają w macierzy wirtualnej i nigdy nie są warstwowe w chmurze. Udziały przypięte lokalnie i woluminy mają następujące funkcje:

* Nie są one objęte opóźnieniami w chmurze lub problemami z łącznością.
* Nadal korzystają z funkcji tworzenia kopii zapasowych w chmurze StorSimple i odzyskiwania po awarii.

Można przywrócić udział przypięty lokalnie lub wolumin jako warstwowy lub udział warstwowy lub wolumin jako przypięty lokalnie. 

Aby uzyskać więcej informacji na temat woluminów przypiętych lokalnie, przejdź do [obszaru Zarządzanie woluminami przy użyciu usługi StorSimple Device Manager](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplikacja i kompresja dla danych warstwowych lub kopii zapasowych w chmurze

StorSimple używa deduplikacji i kompresji danych, aby dodatkowo ograniczyć wymagania dotyczące magazynu w chmurze. Deduplikacja zmniejsza ogólną ilość danych przechowywanych przez wyeliminowanie nadmiarowości w zestawie danych przechowywanych. Wraz ze zmianami informacji StorSimple ignoruje niezmienione dane i przechwytuje tylko te zmiany. Ponadto StorSimple zmniejsza ilość przechowywanych danych, identyfikując i usuwając zduplikowane informacje.

> [!NOTE]
> Dane przechowywane w macierzy wirtualnej nie są deduplikowane lub skompresowane. Cała Deduplikacja i kompresja odbywa się tuż przed wysłaniem danych do chmury.


### <a name="scheduled-and-on-demand-backups"></a>Zaplanowane i na żądanie kopie zapasowe

Funkcje ochrony danych StorSimple umożliwiają tworzenie kopii zapasowych na żądanie. Ponadto domyślny harmonogram tworzenia kopii zapasowych gwarantuje, że kopie zapasowe danych są tworzone codziennie. Kopie zapasowe są tworzone w postaci migawek przyrostowych, które są przechowywane w chmurze. Migawki, które rejestrują tylko zmiany od ostatniej kopii zapasowej, mogą być tworzone i przywracane szybko. Te migawki mogą być krytycznie ważne w scenariuszach odzyskiwania po awarii, ponieważ zastępują dodatkowe systemy magazynów (na przykład kopię zapasową na taśmach) i umożliwiają przywrócenie danych do centrum dane lub lokacji alternatywnych w razie potrzeby.

## <a name="managing-personal-information"></a>Zarządzanie informacjami osobistymi

StorSimple Device Manager dla serii wirtualnej zbiera dane osobowe w dwóch kluczowych wystąpieniach:
 - Alert ustawienia użytkownika, w którym są skonfigurowane adresy e-mail użytkowników. Te informacje mogą zostać wyczyszczone przez administratora. 
 - Użytkownicy, którzy mogą uzyskiwać dostęp do danych w udziałach. Zostanie wyświetlona lista użytkowników, którzy mogą uzyskiwać dostęp do danych udziału. Ta lista jest usuwana po usunięciu udziału.

Aby uzyskać więcej informacji, zapoznaj się z [zasadami ochrony prywatności firmy Microsoft w centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak przygotować Portal macierzy wirtualnej](storsimple-virtual-array-deploy1-portal-prep.md).
