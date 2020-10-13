---
title: Automatyczne tworzenie kopii zapasowych wartości kluczy z magazynów konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak skonfigurować automatyczne tworzenie kopii zapasowych kluczy między magazynami konfiguracji aplikacji.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-dotnet
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: a3c1699dd4b7b828c7dc652f14f431878f785061
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207146"
---
# <a name="back-up-app-configuration-stores-automatically"></a>Automatyczne tworzenie kopii zapasowych magazynów konfiguracji aplikacji

W tym artykule dowiesz się, jak skonfigurować automatyczne tworzenie kopii zapasowych kluczowych wartości z podstawowego magazynu konfiguracji aplikacji platformy Azure w magazynie pomocniczym. Automatyczne kopie zapasowe wykorzystują integrację Azure Event Grid z konfiguracją aplikacji. 

Po skonfigurowaniu automatycznego tworzenia kopii zapasowej konfiguracja aplikacji będzie publikować zdarzenia do Azure Event Grid dla wszelkich zmian wprowadzonych w kluczowych wartościach w magazynie konfiguracji. Event Grid obsługuje wiele usług platformy Azure, z których użytkownicy mogą subskrybować zdarzenia emitowane przy każdym tworzeniu, aktualizowaniu lub usunięciu wartości kluczy.

## <a name="overview"></a>Omówienie

W tym artykule przedstawiono użycie usługi Azure queue storage do odbierania zdarzeń z Event Grid i używania wyzwalacza czasomierza Azure Functions do przetwarzania zdarzeń w kolejce w partiach. 

Gdy funkcja jest wyzwalana na podstawie zdarzeń, pobierze najnowsze wartości kluczy, które uległy zmianie z podstawowego magazynu konfiguracji aplikacji, i odpowiednio zaktualizuj magazyn pomocniczy. Ta konfiguracja umożliwia łączenie wielu zmian, które wystąpiły w krótkim czasie w jednej operacji tworzenia kopii zapasowej, co pozwala uniknąć nadmiernego żądania do magazynów konfiguracji aplikacji.

![Diagram przedstawiający architekturę kopii zapasowej magazynu konfiguracji aplikacji.](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Aprowizowanie zasobów

Uzasadnienie tworzenia kopii zapasowych magazynów konfiguracji aplikacji polega na użyciu wielu magazynów konfiguracji w różnych regionach platformy Azure w celu zwiększenia odporności aplikacji. Aby to osiągnąć, magazyny główne i pomocnicze powinny znajdować się w różnych regionach świadczenia usługi Azure. Wszystkie inne zasoby utworzone w tym samouczku mogą być obsługiwane w dowolnym wybranym regionie. Wynika to z faktu, że jeśli region podstawowy nie działa, nie będzie żadnych nowych kopii zapasowych do momentu ponownego dostępu do regionu podstawowego.

W tym samouczku utworzysz magazyn pomocniczy w `centralus` regionie i wszystkie inne zasoby w `westus` regionie.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/). 
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem programowania na platformie Azure.
- [Zestaw .NET Core SDK](https://dotnet.microsoft.com/download).
- Najnowsza wersja interfejsu wiersza polecenia platformy Azure (2.3.1 lub nowszy). Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure, musisz najpierw zalogować się przy użyciu programu `az login` . Opcjonalnie możesz użyć Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to kolekcja logiczna przeznaczona do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group).

Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie `<resource_group_name>` w lokalizacji `westus`.  Zamień `<resource_group_name>` na unikatową nazwę grupy zasobów.

```azurecli-interactive
resourceGroupName="<resource_group_name>"
az group create --name $resourceGroupName --location westus
```

## <a name="create-app-configuration-stores"></a>Tworzenie magazynów konfiguracji aplikacji

Utwórz podstawowe i pomocnicze magazyny konfiguracji aplikacji w różnych regionach.
Zamień  `<primary_appconfig_name>` i `<secondary_appconfig_name>` z unikatowymi nazwami dla magazynów konfiguracji. Nazwa każdego magazynu musi być unikatowa, ponieważ jest używana jako nazwa DNS.

```azurecli-interactive
primaryAppConfigName="<primary_appconfig_name>"
secondaryAppConfigName="<secondary_appconfig_name>"
az appconfig create \
  --name $primaryAppConfigName \
  --location westus \
  --resource-group $resourceGroupName\
  --sku standard

az appconfig create \
  --name $secondaryAppConfigName \
  --location centralus \
  --resource-group $resourceGroupName\
  --sku standard
```

## <a name="create-a-queue"></a>Tworzenie kolejki

Utwórz konto magazynu i kolejkę na potrzeby otrzymywania zdarzeń opublikowanych przez Event Grid.

```azurecli-interactive
storageName="<unique_storage_name>"
queueName="<queue_name>"
az storage account create -n $storageName -g $resourceGroupName -l westus --sku Standard_LRS
az storage queue create --name $queueName --account-name $storageName --auth-mode login
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="subscribe-to-your-app-configuration-store-events"></a>Subskrybowanie zdarzeń ze sklepu z konfiguracją aplikacji

Subskrybujesz te dwa zdarzenia z podstawowego magazynu konfiguracji aplikacji:

- `Microsoft.AppConfiguration.KeyValueModified`
- `Microsoft.AppConfiguration.KeyValueDeleted`

Następujące polecenie tworzy subskrypcję Event Grid dla dwóch zdarzeń wysyłanych do kolejki. Typ punktu końcowego jest ustawiony na `storagequeue` , a punkt końcowy jest ustawiony na identyfikator kolejki. Zamień na `<event_subscription_name>` wybraną nazwę subskrypcji zdarzenia.

```azurecli-interactive
storageId=$(az storage account show --name $storageName --resource-group  $resourceGroupName --query id --output tsv)
queueId="$storageId/queueservices/default/queues/$queueName"
appconfigId=$(az appconfig show --name $primaryAppConfigName --resource-group $resourceGroupName --query id --output tsv)
eventSubscriptionName="<event_subscription_name>"
az eventgrid event-subscription create \
  --source-resource-id $appconfigId \
  --name $eventSubscriptionName \
  --endpoint-type storagequeue \
  --endpoint $queueId \
  --included-event-types Microsoft.AppConfiguration.KeyValueModified Microsoft.AppConfiguration.KeyValueDeleted 
```

## <a name="create-functions-for-handling-events-from-queue-storage"></a>Tworzenie funkcji obsługi zdarzeń z magazynu kolejek

### <a name="set-up-with-ready-to-use-functions"></a>Konfigurowanie za pomocą gotowych do użycia funkcji

W tym artykule opisano, jak korzystać z funkcji języka C#, które mają następujące właściwości:
- Stos środowiska uruchomieniowego .NET Core 3,1
- Azure Functions środowiska uruchomieniowego w wersji 3. x
- Funkcja wyzwalana przez czasomierz co 10 minut

Aby ułatwić rozpoczęcie tworzenia kopii zapasowych danych, [Przetestowano i opublikowano funkcję](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , której można użyć bez wprowadzania żadnych zmian w kodzie. Pobierz pliki projektu i [Opublikuj je w swojej aplikacji funkcji platformy Azure z programu Visual Studio](/azure/azure-functions/functions-develop-vs#publish-to-azure).

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian w zmiennych środowiskowych w pobranym kodzie. W następnej sekcji utworzysz wymagane ustawienia aplikacji.
>

### <a name="build-your-own-function"></a>Tworzenie własnej funkcji

Jeśli przykładowy kod podany wcześniej nie spełnia wymagań użytkownika, można również utworzyć własną funkcję. Aby można było ukończyć tworzenie kopii zapasowej, funkcja musi mieć możliwość wykonania następujących zadań:
- Okresowo Odczytuj zawartość kolejki, aby sprawdzić, czy zawiera ona powiadomienia z Event Grid. Szczegóły implementacji można znaleźć w [zestawie SDK kolejki magazynu](/azure/storage/queues/storage-quickstart-queues-dotnet) .
- Jeśli kolejka zawiera [powiadomienia o zdarzeniach z Event Grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), Wyodrębnij wszystkie unikatowe `<key, label>` informacje z komunikatów o zdarzeniach. Kombinacja klucza i etykiety jest unikatowym identyfikatorem dla zmian wartości kluczy w magazynie podstawowym.
- Odczytaj wszystkie ustawienia z magazynu głównego. Zaktualizuj tylko te ustawienia w magazynie pomocniczym, które mają odpowiednie zdarzenie w kolejce. Usuń wszystkie ustawienia z magazynu pomocniczego, które znajdowały się w kolejce, ale nie w magazynie podstawowym. [Zestawu SDK aplikacji](https://github.com/Azure/AppConfiguration#sdks) można użyć do programistycznego uzyskiwania dostępu do magazynów konfiguracji.
- Usuwanie komunikatów z kolejki w przypadku braku wyjątków podczas przetwarzania.
- Zaimplementuj obsługę błędów zgodnie z potrzebami. Zapoznaj się z poprzednim przykładem kodu, aby zobaczyć typowe wyjątki, które mogą być obsługiwane.

Aby dowiedzieć się więcej o tworzeniu funkcji, zobacz: [Tworzenie funkcji na platformie Azure wyzwalanej przez czasomierz](/azure/azure-functions/functions-create-scheduled-function) i [opracowywanie Azure Functions przy użyciu programu Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Skorzystaj z najlepszej oceny, aby wybrać harmonogram czasomierza w zależności od tego, jak często wprowadzasz zmiany w podstawowym magazynie konfiguracji. Uruchamianie funkcji zbyt często może spowodować ograniczenie żądań dotyczących magazynu.
>


## <a name="create-function-app-settings"></a>Tworzenie ustawień aplikacji funkcji

Jeśli używasz funkcji dostarczonej przez nas, potrzebujesz następujących ustawień aplikacji w aplikacji funkcji:
- `PrimaryStoreEndpoint`: Punkt końcowy magazynu konfiguracji aplikacji podstawowej. Może to być na przykład `https://{primary_appconfig_name}.azconfig.io`.
- `SecondaryStoreEndpoint`: Punkt końcowy magazynu konfiguracji aplikacji pomocniczych. Może to być na przykład `https://{secondary_appconfig_name}.azconfig.io`.
- `StorageQueueUri`: Identyfikator URI kolejki. Może to być na przykład `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`.

Następujące polecenie tworzy wymagane ustawienia aplikacji w aplikacji funkcji. Zamień `<function_app_name>` na nazwę aplikacji funkcji.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-the-function-app"></a>Udzielanie dostępu do zarządzanej tożsamości aplikacji funkcji

Użyj poniższego polecenia lub [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) , aby dodać tożsamość zarządzaną przypisaną przez system do aplikacji funkcji.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Aby można było wykonać wymagane tworzenie zasobów i zarządzanie rolami, Twoje konto musi mieć `Owner` uprawnienia w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy z przypisaniem roli, Dowiedz się, [jak dodać lub usunąć przypisania roli platformy Azure przy użyciu Azure Portal](/azure/role-based-access-control/role-assignments-portal).

Użyj następujących poleceń lub [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) , aby przyznać zarządzaną tożsamość aplikacji funkcji dostęp do Twoich magazynów konfiguracji aplikacji. Użyj tych ról:
- Przypisz `App Configuration Data Reader` rolę w podstawowym magazynie konfiguracji aplikacji.
- Przypisz `App Configuration Data Owner` rolę w magazynie konfiguracji aplikacji pomocniczej.

```azurecli-interactive
functionPrincipalId=$(az functionapp identity show --name $functionAppName --resource-group  $resourceGroupName --query principalId --output tsv)
primaryAppConfigId=$(az appconfig show -n $primaryAppConfigName --query id --output tsv)
secondaryAppConfigId=$(az appconfig show -n $secondaryAppConfigName --query id --output tsv)

az role assignment create \
    --role "App Configuration Data Reader" \
    --assignee $functionPrincipalId \
    --scope $primaryAppConfigId

az role assignment create \
    --role "App Configuration Data Owner" \
    --assignee $functionPrincipalId \
    --scope $secondaryAppConfigId
```

Użyj poniższego polecenia lub [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-azure-roles-using-the-azure-portal) , aby przyznać zarządzaną tożsamość aplikacji funkcji dostęp do kolejki. Przypisz `Storage Queue Data Contributor` rolę do kolejki.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Wyzwalanie zdarzenia konfiguracji aplikacji

Aby sprawdzić, czy wszystko działa, możesz utworzyć, zaktualizować lub usunąć klucz-wartość z magazynu podstawowego. Ta zmiana powinna być automatycznie widoczna w magazynie pomocniczym kilka sekund po Azure Functions wyzwalaczy czasomierza.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Zdarzenie zostało wyzwolone. W ciągu kilku chwil Event Grid wyśle powiadomienie o zdarzeniu do kolejki. *Po następnym zaplanowanym uruchomieniu funkcji*Wyświetl ustawienia konfiguracji w magazynie pomocniczym, aby sprawdzić, czy zawiera ona zaktualizowaną wartość klucza z magazynu podstawowego.

> [!NOTE]
> Funkcję można [wyzwolić ręcznie](/azure/azure-functions/functions-manually-run-non-http) podczas testowania i rozwiązywania problemów bez oczekiwania na zaplanowaną czasomierz-wyzwalacz.

Po upewnieniu się, że funkcja tworzenia kopii zapasowej została uruchomiona pomyślnie, można zobaczyć, że klucz znajduje się w magazynie pomocniczym.

```azurecli-interactive
az appconfig kv show --name $secondaryAppConfigName --key Foo
```

```json
{
  "contentType": null,
  "etag": "eVgJugUUuopXnbCxg0dB63PDEJY",
  "key": "Foo",
  "label": null,
  "lastModified": "2020-04-27T23:25:08+00:00",
  "locked": false,
  "tags": {},
  "value": "Bar"
}
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli nowe ustawienie nie jest widoczne w magazynie pomocniczym:

- Upewnij się, że funkcja tworzenia kopii zapasowej została wyzwolona *po* utworzeniu ustawienia w magazynie podstawowym.
- Istnieje możliwość, że Event Grid nie mogła wysłać powiadomienia o zdarzeniu do kolejki w czasie. Sprawdź, czy kolejka nadal zawiera powiadomienie o zdarzeniu z magazynu podstawowego. Jeśli tak, wyzwól ponownie funkcję tworzenia kopii zapasowej.
- Sprawdź [dzienniki Azure Functions](/azure/azure-functions/functions-create-scheduled-function#test-the-function) pod kątem błędów lub ostrzeżeń.
- Użyj [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) , aby upewnić się, że aplikacja funkcji platformy Azure zawiera poprawne wartości ustawień aplikacji, które Azure Functions próbować odczytać.
- Możesz również skonfigurować monitorowanie i alerty dla Azure Functions przy użyciu [usługi Azure Application Insights](/azure/azure-functions/functions-monitoring?tabs=cmd). 


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli planujesz kontynuować pracę z tą konfiguracją aplikacji i subskrypcją zdarzeń, nie czyść zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuować pracy, użyj następującego polecenia, aby usunąć zasoby utworzone w tym artykule.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak skonfigurować automatyczne tworzenie kopii zapasowych kluczowych wartości, Dowiedz się więcej o tym, jak zwiększyć geograficzną aplikację:

- [Odporność i odzyskiwanie po awarii](concept-disaster-recovery.md)
