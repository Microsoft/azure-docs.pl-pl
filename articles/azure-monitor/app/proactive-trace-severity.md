---
title: Obniżenie współczynnika ważności śledzenia — Application Insights platformy Azure
description: Monitoruj ślady aplikacji za pomocą usługi Azure Application Insights dla nietypowych wzorców w usłudze śledzenia danych telemetrycznych z funkcją inteligentnego wykrywania.
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 2b27860adfc1652b58fe9c51d4d0b0a6c271fc0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86539877"
---
# <a name="degradation-in-trace-severity-ratio-preview"></a>Spadek współczynnika ważności śledzenia (wersja zapoznawcza)

Ślady są szeroko używane w aplikacjach, co pomaga w poinformowaniu o tym, co się dzieje w tle. Gdy coś się nie stanie, ślady zapewniają decydujący wgląd w sekwencję zdarzeń prowadzących do nieżądanego stanu. Podczas gdy ślady są zwykle niestrukturalne, istnieje jeden element, który można w konkretnym czasie poznać z ich poziomu ważności. W stanie stałym aplikacji oczekujemy, że współczynnik między "dobrymi" śladami (*informacje* i *pełne*) i "złe" ślady (*Ostrzeżenie*, *błąd* i *krytyczne*) pozostają stabilne. Przyjęto założenie, że dane śledzenia "złe" mogą występować regularnie w pewnym zakresie z powodu dowolnej liczby powodów (przejściowych problemów z siecią dla wystąpienia). Jednak gdy rzeczywisty problem zacznie rosnąć, zazwyczaj manifestuje się jako wzrost względnej proporcji "nieprawidłowych" śladów i "dobry" śladów. Application Insights Inteligentne wykrywanie automatycznie analizuje ślady zarejestrowane przez aplikację, a także ostrzega o nietypowych wzorcach w ważności telemetrii śledzenia.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż Konfigurowanie rejestrowania śledzenia dla aplikacji (Zobacz jak skonfigurować odbiornik dziennika śledzenia dla [platformy .NET](./asp-net-trace-logs.md) lub [Java](./java-trace-logs.md)). Jest ona aktywna, gdy aplikacja generuje wystarczającą ilość danych telemetrycznych wyjątku.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy mam ten typ powiadomienia inteligentnego wykrywania?
Ten typ powiadomienia można uzyskać w przypadku, gdy stosunek między "dobrymi" śladami (ślady zarejestrowane na poziomie *informacji* lub *pełnym*) i "złe" ślady (ślady zarejestrowane z poziomem *ostrzeżenia*, *błąd* lub *krytyczny*) są spadek w określonym dniu w porównaniu z linią bazową obliczoną w ciągu ostatnich siedmiu dni.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja ma w nieskończoność problem?
Nie, powiadomienie nie oznacza, że w aplikacji występuje problem. Chociaż spadek współczynnika między śladami "dobre" i "złe" może wskazywać na problem z aplikacją, zmiana współczynnika może być niegroźna. Na przykład wzrost może być spowodowany przez nowy przepływ w aplikacji emitującej więcej "złych" śladów niż istniejące przepływy).

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi procesu diagnostyki:
1. **Klasyfikacja.** Powiadomienie pokazuje, ile operacji ma wpływ. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Czy problem wpływa na cały ruch, czy tylko niektóre operacje? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Możesz użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszej analizie problemu.
