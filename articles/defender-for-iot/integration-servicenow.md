---
title: Informacje o integracji usługi ServiceNow
titleSuffix: Azure Defender for IoT
description: Aplikacja do zarządzania usługą ICS dla usługi IoT usługi ServiceNow udostępnia analityków SOC, którzy mają wielowymiarowe wgląd w wyspecjalizowane protokoły i urządzenia IoT wdrożone w środowiskach przemysłowych, a także umożliwia szybkie wykrywanie podejrzanych lub nietypowych zachowań.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/17/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: f2a4c3e79a762de19c6e8c029256cd70dedfe3dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98558729"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>Aplikacja do zarządzania usługą ICS dla usługi IoT usługi ServiceNow

Aplikacja do zarządzania usługą ICS dla usługi IoT usługi ServiceNow udostępnia analityków SOC, którzy mają wielowymiarowe wgląd w wyspecjalizowane protokoły i urządzenia IoT wdrożone w środowiskach przemysłowych, a także umożliwia szybkie wykrywanie podejrzanych lub nietypowych zachowań. Jest to istotny ewolucja, która zapewnia ciągłą zbieżność IT i z obsługą nowych inicjatyw IoT, takich jak maszyny inteligentne i analiza w czasie rzeczywistym.

Aplikacja włącza również zarówno odpowiedź na zdarzenia, jak i z jednej firmy SOC.

## <a name="about-defender-for-iot"></a>Informacje o usłudze Defender for IoT

Usługa Defender for IoT oferuje jedyną platformę internetową i IoT cyberbezpieczeństwa utworzoną przez ekspertów z firmy Blue-Team z rekordem śledzenia mającym na celu podwyższenie poziomu infrastruktury krajowej oraz jedyną platformę z opatentowaną analizą zagrożeń obsługującą usługę ICS i uczenie maszynowe. Usługa Defender for IoT oferuje następujące informacje:

- Natychmiastowy wgląd w szczegółowe informacje o tym, jak usługa ICS, ma szeroki zakres szczegółowych informacji o atrybutach.

- Oparta na usłudze ICS Szczegółowa baza wiedzy o protokołach, urządzeniach, aplikacjach i ich zachowaniach.

- Natychmiastowe uzyskiwanie szczegółowych informacji na temat luk w zabezpieczeniach oraz znane zagrożenia zerowe.

- Automatyczna Technologia modelowania zagrożeń dla usługi ICS do przewidywania najbardziej najprawdopodobniej ścieżek skierowanych do nich ataków usługi ICS za pośrednictwem własnej analizy.

> [!Note]
> Odwołania do CyberX odnoszą się do usługi Azure Defender dla IoT.

## <a name="about-the-integration"></a>Informacje o integracji

Integracja usługi Defender for IoT z usługą usługi ServiceNow zapewnia nowy poziom scentralizowanego wglądu, monitorowania i kontroli dla IoT i niekrajobrazu. Te platformy mostkowe umożliwiają automatyczną widoczność urządzeń i zarządzanie zagrożeniami do wcześniej nieosiągalnej usługi ICS & urządzeń IoT.

### <a name="threat-management"></a>Zarządzanie zagrożeniami

Aplikacja do zarządzania usługą ICS dla usługi IoT

- Skrócenie czasu wymaganego przez organizacje przemysłowe i krytyczne do wykrywania i badania zagrożeń cybernetycznymi oraz reagowania na nie.

- Uzyskiwanie analizy w czasie rzeczywistym informacji o zagrożeniach.

- Skorelowanie alertów usługi Defender for IoT z przepływami pracy monitorowania zagrożeń usługi ServiceNow i zarządzania zdarzeniami.

- Wyzwalaj bilety usługi ServiceNow i przepływy pracy przy użyciu innych usług i aplikacji na platformie usługi ServiceNow.

W przypadku aparatów zabezpieczeń usługi IoT są identyfikowane zagrożenia bezpieczeństwa i SCADA, które zapewniają natychmiastowe reagowanie na ataki złośliwego oprogramowania, sieci i odchyleń zasad zabezpieczeń, a także anomalie operacyjne i protokoły. Aby uzyskać szczegółowe informacje o szczegółach alertów wysyłanych do usługi usługi ServiceNow, zobacz [raportowanie alertów](#alert-reporting).

### <a name="device-visibility-and-management"></a>Widoczność i zarządzanie urządzeniami

Baza danych usługi ServiceNow Configuration Management (CMDB) jest wzbogacana i uzupełniona bogatym zestawem atrybutów urządzeń wypychanych przez usługę Defender for IoT. Zapewnia to kompleksowy i ciągły wgląd w poziomie urządzenia i umożliwia monitorowanie i reagowanie z jednego propanu szkła. Aby uzyskać szczegółowe informacje o atrybutach urządzeń wysyłanych do usługi ServiceNowSee, zobacz [Wyświetlanie usługi Defender for IoT Detection in usługi ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Wymagania systemowe i architektura

W tym artykule opisano:

- **Wymagania dotyczące oprogramowania**  
- **Architektura**

## <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

- Usługi ServiceNow zarządzania usługami w wersji 3.0.2

- Poprawka usługi Defender for IoT 2.8.11.1 lub nowsza

> [!Note]
> Jeśli już pracujesz z usługą Defender dla integracji IoT i usługi ServiceNow, a następnie uaktualniasz przy użyciu lokalnej konsoli zarządzania, dane poprzedniej otrzymane z usługi Defender for IoT powinny zostać wyczyszczone z usługi ServiceNow.

## <a name="architecture"></a>Architektura

### <a name="on-premises-management-console-architecture"></a>Architektura lokalnej konsoli zarządzania

Lokalna Konsola zarządzania zapewnia ujednolicone Źródło dla wszystkich informacji o urządzeniu i alercie wysyłanych do usługi usługi ServiceNow.

Można skonfigurować lokalną konsolę zarządzania, aby komunikować się z jednym wystąpieniem usługi ServiceNow. Lokalna Konsola zarządzania umożliwia wypychanie danych czujników do aplikacji Defender for IoT przy użyciu interfejsu API REST.

Jeśli konfigurujesz system do pracy z lokalną konsolą zarządzania, wyłącz synchronizację usługi ServiceNow, reguły przekazywania i konfiguracje serwera proxy w czujnikach, jeśli zostały skonfigurowane.

Te konfiguracje należy skonfigurować dla lokalnej konsoli zarządzania. Instrukcje dotyczące konfiguracji zostały opisane w tym artykule.

### <a name="sensor-architecture"></a>Architektura czujnika

Jeśli chcesz skonfigurować środowisko w celu uwzględnienia bezpośredniej komunikacji między czujnikami i usługi ServiceNow, dla każdego czujnika Zdefiniuj zasady synchronizacji usługi ServiceNow, reguły przekazywania i konfigurację serwera proxy (Jeśli potrzebny jest serwer proxy).

Zaleca się skonfigurowanie integracji przy użyciu lokalnej konsoli zarządzania w celu komunikowania się z usługą usługi ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Utwórz tokeny dostępu w usługi ServiceNow

W tym artykule opisano sposób tworzenia tokenu dostępu w usługi ServiceNow. Token jest wymagany do komunikowania się z usługą Defender for IoT.

**Identyfikator klienta** i **klucz tajny klienta** będą potrzebne podczas tworzenia programu Defender dla reguł przekazywania IoT, które przesyłają informacje o alertach do usługi ServiceNow oraz podczas konfigurowania usługi Defender for IoT w celu wypychania atrybutów urządzenia do tabel usługi ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Konfigurowanie usługi Defender for IoT w celu komunikowania się z usługą usługi ServiceNow

W tym artykule opisano sposób konfigurowania usługi Defender for IoT w celu komunikowania się z usługą usługi ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Informacje o alertach wysyłania usługi Defender for IoT

W tym artykule opisano sposób konfigurowania usługi Defender for IoT w celu wypychania informacji o alercie do tabel usługi ServiceNow. Aby uzyskać informacje o wysyłanych danych alertów, zobacz [raportowanie alertów](#alert-reporting).

Osoby poddające się do alertów IoT są wyświetlane w usługi ServiceNow jako zdarzenia związane z bezpieczeństwem.

Zdefiniuj regułę *przesyłania dalej* usługi Defender for IoT w celu wysyłania informacji o alercie do usługi usługi ServiceNow.

Aby zdefiniować regułę:

1. W okienku po lewej stronie usługi Defender dla IoT wybierz pozycję **przekazywanie dalej**.  

1. Wybierz :::image type="content" source="media/integration-servicenow/plus.png" alt-text="przycisk ikony znaku plus."::: . Zostanie otwarte okno dialogowe Tworzenie reguły przekazywania.  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Utwórz regułę przekazywania":::

1. Dodaj nazwę reguły.

1. Zdefiniuj kryteria, w ramach których usługa Defender for IoT będzie wyzwalać regułę przekazywania. Używanie kryteriów reguł przekazywania ułatwia lokalizowanie i zarządzanie ilością informacji wysyłanych z usługi Defender dla IoT do usługi ServiceNow. Dostępne są następujące opcje:

    - **Poziomy ważności:** Jest to zdarzenie minimalnego poziomu zabezpieczeń do przesyłania dalej. Na przykład, jeśli wybrano opcję **drobny** , alerty pomocnicze i alert powyżej tego poziomu zostaną przesłane dalej. Poziomy są wstępnie zdefiniowane przez usługę Defender for IoT.

    - **Protokoły:** Wyzwalaj zasadę przekazywania tylko wtedy, gdy wykryty ruch był uruchomiony przez określone protokoły. Wybierz z listy rozwijanej wymagane protokoły lub wybierz je wszystkie.

    - **Aparaty:** Wybierz wymagane aparaty lub wybierz je wszystkie. Alerty z wybranych aparatów będą wysyłane.

1. Sprawdź, czy zaznaczono **raport powiadomienia o alertach** .

1. W sekcji Akcje wybierz pozycję **Dodaj** , a następnie wybierz pozycję **usługi ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Wybierz pozycję usługi ServiceNow z opcji listy rozwijanej.":::

1. Wprowadź parametry akcji usługi ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Wypełnij parametry akcji usługi ServiceNow":::

1. W okienku **Akcje** ustaw następujące parametry:

  | Parametr | Opis |
  |--|--|
  | Domena | Wprowadź adres IP serwera usługi ServiceNow. |
  | Nazwa użytkownika | Wprowadź nazwę użytkownika serwera usługi ServiceNow. |
  | Hasło | Wprowadź hasło serwera usługi ServiceNow. |
  | Identyfikator klienta | Wprowadź identyfikator klienta otrzymany dla usługi Defender for IoT na stronie **rejestrów aplikacji** w usługi ServiceNow. |
  | Klucz tajny klienta | Wprowadź ciąg tajny klienta utworzony dla usługi Defender for IoT na stronie **rejestrów aplikacji** w usługi ServiceNow. |
  | Typ raportu | **Zdarzenia**: Przekaż listę alertów, które są prezentowane w usługi SERVICENOW z identyfikatorem zdarzenia i krótkim opisem każdego alertu.<br /><br />**Aplikacja Defender for IoT**: przekazywanie pełnych informacji o alercie, w tym szczegółów czujnika, aparatu, źródła i adresów docelowych. Informacje są przekazywane do usługi Defender for IoT w aplikacji usługi ServiceNow. |

1. Wybierz pozycję **Zapisz**. Osoby oddające się do alertów IoT są wyświetlane jako zdarzenia w usługi ServiceNow.

### <a name="send-defender-for-iot-device-attributes"></a>Wyślij atrybuty urządzenia usługi Defender for IoT

W tym artykule opisano sposób konfigurowania usługi Defender for IoT w celu wypychania szerokiego zakresu atrybutów urządzeń do tabel usługi ServiceNow. Zobacz ***informacje dotyczące spisu*** , aby uzyskać szczegółowe informacje o rodzaju informacji wypychanych do usługi ServiceNow.

Aby wysłać atrybuty do usługi ServiceNow, należy zmapować lokalną konsolę zarządzania na wystąpienie usługi ServiceNow. Dzięki temu platforma Defender for IoT może komunikować się i uwierzytelniać za pomocą wystąpienia.

Aby dodać wystąpienie usługi ServiceNow:

1. Zaloguj się do usługi Defender for IoT w lokalnej konsoli zarządzania.

1. Wybierz pozycję **Ustawienia systemowe** , a następnie **usługi ServiceNow** w sekcji integracja z lokalną konsolą zarządzania.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Wybierz przycisk usługi ServiceNow.":::

1. W oknie dialogowym synchronizacja usługi ServiceNow wprowadź następujące parametry synchronizacji.

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="Okno dialogowe Synchronizacja usługi ServiceNow.":::

     Parametr | Opis |
    |--|--|
    | Włącz synchronizację | Włącz i wyłącz synchronizację po zdefiniowaniu parametrów. |
    | Częstotliwość synchronizacji (minuty) | Domyślnie informacje są przekazywane do usługi ServiceNow co 60 minut. Wartość minimalna to 5 minut. |
    | Wystąpienie usługi ServiceNow | Wprowadź adres URL wystąpienia usługi ServiceNow. |
    | Identyfikator klienta | Wprowadź identyfikator klienta otrzymany dla usługi Defender for IoT na stronie **rejestrów aplikacji** w usługi ServiceNow. |
    | Klucz tajny klienta | Wprowadź ciąg tajny klienta utworzony dla usługi Defender for IoT na stronie **rejestrów aplikacji** w usługi ServiceNow. |
    | Nazwa użytkownika | Wprowadź nazwę użytkownika dla tego wystąpienia. |
    | Hasło | Wprowadź hasło dla tego wystąpienia. |

1. Wybierz pozycję **Zapisz**.

## <a name="verify-communication"></a>Weryfikowanie komunikacji

Sprawdź, czy lokalna Konsola zarządzania jest połączona z wystąpieniem usługi ServiceNow, przeglądając datę *ostatniej synchronizacji* .

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Sprawdź, czy nastąpiła komunikacja, sprawdzając ostatnią synchronizację.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Konfigurowanie integracji przy użyciu serwera proxy HTTPS

Podczas konfigurowania usługi Defender pod kątem integracji IoT i usługi ServiceNow, lokalna Konsola zarządzania i serwer usługi ServiceNow komunikują się przy użyciu portu 443. Jeśli serwer usługi ServiceNow znajduje się za serwerem proxy, nie można użyć portu domyślnego.

Usługa Defender for IoT obsługuje serwer proxy HTTPS w integracji usługi ServiceNow przez włączenie zmiany domyślnego portu używanego do integracji.

Aby skonfigurować serwer proxy:

1. Edytuj właściwości globalne w lokalnej konsoli zarządzania:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Dodaj następujące parametry:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Zapisz i Zakończ.

4. Uruchom następujące polecenie: `sudo monit restart all`

Po zakończeniu konfiguracji wszystkie dane usługi ServiceNow są przekazywane przy użyciu skonfigurowanego serwera proxy.

## <a name="download-the-defender-for-iot-application"></a>Pobierz aplikację Defender for IoT

W tym artykule opisano sposób pobierania aplikacji.

Aby uzyskać dostęp do aplikacji Defender for IoT:

1. Przejdź do strony <https://store.servicenow.com/>

2. Wyszukaj ciąg `Defender for IoT` lub `CyberX IoT/ICS Management` .

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Wyszukaj CyberX na pasku wyszukiwania.":::

3. Wybierz aplikację.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Wybierz aplikację z listy.":::

4. Wybierz pozycję **Żądaj aplikacji.**

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Zaloguj się do aplikacji przy użyciu swoich poświadczeń.":::

5. Zaloguj się i Pobierz aplikację.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Wyświetlanie usługi Defender na potrzeby wykrywania IoT w programie usługi ServiceNow

W tym artykule opisano atrybuty urządzenia i informacje o alertach przedstawione w usługi ServiceNow.

Aby wyświetlić atrybuty urządzenia:

1. Zaloguj się do usługi ServiceNow.

2. Przejdź do **platformy CyberX**.

3. Przejdź do **spisu** lub **alertu**.

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Spis lub alert":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Informacje o spisie

Baza danych zarządzania konfiguracją (CMDB) została ulepszona i uzupełniona o dane wysyłane przez usługę Defender for IoT do usługi ServiceNow. Przez dodawanie lub aktualizowanie atrybutów urządzeń w tabelach elementów konfiguracji CMDB usługi ServiceNow usługa Defender dla IoT może wyzwalać przepływy pracy usługi ServiceNow i reguły biznesowe.

Dostępne są następujące informacje:

- Atrybuty urządzenia, na przykład wykryto urządzenie MAC, system operacyjny, dostawca lub protokół.

- Informacje o oprogramowaniu układowym, na przykład wersja oprogramowania układowego i numer seryjny.

- Informacje o podłączonym urządzeniu, na przykład kierunek ruchu między źródłem i miejscem docelowym.

### <a name="devices-attributes"></a>Atrybuty urządzeń

W tym artykule opisano atrybuty urządzenia przekazywane do usługi ServiceNow.

| Element | Opis |
|--|--|
| Wprowadzony | Nazwa czujnika, który wykrył ruch. |
| ID (Identyfikator) | Identyfikator urządzenia przypisany przez usługę Defender for IoT. |
| Nazwa | Nazwa urządzenia. |
| Adres IP | Adres lub adresy IP urządzenia. |
| Typ | Typ urządzenia, na przykład przełącznik, PLC, Historian lub kontroler domeny. |
| Adres MAC | Adres lub adresy MAC urządzenia. |
| System operacyjny | System operacyjny urządzenia. |
| Dostawca | Dostawca urządzenia. |
| Protokoły | Protokoły wykryte w ruchu generowanym przez urządzenie. |
| Właściciel | Wprowadź nazwę właściciela urządzenia. |
| Lokalizacja | Wprowadź fizyczną lokalizację urządzenia. |

Wyświetlanie urządzeń podłączonych do urządzenia w tym widoku.

Aby wyświetlić podłączone urządzenia:

1. Wybierz urządzenie, a następnie wybierz urządzenie **wymienione na liście dla** tego urządzenia.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Wybierz odpowiednie urządzenie z listy.":::

1. W oknie dialogowym **szczegóły urządzenia** wybierz pozycję **połączone urządzenia**.

### <a name="firmware-details"></a>Szczegóły oprogramowania układowego

W tym artykule opisano informacje o oprogramowaniu układowym urządzenia wypchnięte do usługi ServiceNow.

| Element | Opis |
|--|--|
| Wprowadzony | Nazwa czujnika, który wykrył ruch. |
| Urządzenie | Nazwa urządzenia. |
| Adres | Adres IP urządzenia. |
| Adres modułu | Model urządzenia i numer gniazda lub identyfikator. |
| Kolejną | Numer seryjny urządzenia. |
| Model | Numer modelu urządzenia. |
| Wersja | Numer wersji oprogramowania układowego. |
| Dodatkowe dane | Więcej danych o oprogramowaniu układowym zdefiniowanym przez dostawcę, na przykład typ urządzenia. |

### <a name="connection-details"></a>Szczegóły połączenia

W tym artykule opisano informacje o połączeniu urządzenia przekazywane do usługi usługi ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Informacje o połączeniu urządzenia":::

| Element | Opis |
|--|--|
| Wprowadzony | Nazwa czujnika, który wykrył ruch. |
| Kierunek | Kierunek ruchu. <br /> <br /> - **Jeden ze sposobów** wskazuje, że miejscem docelowym jest serwer, a źródłem jest klient. <br /> <br /> - **Dwa ze sposobów** wskazuje, że zarówno źródło, jak i miejsce docelowe są serwerami lub nieznanym klientem. |
| Identyfikator urządzenia źródłowego | Adres IP urządzenia, które komunikuje się z podłączonym urządzeniem. |
| Nazwa urządzenia źródłowego | Nazwa urządzenia, które komunikuje się z podłączonym urządzeniem. |
| Identyfikator urządzenia docelowego | Adres IP podłączonego urządzenia. |
| Nazwa urządzenia docelowego | Nazwa podłączonego urządzenia. |

## <a name="alert-reporting"></a>Raportowanie alertów

Alerty są wyzwalane, gdy usługi obrony dla aparatów IoT wykryją zmiany w ruchu sieciowym i zachowaniu, które wymagają Twojej uwagi. Aby uzyskać szczegółowe informacje na temat rodzajów alertów generowanych przez poszczególne aparaty, zobacz [Informacje o aparatach alertów](#about-alert-engines).

W tym artykule opisano informacje o alertach urządzenia przekazywane do usługi ServiceNow.

| Element | Opis |
|--|--|
| Utworzone | Godzina i Data wygenerowania alertu. |
| Aparat | Aparat, który wykrył zdarzenie. |
| Tytuł | Tytuł alertu. |
| Opis | Opis alertu. |
| Protokół | Protokół wykryty w ruchu. |
| Ważność | Ważność alertu zdefiniowana przez usługę Defender for IoT. |
| Wprowadzony | Nazwa czujnika, który wykrył ruch. |
| Nazwa źródła | Nazwa źródła. |
| Źródłowy adres IP| Źródłowy adres IP. |
| Nazwa miejsca docelowego | Nazwa miejsca docelowego. |
| Docelowy adres IP | Docelowy adres IP. |
| Osoby przydzielonej | Wprowadź nazwę osoby przypisanej do biletu. |

### <a name="updating-alert-information"></a>Aktualizowanie informacji o alercie

Wybierz wpis w kolumnie utworzono, aby wyświetlić informacje o alercie w formularzu. Możesz zaktualizować szczegóły alertu i przypisać alert do osoby do przeglądu i obsługi.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Wyświetl informacje o alercie.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Informacje o aparatach alertów

W tym artykule opisano rodzaj alertów każdego wyzwalacza silnika.

| Typ alertu | Opis |
|--|--|
| Alerty naruszenia zasad | Wyzwalane, gdy aparat naruszenia zasad wykrywa odchylenia od wcześniej podanego ruchu. Na przykład: <br /><br />-Zostanie wykryte nowe urządzenie. <br /><br />-Na urządzeniu Wykryto nową konfigurację. <br /><br />-Urządzenie nie zostało zdefiniowane jako urządzenie programistyczne przeprowadzi zmianę programistyczną. <br /><br />-Zmieniono wersję oprogramowania układowego. |
| Alerty naruszenia protokołu | Wyzwalane, gdy aparat naruszenia protokołu wykryje struktury pakietów lub wartości pól, które nie są zgodne ze specyfikacją protokołu. |
| Alerty operacyjne | Wyzwalane, gdy aparat operacyjny wykrywa zdarzenia operacyjne lub nieprawidłowe działanie urządzenia. Na przykład urządzenie sieciowe zostało zatrzymane przy użyciu polecenia zatrzymania PLC lub interfejs na czujniku przestał ruch monitorowania. |
| Alerty złośliwego oprogramowania | Wyzwalane, gdy aparat złośliwego oprogramowania wykryje złośliwe działanie sieci, na przykład znane ataki, takie jak robak. |
| Alerty anomalii | Wyzwalane, gdy aparat anomalii wykryje odchylenia. Na przykład urządzenie wykonuje skanowanie sieci, ale nie jest zdefiniowane jako urządzenie skanujące. |

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
