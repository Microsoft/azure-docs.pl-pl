---
title: Rozwiązywanie typowych problemów z indeksatorem wyszukiwania
titleSuffix: Azure Cognitive Search
description: Napraw błędy i typowe problemy z indeksatorami w Azure Cognitive Search, w tym połączenie ze źródłem danych, zaporę i brakujące dokumenty.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: efdd9666c8876ddaf12b9555fa66beb62c56e93e
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740075"
---
# <a name="troubleshooting-common-indexer-issues-in-azure-cognitive-search"></a>Rozwiązywanie typowych problemów z indeksatorem w Azure Cognitive Search

Indeksatorzy mogą mieć wiele problemów podczas indeksowania danych do Azure Cognitive Search. Główne kategorie błędów to:

* [Nawiązywanie połączenia ze źródłem danych lub innymi zasobami](#connection-errors)
* [Przetwarzanie dokumentów](#document-processing-errors)
* [Pozyskiwanie dokumentów do indeksu](#index-errors)

## <a name="connection-errors"></a>Błędy połączenia

> [!NOTE]
> Indeksatorzy mają ograniczoną obsługę uzyskiwania dostępu do źródeł danych i innych zasobów, które są zabezpieczone przez mechanizmy zabezpieczeń sieci platformy Azure. Obecnie indeksatorzy mogą uzyskać dostęp do źródeł danych tylko za pośrednictwem odpowiednich mechanizmów ograniczeń zakresu adresów IP lub reguł sieciowej organizacji zabezpieczeń, jeśli ma to zastosowanie. Szczegóły dotyczące uzyskiwania dostępu do poszczególnych obsługiwanych źródeł danych można znaleźć poniżej.
>
> Adres IP usługi wyszukiwania można znaleźć, wysyłając polecenie ping do jej w pełni kwalifikowanej nazwy domeny (np. `<your-search-service-name>.search.windows.net` ).
>
> Zakres adresów IP tagu usługi można znaleźć przy użyciu plików `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) [JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) do pobrania lub za pośrednictwem interfejsu API odnajdywania [tagów usługi](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview). Zakres adresów IP jest aktualizowany co tydzień.

### <a name="configure-firewall-rules"></a>Konfigurowanie reguł zapory

Usługi Azure Storage, CosmosDB i Azure SQL zapewniają konfigurowalna zaporę. Gdy zapora jest włączona, nie ma konkretnego komunikatu o błędzie. Zazwyczaj błędy zapory są ogólne i wyglądają jak `The remote server returned an error: (403) Forbidden` lub `Credentials provided in the connection string are invalid or have expired` .

Dostępne są 2 opcje zezwalania indeksatorom na dostęp do tych zasobów w takim wystąpieniu:

* Wyłącz zaporę, zezwalając na dostęp z **opcji Wszystkie sieci** (jeśli jest to możliwe).
* Alternatywnie możesz zezwolić na dostęp do adresu IP usługi wyszukiwania i zakresu adresów IP tagu usługi w regułach zapory zasobu `AzureCognitiveSearch` [](../virtual-network/service-tags-overview.md#available-service-tags) (ograniczenie zakresu adresów IP).

Szczegółowe informacje dotyczące konfigurowania ograniczeń zakresu adresów IP dla każdego typu źródła danych można znaleźć pod następującymi linkami:

* [Azure Storage](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Cosmos DB](../storage/common/storage-network-security.md#grant-access-from-an-internet-ip-range)

* [Azure SQL](../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)

**Ograniczenie:** Jak określono w powyższej dokumentacji dotyczącej usługi Azure Storage, ograniczenia zakresu adresów IP będą działać tylko wtedy, gdy usługa wyszukiwania i konto magazynu znajdują się w różnych regionach.

Funkcje platformy Azure (które mogą być używane jako umiejętność [niestandardowego internetowego interfejsu API)](cognitive-search-custom-skill-web-api.md)obsługują [również ograniczenia adresów IP.](../azure-functions/ip-addresses.md#ip-address-restrictions) Lista adresów IP do skonfigurowania będzie adresem IP usługi wyszukiwania i zakresem adresów IP `AzureCognitiveSearch` tagu usługi.

Szczegółowe informacje dotyczące uzyskiwania dostępu do danych w programie SQL Server na maszynie wirtualnej platformy Azure zostały opisane [tutaj](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)

### <a name="configure-network-security-group-nsg-rules"></a>Konfigurowanie reguł sieciowej grupy zabezpieczeń

W przypadku uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL lub gdy maszyna wirtualna platformy Azure jest używana jako identyfikator URI usługi internetowej dla umiejętności niestandardowego internetowego interfejsu [API,](cognitive-search-custom-skill-web-api.md)klienci nie muszą być zaniepokojeni określonymi adresami IP.

W takich przypadkach maszynę wirtualną platformy Azure lub wystąpienie zarządzane SQL można skonfigurować tak, aby znajdowało się w sieci wirtualnej. Następnie można skonfigurować sieciową grupę zabezpieczeń do filtrowania typu ruchu sieciowego, który może przepływać do i z podsieci sieci wirtualnej oraz interfejsów sieciowych.

Tagu usługi można używać bezpośrednio w regułach sieciowej organizacji ruchu przychodzącego bez konieczności wyszukiwania `AzureCognitiveSearch` zakresu adresów IP. [](../virtual-network/manage-network-security-group.md#work-with-security-rules)

Więcej szczegółów dotyczących uzyskiwania dostępu do danych w wystąpieniu zarządzanym SQL można znaleźć [tutaj](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

### <a name="cosmosdb-indexing-isnt-enabled"></a>"Indeksowanie" usługi CosmosDB nie jest włączone

Azure Cognitive Search ma niejawną zależność od Cosmos DB indeksowania. Jeśli wyłączysz automatyczne indeksowanie w Cosmos DB, Azure Cognitive Search zwraca stan powodzenia, ale nie można indeksować zawartości kontenera. Aby uzyskać instrukcje dotyczące sprawdzania ustawień i włączanie indeksowania, zobacz [Zarządzanie indeksowaniem w programie Azure Cosmos DB](../cosmos-db/how-to-manage-indexing-policy.md#use-the-azure-portal).

### <a name="sharepoint-online-conditional-access-policies"></a>Zasady dostępu warunkowego usługi SharePoint Online

Podczas tworzenia indeksatora usługi SharePoint Online należy przejść przez krok, który wymaga zalogowania się do aplikacji usługi AAD po podaniem kodu urządzenia. Jeśli zostanie wyświetlony komunikat "Logowanie się powiodło, ale administrator wymaga zarządzania urządzeniem żądający dostępu", indeksator prawdopodobnie będzie mieć zablokowany dostęp do biblioteki dokumentów usługi SharePoint Online z powodu zasad dostępu [warunkowego.](https://review.docs.microsoft.com/azure/active-directory/conditional-access/overview)

Aby zaktualizować zasady w celu umożliwienia indeksatorowi dostępu do biblioteki dokumentów, wykonaj następujące kroki:

1. Otwórz okno Azure Portal wyszukaj pozycję **Dostęp warunkowy** usługi Azure AD, a następnie wybierz **pozycję Zasady w** menu po lewej stronie. Jeśli nie masz dostępu do wyświetlania tej strony, musisz znaleźć kogoś, kto ma dostęp, lub uzyskać dostęp.

1. Ustal, które zasady blokują indeksator usługi SharePoint Online dostęp do biblioteki dokumentów. Zasady, które mogą blokować indeksator, będą obejmować konto użytkownika, które było używane do uwierzytelniania podczas kroku tworzenia indeksatora w **sekcji Użytkownicy i** grupy. Zasady mogą również zawierać **warunki,** które:
    * Ograniczanie **platform systemu Windows.**
    * Ogranicz **aplikacje mobilne i klientów klasycznych.**
    * Dla **ustawienia Stan urządzenia** skonfiguruj opcję **Tak.**

1. Po potwierdzeniu, że istnieją zasady, które blokują indeksator, należy wprowadzić zwolnienie dla indeksatora. Pobierz adres IP usługi wyszukiwania.

    1. Uzyskaj w pełni kwalifikowaną nazwę domeny (FQDN) usługi wyszukiwania. Będzie to wyglądać jak `<search-service-name>.search.windows.net` . Możesz dowiedzieć się więcej o witrynie FQDN, wyszukując usługę wyszukiwania w Azure Portal.

   ![Uzyskiwanie sieci FQDN usługi](media\search-indexer-howto-secure-access\search-service-portal.png "Uzyskiwanie domeny FQDN usługi")

    Adres IP usługi wyszukiwania można uzyskać, wykonując `nslookup` (lub `ping` ) adres FQDN. W poniższym przykładzie należy dodać "150.0.0.1" do reguły ruchu przychodzącego w zaporze usługi Azure Storage. Może upłynieć do 15 minut po zaktualizowaniu ustawień zapory, aby indeksator usługi wyszukiwania mógł uzyskać dostęp do konta usługi Azure Storage.

    ```azurepowershell

    nslookup contoso.search.windows.net
    Server:  server.example.org
    Address:  10.50.10.50
    
    Non-authoritative answer:
    Name:    <name>
    Address:  150.0.0.1
    Aliases:  contoso.search.windows.net
    ```

1. Pobierz zakresy adresów IP dla środowiska wykonywania indeksatora dla swojego regionu.

    Dodatkowe adresy IP są używane dla żądań pochodzących ze środowiska wykonywania wielodostępu [indeksatora](search-indexer-securing-resources.md#indexer-execution-environment). Ten zakres adresów IP można uzyskać z tagu usługi.

    Zakresy adresów IP dla tagu usługi można uzyskać za pośrednictwem interfejsu API odnajdywania (wersja zapoznawcza) lub pliku `AzureCognitiveSearch` [JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)do [](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) pobrania.

    W tym przewodniku, zakładając, że usługa wyszukiwania jest chmurą publiczną platformy Azure, należy pobrać publiczny plik [JSON](https://www.microsoft.com/download/details.aspx?id=56519) platformy Azure.

   ![Pobieranie pliku JSON](media\search-indexer-troubleshooting\service-tag.png "Pobieranie pliku JSON")

    W pliku JSON, zakładając, że usługa wyszukiwania znajduje się w zachodnio-środkowych usa, poniżej przedstawiono listę adresów IP dla środowiska wykonawczego indeksatora z wieloma dzierżawami.

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

1. Po powrocie na stronę Dostęp warunkowy w Azure Portal **wybierz** pozycję Nazwane lokalizacje z menu po lewej stronie, a następnie wybierz pozycję + **Lokalizacja zakresów adresów IP**. Nadaj nowej nazwanej lokalizacji nazwę i dodaj zakresy adresów IP dla środowisk wykonywania usługi wyszukiwania i indeksatora zebranych w dwóch ostatnich krokach.
    * Dla adresu IP usługi wyszukiwania może być konieczne dodanie ciągu "/32" na końcu adresu IP, ponieważ akceptuje on tylko prawidłowe zakresy adresów IP.
    * Należy pamiętać, że w przypadku zakresów adresów IP środowiska wykonywania indeksatora wystarczy dodać zakresy adresów IP dla regionu, w którym znajduje się usługa wyszukiwania.

1. Wyklucz nową nazwaną lokalizację z zasad. 
    1. Wybierz **pozycję Zasady** w menu po lewej stronie. 
    1. Wybierz zasady, które blokują indeksator.
    1. Wybierz **pozycję Warunki.**
    1. Wybierz **pozycję Lokalizacje.**
    1. Wybierz **pozycję Wyklucz,** a następnie dodaj nową nazwaną lokalizację.
    1. **Zapisz** zmiany.

1. Poczekaj kilka minut na zaktualizowanie i wymuszenia nowych reguł zasad przez zasady.

1. Spróbuj ponownie utworzyć indeksator
    1. Wyślij żądanie aktualizacji dla utworzonego obiektu źródła danych.
    1. Wyślij ponownie żądanie utworzenia indeksatora. Użyj nowego kodu, aby się zalogować, a następnie wyślij kolejne żądanie utworzenia indeksatora po pomyślnym zalogowaniu.

## <a name="document-processing-errors"></a>Błędy przetwarzania dokumentów

### <a name="unprocessable-or-unsupported-documents"></a>Dokumenty nieprzetworzone lub nieobsługiwane

Indeksator obiektów blob [dokumentuje, które formaty dokumentów są jawnie obsługiwane.](search-howto-indexing-azure-blob-storage.md#SupportedFormats). Czasami kontener magazynu obiektów blob zawiera nieobsługiwane dokumenty. W innych momentach mogą wystąpić problemy z dokumentami. Możesz uniknąć zatrzymywania indeksatora w tych dokumentach, [zmieniając opcje konfiguracji:](search-howto-indexing-azure-blob-storage.md#DealingWithErrors)

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

Indeksator obiektów blob [znajduje i wyodrębnia tekst z obiektów blob w kontenerze](search-howto-indexing-azure-blob-storage.md#how-azure-search-indexes-blobs). Niektóre problemy z wyodrębnianiem tekstu:

* Dokument zawiera tylko zeskanowane obrazy. Obiekty blob PDF, które mają zawartość nie text, takie jak zeskanowane obrazy (JPG), nie dają wyników w standardowym potoku indeksowania obiektów blob. Jeśli masz zawartość obrazu z elementami tekstowymi, możesz użyć wyszukiwania [poznawczego,](cognitive-search-concept-image-scenarios.md) aby znaleźć i wyodrębnić tekst.
* Indeksator obiektów blob jest skonfigurowany do indeksowania tylko metadanych. Aby wyodrębnić zawartość, indeksator obiektów blob musi być skonfigurowany do wyodrębniania [zawartości i metadanych:](search-howto-indexing-azure-blob-storage.md#PartsOfBlobToIndex)

```
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "contentAndMetadata" } }
}
```

## <a name="index-errors"></a>Błędy indeksu

### <a name="missing-documents"></a>Brakujące dokumenty

Indeksatorzy znajdują dokumenty ze [źródła danych](/rest/api/searchservice/create-data-source). Czasami dokument ze źródła danych, który powinien zostać zindeksowany, wydaje się brakować w indeksie. Istnieje kilka typowych przyczyn, dla których te błędy mogą wystąpić:

* Dokument nie został zindeksowany. Sprawdź w portalu, czy indeksator został pomyślnie uruchomiony.
* Sprawdź wartość [śledzenia](/rest/api/searchservice/create-data-source#data-change-detection-policies) zmian. Jeśli wartość limitu wysokiego jest datą ustawioną na czas przyszły, wszystkie dokumenty, których data jest mniejsza niż ta, zostaną pominięte przez indeksator. Stan śledzenia zmian indeksatora można zrozumieć przy użyciu pól "initialTrackingState" i "finalTrackingState" w [stanie indeksatora](/rest/api/searchservice/get-indexer-status#indexer-execution-result).
* Dokument został zaktualizowany po uruchomieniu indeksatora. Jeśli indeksator jest zgodnie z [harmonogramem,](/rest/api/searchservice/create-indexer#indexer-schedule)po pewnym czasie zostanie on ponownie uruchomić i odbierze dokument.
* Zapytanie [określone](/rest/api/searchservice/create-data-source) w źródle danych wyklucza dokument. Indeksatorzy nie mogą indeksować dokumentów, które nie są częścią źródła danych.
* [Mapowania pól lub](/rest/api/searchservice/create-indexer#fieldmappings) [wzbogacanie AI](./cognitive-search-concept-intro.md) zmieniły dokument i wygląda on inaczej niż oczekiwano.
* Użyj [interfejsu API dokumentu odnośnika,](/rest/api/searchservice/lookup-document) aby znaleźć dokument.