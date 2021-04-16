---
title: Jak używać przypisanej przez system tożsamości zarządzanej do uzyskiwania dostępu do danych usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować przypisaną przez system tożsamość zarządzaną (tożsamość usługi zarządzanej) Azure Active Directory (Azure AD) w celu uzyskiwania dostępu do kluczy z Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: e4a41d508d15c3d8f41cc727776f233cc56c0817
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480940"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Używanie tożsamości zarządzanych przypisanych przez system do uzyskiwania dostępu Azure Cosmos DB danych
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule skonfigurujesz *niezawodne,* niezależne od rotacji kluczy rozwiązanie do uzyskiwania dostępu do kluczy Azure Cosmos DB przy użyciu [tożsamości zarządzanych.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) W przykładzie w tym artykule użyto Azure Functions, ale możesz użyć dowolnej usługi, która obsługuje tożsamości zarządzane. 

Dowiesz się, jak utworzyć aplikację funkcji, która może uzyskać dostęp do Azure Cosmos DB danych bez konieczności kopiowania jakichkolwiek kluczy Azure Cosmos DB aplikacji. Aplikacja funkcji będzie wznawiać się co minutę i będzie rejestrować bieżącą temperaturę zbiorniku z bulwarem. Aby dowiedzieć się, jak skonfigurować aplikację funkcji wyzwalaną przez czasomierz, zobacz artykuł Create a function in Azure that [is triggered by a timer](../azure-functions/functions-create-scheduled-function.md) (Tworzenie funkcji na platformie Azure wyzwalane przez czasomierz).

Aby uprościć ten scenariusz, ustawienie [Czas do transmisji](./time-to-live.md) na żywo jest już skonfigurowane do czyszczenia starszych dokumentów dotyczących temperatury. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Przypisywanie tożsamości zarządzanej przypisanej przez system do aplikacji funkcji

W tym kroku przypiszesz przypisaną przez system tożsamość zarządzaną do aplikacji funkcji.

1. W [witrynie Azure Portal](https://portal.azure.com/)otwórz okienko **Funkcji platformy Azure** i przejdź do aplikacji funkcji. 

1. Otwórz **kartę Tożsamość funkcji**  >   platformy: 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Zrzut ekranu przedstawiający opcje Funkcje platformy i Tożsamość dla aplikacji funkcji.":::

1. Na karcie **Tożsamość** **włącz stan** tożsamości systemu i **wybierz** pozycję **Zapisz.** Okienko **Tożsamość** powinno wyglądać następująco:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Zrzut ekranu przedstawiający stan tożsamości systemu ustawiony na Wł.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Udzielanie dostępu do konta usługi Azure Cosmos

W tym kroku przypiszesz rolę do przypisanej przez system tożsamości zarządzanej aplikacji funkcji. Azure Cosmos DB ma wiele wbudowanych ról, które można przypisać do tożsamości zarządzanej. W przypadku tego rozwiązania użyjesz następujących dwóch ról:

|Rola wbudowana  |Opis  |
|---------|---------|
|[Współautor konta bazy danych DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać Azure Cosmos DB kontami. Umożliwia pobieranie kluczy odczytu/zapisu. |
|[Cosmos DB roli czytelnika konta](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać Azure Cosmos DB konta. Umożliwia pobieranie kluczy odczytu. |

> [!IMPORTANT]
> Obsługa kontroli dostępu opartej na rolach w programie Azure Cosmos DB dotyczy tylko operacji płaszczyzny sterowania. Operacje płaszczyzny danych są zabezpieczane za pomocą kluczy podstawowych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz [artykuł Bezpieczny dostęp do](secure-access-to-data.md) danych.

> [!TIP] 
> Podczas przypisywania ról przypisz tylko wymagany dostęp. Jeśli usługa wymaga tylko odczytu danych, przypisz rolę **czytelnika konta Cosmos DB do** tożsamości zarządzanej. Aby uzyskać więcej informacji na temat znaczenia dostępu z najmniejszymi uprawnieniami, zobacz artykuł Lower exposure of privileged accounts (Mniejsze [narażenie uprzywilejowanych kont).](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

W tym scenariuszu aplikacja funkcji będzie odczytywać temperaturę urządzenia a następnie zapisywać te dane z powrotem w kontenerze w Azure Cosmos DB. Ponieważ aplikacja funkcji musi zapisywać dane, musisz przypisać rolę **Współautor konta usługi DocumentDB.** 

### <a name="assign-the-role-using-azure-portal"></a>Przypisywanie roli przy użyciu Azure Portal

1. Zaloguj się do Azure Portal i przejdź do swojego Azure Cosmos DB konta. Otwórz okienko **Kontrola dostępu (IAM),** a następnie **kartę Przypisania** ról:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Zrzut ekranu przedstawiający okienko Kontrola dostępu i kartę Przypisania ról.":::

1. Wybierz pozycję **+ Dodaj** > **Dodaj przypisanie roli**.

1. Po **prawej stronie zostanie** otwarty panel Dodawanie przypisania roli:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Zrzut ekranu przedstawiający okienko Dodawanie przypisania roli.":::

   * **Rola:** wybierz **pozycję Współautor konta bazy danych DocumentDB**
   * **Przypisz dostęp do**: w podsekcji Wybierz tożsamość zarządzaną **przypisaną** przez system wybierz pozycję **Aplikacja funkcji.**
   * **Wybierz** pozycję : Okienko zostanie wypełnione wszystkimi aplikacjami funkcji w subskrypcji, które mają zarządzaną **tożsamość systemu.** W takim przypadku wybierz aplikację **funkcji DotankTemperatureService:** 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Zrzut ekranu przedstawiający okienko Dodawanie przypisania roli wypełnione przykładami.":::

1. Po wybraniu aplikacji funkcji wybierz pozycję **Zapisz**.

### <a name="assign-the-role-using-azure-cli"></a>Przypisywanie roli przy użyciu interfejsu wiersza polecenia platformy Azure

Aby przypisać rolę przy użyciu interfejsu wiersza polecenia platformy Azure, otwórz Azure Cloud Shell i uruchom następujące polecenia:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programowe uzyskiwanie dostępu do Azure Cosmos DB kluczy

Teraz mamy aplikację funkcji, która ma przypisaną przez system tożsamość zarządzaną z rolą Współautor konta usługi **DocumentDB** w uprawnieniach Azure Cosmos DB usługi. Poniższy kod aplikacji funkcji pobierze klucze Azure Cosmos DB, utworzy obiekt CosmosClient, pobierze temperaturę owody, a następnie zapisze go w Azure Cosmos DB.

W tym przykładzie użyto [interfejsu API kluczy listy,](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) aby uzyskać dostęp do Azure Cosmos DB kont.

> [!IMPORTANT] 
> Jeśli chcesz przypisać [rolę czytelnika konta Cosmos DB,](#grant-access-to-your-azure-cosmos-account) musisz użyć interfejsu API list [kluczy tylko do odczytu.](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys) Spowoduje to wypełnienie tylko kluczy tylko do odczytu.

Interfejs API kluczy listy zwraca `DatabaseAccountListKeysResult` obiekt . Ten typ nie jest zdefiniowany w bibliotekach języka C#. Poniższy kod przedstawia implementację tej klasy:  

```csharp 
namespace Monitor 
{
  public class DatabaseAccountListKeysResult
  {
      public string primaryMasterKey {get;set;}
      public string primaryReadonlyMasterKey {get; set;}
      public string secondaryMasterKey {get; set;}
      public string secondaryReadonlyMasterKey {get;set;}
  }
}
```

W przykładzie użyto również prostego dokumentu o nazwie "TemperatureRecord", który jest zdefiniowany w następujący sposób:

```csharp
using System;

namespace Monitor
{
    public class TemperatureRecord
    {
        public string id { get; set; } = Guid.NewGuid().ToString();
        public DateTime RecordTime { get; set; }
        public int Temperature { get; set; }

    }
}
```

Do uzyskania przypisanego przez system tokenu tożsamości zarządzanej użyjesz biblioteki [Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) Aby poznać inne sposoby uzyskania tokenu i uzyskać więcej informacji na temat biblioteki, zobacz artykuł `Microsoft.Azure.Service.AppAuthentication` [Service-to-service authentication](/dotnet/api/overview/azure/service-to-service-authentication) (Uwierzytelnianie między usługami).


```csharp
using System;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Threading.Tasks;
using Microsoft.Azure.Cosmos;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;

namespace Monitor
{
    public static class FishTankTemperatureService
    {
        private static string subscriptionId =
        "<azure subscription id>";
        private static string resourceGroupName =
        "<name of your azure resource group>";
        private static string accountName =
        "<Azure Cosmos DB account name>";
        private static string cosmosDbEndpoint =
        "<Azure Cosmos DB endpoint>";
        private static string databaseName =
        "<Azure Cosmos DB name>";
        private static string containerName =
        "<container to store the temperature in>";

        [FunctionName("FishTankTemperatureService")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // Authenticate to the Azure Resource Manager to get the Service Managed token.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // Setup the List Keys API to get the Azure Cosmos DB keys.
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // Setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the result back as a DatabaseAccountListKeysResult.
            DatabaseAccountListKeysResult keys = await result.Content.ReadAsAsync<DatabaseAccountListKeysResult>();

            log.LogInformation("Starting to create the client");

            CosmosClient client = new CosmosClient(cosmosDbEndpoint, keys.primaryMasterKey);

            log.LogInformation("Client created");

            var database = client.GetDatabase(databaseName);
            var container = database.GetContainer(containerName);

            log.LogInformation("Get the temperature.");

            var tempRecord = new TemperatureRecord() { RecordTime = DateTime.UtcNow, Temperature = GetTemperature() };

            log.LogInformation("Store temperature");

            await container.CreateItemAsync<TemperatureRecord>(tempRecord);

            log.LogInformation($"Ending temperature monitor: {DateTime.Now}");
        }

        private static int GetTemperature()
        {
            // Fake the temperature sensor for this demo.
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Teraz możesz wdrożyć [aplikację funkcji.](../azure-functions/create-first-function-vs-code-csharp.md)

## <a name="next-steps"></a>Następne kroki

* [Uwierzytelnianie oparte na certyfikatach za pomocą Azure Cosmos DB i Azure Active Directory](certificate-based-authentication.md)
* [Zabezpieczanie kluczy Azure Cosmos DB przy użyciu Azure Key Vault](access-secrets-from-keyvault.md)
* [Punkt odniesienia zabezpieczeń dla Azure Cosmos DB](security-baseline.md)
