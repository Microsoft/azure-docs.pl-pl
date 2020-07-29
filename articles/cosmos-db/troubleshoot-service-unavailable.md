---
title: Rozwiązywanie problemów z niedostępnym wyjątkiem usługi Azure Cosmos DB
description: Jak zdiagnozować i naprawić wyjątek niedostępności usługi Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294263"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Diagnozowanie i rozwiązywanie problemów z usługą Azure Cosmos DB niedostępne
Zestaw SDK nie może nawiązać połączenia z usługą Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów
Poniższa lista zawiera znane przyczyny i rozwiązania niedostępnych wyjątków usługi.

### <a name="1-the-required-ports-are-not-enabled"></a>1. wymagane porty nie są włączone.
Sprawdź, czy wszystkie [wymagane porty](performance-tips-dotnet-sdk-v3-sql.md#networking) są włączone.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Jeśli istniejąca aplikacja lub usługa rozpoczęła pobieranie 503

### <a name="1-there-is-an-outage"></a>1. wystąpił przestój
Sprawdź [Stan platformy Azure](https://status.azure.com/status) , aby sprawdzić, czy występuje problem.

### <a name="2-snat-port-exhaustion"></a>2. wyczerpanie portów w ramach translatora adresów sieciowych
Postępuj zgodnie z [przewodnikiem wyczerpania portów](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. wymagane porty są blokowane
Sprawdź, czy wszystkie [wymagane porty](performance-tips-dotnet-sdk-v3-sql.md#networking) są włączone.

## <a name="next-steps"></a>Następne kroki
* [Diagnozowanie i rozwiązywanie](troubleshoot-dot-net-sdk.md) problemów podczas korzystania z zestawu SDK Azure Cosmos DB .NET
* Informacje o wskazówkach dotyczących wydajności dla [platform .NET v3](performance-tips-dotnet-sdk-v3-sql.md) i [.NET V2](performance-tips.md)