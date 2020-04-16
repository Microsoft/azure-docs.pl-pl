---
title: Samouczek rotacji pojedynczego użytkownika/pojedynczego hasła
description: Ten samouczek służy do automatyzacji obracania klucza tajnego dla zasobów korzystających z uwierzytelniania pojedynczego użytkownika/pojedynczego hasła.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 70eb2449c5c54750831c30ff7d5c948173a38594
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423306"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-single-usersingle-password-authentication"></a>Automatyzacja rotacji klucza tajnego dla zasobów korzystających z uwierzytelniania pojedynczego użytkownika/pojedynczego hasła

Najlepszym sposobem uwierzytelniania w usługach platformy Azure jest użycie [tożsamości zarządzanej,](../general/managed-identity.md)ale istnieją pewne scenariusze, w których nie jest to możliwe. W takich przypadkach używane są klucze dostępu lub wpisy tajne. Należy okresowo obracać klucze dostępu lub wpisy tajne.

W tym samouczku pokazano, jak zautomatyzować okresowy obrót wpisów tajnych dla baz danych i usług, które używają uwierzytelniania pojedynczego użytkownika/pojedynczego hasła. W szczególności ten samouczek obraca hasła programu SQL Server przechowywane w usłudze Azure Key Vault przy użyciu funkcji wyzwalane przez powiadomienie usługi Azure Event Grid:

![Diagram rozwiązania obrotu](../media/rotate1.png)

1. Trzydzieści dni przed datą wygaśnięcia klucza magazyn kluczy publikuje zdarzenie "bliski wygaśnięcia" w uszmia zdarzenia.
1. Usługa Event Grid sprawdza subskrypcje zdarzeń i używa protokołu HTTP POST do wywoływania punktu końcowego aplikacji funkcji subskrybowanego do zdarzenia.
1. Aplikacja funkcji odbiera tajne informacje, generuje nowe losowe hasło i tworzy nową wersję klucza tajnego z nowym hasłem w Key Vault.
1. Aplikacja funkcji aktualizuje SQL Server z nowym hasłem.

> [!NOTE]
> Może wystąpić opóźnienie między krokami 3 i 4. W tym czasie klucz tajny w magazynie kluczy nie będzie mógł uwierzytelnić się w programie SQL Server. W przypadku niepowodzenia dowolnego z kroków, usługa Siatki zdarzeń ponawia ponawia ponawia się przez dwie godziny.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Tworzenie magazynu kluczy i wystąpienia programu SQL Server

Pierwszym krokiem jest utworzenie magazynu kluczy oraz wystąpienia i bazy danych programu SQL Server oraz przechowywanie hasła administratora programu SQL Server w programie Key Vault.

W tym samouczku użyto istniejącego szablonu usługi Azure Resource Manager do tworzenia składników. Kod można znaleźć tutaj: [Przykład szablonu podstawowego tajnego obrotu](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Wybierz łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nowy**. Nazwij grupę **simplerotation**.
1. Wybierz pozycję **Kup**.

    ![Tworzenie grupy zasobów](../media/rotate2.png)

Będziesz teraz mieć magazyn kluczy, wystąpienie programu SQL Server i bazę danych SQL. Tę konfigurację można zweryfikować w platformie Azure CLI, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wynik będzie wyglądać coś następującego wyjścia:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Następnie utwórz aplikację funkcji z tożsamością zarządzaną przez system, oprócz innych wymaganych składników.

Aplikacja funkcji wymaga następujących składników:
- Plan usługi aplikacji platformy Azure
- Konto magazynu
- Zasady dostępu do wpisów tajnych w uchodźczynie za pośrednictwem tożsamości zarządzanej przez aplikację funkcji

1. Wybierz łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Na liście **Grupa zasobów** wybierz **simplerotation**.
1. Wybierz pozycję **Kup**.

   ![Wybierz zakup](../media/rotate3.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikację funkcji. Tę konfigurację można zweryfikować w platformie Azure CLI, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wynik będzie wyglądać mniej więcej tak, jak następujące dane wyjściowe:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation       eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation       eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation       eastus      Microsoft.Sql/servers/databases
simplerotationstrg         simplerotation       eastus      Microsoft.Storage/storageAccounts
simplerotation-plan        simplerotation       eastus      Microsoft.Web/serverFarms
simplerotation-fn          simplerotation       eastus      Microsoft.Web/sites
```

Aby uzyskać informacje na temat tworzenia aplikacji funkcji i używania tożsamości zarządzanej w celu uzyskania dostępu do usługi Key Vault, zobacz [Tworzenie aplikacji funkcji z portalu Azure](../../azure-functions/functions-create-function-app-portal.md) i [zapewnianie uwierzytelniania usługi Key Vault z tożsamością zarządzaną](../general/managed-identity.md).

### <a name="rotation-function"></a>Obrót, funkcja
Funkcja używa zdarzenia do wyzwalania rotacji klucza tajnego przez aktualizację usługi Key Vault i bazy danych SQL.

#### <a name="function-trigger-event"></a>Zdarzenie wyzwalania funkcji

Ta funkcja odczytuje dane zdarzeń i uruchamia logikę obrotu:

```csharp
public static class SimpleRotationEventHandler
{
    [FunctionName("SimpleRotation")]
       public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
       {
            log.LogInformation("C# Event trigger function processed a request.");
            var secretName = eventGridEvent.Subject;
            var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
            var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
            log.LogInformation($"Key Vault Name: {keyVaultName}");
            log.LogInformation($"Secret Name: {secretName}");
            log.LogInformation($"Secret Version: {secretVersion}");

            SeretRotator.RotateSecret(log, secretName, secretVersion, keyVaultName);
        }
}
```

#### <a name="secret-rotation-logic"></a>Logika rotacji tajnej
Ta metoda rotacji odczytuje informacje bazy danych z klucza tajnego, tworzy nową wersję klucza tajnego i aktualizuje bazę danych o nowy klucz tajny:

```csharp
public class SecretRotator
    {
       private const string UserIdTagName = "UserID";
       private const string DataSourceTagName = "DataSource";
       private const int SecretExpirationDays = 31;

    public static void RotateSecret(ILogger log, string secretName, string secretVersion, string keyVaultName)
    {
           //Retrieve current secret
           var kvUri = "https://" + keyVaultName + ".vault.azure.net";
           var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
           KeyVaultSecret secret = client.GetSecret(secretName, secretVersion);
           log.LogInformation("Secret Info Retrieved");
        
           //Retrieve secret info
           var userId = secret.Properties.Tags.ContainsKey(UserIdTagName) ?  
                        secret.Properties.Tags[UserIdTagName] : "";
           var datasource = secret.Properties.Tags.ContainsKey(DataSourceTagName) ? 
                            secret.Properties.Tags[DataSourceTagName] : "";
           log.LogInformation($"Data Source Name: {datasource}");
           log.LogInformation($"User Id Name: {userId}");
        
           //Create new password
           var randomPassword = CreateRandomPassword();
           log.LogInformation("New Password Generated");
        
           //Check DB connection using existing secret
           CheckServiceConnection(secret);
           log.LogInformation("Service Connection Validated");
                    
           //Create new secret with generated password
           CreateNewSecretVersion(client, secret, randomPassword);
           log.LogInformation("New Secret Version Generated");
        
           //Update DB password
           UpdateServicePassword(secret, randomPassword);
           log.LogInformation("Password Changed");
           log.LogInformation($"Secret Rotated Succesffuly");
    }
}
```
Pełny kod można znaleźć na [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Wdrażanie funkcji

1. Pobierz plik zip aplikacji funkcji z [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Przekaż plik simplerotationsample-fn.zip do usługi Azure Cloud Shell.

   ![Prześlij plik](../media/rotate4.png)
1. Użyj tego polecenia interfejsu wiersza polecenia platformy Azure, aby wdrożyć plik zip w aplikacji funkcji:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Po wdrożeniu funkcji powinny zostać wyświetlone dwie funkcje w obszarze simplerotation-fn:

![SimpleRotation i SimpleRotationHttpTest funkcje](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Dodawanie subskrypcji wydarzenia dla zdarzenia SecretNearExpiry

Skopiuj `eventgrid_extension` klucz aplikacji funkcji:

   ![Wybieranie ustawień aplikacji funkcji](../media/rotate6.png)

   ![eventgrid_extension klucz](../media/rotate7.png)

Użyj skopiowanego `eventgrid_extension` klucza i identyfikatora subskrypcji w następującym poleceniu, aby utworzyć subskrypcję usługi Event Grid dla `SecretNearExpiry` zdarzeń:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Dodawanie klucza tajnego do magazynu kluczy
Ustaw zasady dostępu, aby przyznawać użytkownikom uprawnienia do *zarządzania wpisami tajnymi:*

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Utwórz nowy klucz tajny ze znacznikami zawierającymi źródło danych bazy danych SQL i identyfikator użytkownika. Dołącz datę wygaśnięcia ustawioną na jutro.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Utworzenie klucza tajnego z krótką datą `SecretNearExpiry` wygaśnięcia natychmiast opublikuje zdarzenie, które z kolei wyzwoli funkcję obracania klucza tajnego.

## <a name="test-and-verify"></a>Testowanie i weryfikowanie
Po kilku minutach `sqluser` sekret powinien automatycznie się obracać.

Aby sprawdzić, czy klucz tajny został obrócony, przejdź do **funkcji Key Vault** > **Secrets:**

![Przejdź do sekretów](../media/rotate8.png)

Otwórz klucz tajny **sqluser** i wyświetl oryginalne i obrócone wersje:

![Otwórz klucz tajny sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, utwórz aplikację sieci web. Ta aplikacja sieci web otrzyma klucz tajny z usługi Key Vault, wyodrębni informacje o bazie danych SQL i poświadczenia z klucza tajnego i przetestuje połączenie z programem SQL Server.

Aplikacja sieci web wymaga następujących składników:
- Aplikacja internetowa z tożsamością zarządzaną systemem
- Zasady dostępu do wpisów tajnych w programie Key Vault za pośrednictwem tożsamości zarządzanej w aplikacji sieci Web

1. Wybierz łącze wdrażania szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Wybierz **grupę zasobów simplerotation.**
1. Wybierz pozycję **Kup**.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web

Kod źródłowy aplikacji internetowej można znaleźć w [serwisie GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Aby wdrożyć aplikację sieci web, wykonaj następujące kroki:

1. Pobierz plik zip aplikacji funkcji z [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Przekaż plik simplerotationsample-app.zip do usługi Azure Cloud Shell.
1. Użyj tego polecenia interfejsu wiersza polecenia platformy Azure, aby wdrożyć plik zip w aplikacji funkcji:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Otwieranie aplikacji internetowej

Przejdź do wdrożonej aplikacji i wybierz adres URL:
 
![Wybierz adres URL](../media/rotate10.png)

Powinna zostać wyświetlone wygenerowana wartość tajnego z wartością połączoną z bazą danych true.

## <a name="learn-more"></a>Dowiedz się więcej

- Omówienie: [Monitorowanie magazynu kluczy za pomocą usługi Azure Event Grid (wersja zapoznawcza)](../general/event-grid-overview.md)
- Jak: [Odbieranie wiadomości e-mail po zmianie klucza tajnego magazynu](../general/event-grid-logicapps.md)
- [Schemat zdarzeń usługi Azure Event Grid dla usługi Azure Key Vault (wersja zapoznawcza)](../../event-grid/event-schema-key-vault.md)