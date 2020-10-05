---
title: Azure Functions — omówienie
description: Informacje na temat sposobu używania usługi Azure Functions do optymalizowania obciążeń asynchronicznych w ciągu minut.
author: mattchenderson
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 01/16/2020
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: f0948cb58ba9403a34fbfd61ec43c29bed3440bc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "77621002"
---
# <a name="an-introduction-to-azure-functions"></a>Wprowadzenie do usługi Azure Functions

Azure Functions umożliwia uruchamianie małych fragmentów kodu (nazywanych "funkcjami") bez obaw o infrastrukturę aplikacji. W przypadku Azure Functions infrastruktura chmurowa zapewnia wszystkie aktualne serwery, które są potrzebne, aby aplikacja była uruchamiana na dużą skalę.

Funkcja jest wyzwalana przez określony typ zdarzenia. [Obsługiwane wyzwalacze](./functions-triggers-bindings.md) obejmują reagowanie na zmiany danych, reagowanie na komunikaty, uruchamianie zgodnie z harmonogramem lub w wyniku żądania HTTP.

Mimo że zawsze możesz bezpośrednio korzystać z wyposażono usług, integracja z innymi usługami odbywa się przy użyciu powiązań. Powiązania zapewniają [deklaratywny dostęp do różnorodnych usług platformy Azure i innych](./functions-triggers-bindings.md)firm.

## <a name="features"></a>Funkcje

Niektóre kluczowe funkcje Azure Functions obejmują:

- **Aplikacje bezserwerowe**: funkcje umożliwiają Programowanie aplikacji [bezserwerowych](https://azure.microsoft.com/solutions/serverless/) na Microsoft Azure.

- **Wybór języka**: funkcje zapisu przy użyciu wybranych języków [C#, Java, JavaScript, Python i PowerShell](supported-languages.md).

- **Model cen z opcją płatność za użycie**: płacisz tylko za czas działania kodu. Zapoznaj się z opcjami planu hostingowego zużycia w [sekcji cennika](#pricing).  

- Wprowadzanie **własnych zależności**: funkcja obsługuje narzędzia NuGet i npm, zapewniając dostęp do ulubionych bibliotek.

- **Zabezpieczenia zintegrowane**: Ochrona funkcji wyzwalanych przez protokół HTTP z dostawcami OAuth, takimi jak Azure Active Directory, Facebook, Google, Twitter i konto Microsoft.

- **Uproszczona integracja**: łatwo Integruj się z usługami platformy Azure i ofertami oprogramowanie jako usługa (SaaS).

- **Elastyczne programowanie**: Skonfiguruj ciągłą integrację i Wdróż swój kod za poorednictwem usługi [GitHub](../app-service/scripts/cli-continuous-deployment-github.md), [Azure DevOps Services](../app-service/scripts/cli-continuous-deployment-vsts.md)i innych [obsługiwanych narzędzi programistycznych](../app-service/deploy-local-git.md).

- **Bezstanowa architektura bezserwerowa**: organizowanie aplikacji bezserwerowych za pomocą [Durable Functions](durable/durable-functions-overview.md).

- **Open Source**: środowisko uruchomieniowe funkcji to open-source i [dostępne w serwisie GitHub](https://github.com/azure/azure-webjobs-sdk-script).

## <a name="what-can-i-do-with-functions"></a>Co można zrobić w środowisku Functions?

Funkcje to doskonałe rozwiązanie do przetwarzania danych zbiorczych, integrowania systemów, pracy z Internetem rzeczy oraz tworzenia prostych interfejsów API i mikrousług.

Dostępna jest seria szablonów umożliwiająca rozpoczęcie pracy z kluczowymi scenariuszami, w tym:

- **Http**: uruchamianie kodu opartego na [żądaniach HTTP](functions-create-first-azure-function.md)

- **Czasomierz**: Zaplanuj [Uruchamianie kodu w wstępnie zdefiniowanym czasie](./functions-create-scheduled-function.md)

- **Azure Cosmos DB**: Przetwarzaj [nowe i zmodyfikowane dokumenty Azure Cosmos DB](./functions-create-cosmos-db-triggered-function.md)

- **BLOB Storage**: przetwarzanie [nowych i zmodyfikowanych obiektów BLOB usługi Azure Storage](./functions-create-storage-blob-triggered-function.md)

- **Queue storage**: Odpowiedz na [komunikaty kolejki usługi Azure Storage](./functions-create-storage-queue-triggered-function.md)

- **Event Grid**: reagowanie na [zdarzenia Azure Event Grid za pośrednictwem subskrypcji i filtrów](../event-grid/resize-images-on-storage-blob-upload-event.md)

- **Centrum zdarzeń**: reagowanie na [duże ilości zdarzeń usługi Azure Event Hub](./functions-bindings-event-hubs.md)

- **Kolejka Service Bus**: łączenie się z innymi usługami Azure lub lokalnymi przez [Odpowiadanie na komunikaty w kolejce Service Bus](./functions-bindings-service-bus.md)

- **Service Bus tematu**: łączenie innych usług platformy Azure lub usług lokalnych przez [Odpowiadanie na komunikaty tematu Service Bus](./functions-bindings-service-bus.md)

## <a name="how-much-does-functions-cost"></a><a name="pricing"></a>Ile kosztuje usługa Azure Functions?

Azure Functions ma trzy rodzaje planów cenowych. Wybierz ten, który najlepiej odpowiada Twoim potrzebom:

- **Plan użycia**: platforma Azure udostępnia wszystkie niezbędne zasoby obliczeniowe. Nie musisz martwić się o zarządzanie zasobami i płacisz tylko za czas działania kodu.

- **Plan Premium**: należy określić liczbę wstępnie rozgrzanych wystąpień, które są zawsze w trybie online i są gotowe do natychmiastowego reagowania. Po uruchomieniu funkcji platforma Azure udostępnia wszelkie dodatkowe potrzebne zasoby obliczeniowe. Płacisz za uruchomione przed chwilą wystąpienia ciągłe i wszelkie dodatkowe wystąpienia, które są używane w miarę skalowania aplikacji w systemie Azure.

- **App Service plan**: Uruchom swoje funkcje, podobnie jak aplikacje sieci Web. Jeśli używasz App Service dla innych aplikacji, funkcje mogą być uruchamiane w ramach tego samego planu bez dodatkowych kosztów.

Aby uzyskać więcej informacji o planach hostingu, zobacz [Porównanie planów hostingu usługi Azure Functions](functions-scale.md). Szczegółowe informacje są dostępne na [stronie cennika usługi Functions](https://azure.microsoft.com/pricing/details/functions/).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie pierwszej funkcji platformy Azure](functions-create-first-function-vs-code.md)  
  Rozpocznij pracę z [Visual Studio Code](functions-create-first-function-vs-code.md), [wierszem polecenia](functions-create-first-azure-function-azure-cli.md)lub Użyj [Azure Portal](functions-create-first-azure-function.md).

- [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)  
  Dalsze informacje techniczne na temat środowiska uruchomieniowego usługi Azure Functions, a także dokumentacja dotycząca kodowania funkcji oraz definiowania wyzwalaczy i powiązań.

- [Jak skalować usługę Azure Functions](functions-scale.md)  
  Omówienie planów usług dostępnych w środowisku Azure Functions, w tym planu hostingowego zużycia, oraz sposobu wybierania właściwego planu.

- [Dowiedz się więcej o usłudze Azure App Service](../app-service/overview.md)  
  Środowisko Azure Functions korzysta z platformy Azure App Service na potrzeby funkcji podstawowych, takich jak wdrożenia, zmienne środowiskowe i diagnostyka.
