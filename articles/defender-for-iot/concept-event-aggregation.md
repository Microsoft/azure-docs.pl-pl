---
title: Agregacja zdarzeń (wersja zapoznawcza)
description: Usługa Defender Security Agents zbiera dane i zdarzenia systemowe z urządzenia lokalnego oraz wysyła dane do chmury platformy Azure na potrzeby przetwarzania i analizy.
ms.date: 1/20/2021
ms.topic: conceptual
ms.openlocfilehash: c0280e97549009a1e4911c072a7a8ec052684b4e
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779328"
---
# <a name="event-aggregation-preview"></a>Agregacja zdarzeń (wersja zapoznawcza)

Usługa Defender Security Agents zbiera dane i zdarzenia systemowe z urządzenia lokalnego oraz wysyła dane do chmury platformy Azure na potrzeby przetwarzania i analizy. Usługa Defender for IoT Micro umożliwia zbieranie wielu typów zdarzeń urządzeń, w tym nowych procesów, oraz wszystkich nowych zdarzeń połączeń. Nowy proces i nowe zdarzenia połączeń mogą być często wykonywane na urządzeniu w ciągu sekundy. Ta możliwość jest ważna w przypadku kompleksowych zabezpieczeń, jednak liczba komunikatów wysyłanych przez agentów zabezpieczeń może szybko spełnić wymagania lub przekroczyć limity przydziału IoT Hub i kosztów. Jednak te zdarzenia zawierają wysoce cenne informacje zabezpieczające, które są kluczowe dla ochrony urządzenia. 

Aby zmniejszyć dodatkowy limit przydziału i koszty związane z ochroną urządzeń, agenci usługi Defender for IoT agregują następujące typy zdarzeń: 

- ProcessCreate (tylko system Linux) 

- ConnectionCreate (tylko usługa Azure RTO) 

## <a name="how-does-event-aggregation-work"></a>Jak działa agregacja zdarzeń? 

Usługa Defender dla agentów IoT agreguje zdarzenia dla okresu interwału lub przedziału czasu. Po upływie okresu interwału Agent wysyła zagregowane zdarzenia do chmury platformy Azure w celu przeprowadzenia dalszej analizy. Zagregowane zdarzenia są przechowywane w pamięci do momentu wysłania ich do chmury platformy Azure. 

Gdy Agent zbiera takie samo zdarzenie, które jest już przechowywane w pamięci, Agent zwiększa liczbę trafień tego zdarzenia, aby zmniejszyć zużycie pamięci przez agenta. Gdy przejdzie przedział czasu agregacji, Agent wysyła liczbę trafień dla każdego typu zdarzenia, które wystąpiło. Agregacja zdarzeń jest po prostu agregacją liczby trafień każdego zebranego typu zdarzenia. 

## <a name="process-events"></a>Przetwarzanie zdarzeń 

Zdarzenia procesu są obecnie obsługiwane tylko w systemach operacyjnych Linux. 

Zdarzenia procesu są uważane za identyczne, gdy *wiersz polecenia* i  *UserID*   są identyczne. 

Domyślny bufor dla zdarzeń procesu to 32 procesów, po których bufor zostanie przetworzony, a najstarsze zdarzenia procesu są odrzucane w celu zapewnienia pokoju dla nowych zdarzeń procesu.  

## <a name="network-connection-events"></a>Zdarzenia połączenia sieciowego 

Zdarzenia połączenia sieciowego są obecnie obsługiwane tylko w usłudze Azure RTO. 

Zdarzenia połączenia sieciowego są uważane za identyczne, gdy *port lokalny*, *Port zdalny*, *protokół transportu*, *adres lokalny* i  *adres zdalny* są identyczne. 

Domyślny bufor dla zdarzeń połączenia sieciowego to 64. Żadne nowe zdarzenia sieciowe nie będą przechowywane w pamięci podręcznej do następnego cyklu zbierania danych. Zostanie zarejestrowane ostrzeżenie o zwiększeniu rozmiaru pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Sprawdź [alerty zabezpieczeń usługi Defender dla IoT](concept-security-alerts.md).
