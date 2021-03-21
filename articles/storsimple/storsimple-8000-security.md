---
title: Zabezpieczenia z serii StorSimple 8000 | Microsoft Docs
description: W tym artykule opisano funkcje zabezpieczeń i ochrony prywatności, które chronią usługę StorSimple, urządzenie i dane w środowisku lokalnym i w chmurze.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: 72bf252488878c8b6fabb2ea2d61658a2cd87e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94966211"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple zabezpieczeń i ochrony danych

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Bezpieczeństwo jest głównym problemem dla każdego, kto przyjmuje nową technologię, szczególnie gdy technologia jest używana z danymi poufnymi lub zastrzeżonymi. Podczas obliczania różnych technologii należy wziąć pod uwagę zwiększone ryzyko i koszty ochrony danych. Microsoft Azure StorSimple zapewnia zarówno rozwiązanie zabezpieczeń, jak i ochronę prywatności w zakresie ochrony danych, pomagając w zapewnieniu:

* **Poufność** — tylko autoryzowane jednostki mogą wyświetlać dane.
* **Integralność** — tylko autoryzowane jednostki mogą modyfikować lub usuwać dane.

Microsoft Azure StorSimple rozwiązanie składa się z czterech głównych składników, które współpracują ze sobą:

* **Usługa StorSimple Device Manager hostowana w Microsoft Azure** — usługa zarządzania, która służy do konfigurowania i aprowizacji urządzenia StorSimple.
* **Urządzenie StorSimple** — urządzenie fizyczne zainstalowane w centrum danych. Wszystkie hosty i klienci generujący dane nawiązują połączenie z urządzeniem StorSimple, a urządzenie zarządza danymi i przenosi je do chmury platformy Azure zgodnie z potrzebami.
* **Klienci/hosty połączeni z urządzeniem** — klienci w infrastrukturze, którzy łączą się z urządzeniem StorSimple i generują dane, które muszą być chronione.
* **Magazyn w chmurze** — lokalizacja w chmurze platformy Azure, w której są przechowywane dane.

W poniższych sekcjach opisano funkcje zabezpieczeń StorSimple, które ułatwiają ochronę każdego z tych składników oraz przechowywanych na nich danych. Zawiera również listę pytań, które mogą mieć Microsoft Azure StorSimple zabezpieczenia i odpowiadające im odpowiedzi.

## <a name="storsimple-device-manager-service-protection"></a>Ochrona usługi StorSimple Device Manager

Usługa StorSimple Device Manager to usługa zarządzania hostowana w usłudze Microsoft Azure i służąca do zarządzania wszystkimi urządzeniami StorSimple, które zostały zamówione w organizacji. Możesz uzyskać dostęp do usługi StorSimple Device Manager przy użyciu poświadczeń organizacji, aby zalogować się do Azure Portal za pośrednictwem przeglądarki sieci Web.

Dostęp do usługi StorSimple Device Manager wymaga, aby Twoja organizacja miała subskrypcję platformy Azure obejmującą StorSimple. Subskrypcja określa funkcje, do których masz dostęp w witrynie Azure Portal. Jeśli Twoja organizacja nie ma subskrypcji platformy Azure i chcesz dowiedzieć się więcej na ich temat, zobacz [Rejestrowanie się w usłudze Azure jako organizacja](../active-directory/fundamentals/sign-up-organization.md).

Ponieważ Usługa StorSimple Device Manager jest hostowana na platformie Azure, jest chroniona przez funkcje zabezpieczeń platformy Azure. Aby uzyskać więcej informacji na temat funkcji zabezpieczeń platformy Microsoft Azure, zobacz [Centrum zaufania Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Ochrona urządzenia StorSimple

Urządzenie StorSimple to lokalne hybrydowe urządzenie magazynujące, które zawiera dyski półprzewodnikowe (dysków SSD) i dyski twarde (HDD), a także nadmiarowe kontrolery i automatyczne przełączanie w tryb failover. Kontrolery zarządzają warstwami magazynowania, umieszczając aktualnie używane (lub gorącą) dane w lokalnym magazynie (na urządzeniu StorSimple lub na serwerach lokalnych), jednocześnie przenosząc rzadziej używane dane do chmury.

Tylko autoryzowane urządzenia StorSimple mogą dołączać do usługi StorSimple Device Manager utworzonej w ramach subskrypcji platformy Azure. Aby autoryzować urządzenie, należy je zarejestrować w usłudze StorSimple Device Manager, podając klucz rejestracji usługi. Klucz rejestracji usługi jest 128-bitowym kluczem losowym wygenerowanym w Azure Portal.

![Klucz rejestracji usługi](./media/storsimple-security/ServiceRegistrationKey.png)

Aby dowiedzieć się, jak uzyskać klucz rejestracji usługi, przejdź do [kroku 2: Pobieranie klucza rejestracji usługi](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Klucz rejestracji usługi jest długim kluczem zawierającym 100 znaków. Możesz skopiować klucz i zapisać go w pliku tekstowym w bezpiecznej lokalizacji, aby można było go użyć do autoryzowania dodatkowych urządzeń w razie potrzeby. Jeśli klucz rejestracji usługi zostanie utracony po zarejestrowaniu pierwszego urządzenia, można wygenerować nowy klucz z usługi StorSimple Device Manager. Nie ma to wpływu na działanie istniejących urządzeń.

Po zarejestrowaniu urządzenia używa on tokenów do komunikowania się z Microsoft Azure. Klucz rejestracji usługi nie jest używany po rejestracji urządzeń.

> [!NOTE]
> Zalecamy ponowne wygenerowanie klucza rejestracji usługi po każdym użyciu.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Ochrona rozwiązania StorSimple za pośrednictwem haseł

Hasła są ważnym aspektem zabezpieczeń komputera i są używane w szerokim stopniu w rozwiązaniu StorSimple, aby zagwarantować, że dane są dostępne tylko dla autoryzowanych użytkowników. StorSimple umożliwia skonfigurowanie następujących haseł:

* Hasło administratora urządzenia StorSimple
* Inicjator protokołu uwierzytelniania typu Challenge Handshake (CHAP) i hasła docelowe
* Hasło programu StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>program Windows PowerShell dla usługi StorSimple i hasło administratora urządzenia StorSimple

Program Windows PowerShell dla usługi StorSimple jest interfejs wiersza polecenia, którego można użyć do zarządzania urządzeniem StorSimple. Program Windows PowerShell dla usługi StorSimple zawiera funkcje, które umożliwiają rejestrowanie urządzenia, Konfigurowanie interfejsu sieciowego na urządzeniu, instalowanie niektórych typów aktualizacji, rozwiązywanie problemów z urządzeniem przez uzyskanie dostępu do sesji pomocy technicznej i zmiana stanu urządzenia. Dostęp do program Windows PowerShell dla usługi StorSimple można uzyskać, łącząc się z konsolą szeregową na urządzeniu lub przy użyciu funkcji komunikacji zdalnej programu Windows PowerShell.

Komunikacja zdalna programu PowerShell może odbywać się za pośrednictwem protokołu HTTPS lub HTTP. Jeśli zdalne zarządzanie za pośrednictwem protokołu HTTPS jest włączone, należy pobrać certyfikat zdalnego zarządzania z urządzenia i zainstalować go na kliencie zdalnym. Aby uzyskać więcej informacji na temat komunikacji zdalnej programu PowerShell, przejdź do obszaru [Połącz zdalnie z urządzeniem StorSimple](storsimple-8000-remote-connect.md).

Po użyciu program Windows PowerShell dla usługi StorSimple do nawiązania połączenia z urządzeniem należy podać hasło administratora urządzenia, aby zalogować się na urządzeniu.

![Hasło administratora urządzenia](./media/storsimple-security/DeviceAdminPW.png)

Należy pamiętać o następujących najlepszych rozwiązaniach:

* Zdalne zarządzanie jest domyślnie wyłączone. Aby ją włączyć, można użyć usługi StorSimple Device Manager. Najlepszym rozwiązaniem w zakresie zabezpieczeń jest włączenie dostępu zdalnego tylko w czasie, gdy jest to konieczne.
* Jeśli zmienisz hasło, pamiętaj o poinformowaniu wszystkich użytkowników dostępu zdalnego, aby nie napotykali nieoczekiwanej utraty łączności.
* Usługa StorSimple Device Manager nie może pobrać istniejących haseł: można je zresetować tylko. Zalecamy przechowywanie wszystkich haseł w bezpiecznym miejscu, aby nie trzeba było resetować hasła, jeśli jest ono zapomniane. Jeśli musisz zresetować hasło, pamiętaj o poinformowaniu wszystkich użytkowników przed jego zresetowaniem.

Dostęp do interfejsu programu Windows PowerShell można uzyskać przy użyciu połączenia szeregowego z urządzeniem. Możesz również uzyskać do niego dostęp zdalnie przy użyciu protokołu HTTP lub HTTPS, który zapewnia dodatkowe zabezpieczenia. Protokół HTTPS zapewnia wyższy poziom zabezpieczeń niż połączenie szeregowe lub HTTP. Aby jednak korzystać z protokołu HTTPS, należy najpierw zainstalować certyfikat na komputerze klienckim, który będzie miał dostęp do urządzenia. Certyfikat dostępu zdalnego można pobrać ze strony Konfiguracja urządzenia w usłudze StorSimple Device Manager. Jeśli certyfikat dostępu zdalnego zostanie utracony, należy pobrać nowy certyfikat i propagować go do wszystkich klientów uprawnionych do korzystania z zdalnego zarządzania.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Inicjator protokołu uwierzytelniania typu Challenge Handshake (CHAP) i hasła docelowe

Protokół CHAP jest schematem uwierzytelniania używanym przez urządzenie StorSimple do weryfikowania tożsamości klientów zdalnych. Weryfikacja jest oparta na udostępnionym haśle. Protokół CHAP może być jednokierunkowe (jednokierunkowe) lub wzajemnie (dwukierunkowe). Mając jednokierunkowe uwierzytelnianie CHAP, obiekt docelowy (Urządzenie StorSimple) uwierzytelnia inicjatora (hosta). Wzajemny lub odwrotny protokół CHAP wymaga, aby element docelowy uwierzytelniał inicjator, a następnie inicjator uwierzytelniał element docelowy. StorSimple można skonfigurować do korzystania z dowolnej metody.

Podczas konfigurowania protokołu CHAP należy pamiętać o następujących kwestiach:

* Nazwa użytkownika protokołu CHAP musi zawierać mniej niż 233 znaków.
* Hasło protokołu CHAP musi mieć długość od 12 do 16 znaków. Próba użycia dłuższej nazwy użytkownika lub hasła spowoduje niepowodzenie uwierzytelniania na hoście z systemem Windows.
* Nie można użyć tego samego hasła dla inicjatora protokołu CHAP i obiektu docelowego protokołu CHAP.
* Po ustawieniu hasła można je zmienić, ale nie można go pobrać. Jeśli hasło zostanie zmienione, należy powiadomić wszystkich użytkowników dostępu zdalnego, aby mogli pomyślnie nawiązać połączenie z urządzeniem StorSimple.

Aby uzyskać więcej informacji na temat protokołu CHAP i sposobu konfigurowania go dla rozwiązania StorSimple, przejdź do pozycji [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Hasło programu StorSimple Snapshot Manager

StorSimple Snapshot Manager jest przystawką programu Microsoft Management Console (MMC), która używa grup woluminów i Usługa kopiowania woluminów w tle Windows do generowania kopii zapasowych spójnych na poziomie aplikacji. Ponadto można użyć Snapshot Manager StorSimple do tworzenia harmonogramów tworzenia kopii zapasowych i klonowania lub przywracania woluminów.

W przypadku skonfigurowania urządzenia do korzystania z Snapshot Manager StorSimple wymagane będzie podanie hasła Snapshot Manager StorSimple. To hasło jest najpierw ustawione w program Windows PowerShell dla usługi StorSimple podczas rejestracji. Hasło można także ustawić i zmienić z usługi StorSimple Device Manager. To hasło służy do uwierzytelniania urządzenia za pomocą StorSimple Snapshot Manager.

![Hasło programu StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Hasło Snapshot Manager StorSimple musi zawierać od 14 do 15 znaków i musi składać się z co najmniej 3 kombinacji małych i wielkich liter, cyfr i znaków specjalnych. Po ustawieniu hasła Snapshot Manager StorSimple można je zmienić, ale nie można go pobrać. Jeśli zmienisz hasło, pamiętaj o poinformowaniu wszystkich użytkowników zdalnych.

Aby uzyskać więcej informacji na temat StorSimple Snapshot Manager, przejdź do [co to jest StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Najlepsze rozwiązania dotyczące haseł

Zalecamy użycie następujących wytycznych, aby zapewnić, że hasła StorSimple są mocne i dobrze chronione:

* Zmień hasła co trzy miesiące. Zmiana hasła jest wymuszana corocznie.
* Używaj silnych haseł. Aby uzyskać więcej informacji, przejdź do obszaru [Tworzenie silniejszych haseł i ich ochrona](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Zawsze używaj różnych haseł dla różnych mechanizmów dostępu; Każde z określonych haseł powinno być unikatowe.
* Nie udostępniaj nikomu hasła osobom, które nie mają uprawnień dostępu do urządzenia StorSimple.
* Nie mów na temat hasła przed innymi lub wskazówki w formacie hasła.
* Jeśli podejrzewasz, że zabezpieczenia konta lub hasła zostały naruszone, zgłoś zdarzenie do działu zabezpieczeń informacji.
* Traktuj wszystkie hasła jako poufne informacje. 

## <a name="storsimple-data-protection"></a>Ochrona danych StorSimple

W tej sekcji opisano funkcje zabezpieczeń StorSimple chroniące dane przesyłane i przechowywane.

Zgodnie z opisem w innych sekcjach hasła są używane do autoryzacji i uwierzytelniania użytkowników, zanim będą mogły uzyskać dostęp do rozwiązania StorSimple. Innym zagadnieniem dotyczącym zabezpieczeń jest ochrona danych przed nieautoryzowanymi użytkownikami, gdy jest ona przesyłana między systemami magazynu i w trakcie ich przechowywania. W poniższych sekcjach opisano funkcje ochrony danych dostępne w programie StorSimple.

> [!NOTE]
> Deduplikacja zapewnia dodatkową ochronę danych przechowywanych na urządzeniu StorSimple i w magazynie Microsoft Azure. Gdy dane są deduplikowane, obiekty danych są przechowywane niezależnie od metadanych używanych do ich mapowania i uzyskiwania do nich dostępu: Brak dostępnego kontekstu na poziomie magazynu do odtworzenia danych w oparciu o strukturę woluminu, system plików lub nazwę pliku.


## <a name="protect-data-flowing-through-the-service"></a>Ochrona danych przepływających przez usługę

Głównym celem usługi StorSimple Device Manager jest zarządzanie i Konfigurowanie urządzenia StorSimple. Usługa StorSimple Device Manager działa w Microsoft Azure. Użyj Azure Portal, aby wprowadzić dane konfiguracji urządzenia, a następnie Microsoft Azure za pomocą usługi StorSimple Device Manager wysłać dane do urządzenia. StorSimple używa systemu par kluczy asymetrycznych, aby pomóc w zapewnieniu, że naruszenie usługi platformy Azure nie spowoduje naruszenia przechowywanych informacji.

![Szyfrowanie danych w locie](./media/storsimple-security/DataEncryption.png)

System klucza asymetrycznego pomaga chronić dane przesyłane za pomocą usługi w następujący sposób:

1. Certyfikat szyfrowania danych, który używa pary kluczy publicznych i prywatnych, jest generowany na urządzeniu i służy do ochrony danych. Klucze są generowane, gdy pierwsze urządzenie jest zarejestrowane.
2. Klucze certyfikatu szyfrowania danych są eksportowane do pliku wymiany informacji osobistych (pfx), który jest chroniony przez klucz szyfrowania danych usługi, który jest silnym kluczem 128-bitowym, który jest generowany losowo przez pierwsze urządzenie podczas rejestracji.
3. Klucz publiczny certyfikatu jest bezpiecznie dostępny dla usługi StorSimple Device Manager, a klucz prywatny pozostaje na urządzeniu.
4. Dane wprowadzane do usługi są szyfrowane przy użyciu klucza publicznego i odszyfrowywane przy użyciu klucza prywatnego przechowywanego na urządzeniu, co zapewnia, że usługa platformy Azure nie może odszyfrować danych przepływających na urządzenie.

Klucz szyfrowania danych usługi jest generowany tylko na pierwszym urządzeniu zarejestrowanym w usłudze. Wszystkie kolejne urządzenia zarejestrowane w usłudze muszą używać tego samego klucza szyfrowania danych usługi.

> [!IMPORTANT]
> Bardzo ważne jest, aby utworzyć kopię klucza szyfrowania danych usługi i zapisać ją w bezpiecznej lokalizacji. Kopia klucza szyfrowania danych usługi powinna być przechowywana w taki sposób, aby można było uzyskać do niego dostęp przez autoryzowaną osobę i można ją łatwo przekazywać do administratora urządzenia.
> 
> Jeśli klucz szyfrowania danych usługi zostanie utracony, osoba odpowiedzialna za pomoc techniczną firmy Microsoft może pomóc Ci w jego pobraniu, pod warunkiem, że masz co najmniej jedno urządzenie w stanie online. Zalecamy zmianę klucza szyfrowania danych usługi po jego pobraniu.

Aby zmienić klucz szyfrowania danych usługi i odpowiedni certyfikat szyfrowania danych, wykonaj kroki opisane w temacie [Zmiana klucza szyfrowania danych usługi dla usługi StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Zmiana kluczy szyfrowania wymaga, aby wszystkie urządzenia były aktualizowane przy użyciu nowego klucza. W związku z tym zalecamy zmianę klucza, gdy wszystkie urządzenia są w trybie online. Jeśli urządzenia są w trybie offline, ich klucze można zmienić w innym czasie. Urządzenia z nieaktualnymi kluczami nadal będą mogły uruchamiać kopie zapasowe, ale nie będą mogły przywracać danych do momentu zaktualizowania klucza.

Klucz szyfrowania danych usługi i certyfikat szyfrowania danych nie wygasną. Zaleca się jednak, aby zmienić klucz szyfrowania danych usługi co rok, aby zapobiec złamania klucza.

## <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

Urządzenie StorSimple zarządza danymi przez przechowywanie ich w warstwach lokalnie i w chmurze, w zależności od częstotliwości użytkowania. Wszystkie maszyny, które są podłączone do urządzenia, wysyłają dane do urządzenia, które następnie przenosi dane do chmury, zgodnie z potrzebami. Dane są przesyłane z urządzenia do chmury bezpiecznie przez Internet. Każde urządzenie ma jeden obiekt docelowy iSCSI, który obejmuje wszystkie woluminy udostępnione na tym urządzeniu. Wszystkie dane są szyfrowane przed wysłaniem ich do magazynu w chmurze. 

![Klucz szyfrowania magazynu w chmurze](./media/storsimple-security/CloudStorageEncryption.png)

Aby zapewnić bezpieczeństwo i integralność danych przenoszonych do chmury, StorSimple umożliwia definiowanie kluczy szyfrowania magazynu w chmurze w następujący sposób:

* Klucz szyfrowania magazynu w chmurze jest określany podczas tworzenia kontenera woluminów. Nie można później zmodyfikować lub dodać klucza.
* Wszystkie woluminy w kontenerze woluminów mają ten sam klucz szyfrowania. Jeśli chcesz użyć innej formy szyfrowania dla określonego woluminu, zalecamy utworzenie nowego kontenera woluminów na potrzeby hostowania tego woluminu.
* Po wprowadzeniu klucza szyfrowania magazynu w chmurze w usłudze StorSimple Device Manager klucz jest szyfrowany przy użyciu publicznej części klucza szyfrowania danych usługi, a następnie wysyłany do urządzenia.
* Klucz szyfrowania magazynu w chmurze nie jest przechowywany w żadnym miejscu usługi i jest znany tylko dla urządzenia.
* Określenie klucza szyfrowania magazynu w chmurze jest opcjonalne. Na urządzeniu można wysyłać dane, które zostały zaszyfrowane na hoście.

### <a name="additional-security-best-practices"></a>Dodatkowe najlepsze rozwiązania w zakresie zabezpieczeń

* Ruch podzielony: izolowanie sieci SAN iSCSI od ruchu użytkowników w firmowej sieci LAN przez wdrożenie całkowicie oddzielonej podsieci i używanie sieci VLAN, w których izolacja fizyczna nie jest opcją. Dedykowana sieć dla magazynu iSCSI gwarantuje bezpieczeństwo i wydajność danych o kluczowym znaczeniu dla firmy. Mieszanie magazynu i ruchu użytkowników za pośrednictwem firmowej sieci LAN nie jest zalecane i może zwiększyć opóźnienia i spowodować awarie sieci.
* W przypadku zabezpieczeń sieci po stronie hosta należy używać interfejsów sieciowych, które obsługują aparat odciążania protokołu TCP/IP (TOE). Funkcja TOE zmniejsza obciążenie procesora CPU przez przetwarzanie protokołu TCP na karcie sieciowej.

## <a name="protect-data-via-storage-accounts"></a>Ochrona danych za pomocą kont magazynu

Każda subskrypcja Microsoft Azure może utworzyć co najmniej jedno konto magazynu. (Konto magazynu zapewnia unikatową przestrzeń nazw do pracy z danymi przechowywanymi w chmurze platformy Azure). Dostęp do konta magazynu jest kontrolowany przez klucze subskrypcji i dostępu skojarzone z tym kontem magazynu.

Podczas tworzenia konta magazynu Microsoft Azure generuje 2 512-bitowe klucze dostępu do magazynu, z których jeden jest używany do uwierzytelniania, gdy urządzenie StorSimple uzyskuje dostęp do konta magazynu. Należy zauważyć, że tylko jeden z tych kluczy jest używany. Drugi klucz jest przechowywany w rezerwie, co pozwala na okresowe obracanie kluczy. Aby obrócić klucze, Ustaw klucz pomocniczy jako aktywny, a następnie Usuń klucz podstawowy. Następnie można utworzyć nowy klucz do użycia podczas kolejnego obrotu. (Ze względów bezpieczeństwa wiele centrów danych wymaga rotacji kluczy).

Zalecamy przestrzeganie następujących najlepszych rozwiązań dotyczących rotacji kluczy:

* Należy regularnie obrócić klucze konta magazynu, aby pomóc w zapewnieniu, że konto magazynu nie jest dostępne dla nieautoryzowanych użytkowników.
* Okresowo administrator platformy Azure powinien zmienić lub ponownie wygenerować klucz podstawowy lub pomocniczy przy użyciu sekcji Magazyn Azure Portal, aby uzyskać bezpośredni dostęp do konta magazynu.

## <a name="protect-data-via-encryption"></a>Ochrona danych za pomocą szyfrowania

StorSimple używa następujących algorytmów szyfrowania do ochrony danych przechowywanych w lub podróżujących między składnikami rozwiązania StorSimple.

| Algorytm | Długość klucza | Protokoły/aplikacje/Komentarze |
| --- | --- | --- |
| RSA |2048 |Algorytm RSA PKCS 1 v 1.5 jest używany przez Azure Portal do szyfrowania danych konfiguracyjnych wysyłanych do urządzenia: na przykład poświadczenia konta magazynu, Konfiguracja urządzenia StorSimple oraz klucze szyfrowania magazynu w chmurze. |
| AES |256 |Algorytm AES with CBC służy do szyfrowania publicznej części klucza szyfrowania danych usługi przed wysłaniem do Azure Portal z urządzenia StorSimple. Jest on także używany przez urządzenie StorSimple do szyfrowania danych przed wysłaniem danych do konta magazynu w chmurze. |

## <a name="storsimple-cloud-appliance-security"></a>Zabezpieczenia urządzenia w chmurze StorSimple

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Zarządzanie informacjami osobistymi

StorSimple Device Manager dla serii fizycznej i wirtualnej zbiera dane osobowe w następujących kluczowych wystąpieniach:

- Alert ustawienia użytkownika, w którym skonfigurowano adresy e-mail użytkowników. Te informacje mogą być wyświetlane i wyczyszczone przez administratora. Dotyczy to zarówno urządzeń z serii StorSimple 8000, jak i macierzy wirtualnych StorSimple.
  * Aby wyświetlić i wyczyścić ustawienia dla serii StorSimple 8000, wykonaj kroki opisane w temacie [Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Aby wyświetlić i wyczyścić ustawienia macierzy wirtualnej StorSimple, wykonaj kroki opisane w temacie [Wyświetlanie alertów StorSimple i zarządzanie nimi](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Użytkownicy, którzy mogą uzyskiwać dostęp do danych znajdujących się w udziałach. Zostanie wyświetlona lista użytkowników, którzy mogą uzyskiwać dostęp do danych udziału. Ta lista jest również usuwana po usunięciu udziałów. Dotyczy to tylko tablic wirtualnych StorSimple.
  * Aby wyświetlić listę użytkowników, którzy mają dostęp do lub usunąć udział, wykonaj kroki opisane w sekcji [Zarządzanie udziałami w macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-shares.md)

Aby uzyskać więcej informacji, zapoznaj się z Zasadami ochrony prywatności firmy Microsoft w [Centrum zaufania](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

Poniżej przedstawiono kilka pytań i odpowiedzi dotyczących zabezpieczeń i Microsoft Azure StorSimple.

**P:** Zabezpieczenia mojej usługi zostały naruszone. Co należy zrobić?

Odp **.:** Należy natychmiast zmienić klucz szyfrowania danych usługi i klucze konta magazynu dla konta magazynu używanego do obsługi danych warstwowych. Aby uzyskać instrukcje, przejdź do:

* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Kluczowe rotacja kont magazynu](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**P:** Mam nowe urządzenie StorSimple z prośbą o klucz rejestracji usługi. Jak mogę ją pobrać?

Odp **.:** Ten klucz został utworzony podczas pierwszego tworzenia usługi StorSimple Device Manager. W przypadku korzystania z usługi StorSimple Device Manager do nawiązywania połączenia z urządzeniem można użyć strony szybki start usługi, aby wyświetlić lub ponownie wygenerować klucz rejestracji usługi. Generowanie nowego klucza rejestracji usługi nie będzie miało wpływu na istniejące zarejestrowane urządzenia. Aby uzyskać instrukcje, przejdź do:

* [Wyświetl lub ponownie Wygeneruj klucz rejestracji usługi](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**P:** Klucz szyfrowania danych usługi został utracony. Co mam zrobić?

Odp **.:** Skontaktuj się pomoc techniczna firmy Microsoft. Mogą oni zalogować się do sesji pomocy technicznej na urządzeniu i pomóc Ci w pobraniu klucza (jeśli co najmniej jedno urządzenie jest w trybie online). Natychmiast po uzyskaniu klucza szyfrowania danych usługi należy go zmienić, aby upewnić się, że nowy klucz jest znany tylko dla użytkownika. Aby uzyskać instrukcje, przejdź do:

* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**P:**  Przyznało urządzenie do zmiany klucza szyfrowania danych usługi, ale nie uruchomiono procesu zmiany klucza. Co mam zrobić?

Odp **.:** Jeśli limit czasu upłynął, konieczne będzie ponowne autoryzowanie urządzenia pod kątem zmiany klucza szyfrowania danych usługi, a następnie ponowne uruchomienie procesu.

**P:**  Klucz szyfrowania danych usługi został zmieniony, ale nie można zaktualizować innych urządzeń w ciągu 4 godzin. Czy muszę zacząć ponownie?

Odp **.:** Okres 4-godzinny jest tylko w przypadku inicjowania zmiany. Po rozpoczęciu procesu aktualizacji na autoryzowanym urządzeniu StorSimple Autoryzacja jest ważna do momentu zaktualizowania wszystkich urządzeń.

**P:** Nasz administrator StorSimple opuścił firmę. Co mam zrobić?

Odp **.:** Zmień i zresetuj hasła zezwalające na dostęp do urządzenia StorSimple, a następnie Zmień klucz szyfrowania danych usługi, aby upewnić się, że nowe informacje nie są znane osobom nieupoważnionym. Aby uzyskać instrukcje, przejdź do:

* [Zmienianie haseł StorSimple przy użyciu usługi StorSimple Device Manager](storsimple-8000-change-passwords.md)
* [Zmień klucz szyfrowania danych usługi](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurowanie protokołu CHAP dla urządzenia StorSimple](storsimple-8000-configure-chap.md)

**P:** Chcę podać StorSimple Snapshot Manager hasło do hosta, który nawiązuje połączenie z urządzeniem StorSimple, ale hasło jest niedostępne. Co mogę zrobić?

Odp **.:** Jeśli hasło zostało zapomniane, należy utworzyć nowe. Następnie należy poinformować wszystkich istniejących użytkowników o tym, że hasło zostało zmienione i że powinni zaktualizować swoich klientów tak, aby korzystały z nowego hasła. Aby uzyskać instrukcje, przejdź do:

* [Zmiana hasła Snapshot Manager StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Uwierzytelnianie urządzenia](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**P:** Certyfikat dostępu zdalnego do program Windows PowerShell dla usługi StorSimple został zmieniony na urządzeniu. Jak mogę zaktualizować moich klientów dostępu zdalnego?

Odp **.:** Nowy certyfikat można pobrać z usługi StorSimple Device Manager, a następnie udostępnić go w magazynie certyfikatów klientów dostępu zdalnego. Aby uzyskać instrukcje, przejdź do:

* [Polecenie cmdlet Import-Certificate](/powershell/module/pkiclient/import-certificate)

**P:** Czy moje dane są chronione w przypadku naruszenia zabezpieczeń usługi StorSimple Device Manager?

Odp **.:** Dane konfiguracji usługi są zawsze szyfrowane przy użyciu klucza publicznego podczas wyświetlania go w przeglądarce sieci Web. Ponieważ usługa nie ma dostępu do klucza prywatnego, usługa nie będzie mogła wyświetlać żadnych danych. W przypadku naruszenia zabezpieczeń usługi StorSimple Device Manager nie ma żadnego wpływu, ponieważ nie ma kluczy przechowywanych w usłudze StorSimple Device Manager.

**P:** Jeśli ktoś uzyska dostęp do certyfikatu szyfrowania danych, czy moje dane zostaną naruszone?

Odp **.:** Microsoft Azure przechowuje klucz szyfrowania danych klienta (plik PFX) w zaszyfrowanym formacie. Ponieważ plik PFX jest szyfrowany i Usługa StorSimple nie ma klucza szyfrowania danych usługi do odszyfrowania pliku PFX, po prostu uzyskanie dostępu do pliku PFX nie spowoduje ujawnienia żadnych wpisów tajnych.

**P:** Co się stanie, jeśli jednostka rządowa prosi firmę Microsoft o moje dane?

Odp **.:** Ze względu na to, że wszystkie dane są szyfrowane w usłudze, a klucz prywatny jest przechowywany na urządzeniu, jednostka rządowa musi podawać klientowi dane.



## <a name="next-steps"></a>Następne kroki

[Wdróż Urządzenie StorSimple](storsimple-8000-deployment-walkthrough-u2.md).