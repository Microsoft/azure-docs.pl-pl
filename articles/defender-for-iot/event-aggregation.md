---
title: Agregacja zdarzeń
description: Dowiedz się więcej o agregacji zdarzeń usługi Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: aec750d246ce99fa65431e23ef68e70418db0017
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940939"
---
# <a name="defender-for-iot-event-aggregation"></a>Agregacja zdarzeń usługi Defender for IoT

Usługa Defender Security Agents zbiera dane i zdarzenia systemowe z urządzenia lokalnego i wysyła je do chmury platformy Azure w celu przetworzenia i analizy. Agent zabezpieczeń zbiera wiele typów zdarzeń urządzeń, w tym nowy proces i nowe zdarzenia połączenia. Zarówno nowy proces, jak i nowe zdarzenia połączeń mogą być często wykonywane na urządzeniu w ciągu sekundy, a chociaż ważne dla niezawodnego i kompleksowego zabezpieczenia, liczba agentów zabezpieczeń komunikatów jest zmuszona do przesłania lub przekroczenia limitu przydziału IoT Hub i kosztów. Zdarzenia te zawierają jednak wysoce cenne informacje zabezpieczające, które są kluczowe dla ochrony urządzenia.

Aby zmniejszyć dodatkowe przydziały i koszty podczas ochrony urządzeń, agenci usługi Defender dla usługi IoT mogą agregować te typy zdarzeń.

Agregacja zdarzeń jest domyślnie **włączona** i chociaż nie jest zalecana, można ją **wyłączyć** ręcznie w dowolnym momencie.

Agregacja jest obecnie dostępna dla następujących typów zdarzeń:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (tylko system Windows)

## <a name="how-does-event-aggregation-work"></a>Jak działa agregacja zdarzeń?

Gdy agregacja zdarzeń jest **pozostawiona**, usługa Defender dla agentów usługi IoT agreguje zdarzenia w przedziale czasowym lub oknie czasu.
Po upływie okresu interwału Agent wysyła zagregowane zdarzenia do chmury platformy Azure w celu przeprowadzenia dalszej analizy.
Zagregowane zdarzenia są przechowywane w pamięci do momentu wysłania ich do chmury platformy Azure.

Aby zmniejszyć wykorzystanie pamięci przez agenta, za każdym razem, gdy Agent zbiera identyczne zdarzenie do takiego, który jest już przechowywany w pamięci, Agent zwiększa liczbę trafień tego zdarzenia. Gdy przejdzie przedział czasu agregacji, Agent wysyła liczbę trafień każdego określonego typu zdarzenia, które wystąpiło. Agregacja zdarzeń jest po prostu agregacją liczby trafień każdego zebranego typu zdarzenia.

Zdarzenia są uważane za identyczne tylko wtedy, gdy są spełnione następujące warunki:

* Zdarzenia ProcessCreate — gdy są identyczne elementy **CommandLine**, **Executable**, **username**i **UserID**
* Zdarzenia ConnectionCreate — gdy **wiersz polecenia**, **userId, nazwa użytkownika**, **kierunek**, **adres lokalny**, **adres zdalny**, * * protokół i **port docelowy** są identyczne
* Zdarzenia ProcessTerminate — gdy **plik wykonywalny** i **stan zakończenia** są identyczne

### <a name="working-with-aggregated-events"></a>Praca z zagregowanymi zdarzeniami

Podczas agregacji właściwości zdarzeń, które nie są agregowane, są odrzucane i pojawiają się w usłudze log Analytics z wartością 0.

* Zdarzenia ProcessCreate- **ProcessId**i **parentProcessId** są ustawione na 0
* Zdarzenia ConnectionCreate — **Identyfikator procesu**i **port źródłowy** są ustawione na 0

## <a name="event-aggregation-based-alerts"></a>Alerty oparte na agregacji zdarzeń

Po analizie usługa Defender for IoT tworzy alerty zabezpieczeń dla podejrzanych zdarzeń agregowanych. Alerty utworzone na podstawie zagregowanych zdarzeń są wyświetlane tylko raz dla każdego zagregowanego zdarzenia.

Czas rozpoczęcia agregacji, czas zakończenia i liczba trafień dla każdego zdarzenia są rejestrowane w polu usługi Event **ExtraDetails** w log Analytics do użycia podczas badania.

Każde zagregowane zdarzenie przedstawia 24-godzinny okres zebranych alertów. Korzystając z menu Opcje zdarzenia w lewym górnym rogu każdego zdarzenia, można **odrzucić** każde pojedyncze zagregowane zdarzenie.

## <a name="event-aggregation-twin-configuration"></a>Konfiguracja dwuosiowa agregacji zdarzeń

Wprowadź zmiany w konfiguracji agregacji zdarzeń usługi Defender for IoT w [obiekcie konfiguracji agenta](how-to-agent-configuration.md) w ramach usługi **azureiotsecurity** w module.

| Nazwa konfiguracji | Możliwe wartości | Szczegóły | Uwagi |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Włącz/Wyłącz agregację zdarzeń dla zdarzeń tworzenia procesu |
| aggregationIntervalProcessCreate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń tworzenia procesu |
| aggregationEnabledConnectionCreate | boolean| Włącz/Wyłącz agregację zdarzeń dla zdarzeń tworzenia połączenia |
| aggregationIntervalConnectionCreate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń tworzenia połączenia |
| aggregationEnabledProcessTerminate | boolean | Włącz/Wyłącz agregację zdarzeń dla zdarzeń zakończenia procesu | Tylko Windows|
| aggregationIntervalProcessTerminate | Ciąg TimeSpan ISO8601 | Interwał agregacji dla zdarzeń zakończenia procesu | Tylko Windows|
|

## <a name="default-configurations-settings"></a>Ustawienia konfiguracji domyślnej

| Nazwa konfiguracji | Wartości domyślne |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o agregacji agenta zabezpieczeń usługi Defender dla usługi IoT oraz o dostępnych opcjach konfiguracji zdarzeń.

Aby kontynuować wprowadzenie do usługi Defender for IoT, użyj następujących artykułów:

- Informacje na temat [metod uwierzytelniania agentów zabezpieczeń](concept-security-agent-authentication-methods.md)
- Wybierz i Wdróż [agenta zabezpieczeń](how-to-deploy-agent.md)
- Przegląd [wymagań wstępnych usługi](service-prerequisites.md) Defender for IoT
- Dowiedz się, jak [włączyć usługę Defender for IoT w IoT Hub](quickstart-onboard-iot-hub.md)
- Dowiedz się więcej o usłudze z usługi [Defender for IoT — często zadawane pytania](resources-frequently-asked-questions.md)
