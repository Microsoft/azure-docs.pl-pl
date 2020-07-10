---
title: Automatycznie Utwórz kopię zapasową wartości klucza z magazynu konfiguracji aplikacji platformy Azure
description: Dowiedz się, jak skonfigurować automatyczne tworzenie kopii zapasowej kluczowych wartości między magazynami konfiguracji aplikacji
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 04/27/2020
ms.author: avgupta
ms.openlocfilehash: 6dd485adb71bf05be6499f2fc18572e8a28357d7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86209904"
---
# <a name="backup-app-configuration-stores-automatically"></a>Automatyczne tworzenie kopii zapasowych magazynów konfiguracji aplikacji

W tym artykule dowiesz się, jak skonfigurować automatyczne tworzenie kopii zapasowej kluczowych wartości z magazynu konfiguracji aplikacji głównej do magazynu pomocniczego. Wykorzystuje ona integrację Azure Event Grid z konfiguracją aplikacji. Po skonfigurowaniu usługi Konfiguracja aplikacji będzie publikować zdarzenia do Event Grid dla wszelkich zmian wprowadzonych w kluczowych wartościach w magazynie konfiguracji. Event Grid obsługuje wiele usług platformy Azure, z których użytkownicy mogą subskrybować zdarzenia emitowane przy każdym tworzeniu, aktualizowaniu lub usunięciu wartości kluczy.

## <a name="overview"></a>Omówienie

W tym samouczku użyjesz kolejki usługi Azure Storage do odbierania zdarzeń z Event Grid i korzystania z wyzwalacza czasomierza Azure Functions, aby przetwarzać zdarzenia w kolejce magazynu w partiach. Po wyzwoleniu na podstawie zdarzeń funkcja pobierze najnowsze wartości kluczy, które uległy zmianie z podstawowego magazynu konfiguracji aplikacji, i odpowiednio zaktualizuje magazyn pomocniczy. Ta konfiguracja pomaga połączyć wiele zmian w krótkim czasie w jednej operacji tworzenia kopii zapasowej i uniknąć nadmiernych żądań w magazynach konfiguracji aplikacji.

![Architektura tworzenia kopii zapasowych magazynu konfiguracji aplikacji](./media/config-store-backup-architecture.png)

## <a name="resource-provisioning"></a>Aprowizowanie zasobów

Uzasadnienie tworzenia kopii zapasowych magazynów konfiguracji aplikacji polega na użyciu wielu magazynów konfiguracji w różnych regionach platformy Azure w celu zwiększenia odporności aplikacji. Aby to osiągnąć, magazyny główne i pomocnicze powinny znajdować się w różnych regionach świadczenia usługi Azure. Wszystkie inne zasoby utworzone w tym samouczku mogą być obsługiwane w dowolnym wybranym regionie. Wynika to z faktu, że jeśli region podstawowy nie działa, nie będzie żadnych nowych kopii zapasowych, dopóki region podstawowy będzie dostępny ponownie.

W tym samouczku utworzysz magazyn pomocniczy w `centralus` regionie i wszystkie inne zasoby w `westus` regionie.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/). Opcjonalnie możesz użyć Azure Cloud Shell.
- [Program Visual Studio 2019](https://visualstudio.microsoft.com/vs) z obciążeniem programowania na platformie Azure.
- Pobierz i zainstaluj [zestaw .NET Core SDK](https://dotnet.microsoft.com/download).
- Najnowsza wersja interfejsu wiersza polecenia platformy Azure (2.3.1 lub nowsza). Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia platformy Azure, musisz najpierw zalogować się przy użyciu `az login` . Opcjonalnie możesz użyć Azure Cloud Shell.

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
Zamień  `<primary_appconfig_name>` i `<secondary_appconfig_name>` z unikatowymi nazwami dla magazynów konfiguracji. Nazwa magazynu musi być unikatowa, ponieważ jest używana jako nazwa DNS.

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

## <a name="create-azure-storage-queue"></a>Tworzenie kolejki usługi Azure Storage

Utwórz konto magazynu i kolejkę magazynu na potrzeby otrzymywania zdarzeń opublikowanych przez Event Grid.

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

Następujące polecenie tworzy subskrypcję Event Grid dla dwóch zdarzeń wysyłanych do kolejki magazynu, gdzie typ punktu końcowego jest ustawiony na, `storagequeue` a punkt końcowy jest ustawiony na identyfikator kolejki. Zamień na `<event_subscription_name>` wybraną nazwę subskrypcji zdarzenia.

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

## <a name="create-azure-functions-for-handling-events-from-storage-queue"></a>Utwórz Azure Functions do obsługi zdarzeń z kolejki magazynu

### <a name="setup-with-ready-to-use-azure-functions"></a>Instalator z gotowym do użycia Azure Functions

W tym samouczku będziesz pracować z Azure Functions C# z następującymi właściwościami:
- Stos środowiska uruchomieniowego .NET Core 3,1
- Azure Functions środowiska uruchomieniowego w wersji 3. x
- Funkcja wyzwalana przez czasomierz co 10 minut

Aby ułatwić rozpoczęcie tworzenia kopii zapasowych danych, Przetestowano i opublikowano [Azure Functions](https://github.com/Azure/AppConfiguration/tree/master/examples/ConfigurationStoreBackup) , których można użyć bez wprowadzania żadnych zmian w kodzie. Pobierz pliki projektu i [Opublikuj je na własnym aplikacja funkcji platformy Azure z programu Visual Studio.](/azure/azure-functions/functions-develop-vs#publish-to-azure)

> [!IMPORTANT]
> Nie wprowadzaj żadnych zmian w zmiennych środowiskowych w pobranym kodzie. Będziesz tworzyć wymagane ustawienia aplikacji w następnej sekcji.
>

### <a name="build-your-own-azure-functions"></a>Utwórz własne Azure Functions

Jeśli podany powyżej kod przykładowy nie spełnia wymagań użytkownika, można również utworzyć własne Azure Functions. Aby można było ukończyć tworzenie kopii zapasowej, funkcja musi mieć możliwość wykonania następujących zadań:
- Okresowo Odczytuj zawartość kolejki magazynu, aby sprawdzić, czy zawiera ona powiadomienia z Event Grid. Szczegóły implementacji można znaleźć w [zestawie SDK kolejki magazynu](/azure/storage/queues/storage-quickstart-queues-dotnet) .
- Jeśli kolejka magazynu zawiera [powiadomienia o zdarzeniach z Event Grid](/azure/azure-app-configuration/concept-app-configuration-event?branch=pr-en-us-112982#event-schema), Wyodrębnij wszystkie unikatowe klucze <, etykiety> z komunikatów o zdarzeniach. Kombinacja klucza i etykiety jest unikatowym identyfikatorem dla zmian wartości kluczy w magazynie podstawowym.
- Odczytaj wszystkie ustawienia z magazynu podstawowego. Zaktualizuj tylko te ustawienia w magazynie pomocniczym, które mają odpowiednie zdarzenie w kolejce magazynu. Usuń wszystkie ustawienia z magazynu pomocniczego, które znajdowały się w kolejce magazynu, ale nie w magazynie podstawowym. Aby programowo uzyskać dostęp do swoich magazynów konfiguracji, możesz użyć [zestawu SDK aplikacji](https://github.com/Azure/AppConfiguration#sdks) .
- Usuwanie komunikatów z kolejki magazynu w przypadku braku wyjątków podczas przetwarzania.
- Upewnij się, że obsługa błędów jest zaimplementowana zgodnie z potrzebami. Możesz odwołać się do powyższego przykładu kodu, aby zobaczyć niektóre typowe wyjątki, które mogą być obsługiwane.

Aby dowiedzieć się więcej na temat tworzenia Azure Functions, zobacz: [Tworzenie funkcji na platformie Azure wyzwalanej przez czasomierz](/azure/azure-functions/functions-create-scheduled-function) i [opracowywanie Azure Functions przy użyciu programu Visual Studio](/azure/azure-functions/functions-develop-vs).


> [!IMPORTANT]
> Skorzystaj z najlepszej oceny, aby wybrać harmonogram czasomierza w zależności od tego, jak często wprowadzasz zmiany w podstawowym magazynie konfiguracji. Należy pamiętać, że uruchomienie funkcji zbyt często może spowodować przeznaczenie żądań ograniczenia dla magazynu.
>


## <a name="create-azure-function-app-settings"></a>Tworzenie ustawień usługi Azure aplikacja funkcji

Jeśli używasz Azure Functions, musisz mieć następujące ustawienia aplikacji na platformie Azure aplikacja funkcji:
- `PrimaryStoreEndpoint`: Punkt końcowy magazynu konfiguracji aplikacji podstawowej. Na przykład: `https://{primary_appconfig_name}.azconfig.io`
- `SecondaryStoreEndpoint`: Punkt końcowy magazynu konfiguracji aplikacji pomocniczych. Na przykład: `https://{secondary_appconfig_name}.azconfig.io`
- `StorageQueueUri`: Identyfikator URI kolejki magazynu. Na przykład: `https://{unique_storage_name}.queue.core.windows.net/{queue_name}`

Następujące polecenie tworzy wymagane ustawienia aplikacji w usłudze Azure aplikacja funkcji. Zamień na `<function_app_name>` nazwę aplikacja funkcji platformy Azure.

```azurecli-interactive
functionAppName="<function_app_name>"
primaryStoreEndpoint="https://$primaryAppConfigName.azconfig.io"
secondaryStoreEndpoint="https://$secondaryAppConfigName.azconfig.io"
storageQueueUri="https://$storageName.queue.core.windows.net/$queueName"
az functionapp config appsettings set --name $functionAppName --resource-group $resourceGroupName --settings StorageQueueUri=$storageQueueUri PrimaryStoreEndpoint=$primaryStoreEndpoint SecondaryStoreEndpoint=$secondaryStoreEndpoint
```


## <a name="grant-access-to-the-managed-identity-of-azure-function-app"></a>Udzielanie dostępu do zarządzanej tożsamości aplikacja funkcji platformy Azure

Użyj poniższego polecenia lub [Azure Portal](/azure/app-service/overview-managed-identity#add-a-system-assigned-identity) , aby dodać tożsamość zarządzaną przez system dla aplikacja funkcji platformy Azure.

```azurecli-interactive
az functionapp identity assign --name $functionAppName --resource-group $resourceGroupName
```

> [!NOTE]
> Aby można było wykonać wymagane tworzenie zasobów i zarządzanie rolami, Twoje konto musi mieć `'Owner'` uprawnienia w odpowiednim zakresie (subskrypcji lub grupy zasobów). Jeśli potrzebujesz pomocy z przypisaniem roli, Dowiedz się, [jak dodać lub usunąć przypisania ról platformy Azure przy użyciu Azure Portal](/azure/role-based-access-control/role-assignments-portal).

Użyj następujących poleceń lub [Azure Portal](/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity#grant-access-to-app-configuration) , aby przyznać zarządzaną tożsamość usługi Azure aplikacja funkcji dostęp do Twoich magazynów konfiguracji aplikacji.
- Przypisz `App Configuration Data Reader` rolę w podstawowym magazynie konfiguracji aplikacji.
- Przypisz `App Configuration Data Owner` rolę w magazynie konfiguracji aplikacji pomocniczych.

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

Użyj poniższego polecenia lub [Azure Portal](/azure/storage/common/storage-auth-aad-rbac-portal#assign-rbac-roles-using-the-azure-portal) , aby przyznać zarządzaną tożsamość usługi Azure aplikacja funkcji dostęp do kolejki magazynu. 
- Przypisz `Storage Queue Data Contributor` rolę w kolejce magazynu.

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee $functionPrincipalId \
    --scope $queueId
```

## <a name="trigger-an-app-configuration-event"></a>Wyzwalanie zdarzenia konfiguracji aplikacji

Aby sprawdzić, czy wszystko działa, możesz utworzyć/zaktualizować/usunąć wartość klucza z magazynu podstawowego. Ta zmiana powinna być automatycznie widoczna w magazynie pomocniczym kilka sekund po wyzwoleniu Azure Functions przez czasomierz.

```azurecli-interactive
az appconfig kv set --name $primaryAppConfigName --key Foo --value Bar --yes
```

Zdarzenie zostało wyzwolone, a w ciągu kilku chwil Event Grid wyśle powiadomienie o zdarzeniu do kolejki usługi Azure Storage. ***Po następnym zaplanowanym uruchomieniu Azure Functions***Wyświetl ustawienia konfiguracji w magazynie pomocniczym, aby sprawdzić, czy zawiera on zaktualizowaną wartość klucza z magazynu podstawowego.

> [!NOTE]
> Możesz [wyzwolić Azure Functions ręcznie](/azure/azure-functions/functions-manually-run-non-http) podczas testowania i rozwiązywania problemów bez oczekiwania na zaplanowaną czasomierz-wyzwalacz.

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

- Upewnij się, że funkcja tworzenia kopii zapasowej została wyzwolona ***po*** utworzeniu ustawienia w magazynie podstawowym.
- Możliwe, że Event Grid nie było w stanie wysłać powiadomienia o zdarzeniu do kolejki magazynu w czasie. Sprawdź, czy kolejka magazynu nadal zawiera powiadomienie o zdarzeniu z magazynu podstawowego, a jeśli tak, uruchom ponownie funkcję tworzenia kopii zapasowej.
- Sprawdź [dzienniki Azure Functions](/azure/azure-functions/functions-create-scheduled-function#test-the-function) pod kątem błędów lub ostrzeżeń.
- Użyj [Azure Portal](/azure/azure-functions/functions-how-to-use-azure-function-app-settings#get-started-in-the-azure-portal) , aby upewnić się, że usługa Azure aplikacja funkcji zawiera poprawne wartości ustawień aplikacji, które Azure Functions próbować odczytać.
- Możesz również skonfigurować monitorowanie i alerty dla Azure Functions przy użyciu [usługi Azure Application Insights.](/azure/azure-functions/functions-monitoring?tabs=cmd) 


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli planujesz kontynuować pracę z tą konfiguracją aplikacji i subskrypcją zdarzeń, nie czyść zasobów utworzonych w tym artykule. Jeśli nie planujesz kontynuowania pracy, użyj poniższego polecenia, aby usunąć zasoby utworzone w ramach tego artykułu.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak skonfigurować automatyczne tworzenie kopii zapasowych wartości kluczy, Dowiedz się więcej o tym, jak zwiększyć geograficzną aplikację:

- [Odporność i odzyskiwanie po awarii](concept-disaster-recovery.md)
