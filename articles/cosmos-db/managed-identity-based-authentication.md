---
title: Jak uzyskać dostęp do danych usługi Azure Cosmos DB przy użyciu tożsamości zarządzanej przypisanej do systemu
description: Dowiedz się, jak skonfigurować tożsamość zarządzaną przypisaną przez system usługi Azure AD do uzyskiwania dostępu do kluczy z usługi Azure Cosmos DB. msi, tożsamość usługi zarządzanej, aad, usługa Azure Active Directory, tożsamość
author: j-patrick
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: justipat
ms.reviewer: sngun
ms.openlocfilehash: 37e5cb817db2c54a07ab04c4dcc31b1976fdf03d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450058"
---
# <a name="how-to-use-a-system-assigned-managed-identity-to-access-azure-cosmos-db-data"></a>Jak uzyskać dostęp do danych usługi Azure Cosmos DB przy użyciu tożsamości zarządzanej przypisanej do systemu

W tym artykule skonfigurujesz **niezawodne, kluczowe agnostyki,** rozwiązanie dostępu do kluczy usługi Azure Cosmos DB, wykorzystując [zarządzane tożsamości](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md). W tym artykule użyto funkcji platformy Azure. Jednak można osiągnąć to rozwiązanie przy użyciu dowolnej usługi, która obsługuje tożsamości zarządzane. 

Dowiesz się, jak utworzyć funkcję platformy Azure, która może uzyskać dostęp do usługi Azure Cosmos DB bez konieczności kopiowania kluczy usługi Azure Cosmos DB. Funkcja obudzi się co minutę i nagra bieżącą temperaturę akwariowego akwarium. Aby dowiedzieć się, jak skonfigurować czasomierz wyzwalany funkcja platformy Azure zobacz [Tworzenie funkcji na platformie Azure, która jest wyzwalana przez czasomierza](../azure-functions/functions-create-scheduled-function.md) artykułu.

Aby uprościć scenariusz, oczyszczanie starszych dokumentów temperatury jest obsługiwane przez już skonfigurowane ustawienie [Czas na żywo.](./time-to-live.md) 

## <a name="assign-a-system-assigned-managed-identity-to-an-azure-function"></a>Przypisywanie tożsamości zarządzanej przypisanej do systemu do funkcji platformy Azure

W tym kroku zostanie przypisana tożsamość zarządzana przypisana przez system do funkcji platformy Azure.

1. W [witrynie Azure portal](https://portal.azure.com/)otwórz okienko **funkcji platformy Azure** i przejdź do aplikacji funkcji. 

1. Otwórz kartę > **Tożsamość** **funkcji platformy:** 

   ![Karta Tożsamość](./media/managed-identity-based-authentication/identity-tab-selection.png)

1. Na karcie **Tożsamość** **włącz** stan **Tożsamość systemu.** Pamiętaj, aby wybrać pozycję **Zapisz**i upewnij się, że chcesz włączyć tożsamość systemu. Na końcu okienko **Tożsamość systemowa** powinno wyglądać następująco:  

   ![Tożsamość systemowa włączona](./media/managed-identity-based-authentication/identity-tab-system-managed-on.png)

## <a name="grant-the-managed-identity-access-to-your-azure-cosmos-account"></a>Udzielanie dostępu do tożsamości zarządzanej do konta usługi Azure Cosmos

W tym kroku przypiszesz rolę do tożsamości zarządzanej przypisanej przez system usługi Azure Function. Usługa Azure Cosmos DB ma wiele wbudowanych ról, które można przypisać do tożsamości zarządzanej. W tym rozwiązaniu użyjesz następujących dwóch ról:

|Wbudowana rola  |Opis  |
|---------|---------|
|[Współautor konta DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Może zarządzać kontami usługi Azure Cosmos DB. Umożliwia pobieranie kluczy odczytu/zapisu. |
|[Czytnik kont usługi Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Może odczytywać dane konta usługi Azure Cosmos DB. Umożliwia pobieranie kluczy odczytu. |

> [!IMPORTANT]
> Obsługa rbac w usłudze Azure Cosmos DB ma zastosowanie tylko do operacji płaszczyzny sterowania. Operacje na płaszczyźnie danych są zabezpieczone przy użyciu kluczy głównych lub tokenów zasobów. Aby dowiedzieć się więcej, zobacz artykuł [Bezpieczny dostęp do danych.](secure-access-to-data.md)

> [!TIP] 
> Podczas przypisywania ról przypisz tylko wymagany dostęp. Jeśli usługa wymaga tylko odczytu danych, przypisz tożsamość zarządzaną do roli **czytnika kont usługi Cosmos DB.** Aby uzyskać więcej informacji na temat ważności dostępu o najmniejszych uprawnieniach, zobacz [niższy poziom ekspozycji kont uprzywilejowanych](../security/fundamentals/identity-management-best-practices.md#lower-exposure-of-privileged-accounts) artykułu.

W danym scenariuszu odczytasz temperaturę, a następnie zapisz te dane do kontenera w usłudze Azure Cosmos DB. Ponieważ musisz zapisać dane, użyjesz roli **współautora kont DocumentDB.** 

1. Zaloguj się do witryny Azure portal i przejdź do konta usługi Azure Cosmos DB. Otwórz **okienko Zarządzanie dostępem (IAM),** a następnie kartę **Przypisania ról:**

   ![Okienko IAM](./media/managed-identity-based-authentication/cosmos-db-iam-tab.png)

1. Wybierz przycisk **+ Dodaj,** a następnie **dodaj przypisanie roli**.

1. Po prawej stronie zostanie otwarty panel **Dodaj przypisanie roli:**

   ![Dodaj rolę](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane.png)

   * **Rola** — wybierz **współautora konta DocumentDB**
   * **Przypisywanie dostępu do** — w podsekcji Wybierz **tożsamość zarządzaną przypisaną systemowi** wybierz pozycję **Aplikacja funkcji**.
   * **Wybierz** — okienko zostanie wypełnione wszystkimi aplikacjami funkcyjnymi w ramach subskrypcji, które mają **tożsamość systemu zarządzanego.** W naszym przypadku wybieram aplikację funkcji **SummaryService:** 

      ![Wybierz przydział](./media/managed-identity-based-authentication/cosmos-db-iam-tab-add-role-pane-filled.png)

1. Po wybraniu tożsamości aplikacji funkcji kliknij przycisk **Zapisz**.

## <a name="programmatically-access-the-azure-cosmos-db-keys-from-the-azure-function"></a>Programowo uzyskuj dostęp do kluczy usługi Azure Cosmos DB z funkcji platformy Azure

Teraz mamy aplikację funkcji, która ma przypisaną do systemu tożsamość zarządzaną. Ta tożsamość jest nadana roli **współautora konta documentdb** w uprawnieniach usługi Azure Cosmos DB. Poniższy kod aplikacji funkcji otrzyma klucze usługi Azure Cosmos DB, utworzy obiekt CosmosClient, uzyska temperaturę, a następnie zapisz to w usłudze Cosmos DB.

W tym przykładzie użyto [interfejsu API kluczy listy,](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListKeys) aby uzyskać dostęp do kluczy konta usługi Azure Cosmos DB.

> [!IMPORTANT] 
> Jeśli chcesz przypisać rolę [ **czytnika kont usługi Cosmos DB,** ](#grant-the-managed-identity-access-to-your-azure-cosmos-account) musisz użyć [interfejsu api Klawisze listy](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/DatabaseAccounts/ListReadOnlyKeys)tylko do odczytu . Spowoduje to wypełnić tylko klucze tylko do odczytu.

Interfejs API kluczy `DatabaseAccountListKeysResult` listy zwraca obiekt. Ten typ nie jest zdefiniowany w bibliotekach języka C#. Poniższy kod przedstawia implementację tej klasy:  

```csharp 
namespace SummarizationService 
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

Użyjesz biblioteki [Microsoft.Azure.Services.AppAuthentication,](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) aby uzyskać token tożsamości zarządzanej przypisany przez system. Aby dowiedzieć się, inne sposoby, `Microsoft.Azure.Service.AppAuthentication` aby uzyskać token i więcej informacji na temat biblioteki, zobacz [service to service authentication](../key-vault/general/service-to-service-authentication.md) artykułu.

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
    public static class TemperatureMonitor
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

        [FunctionName("TemperatureMonitor")]
        public static async Task Run([TimerTrigger("0 * * * * *")]TimerInfo myTimer, ILogger log)
        {
            log.LogInformation($"Starting temperature monitoring: {DateTime.Now}");

            // AzureServiceTokenProvider will help us to get the Service Managed token.
            var azureServiceTokenProvider = new AzureServiceTokenProvider();

            // In order to get the Service Managed token we need to authenticate to the Azure Resource Manager.
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");

            // To get the Azure Cosmos DB keys setup the List Keys API:
            string endpoint = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2019-12-12";

            // setup an HTTP Client and add the access token.
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

            // Post to the endpoint to get the keys result.
            var result = await httpClient.PostAsync(endpoint, new StringContent(""));

            // Get the Result back as a DatabaseAccountListKeysResult.
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
            // fake the temperature sensor for this demo
            Random r = new Random(DateTime.UtcNow.Second);
            return r.Next(0, 120);
        }
    }
}
```

Teraz możesz przystąpić do [wdrażania funkcji platformy Azure.](../azure-functions/functions-create-first-function-vs-code.md)

## <a name="next-steps"></a>Następne kroki

* [Uwierzytelnianie oparte na certyfikatach za pomocą usługi Azure Cosmos DB i usługi Active Directory](certificate-based-authentication.md)
* [Zabezpieczanie kluczy usługi Azure Cosmos przy użyciu usługi Azure Key Vault](access-secrets-from-keyvault.md)
* [Linia bazowa zabezpieczeń usługi Azure Cosmos DB](security-baseline.md)
