---
title: Jak uzyskać dostęp do danych usługi Azure Cosmos DB przy użyciu tożsamości zarządzanej przypisanej do systemu
description: Dowiedz się, jak skonfigurować tożsamość zarządzaną przypisaną przez usługę Azure Service Directory (Azure AD) do uzyskiwania dostępu do kluczy z usługi Azure Cosmos DB.
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 8136ad7a1fe29bc3394e959c10aafc52988c0a23
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641223"
---
# <a name="use-system-assigned-managed-identities-to-access-azure-cosmos-db-data"></a>Uzyskiwanie dostępu do danych usługi Azure Cosmos DB za pomocą tożsamości zarządzanych przypisanych do systemu

W tym artykule skonfigurujesz *niezawodne, kluczowe rozwiązanie niezależnego od rotacji,* aby uzyskać dostęp do kluczy usługi Azure Cosmos DB przy użyciu [zarządzanych tożsamości.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) W tym artykule użyto usługi Azure Functions, ale można użyć dowolnej usługi, która obsługuje tożsamości zarządzane. 

Dowiesz się, jak utworzyć aplikację funkcji, która może uzyskać dostęp do danych usługi Azure Cosmos DB bez konieczności kopiowania kluczy usługi Azure Cosmos DB. Aplikacja funkcji obudzi się co minutę i nagrać aktualną temperaturę akwariowego akwarium. Aby dowiedzieć się, jak skonfigurować aplikację funkcji wyzwalane czasomierza, zobacz [Tworzenie funkcji na platformie Azure, który jest wyzwalany przez czasomierz](../azure-functions/functions-create-scheduled-function.md) artykułu.

Aby uprościć scenariusz, ustawienie [Czas na żywo](./time-to-live.md) jest już skonfigurowane do czyszczenia starszych dokumentów temperatury. 

## <a name="assign-a-system-assigned-managed-identity-to-a-function-app"></a>Przypisywanie tożsamości zarządzanej przypisanej do systemu do aplikacji funkcji

W tym kroku zostanie przypisana tożsamość zarządzana przypisana przez system do aplikacji funkcji.

1. W [witrynie Azure portal](https://portal.azure.com/)otwórz okienko **funkcji platformy Azure** i przejdź do aplikacji funkcji. 

1. Otwórz kartę > **Tożsamość** **funkcji platformy:** 

   ![Zrzut ekranu przedstawiający funkcje platformy i opcje tożsamości dla aplikacji funkcji.](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na karcie **Tożsamość** **włącz** stan **tożsamości** systemu i wybierz pozycję **Zapisz**. Okienko **tożsamości** powinno wyglądać następująco:  

   ![Zrzut ekranu przedstawiający stan tożsamości systemu ustawiony na Włączone.](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-access-to-your-azure-cosmos-account"></a>Udzielanie dostępu do konta usługi Azure Cosmos

W tym kroku przypiszesz rolę do tożsamości zarządzanej przypisanej przez aplikację funkcji. Usługa Azure Cosmos DB ma wiele wbudowanych ról, które można przypisać do tożsamości zarządzanej. W tym rozwiązaniu użyjesz następujących dwóch ról:

|Wbudowana rola  |Opis  |
|---------|---------|
|[Współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać kontami usługi Azure Cosmos DB. Umożliwia pobieranie kluczy odczytu/zapisu. |
|[Czytnik kont usługi Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać dane konta usługi Azure Cosmos DB. Umożliwia pobieranie kluczy odczytu. |

> [!IMPORTANT]
> Obsługa kontroli dostępu opartej na rolach w usłudze Azure Cosmos DB dotyczy tylko operacji płaszczyzny sterowania. Operacje na płaszczyźnie danych są zabezpieczone za pomocą kluczy głównych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz artykuł [Bezpieczny dostęp do danych.](secure-access-to-data.md)

> [!TIP] 
> Podczas przypisywania ról należy przypisać tylko wymagany dostęp. Jeśli usługa wymaga tylko odczytu danych, przypisz rolę **czytnik kont usługi Cosmos DB** do tożsamości zarządzanej. Aby uzyskać więcej informacji na temat ważności dostępu z najmniejszymi uprawnieniami, zobacz artykuł [Niższa ekspozycja kont uprzywilejowanych.](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts)

W tym scenariuszu aplikacja funkcji odczyta temperaturę akwarium, a następnie zapisz te dane do kontenera w usłudze Azure Cosmos DB. Ponieważ aplikacja funkcji musi zapisać dane, należy przypisać **rolę współautora konta DocumentDB.** 

1. Zaloguj się do witryny Azure portal i przejdź do konta usługi Azure Cosmos DB. Otwórz okienko **kontroli dostępu (IAM),** a następnie kartę **Przypisania ról:**

   ![Zrzut ekranu przedstawiający okienko kontroli dostępu i kartę Przypisania ról.](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Wybierz **+ Dodaj** > **przypisanie roli .**

1. Po prawej stronie zostanie otwarty panel **Dodaj przypisanie roli:**

   ![Zrzut ekranu przedstawiający okienko Dodawanie przypisania roli.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Rola**: Wybierz **współautora konta DocumentDB**
   * **Przypisywanie dostępu do**: W podsekcji **Wybierz tożsamość zarządzaną przypisaną do systemu** wybierz pozycję Aplikacja **funkcji**.
   * **Wybierz**: Okienko zostanie wypełnione wszystkimi aplikacjami funkcyjnymi w ramach subskrypcji, które mają **zarządzaną tożsamość systemu**. W takim przypadku wybierz aplikację funkcji **FishTankTemperatureService:** 

      ![Zrzut ekranu przedstawiający okienko Dodawanie przypisania roli wypełnione przykładami.](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Po wybraniu aplikacji funkcji wybierz pozycję **Zapisz**.

## <a name="programmatically-access-the-azure-cosmos-db-keys"></a>Programowy dostęp do kluczy usługi Azure Cosmos DB

Teraz mamy aplikację funkcji, która ma przypisaną do systemu tożsamość zarządzaną z rolą **współautora konta DocumentDB** w uprawnieniach usługi Azure Cosmos DB. Poniższy kod aplikacji funkcji otrzyma klucze usługi Azure Cosmos DB, utworzy obiekt CosmosClient, uzyska temperaturę akwarium, a następnie zapisze to w usłudze Azure Cosmos DB.

W tym przykładzie użyto [interfejsu API kluczy listy,](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) aby uzyskać dostęp do kluczy konta usługi Azure Cosmos DB.

> [!IMPORTANT] 
> Jeśli chcesz przypisać rolę czytnika kont usługi [Cosmos DB,](#grant-access-to-your-azure-cosmos-account) musisz użyć [interfejsu API Klawisze tylko](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)do odczytu listy . Spowoduje to wypełnić tylko tylko klucze tylko do odczytu.

Interfejs API kluczy `DatabaseAccountListKeysResult` listy zwraca obiekt. Ten typ nie jest zdefiniowany w bibliotekach języka C#. Poniższy kod przedstawia implementację tej klasy:  

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

Użyjesz biblioteki [Microsoft.Azure.Services.AppAuthentication,](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) aby uzyskać token tożsamości zarządzanej przypisany przez system. Aby dowiedzieć się, inne sposoby, aby `Microsoft.Azure.Service.AppAuthentication` uzyskać token i dowiedzieć się więcej informacji na temat biblioteki, zobacz [service-to-service uwierzytelniania](../key-vault/general/service-to-service-authentication.md) artykułu.


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

* [Uwierzytelnianie oparte na certyfikatach za pomocą usługi Azure Cosmos DB i usługi Azure Active Directory](certificate-based-authentication.md)
* [Bezpieczne klucze usługi Azure Cosmos DB przy użyciu usługi Azure Key Vault](access-secrets-from-keyvault.md)
* [Linia bazowa zabezpieczeń usługi Azure Cosmos DB](security-baseline.md)
