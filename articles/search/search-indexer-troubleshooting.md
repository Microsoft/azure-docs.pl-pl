---
title: Rozwiązywanie typowych problemów dotyczących indeksatora wyszukiwania
titleSuffix: Azure Cognitive Search
description: Rozwiązywanie błędów i typowych problemów dotyczących indeksatorów na platformie Azure Wyszukiwanie poznawcze, w tym połączenia ze źródłem danych, zapory i brakujących dokumentów.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7eadc9121c54b636fa8b42579284d4018043e1c1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355129"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Rozwiązywanie typowych problemów indeksatora na platformie Azure Wyszukiwanie poznawcze

Indeksatory mogą być uruchamiane w ramach wielu problemów podczas indeksowania danych na platformie Azure Wyszukiwanie poznawcze. Główne kategorie niepowodzeń obejmują:

* [Nawiązywanie połączenia ze źródłem danych lub innymi zasobami](#connection-errors)
* [Przetwarzanie dokumentu](#document-processing-errors)
* [Pozyskiwanie dokumentów do indeksu](#index-errors)

## <a name="connection-errors"></a>Błędy połączenia

> [!NOTE]
> Indeksatory mają ograniczoną obsługę dostępu do źródeł danych i innych zasobów zabezpieczonych przez mechanizmy zabezpieczeń sieci platformy Azure. Obecnie indeksatory mogą uzyskiwać dostęp tylko do źródeł danych za pomocą odpowiednich mechanizmów ograniczeń zakresu adresów IP lub reguł sieciowej grupy zabezpieczeń, gdy ma to zastosowanie. Szczegóły dotyczące uzyskiwania dostępu do każdego obsługiwanego źródła danych można znaleźć poniżej.
>
> Adres IP usługi wyszukiwania można sprawdzić, wysyłając polecenie ping do jego w pełni kwalifikowanej nazwy domeny (np. `<your-search-service-name>.search.windows.net` ).
>
> Zakres adresów IP można sprawdzić `AzureCognitiveSearch` [service tag](../virtual-network/service-tags-overview.md#available-service-tags) za pomocą [plików JSON do pobrania](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) lub za pośrednictwem [interfejsu API odnajdywania tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Zakres adresów IP jest aktualizowany co tydzień.

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Usługi Azure Storage, CosmosDB i Azure SQL oferują konfigurowalną zaporę. Jeśli Zapora jest włączona, nie ma określonego komunikatu o błędzie. Zwykle błędy zapory są ogólne i wyglądają jak `The remote server returned an error: (403) Forbidden` lub `Credentials provided in the connection string are invalid or have expired` .

Dostępne są dwie opcje zezwalające indeksatorom na dostęp do tych zasobów w takich wystąpieniach:

* Wyłącz zaporę, zezwalając na dostęp ze **wszystkich sieci** (jeśli to możliwe).
* Alternatywnie możesz zezwolić na dostęp dla adresu IP usługi wyszukiwania oraz zakresu adresów IP `AzureCognitiveSearch` [znacznika usługi](../virtual-network/service-tags-overview.md#available-service-tags) w regułach zapory dla zasobu (ograniczenie zakresu adresów IP).

Szczegóły dotyczące konfigurowania ograniczeń zakresu adresów IP dla każdego typu źródła danych można znaleźć w następujących linkach:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Ograniczenie**: zgodnie z powyższym opisem w dokumentacji dotyczącej usługi Azure Storage ograniczenia zakresu adresów IP będą działały tylko wtedy, gdy usługa wyszukiwania i konto magazynu znajdują się w różnych regionach.

Usługa Azure Functions (, która może być używana jako [niestandardowa umiejętność interfejsu API sieci Web](cognitive-search-custom-skill-web-api.md)), również obsługuje [Ograniczenia adresów IP](../azure-functions/ip-addresses.md#ip-address-restrictions). Lista adresów IP do skonfigurowania to adres IP usługi wyszukiwania i zakres adresów IP w `AzureCognitiveSearch` tagu usługi.

Szczegóły dotyczące uzyskiwania dostępu do danych w programie SQL Server na maszynie wirtualnej platformy Azure zostały opisane [tutaj](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Skonfiguruj reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń)

Podczas uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL lub gdy maszyna wirtualna platformy Azure jest używana jako identyfikator URI usługi sieci Web dla [niestandardowej umiejętności interfejsu API sieci Web](cognitive-search-custom-skill-web-api.md), klienci nie muszą mieć określonych adresów IP.

W takich przypadkach maszyna wirtualna platformy Azure lub wystąpienie zarządzane SQL można skonfigurować tak, aby znajdowały się w sieci wirtualnej. Następnie można skonfigurować sieciową grupę zabezpieczeń w celu filtrowania typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej i interfejsów sieciowych.

`AzureCognitiveSearch`Tag usługi może być bezpośrednio używany w [regułach sieciowej grupy zabezpieczeń](../virtual-network/manage-network-security-group.md#work-with-security-rules) dla ruchu przychodzącego bez konieczności wyszukiwania jego zakresu adresów IP.

Więcej szczegółów dotyczących uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL przedstawiono [tutaj](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>CosmosDB "Indexing" nie jest włączona

Usługa Azure Wyszukiwanie poznawcze ma niejawną zależność od indeksowania Cosmos DB. Jeśli wyłączysz automatyczne indeksowanie w Cosmos DB, usługa Azure Wyszukiwanie poznawcze zwróci stan pomyślne, ale nie będzie można indeksować zawartości kontenera. Aby uzyskać instrukcje dotyczące sprawdzania ustawień i włączania indeksowania, zobacz [Zarządzanie indeksowanie w Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

## <a name="document-processing-errors"></a>Błędy przetwarzania dokumentu

### <a name="unprocessable-or-unsupported-documents"></a>Nieprzetworzone lub nieobsługiwane dokumenty

Indeksator obiektów BLOB umożliwia dokumentowanie [formatów dokumentów, które są jawnie obsługiwane.](search-howto-indexing-azure-blob-storage.md#SupportedFormats) Czasami kontener magazynu obiektów BLOB zawiera nieobsługiwane dokumenty. Inne przypadki mogą wystąpić problemy z dokumentami. Możesz uniknąć zatrzymywania indeksatora w tych dokumentach, [zmieniając opcje konfiguracji](search-howto-indexing-azure-blob-storage.md#DealingWithErrors):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="missing-document-content"></a>Brak zawartości dokumentu

Indeksator obiektów BLOB [odnajduje i wyodrębnia tekst z obiektów BLOB w kontenerze](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Niektóre problemy z wyodrębnianiem tekstu obejmują:

* Dokument zawiera tylko zeskanowane obrazy. Obiekty blob PDF, które zawierają zawartość nietekstową, na przykład zeskanowane obrazy (JPGs), nie generują wyników w standardowym potoku indeksowania obiektów BLOB. Jeśli masz zawartość obrazu z elementami tekstowymi, możesz użyć [wyszukiwania poznawczego](cognitive-search-concept-image-scenarios.md) do znajdowania i wyodrębniania tekstu.
* Indeksator obiektu BLOB jest skonfigurowany tylko do indeksowania metadanych. Aby wyodrębnić zawartość, indeksator obiektu BLOB musi być skonfigurowany do [wyodrębniania zawartości i metadanych](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex):

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Błędy indeksowania

### <a name="missing-documents"></a>Brakujące dokumenty

Indeksatory Znajdź dokumenty ze [źródła danych](/rest/api/searchservice/create-data-source). Czasami dokument ze źródła danych, który powinien być indeksowany, prawdopodobnie nie znajduje się w indeksie. Istnieje kilka typowych powodów, dla których mogą wystąpić te błędy:

* Dokument nie został zindeksowany. Sprawdź, czy Portal zakończył działanie indeksatora.
* Sprawdź wartość [śledzenia zmian](/rest/api/searchservice/create-data-source#data-change-detection-policies) . Jeśli wartość górnego limitu jest datą ustawioną w przyszłości, wszystkie dokumenty, które mają datę mniejszą niż ta, zostaną pominięte przez indeksator. Można zrozumieć stan śledzenia zmian indeksatora przy użyciu pól "initialTrackingState" i "finalTrackingState" w [stanie indeksatora](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Dokument został zaktualizowany po uruchomieniu indeksatora. Jeśli indeksator jest zgodnie z [harmonogramem](/rest/api/searchservice/create-indexer#indexer-schedule), zostanie on ostatecznie uruchomiony i zostanie pobrany do dokumentu.
* [Zapytanie](/rest/api/searchservice/create-data-source) określone w źródle danych wyklucza dokument. Indeksatory nie mogą indeksować dokumentów, które nie są częścią źródła danych.
* [Mapowania pól](/rest/api/searchservice/create-indexer#fieldmappings) lub [wzbogacania AI](./cognitive-search-concept-intro.md) zmieniły dokument i wyglądają inaczej niż oczekiwano.
* Użyj [interfejsu API dokumentu wyszukiwania](/rest/api/searchservice/lookup-document) , aby znaleźć dokument.