---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 72588ccfd9ad00305cbdeaf8132999731d86b9b1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164924"
---
Do każdego dziennika jest przypisany *poziom dziennika* . Wartość jest liczbą całkowitą wskazującą ważność względną:

|LogLevel    |Kod| Opis |
|------------|---|--------------|
|Ślad       | 0 |Dzienniki zawierające najwięcej szczegółowych komunikatów. Te komunikaty mogą zawierać poufne dane aplikacji. Te komunikaty są domyślnie wyłączone i nigdy nie powinny być włączone w środowisku produkcyjnym.|
|Debugowanie       | 1 | Dzienniki, które są używane do interaktywnego badania podczas opracowywania. Te dzienniki powinny przede wszystkim zawierać informacje przydatne do debugowania i nie mają długoterminowej wartości. |
|Informacje | 2 | Dzienniki, które śledzą ogólny przepływ aplikacji. Te dzienniki powinny mieć wartość długoterminową. |
|Ostrzeżenie     | 3 | Dzienniki, które wyróżnią zdarzenia nietypowe lub nieoczekiwane w przepływie aplikacji, ale w przeciwnym razie nie powodują zatrzymania wykonywania aplikacji. |
|Błąd       | 4 | Dzienniki, które różnią się, gdy bieżący przepływ wykonywania został zatrzymany z powodu błędu. Te błędy powinny wskazywać niepowodzenie w bieżącym działaniu, a nie awarię całego aplikacji. |
|Krytyczne    | 5 | Dzienniki opisujące nieodwracalną awarię aplikacji lub systemu albo Katastrofalny błąd, który wymaga natychmiastowej uwagi. |
|Brak        | 6 | Wyłącza rejestrowanie dla określonej kategorii. |

[ *host.jsw* ](../articles/azure-functions/functions-host-json.md) obszarze Konfiguracja pliku określa, ile rejestrowania aplikacja funkcji wysyła do Application Insights.  
