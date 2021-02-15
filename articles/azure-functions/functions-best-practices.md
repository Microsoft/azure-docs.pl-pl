---
title: Najlepsze rozwiązania dotyczące Azure Functions
description: Poznaj najlepsze rozwiązania i wzorce Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100378251"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Najlepsze rozwiązania dotyczące wydajności i niezawodności Azure Functions

Ten artykuł zawiera wskazówki dotyczące poprawy wydajności i niezawodności aplikacji funkcji [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) .  

Poniżej przedstawiono najlepsze rozwiązania w zakresie kompilowania i tworzenia architektury rozwiązań bezserwerowych przy użyciu Azure Functions.

## <a name="avoid-long-running-functions"></a>Unikaj długotrwałych funkcji

Duże, długotrwałe funkcje mogą powodować nieoczekiwane problemy z przekroczeniem limitu czasu. Aby dowiedzieć się więcej o limitach czasu dla danego planu hostingu, zobacz [limit czasu aplikacji funkcji](functions-scale.md#timeout).

Funkcja może być duża ze względu na wiele zależności Node.js. Importowanie zależności może być również przyczyną zwiększonych czasów ładowania, które powodują nieoczekiwane przekroczenie limitu czasu. Zależności są ładowane jawnie i niejawnie. Pojedynczy moduł załadowany przez kod może ładować własne dodatkowe moduły.

Jeśli to możliwe, Refaktoryzacja duże funkcje do mniejszych zestawów funkcji, które współpracują i zwracają odpowiedzi szybko. Na przykład funkcja webhook lub wyzwalacz HTTP może wymagać odpowiedzi potwierdzenia w określonym limicie czasu; elementy webhook często wymagają natychmiastowej reakcji. Ładunek wyzwalacza HTTP można przekazać do kolejki w celu przetworzenia przez funkcję wyzwalacza kolejki. Takie podejście umożliwia odroczenie rzeczywistej pracy i zwrócenie natychmiastowej odpowiedzi.

## <a name="cross-function-communication"></a>Komunikacja między funkcjami

[Durable Functions](durable/durable-functions-overview.md) i [Azure Logic Apps](../logic-apps/logic-apps-overview.md) są kompilowane do zarządzania przejściami stanu i komunikacją między wieloma funkcjami.

Jeśli nie korzystasz z Durable Functions lub Logic Apps do integracji z wieloma funkcjami, najlepszym rozwiązaniem jest użycie kolejek usługi Storage w celu komunikacji między funkcjami. Głównym powodem jest to, że kolejki magazynu są tańsze i łatwiejsze do aprowizacji niż inne opcje magazynu.

Rozmiar poszczególnych komunikatów w kolejce magazynu jest ograniczony do 64 KB. Jeśli konieczne jest przekazanie większych komunikatów między funkcjami, kolejka Azure Service Bus może służyć do obsługi rozmiaru wiadomości do 256 KB w warstwie Standardowa i do 1 MB w warstwie Premium.

Tematy Service Bus są przydatne, jeśli wymagane jest filtrowanie komunikatów przed przetworzeniem.

Centra zdarzeń są przydatne do obsługi komunikacji dużej ilości.

## <a name="write-functions-to-be-stateless"></a>Funkcje zapisu są bezstanowe

Funkcje powinny być bezstanowe i idempotentne, jeśli jest to możliwe. Skojarz wszystkie wymagane informacje o stanie z danymi. Na przykład przetworzone zamówienie prawdopodobnie ma skojarzony `state` element członkowski. Funkcja może przetwarzać zamówienie na podstawie tego stanu, gdy sama funkcja pozostaje bez zmian.

Funkcje idempotentne są szczególnie zalecane z wyzwalaczami czasomierza. Na przykład, jeśli masz coś, co bezwzględnie musi być uruchamiane raz dziennie, napisz je tak, aby można je było uruchamiać w dowolnym momencie dnia z takimi samymi wynikami. Funkcja może wyjść, gdy nie ma pracy przez konkretny dzień. Ponadto jeśli poprzednie uruchomienie nie powiodło się, następne uruchomienie powinno zostać wznowione w miejscu, w którym zostało pozostawione.

## <a name="write-defensive-functions"></a>Zapisz funkcje obronne

Załóżmy, że funkcja może napotkać wyjątek w dowolnym momencie. Zaprojektuj funkcje z możliwością kontynuowania z poprzedniego punktu awarii podczas następnego wykonania. Rozważmy scenariusz, który wymaga następujących akcji:

1. Zapytanie o 10 000 wierszy w bazie danych.
2. Utwórz komunikat kolejki dla każdego z tych wierszy, aby przetworzyć kolejny wiersz.

W zależności od tego, jak skomplikowany jest system, może być: działania usług podrzędnych zachowuje się nieprawidłowo, awaria sieci lub osiągnięto limity przydziału itd. Wszystkie te funkcje mogą mieć wpływ na funkcję w dowolnym momencie. Musisz zaprojektować swoje funkcje, aby można je było przygotować.

Jak reaguje kod, jeśli wystąpi błąd po wstawieniu 5 000 elementów do kolejki w celu przetworzenia? Śledź elementy w zestawie, który został ukończony. W przeciwnym razie można je ponownie wstawić później. Takie podwójne wstawianie może mieć poważny wpływ na przepływ pracy, więc [idempotentne funkcje](functions-idempotent.md). 

Jeśli element kolejki został już przetworzony, zezwól funkcji na wartość No-op.

Skorzystaj ze środków obronnych już dostarczonych dla składników, których używasz na platformie Azure Functions. Na przykład zobacz **Obsługa komunikatów trującej kolejki** w dokumentacji [wyzwalaczy i powiązań kolejki usługi Azure Storage](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Najlepsze rozwiązania w zakresie organizacji

W ramach rozwiązania można opracowywać i publikować wiele funkcji. Te funkcje są często połączone w jedną aplikację funkcji, ale mogą być również uruchamiane w oddzielnych aplikacjach funkcji. W planach hostingu Premium i dedykowanych (App Service) wiele aplikacji funkcji może również współużytkować te same zasoby, uruchamiając je w ramach tego samego planu. Sposób grupowania funkcji i aplikacji funkcji może mieć wpływ na wydajność, skalowanie, konfigurację, wdrożenie i bezpieczeństwo całego rozwiązania. Nie ma reguł mających zastosowanie do każdego scenariusza, dlatego należy wziąć pod uwagę informacje przedstawione w tej sekcji podczas planowania i opracowywania funkcji.

### <a name="organize-functions-for-performance-and-scaling"></a>Organizowanie funkcji pod kątem wydajności i skalowania

Każda utworzona funkcja ma rozmiar pamięci. Chociaż jest to zwykle małe, zbyt wiele funkcji w aplikacji funkcji może prowadzić do wolniejszego uruchamiania aplikacji w nowych wystąpieniach. Oznacza to również, że całkowite użycie pamięci przez aplikację funkcji może być wyższe. Trudno jest powiedzieć, ile funkcji powinna znajdować się w pojedynczej aplikacji, co zależy od konkretnego obciążenia. Jeśli jednak funkcja przechowuje wiele danych w pamięci, należy rozważyć użycie mniejszej liczby funkcji w pojedynczej aplikacji.

W przypadku uruchamiania wielu aplikacji funkcji w ramach planu Premium lub dedykowanego (App Service) wszystkie te aplikacje są skalowane ze sobą. Jeśli masz jedną aplikację funkcji, która ma znacznie większą ilość pamięci niż pozostałe, używa nieproporcjonalnej ilości zasobów pamięci dla każdego wystąpienia, na którym aplikacja jest wdrożona. Ponieważ może to spowodować pozostawienie mniejszej ilości pamięci dla innych aplikacji w każdym wystąpieniu, może być konieczne uruchomienie aplikacji funkcji o wysokiej pamięci, takiej jak ta w osobnym planie hostingu.

> [!NOTE]
> W przypadku korzystania z [planu zużycia](./functions-scale.md)zalecamy zawsze umieszczenie każdej aplikacji w swoim planie, ponieważ aplikacje są skalowane niezależnie.

Zastanów się, czy chcesz grupować funkcje o różnych profilach obciążenia. Na przykład jeśli masz funkcję, która przetwarza wiele tysięcy komunikatów w kolejce, a inna, która jest wywoływana tylko sporadycznie, ale ma wysokie wymagania dotyczące pamięci, możesz chcieć wdrożyć je w oddzielnych aplikacjach funkcji, aby uzyskać własne zestawy zasobów i skalować je niezależnie od siebie.

### <a name="organize-functions-for-configuration-and-deployment"></a>Organizowanie funkcji konfiguracji i wdrażania

Aplikacje funkcji mają `host.json` plik, który służy do konfigurowania zaawansowanego zachowania wyzwalaczy funkcji i środowiska uruchomieniowego Azure Functions. Zmiany w `host.json` pliku dotyczą wszystkich funkcji w aplikacji. Jeśli masz pewne funkcje, które wymagają konfiguracji niestandardowych, rozważ przeniesienie ich do własnej aplikacji funkcji.

Wszystkie funkcje w projekcie lokalnym są wdrażane wraz z zestawem plików w aplikacji funkcji na platformie Azure. Może być konieczne oddzielne wdrożenie pojedynczych funkcji lub użycie takich funkcji jak miejsca [wdrożenia](./functions-deployment-slots.md) dla niektórych funkcji, a nie innych. W takich przypadkach należy wdrożyć te funkcje (w oddzielnych projektach kodu) do różnych aplikacji funkcji.

### <a name="organize-functions-by-privilege"></a>Organizuj funkcje według uprawnień

Parametry połączenia i inne poświadczenia przechowywane w ustawieniach aplikacji dają wszystkie funkcje w aplikacji funkcji ten sam zestaw uprawnień w skojarzonym zasobie. Rozważ zminimalizowanie liczby funkcji mających dostęp do określonych poświadczeń, przenosząc funkcje, które nie używają tych poświadczeń do osobnej aplikacji funkcji. Można zawsze używać technik, takich jak [łańcuchy funkcji](/learn/modules/chain-azure-functions-data-using-bindings/) , do przekazywania danych między funkcjami w różnych aplikacjach funkcji.  

## <a name="scalability-best-practices"></a>Najlepsze rozwiązania dotyczące skalowalności

Istnieje wiele czynników, które mają wpływ na sposób skalowania aplikacji funkcji. Szczegółowe informacje znajdują się w dokumentacji dotyczącej [skalowania funkcji](functions-scale.md).  Poniżej przedstawiono niektóre najlepsze rozwiązania zapewniające optymalną skalowalność aplikacji funkcji.

### <a name="share-and-manage-connections"></a>Udostępnianie połączeń i zarządzanie nimi

Używaj ponownie połączeń z zasobami zewnętrznymi, gdy jest to możliwe. Zobacz [jak zarządzać połączeniami w Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Unikaj udostępniania kont magazynu

Podczas tworzenia aplikacji funkcji należy skojarzyć ją z kontem magazynu. Połączenie konta magazynu jest obsługiwane w [ustawieniu aplikacji AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage).

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Nie mieszaj kodu testowego i produkcyjnego w tej samej aplikacji funkcji

Działa w ramach zasobów funkcji udział aplikacji. Na przykład pamięć jest udostępniona. Jeśli używasz aplikacji funkcji w środowisku produkcyjnym, nie dodawaj do niej funkcji i zasobów związanych z testami. Może to spowodować nieoczekiwane obciążenie podczas wykonywania kodu produkcyjnego.

Należy zachować ostrożność w aplikacjach funkcji produkcyjnych. Pamięć jest średnia dla każdej funkcji w aplikacji.

Jeśli masz przywoływany zestaw współużytkowany w wielu funkcjach platformy .NET, umieść go w wspólnym folderze udostępnionym. W przeciwnym razie można przypadkowo wdrożyć wiele wersji tego samego pliku binarnego, które zachowują się inaczej między funkcjami.

Nie używaj pełnego rejestrowania w kodzie produkcyjnym, który ma negatywny wpływ na wydajność.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Użyj kodu asynchronicznego, ale unikaj blokowania wywołań

Programowanie asynchroniczne jest zalecanym najlepszym rozwiązaniem, szczególnie w przypadku blokowania operacji we/wy.

W języku C# należy zawsze unikać odwoływania się do `Result` właściwości lub metody wywołującej `Wait` w `Task` wystąpieniu. Takie podejście może prowadzić do wyczerpania wątków.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Korzystanie z wielu procesów roboczych

Domyślnie każde wystąpienie hosta dla funkcji używa jednego procesu roboczego. Aby zwiększyć wydajność, szczególnie w przypadku środowisk uruchomieniowych jednowątkowego, takich jak Python, użyj [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) , aby zwiększyć liczbę procesów roboczych na hosta (do 10). Azure Functions następnie próbuje równomiernie rozpowszechnić jednoczesne wywołania funkcji przez tych pracowników.

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który tworzy funkcje podczas skalowania aplikacji w celu spełnienia wymagań.

### <a name="receive-messages-in-batch-whenever-possible"></a>W miarę możliwości odbieraj komunikaty w usłudze Batch

Niektóre wyzwalacze, takie jak centrum zdarzeń, umożliwiają otrzymywanie wsadowych komunikatów na pojedynczym wywołaniu.  Przetwarzanie wsadowe komunikatów ma znacznie lepszą wydajność.  Maksymalny rozmiar wsadu można skonfigurować w `host.json` pliku zgodnie z opisem w [host.jsdokumentacji referencyjnej](functions-host-json.md)

Dla funkcji języka C# można zmienić typ na tablicę o jednoznacznie określonym typie.  Na przykład zamiast `EventData sensorEvent` sygnatury metody może być `EventData[] sensorEvent` .  W przypadku innych języków należy jawnie ustawić właściwość Kardynalność w do, aby można było `function.json` `many` włączyć przetwarzanie wsadowe, [jak pokazano tutaj](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Konfigurowanie zachowań hosta w celu lepszego obsłużenia współbieżności

`host.json`Plik w aplikacji funkcji umożliwia konfigurację środowiska uruchomieniowego hosta i zachowań wyzwalających.  Oprócz zachowań wsadowych można zarządzać współbieżnością dla wielu wyzwalaczy. Często dostosowanie wartości w tych opcjach może pomóc każdej skali wystąpienia odpowiednio do potrzeb wywołanych funkcji.

Ustawienia w host.jsw pliku dotyczą wszystkich funkcji w aplikacji w ramach *jednego wystąpienia* funkcji. Na przykład jeśli masz aplikację funkcji z dwoma funkcjami HTTP i [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) żądaniami ustawionymi na wartość 25, żądanie do wyzwalacza http będzie wliczane do współużytkowanych 25 współbieżnych żądań.  Gdy aplikacja funkcji jest skalowana do 10 wystąpień, dziesięć funkcji skutecznie zezwala na 250 współbieżnych żądań (10 wystąpień * 25 współbieżnych żądań na wystąpienie). 

Inne opcje konfiguracji hosta znajdują się w [ artykulehost.jsw konfiguracji](functions-host-json.md).

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Jak zarządzać połączeniami w Azure Functions](manage-connections.md)
* [Azure App Service najlepszych praktyk](../app-service/app-service-best-practices.md)
