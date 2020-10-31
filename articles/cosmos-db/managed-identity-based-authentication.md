---
title: Jak używać przypisanej przez system tożsamości zarządzanej do uzyskiwania dostępu do danych usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować tożsamość zarządzaną przez system Azure Active Directory (tożsamość usługi zarządzanej) w celu uzyskania dostępu do kluczy z Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 918033f736a28534cd36a4637b41d0a6b3b4cdc7
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93088577"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Korzystanie z zarządzanych tożsamości przypisanych do systemu w celu uzyskiwania dostępu do danych Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

W tym artykule opisano, jak skonfigurować *niezawodne, kluczowe rozwiązanie niezależny od* do uzyskiwania dostępu do kluczy Azure Cosmos DB za pomocą [zarządzanych tożsamości](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). W przykładzie w tym artykule użyto Azure Functions, ale można użyć dowolnej usługi, która obsługuje zarządzane tożsamości. 

Dowiesz się, jak utworzyć aplikację funkcji, która może uzyskiwać dostęp do Azure Cosmos DB danych bez konieczności kopiowania kluczy Azure Cosmos DB. Aplikacja funkcji zostanie wznowiona co minutę i rejestruje bieżącą temperaturę zbiornika Aquarium ryby. Aby dowiedzieć się, jak skonfigurować aplikację funkcji wyzwalanej przez czasomierz, zobacz artykuł [Tworzenie funkcji na platformie Azure, która jest wyzwalana przez czasomierz](../azure-functions/functions-create-scheduled-function.md) .

Aby uprościć scenariusz, ustawienie [czasu wygaśnięcia](./time-to-live.md) jest już skonfigurowane do czyszczenia starszych dokumentów temperatury. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Przypisywanie tożsamości zarządzanej przypisanej przez system do aplikacji funkcji

W tym kroku przypiszesz tożsamość zarządzaną przypisaną przez system do aplikacji funkcji.

1. W [Azure Portal](https://portal.azure.com/)Otwórz okienko **funkcji platformy Azure** i przejdź do aplikacji funkcji. 

1. Otwórz kartę **tożsamość funkcji platformy**  >  **Identity** : 

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-selection.png" alt-text="Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.":::

1. Na karcie **tożsamość** **Włącz pozycję** **stan** tożsamości systemu i wybierz pozycję **Zapisz** . Okienko **tożsamość** powinno wyglądać następująco:  

   :::image type="content" source="./media/managed-identity-based-authentication/identity-tab-system-managed-on.png" alt-text="Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.":::

## <a name="grant-access-to-your-azure-cosmos-account"></a>Udzielanie dostępu do konta usługi Azure Cosmos

W tym kroku przypiszesz rolę do tożsamości zarządzanej przypisanej do systemu przez aplikację funkcji. Azure Cosmos DB ma wiele wbudowanych ról, które można przypisać do tożsamości zarządzanej. W przypadku tego rozwiązania zostaną użyte następujące dwie role:

|Wbudowana rola  |Opis  |
|---------|---------|
|[Współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać kontami Azure Cosmos DB. Umożliwia pobieranie kluczy do odczytu i zapisu. |
|[Rola czytnika konta Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać Azure Cosmos DB dane konta. Umożliwia pobieranie kluczy odczytu. |

> [!IMPORTANT]
> Obsługa kontroli dostępu opartej na rolach w Azure Cosmos DB ma zastosowanie tylko do operacji na płaszczyźnie kontroli. Operacje płaszczyzny danych są zabezpieczane za poorednictwem kluczy podstawowych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz artykuł [bezpieczny dostęp do danych](secure-access-to-data.md) .

> [!TIP] 
> Podczas przypisywania ról należy przypisać tylko wymagany dostęp. Jeśli usługa wymaga tylko odczytywania danych, przypisz rolę **czytnika konta Cosmos DB** do tożsamości zarządzanej. Aby uzyskać więcej informacji na temat ważności najmniejszego poziomu uprawnień dostępu, zobacz [dolne narażenie na konta uprzywilejowane](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) .

W tym scenariuszu aplikacja funkcji odczyta temperaturę Aquarium, a następnie zapisze te dane w kontenerze w Azure Cosmos DB. Ponieważ aplikacja funkcji musi zapisywać dane, należy przypisać rolę **współautor konta DocumentDB** . 

### <a name="assign-the-role-using-azure-portal"></a>Przypisz rolę przy użyciu Azure Portal

1. Zaloguj się do Azure Portal i przejdź do swojego konta Azure Cosmos DB. Otwórz okienko **Kontrola dostępu (IAM)** , a następnie kartę **przypisania ról** :

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab.png" alt-text="Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.":::

1. Wybierz pozycję **+ Dodaj** > **Dodaj przypisanie roli** .

1. Zostanie otwarty panel **Dodawanie przypisania roli** z prawej strony:

   :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png" alt-text="Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.":::

   * **Rola** : Wybierz **współautor konta DocumentDB**
   * **Przypisz dostęp do** : w podsekcji **Wybieranie tożsamości zarządzanej przez system** wybierz pozycję **aplikacja funkcji** .
   * **Wybierz** : okienko zostanie wypełnione wszystkimi aplikacjami funkcji w subskrypcji, które mają **tożsamość systemu zarządzanego** . W takim przypadku wybierz aplikację funkcji **FishTankTemperatureService** : 

      :::image type="content" source="./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png" alt-text="Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.":::

1. Po wybraniu aplikacji funkcji wybierz pozycję **Zapisz** .

### <a name="assign-the-role-using-azure-cli"></a>Przypisywanie roli przy użyciu interfejsu wiersza polecenia platformy Azure

Aby przypisać rolę za pomocą interfejsu wiersza polecenia platformy Azure, Otwórz Azure Cloud Shell i uruchom następujące polecenia:

```azurecli-interactive

scope=$(az cosmosdb show --name '<Your_Azure_Cosmos_account_name>' --resource-group '<CosmosDB_Resource_Group>' --query id)

principalId=$(az webapp identity show -n '<Your_Azure_Function_name>' -g '<Azure_Function_Resource_Group>' --query principalId)

az role assignment create --assignee $principalId --role "DocumentDB Account Contributor" --scope $scope
```

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programowe uzyskiwanie dostępu do kluczy Azure Cosmos DB

Teraz mamy aplikację funkcji, która ma tożsamość zarządzaną przypisaną przez system z rolą **współautor konta DocumentDB** w uprawnieniach Azure Cosmos DB. Poniższy kod aplikacji funkcji pobierze klucze Azure Cosmos DB, utworzy obiekt CosmosClient, pobierze temperaturę Aquarium, a następnie zapisze go w Azure Cosmos DB.

Ten przykład używa [interfejsu API kluczy list](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listkeys) , aby uzyskać dostęp do kluczy konta Azure Cosmos DB.

> [!IMPORTANT] 
> Jeśli chcesz przypisać rolę [czytnika konta Cosmos DB](#grant-access-to-your-azure-cosmos-account) , musisz użyć [interfejsu API tylko do odczytu listy kluczy](/rest/api/cosmos-db-resource-provider/2020-04-01/databaseaccounts/listreadonlykeys). Spowoduje to wypełnienie tylko kluczy tylko do odczytu.

Interfejs API kluczy list zwraca `DatabaseAccountListKeysResult` obiekt. Ten typ nie jest zdefiniowany w bibliotekach języka C#. Poniższy kod przedstawia implementację tej klasy:  

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

W przykładzie używany jest również prosty dokument o nazwie "TemperatureRecord", który jest zdefiniowany w następujący sposób:

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

Użyjesz biblioteki [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) , aby uzyskać token zarządzanej tożsamości przypisany do systemu. Aby poznać inne sposoby uzyskiwania tokenu i dowiedzieć się więcej o `Microsoft.Azure.Service.AppAuthentication` bibliotece, zobacz artykuł dotyczący [uwierzytelniania między usługami](../key-vault/general/service-to-service-authentication.md) .


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

Teraz możesz przystąpić do [wdrażania aplikacji funkcji](../azure-functions/functions-create-first-function-vs-code.md).

## <a name="next-steps"></a>Następne kroki

* [Uwierzytelnianie oparte na certyfikatach z użyciem Azure Cosmos DB i Azure Active Directory](certificate-based-authentication.md)
* [Zabezpiecz klucze Azure Cosmos DB przy użyciu Azure Key Vault](access-secrets-from-keyvault.md)
* [Linia bazowa zabezpieczeń dla Azure Cosmos DB](security-baseline.md)
