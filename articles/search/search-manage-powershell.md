---
title: Skrypty programu PowerShell używające polecenia AZ. Search module
titleSuffix: Azure Cognitive Search
description: Utwórz i skonfiguruj usługę Azure Wyszukiwanie poznawcze przy użyciu programu PowerShell. Można skalować usługę w górę lub w dół, zarządzać administratorami i interfejsami API-Keys oraz wysyłać zapytania o informacje o systemie.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 9f189d1889f3ca3a3aa3234432452b1b3d696c04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935096"
---
# <a name="manage-your-azure-cognitive-search-service-with-powershell"></a>Zarządzanie usługą Wyszukiwanie poznawcze platformy Azure przy użyciu programu PowerShell
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [Program PowerShell](search-manage-powershell.md)
> * [Interfejs API REST](/rest/api/searchmanagement/)
> * [Zestaw SDK platformy .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

Polecenia cmdlet programu PowerShell i skrypty w systemie Windows, Linux lub [Azure Cloud Shell](../cloud-shell/overview.md) można uruchomić w celu utworzenia i skonfigurowania usługi Azure wyszukiwanie poznawcze. Moduł **AZ. Search** rozszerza [Azure PowerShell](/powershell/) z pełną parzystością do [interfejsów API REST zarządzania wyszukiwaniem](/rest/api/searchmanagement) i umożliwia wykonywanie następujących zadań:

> [!div class="checklist"]
> * [Wyświetlanie listy usług wyszukiwania w ramach subskrypcji](#list-search-services)
> * [Informacje o usłudze zwracanej](#get-search-service-information)
> * [Tworzenie lub usuwanie usługi](#create-or-delete-a-service)
> * [Ponowne generowanie kluczy interfejsu API administratora](#regenerate-admin-keys)
> * [Utwórz lub Usuń zapytanie API-Keys](#create-or-delete-query-keys)
> * [Skalowanie w górę lub w dół przy użyciu replik i partycji](#scale-replicas-and-partitions)

Czasami pytania są zadawane o zadaniach, których *nie* ma na powyższej liście. Obecnie nie można używać modułu **AZ. Search** ani interfejsu API REST zarządzania w celu zmiany nazwy serwera, regionu lub warstwy. Dedykowane zasoby są przydzielane podczas tworzenia usługi. W związku z tym zmiana podstawowego sprzętu (lokalizacji lub typu węzła) wymaga nowej usługi. Podobnie nie ma narzędzi ani interfejsów API do przesyłania zawartości, takich jak indeks, z jednej usługi do innej.

W ramach usługi Tworzenie zawartości i zarządzanie nią odbywa się za [Search Service pomocą interfejsu API REST](/rest/api/searchservice/) lub [zestawu .NET SDK](/dotnet/api/?term=microsoft.azure.search). Chociaż nie ma żadnych dedykowanych poleceń programu PowerShell dla zawartości, można napisać skrypt programu PowerShell, który wywołuje interfejsy API REST lub .NET do tworzenia i ładowania indeksów.

<a name="check-versions-and-load"></a>

## <a name="check-versions-and-load-modules"></a>Sprawdzanie wersji i modułów ładowania

Przykłady w tym artykule są interaktywne i wymagają podniesionych uprawnień. Należy zainstalować Azure PowerShell ( **AZ** module). Aby uzyskać więcej informacji, zobacz [Install Azure PowerShell](/powershell/azure/).

### <a name="powershell-version-check-51-or-later"></a>Sprawdzenie wersji programu PowerShell (5,1 lub nowszej)

Lokalny program PowerShell musi mieć 5,1 lub nowszą, na dowolnym obsługiwanym systemie operacyjnym.

```azurepowershell-interactive
$PSVersionTable.PSVersion
```

### <a name="load-azure-powershell"></a>Załaduj Azure PowerShell

Jeśli nie masz pewności, czy jest zainstalowany program **AZ** , uruchom następujące polecenie w ramach kroku weryfikacji. 

```azurepowershell-interactive
Get-InstalledModule -Name Az
```

W niektórych systemach nie są ładowane automatyczne moduły. Jeśli wystąpi błąd w poprzednim poleceniu, spróbuj załadować moduł, a jeśli to się nie powiedzie, Wróć do instrukcji instalacji, aby sprawdzić, czy pominięto krok.

```azurepowershell-interactive
Import-Module -Name Az
```

### <a name="connect-to-azure-with-a-browser-sign-in-token"></a>Nawiązywanie połączenia z platformą Azure za pomocą tokenu logowania w przeglądarce

Aby nawiązać połączenie z subskrypcją w programie PowerShell, można użyć poświadczeń logowania portalu. Alternatywnie można [uwierzytelnić się w sposób nieinteraktywny za pomocą nazwy głównej usługi](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

```azurepowershell-interactive
Connect-AzAccount
```

Jeśli przechowujesz wiele subskrypcji platformy Azure, ustaw subskrypcję platformy Azure. Aby wyświetlić listę bieżących subskrypcji, Uruchom to polecenie.

```azurepowershell-interactive
Get-AzSubscription | sort SubscriptionName | Select SubscriptionName
```

Aby określić subskrypcję, uruchom następujące polecenie. W poniższym przykładzie nazwa subskrypcji to `ContosoSubscription` .

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName ContosoSubscription
```

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Wyświetlanie listy usług w ramach subskrypcji

Następujące polecenia pochodzą z [**AZ. resources**](/powershell/module/az.resources/?view=azps-1.4.0#resources), zwracając informacje o istniejących zasobach i usługach, które zostały już zainicjowane w ramach subskrypcji. Jeśli nie wiesz, ile usług wyszukiwania zostało już utworzonych, te polecenia zwracają te informacje, co umożliwia zapisanie podróży do portalu.

Pierwsze polecenie zwraca wszystkie usługi wyszukiwania.

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Search/searchServices | ft
```

Z listy usług Zwróć informacje o określonym zasobie.

```azurepowershell-interactive
Get-AzResource -ResourceName <service-name>
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : westus
ResourceId        : /subscriptions/<alpha-numeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="import-azsearch"></a>Importuj AZ. Search

Polecenia [**AZ. Search**](/powershell/module/az.search/?view=azps-1.4.0#search) nie są dostępne do momentu załadowania modułu.

```azurepowershell-interactive
Install-Module -Name Az.Search
```

### <a name="list-all-azsearch-commands"></a>Wyświetl wszystkie polecenia AZ. Search

W ramach kroku weryfikacji zwraca listę poleceń dostępnych w module.

```azurepowershell-interactive
Get-Command -Module Az.Search
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

```
CommandType     Name                                Version    Source
-----------     ----                                -------    ------
Cmdlet          Get-AzSearchAdminKeyPair            0.7.1      Az.Search
Cmdlet          Get-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          Get-AzSearchService                 0.7.1      Az.Search
Cmdlet          New-AzSearchAdminKey                0.7.1      Az.Search
Cmdlet          New-AzSearchQueryKey                0.7.1      Az.Search
Cmdlet          New-AzSearchService                 0.7.1      Az.Search
Cmdlet          Remove-AzSearchQueryKey             0.7.1      Az.Search
Cmdlet          Remove-AzSearchService              0.7.1      Az.Search
Cmdlet          Set-AzSearchService                 0.7.1      Az.Search
```

## <a name="get-search-service-information"></a>Pobierz informacje o usłudze wyszukiwania

Po zaimportowaniu **. Wyszukiwanie** zostanie zaimportowane i znasz grupę zasobów zawierającą usługę wyszukiwania, uruchom polecenie [Get-AzSearchService](/powershell/module/az.search/get-azsearchservice?view=azps-1.4.0) , aby zwrócić definicję usługi, w tym nazwę, region, warstwę i liczbę partycji.

```azurepowershell-interactive
Get-AzSearchService -ResourceGroupName <resource-group-name>
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

```
Name              : my-demo-searchapp
ResourceGroupName : demo-westus
ResourceType      : Microsoft.Search/searchServices
Location          : West US
Sku               : Standard
ReplicaCount      : 1
PartitionCount    : 1
HostingMode       : Default
ResourceId        : /subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="create-or-delete-a-service"></a>Tworzenie lub usuwanie usługi

[**Nowy — AzSearchService**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) jest używany do [tworzenia nowej usługi wyszukiwania](search-create-service-portal.md).

```azurepowershell-interactive
New-AzSearchService -ResourceGroupName "demo-westus" -Name "my-demo-searchapp" -Sku "Standard" -Location "West US" -PartitionCount 3 -ReplicaCount 3
``` 
Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Id                : /subscriptions/<alphanumeric-subscription-ID>/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 3
PartitionCount    : 3
HostingMode       : Default
Tags
```     

## <a name="regenerate-admin-keys"></a>Wygeneruj ponownie klucze administratora

[**Nowy — AzSearchAdminKey**](/powershell/module/az.search/new-azsearchadminkey?view=azps-1.4.0) jest używany do przejęcia [kluczy interfejsu API](search-security-api-keys.md)administratora. Dwa klucze administratora są tworzone przy użyciu każdej usługi dla dostępu uwierzytelnionego. Klucze są wymagane dla każdego żądania. Oba klucze administratora są funkcjonalnie równoważne, dając pełny dostęp do zapisu do usługi wyszukiwania z możliwością pobierania dowolnych informacji lub tworzenia i usuwania dowolnych obiektów. Istnieją dwa klucze, aby można było użyć jednego z nich, zastępując pozostałe. 

Możesz ponownie wygenerować tylko jeden raz, jako `primary` `secondary` klucz lub. W przypadku niezakłóconej usługi Pamiętaj, aby zaktualizować cały kod klienta, aby użyć klucza pomocniczego podczas wycofywania klucza podstawowego. Unikaj zmiany kluczy, gdy operacje są w locie.

W zależności od tego, czy klucze zostaną ponownie wygenerowane bez aktualizowania kodu klienta, żądania przy użyciu starego klucza zakończą się niepowodzeniem. Ponowne wygenerowanie wszystkich nowych kluczy nie powoduje trwałego zablokowania Twojej usługi i nadal można uzyskać dostęp do usługi za pomocą portalu. Po ponownym wygenerowaniu kluczy podstawowych i pomocniczych można zaktualizować kod klienta, aby używał nowych kluczy, a operacje zostaną odpowiednio wznowione.

Wartości kluczy interfejsu API są generowane przez usługę. Nie można podać klucza niestandardowego do użycia w usłudze Azure Wyszukiwanie poznawcze. Podobnie nie istnieje zdefiniowana przez użytkownika nazwa kluczy API-Keys. Odwołania do klucza są stałymi ciągami, albo `primary` lub `secondary` . 

```azurepowershell-interactive
New-AzSearchAdminKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -KeyKind Primary
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych. Oba klucze są zwracane, nawet jeśli można je zmienić pojedynczo.

```
Primary                    Secondary
-------                    ---------
<alphanumeric-guid>        <alphanumeric-guid>  
```

## <a name="create-or-delete-query-keys"></a>Tworzenie lub usuwanie kluczy zapytania

[**New-AzSearchQueryKey**](/powershell/module/az.search/new-azsearchquerykey?view=azps-1.4.0) służy do tworzenia [kluczy interfejsu API](search-security-api-keys.md) zapytania dla dostępu tylko do odczytu z aplikacji klienckich do indeksu wyszukiwanie poznawcze platformy Azure. Klucze zapytań są używane do uwierzytelniania w określonym indeksie na potrzeby pobierania wyników wyszukiwania. Klucze zapytań nie zapewniają dostępu tylko do odczytu do innych elementów w usłudze, takich jak indeks, źródło danych lub indeksator.

Nie można podać klucza dla usługi Azure Wyszukiwanie poznawcze do użycia. Klucze interfejsu API są generowane przez usługę.

```azurepowershell-interactive
New-AzSearchQueryKey -ResourceGroupName <resource-group-name> -ServiceName <search-service-name> -Name <query-key-name> 
```

## <a name="scale-replicas-and-partitions"></a>Skalowanie replik i partycji

[**Set-AzSearchService**](/powershell/module/az.search/set-azsearchservice?view=azps-1.4.0) służy do [zwiększania lub zmniejszania replik i partycji](search-capacity-planning.md) w celu ponownego dopasowania zasobów rozliczanych w ramach usługi. Powiększanie replik lub partycji powoduje dodanie do rachunku, w którym naliczane są opłaty stałe i zmienne. Jeśli istnieje tymczasowa potrzeba dodatkowej mocy obliczeniowej, można zwiększyć liczbę replik i partycji do obsługi obciążenia. Obszar monitorowania na stronie portalu przeglądu zawiera kafelki dotyczące opóźnień zapytań, zapytań na sekundę i ograniczania, wskazujących, czy bieżąca pojemność jest odpowiednia.

Dodanie lub usunięcie odzyskania może chwilę potrwać. Zmiany pojemności są wykonywane w tle, dzięki czemu istniejące obciążenia będą kontynuowane. Dodatkowa pojemność jest używana w przypadku żądań przychodzących, gdy tylko jest gotowa, bez konieczności dodatkowej konfiguracji. 

Usunięcie pojemności może być kłopotliwe. Zaleca się zatrzymywanie wszystkich zadań indeksowania i indeksatora przed zmniejszeniem pojemności, aby uniknąć porzuconych żądań. Jeśli to nie jest możliwe, można rozważyć zmniejszenie wydajności przyrostowo jednej repliki i partycji w danym momencie do momentu osiągnięcia nowych poziomów docelowych.

Gdy wyślesz polecenie, nie ma możliwości jego przerwania w odróżnieniu od. Musisz poczekać, aż polecenie zostanie zakończone przed skorygowaniem liczby.

```azurepowershell-interactive
Set-AzSearchService -ResourceGroupName <resource-group-name> -Name <search-service-name> -PartitionCount 6 -ReplicaCount 6
```

Wyniki powinny wyglądać podobnie do poniższych danych wyjściowych.

```
ResourceGroupName : demo-westus
Name              : my-demo-searchapp
Location          : West US
Sku               : Standard
ReplicaCount      : 6
PartitionCount    : 6
HostingMode       : Default
Id                : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp
```

## <a name="next-steps"></a>Następne kroki

Tworzenie [indeksu](search-what-is-an-index.md), [wykonywanie zapytań względem indeksu](search-query-overview.md) przy użyciu portalu, interfejsów API REST lub zestawu .NET SDK.

* [Utwórz indeks Wyszukiwanie poznawcze platformy Azure w Azure Portal](search-get-started-portal.md)
* [Konfigurowanie indeksatora w celu załadowania danych z innych usług](search-indexer-overview.md)
* [Tworzenie zapytań względem indeksu Wyszukiwanie poznawcze platformy Azure przy użyciu Eksploratora wyszukiwania w Azure Portal](search-explorer.md)
* [Jak korzystać z usługi Azure Wyszukiwanie poznawcze w środowisku .NET](search-howto-dotnet-sdk.md)