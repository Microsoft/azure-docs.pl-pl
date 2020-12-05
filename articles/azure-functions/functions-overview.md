---
title: Azure Functions — omówienie
description: Dowiedz się, jak Azure Functions może pomóc w tworzeniu skalowalnych aplikacji bezserwerowych.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 8b836ac8bd2255abf9a94e532a0599994c3ede89
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608256"
---
# <a name="introduction-to-azure-functions"></a>Wprowadzenie do usługi Azure Functions

Często kompilujemy systemy, aby reagować na serię krytycznych zdarzeń. Niezależnie od tego, czy tworzysz internetowy interfejs API, odpowiadasz na zmiany bazy danych, przetwarzanie strumieni danych IoT, czy nawet Zarządzanie kolejkami komunikatów — każda aplikacja musi mieć możliwość uruchamiania kodu w miarę wystąpienia tych zdarzeń.

Aby spełnić to wymaganie, Azure Functions zapewnia "obliczeniowe na żądanie" na dwa znaczące sposoby.

Najpierw Azure Functions umożliwia zaimplementowanie logiki systemu w postaci łatwo dostępnych bloków kodu. Te bloki kodu są nazywane "funkcjami". Różne funkcje można uruchamiać w dowolnym momencie, gdy trzeba odpowiedzieć na krytyczne zdarzenia.

Po drugie, jako żądania zwiększają się, Azure Functions zaspokaja zapotrzebowanie z dowolną liczbą zasobów i wystąpień funkcji — ale tylko wtedy, gdy jest to konieczne. Zgodnie z żądaniami wszystkie dodatkowe zasoby i wystąpienia aplikacji są wyrzucane automatycznie.

Skąd pochodzą wszystkie zasoby obliczeniowe? Azure Functions [udostępnia tyle lub więcej zasobów obliczeniowych, ile jest potrzebnych](./functions-scale.md) do spełnienia wymagań aplikacji.

Zapewnianie zasobów obliczeniowych na żądanie jest istotą [obliczeń bezserwerowych](https://azure.microsoft.com/solutions/serverless/) w Azure Functions.

## <a name="scenarios"></a>Scenariusze

W wielu przypadkach funkcja [integruje się z tablicą usług w chmurze](./functions-triggers-bindings.md) w celu zapewnienia zaawansowanych implementacji funkcji.

Poniżej znajdują się wspólne, _ale nie oznacza to wyczerpujący_ zestaw scenariuszy dla Azure Functions.

| Jeśli chcesz... | następnie... |
| --- | --- |
| **Tworzenie internetowego interfejsu API** | Implementowanie punktu końcowego dla aplikacji sieci Web przy użyciu [wyzwalacza http](./functions-bindings-http-webhook.md) |
| **Przetwarzaj operacje przekazywania plików** | Uruchom kod, gdy plik zostanie przekazany lub zmieniony w usłudze [BLOB Storage](./functions-bindings-storage-blob.md) |
| **Tworzenie bezserwerowego przepływu pracy** | Łączenie szeregu funkcji przy użyciu [funkcji trwałych](./durable/durable-functions-overview.md) |
| **Reagowanie na zmiany bazy danych** | Uruchamiaj logikę niestandardową, gdy dokument jest tworzony lub aktualizowany w [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Uruchamianie zaplanowanych zadań** | Wykonaj kod w [ustalonych godzinach](./functions-bindings-timer.md) |
| **Tworzenie niezawodnych systemów kolejki komunikatów** | Przetwarzaj kolejki komunikatów przy użyciu [queue storage](./functions-bindings-storage-queue.md), [Service Bus](./functions-bindings-service-bus.md)lub [Event Hubs](./functions-bindings-event-hubs.md) |
| **Analizowanie strumieni danych IoT** | Zbieranie i przetwarzanie [danych z urządzeń IoT](./functions-bindings-event-iot.md) |
| **Przetwarzanie danych w czasie rzeczywistym** | Korzystanie z [funkcji i sygnalizującego](./functions-bindings-signalr-service.md) w celu reagowania na dane w momencie |

Podczas kompilowania funkcji dostępne są następujące opcje i zasoby:

- **Używaj preferowanego języka**: pisz funkcje w [językach C#, Java, JavaScript, PowerShell lub Python](./supported-languages.md)lub Użyj [niestandardowego programu obsługi](./functions-custom-handlers.md) , aby użyć praktycznie dowolnego innego języka.

- **Automatyzowanie wdrażania**: w ramach podejścia opartego na narzędziach do używania potoków zewnętrznych jest dostępnych [wyposażono opcji wdrażania](./functions-deployment-technologies.md) .

- **Rozwiązywanie problemów z funkcją**: Użyj [narzędzi monitorowania](./functions-monitoring.md) i [strategii testowania](./functions-test-a-function.md) , aby uzyskać wgląd w swoje aplikacje.

- **Elastyczne opcje cenowe**: w przypadku planu [zużycia](./pricing.md) płacisz tylko wtedy, gdy funkcje są uruchomione, a plany [Premium](./pricing.md) i [App Service](./pricing.md) oferują funkcje dla wyspecjalizowanych potrzeb.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zacznij korzystać z lekcji, przykładów i interaktywnych samouczków](./functions-get-started.md)
