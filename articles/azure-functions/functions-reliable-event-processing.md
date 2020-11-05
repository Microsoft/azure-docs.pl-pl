---
title: Azure Functions przetwarzanie niezawodnego zdarzenia
description: Unikaj braku komunikatów centrum zdarzeń w Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379591"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions przetwarzanie niezawodnego zdarzenia

Przetwarzanie zdarzeń jest jednym z najpopularniejszych scenariuszy związanych z architekturą bezserwerową. W tym artykule opisano sposób tworzenia niezawodnego procesora komunikatów przy użyciu Azure Functions, aby uniknąć utraty komunikatów.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Wyzwania dotyczące strumieni zdarzeń w systemach rozproszonych

Rozważmy system, który wysyła zdarzenia ze stałą szybkością 100 zdarzeń na sekundę. Dzięki temu w ciągu kilku minut wiele wystąpień funkcji równoległych może zużywać przychodzące zdarzenia 100 co sekundę.

Jednak możliwe są następujące mniej optymalne warunki:

- Co w przypadku, gdy Wydawca zdarzeń wysyła uszkodzone zdarzenie?
- Co zrobić, jeśli wystąpienie funkcji napotyka Nieobsłużone wyjątki?
- Co zrobić, jeśli system podrzędny przejdzie w tryb offline?

Jak obsłużyć te sytuacje podczas zachowywania przepływności aplikacji?

W przypadku kolejek niezawodna obsługa komunikatów jest w naturalny sposób. W przypadku sparowania z wyzwalaczem funkcji funkcja tworzy blokadę komunikatu w kolejce. Jeśli przetwarzanie nie powiedzie się, blokada zostanie wydana, aby umożliwić innemu wystąpieniu ponowne przetwarzanie. Przetwarzanie będzie kontynuowane, dopóki komunikat nie zostanie oceniony pomyślnie lub zostanie dodany do kolejki trującej.

Nawet w przypadku, gdy pojedynczy komunikat kolejki może pozostawać w trakcie ponawiania prób, inne równoległe wykonania nadal zachowują się, aby usunąć pozostałe komunikaty z kolejki. Wynika to z tego, że ogólna przepływność pozostaje niezależna od jednej nieprawidłowej wiadomości. Jednak kolejki magazynu nie gwarantują uporządkowania i nie są zoptymalizowane pod kątem wymagań o wysokiej przepływności wymaganych przez Event Hubs.

Z kolei usługa Azure Event Hubs nie obejmuje koncepcji blokowania. Aby umożliwić korzystanie z funkcji, takich jak Wysoka przepływność, wiele grup odbiorców i odtwarzanie, Event Hubs zdarzenia zachowują się podobnie do odtwarzacza wideo. Zdarzenia są odczytywane z pojedynczego punktu w strumieniu na partycję. Od wskaźnika można odczytywać do przodu lub do tyłu z tej lokalizacji, ale trzeba wybrać opcję przeniesienia wskaźnika dla zdarzeń do przetworzenia.

W przypadku wystąpienia błędów w strumieniu, jeśli zdecydujesz się zachować wskaźnik w tym samym miejscu, przetwarzanie zdarzeń jest blokowane, dopóki wskaźnik nie zostanie zaawansowana. Innymi słowy, jeśli wskaźnik zostanie zatrzymany, aby rozwiązać problemy związane z przetwarzaniem pojedynczego zdarzenia, zdarzenia nieprzetworzone piling się w górę.

Azure Functions unika zakleszczenii przez przesuwanie wskaźnika strumienia bez względu na powodzenie lub niepowodzenie. Ze względu na to, że wskaźnik kontynuuje proces przesuwania, funkcje muszą odpowiednio rozwiązywać błędy.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Jak Azure Functions zużywa zdarzenia Event Hubs

Azure Functions zużywa zdarzenia centrum zdarzeń podczas cyklicznego wykonywania następujących czynności:

1. Wskaźnik jest tworzony i utrwalany w usłudze Azure Storage dla każdej partycji centrum zdarzeń.
2. Gdy odbierane są nowe komunikaty (domyślnie w partii), host próbuje wyzwolić funkcję z partii komunikatów.
3. Jeśli funkcja kończy wykonywanie (z wyjątkiem lub bez wyjątku), wskaźnik zostanie zaawansowany, a punkt kontrolny jest zapisywany na koncie magazynu.
4. Jeśli warunki uniemożliwiają ukończenie wykonywania funkcji, host nie będzie mógł postępować. Jeśli wskaźnik nie jest zaawansowana, program sprawdza zakończenie przetwarzania tych samych komunikatów.
5. Powtórz kroki 2 – 4

To zachowanie ujawnia kilka ważnych punktów:

- *Nieobsłużone wyjątki mogą spowodować utratę komunikatów.* Wykonanie, które spowoduje wyjątek, będzie nadal postępować nad wskaźnikiem.  Ustawienie [zasad ponawiania](./functions-bindings-error-pages.md#retry-policies-preview) spowoduje opóźnienie postępu, dopóki nie zostaną ocenione wszystkie zasady ponawiania.
- *Funkcje gwarantują dostarczenie co najmniej jednokrotne.* Kod i systemy zależne mogą być konieczne do [uwzględnienia faktu, że ten sam komunikat może zostać odebrany dwa razy](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Obsługa wyjątków

Zgodnie z ogólną regułą każda funkcja powinna zawierać [blok try/catch](./functions-bindings-error-pages.md) na najwyższym poziomie kodu. Wszystkie funkcje, które zużywają zdarzenia Event Hubs, powinny mieć `catch` blok. W ten sposób, gdy zostanie zgłoszony wyjątek, blok catch obsługuje błąd przed postępem wskaźnika.

### <a name="retry-mechanisms-and-policies"></a>Zasady i mechanizmy ponawiania

Niektóre wyjątki są przejściowe i nie pojawiają się ponownie po ponownym próbie wykonania operacji później. To dlatego, że pierwszym krokiem jest zawsze ponowienie próby wykonania operacji.  W ramach wykonywania funkcji można wykorzystać [zasady ponawiania prób](./functions-bindings-error-pages.md#retry-policies-preview) aplikacji lub utworzyć logikę ponowień.

Wprowadzenie zachowań obsługi błędów do funkcji pozwala definiować podstawowe i zaawansowane zasady ponawiania. Na przykład można zaimplementować zasady, które po przepływie pracy zilustrowane przez następujące reguły:

- Spróbuj ponownie wstawić komunikat trzy razy (prawdopodobnie z opóźnieniem między ponownymi próbami).
- Jeśli wynikiem wszystkich ponownych prób jest błąd, Dodaj komunikat do kolejki, aby przetwarzanie było kontynuowane w strumieniu.
- Uszkodzone lub nieprzetworzone komunikaty są następnie obsługiwane później.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) to przykład biblioteki odporności i obsługi błędów przejściowych dla aplikacji w języku C#.

## <a name="non-exception-errors"></a>Błędy niepowodujące wyjątku

Niektóre problemy występują nawet wtedy, gdy błąd nie jest obecny. Rozważmy na przykład błąd występujący w trakcie wykonywania. W takim przypadku, jeśli funkcja nie zakończy wykonywania, wskaźnik przesunięcia nigdy nie będzie postępować. Jeśli wskaźnik nie zostanie zaawansowany, każde wystąpienie uruchomione po nieudanym wykonaniu nadal odczytuje te same komunikaty. Ta sytuacja zapewnia gwarancję "co najmniej raz".

Gwarancja, że każdy komunikat jest przetwarzany co najmniej jeden raz, oznacza, że niektóre komunikaty mogą być przetwarzane więcej niż jeden raz. Aplikacje funkcji muszą mieć świadomość tej możliwości i muszą zostać skompilowane w oparciu o [zasady idempotentności](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Zatrzymaj i uruchom ponownie wykonywanie

Co najmniej kilka błędów może być akceptowalnych, co w przypadku, gdy w aplikacji występują znaczne błędy? Możesz chcieć przerwać Wyzwalanie zdarzeń do momentu, aż system osiągnie stan prawidłowy. Możliwość wstrzymania przetwarzania jest często osiągana z wzorcem wyłącznika. Wzorzec wyłącznika pozwala aplikacji na "przerywanie obwodu" procesu zdarzeń i wznawianie w późniejszym czasie.

Istnieją dwa sztuki wymagane do zaimplementowania wyłącznika w procesie zdarzeń:

- Współużytkowany stan dla wszystkich wystąpień do śledzenia i monitorowania kondycji obwodu
- Proces główny, który może zarządzać stanem obwodu (otwartym lub zamkniętym)

Szczegóły implementacji mogą się różnić, ale w celu udostępnienia stanu między wystąpieniami potrzebujesz mechanizmu magazynu. Możesz wybrać opcję przechowywania stanu w usłudze Azure Storage, pamięci podręcznej Redis lub dowolnego innego konta, które jest dostępne dla kolekcji funkcji.

Funkcje [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lub [trwałe](./durable/durable-functions-overview.md) są naturalnym dopasowaniem do zarządzania przepływem pracy i stanem obwodu. Inne usługi mogą działać równie dobrze, ale w tym przykładzie są używane aplikacje logiki. Za pomocą usługi Logic Apps można wstrzymywać i ponownie uruchamiać wykonywanie funkcji, dając kontrolkę wymaganą do zaimplementowania wzorca wyłącznika.

### <a name="define-a-failure-threshold-across-instances"></a>Zdefiniuj próg błędu w różnych wystąpieniach

Aby można było jednocześnie przetwarzać zdarzenia przetwarzania zdarzeń w wielu wystąpieniach, do monitorowania kondycji obwodu jest wymagany trwały współużytkowany stan zewnętrzny.

Reguła, którą można zaimplementować, może wymusić, że:

- Jeśli w ciągu 30 sekund między wszystkimi wystąpieniami występuje więcej niż 100 błędów, wówczas Przerwij obwód i Zatrzymaj wyzwalanie dla nowych komunikatów.

Szczegóły implementacji będą się różnić w zależności od potrzeb, ale ogólnie rzecz biorąc, można utworzyć system, który:

1. Rejestruj błędy na koncie magazynu (Azure Storage, Redis itp.)
1. Po zarejestrowaniu nowego błędu Zbadaj licznik kroczący, aby sprawdzić, czy próg został spełniony (na przykład więcej niż 100 w ciągu ostatnich 30 sekund).
1. W przypadku spełnienia progu Emituj zdarzenie, aby Azure Event Grid poinformowanie systemu o przerwaniu obwodu.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Zarządzanie stanem obwodu za pomocą Azure Logic Apps

Poniższy opis przedstawia jeden ze sposobów tworzenia aplikacji logiki platformy Azure w celu wstrzymania przetwarzania aplikacji funkcji.

Azure Logic Apps obejmuje wbudowane łączniki z różnymi usługami, funkcje aranżacji stanowe i jest naturalnym wyborem do zarządzania stanem obwodu. Po wykryciu konieczności przerwania obwodu można utworzyć aplikację logiki, aby zaimplementować następujący przepływ pracy:

1. Wyzwól przepływ pracy Event Grid i Zatrzymaj funkcję platformy Azure (za pomocą łącznika zasobów platformy Azure)
1. Wyślij wiadomość e-mail z powiadomieniem zawierającą opcję ponownego uruchomienia przepływu pracy

Odbiorca wiadomości e-mail może zbadać kondycję obwodu i, w razie potrzeby, ponownie uruchomić obwód za pośrednictwem linku w wiadomości e-mail z powiadomieniem. Gdy przepływ pracy zostanie ponownie uruchomiony, komunikaty są przetwarzane z ostatniego punktu kontrolnego centrum zdarzeń.

Korzystając z tego podejścia, żadne komunikaty nie są tracone, wszystkie komunikaty są przetwarzane w kolejności i można przerwać obwód tak długo, jak to konieczne.

## <a name="resources"></a>Zasoby

- [Przykłady przetwarzania niezawodnych zdarzeń](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Wyłącznik jednostki trwałej platformy Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

- [Obsługa błędów Azure Functions](./functions-bindings-error-pages.md)
- [Automatyzowanie zmiany rozmiaru przekazanych obrazów za pomocą usługi Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Tworzenie funkcji integrującej się z usługą Azure Logic Apps](./functions-twitter-email.md)
