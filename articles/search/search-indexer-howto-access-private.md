---
title: Połączenia indeksatora za pomocą prywatnego punktu końcowego
titleSuffix: Azure Cognitive Search
description: Skonfiguruj połączenia indeksatora, aby uzyskiwać dostęp do zawartości z innych zasobów platformy Azure, które są chronione za pośrednictwem prywatnego punktu końcowego.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: ff8aa6688d8a838fa2e06d2eef546025cdd9213f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340057"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Tworzenie połączeń indeksatora za pomocą prywatnego punktu końcowego

Wiele zasobów platformy Azure, takich jak konta usługi Azure Storage, można skonfigurować w taki sposób, aby akceptowały połączenia z listy sieci wirtualnych i odrzucać połączenia spoza sieci publicznej. Jeśli używasz indeksatora do indeksowania danych w usłudze Azure Wyszukiwanie poznawcze, a Twoje źródło danych znajduje się w sieci prywatnej, możesz utworzyć wychodzące połączenie z [punktem końcowym](../private-link/private-endpoint-overview.md) w celu uzyskania dostępu do danych.

Ta metoda łączenia indeksatora podlega następującym dwóm wymaganiom:

+ Zasób platformy Azure, który udostępnia zawartość lub kod, musi być wcześniej zarejestrowany w [usłudze Azure Private Link Service](https://azure.microsoft.com/services/private-link/).

+ Usługa Wyszukiwanie poznawcze platformy Azure musi znajdować się w warstwie Podstawowa lub wyższej. Ta funkcja nie jest dostępna w warstwie Bezpłatna. Ponadto jeśli indeksator ma zestawu umiejętności, warstwa musi mieć wartość Standard 2 (S2) lub wyższą. Aby uzyskać więcej informacji, zobacz [limity usługi](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Interfejsy API zarządzania zasobami udostępnionego linku prywatnego

Prywatne punkty końcowe zabezpieczonych zasobów, które są tworzone za pomocą interfejsów API usługi Azure Wyszukiwanie poznawcze, są określane jako *udostępnione zasoby linków prywatnych*. Wynika to z faktu, że masz dostęp do zasobu, takiego jak konto magazynu, który został zintegrowany z [usługą link prywatny platformy Azure](https://azure.microsoft.com/services/private-link/).

Za pośrednictwem interfejsu API REST zarządzania usługa Azure Wyszukiwanie poznawcze udostępnia operację [metodę createorupdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) , której można użyć do skonfigurowania dostępu za pomocą indeksatora wyszukiwanie poznawcze platformy Azure.

Połączenia prywatnych punktów końcowych można tworzyć tylko dla niektórych zasobów przy użyciu wersji zapoznawczej interfejsu API zarządzania wyszukiwaniem (wersja *2020-08-01-wersja zapoznawcza* lub nowsza), która jest oznaczona jako wersja *zapoznawcza* w poniższej tabeli. Zasoby bez oznaczenia *podglądu* można utworzyć przy użyciu wersji zapoznawczej lub ogólnie dostępnej interfejsu API (*2020-08-01* lub nowszej).

W poniższej tabeli przedstawiono zasoby platformy Azure, dla których można utworzyć wychodzące punkty końcowe z usługi Azure Wyszukiwanie poznawcze. Aby utworzyć zasób udostępnionego linku prywatnego, wprowadź wartości **identyfikatora grupy** dokładnie tak, jak są zapisywane w interfejsie API. W wartościach jest rozróżniana wielkość liter.

| Zasób platformy Azure | Identyfikator grupy |
| --- | --- |
| Azure Storage — obiekt BLOB (lub) ADLS Gen 2 | `blob`|
| Azure Storage — tabele | `table`|
| Azure Cosmos DB — interfejs API SQL | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (wersja zapoznawcza) | `mysqlServer`|
| W usłudze Azure Key Vault | `vault` |
| Azure Functions (wersja zapoznawcza) | `sites` |

Możesz również wysyłać zapytania dotyczące zasobów platformy Azure, dla których wychodzące połączenia prywatnego punktu końcowego są obsługiwane za pomocą [listy obsługiwanych interfejsów API](/rest/api/searchmanagement/privatelinkresources/listsupported).

W pozostałej części tego artykułu mieszane interfejsy API [ARMClient](https://github.com/projectkudu/ARMClient) i [Poster](https://www.postman.com/) są używane do zademonstrowania wywołań interfejsu API REST.

> [!NOTE]
> Przykłady w tym artykule są oparte na następujących założeniach:
> * Nazwa usługi wyszukiwania to _firma Contoso-Search_, która istnieje w grupie zasobów _contoso_ subskrypcji z identyfikatorem subskrypcji _00000000-0000-0000-0000-000000000000_. 
> * Identyfikator zasobu tej usługi wyszukiwania to _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Search/searchServices/contoso-Search_.

W pozostałych przykładach pokazano, jak można skonfigurować usługę _contoso-Search_ , aby jej indeksatory mogły uzyskiwać dostęp do danych z konta bezpiecznego magazynu _/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/Providers/Microsoft.Storage/storageAccounts/contoso-Storage_.

## <a name="secure-your-storage-account"></a>Zabezpiecz swoje konto magazynu

Skonfiguruj konto magazynu tak, aby [zezwalało na dostęp tylko z określonych podsieci](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). W Azure Portal, w przypadku wybrania tej opcji i pozostawienia ustawienia pustego, oznacza to, że ruch z sieci wirtualnych nie jest dozwolony.

   ![Zrzut ekranu okienka "zapory i sieci wirtualne" pokazujący opcję zezwalania na dostęp do wybranych sieci. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Możesz użyć [podejścia zaufanej usługi firmy Microsoft](../storage/common/storage-network-security.md#trusted-microsoft-services) , aby obejść ograniczenia sieci wirtualnej lub protokołu IP na koncie magazynu. Możesz również włączyć usługę wyszukiwania, aby uzyskać dostęp do danych na koncie magazynu. Aby to zrobić, zobacz [indeksatora dostęp do usługi Azure Storage przy użyciu wyjątku usługi zaufanej](search-indexer-howto-access-trusted-service-exception.md). 
>
> Jednak w przypadku korzystania z tej metody komunikacja między usługą Azure Wyszukiwanie poznawcze i kontem magazynu odbywa się za pośrednictwem publicznego adresu IP konta magazynu za pośrednictwem bezpiecznej sieci szkieletowej firmy Microsoft.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Krok 1. Tworzenie zasobu udostępnionego linku prywatnego na koncie magazynu

Aby zażądać Wyszukiwanie poznawcze platformy Azure w celu utworzenia wychodzącego połączenia z prywatnym punktem końcowym z kontem magazynu, wykonaj następujące wywołanie interfejsu API: 

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Zawartość *create-pe.jsw* pliku, która reprezentuje treść żądania do interfejsu API, są następujące:

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

`202 Accepted`Odpowiedź jest zwracana po powodzeniu. Proces tworzenia wychodzącego prywatnego punktu końcowego jest operacją długotrwałą (asynchroniczną). Obejmuje to wdrożenie następujących zasobów:

* Prywatny punkt końcowy przydzielony z prywatnym adresem IP w `"Pending"` stanie. Prywatny adres IP jest uzyskiwany z przestrzeni adresowej, która została przypisana do sieci wirtualnej środowiska wykonawczego dla indeksatora prywatnego specyficznego dla usługi wyszukiwania. Po zatwierdzeniu prywatnego punktu końcowego każda komunikacja z usługi Azure Wyszukiwanie poznawcze do konta magazynu pochodzi z prywatnego adresu IP i kanału bezpiecznego łącza prywatnego.

* Prywatna strefa DNS dla typu zasobu, na podstawie `groupId` . Wdrażając ten zasób, należy upewnić się, że każde wyszukiwanie DNS do zasobu prywatnego korzysta z adresu IP skojarzonego z prywatnym punktem końcowym.

Upewnij się, że określono prawidłową wartość `groupId` dla typu zasobu, dla którego tworzysz prywatny punkt końcowy. Niezgodność spowoduje niepowodzenie komunikatu odpowiedzi.

Podobnie jak w przypadku wszystkich asynchronicznych operacji platformy Azure, `PUT` wywołanie zwraca `Azure-AsyncOperation` wartość nagłówka, która wygląda następująco:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Można sondować ten identyfikator URI okresowo, aby uzyskać stan operacji. Przed kontynuowaniem zalecamy zaczekanie, aż operacja udostępnionego zasobu linku prywatnego osiągnie stan terminalu (oznacza to, że operacja jest w stanie *powodzenie*).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Krok 2a: Zatwierdź połączenie prywatnego punktu końcowego dla konta magazynu

> [!NOTE]
> W tej sekcji użyjesz Azure Portal, aby zapoznać się z przepływem zatwierdzania dla prywatnego punktu końcowego w celu przechowania. Alternatywnie można użyć [interfejsu API REST](/rest/api/storagerp/privateendpointconnections) , który jest dostępny za pośrednictwem dostawcy zasobów magazynu.
>
> Inni dostawcy, tacy jak Azure Cosmos DB lub Azure SQL Server, oferują podobne interfejsy API dostawcy zasobów magazynu do zarządzania połączeniami prywatnych punktów końcowych.

1. W Azure Portal wybierz kartę **połączenia prywatnego punktu końcowego** na koncie magazynu. Po pomyślnym zakończeniu operacji asynchronicznej powinno istnieć żądanie połączenia prywatnego punktu końcowego z komunikatem żądania z poprzedniego wywołania interfejsu API.

   ![Zrzut ekranu przedstawiający Azure Portal w okienku "połączenia prywatne punktów końcowych".](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Wybierz prywatny punkt końcowy utworzony przez usługę Azure Wyszukiwanie poznawcze. W kolumnie **prywatny punkt końcowy** Zidentyfikuj połączenie prywatnego punktu końcowego o nazwie określonej w poprzednim interfejsie API, wybierz pozycję **Zatwierdź**, a następnie wprowadź odpowiedni komunikat. Zawartość komunikatu nie jest istotna. 

   Upewnij się, że połączenie z prywatnym punktem końcowym jest wyświetlane, jak pokazano na poniższym zrzucie ekranu. Zaktualizowanie stanu w portalu może potrwać od 1 do 2 minut.

   ![Zrzut ekranu przedstawiający Azure Portal, w którym jest wyświetlany stan "zatwierdzone" w okienku "połączenia prywatne punktów końcowych".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

Po zatwierdzeniu żądania połączenia z prywatnym punktem końcowym ruch *może* przepływać przez prywatny punkt końcowy. Po zatwierdzeniu prywatnego punktu końcowego usługa Azure Wyszukiwanie poznawcze tworzy wymagane mapowania stref DNS w tworzonej dla niego strefie DNS.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Krok 2B: wykonywanie zapytania dotyczącego stanu udostępnionego zasobu linku prywatnego

Aby upewnić się, że zasób udostępnionego linku prywatnego został zaktualizowany po zatwierdzeniu, uzyskaj jego stan przy użyciu [interfejsu API pobierania](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Jeśli `properties.provisioningState` zasób ma wartość `Succeeded` i `properties.status` jest `Approved` , oznacza to, że udostępniony zasób link prywatny jest funkcjonalny, a indeksator można skonfigurować do komunikowania się za pośrednictwem prywatnego punktu końcowego.

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

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Krok 3. Konfigurowanie indeksatora do uruchamiania w środowisku prywatnym

> [!NOTE]
> Ten krok można wykonać przed zatwierdzeniem połączenia prywatnego punktu końcowego. Dopóki prywatne połączenie z punktem końcowym nie zostanie zatwierdzone, każdy indeksator próbujący komunikować się z bezpiecznym zasobem (na przykład konto magazynu) zakończy się niepowodzeniem w stanie przejściowym. Nie można utworzyć nowych indeksatorów. Gdy tylko zostanie zatwierdzone połączenie prywatnego punktu końcowego, indeksatory mogą uzyskać dostęp do prywatnego konta magazynu.

1. [Utwórz źródło danych](/rest/api/searchservice/create-data-source) wskazujące konto bezpiecznego magazynu i odpowiedni kontener na koncie magazynu. Poniższy zrzut ekranu przedstawia to żądanie w programie Poster.

   ![Zrzut ekranu przedstawiający tworzenie źródła danych w interfejsie użytkownika programu Poster.](media\search-indexer-howto-secure-access\create-ds.png )

1. Podobnie należy [utworzyć indeks](/rest/api/searchservice/create-index) i opcjonalnie [utworzyć zestawu umiejętności](/rest/api/searchservice/create-skillset) za pomocą interfejsu API REST.

1. [Utwórz indeksator](/rest/api/searchservice/create-indexer) wskazujący źródło danych, indeks i zestawu umiejętności, które zostały utworzone w poprzednim kroku. Ponadto należy wymusić uruchomienie indeksatora w prywatnym środowisku wykonywania przez ustawienie `executionEnvironment` właściwości konfiguracja indeksatora na `private` .

   ![Zrzut ekranu przedstawiający tworzenie indeksatora w interfejsie użytkownika programu Poster.](media\search-indexer-howto-secure-access\create-idr.png)

   Po pomyślnym utworzeniu indeksatora powinien zacząć indeksować zawartość z konta magazynu za pośrednictwem połączenia prywatnego punktu końcowego. Stan indeksatora można monitorować przy użyciu [interfejsu API stanu indeksatora](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Jeśli masz już istniejące indeksatory, możesz je zaktualizować za pomocą [interfejsu API Put](/rest/api/searchservice/create-indexer) , ustawiając wartość `executionEnvironment` na `private` .

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Jeśli utworzenie indeksatora kończy się niepowodzeniem z komunikatem o błędzie, takim jak "poświadczenia źródła danych są nieprawidłowe", oznacza to, że stan połączenia prywatnego punktu końcowego nie jest jeszcze *zatwierdzony* lub połączenie nie działa. Aby rozwiązać ten problem: 
  * Uzyskaj stan udostępnionego zasobu linku prywatnego przy użyciu [interfejsu API pobierania](/rest/api/searchmanagement/sharedprivatelinkresources/get). Jeśli stan ma wartość *zatwierdzone*, sprawdź `properties.provisioningState` zasób. Jeśli w tym miejscu znajduje się stan `Incomplete` , oznacza to, że nie można skonfigurować niektórych podstawowych zależności dla zasobu. `PUT`Ponowna próba utworzenia zasobu udostępnionego linku prywatnego powinna rozwiązać ten problem. Konieczne może być zatwierdzenie. Sprawdź ponownie stan zasobu, aby sprawdzić, czy problem został rozwiązany.

- Jeśli utworzysz indeksator bez ustawienia jego `executionEnvironment` właściwości, utworzenie może się powieść, ale jego historia wykonywania będzie wskazywać, że uruchomienie indeksatora nie powiedzie się. Aby rozwiązać ten problem:
   * [Zaktualizuj indeksator,](/rest/api/searchservice/update-indexer) aby określić środowisko wykonywania.

- Jeśli indeksator został utworzony bez ustawienia `executionEnvironment` właściwości i zostanie uruchomiony pomyślnie, oznacza to, że usługa Azure wyszukiwanie poznawcze zdecydowała się, że środowisko wykonawcze jest środowiskiem *prywatnym* specyficznym dla usługi wyszukiwania. Może to zmienić, w zależności od zasobów używanych przez indeksatora, obciążenia usługi wyszukiwania i innych czynników, które mogą działać później. Aby rozwiązać ten problem:
  * Zdecydowanie zalecamy ustawienie właściwości na, aby `executionEnvironment` `private` upewnić się, że nie będzie działać w przyszłości.

[Przydziały i limity](search-limits-quotas-capacity.md) określają, ile udostępnionych zasobów linków prywatnych można utworzyć i zależeć od jednostki SKU usługi wyszukiwania.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o prywatnych punktach końcowych:

- [Co to są prywatne punkty końcowe?](../private-link/private-endpoint-overview.md)
- [Konfiguracje DNS, które są zbędne dla prywatnych punktów końcowych](../private-link/private-endpoint-dns.md)
