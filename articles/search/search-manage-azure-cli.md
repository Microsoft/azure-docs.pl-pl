---
title: Skrypty interfejsu wiersza polecenia platformy Azure używające polecenia AZ Search module
titleSuffix: Azure Cognitive Search
description: Utwórz i skonfiguruj usługę Wyszukiwanie poznawcze platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Można skalować usługę w górę lub w dół, zarządzać administratorami i interfejsami API-Keys oraz wysyłać zapytania o informacje o systemie.
manager: luisca
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: bb132baf3e93242c1b228f0ac7f0cfb7f41e1164
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680373"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Zarządzanie usługą Wyszukiwanie poznawcze platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs wiersza polecenia platformy Azure](search-manage-azure-cli.md)
> * [Interfejs API REST](/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Aby utworzyć i skonfigurować usługę Azure Wyszukiwanie poznawcze, można uruchomić polecenia i skrypty interfejsu wiersza poleceń platformy Azure w systemie Windows, macOS, Linux lub w [Azure Cloud Shell](../cloud-shell/overview.md) . Moduł [**AZ Search**](/cli/azure/search) rozszerza [interfejs wiersza polecenia platformy Azure](/cli/) z pełną parzystością do [interfejsów API REST zarządzania wyszukiwaniem](/rest/api/searchmanagement) i umożliwia wykonywanie następujących zadań:

> [!div class="checklist"]
> * [Wyświetlanie listy usług wyszukiwania w ramach subskrypcji](#list-search-services)
> * [Informacje o usłudze zwracanej](#get-search-service-information)
> * [Tworzenie lub usuwanie usługi](#create-or-delete-a-service)
> * [Tworzenie usługi przy użyciu prywatnego punktu końcowego](#create-a-service-with-a-private-endpoint)
> * [Ponowne generowanie kluczy interfejsu API administratora](#regenerate-admin-keys)
> * [Utwórz lub Usuń zapytanie API-Keys](#create-or-delete-query-keys)
> * [Skalowanie w górę lub w dół przy użyciu replik i partycji](#scale-replicas-and-partitions)
> * [Tworzenie zasobu udostępnionego linku prywatnego](#create-a-shared-private-link-resource)

Czasami pytania są zadawane o zadaniach, których *nie* ma na powyższej liście. Obecnie nie można używać modułu **AZ Search** ani interfejsu API REST zarządzania w celu zmiany nazwy serwera, regionu lub warstwy. Dedykowane zasoby są przydzielane podczas tworzenia usługi. W związku z tym zmiana podstawowego sprzętu (lokalizacji lub typu węzła) wymaga nowej usługi. Podobnie nie ma narzędzi ani interfejsów API do przesyłania zawartości, takich jak indeks, z jednej usługi do innej.

W ramach usługi Tworzenie zawartości i zarządzanie nią odbywa się za [Search Service pomocą interfejsu API REST](/rest/api/searchservice/) lub [zestawu .NET SDK](/dotnet/api/overview/azure/search.documents-readme). Chociaż nie ma żadnych dedykowanych poleceń programu PowerShell dla zawartości, możesz pisać skrypty, które wywołują interfejsy API REST lub .NET, aby tworzyć i ładować indeksy.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-upgrade"></a>Sprawdź wersje i Uaktualnij

Przykłady w tym artykule są interaktywne i wymagają podniesionych uprawnień. Należy zainstalować interfejs wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Teraz możesz uruchomić interfejs wiersza polecenia platformy Azure za pomocą `az` polecenia z poziomu wiersza poleceń systemu Windows, programu PowerShell lub [Azure Cloud Shell](../cloud-shell/overview). Program PowerShell oferuje pewne funkcje uzupełniania po naciśnięciu klawisza Tab niedostępne w wierszu polecenia systemu Windows. 

### <a name="check-the-azure-cli-version"></a>Sprawdź wersję interfejsu wiersza polecenia platformy Azure

Jeśli nie masz pewności, czy jest zainstalowany interfejs wiersza polecenia platformy Azure, uruchom następujące polecenie w ramach kroku weryfikacji. 

```azurecli-interactive
az --version
```
Jeśli to polecenie nie działa, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , aby zainstalować interfejs wiersza polecenia platformy Azure.

Jeśli masz wersję 2.11.0 lub nowszą, możesz uruchomić polecenie, `az upgrade` Aby zaktualizować interfejs wiersza polecenia do najnowszej wersji.

```azurecli-interactive
az upgrade
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Nawiązywanie połączenia z platformą Azure za pomocą tokenu logowania w przeglądarce

Aby nawiązać połączenie z subskrypcją w interfejsie wiersza polecenia platformy Azure, można użyć poświadczeń logowania portalu. Alternatywnie można [uwierzytelnić się w sposób nieinteraktywny za pomocą nazwy głównej usługi](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal).

```azurecli-interactive
az login
```

Jeśli przechowujesz wiele subskrypcji platformy Azure, ustaw subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, Uruchom to polecenie.

```azurecli-interactive
az account list --output table
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa subskrypcji to `ContosoSubscription` .

```azurecli-interactive
az account set --subscription "ContosoSubscription"
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Wyświetlanie listy usług w ramach subskrypcji

Następujące polecenia pochodzą z [**AZ Resource**](/cli/azure/resource), zwracając informacje o istniejących zasobach i usługach, które zostały już zainicjowane w ramach subskrypcji. Jeśli nie wiesz, ile usług wyszukiwania zostało już utworzonych, te polecenia zwracają te informacje, co umożliwia zapisanie podróży do portalu.

Pierwsze polecenie zwraca wszystkie usługi wyszukiwania.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

Z listy usług Zwróć informacje o określonym zasobie.

```azurecli-interactive
az resource list --name <service-name>
```

## <a name="list-all-az-search-commands"></a>Wyświetl wszystkie polecenia AZ Search

Można wyświetlić informacje dotyczące podgrup i poleceń dostępnych w [**AZ Search**](/cli/azure/search) z poziomu interfejsu wiersza polecenia. Alternatywnie możesz zapoznać się z [dokumentacją](/cli/azure/search).

Aby wyświetlić podgrupy dostępne w programie `az search` , uruchom następujące polecenie.

```azurecli-interactive
az search --help
```

Odpowiedź powinna wyglądać podobnie do poniższego wyniku.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

W każdej podgrupie dostępne są wiele poleceń. Dostępne polecenia dla `service` podgrupy można wyświetlić, uruchamiając Poniższy wiersz.

```azurecli-interactive
az search service --help
```

Można także zobaczyć argumenty dostępne dla danego polecenia.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Pobierz informacje o usłudze wyszukiwania

Jeśli znasz grupę zasobów zawierającą usługę wyszukiwania, uruchom polecenie [**AZ Search Service show**](/cli/azure/search/service#az_search_service_show) , aby zwrócić definicję usługi, w tym nazwę, region, warstwę oraz liczbę replik i partycji.

```azurecli-interactive
az search service show --name <service-name> --resource-group <resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Tworzenie lub usuwanie usługi

Aby [utworzyć nową usługę wyszukiwania](search-create-service-portal), użyj polecenia [**AZ Search Service Create**](/cli/azure/search/service#az_search_service_show) .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Wyniki powinny wyglądać podobnie do następujących danych wyjściowych:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Tworzenie usługi z regułami adresów IP

W zależności od wymagań dotyczących zabezpieczeń można utworzyć usługę wyszukiwania z [skonfigurowaną zaporą IP](service-configure-firewall.md). W tym celu należy przekazać adresy Public IP (v4) lub zakresy CIDR do `ip-rules` argumentu, jak pokazano poniżej. Reguły powinny być oddzielone przecinkami ( `,` ) lub średnikami ( `;` ).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Tworzenie usługi przy użyciu tożsamości zarządzanej przypisanej przez system

W niektórych przypadkach, na przykład w przypadku [używania tożsamości zarządzanej do nawiązywania połączenia ze źródłem danych](search-howto-managed-identities-storage.md), należy włączyć [tożsamość zarządzaną przypisane przez system](../active-directory/managed-identities-azure-resources/overview.md). W tym celu należy dodać `--identity-type SystemAssigned` polecenie.

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Tworzenie usługi przy użyciu prywatnego punktu końcowego

[Prywatne punkty końcowe](../private-link/private-endpoint-overview.md) dla systemu Azure wyszukiwanie poznawcze umożliwiają klientowi w sieci wirtualnej bezpieczne uzyskiwanie dostępu do danych w indeksie wyszukiwania za pośrednictwem [prywatnego linku](../private-link/private-link-overview.md). Prywatny punkt końcowy używa adresu IP z [przestrzeni adresowej sieci wirtualnej](../virtual-network/private-ip-addresses.md) dla usługi wyszukiwania. Ruch sieciowy między klientem a usługą wyszukiwania odbywa się za pośrednictwem sieci wirtualnej i łączy prywatnych w sieci szkieletowej firmy Microsoft, eliminując ekspozycję z publicznego Internetu. Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [tworzenia prywatnego punktu końcowego dla usługi Azure wyszukiwanie poznawcze](service-create-private-endpoint.md)

Poniższy przykład pokazuje, jak utworzyć usługę wyszukiwania za pomocą prywatnego punktu końcowego.

Najpierw Wdróż usługę wyszukiwania z `PublicNetworkAccess` ustawioną opcją `Disabled` .

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

Następnie Utwórz sieć wirtualną i prywatny punkt końcowy.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <resource-group-name> \
    --name <service-name> \
    --query [id]' \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Na koniec Utwórz prywatną strefę DNS. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Aby uzyskać więcej informacji na temat tworzenia prywatnych punktów końcowych w programie PowerShell, zobacz ten [prywatny link — szybki start](https://docs.microsoft.com/azure/private-link/create-private-endpoint-cli)

### <a name="manage-private-endpoint-connections"></a>Zarządzanie połączeniami prywatnego punktu końcowego

Oprócz tworzenia połączenia z prywatnym punktem końcowym można również `show` `update` `delete` nawiązać połączenie.

Aby pobrać połączenie prywatnego punktu końcowego i wyświetlić jego stan, użyj polecenie [**AZ Search Private-Endpoint-Connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Aby zaktualizować połączenie, użyj polecenie [**AZ Search Private-Endpoint-Connection Update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). Poniższy przykład ustawia połączenie prywatnego punktu końcowego z odrzucony:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Aby usunąć połączenie prywatnego punktu końcowego, użyj polecenie [**AZ Search Private-Endpoint-Connection Delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

Aby przerzucić [klucze interfejsu API](search-security-api-keys.md)administratora, użyj [**AZ Search admin-Key Renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Dwa klucze administratora są tworzone przy użyciu każdej usługi dla dostępu uwierzytelnionego. Klucze są wymagane dla każdego żądania. Oba klucze administratora są funkcjonalnie równoważne, dając pełny dostęp do zapisu do usługi wyszukiwania z możliwością pobierania dowolnych informacji lub tworzenia i usuwania dowolnych obiektów. Istnieją dwa klucze, aby można było użyć jednego z nich, zastępując pozostałe. 

Możesz ponownie wygenerować tylko jeden raz, jako `primary` `secondary` klucz lub. W przypadku niezakłóconej usługi Pamiętaj, aby zaktualizować cały kod klienta, aby użyć klucza pomocniczego podczas wycofywania klucza podstawowego. Unikaj zmiany kluczy, gdy operacje są w locie.

W zależności od tego, czy klucze zostaną ponownie wygenerowane bez aktualizowania kodu klienta, żądania przy użyciu starego klucza zakończą się niepowodzeniem. Ponowne wygenerowanie wszystkich nowych kluczy nie powoduje trwałego zablokowania Twojej usługi i nadal można uzyskać dostęp do usługi za pomocą portalu. Po ponownym wygenerowaniu kluczy podstawowych i pomocniczych można zaktualizować kod klienta, aby używał nowych kluczy, a operacje zostaną odpowiednio wznowione.

Wartości kluczy interfejsu API są generowane przez usługę. Nie można podać klucza niestandardowego do użycia w usłudze Azure Wyszukiwanie poznawcze. Podobnie nie istnieje zdefiniowana przez użytkownika nazwa kluczy API-Keys. Odwołania do klucza są stałymi ciągami, albo `primary` lub `secondary` . 

```azurecli-interactive
az search admin-key renew \
    --resource-group <resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych. Oba klucze są zwracane, nawet jeśli można je zmienić pojedynczo.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Tworzenie lub usuwanie kluczy zapytania

Aby utworzyć [klucze interfejsu API](search-security-api-keys.md) zapytania dla dostępu tylko do odczytu z aplikacji klienckich do indeksu wyszukiwanie poznawcze platformy Azure, użyj polecenie [**AZ Search Query-Key Create**](/cli/azure/search/query-key#az_search_query_key_create). Klucze zapytań są używane do uwierzytelniania w określonym indeksie na potrzeby pobierania wyników wyszukiwania. Klucze zapytań nie zapewniają dostępu tylko do odczytu do innych elementów w usłudze, takich jak indeks, źródło danych lub indeksator.

Nie można podać klucza dla usługi Azure Wyszukiwanie poznawcze do użycia. Klucze interfejsu API są generowane przez usługę.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Skalowanie replik i partycji

Aby [zwiększyć lub zmniejszyć repliki i partycje,](search-capacity-planning.md) Użyj [**AZ Search Service Update**](/cli/azure/search/service#az_search_service_update). Powiększanie replik lub partycji powoduje dodanie do rachunku, w którym naliczane są opłaty stałe i zmienne. Jeśli istnieje tymczasowa potrzeba dodatkowej mocy obliczeniowej, można zwiększyć liczbę replik i partycji do obsługi obciążenia. Obszar monitorowania na stronie portalu przeglądu zawiera kafelki dotyczące opóźnień zapytań, zapytań na sekundę i ograniczania, wskazujących, czy bieżąca pojemność jest odpowiednia.

Dodanie lub usunięcie odzyskania może chwilę potrwać. Zmiany pojemności są wykonywane w tle, dzięki czemu istniejące obciążenia będą kontynuowane. Dodatkowa pojemność jest używana w przypadku żądań przychodzących, gdy tylko jest gotowa, bez konieczności dodatkowej konfiguracji. 

Usunięcie pojemności może być kłopotliwe. Zaleca się zatrzymywanie wszystkich zadań indeksowania i indeksatora przed zmniejszeniem pojemności, aby uniknąć porzuconych żądań. Jeśli to nie jest możliwe, można rozważyć zmniejszenie wydajności przyrostowo jednej repliki i partycji w danym momencie do momentu osiągnięcia nowych poziomów docelowych.

Gdy wyślesz polecenie, nie ma możliwości jego przerwania w odróżnieniu od. Musisz poczekać, aż polecenie zostanie zakończone przed skorygowaniem liczby.

```azurecli-interactive
az search service update \
    --name <service-name> \
    --resource-group <resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Oprócz aktualizowania liczby replik i partycji można także aktualizować `ip-rules` , `public-access` i `identity-type` .

## <a name="create-a-shared-private-link-resource"></a>Tworzenie zasobu udostępnionego linku prywatnego

Prywatne punkty końcowe zabezpieczonych zasobów, które są tworzone za pomocą interfejsów API usługi Azure Wyszukiwanie poznawcze, są określane jako *udostępnione zasoby linków prywatnych*. Wynika to z faktu, że masz dostęp do zasobu, takiego jak konto magazynu, który został zintegrowany z [usługą link prywatny platformy Azure](https://azure.microsoft.com/services/private-link/).

Jeśli używasz indeksatora do indeksowania danych w usłudze Azure Wyszukiwanie poznawcze, a Twoje źródło danych znajduje się w sieci prywatnej, możesz utworzyć wychodzące połączenie z [punktem końcowym](../private-link/private-endpoint-overview.md) w celu uzyskania dostępu do danych.

Pełna lista zasobów platformy Azure, dla których można utworzyć wychodzące prywatne punkty końcowe z usługi Azure Wyszukiwanie poznawcze, można znaleźć [tutaj](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis) wraz z wartościami identyfikatorów powiązanej **grupy** .

Aby utworzyć zasób udostępnionego linku prywatnego, użyj polecenie [**AZ Search Shared-Private-link-Resource Create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Należy pamiętać, że przed uruchomieniem tego polecenia może być wymagana pewna konfiguracja źródła danych.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Aby pobrać udostępnione zasoby linku prywatnego i wyświetlić ich stan, użyj polecenie [**AZ Search Shared-Private-link-Resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Musisz zatwierdzić połączenie przy użyciu poniższego polecenia, aby można było go użyć. Identyfikator prywatnego połączenia z punktem końcowym musi zostać pobrany z zasobu podrzędnego. W tym przypadku pobieramy identyfikator połączenia z AZ Storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Aby usunąć zasób udostępnionego linku prywatnego, użyj polecenie [**AZ Search Shared-Private-link-Resource Delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <resource-group-name> 
```

Aby uzyskać szczegółowe informacje dotyczące konfigurowania udostępnionych zasobów linków prywatnych, zobacz dokumentację dotyczącą [tworzenia połączeń indeksatora za pomocą prywatnego punktu końcowego](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Następne kroki

Tworzenie [indeksu](search-what-is-an-index.md), [wykonywanie zapytań względem indeksu](search-query-overview.md) przy użyciu portalu, interfejsów API REST lub zestawu .NET SDK.

* [Utwórz indeks Wyszukiwanie poznawcze platformy Azure w Azure Portal](search-get-started-portal.md)
* [Konfigurowanie indeksatora w celu załadowania danych z innych usług](search-indexer-overview.md)
* [Tworzenie zapytań względem indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Eksploratora wyszukiwania w Azure Portal](search-explorer.md)
* [Jak korzystać z usługi Azure Wyszukiwanie poznawcze w środowisku .NET](search-howto-dotnet-sdk.md)
