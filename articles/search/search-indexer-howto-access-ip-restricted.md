---
title: Zezwalaj na dostęp do zakresów adresów IP indeksatora
titleSuffix: Azure Cognitive Search
description: Przewodnik, który opisuje sposób konfigurowania reguł zapory adresów IP, aby indeksatory mogły mieć dostęp.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: c80462707d3dccbb8fccff244017053c25ad46e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89463782"
---
# <a name="setting-up-ip-firewall-rules-to-enable-indexer-access"></a>Konfigurowanie reguł zapory adresów IP w celu włączenia dostępu indeksatora

Reguły zapory adresów IP w zasobach platformy Azure, takich jak konta magazynu, konta Cosmos DB i serwery SQL platformy Azure, zezwalają tylko na ruch pochodzący z określonych zakresów adresów IP w celu uzyskania dostępu do danych.

W tym artykule opisano sposób konfigurowania reguł IP przy użyciu Azure Portal dla konta magazynu, dzięki czemu indeksatory usługi Azure Wyszukiwanie poznawcze mogą bezpiecznie uzyskiwać dostęp do danych. W odniesieniu do magazynu ten przewodnik można bezpośrednio przetłumaczyć na inne zasoby platformy Azure, które oferują także reguły zapory adresów IP do zabezpieczania dostępu do danych.

> [!NOTE]
> Reguły zapory IP dla konta magazynu działają tylko wtedy, gdy konto magazynu i usługa wyszukiwania znajdują się w różnych regionach. Jeśli Instalator nie zezwala na to, zalecamy użycie [opcji wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Pobierz adres IP usługi wyszukiwania

Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) usługi wyszukiwania. Będzie to wyglądać następująco `<search-service-name>.search.windows.net` . Nazwę FQDN można sprawdzić, wyszukując usługę wyszukiwania w Azure Portal.

   ![Uzyskaj nazwę FQDN usługi](media\search-indexer-howto-secure-access\search-service-portal.png "Uzyskaj nazwę FQDN usługi")

Adres IP usługi wyszukiwania można uzyskać przez wykonanie `nslookup` (lub `ping` ) nazwy FQDN. Będzie to jeden z adresów IP, który ma zostać dodany do reguł zapory.

```azurepowershell

nslookup contoso.search.windows.net
Server:  server.example.org
Address:  10.50.10.50

Non-authoritative answer:
Name:    <name>
Address:  150.0.0.1
Aliases:  contoso.search.windows.net
```

## <a name="get-the-ip-address-ranges-for-azurecognitivesearch-service-tag"></a>Pobierz zakresy adresów IP dla tagu usługi "AzureCognitiveSearch"

Zakresy adresów IP dla `AzureCognitiveSearch` tagu usługi mogą być uzyskane za pośrednictwem [interfejsu API odnajdowania (wersja zapoznawcza)](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) lub [pliku JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

W tym instruktażu, przy założeniu, że usługa wyszukiwania jest chmurą publiczną platformy Azure, należy pobrać [publiczny plik JSON usługi Azure](https://www.microsoft.com/download/details.aspx?id=56519) .

   ![Pobierz plik JSON](media\search-indexer-howto-secure-access\service-tag.png "Pobierz plik JSON")

Z pliku JSON, przy założeniu, że usługa wyszukiwania znajduje się w regionie zachodnie stany USA, lista adresów IP dla środowiska wykonywania indeksatora z wieloma dzierżawcami znajduje się poniżej.

```json
    {
      "name": "AzureCognitiveSearch.WestCentralUS",
      "id": "AzureCognitiveSearch.WestCentralUS",
      "properties": {
        "changeNumber": 1,
        "region": "westcentralus",
        "platform": "Azure",
        "systemService": "AzureCognitiveSearch",
        "addressPrefixes": [
          "52.150.139.0/26",
          "52.253.133.74/32"
        ]
      }
    }
```

Dla adresów IP/32 Porzuć "/32" (52.253.133.74/32-> 52.253.133.74), inne mogą być używane Verbatim.

## <a name="add-the-ip-address-ranges-to-ip-firewall-rules"></a>Dodaj zakresy adresów IP do reguł zapory adresów IP

Najprostszym sposobem dodawania zakresów adresów IP do reguły zapory konta magazynu jest za pośrednictwem Azure Portal. Znajdź konto magazynu w portalu i przejdź do karty "**zapory i sieci wirtualne**".

   ![Zapora i sieci wirtualne](media\search-indexer-howto-secure-access\storage-firewall.png "Zapora i sieci wirtualne")

Dodaj wcześniej uzyskane trzy adresy IP (1 dla adresu IP usługi wyszukiwania, 2 dla `AzureCognitiveSearch` tagu usługi) w zakresie adresów, a następnie kliknij pozycję "**Zapisz**".

   ![Reguły protokołu IP zapory](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Reguły protokołu IP zapory")

Reguły zapory pobierają od 5-10 minut do zaktualizowania, gdy indeksatory będą mogły uzyskać dostęp do danych na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz, jak uzyskać dwa zestawy adresów IP, aby zezwolić na dostęp do indeksów, użyj następujących linków, aby zaktualizować reguły zapory IP dla niektórych typowych źródeł danych.

- [Konfigurowanie zapór usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security)
- [Konfigurowanie zapory IP dla CosmosDB](https://docs.microsoft.com/azure/cosmos-db/firewall-support)
- [Konfigurowanie zapory IP dla programu Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)