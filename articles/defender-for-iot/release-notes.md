---
title: Co nowego w programie Azure Defender dla IoT
description: Ten artykuł zawiera informacje o nowościach w najnowszej wersji usługi Defender dla IoT.
ms.topic: overview
ms.date: 4/6/2021
ms.openlocfilehash: df6a43dc68acd025b1c65877c65d1b7e947f210b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739390"
---
# <a name="whats-new-in-azure-defender-for-iot"></a>Co nowego w programie Azure Defender dla IoT?

W tym artykule wymieniono nowe funkcje i ulepszenia funkcji usługi Defender dla IoT.

Zauważyć funkcje są w wersji zapoznawczej. Postanowienia [uzupełniające dotyczące platformy Azure w](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczej obejmują dodatkowe postanowienia prawne dotyczące funkcji platformy Azure, które są w wersji beta, wersji zapoznawczej lub w inny sposób nie zostały jeszcze wydane jako ogólnie dostępne.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Obsługa wersji i Azure Defender dla IoT 

Poniżej wymieniono obsługę, zasady zmiany istotnej dla usługi Defender dla IoT oraz wersje Azure Defender dla IoT, które są obecnie dostępne. 

### <a name="servicing-information-and-timelines"></a>Informacje o obsłudze i osie czasu 

Firma Microsoft planuje wydania aktualizacji dla Azure Defender dla IoT nie mniej niż raz na kwartał. Każda ogólno dostępna wersja czujnika Azure Defender dla IoT i lokalna konsola zarządzania jest obsługiwana przez maksymalnie dziewięć miesięcy od jej wydania. Poprawki i nowe funkcje zostaną zastosowane do bieżącej wersji ga-ga, które są obecnie w pomocy technicznej i nie będą stosowane do starszych wersji gałę.

### <a name="versions-and-support-dates"></a>Wersje i daty pomocy technicznej

| Wersja | Data wydania | Data zakończenia pomocy technicznej |
|--|--|--|
| 10,0 | 01/2021 | 10/2021 |
## <a name="march-2021"></a>Marzec 2021 r.

### <a name="sensor---enhanced-custom-alert-rules-public-preview"></a>Czujnik — rozszerzone niestandardowe reguły alertów (publiczna wersja zapoznawcza)

Teraz można tworzyć niestandardowe reguły alertów na podstawie dnia, grupy dni i czasu wykrycia aktywności sieci.  Praca z warunkami reguły dnia i godziny jest przydatna, na przykład w przypadkach, gdy ważność alertu jest uzyskiwana przez czas wystąpienia zdarzenia alertu. Na przykład utwórz regułę niestandardową, która wyzwala alert o wysokiej ważności w przypadku wykrycia aktywności sieci w weekend lub wieczorami.

Ta funkcja jest dostępna na czujniku w wersji 10.2.

### <a name="on-premises-management-console---export-alerts-public-preview"></a>Lokalna konsola zarządzania — eksportowanie alertów (publiczna wersja zapoznawcza)

Informacje o alertach można teraz wyeksportować do pliku CSV z lokalnej konsoli zarządzania. Można eksportować informacje o wszystkich wykrytych alertach lub eksportować informacje na podstawie filtrowanych widoków.

Ta funkcja jest dostępna w lokalnej konsoli zarządzania w wersji 10.2.

### <a name="add-second-network-interface-to-on-premises-management-console-public-preview"></a>Dodawanie drugiego interfejsu sieciowego do lokalnej konsoli zarządzania (publiczna wersja zapoznawcza)

Teraz możesz zwiększyć bezpieczeństwo wdrożenia, dodając drugi interfejs sieciowy do lokalnej konsoli zarządzania. Ta funkcja umożliwia lokalnemu zarządzaniu korzystanie z podłączonych czujników w jednej bezpiecznej sieci, a jednocześnie umożliwia użytkownikom dostęp do lokalnej konsoli zarządzania za pośrednictwem drugiego oddzielnego interfejsu sieciowego.

Ta funkcja jest dostępna w lokalnej konsoli zarządzania w wersji 10.2.
### <a name="device-builder---new-micro-agent-public-preview"></a>Konstruktor urządzenia — nowy mikro agent (publiczna wersja zapoznawcza)

Dostępny jest nowy moduł konstruktora urządzeń. Moduł, nazywany mikro agentem, umożliwia:

- **Integracja z Azure IoT Hub** i Azure Defender dla IoT — wbuduj silniejsze zabezpieczenia punktu końcowego bezpośrednio do urządzeń IoT, integrując je z opcją monitorowania zapewnianą przez Azure IoT Hub i Azure Defender dla IoT.
- Elastyczne opcje wdrażania z obsługą standardowych systemów operacyjnych IoT — mogą być wdrażane jako pakiet binarny lub jako modyfikowalny kod źródłowy z obsługą standardowych systemów operacyjnych **IoT,** takich jak Linux i Azure RTOS.
- **Minimalne wymagania dotyczące zasobów bez zależności jądra** systemu operacyjnego — niewielkie zużycie procesora CPU i brak zależności jądra systemu operacyjnego.
- **Zarządzanie stanem zabezpieczeń** — proaktywne monitorowanie stanu zabezpieczeń urządzeń IoT.
- **Ciągłe wykrywanie zagrożeń IoT/OT** w czasie rzeczywistym — wykrywanie zagrożeń, takich jak botnety, próby ataków siłowych, kryptograficzne minery i podejrzane działania sieciowe

Przestarzała dokumentacja usługi Defender-IoT-micro-agent zostanie przeniesiona do rozwiązania opartego na agentach dla konstruktorów urządzeń>*folderu klasycznego.*

Ten zestaw funkcji jest dostępny w bieżącej publicznej wersji zapoznawczej chmury.

## <a name="january-2021"></a>Styczeń 2021 r.

- [Bezpieczeństwo](#security)
- [Dołączanie](#onboarding)
- [Użyteczność](#usability)
- [Inne aktualizacje](#other-updates)
### <a name="security"></a>Zabezpieczenia

W tej wersji wprowadzono ulepszenia dotyczące odzyskiwania certyfikatów i haseł.

#### <a name="certificates"></a>Certyfikaty
  
Ta wersja umożliwia:

- Przekazywanie certyfikatów SSL bezpośrednio do czujników i lokalnych konsol zarządzania.
- Wykonaj walidację między lokalną konsolą zarządzania a podłączonymi czujnikami oraz między konsolą zarządzania a konsolą zarządzania o wysokiej dostępności. Weryfikacja jest oparta na datach wygaśnięcia, autentyczności głównego urzędu certyfikacji i list odwołania certyfikatów.  Jeśli walidacja nie powiedzie się, sesja nie będzie kontynuowana.

W przypadku uaktualnień:

- Podczas uaktualniania nie ma żadnych zmian w certyfikatach SSL ani funkcjach walidacji.
- Po uaktualnieniu użytkownicy administracyjni czujników i lokalnej konsoli zarządzania mogą zastąpić certyfikaty SSL lub aktywować weryfikację certyfikatu SSL w oknie Ustawienia systemu, Certyfikat SSL.  

W przypadku nowych instalacji:

- Podczas pierwszego logowania użytkownicy muszą użyć certyfikatu SSL (zalecane) lub lokalnie wygenerowanego certyfikatu z podpisem własnym (nie jest to zalecane)
- Weryfikacja certyfikatu jest domyślnie włączona dla nowych instalacji.

#### <a name="password-recovery"></a>Odzyskiwanie hasła
  
Czujnik i lokalna konsola zarządzania Użytkownicy administracyjni mogą teraz odzyskiwać hasła z poziomu Azure Defender dla IoT portal. Wcześniej odzyskiwanie hasła wymagało interwencji zespołu pomocy technicznej.

### <a name="onboarding"></a>Dołączanie

#### <a name="on-premises-management-console---committed-devices"></a>Lokalna konsola zarządzania — zatwierdzone urządzenia

Po początkowym zalogowaniu się do lokalnej konsoli zarządzania użytkownicy muszą przekazać plik aktywacji. Plik zawiera zagregowaną liczbę urządzeń, które mają być monitorowane w sieci organizacyjnej. Ta liczba jest określana jako liczba zatwierdzonego urządzenia.
Zatwierdzone urządzenia są definiowane podczas procesu dołączania w portalu Azure Defender dla IoT, w którym jest generowany plik aktywacji.
W celu przekazania pliku aktywacji wymagane jest przekazanie pierwszego uaktualnienia przez użytkowników i użytkowników.
Po początkowej aktywacji liczba urządzeń wykrytych w sieci może przekroczyć liczbę urządzeń, które zostały zatwierdzone. Może się to zdarzyć na przykład w przypadku połączenia większej liczby czujników z konsolą zarządzania. Jeśli występuje rozbieżność między liczbą wykrytych urządzeń i liczbą urządzeń, w konsoli zarządzania zostanie wyświetlone ostrzeżenie. W przypadku wystąpienia tego zdarzenia należy przekazać nowy plik aktywacji.

#### <a name="pricing-page-options"></a>Opcje strony cennika

Strona cennika umożliwia dołączanie nowych subskrypcji w celu Azure Defender dla IoT definiowania zatwierdzonego urządzenia w sieci.  
Ponadto strona Cennik umożliwia teraz zarządzanie istniejącymi subskrypcjami skojarzonymi z zobowiązaniem czujnika i aktualizowanie urządzenia.

#### <a name="view-and-manage-onboarded-sensors"></a>Wyświetlanie czujników dołączanych i zarządzanie nimi

Nowa strona portalu Witryny i czujników umożliwia:

- Dodaj opisowe informacje o czujniku. Na przykład strefa skojarzona z czujnikiem lub tagi tekstu swobodnego.
- Wyświetlanie i filtrowanie informacji z czujników. Możesz na przykład wyświetlić szczegółowe informacje o czujnikach połączonych z chmurą lub zarządzanych lokalnie albo wyświetlić informacje o czujnikach w określonej strefie.  

### <a name="usability"></a>Łatwość obsługi

#### <a name="azure-sentinel-new-connector-page"></a>Azure Sentinel strony nowego łącznika

Strona Azure Defender dla IoT łącznika danych w Azure Sentinel została przeprojektowana. Łącznik danych jest teraz oparty na subskrypcjach, a nie na centrach IoT Hub. umożliwia klientom lepsze zarządzanie połączeniem konfiguracyjnym z Azure Sentinel.

#### <a name="azure-portal-permission-updates"></a>Azure Portal aktualizacji uprawnień  

Dodano obsługę czytelnika zabezpieczeń i administratora zabezpieczeń.

### <a name="other-updates"></a>Inne aktualizacje

#### <a name="access-group---zone-permissions"></a>Grupa dostępu — uprawnienia strefy
  
Reguły grupy dostępu konsoli zarządzania lokalnego nie będą zawierać opcji udzielania dostępu do określonej strefy. Nie ma żadnych zmian w definiowaniu reguł, które korzystają z lokacji, regionów i jednostek biznesowych.   Po uaktualnieniu grupy dostępu zawierające reguły zezwalania na dostęp do określonych stref zostaną zmodyfikowane w celu umożliwienia dostępu do jej lokacji nadrzędnej, w tym do wszystkich stref.

#### <a name="terminology-changes"></a>Zmiany terminologii

Nazwa urządzenia dla zasobu została zmieniona w czujniku i lokalnej konsoli zarządzania, raportach i innych interfejsach rozwiązań.
W czujnikach i lokalnej konsoli zarządzania Alerty termin Zarządzaj tym zdarzeniem nosi nazwę Kroki korygowania.

## <a name="next-steps"></a>Następne kroki

[Wprowadzenie do usługi Defender dla IoT](getting-started.md)
