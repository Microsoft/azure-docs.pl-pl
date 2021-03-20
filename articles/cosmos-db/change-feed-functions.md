---
title: Jak używać Azure Cosmos DB ze źródłem zmian Azure Functions
description: Użyj Azure Functions, aby nawiązać połączenie ze źródłem zmian Azure Cosmos DB. Później można tworzyć reaktywne usługi Azure Functions, które są wyzwalane dla każdego nowego zdarzenia.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7f4903cf29f15132db91e47d78efe5a556efd937
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340247"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Architektury oparte na zdarzeniach bezserwerowych z Azure Cosmos DB i Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Functions zapewnia najprostszy sposób nawiązywania połączenia ze [źródłem zmian](change-feed.md). Można utworzyć małe Azure Functions reaktywne, które będą automatycznie wyzwalane dla każdego nowego zdarzenia w źródle zmian kontenera usługi Azure Cosmos.

:::image type="content" source="./media/change-feed-functions/functions.png" alt-text="Funkcje oparte na zdarzeniach bezserwerowych działające z wyzwalaczem Azure Functions dla Cosmos DB" border="false":::

Korzystając z [wyzwalacza Azure Functions dla Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md), można użyć funkcji skalowania i niezawodnego wykrywania zdarzeń przez [procesor kanału informacyjnego](./change-feed-processor.md), bez konieczności zachowywania [infrastruktury procesów roboczych](./change-feed-processor.md). Wystarczy skupić się na logice funkcji platformy Azure bez obaw o resztę potoku pozyskania zdarzeń. Można nawet mieszać wyzwalacz z innymi [powiązaniami Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Obecnie wyzwalacz Azure Functions dla Cosmos DB jest obsługiwany wyłącznie z interfejsem API Core (SQL).

## <a name="requirements"></a>Wymagania

Aby zaimplementować przepływ oparty na zdarzeniach bezserwerowych, potrzebne są:

* **Monitorowany kontener**: monitorowany kontener jest monitorowanym kontenerem usługi Azure Cosmos i przechowuje dane, z których jest generowana podawanie zmian. Wszystkie operacje wstawiania, aktualizacje monitorowanego kontenera są odzwierciedlane w kanale zmian kontenera.
* **Kontener dzierżawy**: kontener dzierżawy zachowuje stan między wieloma i dynamicznymi wystąpieniami funkcji platformy Azure bez serwera i umożliwia dynamiczne skalowanie. Ten kontener dzierżawy może być ręcznie lub automatycznie tworzony przez wyzwalacz Azure Functions dla Cosmos DB. Aby automatycznie utworzyć kontener dzierżawy, Ustaw flagę *CreateLeaseCollectionIfNotExists* w [konfiguracji](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration). Podzielone na partycje kontenery dzierżaw muszą mieć `/id` definicję klucza partycji.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Utwórz wyzwalacz Azure Functions dla Cosmos DB

Tworzenie funkcji platformy Azure z wyzwalaczem Azure Functions dla Cosmos DB jest teraz obsługiwane dla wszystkich Azure Functions IDE i integracji interfejsu wiersza polecenia:

* [Rozszerzenie programu Visual Studio](../azure-functions/functions-develop-vs.md) dla użytkowników programu Visual Studio.
* [Visual Studio Code rozszerzenie](/azure/developer/javascript/tutorial-vscode-serverless-node-01) dla użytkowników Visual Studio Code.
* A wreszcie [podstawowe narzędzia interfejsu wiersza polecenia](../azure-functions/functions-run-local.md#create-func) dla wieloplatformowego środowiska IDE niezależny od.

## <a name="run-your-trigger-locally"></a>Uruchamianie wyzwalacza lokalnie

[Funkcję platformy Azure](../azure-functions/functions-develop-local.md) można uruchamiać lokalnie za pomocą [emulatora Azure Cosmos DB](./local-emulator.md) , aby tworzyć i opracowywać przepływy oparte na zdarzeniach Bezserwerowych bez subskrypcji platformy Azure ani ponoszenia kosztów.

Jeśli chcesz przetestować scenariusze na żywo w chmurze, możesz [bezpłatnie Wypróbować Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez żadnej karty kredytowej lub subskrypcji platformy Azure.

## <a name="next-steps"></a>Następne kroki

Teraz możesz dalej dowiedzieć się więcej na temat źródła zmian w następujących artykułach:

* [Przegląd źródła zmian](change-feed.md)
* [Sposoby odczytywania źródła zmian](read-change-feed.md)
* [Korzystanie z biblioteki procesora kanału informacyjnego zmiany](change-feed-processor.md)
* [Jak korzystać z biblioteki procesora źródła zmian](change-feed-processor.md)
* [Przetwarzanie baz danych bezserwerowe przy użyciu Azure Cosmos DB i Azure Functions](serverless-computing-database.md)
