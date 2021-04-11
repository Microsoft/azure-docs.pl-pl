---
title: Co nowego w usłudze Azure Defender dla IoT
description: W tym artykule opisano nowości w najnowszej wersji usługi Defender for IoT.
ms.topic: overview
ms.date: 03/14/2021
ms.openlocfilehash: 7d8c4ebfc55c80878b780fe9f663aaec62a75f9d
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382916"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Co nowego w usłudze Azure Defender for IoT?

W tym artykule wymieniono nowe funkcje i ulepszenia funkcji usługi Defender for IoT.

Notowane funkcje są w wersji zapoznawczej. [Postanowienia uzupełniające dotyczące platformy Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zawierają dodatkowe postanowienia prawne dotyczące funkcji systemu Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze udostępnione.
## <a name="march-2021"></a>Marzec 2021

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Ulepszone niestandardowe reguły alertów z czujnikiem (publiczna wersja zapoznawcza)

Teraz można tworzyć niestandardowe reguły alertów w oparciu o dzień, grupę dni i aktywność sieciową okresu.  Praca z warunkami reguł daty i godziny jest przydatna, na przykład w przypadkach, w których ważność alertu jest określana przez czas trwania zdarzenia alertu. Na przykład utwórz regułę niestandardową, która wyzwala alert o wysokiej ważności w przypadku wykrycia aktywności sieciowej w weekendie lub wieczorie.

Ta funkcja jest dostępna na czujniku w wersji 10,2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Lokalna Konsola zarządzania — eksportowanie alertów (publiczna wersja zapoznawcza)

Informacje o alertach można teraz wyeksportować do pliku CSV z lokalnej konsoli zarządzania. Możesz wyeksportować informacje o wszystkich alertach wykrytych lub wyeksportować informacje na podstawie filtrowanego widoku.

Ta funkcja jest dostępna w lokalnej konsoli zarządzania w wersji 10,2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Dodaj drugi interfejs sieciowy do lokalnej konsoli zarządzania (publiczna wersja zapoznawcza)

Teraz można zwiększyć bezpieczeństwo wdrożenia przez dodanie drugiego interfejsu sieciowego do lokalnej konsoli zarządzania. Ta funkcja umożliwia zarządzanie lokalne w celu połączenia czujników w jednej zabezpieczonej sieci, dzięki czemu użytkownicy mogą uzyskać dostęp do lokalnej konsoli zarządzania za pomocą drugiego oddzielnego interfejsu sieciowego.

Ta funkcja jest dostępna w lokalnej konsoli zarządzania w wersji 10,2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Konstruktor urządzeń — nowy Micro Agent (publiczna wersja zapoznawcza)

Dostępny jest nowy moduł konstruktora urządzeń. Moduł, zwany jako mikroagentem, umożliwia:

- **Integracja z usługą azure IoT Hub i usługą Azure Defender dla IoT** — tworzenie silniejszych zabezpieczeń punktu końcowego bezpośrednio na urządzeniach IoT poprzez integrację z opcją monitorowania zapewnianą przez usługę Azure IoT Hub i usługę Azure Defender for IoT.
- **Elastyczne opcje wdrażania z obsługą standardowych systemów operacyjnych IoT** — można wdrożyć jako pakiet binarny lub jako modyfikowalny kod źródłowy z obsługą standardowych systemów operacyjnych IoT, takich jak Linux i Azure RTO.
- **Minimalne wymagania dotyczące zasobów, które nie mają zależności jądra systemu operacyjnego** — niewielkie użycie, niskie użycie procesora CPU i Brak zależności jądra systemu operacyjnego.
- **Security stan Management** — proaktywne monitorowanie stan zabezpieczeń urządzeń IoT.
- **Ciągły, wykrycie IoT/spoza w czasie rzeczywistym** — wykrywanie zagrożeń, takich jak botnetami, próby odłożenia, wyszukiwarek używa tego kryptograficzne i podejrzana aktywność sieci

Przestarzała Dokumentacja usługi Defender-IoT-Micro-Agent zostanie przeniesiona do *rozwiązania opartego na agencie dla konstruktorów urządzeń>folderze klasycznym* .

Ten zestaw funkcji jest dostępny w bieżącej publicznej wersji zapoznawczej w chmurze.

## <a name="january-2021"></a>Styczeń 2021 r.

- [Bezpieczeństwo](#security)
- [Dołączanie](#onboarding)
- [Wykorzystania](#usability)
- [Inne aktualizacje](#other-updates)
### <a name="security"></a>Bezpieczeństwo

Ulepszenia odzyskiwania certyfikatu i hasła zostały wprowadzone do tej wersji.

#### <a name="certificates"></a>Certyfikaty
  
Ta wersja pozwala:

- Przekazuj certyfikaty SSL bezpośrednio do czujników i lokalnych konsol zarządzania.
- Wykonaj walidację między lokalną konsolą zarządzania i połączonymi czujnikami oraz między konsolą zarządzania i konsolą zarządzania o wysokiej dostępności. Walidacja jest oparta na datach wygaśnięcia, autentyczności głównego urzędu certyfikacji i listach odwołania certyfikatów.  Jeśli walidacja nie powiedzie się, sesja nie będzie kontynuowana.

W przypadku uaktualnień:

- Podczas uaktualniania nie wprowadzono żadnych zmian w certyfikacie SSL ani w funkcji weryfikacji.
- Po uaktualnieniu Użytkownicy administracyjni usługi czujników i lokalnej konsoli zarządzania mogą zastąpić certyfikaty SSL lub aktywować weryfikację certyfikatu SSL z ustawień systemowych, z okna certyfikatu protokołu SSL.  

W przypadku nowych instalacji:

- Podczas pierwszego logowania użytkownicy muszą użyć certyfikatu SSL (zalecane) lub certyfikatu z podpisem własnym, który został wygenerowany lokalnie (niezalecane)
- Sprawdzanie poprawności certyfikatu jest domyślnie włączone dla nowych instalacji.

#### <a name="password-recovery"></a>Odzyskiwanie hasła
  
Użytkownicy administracyjni z czujnikiem i lokalną konsolą zarządzania mogą teraz odzyskiwać hasła z portalu usługi Azure Defender dla IoT. Wcześniej Odzyskiwanie hasła wymagało interwencji zespołu pomocy technicznej.

### <a name="onboarding"></a>Dołączanie

#### <a name="on-premises-management-console---committed-devices"></a>Lokalna Konsola zarządzania — urządzenia zatwierdzone

Po wstępnym logowaniu do lokalnej konsoli zarządzania użytkownicy są zobowiązani do przekazania pliku aktywacji. Plik zawiera zagregowaną liczbę urządzeń, które mają być monitorowane w sieci organizacji. Ta liczba jest określana jako liczba zatwierdzonych urządzeń.
Zatwierdzone urządzenia są definiowane podczas procesu dołączania w portalu usługi Azure Defender dla IoT, w którym jest generowany plik aktywacji.
W celu przekazania pliku aktywacji wymagane są aktualizacje użytkowników w pierwszym czasie i uaktualnienia.
Po początkowej aktywacji liczba urządzeń wykrytych w sieci może przekroczyć liczbę zatwierdzonych urządzeń. Może się to zdarzyć na przykład wtedy, gdy połączysz więcej czujników z konsolą zarządzania. W przypadku rozbieżności między liczbą wykrytych urządzeń i liczbą zatwierdzonych urządzeń w konsoli zarządzania zostanie wyświetlone ostrzeżenie. Jeśli wystąpi to zdarzenie, należy przekazać nowy plik aktywacji.

#### <a name="pricing-page-options"></a>Opcje strony cennika

Strona cennika umożliwia dołączanie nowych subskrypcji do usługi Azure Defender dla IoT i Definiowanie zatwierdzonych urządzeń w sieci.  
Ponadto na stronie cennika można teraz zarządzać istniejącymi subskrypcjami związanymi z czujnikiem i aktualizowaniem zobowiązania dotyczącego urządzeń.

#### <a name="view-and-manage-onboarded-sensors"></a>Wyświetlanie czujników dołączanych i zarządzanie nimi

Nowa strona witryny i portalu czujników umożliwia:

- Dodaj opisowe informacje o czujniku. Na przykład strefa skojarzona z czujnikiem lub tagami dowolnego tekstu.
- Wyświetl i Filtruj informacje o czujnikach. Na przykład Wyświetl szczegóły dotyczące czujników, które są połączone w chmurze lub lokalnie zarządzane, lub Wyświetl informacje dotyczące czujników w określonej strefie.  

### <a name="usability"></a>Łatwość obsługi

#### <a name="azure-sentinel-new-connector-page"></a>Strona nowego łącznika Azure wskaźnikowego

Przeprojektowano stronę łącznika danych usługi Azure Defender for IoT na platformie Azure. Łącznik danych jest teraz oparty na subskrypcjach, a nie w centrach IoT. umożliwienie klientom lepszego zarządzania połączeniem konfiguracji z platformą Azure.

#### <a name="azure-portal-permission-updates"></a>Azure Portal aktualizacje uprawnień  

Dodano obsługę czytnika zabezpieczeń i administratora zabezpieczeń.

### <a name="other-updates"></a>Inne aktualizacje

#### <a name="access-group---zone-permissions"></a>Dostęp do strefy grupy dostępu
  
Zasady grupy dostępu lokalnej konsoli zarządzania nie będą zawierać opcji udzielenia dostępu do określonej strefy. Nie wprowadzono zmian w definiowaniu reguł, które korzystają z witryn, regionów i jednostek roboczych.   Po uaktualnieniu grupy dostępu zawierające reguły zezwalające na dostęp do określonych stref zostaną zmodyfikowane w taki sposób, aby zezwalały na dostęp do jego lokacji nadrzędnej, w tym wszystkich jej stref.

#### <a name="terminology-changes"></a>Zmiany terminologii

W przypadku danego elementu zawartości zmieniono nazwę urządzenia w czujniku i lokalnej konsoli zarządzania, raportach i innych interfejsach rozwiązań.
W przypadku alertów dotyczących czujników i lokalnych konsoli zarządzania termin zarządzanie tym zdarzeniem nosi nazwę kroki zaradcze.

## <a name="next-steps"></a>Następne kroki

[Wprowadzenie do usługi Defender for IoT](getting-started.md)
