---
title: Nietypowy wzrost w woluminie wyjątku — Application Insights platformy Azure
description: Monitoruj wyjątki aplikacji z funkcją inteligentnego wykrywania w usłudze Azure Application Insights dla nietypowych wzorców w woluminie wyjątku.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 00b7a28a51f91c969b41d2ab85b611f6dde51396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499430"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Nietypowy wzrost w woluminie wyjątku (wersja zapoznawcza)

Application Insights automatycznie analizuje wyjątki zgłoszone w aplikacji i może ostrzec o nietypowych wzorcach w telemetrii wyjątku.

Ta funkcja nie wymaga żadnych specjalnych ustawień, innych niż [Konfigurowanie raportowania wyjątków](./asp-net-exceptions.md#set-up-exception-reporting) dla aplikacji. Jest ona aktywna, gdy aplikacja generuje wystarczającą ilość danych telemetrycznych wyjątku.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>Kiedy mam ten typ powiadomienia inteligentnego wykrywania?
Ten typ powiadomienia może zostać wyświetlony, jeśli w aplikacji występuje nietypowy wzrost liczby wyjątków określonego typu w ciągu dnia, w porównaniu z linią bazową obliczoną w ciągu ostatnich siedmiu dni.
Algorytmy uczenia maszynowego są używane do wykrywania wzrostu liczby wyjątków, przy jednoczesnym uwzględnieniu naturalnego wzrostu użycia aplikacji.

## <a name="does-my-app-definitely-have-a-problem"></a>Czy moja aplikacja ma w nieskończoność problem?
Nie, powiadomienie nie oznacza, że w aplikacji występuje problem. Chociaż nadmierna liczba wyjątków zwykle wskazuje na problem z aplikacją, te wyjątki mogą być niegroźne i prawidłowo obsługiwane przez aplikację.

## <a name="how-do-i-fix-it"></a>Jak go naprawić?
Powiadomienia obejmują informacje diagnostyczne do obsługi procesu diagnostyki:
1. **Klasyfikacja.** Powiadomienie przedstawia liczbę użytkowników lub liczbę żądań, których dotyczy. Może to pomóc w przypisaniu priorytetu do problemu.
2. **Scope.** Czy problem wpływa na cały ruch, czy tylko niektóre operacje? Te informacje można uzyskać na podstawie powiadomienia.
3. **Sprawdź.** Wykrywanie zawiera informacje o metodzie, z której został zgłoszony wyjątek, a także typ wyjątku. Możesz również użyć powiązanych elementów i raportów łączących się z informacjami pomocniczymi, aby pomóc w dalszej analizie problemu.
