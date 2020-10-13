---
title: Indeksatory uzyskujące dostęp do bezpiecznych zasobów za pośrednictwem prywatnych punktów końcowych
titleSuffix: Azure Cognitive Search
description: Przewodnik dotyczący konfigurowania prywatnych punktów końcowych dla indeksatorów w celu komunikowania się z bezpiecznymi zasobami
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/07/2020
ms.openlocfilehash: 94763cee852893057348f8eea1fa74fa742f62a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91534730"
---
# <a name="accessing-secure-resources-via-private-endpoints"></a>Uzyskiwanie dostępu do bezpiecznych zasobów za pośrednictwem prywatnych punktów końcowych

Zasoby platformy Azure (takie jak konta magazynu używane jako źródła danych) można skonfigurować tak, aby były dostępne tylko z określonej listy sieci wirtualnych. Można je również skonfigurować tak, aby nie zezwalać na dostęp "sieci publicznej".
Klienci mogą zażądać usługi Azure Wyszukiwanie poznawcze, aby utworzyć (wychodzące) [połączenie prywatnego punktu końcowego](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) w celu bezpiecznego dostępu do danych z tych źródeł danych za pośrednictwem indeksatorów.

## <a name="shared-private-link-resources-management-apis"></a>Interfejsy API zarządzania zasobami udostępnionego linku prywatnego

Prywatne punkty końcowe, które są tworzone przez platformę Azure Wyszukiwanie poznawcze na żądanie klienta, aby uzyskać dostęp do "bezpiecznych" zasobów nazywanych *udostępnionymi zasobami linków prywatnych*. Klient uzyskuje dostęp do zasobu (takiego jak konto magazynu), który jest dołączony do [usługi łącza prywatnego platformy Azure](https://azure.microsoft.com/services/private-link/).

Usługa Azure Wyszukiwanie poznawcze oferuje interfejs API zarządzania wyszukiwaniem, możliwość [tworzenia lub aktualizowania udostępnionych zasobów linków prywatnych](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate). Użyjesz tego interfejsu API wraz z innymi udostępnionymi interfejsami API zarządzania *zasobami linków prywatnych* , aby skonfigurować dostęp do bezpiecznego zasobu z usługi Azure wyszukiwanie poznawcze indeksator.

Połączenia z prywatnymi punktami końcowymi do niektórych zasobów można tworzyć tylko za pośrednictwem wersji zapoznawczej interfejsu API zarządzania wyszukiwaniem ( `2020-08-01-Preview` ) wskazanego za pomocą tagu "wersja zapoznawcza" w poniższej tabeli. Zasoby bez tagu "Preview" można tworzyć za pośrednictwem interfejsu API w wersji zapoznawczej oraz interfejsu API GA ( `2020-08-01` )

Poniżej znajduje się lista zasobów platformy Azure, do których można utworzyć wychodzące punkty końcowe punktów końcowych z usługi Azure Wyszukiwanie poznawcze. `groupId` w poniższej tabeli należy używać dokładnie (z uwzględnieniem wielkości liter) w interfejsie API, aby utworzyć zasób udostępnionego linku prywatnego.

| Zasób platformy Azure | Identyfikator grupy |
| --- | --- |
| Azure Storage — obiekt BLOB (lub) ADLS Gen 2 | `blob`|
| Azure Storage — tabele | `table`|
| Azure Cosmos DB — interfejs API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (wersja zapoznawcza) | `mysqlServer`|
| W usłudze Azure Key Vault | `vault` |
| Azure Functions (wersja zapoznawcza) | `sites` |

Lista zasobów platformy Azure, dla których obsługiwane są wychodzące połączenia z prywatnymi punktami końcowymi, może być również wykonywana za pośrednictwem [listy obsługiwanych interfejsów API](https://docs.microsoft.com/rest/api/searchmanagement/privatelinkresources/listsupported).

Na potrzeby tego przewodnika mieszanka [ARMClient](https://github.com/projectkudu/ARMClient) i [Poster](https://www.postman.com/) są używane do zademonstrowania wywołań interfejsu API REST.

> [!NOTE]
> W tym przewodniku przyjęto założenie, że nazwa usługi wyszukiwania to __contoso-Search__ , która istnieje w grupie zasobów __contoso__ subskrypcji z identyfikatorem subskrypcji __00000000-0000-0000-0000-000000000000__. Identyfikator zasobu tej usługi wyszukiwania będzie `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search`

W pozostałej części przewodnika pokazano, jak można skonfigurować usługę __contoso-Search__ , aby jej indeksatory mogły uzyskiwać dostęp do danych z konta bezpiecznego magazynu `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage`

## <a name="securing-your-storage-account"></a>Zabezpieczanie konta magazynu

Skonfiguruj konto magazynu tak, aby [zezwalało na dostęp tylko z określonych podsieci](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network). Po zaznaczeniu tej opcji i pozostawieniu wartości pustej przez Azure Portal w przypadku zaznaczenia tego ustawienia nie jest dozwolony żaden ruch z sieci wirtualnej.

   ![Dostęp Virtual Network](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png "Dostęp Virtual Network")

> [!NOTE]
> [Podejście do zaufanej usługi firmy Microsoft](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services) może służyć do obejścia ograniczeń sieci wirtualnej lub protokołu IP na takich kontach magazynu, a usługa wyszukiwania może uzyskiwać dostęp do danych na koncie magazynu zgodnie z opisem w [przewodniku](search-indexer-howto-access-trusted-service-exception.md). Jednak podczas korzystania z tej metody komunikacja między usługą Azure Wyszukiwanie poznawcze i kontem magazynu odbywa się za pośrednictwem publicznego adresu IP konta magazynu za pośrednictwem bezpiecznej sieci szkieletowej firmy Microsoft.

## <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Krok 1. Tworzenie zasobu udostępnionego linku prywatnego na koncie magazynu

Wykonaj następujące wywołanie interfejsu API, aby zażądać Wyszukiwanie poznawcze platformy Azure, aby utworzyć wychodzące połączenie prywatnego punktu końcowego z kontem magazynu

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Zawartość `create-pe.json` pliku (reprezentująca treść żądania do interfejsu API) jest następująca:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

`202 Accepted`Odpowiedź jest zwracana po powodzeniu — proces tworzenia wychodzącego prywatnego punktu końcowego jest długotrwałą operacją (asynchroniczną). Obejmuje to wdrożenie następujących zasobów —

1. Prywatny punkt końcowy przydzielony z prywatnym adresem IP w `"Pending"` stanie. Prywatny adres IP jest uzyskiwany z przestrzeni adresowej przydzieloną do sieci wirtualnej środowiska wykonawczego prywatnego indeksatora usługi wyszukiwania. Po zatwierdzeniu prywatnego punktu końcowego każda komunikacja z usługi Azure Wyszukiwanie poznawcze do konta magazynu pochodzi z prywatnego adresu IP i kanału bezpiecznego łącza prywatnego.
2. Prywatna strefa DNS dla typu zasobu, na podstawie `groupId` . Zapewni to, że każde wyszukiwanie DNS do zasobu prywatnego korzysta z adresu IP skojarzonego z prywatnym punktem końcowym.

Upewnij się, że określono prawidłową wartość `groupId` dla typu zasobu, dla którego tworzysz prywatny punkt końcowy. Niezgodność spowoduje niepowodzenie komunikatu odpowiedzi.

Podobnie jak w przypadku wszystkich asynchronicznych operacji platformy Azure, `PUT` wywołanie zwraca `Azure-AsyncOperation` wartość nagłówka, która będzie wyglądać następująco:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Ten identyfikator URI może być sondowany okresowo w celu uzyskania stanu operacji. Zalecamy oczekiwanie, aż stan operacji udostępnionego zasobu linku prywatnego osiągnie stan końcowy (czyli `succeeded` ) przed kontynuowaniem.

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

## <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Krok 2a: Zatwierdź połączenie prywatnego punktu końcowego dla konta magazynu

> [!NOTE]
> Ta sekcja używa Azure Portal do przechodzenia przez przepływ zatwierdzania dla prywatnego punktu końcowego do magazynu. [Interfejs API REST](https://docs.microsoft.com/rest/api/storagerp/privateendpointconnections) dostępny za pośrednictwem dostawcy zasobów magazynu (RP) może być również używany w zamian.
>
> Inni dostawcy, tacy jak CosmosDB, Azure SQL Server itp., oferują również podobne interfejsy API RP do zarządzania połączeniami prywatnych punktów końcowych.

Przejdź do karty "**połączenia prywatne punktów końcowych**" konta magazynu na Azure Portal. Powinna istnieć prośba o połączenie prywatnego punktu końcowego z komunikatem żądania z poprzedniego wywołania interfejsu API (po __pomyślnym__zakończeniu operacji asynchronicznej).

   ![Zatwierdzenie prywatnego punktu końcowego](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png "Zatwierdzenie prywatnego punktu końcowego")

Wybierz prywatny punkt końcowy, który został utworzony przez usługę Azure Wyszukiwanie poznawcze (Użyj kolumny "prywatny punkt końcowy", aby zidentyfikować połączenie prywatnego punktu końcowego o nazwie określonej w poprzednim interfejsie API), a następnie wybierz pozycję "Zatwierdź" z odpowiednim komunikatem (komunikat nie jest istotny). Upewnij się, że połączenie z prywatnym punktem końcowym jest wyświetlane w następujący sposób (w dowolnym miejscu od 1-2 minut do zaktualizowania stanu w portalu)

![Zatwierdzono prywatny punkt końcowy](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png "Zatwierdzono prywatny punkt końcowy")

Gdy żądanie połączenia z prywatnym punktem końcowym zostanie zatwierdzone, oznacza to, że ruch *może* przepływać przez prywatny punkt końcowy. Po zatwierdzeniu prywatnego punktu końcowego usługa Azure Wyszukiwanie poznawcze utworzy wymagane mapowania stref DNS w tworzonej dla niego strefie DNS.

## <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Krok 2B: wykonywanie zapytania dotyczącego stanu udostępnionego zasobu linku prywatnego

 Aby upewnić się, że zasób udostępnionego linku prywatnego został zaktualizowany po zatwierdzeniu, uzyskaj jego status za pośrednictwem [interfejsu API pobierania](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Jeśli `properties.provisioningState` zasób ma wartość `Succeeded` i `properties.status` jest `Approved` , oznacza to, że udostępniony zasób link prywatny jest funkcjonalny, a indeksatory można skonfigurować do komunikowania się za pośrednictwem prywatnego punktu końcowego.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

## <a name="step-3-configure-indexer-to-run-in-the-private-environment"></a>Krok 3. Konfigurowanie indeksatora do uruchamiania w środowisku prywatnym

> [!NOTE]
> Ten krok można wykonać nawet przed zatwierdzeniem połączenia prywatnego punktu końcowego. Dopóki prywatne połączenie z punktem końcowym nie zostanie zatwierdzone, każdy indeksator próbujący skomunikować się z bezpiecznym zasobem (na przykład konto magazynu) zakończy się niepowodzeniem w stanie przejściowym. Nie można utworzyć nowych indeksatorów. Gdy tylko zostanie zatwierdzone połączenie prywatnego punktu końcowego, indeksatory będą mogły uzyskać dostęp do prywatnego konta magazynu.

1. [Utwórz źródło danych](https://docs.microsoft.com/rest/api/searchservice/create-data-source) wskazujące konto bezpiecznego magazynu i odpowiedni kontener na koncie magazynu. Poniżej przedstawiono to żądanie wykonywane za pośrednictwem programu Poster.
![Create Data Source](media\search-indexer-howto-secure-access\create-ds.png "Tworzenie źródła danych") (Tworzenie źródła danych)

2. Podobnie [Utwórz indeks](https://docs.microsoft.com/rest/api/searchservice/create-index) i opcjonalnie [Utwórz zestawu umiejętności](https://docs.microsoft.com/rest/api/searchservice/create-skillset) przy użyciu interfejsu API REST.

3. [Utwórz indeksator](https://docs.microsoft.com/rest/api/searchservice/create-indexer) wskazujący źródło danych, indeks i zestawu umiejętności utworzone powyżej. Ponadto należy wymusić uruchomienie indeksatora w prywatnym środowisku wykonywania, ustawiając właściwość konfiguracja indeksatora `executionEnvironment` na `"Private"` .
![Utwórz indeksator](media\search-indexer-howto-secure-access\create-idr.png "Tworzenie indeksatora")

Indeksator powinien zostać pomyślnie utworzony i powinien być w trakcie wykonywania indeksowanie zawartości z konta magazynu przez połączenie prywatnego punktu końcowego. Stan indeksatora można monitorować za pośrednictwem [interfejsu API stanu indeksatora](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Jeśli masz już istniejące indeksatory, możesz po prostu zaktualizować je za pomocą [interfejsu API Put](https://docs.microsoft.com/rest/api/searchservice/create-indexer) , aby ustawić `executionEnvironment` na `"Private"` .

## <a name="troubleshooting-issues"></a>Rozwiązywanie problemów

- W przypadku tworzenia indeksatora, jeśli Tworzenie zakończy się niepowodzeniem z komunikatem o błędzie podobnym do "poświadczenia źródła danych są nieprawidłowe", oznacza to, że połączenie prywatnego punktu końcowego nie zostało *zatwierdzone* lub nie działa.
Uzyskaj stan udostępnionego zasobu linku prywatnego przy użyciu [interfejsu API pobierania](https://docs.microsoft.com/rest/api/searchmanagement/sharedprivatelinkresources/get). Jeśli została *zatwierdzona* , sprawdź `properties.provisioningState` zasób. Jeśli tak jest `Incomplete` , oznacza to, że niektóre z podstawowych zależności dla zasobu nie powiodło się — należy `PUT` ponownie wydać żądanie do "ponownego utworzenia" udostępnionego zasobu linku prywatnego, który powinien rozwiązać ten problem. Ponowne zatwierdzenie może być konieczne — Sprawdź stan zasobu ponownie, aby zweryfikować.
- Jeśli indeksator zostanie utworzony bez ustawienia `executionEnvironment` , tworzenie indeksatora może się powieść, ale jego historia wykonywania będzie pokazywała, że uruchomienie indeksatora nie powiedzie się. Należy [zaktualizować indeksator](https://docs.microsoft.com/rest/api/searchservice/update-indexer) , aby określić środowisko wykonywania.
- Jeśli indeksator zostanie utworzony bez ustawienia `executionEnvironment` i zostanie uruchomiony pomyślnie, oznacza to, że usługa Azure wyszukiwanie poznawcze zdecydowała się, że jego środowisko uruchomieniowe jest określonym środowiskiem "prywatnym" usługi wyszukiwania. Jednak może to ulec zmianie w zależności od różnych czynników (zasobów używanych przez indeksator, obciążenia usługi wyszukiwania itp.) i może zakończyć się niepowodzeniem w późniejszym czasie — zdecydowanie zalecamy ustawienie opcji `executionEnvironment` tak `"Private"` , aby upewnić się, że nie ulegnie awarii w przyszłości.
- [Przydziały i limity](search-limits-quotas-capacity.md) określają, ile udostępnionych zasobów linków prywatnych można utworzyć i zależeć od jednostki SKU usługi wyszukiwania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o prywatnych punktach końcowych:

- [Co to są prywatne punkty końcowe?](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
- [Konfiguracje DNS, które są zbędne dla prywatnych punktów końcowych](https://docs.microsoft.com/azure/private-link/private-endpoint-dns)