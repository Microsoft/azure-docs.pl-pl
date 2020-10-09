---
title: Samouczek dotyczący rotacji zasobów z jednym zestawem poświadczeń uwierzytelniania przechowywanych w Azure Key Vault
description: Skorzystaj z tego samouczka, aby dowiedzieć się, jak zautomatyzować rotację klucza tajnego dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: 661622b296a7a81a8d4c203e86a7c8d61c386e5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843230"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatyzowanie obrotu wpisu tajnego dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania

Najlepszym sposobem na uwierzytelnianie w usługach platformy Azure jest użycie [tożsamości zarządzanej](../general/authentication.md), ale istnieje kilka scenariuszy, w których nie jest to opcja. W tych przypadkach są używane klucze dostępu lub wpisy tajne. Należy okresowo obracać klucze dostępu lub wpisy tajne.

W tym samouczku pokazano, jak zautomatyzować okresowe rotacje wpisów tajnych dla baz danych i usług korzystających z jednego zestawu poświadczeń uwierzytelniania. W tym samouczku zaprojektowano SQL Server haseł przechowywanych w Azure Key Vault przy użyciu funkcji wyzwalanej przez powiadomienie Azure Event Grid:

![Diagram rozwiązania rotacji](../media/rotate-1.png)

1. 30 dni przed datą wygaśnięcia wpisu tajnego Key Vault opublikuje zdarzenie "niedługo wygasnąć", aby Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa POST protokołu HTTP w celu wywołania punktu końcowego aplikacji funkcji subskrybowanego dla zdarzenia.
1. Aplikacja funkcji otrzymuje informacje o kluczu tajnym, generuje nowe hasło losowe i tworzy nową wersję dla wpisu tajnego z nowym hasłem w Key Vault.
1. Aplikacja funkcji aktualizuje SQL Server przy użyciu nowego hasła.

> [!NOTE]
> Może wystąpić opóźnienie między krokami 3 i 4. W tym czasie wpis tajny w Key Vault nie będzie mógł uwierzytelnić się w SQL Server. W przypadku awarii któregokolwiek z tych kroków Event Grid ponownych prób przez dwie godziny.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* W usłudze Azure Key Vault
* SQL Server

Jeśli nie masz istniejących Key Vault i SQL Server, możesz użyć poniższego linku wdrażania:

[![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. W obszarze **Grupa zasobów**wybierz pozycję **Utwórz nową**. Nadaj grupie nazwę **akvrotation**.
1. W obszarze **Logowanie administratora SQL**wpisz nazwę logowania administratora SQL. 
1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Wybierz pozycję **Utwórz**

    ![Tworzenie grupy zasobów](../media/rotate-2.png)

Teraz masz Key Vault i wystąpienie SQL Server. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wynik będzie wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv          akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Tworzenie i wdrażanie funkcji rotacji hasła programu SQL Server
> [!IMPORTANT]
> Poniższy szablon wymaga Key Vault, programu SQL Server i funkcji platformy Azure, aby znajdować się w tej samej grupie zasobów

Następnie Utwórz aplikację funkcji z tożsamością zarządzaną przez system, a także z innymi wymaganymi składnikami i Wdróż funkcje rotacji hasła programu SQL Server.

Aplikacja funkcji wymaga następujących składników:
- Plan Azure App Service
- aplikacja funkcji z funkcjami rotacji hasła SQL z wyzwalaczem zdarzeń i wyzwalaczem http 
- Konto magazynu wymagane do zarządzania wyzwalaczem aplikacji funkcji
- Zasady dostępu do aplikacja funkcji tożsamości w celu uzyskania dostępu do wpisów tajnych w programie Key Vault
- Subskrypcja zdarzeń EventGrid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Na liście **Grupa zasobów** wybierz pozycję **akvrotation**.
1. W polu **Nazwa serwera SQL**wpisz nazwę serwera SQL z hasłem, aby obrócić
1. W polu **nazwa Key Vault**wpisz nazwę magazynu kluczy
1. W polu **nazwa aplikacja funkcji**wpisz nazwę aplikacji funkcji
1. W polu **Nazwa wpisu tajnego**wpisz nazwę wpisu tajnego, w którym będzie przechowywane hasło
1. W **adresie URL repozytorium**wpisz kod funkcji Lokalizacja usługi GitHub ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Wybierz pozycję **Utwórz**.

   ![Wybieranie opcji Recenzja + tworzenie](../media/rotate-3.png)

Po wykonaniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikację funkcji. Tę konfigurację można sprawdzić w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table
```

Wyniki będą wyglądać podobnie jak następujące dane wyjściowe:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation       eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation       eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation       eastus      Microsoft.Sql/servers/databases
cfogyydrufs5wazfunctions akvrotation       eastus      Microsoft.Storage/storageAccounts
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/serverFarms
akvrotation-fnapp        akvrotation       eastus      Microsoft.Web/sites
akvrotation-fnapp        akvrotation       eastus      Microsoft.insights/components
```

Aby uzyskać informacje na temat sposobu tworzenia aplikacji funkcji i używania tożsamości zarządzanej do uzyskiwania dostępu do Key Vault, zobacz [Tworzenie aplikacji funkcji na podstawie Azure Portal](/azure/azure-functions/functions-create-function-app-portal), [jak używać tożsamości zarządzanej dla App Service i Azure Functions](/azure/app-service/overview-managed-identity), a następnie [przypisywać zasady dostępu Key Vault przy użyciu Azure Portal](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Funkcja rotacji
Wdrożone w poprzednim kroku funkcja używa zdarzenia do wyzwolenia rotacji wpisu tajnego przez aktualizację Key Vault i bazy danych SQL. 

#### <a name="function-trigger-event"></a>Zdarzenie wyzwalacza funkcji

Ta funkcja odczytuje dane zdarzenia i uruchamia logikę obrotu:

```csharp
public static class SimpleRotationEventHandler
{
   [FunctionName("AKVSQLRotation")]
   public static void Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
   {
      log.LogInformation("C# Event trigger function processed a request.");
      var secretName = eventGridEvent.Subject;
      var secretVersion = Regex.Match(eventGridEvent.Data.ToString(), "Version\":\"([a-z0-9]*)").Groups[1].ToString();
      var keyVaultName = Regex.Match(eventGridEvent.Topic, ".vaults.(.*)").Groups[1].ToString();
      log.LogInformation($"Key Vault Name: {keyVaultName}");
      log.LogInformation($"Secret Name: {secretName}");
      log.LogInformation($"Secret Version: {secretVersion}");

      SecretRotator.RotateSecret(log, secretName, keyVaultName);
   }
}
```

#### <a name="secret-rotation-logic"></a>Logika rotacji wpisów tajnych
Ta metoda rotacji odczytuje informacje o bazie danych z wpisu tajnego, tworzy nową wersję wpisu tajnego i aktualizuje bazę danych przy użyciu nowego klucza tajnego:

```csharp
    public class SecretRotator
    {
        private const string CredentialIdTag = "CredentialId";
        private const string ProviderAddressTag = "ProviderAddress";
        private const string ValidityPeriodDaysTag = "ValidityPeriodDays";

        public static void RotateSecret(ILogger log, string secretName, string keyVaultName)
        {
            //Retrieve Current Secret
            var kvUri = "https://" + keyVaultName + ".vault.azure.net";
            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
            KeyVaultSecret secret = client.GetSecret(secretName);
            log.LogInformation("Secret Info Retrieved");

            //Retrieve Secret Info
            var credentialId = secret.Properties.Tags.ContainsKey(CredentialIdTag) ? secret.Properties.Tags[CredentialIdTag] : "";
            var providerAddress = secret.Properties.Tags.ContainsKey(ProviderAddressTag) ? secret.Properties.Tags[ProviderAddressTag] : "";
            var validityPeriodDays = secret.Properties.Tags.ContainsKey(ValidityPeriodDaysTag) ? secret.Properties.Tags[ValidityPeriodDaysTag] : "";
            log.LogInformation($"Provider Address: {providerAddress}");
            log.LogInformation($"Credential Id: {credentialId}");

            //Check Service Provider connection
            CheckServiceConnection(secret);
            log.LogInformation("Service  Connection Validated");
            
            //Create new password
            var randomPassword = CreateRandomPassword();
            log.LogInformation("New Password Generated");

            //Add secret version with new password to Key Vault
            CreateNewSecretVersion(client, secret, randomPassword);
            log.LogInformation("New Secret Version Generated");

            //Update Service Provider with new password
            UpdateServicePassword(secret, randomPassword);
            log.LogInformation("Password Changed");
            log.LogInformation($"Secret Rotated Successfully");
        }
}
```
Kompletny kod można znaleźć w witrynie [GitHub](https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp).

## <a name="add-the-secret-to-key-vault"></a>Dodaj klucz tajny do Key Vault
Ustaw zasady dostępu, aby przyznać użytkownikom uprawnienia do zarządzania wpisami *tajnymi* :

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Utwórz nowy wpis tajny ze znacznikami zawierającymi identyfikator zasobu SQL Server, nazwę logowania SQL Server i okres ważności klucza tajnego w dniach. Podaj nazwę wpisu tajnego, początkowe hasło z usługi SQL Database (w naszym przykładzie "Simple123") i Uwzględnij datę wygaśnięcia ustawioną na jutro.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Utworzenie klucza tajnego z krótką datą wygaśnięcia spowoduje opublikowanie `SecretNearExpiry` zdarzenia w ciągu 15 minut, co spowoduje wyzwolenie funkcji na rotację klucza tajnego.

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Aby sprawdzić, czy klucz tajny został obrócony, przejdź do **Key Vault**wpisów  >  **tajnych**:

![Przejdź do wpisów tajnych](../media/rotate-8.png)

Otwórz wpis tajny **SQLPassword** i wyświetl wersje oryginalne i obrócone:

![Otwórz klucz tajny sqluser](../media/rotate-9.png)

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, Utwórz aplikację internetową. Ta aplikacja internetowa pobierze klucz tajny z Key Vault, wyodrębni informacje o bazie danych SQL i poświadczenia z klucza tajnego i przetestuje połączenie z SQL Server.

Aplikacja sieci Web wymaga następujących składników:
- Aplikacja internetowa z tożsamością zarządzaną przez system
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault przez tożsamość zarządzaną przez aplikację sieci Web

1. Wybierz link wdrożenie szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmaster%2Farm-templates%2FWeb-App%2Fazuredeploy.json)

1. Wybierz grupę zasobów **akvrotation** .
1. W polu **Nazwa serwera SQL**wpisz nazwę serwera SQL z hasłem, aby obrócić
1. W polu **nazwa Key Vault**wpisz nazwę magazynu kluczy
1. W polu **Nazwa wpisu tajnego**wpisz nazwę wpisu tajnego, w którym jest przechowywane hasło
1. W **adresie URL repozytorium**wpisz w polu kod aplikacji sieci Web lokalizację usługi GitHub ( **https://github.com/jlichwa/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** ).
1. Wybierz pozycję **Przeglądanie + tworzenie**.
1. Wybierz pozycję **Utwórz**.


### <a name="open-the-web-app"></a>Otwieranie aplikacji sieci Web

Przejdź do adresu URL wdrożonej aplikacji:
 
https://akvrotation-app.azurewebsites.net/

Gdy aplikacja zostanie otwarta w przeglądarce, zostanie wyświetlona **wartość wygenerowanego klucza tajnego** i **połączona wartość bazy danych** *true*.

## <a name="learn-more"></a>Dowiedz się więcej

- Samouczek: [rotacja zasobów przy użyciu dwóch zestawów poświadczeń](tutorial-rotation-dual.md)
- Przegląd: [monitorowanie Key Vault z Azure Event Grid](../general/event-grid-overview.md)
- Instrukcje: [otrzymywanie wiadomości e-mail po zmianie wpisu tajnego magazynu kluczy](../general/event-grid-logicapps.md)
- [Schemat zdarzeń Azure Event Grid dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
