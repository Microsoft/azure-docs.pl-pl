---
title: Zapobiegaj błędom ograniczania szybkości dla Azure Cosmos DB API dla operacji MongoDB.
description: Dowiedz się, jak zapobiegać występowaniu błędów ograniczania przez interfejs API usługi Azure Cosmos DB dla operacji MongoDB przy użyciu funkcji SSR (ponowna próba po stronie serwera).
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 73c2aba3028f42621f241bd8f295e83e0ef96e68
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540609"
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


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o rozwiązywaniu typowych błędów, zobacz ten artykuł:

* [Rozwiązywanie typowych problemów z interfejsem API Azure Cosmos DB MongoDB](mongodb-troubleshoot.md)
