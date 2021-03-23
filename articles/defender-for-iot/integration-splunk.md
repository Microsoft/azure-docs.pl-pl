---
title: Informacje o integracji Splunk
description: Aby sprostać brakom wglądu w zabezpieczenia i odporność niektórych sieci, usługa Defender for IoT opracowała aplikację Defender for IoT, IIoT i monitorowanie zagrożeń usługi ICS dla Splunk, natywnej integracji między usługą Defender dla IoT i Splunk, która umożliwia ujednolicone podejście do niego i zabezpieczenia.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785924"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Aplikacja do monitorowania usługi Defender dla IoT i ICS dla programu Splunk

Usługa Defender for IoT zmniejsza ryzyko IIoT, ICS i SCADA z użyciem opatentowanych, opartych na usłudze ICS aparatów samodzielnych, które dostarczają natychmiastowy wgląd w informacje dotyczące urządzeń i luk w zabezpieczeniach i zagrożeń w krótszym czasie niż godzina obrazu i bez polegania na agentach, regułach lub podpisach, specjalistycznych umiejętnościach lub poprzedniej znajomości środowiska.

Aby sprostać brakom wglądu w zabezpieczenia i odporność niektórych sieci, usługa Defender for IoT opracowała aplikację Defender for IoT, IIoT i monitorowanie zagrożeń usługi ICS dla Splunk, natywnej integracji między usługą Defender dla IoT i Splunk, która umożliwia ujednolicone podejście do niego i zabezpieczenia.

> [!Note]
> Odwołania do CyberX odnoszą się do usługi Azure Defender dla IoT.

## <a name="about-the-splunk-application"></a>Informacje o aplikacji Splunk

Aplikacja udostępnia analityków SOC z wielowymiarową widocznością dla wyspecjalizowanych protokołów i urządzeń IIoT wdrożonych w środowiskach przemysłowych, a także analizy behawioralne obsługujące usługę ICS, aby szybko wykrywać podejrzane lub nietypowe zachowanie. Aplikacja włącza również zarówno odpowiedź na zdarzenia, jak i z jednej firmy SOC. Jest to istotny ewolucja, która zapewnia ciągłą zbieżność IT i z obsługą nowych inicjatyw IIoT, takich jak maszyny inteligentne i analiza w czasie rzeczywistym.

Aplikację Splunk można zainstalować lokalnie lub uruchomić w chmurze. Integracja z usługą Defender for IoT obsługuje oba wdrożenia.

## <a name="about-the-integration"></a>Informacje o integracji

Integracja usługi Defender dla IoT i Splunk za pośrednictwem aplikacji natywnej umożliwia użytkownikom:

- Skrócenie czasu wymaganego przez organizacje przemysłowe i krytyczne do wykrywania i badania zagrożeń cybernetycznymi oraz reagowania na nie.

- Uzyskiwanie analizy w czasie rzeczywistym informacji o zagrożeniach.

- Skorelowanie alertów usługi Defender for IoT z Splunk repozytoriami analizy zagrożeń przedsiębiorstwa.

- Monitoruj pojedyncze okulary i odpowiadaj na nie.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Strona główna narzędzia Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="Strona alerty w Splunk.":::

Aplikacja umożliwia administratorom Splunk analizowanie alertów wysyłanych przez usługę Defender for IoT i monitorowanie całego wdrożenia zabezpieczeń, w tym:

- Który z pięciu aparatów analizy wykrył alert.

- Który protokół wygenerował alert.

- Który usługa Defender dla czujnika IoT wygenerowała alert.

- Poziom ważności alertu.

- Źródło i miejsce docelowe komunikacji.

## <a name="requirements"></a>Wymagania

### <a name="version-requirements"></a>Wymagania dotyczące wersji

Poniżej wymieniono wymagania.

- Usługa Defender dla IoT w wersji 2,4 lub nowszej.

- Splunkbase w wersji 11 lub nowszej.

- Splunk Enterprise w wersji 7,2 lub nowszej.
  
## <a name="download-the-application"></a>Pobieranie aplikacji

Pobierz *CyberX monitorowanie zagrożeń usługi ICS dla aplikacji Splunk* z [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Wymagania dotyczące uprawnień Splunk

Wymagane jest następujące uprawnienie Splunk:

- Każdy użytkownik z uprawnieniami roli użytkownika *administrator* .

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Wyślij alerty usługi Defender for IoT do usługi Splunk

Alerty usługi Defender for IoT zawierają informacje o szerokim zakresie zdarzeń zabezpieczeń, w tym:

- Odchylenia od działania poznanej sieci podstawowej.

- Wykrywanie złośliwego oprogramowania.

- Wykrywanie na podstawie podejrzanych zmian operacyjnych.

- Anomalie sieci.

- Odchylenia protokołu od specyfikacji protokołu.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="Ekran wykrywania.":::

Usługę Defender for IoT można skonfigurować do wysyłania alertów do serwera Splunk, w którym informacje o alercie są wyświetlane na pulpicie nawigacyjnym Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Wyświetl wszystkie alerty i ich szczegóły.":::

Następujące informacje o alercie są wysyłane do serwera Splunk.

- Data i godzina alertu.

- Aparat Defender for IoT wykrył zdarzenie: naruszenie protokołu, naruszenie zasad, złośliwe oprogramowanie, anomalia lub aparat operacyjny.

- Tytuł alertu.

- Komunikat alertu.

- Ważność alertu: ostrzeżenie, pomocniczy, główny lub krytyczny.

- Nazwa urządzenia źródłowego.

- Adres IP urządzenia źródłowego.

- Nazwa urządzenia docelowego.

- Adres IP urządzenia docelowego.

- Adres IP platformy usługi Defender for IoT (Host).

- Nazwa urządzenia usługi Defender for IoT (typ źródła).

Przykładowe dane wyjściowe przedstawiono poniżej:

| Godzina | Zdarzenie |
|--|--|
| 7/23/15<br />9:28:31.000 PM | **Alert dotyczący platformy usługi Defender for IoT**: urządzenie zostało zatrzymane przez PLC polecenie<br /><br />**Typ**: naruszenie operacyjne <br /><br />**Ważność**: główna <br /><br />**Nazwa źródła**: my_device1 <br /><br />**Źródłowy adres IP**: 192.168.110.131 <br /><br />**Nazwa miejsca docelowego**: my_device2<br /><br /> **Docelowy adres IP**: 10.140.33.238 <br /><br />**Komunikat**: urządzenie sieciowe zostało zatrzymane przy użyciu polecenia zatrzymania PLC. To urządzenie nie będzie działać do momentu wysłania polecenia uruchomienia. 192.168.110.131 został zatrzymany przez 10.140.33.238 (urządzenie z systemem Siemens S7) przy użyciu polecenia PLC zatrzymania.<br /><br />**Host**: 192.168.90.43 <br /><br />**SourceType**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definiowanie reguł przekazywania alertów

Użyj *reguł przekazywania* usługi Defender dla usługi IoT, aby wysyłać informacje o alertach do serwerów Splunk.

Dostępne są opcje dostosowywania reguł alertów na podstawie:

- Wykryto określone protokoły.

- Ważność zdarzenia.

- Aparat usługi Defender dla IoT wykrywający zdarzenia.

Aby utworzyć regułę przekazywania:

1. W okienku po lewej stronie czujnika lub lokalnej konsoli zarządzania wybierz pozycję **przekazywanie dalej.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Wybierz niebieski przycisk Utwórz alert przesyłania dalej.":::

1. Wybierz pozycję **Utwórz reguły przekazywania**. W oknie **Tworzenie reguły przekazywania** Określ parametry reguł.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Tworzenie reguł dla reguły przekazywania.":::

    | Parametr | Opis |
    |--|--|
    | **Nazwa** | Nazwa reguły przekazywania. |
    | **Wybierz ważność** | Minimalne zdarzenie poziomu zabezpieczeń do przodu. Na przykład, jeśli wybrano opcję pomocnicze, zostaną przekazane alerty pomocnicze i alert powyżej tego poziomu ważności. |
    | **Protokoły** | Domyślnie są wybierane wszystkie protokoły. Aby wybrać określony protokół, wybierz opcję **określony** i wybierz protokół, dla którego ma zostać zastosowana ta reguła. |
    | **Silnika** | Domyślnie są wykorzystywane wszystkie silniki zabezpieczeń. Aby wybrać konkretny aparat zabezpieczeń, dla którego zostanie zastosowana ta reguła, wybierz pozycję **określone** i wybierz aparat. |
    | **Powiadomienia systemowe** | Stan online/offline czujnika przekazujący. Ta opcja jest dostępna tylko wtedy, gdy użytkownik zalogował się do Menedżera centrali. |

1. Aby wydać polecenie Defender for IoT do wysyłania informacji o zasobach do Splunk, wybierz **akcję**, a następnie wybierz pozycję **Wyślij do serwera Splunk**.

1. Wprowadź następujące parametry Splunk.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Parametry Splunk, które należy wprowadzić na tym ekranie.":::

    | Parametr | Opis |
    |--|--|
    | **Host** | Adres serwera Splunk |
    | **Port** | 8089 |
    | **Nazwa użytkownika** | Nazwa użytkownika serwera Splunk |
    | **Password** (Hasło) | Hasło serwera Splunk |

1. Wybierz pozycję **Prześlij**

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [przekazywać informacje o alertach](how-to-forward-alert-information-to-partners.md).
