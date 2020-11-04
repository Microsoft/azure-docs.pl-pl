---
title: Jak skonwertować formaty tokenów sesji w zestawie .NET SDK — Azure Cosmos DB
description: Informacje na temat konwertowania formatów tokenów sesji w celu zapewnienia zgodności między różnymi wersjami zestawu SDK platformy .NET
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342049"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Konwertowanie formatów tokenów sesji w zestawie .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule wyjaśniono, jak konwertować różne formaty tokenów sesji w celu zapewnienia zgodności między wersjami zestawu SDK.

> [!NOTE]
> Domyślnie zestaw SDK śledzi token sesji automatycznie i użyje najnowszego tokenu sesji.  Aby uzyskać więcej informacji, odwiedź stronę [Korzystanie z tokenów sesji](how-to-manage-consistency.md#utilize-session-tokens). Instrukcje zawarte w tym artykule dotyczą tylko następujących warunków:
> * Twoje konto Azure Cosmos DB używa spójności sesji.
> * Zarządzanie tokenami sesji odbywa się ręcznie.
> * Używasz wielu wersji zestawu SDK w tym samym czasie.

## <a name="session-token-formats"></a>Formaty tokenów sesji

Istnieją dwa formaty tokenów sesji: **proste** i **wektorowe**.  Te dwa formaty nie są zamienne, więc format powinien zostać przekonwertowany podczas przekazywania do aplikacji klienckiej z różnymi wersjami.
- Format tokenu sesji **prostego** jest używany przez zestaw .NET SDK V1 (Microsoft.Azure.DocumentDB-Version 1. x)
- Format tokenu sesji **wektorowej** jest używany przez zestaw .NET SDK V2 (Microsoft.Azure.DocumentDB — wersja 2. x)

### <a name="simple-session-token"></a>Prosty token sesji

Prosty token sesji ma następujący format: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Token sesji wektora

Token sesji wektorowej ma następujący format: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Konwertuj na prosty token sesji

Aby przekazać token sesji do klienta przy użyciu zestawu .NET SDK V1, użyj **prostego** formatu tokenu sesji.  Na przykład użyj następującego przykładowego kodu, aby go przekonwertować.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Konwertuj na token sesji wektorowej

Aby przekazać token sesji do klienta przy użyciu zestawu .NET SDK V2, użyj formatu tokenu sesji **wektora** .  Na przykład użyj następującego przykładowego kodu, aby go przekonwertować.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Następne kroki

Przeczytaj następujące artykuły:

* [Używanie tokenów sesji do zarządzania spójnością w Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Wybierz odpowiedni poziom spójności w Azure Cosmos DB](./consistency-levels.md)
* [Wady dotyczące spójności, dostępności i wydajności w Azure Cosmos DB](./consistency-levels.md)
* [Wady dostępności i wydajności dla różnych poziomów spójności](./consistency-levels.md)