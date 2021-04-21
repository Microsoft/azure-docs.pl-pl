---
title: Samouczek rotacji dla zasobów z jednym zestawem poświadczeń uwierzytelniania przechowywanych w Azure Key Vault
description: Z tego samouczka dowiesz się, jak zautomatyzować rotację tajnego hasła dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania.
services: key-vault
author: msmbaldwin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 01/26/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: e66be3b0e3ecae5caa1a76294cc8b8dc11a5f207
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748668"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-use-one-set-of-authentication-credentials"></a>Automatyzowanie rotacji tajnego hasła dla zasobów, które używają jednego zestawu poświadczeń uwierzytelniania

Najlepszym sposobem uwierzytelniania w usługach [](../general/authentication.md)platformy Azure jest użycie tożsamości zarządzanej, ale w niektórych scenariuszach nie jest to możliwe. W takich przypadkach są używane klucze dostępu lub wpisy tajne. Należy okresowo obracać klucze dostępu lub wpisy tajne.

W tym samouczku pokazano, jak zautomatyzować okresową rotację wpisów tajnych dla baz danych i usług, które używają jednego zestawu poświadczeń uwierzytelniania. W szczególności w tym samouczku SQL Server hasła przechowywane w Azure Key Vault przy użyciu funkcji wyzwalane przez Azure Event Grid powiadomień:


:::image type="content" source="../media/rotate-1.png" alt-text="Diagram rozwiązania rotacji":::

1. 30 dni przed datą wygaśnięcia tajnego Key Vault publikuje zdarzenie "blisko wygaśnięcia" Event Grid.
1. Event Grid sprawdza subskrypcje zdarzeń i używa żądania HTTP POST do wywołania punktu końcowego aplikacji funkcji subskrybowanego do zdarzenia.
1. Aplikacja funkcji odbiera informacje o kluczu tajnym, generuje nowe losowe hasło i tworzy nową wersję dla tego hasła przy użyciu nowego hasła w Key Vault.
1. Aplikacja funkcji zostanie SQL Server przy użyciu nowego hasła.

> [!NOTE]
> Może być opóźnienie między krokami 3 i 4. W tym czasie klucz tajny w Key Vault nie będzie mógł uwierzytelnić się w SQL Server. W przypadku niepowodzenia dowolnego z tych kroków Event Grid ponowne próby przez dwie godziny.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault
* SQL Server

Poniższego linku wdrażania można użyć, jeśli nie masz istniejących Key Vault i SQL Server:

[![Obraz przedstawiający przycisk z etykietą "Wdrażanie na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. W **obszarze Grupa zasobów** wybierz pozycję Utwórz **nową.** Nadaj grupie nazwę. W tym **samouczku użyjemy akvrotation.**
1. W **obszarze Identyfikator logowania administratora SQL** wpisz nazwę logowania administratora sql. 
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**

:::image type="content" source="../media/rotate-2.png" alt-text="Tworzenie grupy zasobów":::

Teraz będziesz mieć wystąpienie Key Vault i SQL Server wystąpienia. Tę konfigurację można zweryfikować w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table -g akvrotation
```

Wynik będzie wyglądać następująco:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv           akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotation-sql          akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql/master   akvrotation      eastus      Microsoft.Sql/servers/databases
akvrotation-sql2         akvrotation      eastus      Microsoft.Sql/servers
akvrotation-sql2/master  akvrotation      eastus      Microsoft.Sql/servers/databases
```

## <a name="create-and-deploy-sql-server-password-rotation-function"></a>Tworzenie i wdrażanie funkcji rotacji haseł programu SQL Server
> [!IMPORTANT]
> Poniższy szablon wymaga Key Vault, programu SQL Server i funkcji platformy Azure w tej samej grupie zasobów

Następnie utwórz aplikację funkcji z tożsamością zarządzaną przez system, oprócz innych wymaganych składników, i wdrożysz funkcje rotacji haseł programu SQL Server

Aplikacja funkcji wymaga tych składników:
- Plan Azure App Service aplikacji
- Aplikacja funkcji z funkcjami rotacji haseł SQL z wyzwalaczem zdarzenia i wyzwalaczem HTTP 
- Konto magazynu wymagane do zarządzania wyzwalaczem aplikacji funkcji
- Zasady dostępu dla tożsamości aplikacji funkcji w celu uzyskania dostępu do wpisów tajnych w Key Vault
- Subskrypcja zdarzeń EventGrid dla zdarzenia **SecretNearExpiry**

1. Wybierz link wdrażania szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk z etykietą "Wdrażanie na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp%2Fmain%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Na **liście Grupa zasobów** wybierz pozycję **akvrotation**.
1. W nazwie **programu SQL Server** wpisz nazwę programu Sql Server z hasłem do rotacji
1. W **Key Vault wpisz** nazwę magazynu kluczy
1. W **nazwie aplikacji funkcji** wpisz nazwę aplikacji funkcji
1. W **skrypcie Nazwa** wpisów tajnych wpisz nazwę wpisów tajnych, w której będzie przechowywane hasło
1. W polu **Adres URL repozytorium** wpisz kod funkcji Lokalizacja w usłudze GitHub ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp.git** )
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.

:::image type="content" source="../media/rotate-3.png" alt-text="Wybierz pozycję Przejrzyj i utwórz":::
  

Po ukończeniu powyższych kroków będziesz mieć konto magazynu, farmę serwerów i aplikację funkcji. Możesz zweryfikować tę konfigurację w interfejsie wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az resource list -o table -g akvrotation
```

Wynik będzie wyglądać podobnie do następujących danych wyjściowych:

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

Aby uzyskać informacje na temat sposobu tworzenia aplikacji funkcji i używania tożsamości zarządzanej do uzyskiwania dostępu do usługi Key Vault, zobacz Tworzenie aplikacji funkcji z usługi [Azure Portal,](../../azure-functions/functions-create-function-app-portal.md)Jak używać tożsamości zarządzanej dla usług [App Service i Azure Functions](../../app-service/overview-managed-identity.md)oraz Przypisywanie zasad dostępu do usługi Key Vault przy użyciu usługi [Azure Portal](../general/assign-access-policy-portal.md).

### <a name="rotation-function"></a>Rotation, funkcja
Funkcja wdrożona w poprzednim kroku używa zdarzenia w celu wyzwolenia rotacji tajnego przez zaktualizowanie Key Vault i bazy danych SQL. 

#### <a name="function-trigger-event"></a>Zdarzenie wyzwalacza funkcji

Ta funkcja odczytuje dane zdarzenia i uruchamia logikę rotacji:

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

#### <a name="secret-rotation-logic"></a>Logika rotacji tajnych
Ta metoda rotacji odczytuje informacje o bazie danych z tajnego, tworzy nową wersję tajnego i aktualizuje bazę danych nowym kluczem tajnym:

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
Kompletny kod można znaleźć w witrynie [GitHub.](https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp)

## <a name="add-the-secret-to-key-vault"></a>Dodaj klucz tajny do Key Vault
Ustaw zasady dostępu, aby przyznać *użytkownikom uprawnienia do* zarządzania wpisami tajnymi:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Utwórz nowy klucz tajny z tagami, które zawierają identyfikator SQL Server, nazwę logowania SQL Server oraz okres ważności dla tego hasła w dniach. Podaj nazwę tajnego i początkowego hasła z bazy danych SQL (w naszym przykładzie "Simple123") i dołącz datę wygaśnięcia ustawioną na przyszłość.

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name sqlPassword --vault-name akvrotation-kv --value "Simple123" --tags "CredentialId=sqlAdmin" "ProviderAddress=<sql-database-resource-id>" "ValidityPeriodDays=90" --expires $tomorrowDate
```

Utworzenie tajnego hasła z krótką datą wygaśnięcia spowoduje opublikowanie zdarzenia w ciągu 15 minut, co z kolei spowoduje wyzwolenie rotacji `SecretNearExpiry` tego tajnego przez funkcję.

## <a name="test-and-verify"></a>Testowanie i weryfikowanie

Aby sprawdzić, czy wpis tajny został obrócony, przejdź **do** Key Vault  >  **Wpisy tajne:**

:::image type="content" source="../media/rotate-8.png" alt-text="Zrzut ekranu przedstawiający sposób uzyskiwania dostępu do Key Vault > Wpisów tajnych.":::

Otwórz klucz **tajny sqlPassword** i wyświetl oryginalne i obrócone wersje:

:::image type="content" source="../media/rotate-9.png" alt-text="Przejdź do wpisów tajnych":::

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Aby zweryfikować poświadczenia SQL, utwórz aplikację internetową. Ta aplikacja internetowa pobierze klucz tajny z Key Vault, wyodrębni informacje i poświadczenia bazy danych SQL z tego tajnego kodu oraz przetestuje połączenie z SQL Server.

Aplikacja internetowa wymaga tych składników:
- Aplikacja internetowa z tożsamością zarządzaną przez system
- Zasady dostępu do uzyskiwania dostępu do wpisów tajnych w Key Vault za pośrednictwem tożsamości zarządzanej aplikacji internetowej

1. Wybierz link wdrażania szablonu platformy Azure: 

   [![Obraz przedstawiający przycisk z etykietą "Wdrażanie na platformie Azure".](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-SQLPassword-Csharp-WebApp%2Fmain%2FARM-Templates%2FWeb-App%2Fazuredeploy.json)

1. Wybierz **grupę zasobów akvrotation.**
1. W nazwie **programu SQL Server** wpisz nazwę programu Sql Server z hasłem do rotacji
1. W **Key Vault wpisz** nazwę magazynu kluczy
1. W **skrypcie Nazwa** wpisów tajnych wpisz nazwę wpisów tajnych, w której jest przechowywane hasło
1. W polu **Adres URL repozytorium** wpisz kod aplikacji internetowej Lokalizacja w serwisie GitHub ( **https://github.com/Azure-Samples/KeyVault-Rotation-SQLPassword-Csharp-WebApp.git** )
1. Wybierz pozycję **Przejrzyj i utwórz**.
1. Wybierz pozycję **Utwórz**.


### <a name="open-the-web-app"></a>Otwieranie aplikacji internetowej

Przejdź do adresu URL wdrożonej aplikacji:
 
'https://akvrotation-app.azurewebsites.net/'

Gdy aplikacja zostanie otwarta w przeglądarce, zobaczysz wartość **Wygenerowano** klucz tajny i **Wartość** połączona z bazą *danych o wartości true*.

## <a name="learn-more"></a>Więcej tutaj

- Samouczek: [rotacja zasobów z dwoma zestawami poświadczeń](tutorial-rotation-dual.md)
- Omówienie: [Monitorowanie Key Vault za pomocą Azure Event Grid](../general/event-grid-overview.md)
- Dzieje się tak: [otrzymywanie wiadomości e-mail po zmianie klucza tajnego magazynu kluczy](../general/event-grid-logicapps.md)
- [Azure Event Grid zdarzeń dla Azure Key Vault](../../event-grid/event-schema-key-vault.md)
