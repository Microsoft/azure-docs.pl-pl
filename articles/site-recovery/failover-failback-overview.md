---
title: Informacje o trybie failover i powrotu po awarii w Azure Site Recovery
description: Więcej informacji na temat trybu failover i niepowodzenia w Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87089536"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Informacje o lokalnym przejściu w tryb failover i powrotu po awarii

Ten artykuł zawiera omówienie trybu failover i powrotu po awarii podczas odzyskiwania po awarii maszyn lokalnych na platformie Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Etapy odzyskiwania

Przełączanie do trybu failover i powrotu po awarii w Site Recovery ma cztery etapy:

- **Etap 1: przełączenie w tryb failover z lokalnego**: po skonfigurowaniu replikacji na platformie Azure dla maszyn lokalnych, gdy lokacja lokalna przestanie działać, te maszyny nie przechodzą na platformę Azure. Po przejściu w tryb failover maszyny wirtualne platformy Azure są tworzone na podstawie replikowanych danych.
- **Etap 2: Ponowne włączanie ochrony maszyn wirtualnych platformy Azure**: na platformie Azure można ponownie włączyć ochronę maszyn wirtualnych platformy Azure, aby umożliwić ich replikację z powrotem do lokacji lokalnej. Lokalna maszyna wirtualna (jeśli jest dostępna) jest wyłączona podczas ponownej ochrony, aby zapewnić spójność danych.
- **Etap 3:** przełączenie w tryb failover z platformy Azure: gdy lokacja lokalna jest uruchomiona ponownie, należy uruchomić inną pracę w trybie pracy awaryjnej, tym razem, aby zakończyć przywracanie maszyn wirtualnych platformy Azure do lokacji lokalnej. Możesz wrócić do oryginalnej lokalizacji, z której przełączysz się w tryb failover lub do innej lokalizacji.
- **Etap 4: Ponowne włączanie ochrony maszyn lokalnych**: po niepowodzeniu z powrotem Włącz replikację maszyn lokalnych na platformę Azure.

## <a name="failover"></a>Tryb failover

Przełączenie w tryb failover jest częścią strategii ciągłości działania i odzyskiwania po awarii (BCDR).

- Pierwszym krokiem w strategii BCDR jest ciągła Replikacja maszyn lokalnych na platformę Azure. Użytkownicy uzyskują dostęp do obciążeń i aplikacji działających na lokalnych maszynach źródłowych.
- Jeśli zachodzi taka potrzeba, na przykład w przypadku awarii w środowisku lokalnym, replikacja maszyn nie powiedzie się na platformie Azure. Maszyny wirtualne platformy Azure są tworzone przy użyciu replikowanych danych.
- W celu zapewnienia ciągłości biznesowej użytkownicy mogą nadal uzyskiwać dostęp do aplikacji na maszynach wirtualnych platformy Azure.

Tryb failover to działanie dwufazowe:

- **Tryb failover**: przejście w tryb failover, które tworzy i uruchamia maszynę wirtualną platformy Azure przy użyciu wybranego punktu odzyskiwania.
- **Zatwierdzenie**: po przejściu do trybu failover Zweryfikuj maszynę wirtualną na platformie Azure:
    - Następnie możesz zatwierdzić przejście w tryb failover do wybranego punktu odzyskiwania lub wybrać inny punkt dla zatwierdzenia.
    - Po zatwierdzeniu trybu failover nie można zmienić punktu odzyskiwania.


## <a name="connect-to-azure-after-failover"></a>Połącz z platformą Azure po przejściu w tryb failover

Aby nawiązać połączenie z maszynami wirtualnymi platformy Azure utworzonymi po przejściu do trybu failover przy użyciu protokołu RDP/SSH, istnieje kilka wymagań.

**Tryb failover** | **Lokalizacja** | **Akcje**
--- | --- | ---
**Maszyna wirtualna platformy Azure (system Windows (** | Na maszynie lokalnej przed przejściem w tryb failover | **Dostęp za pośrednictwem Internetu**: Włącz protokół RDP. Upewnij się, że reguły TCP i UDP są dodawane do **publicznej**wersji, a protokół RDP jest dozwolony dla wszystkich profilów w aplikacjach **zapory systemu Windows**  >  **dozwolone aplikacje**.<br/><br/> **Dostęp za pośrednictwem sieci VPN typu lokacja-lokacja**: Włącz protokół RDP na maszynie. Sprawdź, czy protokół RDP jest dozwolony **Windows Firewall**w  ->  przypadku**aplikacji i funkcji dozwolonych**przez zaporę systemu Windows w przypadku sieci **i domen prywatnych** .<br/><br/>  Upewnij się, że zasady sieci SAN systemu operacyjnego są ustawione na **OnlineAll**. [Dowiedz się więcej](https://support.microsoft.com/kb/3031135).<br/><br/> Upewnij się, że nie ma żadnych oczekujących aktualizacji systemu Windows na maszynie wirtualnej podczas wyzwalania trybu failover. Windows Update może rozpocząć się po przełączeniu w tryb failover i nie będzie można zalogować się do maszyny wirtualnej, dopóki aktualizacje nie zostaną wykonane.
**Maszyna wirtualna platformy Azure z systemem Windows** | Na maszynie wirtualnej platformy Azure po przejściu w tryb failover |  [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu RDP.<br/><br/> Sprawdź **diagnostykę rozruchu** , aby zweryfikować zrzut ekranu maszyny wirtualnej. Jeśli nie możesz się połączyć, sprawdź, czy maszyna wirtualna jest uruchomiona, i przejrzyj [wskazówki dotyczące rozwiązywania problemów](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**Maszyna wirtualna platformy Azure z systemem Linux** | Na maszynie lokalnej przed przejściem w tryb failover | Upewnij się, że usługa Secure Shell na maszynie wirtualnej jest uruchamiana automatycznie przy rozruchu systemu.<br/><br/> Sprawdź, czy reguły zapory zezwalają na połączenie SSH.
**Maszyna wirtualna platformy Azure z systemem Linux** | Na maszynie wirtualnej platformy Azure po przejściu w tryb failover | Reguły sieciowej grupy zabezpieczeń na maszynie wirtualnej w trybie failover (i podsieci platformy Azure, z którą jest połączona) muszą zezwalać na połączenia przychodzące do portu SSH.<br/><br/> [Dodaj publiczny adres IP](https://aka.ms/addpublicip) dla maszyny wirtualnej.<br/><br/> Sprawdź **diagnostykę rozruchu** dla zrzutu ekranu maszyny wirtualnej.<br/><br/>

## <a name="types-of-failover"></a>Typy trybu failover

Site Recovery udostępnia różne opcje trybu failover.

**Tryb failover** | **Szczegóły** | **Odzyskiwania** | **Przepływ pracy**
--- | --- | --- | ---
**Testowanie pracy w trybie failover** | Służy do przeprowadzania przechodzenia do szczegółów, który weryfikuje strategię BCDR bez utraty danych ani przestojów.| Tworzy kopię maszyny wirtualnej na platformie Azure bez wpływu na bieżącą replikację ani w środowisku produkcyjnym. | 1. Uruchom test pracy w trybie failover na jednej maszynie wirtualnej lub na wielu maszynach wirtualnych w planie odzyskiwania.<br/><br/> 2. Wybierz punkt odzyskiwania do użycia w testowaniu pracy w trybie failover.<br/><br/> 3. Wybierz sieć platformy Azure, w której zostanie umieszczona maszyna wirtualna platformy Azure po jej utworzeniu po przejściu do trybu failover. Sieć jest używana tylko na potrzeby testu pracy w trybie failover.<br/><br/> 4. Sprawdź, czy przechodzenie do szczegółów zadziałało zgodnie z oczekiwaniami. Site Recovery automatycznie czyści maszyny wirtualne utworzone na platformie Azure podczas przechodzenia do szczegółów.
**Planowana praca w trybie failover — funkcja Hyper-V**  | Zwykle używany do planowanych przestojów.<br/><br/> Źródłowe maszyny wirtualne są zamykane. Najnowsze dane są synchronizowane przed zainicjowaniem trybu failover. | Zero utraty danych dla planowanego przepływu pracy. | 1. Zaplanuj okno obsługi przestoju i Powiadom użytkowników.<br/><br/> 2. Korzystaj z aplikacji dla użytkowników w trybie offline.<br/><br/> 3. zainicjuj planowaną pracę w trybie failover przy użyciu najnowszego punktu odzyskiwania. Tryb failover nie jest uruchamiany, jeśli maszyna nie jest wyłączana lub występują błędy.<br/><br/> 4. po przejściu do trybu failover Sprawdź, czy replika maszyny wirtualnej platformy Azure jest aktywna na platformie Azure.<br/><br/> 5. Zatwierdź pracę w trybie failover, aby zakończyć. Akcja Zatwierdź usuwa wszystkie punkty odzyskiwania.
**Tryb failover funkcji Hyper-V** | Zwykle uruchamiany, jeśli wystąpi nieplanowana awaria lub lokacja główna jest niedostępna.<br/><br/> Opcjonalnie Zamknij maszynę wirtualną i zsynchronizuj zmiany końcowe przed zainicjowaniem trybu failover.  | Minimalna utrata danych dla aplikacji. | 1. zainicjuj plan BCDR. <br/><br/> 2. zainicjuj pracę w trybie failover. Określ, czy Site Recovery powinien zamknąć maszynę wirtualną, a następnie zsynchronizuj/Replikuj najnowsze zmiany przed wyzwoleniem trybu failover.<br/><br/> 3. możesz przejść w tryb failover do kilku opcji punktów odzyskiwania, które zostały podsumowane w poniższej tabeli.<br/><br/> Jeśli nie włączysz opcji zamykania maszyny wirtualnej lub jeśli Site Recovery nie można jej zamknąć, zostanie użyty najnowszy punkt odzyskiwania.<br/>Tryb failover działa nawet wtedy, gdy nie można wyłączyć maszyny.<br/><br/> 4. po przejściu do trybu failover Sprawdź, czy replika maszyny wirtualnej platformy Azure jest aktywna na platformie Azure.<br/> W razie potrzeby możesz wybrać inny punkt odzyskiwania z okna przechowywania w ciągu 24 godzin.<br/><br/> 5. Zatwierdź pracę w trybie failover, aby zakończyć. Akcja Zatwierdź usuwa wszystkie dostępne punkty odzyskiwania.
**Tryb failover — VMware** | Zwykle uruchamiany, jeśli wystąpi nieplanowana awaria lub lokacja główna jest niedostępna.<br/><br/> Opcjonalnie można określić, że Site Recovery powinna próbować wyzwolić zamknięcie maszyny wirtualnej, a następnie zsynchronizować i replikować końcowe zmiany przed zainicjowaniem trybu failover.  | Minimalna utrata danych dla aplikacji. | 1. zainicjuj plan BCDR. <br/><br/> 2. zainicjuj pracę w trybie failover z poziomu Site Recovery. Określ, czy Site Recovery powinien próbować wyzwolić zamknięcie maszyny wirtualnej i zsynchronizować ją przed uruchomieniem trybu failover.<br/> Tryb failover działa nawet wtedy, gdy nie można wyłączyć maszyn.<br/><br/> 3. po zakończeniu pracy w trybie failover Sprawdź, czy replika maszyny wirtualnej platformy Azure jest aktywna na platformie Azure. <br/>W razie potrzeby możesz wybrać inny punkt odzyskiwania z okna przechowywania 72 godzin.<br/><br/> 5. Zatwierdź pracę w trybie failover, aby zakończyć. Akcja Zatwierdź usuwa wszystkie punkty odzyskiwania.<br/> W przypadku maszyn wirtualnych z systemem Windows Site Recovery wyłącza narzędzia VMware podczas pracy w trybie failover.

## <a name="failover-processing"></a>Przetwarzanie w trybie failover

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego ukończenie trwa od 8 do 10 minut. Możesz zauważyć dłuższe czasy testowania pracy w trybie failover dla:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszą niż 9,8.
* Serwery fizyczne.
* Maszyny wirtualne VMware Linux.
* Maszyny wirtualne funkcji Hyper-V chronione jako serwery fizyczne.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, VMBus, storflt, Intelide, ATAPI.

## <a name="recovery-point-options"></a>Opcje punktu odzyskiwania

Podczas pracy w trybie failover można wybrać kilka opcji punktów odzyskiwania.

**Opcja** | **Szczegóły**
--- | ---
**Najnowsze (najniższy cel punktu odzyskiwania)** | Ta opcja zapewnia najniższy cel punktu odzyskiwania (RPO). Najpierw przetwarza wszystkie dane, które zostały wysłane do usługi Site Recovery, aby utworzyć punkt odzyskiwania dla każdej maszyny wirtualnej przed przejściem w tryb failover. Ten punkt odzyskiwania ma wszystkie dane zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
**Ostatnie przetworzone**  | Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Aby wyświetlić najnowszy punkt odzyskiwania dla określonej maszyny wirtualnej, sprawdź **najnowsze punkty odzyskiwania** w ustawieniach maszyny wirtualnej. Ta opcja zapewnia niską wartość celu czasu odzyskiwania, ponieważ nie wymaga przetwarzania nieprzetworzonych danych.
**Najnowsza aplikacja — spójna** |  Ta opcja powoduje przełączenie maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery, jeśli punkty odzyskiwania spójne z aplikacjami są włączone. Sprawdź najnowszy punkt odzyskiwania w ustawieniach maszyny wirtualnej.
**Najnowsza przetworzona wiele maszyn wirtualnych** | Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jedną maszyną wirtualną, na której włączono spójność wielu maszyn wirtualnych. Maszyny wirtualne z włączonym ustawieniem przechodzą w tryb failover do najnowszego wspólnego punktu odzyskiwania z wielowątkową maszyną wirtualną. Wszystkie inne maszyny wirtualne w planie są przełączane w tryb failover do ostatniego przetworzonego punktu odzyskiwania.
**Najnowsza aplikacja obsługująca wiele maszyn wirtualnych — spójna** |  Ta opcja jest dostępna w przypadku planów odzyskiwania z co najmniej jedną maszyną wirtualną, na której włączono spójność wielu maszyn wirtualnych. Maszyny wirtualne, które są częścią grupy replikacji, są przełączane w tryb failover do najnowszego wspólnego punktu odzyskiwania spójnego z aplikacjami wielomaszynowo. Inne maszyny wirtualne są przełączane w tryb failover do najnowszego punktu odzyskiwania spójnego z aplikacją.
**Niestandardowe** | Użyj tej opcji, aby przełączyć konkretną maszynę wirtualną w tryb failover do określonego punktu odzyskiwania w czasie. Ta opcja jest niedostępna w przypadku planów odzyskiwania.

> [!NOTE]
> Nie można migrować punktów odzyskiwania do innego magazynu Recovery Services.

## <a name="reprotectionfailback"></a>Ochrona/powrót po awarii

Po przejściu do trybu failover na platformie Azure zreplikowane maszyny wirtualne platformy Azure są w stanie niechronionym.

- Pierwszym krokiem w przypadku powrotu po awarii do lokacji lokalnej jest uruchomienie maszyn wirtualnych platformy Azure replikowanych w środowisku lokalnym. Proces ponownego włączania ochrony zależy od typu maszyn przetworzonych w tryb failover.
- Gdy maszyny są replikowane z platformy Azure do lokacji lokalnej, można uruchomić tryb failover z platformy Azure do swojej witryny lokalnej.
- Po ponownym uruchomieniu maszyn w środowisku lokalnym można włączyć replikację, aby replikować dane na platformę Azure w celu odzyskiwania po awarii.

Powrót po awarii przebiega w następujący sposób:

- Aby powrócić po awarii, maszyna wirtualna musi mieć co najmniej jeden punkt odzyskiwania w celu powrotu po awarii. W planie odzyskiwania wszystkie maszyny wirtualne w planie muszą mieć co najmniej jeden punkt odzyskiwania.
- Zalecamy używanie **najnowszego** punktu odzyskiwania do powrotu po awarii (jest to punkt spójny z awarią).
    - Istnieje opcja punktu odzyskiwania spójna z aplikacją. W takim przypadku Pojedyncza maszyna wirtualna odzyskuje do najnowszego dostępnego punktu odzyskiwania spójnego na poziomie aplikacji. W przypadku planu odzyskiwania z grupą replikacji każda grupa replikacji odzyskuje do wspólnego dostępnego punktu odzyskiwania.
    - Punkty odzyskiwania spójne z aplikacjami mogą znajdować się w czasie i mogą ulec utracie dane.
- Podczas pracy w trybie failover z platformy Azure do lokacji lokalnej program Site Recovery wyłącza maszyny wirtualne platformy Azure. Po zatwierdzeniu trybu failover program Site Recovery usuwa uszkodzone maszyny wirtualne platformy Azure na platformie Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>Oprogramowanie VMware/ochrona fizyczna/powrót po awarii

W celu ponownego włączenia ochrony i powrotu maszyn VMware i serwerów fizycznych z platformy Azure do lokacji lokalnej potrzebna jest infrastruktura powrotu po awarii i istnieje kilka wymagań.

- **Tymczasowy serwer przetwarzania na platformie Azure**: aby powrócić po awarii z platformy Azure, należy skonfigurować maszynę wirtualną platformy Azure do działania jako serwer przetwarzania w celu obsługi replikacji z platformy Azure. Po zakończeniu powrotu po awarii można usunąć tę maszynę wirtualną.
- **Połączenie sieci VPN**: w celu powrotu po awarii wymagane jest połączenie sieci VPN (lub ExpressRoute) z sieci platformy Azure z lokacją lokalną.
- **Oddzielny główny serwer docelowy**: domyślnie główny serwer docelowy, który został zainstalowany z serwerem konfiguracji na lokalnej maszynie wirtualnej VMware obsługuje powrót po awarii. Jeśli zachodzi potrzeba odtworzenia dużych ilości ruchu, należy skonfigurować w tym celu oddzielny lokalny główny serwer docelowy.
- **Zasady powrotu po awarii**: aby móc przeprowadzić ponowną replikację do lokacji lokalnej, należy utworzyć zasady powrotu po awarii. Te zasady są tworzone automatycznie podczas tworzenia zasad replikacji z lokalnego na platformę Azure.
    - Te zasady są automatycznie kojarzone z serwerem konfiguracji.
    - Nie można edytować tych zasad.
    - Wartości zasad: próg punktu odzyskiwania — 15 minut; Przechowywanie punktu odzyskiwania — 24 godziny; Częstotliwość migawek spójnych na poziomie aplikacji — 60 minut.

Dowiedz się więcej o oprogramowaniu VMware/fizycznej reochrony i powrotu po awarii:
- [Zapoznaj](vmware-azure-reprotect.md#before-you-begin) się z dodatkowymi wymaganiami dotyczącymi ochrony i powrotu po awarii.
- [Wdróż](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) serwer przetwarzania na platformie Azure.
- [Wdróż](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) oddzielny główny serwer docelowy.

Po ponownym włączeniu ochrony maszyn wirtualnych platformy Azure w środowisku lokalnym można określić, że chcesz powrócić po awarii do oryginalnej lokalizacji lub do innej lokalizacji.

- **Odzyskiwanie do lokalizacji oryginalnej**: to samo kończy się z powrotem z platformy Azure do tej samej źródłowej maszyny lokalnej, jeśli istnieje. W tym scenariuszu tylko zmiany są replikowane z powrotem do lokalnego.
- **Odzyskiwanie lokalizacji alternatywnej**: Jeśli maszyna lokalna nie istnieje, możesz wrócić z platformy Azure do innej lokalizacji. W przypadku ponownej ochrony maszyny wirtualnej platformy Azure w środowisku lokalnym tworzony jest komputer lokalny. Replikacja pełnej danych z platformy Azure odbywa się lokalnie. — [Zapoznaj](concepts-types-of-failback.md) się z wymaganiami i ograniczeniami dotyczącymi powrotu po awarii.



## <a name="hyper-v-reprotectionfailback"></a>Ponownej ochrony funkcji Hyper-V/powrotu po awarii

Aby ponownie chronić maszyny wirtualne funkcji Hyper-V z platformy Azure i przywracać je w środowisku lokalnym:

- Istnieje możliwość powrotu po awarii maszyn wirtualnych funkcji Hyper-V, które są replikowane przy użyciu konta magazynu. Powrót po awarii maszyn wirtualnych funkcji Hyper-V, które są replikowane przy użyciu dysków zarządzanych, nie jest obsługiwany.
- Lokalne hosty funkcji Hyper-V (jeśli są używane) powinny być połączone z platformą Azure.
- Planowane powrotu po awarii z platformy Azure do lokacji lokalnej.
- Nie trzeba konfigurować określonych składników na potrzeby powrotu po awarii maszyny wirtualnej funkcji Hyper-V.
- Podczas planowanej pracy w trybie failover można wybrać opcje synchronizacji danych przed powrotem po awarii:
    - **Synchronizuj dane przed**przełączeniem w tryb failover: Ta opcja minimalizuje przestoje maszyn wirtualnych podczas synchronizowania maszyn bez wyłączania ich.
        - Faza 1: tworzy migawkę maszyny wirtualnej platformy Azure i kopiuje ją do lokalnego hosta funkcji Hyper-V. Maszyna kontynuuje działanie na platformie Azure.
        - Faza 2: zamyka maszynę wirtualną platformy Azure, aby nie pojawiły się żadne nowe zmiany. Końcowy zestaw zmian różnicowych jest transferowany na serwer lokalny, a lokalna maszyna wirtualna jest uruchamiana.
    - **Synchronizuj dane tylko podczas pracy w trybie failover**: Ta opcja jest szybsza, ponieważ oczekuje się, że większość dysku uległa zmianie i w związku z tym nie wykonuje obliczeń sum kontrolnych. Pobiera dysk. Zalecamy użycie tej opcji, jeśli maszyna wirtualna była uruchomiona na platformie Azure przez pewien czas (co miesiąc lub więcej) lub jeśli lokalna maszyna wirtualna została usunięta.

[Dowiedz się więcej](hyper-v-azure-failback.md) na temat ponownej ochrony funkcji Hyper-V i powrotu po awarii.

Po ponownym włączeniu ochrony maszyn wirtualnych platformy Azure w środowisku lokalnym można określić, że chcesz powrócić po awarii do oryginalnej lokalizacji lub do innej lokalizacji.

- **Odzyskiwanie do lokalizacji oryginalnej**: to samo kończy się z powrotem z platformy Azure do tej samej źródłowej maszyny lokalnej, jeśli istnieje. W tym scenariuszu należy wybrać jedną z opcji synchronizacji opisanych w poprzedniej procedurze.
- **Odzyskiwanie lokalizacji alternatywnej**: Jeśli maszyna lokalna nie istnieje, możesz wrócić z platformy Azure do innej lokalizacji. W przypadku ponownej ochrony maszyny wirtualnej platformy Azure w środowisku lokalnym tworzony jest komputer lokalny. W przypadku tej opcji zaleca się wybranie opcji synchronizowania danych przed przełączeniem w tryb failover
- [Zapoznaj](hyper-v-azure-failback.md) się z wymaganiami i ograniczeniami dotyczącymi powrotu po awarii.


Po powrocie po awarii do lokacji lokalnej należy włączyć **replikację odwrotną** , aby rozpocząć REPLIKACJĘ maszyny wirtualnej na platformę Azure, kończąc cykl.




## <a name="next-steps"></a>Następne kroki
- Przełączenie w tryb failover [określonych maszyn wirtualnych programu VMware](vmware-azure-tutorial-failover-failback.md)
- Przełączenie w tryb failover [określonych maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failover-failback-tutorial.md).
- [Utwórz](site-recovery-create-recovery-plans.md) plan odzyskiwania.
- Praca awaryjna [maszyn wirtualnych w planie odzyskiwania](site-recovery-failover.md).
- [Przygotuj do](vmware-azure-failback.md) Ochrona VMware i powrót po awarii.
- Powrót po awarii [maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failback.md).

