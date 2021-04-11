---
title: Rozwiązywanie problemów z nieprawidłowymi wyjątkami żądań Azure Cosmos DB
description: Dowiedz się, jak zdiagnozować i naprawić złe wyjątki żądań, takie jak zawartość wejściowa lub klucz partycji, w Azure Cosmos DB nie pasuje do klucza partycji.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 04/06/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e0ce00331eb524d064fd6e7c5205be8b66d4dbc2
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556126"
---
# <a name="diagnose-and-troubleshoot-bad-request-exceptions-in-azure-cosmos-db"></a>Diagnozowanie i rozwiązywanie problemów z nieprawidłowymi wyjątkami żądań w Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Kod stanu HTTP 400 reprezentuje żądanie zawiera nieprawidłowe dane lub brakuje wymaganych parametrów.

## <a name="missing-the-id-property"></a><a name="missing-id-property"></a>Brak właściwości identyfikatora
W tym scenariuszu często widzisz błąd:

*Zawartość wejściowa jest nieprawidłowa, ponieważ wymagane właściwości-"ID;" "— Brak*

Odpowiedź z tym błędem oznacza, że dokument JSON, który jest wysyłany do usługi, nie ma wymaganej właściwości ID.

### <a name="solution"></a>Rozwiązanie
Określ `id` Właściwość z wartością ciągu zgodnie ze [specyfikacją REST](/rest/api/cosmos-db/documents) w ramach dokumentu, zestawy SDK nie generują automatycznie wartości tej właściwości.

## <a name="invalid-partition-key-type"></a><a name="invalid-partition-key-type"></a>Nieprawidłowy typ klucza partycji
W tym scenariuszu często są wyświetlane błędy, takie jak:

*Klucz partycji... jest nieprawidłowy.*

Odpowiedź z tym błędem oznacza, że wartość klucza partycji jest nieprawidłowego typu.

### <a name="solution"></a>Rozwiązanie
Wartość klucza partycji powinna być ciągiem lub liczbą, upewnij się, że wartość ma oczekiwane typy.

## <a name="wrong-partition-key-value"></a><a name="wrong-partition-key-value"></a>Nieprawidłowa wartość klucza partycji
W tym scenariuszu często widzisz błąd:

*PartitionKey wyodrębniony z dokumentu nie jest zgodny z określonym w nagłówku*

Odpowiedź z tym błędem oznacza, że wykonujesz operację i przekazujesz wartość klucza partycji, która nie jest zgodna z wartością treści dokumentu dla oczekiwanej właściwości. Jeśli ścieżka klucza partycji kolekcji to `/myPartitionKey` , dokument ma właściwość o nazwie `myPartitionKey` , która nie jest zgodna z wartością klucza partycji podczas wywoływania metody zestawu SDK.

### <a name="solution"></a>Rozwiązanie
Wyślij parametr wartości klucza partycji, który jest zgodny z wartością właściwości dokumentu.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB platformy .NET.
* Poznaj wskazówki dotyczące wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md).
* [Diagnozowanie i rozwiązywanie](troubleshoot-java-sdk-v4-sql.md) problemów w przypadku używania Azure Cosmos DB Java v4 SDK.
* Poznaj wskazówki dotyczące wydajności dla [zestawu SDK dla języka Java v4](performance-tips-java-sdk-v4-sql.md).
