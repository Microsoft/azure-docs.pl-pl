---
title: Połączenie wystąpienia zarządzanego usługi Azure SQL na potrzeby indeksowania wyszukiwania
titleSuffix: Azure Cognitive Search
description: Włącz publiczny punkt końcowy, aby zezwalać na połączenia z wystąpieniami zarządzanymi SQL z indeksatora na platformie Azure Wyszukiwanie poznawcze.
manager: nitinme
author: vl8163264128
ms.author: victliu
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9e8625724f67caac99ae799674f9db9399e11ad8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89294258"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-managed-instance"></a>Skonfiguruj połączenie z usługą Azure Wyszukiwanie poznawcze indeksator do wystąpienia zarządzanego SQL

Jak zostało to opisane w temacie [łączenie Azure SQL Database z platformą azure wyszukiwanie poznawcze przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), tworzenie indeksatorów z **wystąpieniami zarządzanymi SQL** jest obsługiwane przez usługę Azure wyszukiwanie poznawcze za pośrednictwem publicznego punktu końcowego.

## <a name="create-azure-sql-managed-instance-with-public-endpoint"></a>Utwórz wystąpienie zarządzane Azure SQL z publicznym punktem końcowym
Utwórz wystąpienie zarządzane SQL z wybraną opcją **Włącz publiczny punkt końcowy** .

   ![Włącz publiczny punkt końcowy](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/enable-public-endpoint.png "Włącz publiczny punkt końcowy")

## <a name="enable-azure-sql-managed-instance-public-endpoint"></a>Włącz publiczny punkt końcowy wystąpienia zarządzanego usługi Azure SQL
Możesz również włączyć publiczny punkt końcowy na istniejącym wystąpieniu zarządzanym SQL w obszarze **Security**  >  **Network Virtual**  >  **Endpoint**  >  **enable**.

   ![Włącz publiczny punkt końcowy przy użyciu sieci wirtualnej wystąpienia zarządzanego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-vnet.png "Włącz publiczny punkt końcowy")

## <a name="verify-nsg-rules"></a>Weryfikowanie reguł sieciowej grupy zabezpieczeń
Sprawdź, czy sieciowa Grupa zabezpieczeń ma poprawne **reguły zabezpieczeń ruchu przychodzącego** , które zezwalają na połączenia z usług platformy Azure.

   ![Reguła zabezpieczeń dla ruchu przychodzącego sieciowej grupy zabezpieczeń](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/nsg-rule.png "Reguła zabezpieczeń dla ruchu przychodzącego sieciowej grupy zabezpieczeń")

> [!NOTE]
> Indeksatory nadal wymagają skonfigurowania wystąpienia zarządzanego SQL z publicznym punktem końcowym w celu odczytu danych.
> Można jednak ograniczyć dostęp przychodzący do tego publicznego punktu końcowego, zastępując bieżącą regułę ( `public_endpoint_inbound` ) następującymi 2 regułami:
>
> * Zezwalanie na dostęp przychodzący z `AzureCognitiveSearch` [tagu usługi](../virtual-network/service-tags-overview.md#available-service-tags) ("source" = `AzureCognitiveSearch` , "name" = `cognitive_search_inbound` )
>
> * Zezwalanie na dostęp przychodzący z adresu IP usługi wyszukiwania, który można uzyskać przez polecenie ping do jego w pełni kwalifikowanej nazwy domeny (np. `<your-search-service-name>.search.windows.net` ). ("Źródło" = `IP address` , "nazwa" = `search_service_inbound` )
>
> Dla każdej z tych 2 reguł ustaw wartość "PORT" = `3342` , "Protocol" = `TCP` , "Destination" = `Any` , "Action" = `Allow`

## <a name="get-public-endpoint-connection-string"></a>Pobierz parametry połączenia publicznego punktu końcowego
Upewnij się, że używasz parametrów połączenia dla **publicznego punktu końcowego** (port 3342, nie port 1433).

   ![Parametry połączenia publicznego punktu końcowego](media/search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers/mi-connection-string.png "Parametry połączenia publicznego punktu końcowego")

## <a name="next-steps"></a>Następne kroki
Za pomocą konfiguracji możesz teraz określić wystąpienie zarządzane SQL jako źródło danych dla indeksatora Wyszukiwanie poznawcze platformy Azure przy użyciu portalu lub interfejsu API REST. Aby uzyskać więcej informacji [, zobacz łączenie Azure SQL Database z usługą Azure wyszukiwanie poznawcze przy użyciu indeksatorów](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) .