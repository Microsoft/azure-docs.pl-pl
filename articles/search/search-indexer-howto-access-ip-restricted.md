---
title: Zezwalaj na dostęp do zakresów adresów IP indeksatora
titleSuffix: Azure Cognitive Search
description: Skonfiguruj reguły zapory adresów IP, aby zezwolić na dostęp do danych za pomocą indeksatora usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 01a88be9c54a2701130daace26c44159ee364e4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98757247"
---
# <a name="configure-ip-firewall-rules-to-allow-indexer-connections-azure-cognitive-search"></a>Konfigurowanie reguł zapory adresów IP w celu zezwalania na połączenia indeksatora (Azure Wyszukiwanie poznawcze)

Reguły zapory adresów IP w zasobach platformy Azure, takich jak konta magazynu, konta Cosmos DB i serwery SQL platformy Azure, zezwalają tylko na ruch pochodzący z określonych zakresów adresów IP w celu uzyskania dostępu do danych.

W tym artykule opisano sposób konfigurowania reguł IP przy użyciu Azure Portal dla konta magazynu, dzięki czemu indeksatory usługi Azure Wyszukiwanie poznawcze mogą bezpiecznie uzyskiwać dostęp do danych. W odniesieniu do usługi Azure Storage podejście to również działa dla innych zasobów platformy Azure, które używają reguł zapory IP do zabezpieczania dostępu do danych.

> [!NOTE]
> Reguły zapory IP dla konta magazynu działają tylko wtedy, gdy konto magazynu i usługa wyszukiwania znajdują się w różnych regionach. Jeśli Instalator nie zezwala na to, zalecamy użycie [opcji wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md).

## <a name="get-the-ip-address-of-the-search-service"></a>Pobierz adres IP usługi wyszukiwania

Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) usługi wyszukiwania. Będzie to wyglądać następująco `<search-service-name>.search.windows.net` . Nazwę FQDN można sprawdzić, wyszukując usługę wyszukiwania w Azure Portal.

   ![Uzyskaj nazwę FQDN usługi](media\search-indexer-howto-secure-access\search-service-portal.png "Uzyskaj nazwę FQDN usługi")

Adres IP usługi wyszukiwania można uzyskać przez wykonanie `nslookup` (lub `ping` ) nazwy FQDN. W poniższym przykładzie dodasz "150.0.0.1" do reguły ruchu przychodzącego w zaporze usługi Azure Storage. Aktualizacja ustawień zapory dla indeksatora usługi Search może potrwać do 15 minut, aby uzyskać dostęp do konta usługi Azure Storage.

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

Dodatkowe adresy IP są używane w przypadku żądań pochodzących ze [środowiska wykonywania wielodostępnego](search-indexer-securing-resources.md#indexer-execution-environment)indeksatora. Ten zakres adresów IP można uzyskać ze znacznika usługi.

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

Najprostszym sposobem dodawania zakresów adresów IP do reguły zapory konta magazynu jest za pośrednictwem Azure Portal. Znajdź konto magazynu w portalu i przejdź do karty **zapory i sieci wirtualne** .

   ![Zapora i sieci wirtualne](media\search-indexer-howto-secure-access\storage-firewall.png "Zapora i sieci wirtualne")

Dodaj do zakresu adresów trzy wcześniej uzyskane adresy IP (1 dla adresu IP usługi wyszukiwania, 2 dla `AzureCognitiveSearch` tagu usługi) i wybierz pozycję **Zapisz**.

   ![Reguły protokołu IP zapory](media\search-indexer-howto-secure-access\storage-firewall-ip.png "Reguły protokołu IP zapory")

Zaktualizowanie reguł zapory trwa 5-10 minut, po których indeksatory powinny mieć dostęp do danych na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie zapór usługi Azure Storage](../storage/common/storage-network-security.md)
- [Konfigurowanie zapory IP dla Cosmos DB](../cosmos-db/how-to-configure-firewall.md)
- [Konfigurowanie zapory IP dla usługi Azure SQL Server](../azure-sql/database/firewall-configure.md)