---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 258908ed1118b0463e8c824cd8c699fb460dfff2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "90947659"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/dotnet/).
- Najnowsza wersja [programu .NET Core Client Library](https://dotnet.microsoft.com/download/dotnet-core) dla danego systemu operacyjnego.
- Pobierz najnowszą wersję [biblioteki klienta tożsamości platformy .NET](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet).
- Pobierz najnowszą wersję [biblioteki klienta zarządzania .NET](../../concepts/sdk-options.md).

## <a name="installing-the-client-library"></a>Instalowanie biblioteki klienckiej

Najpierw Uwzględnij bibliotekę klienta zarządzania usługami komunikacyjnymi w projekcie w języku C#:

```csharp
using Azure.ResourceManager.Communication;
```

## <a name="subscription-id"></a>Identyfikator subskrypcji

Musisz znać Identyfikator subskrypcji platformy Azure. Można to uzyskać z portalu:

1.  Zaloguj się do konta platformy Azure
2.  Wybierz subskrypcje na lewym pasku bocznym
3.  Wybierz wymaganą subskrypcję
4.  Kliknij pozycję przegląd
5.  Wybierz identyfikator subskrypcji

W tym przewodniku szybki start założono, że identyfikator subskrypcji jest przechowywany w zmiennej środowiskowej o nazwie `AZURE_SUBSCRIPTION_ID` .

## <a name="authentication"></a>Authentication

Aby komunikować się z usługami Azure Communications Services, należy najpierw uwierzytelnić się na platformie Azure. Zwykle jest to możliwe przy użyciu tożsamości głównej usługi.

### <a name="option-1-managed-identity"></a>Opcja 1: tożsamość zarządzana

Jeśli Twój kod jest uruchomiony jako usługa na platformie Azure, najprostszym sposobem uwierzytelniania jest uzyskanie zarządzanej tożsamości z platformy Azure. Dowiedz się więcej o [tożsamościach zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

[Usługi platformy Azure, które obsługują tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

[Jak używać tożsamości zarządzanych do App Service i Azure Functions](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)

#### <a name="system-assigned-managed-identity"></a>[Tożsamość zarządzana przypisana przez system](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-system-assigned-identity)

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var acsClient = new CommunicationManagementClient(subscriptionId, new ManagedIdentityCredential());
```

#### <a name="user-assigned-managed-identity"></a>[Tożsamość zarządzana przypisana przez użytkownika](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet#add-a-user-assigned-identity)

ClientId zarządzanej tożsamości musi być przekazywać `ManagedIdentityCredential` jawnie.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = "AZURE_SUBSCRIPTION_ID";
var managedIdentityCredential = new ManagedIdentityCredential("AZURE_CLIENT_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, managedIdentityCredential);
```

### <a name="option-2-service-principal"></a>Opcja 2: Nazwa główna usługi

Zamiast korzystać z tożsamości zarządzanej, można uwierzytelnić się na platformie Azure przy użyciu jednostki usługi, którą zarządzasz samodzielnie. Dowiedz się więcej, korzystając z dokumentacji dotyczącej [tworzenia nazwy głównej usługi w Azure Active Directory i zarządzania nią](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Po utworzeniu nazwy głównej usługi należy zebrać następujące informacje na jej temat z Azure Portal:

- **Identyfikator klienta**
- **Klucz tajny klienta**
- **Identyfikator dzierżawy**

Przechowuj te wartości w zmiennych środowiskowych o nazwach `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` i `AZURE_TENANT_ID` odpowiednio. Następnie można utworzyć klienta zarządzania usługami komunikacyjnymi w następujący sposób:

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var acsClient = new CommunicationManagementClient(subscriptionId, new EnvironmentCredential());
```

### <a name="option-3-user-identity"></a>Opcja 3: tożsamość użytkownika

Jeśli chcesz wywołać platformę Azure w imieniu użytkownika interaktywnego, zamiast korzystać z tożsamości usługi, możesz użyć poniższego kodu, aby utworzyć klienta zarządzania usług Azure Communications Services. Spowoduje to otwarcie okna przeglądarki w celu wyświetlenia monitu o podanie konta MSA lub poświadczeń usługi Azure AD.

```csharp
using Azure.Identity;
using Azure.ResourceManager.Communication;
using Azure.ResourceManager.Communication.Models;
using System;
...
var subscriptionId = Environment.GetEnvironmentVariable("AZURE_SUBSCRIPTION_ID");
var communicationServiceClient = new CommunicationManagementClient(subscriptionId, new InteractiveBrowserCredential());
```

## <a name="managing-communication-services-resources"></a>Zarządzanie zasobami usług komunikacyjnych

### <a name="interacting-with-azure-resources"></a>Korzystanie z zasobów platformy Azure

Po uwierzytelnieniu możesz użyć swojego klienta zarządzania do wykonywania wywołań interfejsu API.

Dla każdego z poniższych przykładów przypiszemy nasze zasoby usług komunikacyjnych do istniejącej grupy zasobów.

Jeśli musisz utworzyć grupę zasobów, możesz to zrobić za pomocą [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-portal) lub [Azure Resource Manager biblioteki klienta](https://github.com/Azure/azure-sdk-for-net/blob/master/doc/mgmt_preview_quickstart.md).

### <a name="create-and-manage-a-communication-services-resource"></a>Tworzenie zasobu usług komunikacyjnych i zarządzanie nim

Nasze wystąpienie klienta biblioteki klienta zarządzania usługami komunikacyjnymi ( ``Azure.ResourceManager.Communication.CommunicationManagementClient`` ) może służyć do wykonywania operacji na zasobach usług komunikacyjnych.

#### <a name="create-a-communication-services-resource"></a>Tworzenie zasobu usług Communication Services

Podczas tworzenia zasobu usług komunikacyjnych należy określić nazwę grupy zasobów i nazwę zasobu. Należy pamiętać, że `Location` Właściwość zawsze będzie `global` , a w publicznej wersji zapoznawczej `DataLocation` wartość musi być `UnitedStates` .

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates"  };
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="update-a-communication-services-resource"></a>Aktualizowanie zasobu usług komunikacyjnych

```csharp
...
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var resource = new CommunicationServiceResource { Location = "Global", DataLocation = "UnitedStates" };
resource.Tags.Add("environment","test");
resource.Tags.Add("department","tech");
// Use existing resource name and new resource object
var operation = await acsClient.CommunicationService.StartCreateOrUpdateAsync(resourceGroupName, resourceName, resource);
await operation.WaitForCompletionAsync();
```

#### <a name="list-all-communication-services-resources"></a>Wyświetl listę wszystkich zasobów usług komunikacyjnych

```csharp
var resources = acsClient.CommunicationService.ListBySubscription();
foreach (var resource in resources)
{
    Console.WriteLine(resource.Name);
}
```

#### <a name="delete-a-communication-services-resource"></a>Usuwanie zasobu usług komunikacyjnych

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
await acsClient.CommunicationService.StartDeleteAsync(resourceGroupName, resourceName);
```

## <a name="managing-keys-and-connection-strings"></a>Zarządzanie kluczami i ciągami połączeń

Każdy zasób usług komunikacyjnych ma parę kluczy dostępu i odpowiadające im parametry połączenia. Dostęp do tych kluczy można uzyskać za pomocą biblioteki klienta zarządzania, a następnie użyć innych bibliotek klienckich usług komunikacyjnych w celu samodzielnego uwierzytelnienia w usłudze Azure Communications Services.

#### <a name="get-access-keys-for-a-communication-services-resource"></a>Pobieranie kluczy dostępu dla zasobu usług komunikacyjnych

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keys = await acsClient.CommunicationService.ListKeysAsync(resourceGroupName, resourceName);

Console.WriteLine(keys.Value.PrimaryConnectionString);
Console.WriteLine(keys.Value.SecondaryConnectionString);
```

#### <a name="regenerate-an-access-key-for-a-communication-services-resource"></a>Wygeneruj ponownie klucz dostępu dla zasobu usług komunikacyjnych

```csharp
var resourceGroupName = "myResourceGroupName";
var resourceName = "myResource";
var keyParams = new RegenerateKeyParameters { KeyType = KeyType.Primary };
var keys = await acsClient.CommunicationService.RegenerateKeyAsync(resourceGroupName, resourceName, keyParams);

Console.WriteLine(keys.Value.PrimaryKey);
```
