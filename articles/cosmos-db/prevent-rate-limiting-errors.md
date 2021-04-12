---
title: Zapobiegaj błędom ograniczania szybkości dla Azure Cosmos DB API dla operacji MongoDB.
description: Dowiedz się, jak zapobiegać występowaniu błędów ograniczania przez interfejs API usługi Azure Cosmos DB dla operacji MongoDB przy użyciu funkcji SSR (ponowna próba po stronie serwera).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507934"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Zapobiegaj błędom ograniczania szybkości dla Azure Cosmos DB API dla operacji MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Azure Cosmos DB interfejsu API dla operacji MongoDB może zakończyć się niepowodzeniem z błędami ograniczania szybkości (16500/429), jeśli przekraczają limit przepływności kolekcji (jednostek ru). 

Możesz włączyć funkcję ponowień po stronie serwera (SSR) i pozwolić serwerowi na automatyczne ponawianie tych operacji. Żądania są ponawiane po krótkim opóźnieniu dla wszystkich kolekcji na Twoim koncie. Ta funkcja jest wygodną alternatywą do obsługi błędów ograniczania szybkości w aplikacji klienckiej.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Przejdź do usługi Azure Cosmos DB API dla konta MongoDB.

1. Przejdź do okienka **funkcje** poniżej sekcji **Ustawienia** .

1. Wybierz pozycję **Ponów próbę po stronie serwera**.

1. Kliknij pozycję **Włącz** , aby włączyć tę funkcję dla wszystkich kolekcji na Twoim koncie.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Zrzut ekranu funkcji ponawiania po stronie serwera dla interfejsu API Azure Cosmos DB dla MongoDB":::

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

1. Sprawdź, czy dla Twojego konta jest już włączona funkcja SSR:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Włącz** SSR dla wszystkich kolekcji na koncie bazy danych. Wprowadzenie tej zmiany może potrwać do 15 min.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Następujące polecenie spowoduje **wyłączenie** SSR dla wszystkich kolekcji na koncie bazy danych przez usunięcie "DisableRateLimitingResponses" z listy możliwości. Wprowadzenie tej zmiany może potrwać do 15 min.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania
* Jak są ponawiane żądania?
    * Żądania są podejmowane w sposób ciągły (ponad i zbyt ponownie) do momentu osiągnięcia 60-sekundowego limitu czasu. Po osiągnięciu limitu czasu klient otrzyma [wyjątek ExceededTimeLimit (50)](mongodb-troubleshoot.md).
*  Jak mogę monitorować skutki tego SSR?
    *  Można wyświetlić błędy ograniczające szybkość (429s), które są ponawiane po stronie serwera w okienku metryki Cosmos DB. Należy pamiętać, że te błędy nie przechodzą do klienta, gdy jest włączony serwer SSR, ponieważ są obsługiwane i ponawiane po stronie serwera. 
    *  W [Cosmos DB dziennikach zasobów](cosmosdb-monitor-resource-logs.md)można wyszukać wpisy dziennika zawierające ciąg "estimatedDelayFromRateLimitingInMilliseconds".
*  Czy program SSR będzie miał wpływ na mój poziom spójności?
    *  SSR nie ma wpływu na spójność żądania. Żądania są podejmowane ponownie po stronie serwera, jeśli są ograniczone szybkością (z błędem 429). 
*  Czy program SSR ma wpływ na dowolny typ błędu, który klient może otrzymać?
    *  Nie, SSR wpływa tylko na błędy ograniczające szybkość (429s) przez ponowne ponowienie ich po stronie serwera. Ta funkcja uniemożliwia obsługę błędów ograniczania szybkości w aplikacji klienckiej. Wszystkie [inne błędy](mongodb-troubleshoot.md) zostaną przestawione na klienta. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rozwiązywaniu typowych błędów, zobacz ten artykuł:

* [Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB](mongodb-troubleshoot.md)
