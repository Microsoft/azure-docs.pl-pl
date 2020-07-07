---
title: Samouczek dotyczący rotacji zasobów z jednym zestawem poświadczeń uwierzytelniania
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zautomatyzować rotację klucza tajnego dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.openlocfilehash: 9bff8c040f4cfed612278dd83ebb354b31a3a1f3
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/01/2020
ms.locfileid: "85801448"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatyzowanie obrotu wpisu tajnego dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania

Najlepszym sposobem na uwierzytelnianie w usługach platformy Azure jest użycie [tożsamości zarządzanej](../general/managed-identity.md), ale istnieje kilka scenariuszy, w których nie jest to opcja. W tych przypadkach są używane klucze dostępu lub wpisy tajne. Należy okresowo obracać klucze dostępu lub wpisy tajne.

W tym samouczku pokazano, jak zautomatyzować okresowe rotacje wpisów tajnych dla baz danych i usług korzystających z jednego zestawu poświadczeń uwierzytelniania. W tym samouczku zaprojektowano SQL Server haseł przechowywanych w Azure Key Vault przy użyciu funkcji wyzwalanej przez powiadomienie Azure Event Grid:

![Diagram rozwiązania rotacji](../media/rotate1.png)

1. 30 dni przed datą wygaśnięcia wpisu tajnego Key Vault opublikuje zdarzenie "niedługo wygasnąć", aby Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa POST protokołu HTTP w celu wywołania punktu końcowego aplikacji funkcji subskrybowanego dla zdarzenia.
1. Aplikacja funkcji otrzymuje informacje o kluczu tajnym, generuje nowe hasło losowe i tworzy nową wersję dla wpisu tajnego z nowym hasłem w Key Vault.
1. Aplikacja funkcji aktualizuje SQL Server przy użyciu nowego hasła.

> [!NOTE]
> Może wystąpić opóźnienie między krokami 3 i 4. W tym czasie wpis tajny w Key Vault nie będzie mógł uwierzytelnić się w SQL Server. W przypadku awarii któregokolwiek z tych kroków Event Grid ponownych prób przez dwie godziny.

## <a name="create-a-key-vault-and-sql-server-instance"></a>Tworzenie magazynu kluczy i wystąpienia SQL Server

Pierwszym krokiem jest utworzenie magazynu kluczy oraz wystąpienia SQL Server i bazy danych oraz przechowywanie SQL Server hasło administratora w Key Vault.

W tym samouczku do tworzenia składników jest wykorzystywany istniejący szablon Azure Resource Manager. Kod można znaleźć tutaj: [przykładowy szablon rotacji podstawowych wpisów tajnych](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/arm-templates).

1. Wybierz link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Finitial-setup%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**. Nadaj grupie nazwę **simplerotation**.
1. Wybierz pozycję **Kup**.

    ![Tworzenie grupy zasobów](../media/rotate2.png)

Teraz masz Magazyn kluczy, wystąpienie SQL Server i bazę danych SQL. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wynik będzie wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
simplerotation-kv          simplerotation      eastus      Microsoft.KeyVault/vaults
simplerotation-sql         simplerotation      eastus      Microsoft.Sql/servers
simplerotation-sql/master  simplerotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-a-function-app"></a>Tworzenie aplikacji funkcji

Następnie Utwórz aplikację funkcji z tożsamością zarządzaną przez system, a także z innymi wymaganymi składnikami.

Aplikacja funkcji wymaga następujących składników:
- Plan Azure App Service
- Konto magazynu
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault za pośrednictwem tożsamości zarządzanej aplikacji funkcji

1. Wybierz link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Ffunction-app%2Fazuredeploy.json" target="_blank"><img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Na liście **Grupa zasobów** wybierz pozycję **simplerotation**.
1. Wybierz pozycję **Kup**.

   ![Wybierz zakup](../media/rotate3.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikację funkcji. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać podobnie jak następujące dane wyjściowe:

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

Aby uzyskać informacje na temat sposobu tworzenia aplikacji funkcji i używania tożsamości zarządzanej do uzyskiwania dostępu do Key Vault, zobacz [Tworzenie aplikacji funkcji na podstawie Azure Portal](../../azure-functions/functions-create-function-app-portal.md) i [udostępnianie Key Vault uwierzytelniania przy użyciu tożsamości zarządzanej](../general/managed-identity.md).

### <a name="rotation-function"></a>Funkcja rotacji
Funkcja używa zdarzenia do wyzwalania rotacji wpisu tajnego przez aktualizację Key Vault i bazy danych SQL.

#### <a name="function-trigger-event"></a>Zdarzenie wyzwalacza funkcji

Ta funkcja odczytuje dane zdarzenia i uruchamia logikę obrotu:

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

#### <a name="secret-rotation-logic"></a>Logika rotacji wpisów tajnych
Ta metoda rotacji odczytuje informacje o bazie danych z wpisu tajnego, tworzy nową wersję wpisu tajnego i aktualizuje bazę danych przy użyciu nowego klucza tajnego:

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
Kompletny kod można znaleźć w witrynie [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/rotation-function).

#### <a name="function-deployment"></a>Wdrożenie funkcji

1. Pobierz plik zip aplikacji funkcji z usługi [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-fn.zip).

1. Przekaż plik simplerotationsample-fn.zip, aby Azure Cloud Shell.

   ![Przekaż plik](../media/rotate4.png)
1. Użyj tego polecenia platformy Azure w celu wdrożenia pliku zip w aplikacji funkcji:

   ```azurecli
   az functionapp deployment source config-zip -g simplerotation -n simplerotation-fn --src /home/{firstname e.g jack}/simplerotationsample-fn.zip
   ```

Po wdrożeniu funkcji powinny zostać wyświetlone dwie funkcje w obszarze simplerotation-fn:

![Funkcje SimpleRotation i SimpleRotationHttpTest](../media/rotate5.png)

## <a name="add-an-event-subscription-for-the-secretnearexpiry-event"></a>Dodawanie subskrypcji zdarzeń dla zdarzenia SecretNearExpiry

Skopiuj klucz aplikacji funkcji `eventgrid_extension` :

   ![Wybieranie ustawień aplikacji funkcji](../media/rotate6.png)

   ![klucz eventgrid_extension](../media/rotate7.png)

Użyj skopiowanego `eventgrid_extension` klucza i identyfikatora subskrypcji w poniższym poleceniu, aby utworzyć subskrypcję Event Grid dla `SecretNearExpiry` zdarzeń:

```azurecli
az eventgrid event-subscription create --name simplerotation-eventsubscription --source-resource-id "/subscriptions/<subscription-id>/resourceGroups/simplerotation/providers/Microsoft.KeyVault/vaults/simplerotation-kv" --endpoint "https://simplerotation-fn.azurewebsites.net/runtime/webhooks/EventGrid?functionName=SimpleRotation&code=<extension-key>" --endpoint-type WebHook --included-event-types "Microsoft.KeyVault.SecretNearExpiry"
```

## <a name="add-the-secret-to-key-vault"></a>Dodaj klucz tajny do Key Vault
Ustaw zasady dostępu, aby przyznać użytkownikom uprawnienia do zarządzania wpisami *tajnymi* :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name simplerotation-kv --secret-permissions set delete get list
```

Utwórz nowy wpis tajny za pomocą tagów zawierających źródło danych SQL Database i identyfikator użytkownika. Uwzględnij datę wygaśnięcia ustawioną na jutro.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqluser --vault-name simplerotation-kv --value "Simple123" --tags "UserID=azureuser" "DataSource=simplerotation-sql.database.windows.net" --expires $tomorrowDate
```

Utworzenie klucza tajnego z krótką datą wygaśnięcia spowoduje natychmiastowe opublikowanie `SecretNearExpiry` zdarzenia, co spowoduje wyzwolenie funkcji na rotację klucza tajnego.

## <a name="test-and-verify"></a>Testowanie i weryfikowanie
Po kilku minutach `sqluser` klucz tajny powinien zostać automatycznie obrócony.

Aby sprawdzić, czy klucz tajny został obrócony, przejdź do **Key Vault**wpisów  >  **tajnych**:

![Przejdź do wpisów tajnych](../media/rotate8.png)

Otwórz klucz tajny **sqluser** i wyświetl wersje oryginalne i obrócone:

![Otwórz klucz tajny sqluser](../media/rotate9.png)

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, Utwórz aplikację internetową. Ta aplikacja internetowa pobierze klucz tajny z Key Vault, wyodrębni informacje o bazie danych SQL i poświadczenia z klucza tajnego i przetestuje połączenie z SQL Server.

Aplikacja sieci Web wymaga następujących składników:
- Aplikacja internetowa z tożsamością zarządzaną przez system
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault przez tożsamość zarządzaną przez aplikację sieci Web

1. Wybierz link wdrożenie szablonu platformy Azure:
<br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2Fazure-keyvault-basicrotation-tutorial%2Fmaster%2Farm-templates%2Fweb-app%2Fazuredeploy.json" target="_blank"> <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/></a>
1. Wybierz grupę zasobów **simplerotation** .
1. Wybierz pozycję **Kup**.

### <a name="deploy-the-web-app"></a>Wdrażanie aplikacji sieci Web

Kod źródłowy aplikacji sieci Web można znaleźć w witrynie [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/tree/master/test-webapp).

Aby wdrożyć aplikację sieci Web, wykonaj następujące kroki:

1. Pobierz plik zip aplikacji funkcji z usługi [GitHub](https://github.com/jlichwa/azure-keyvault-basicrotation-tutorial/raw/master/simplerotationsample-app.zip).
1. Przekaż plik simplerotationsample-app.zip, aby Azure Cloud Shell.
1. Użyj tego polecenia platformy Azure w celu wdrożenia pliku zip w aplikacji funkcji:

   ```azurecli
   az webapp deployment source config-zip -g simplerotation -n simplerotation-app --src /home/{firstname e.g jack}/simplerotationsample-app.zip
   ```

### <a name="open-the-web-app"></a>Otwieranie aplikacji sieci Web

Przejdź do wdrożonej aplikacji i wybierz adres URL:
 
![Wybierz adres URL](../media/rotate10.png)

Gdy aplikacja zostanie otwarta w przeglądarce, zostanie wyświetlona **wartość wygenerowanego klucza tajnego** i **połączona wartość bazy danych** *true*.

## <a name="learn-more"></a>Dowiedz się więcej

- Przegląd: [monitorowanie Key Vault z Azure Event Grid (wersja zapoznawcza)](../general/event-grid-overview.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](../general/event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault (wersja zapoznawcza)](../../event-grid/event-schema-key-vault.md)
