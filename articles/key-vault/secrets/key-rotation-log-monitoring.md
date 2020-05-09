---
title: Skonfiguruj Azure Key Vault z kompleksowym rotacją i inspekcją kluczy | Microsoft Docs
description: Skorzystaj z tego przewodnika, aby pomóc Ci w konfigurowaniu rotacji kluczy i monitorowaniu dzienników magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: a5aaef50f12bfec89cf5e883ed6b1c85fa984ad6
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2020
ms.locfileid: "82995977"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurowanie Azure Key Vault przy użyciu rotacji kluczy i inspekcji

## <a name="introduction"></a>Wprowadzenie

Po utworzeniu magazynu kluczy można rozpocząć korzystanie z niego w celu przechowywania kluczy i wpisów tajnych. Aplikacje nie muszą już utrwalać kluczy lub wpisów tajnych, ale mogą żądać ich z magazynu zgodnie z potrzebami. Magazyn kluczy umożliwia aktualizowanie kluczy i wpisów tajnych bez wpływu na zachowanie aplikacji, co umożliwia otwarcie szeregu możliwości klucza i tajnego zarządzania.

W tym artykule omówiono sposób implementacji planowanej rotacji kluczy konta magazynu, monitorowania dzienników inspekcji magazynu kluczy i zgłaszania alertów w przypadku nieoczekiwanych żądań. 

Najpierw należy utworzyć magazyn kluczy przy użyciu wybranej metody:

- [konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
- [Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure PowerShell](quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego z Azure Key Vault przy użyciu Azure Portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Przechowywanie klucza tajnego

Aby umożliwić aplikacji pobranie wpisu tajnego z Key Vault, musisz najpierw utworzyć klucz tajny i przekazać go do magazynu.

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:

```powershell
Connect-AzAccount
```

W oknie podręcznym przeglądarki wprowadź nazwę użytkownika i hasło do konta platformy Azure. Program PowerShell pobierze wszystkie subskrypcje, które są skojarzone z tym kontem. Program PowerShell domyślnie używa pierwszego z nich.

Jeśli masz wiele subskrypcji, może być konieczne określenie, która została użyta do utworzenia magazynu kluczy. Wprowadź następujące elementy, aby wyświetlić subskrypcje dla Twojego konta:

```powershell
Get-AzSubscription
```

Aby określić subskrypcję skojarzoną z magazynem kluczy, który będzie rejestrowany, wpisz:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Ponieważ w tym artykule przedstawiono przechowywanie klucza konta magazynu jako klucza tajnego, należy pobrać ten klucz konta magazynu.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po pobraniu wpisu tajnego (w tym przypadku klucza konta magazynu) należy przekonwertować ten klucz na bezpieczny ciąg, a następnie utworzyć wpis tajny o tej wartości w magazynie kluczy.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Następnie Uzyskaj identyfikator URI utworzonego wpisu tajnego. Ten identyfikator URI będzie potrzebny w późniejszym kroku, aby wywołać Magazyn kluczy i pobrać klucz tajny. Uruchom następujące polecenie programu PowerShell i zanotuj wartość identyfikatora, który jest identyfikatorem URI wpisu tajnego:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurowanie aplikacji

Teraz, gdy masz klucz tajny, możesz go pobrać i używać po wykonaniu kilku kroków.

Najpierw musisz zarejestrować aplikację w Azure Active Directory. Następnie poinformuj Key Vault informacje o aplikacji, aby zezwolić na żądania z aplikacji.

> [!NOTE]
> Aplikacja musi zostać utworzona w tej samej dzierżawie Azure Active Directoryej co Magazyn kluczy.

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. Jeśli Twoje konto zapewnia dostęp do więcej niż jednej dzierżawy, wybierz swoje konto w prawym górnym rogu. Skonfiguruj sesję portalu do dzierżawy usługi Azure AD, której chcesz użyć.
1. Wyszukaj i wybierz pozycję **Azure Active Directory**. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. W obszarze **zarejestruj aplikację**wprowadź zrozumiałą nazwę aplikacji wyświetlaną dla użytkowników.
1. Określ, kto może korzystać z aplikacji w następujący sposób:

    | Obsługiwane typy konta | Opis |
    |-------------------------|-------------|
    | **Konta tylko w tym katalogu organizacyjnym** | Wybierz tę opcję, jeśli kompilujesz aplikację biznesową. Ta opcja jest niedostępna, jeśli aplikacja nie jest rejestrowana w katalogu.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację jednodostępną.<br><br>Ta opcja jest wartością domyślną, chyba że rejestrujesz aplikację poza katalogiem. W przypadkach, gdy aplikacja jest rejestrowana poza katalogiem, wartość domyślna to aplikacja wielodostępna usługi Azure AD i konta osobiste Microsoft. |
    | **Konta w dowolnym katalogu organizacyjnym** | Wybierz tę opcję, jeśli aplikacja jest przeznaczona dla wszystkich klientów biznesowych i edukacyjnych.<br><br>Ta opcja mapuje do usługi Azure AD tylko aplikację wielodostępną.<br><br>Jeśli aplikacja została zarejestrowana jako jedyna dzierżawa usługi Azure AD, możesz ją zaktualizować do usługi Azure AD z wieloma dzierżawcami i z powrotem do jednej dzierżawy za pomocą strony **uwierzytelniania** . |
    | **Konta w dowolnym katalogu organizacyjnym i konta osobiste Microsoft** | Wybierz tę opcję, aby przeznaczyć aplikację dla najszerszego możliwego grona klientów.<br><br>Ta opcja mapuje do usługi Azure AD aplikację wielodostępną i konta osobiste Microsoft.<br><br>Jeśli aplikacja została zarejestrowana jako usługa Azure AD z wieloma dzierżawcami i osobistymi kontami Microsoft, nie można zmienić tego ustawienia w interfejsie użytkownika. Zamiast tego musisz użyć edytora manifestu aplikacji, aby zmienić obsługiwane typy konta. |

1. W obszarze **URI przekierowania (opcjonalnie)** wybierz typ aplikacji, którą tworzysz: klient **internetowy** lub **publiczny (Mobile & Desktop)**. Następnie wprowadź identyfikator URI przekierowania lub adres URL odpowiedzi dla aplikacji.

    * W przypadku aplikacji internetowej podaj podstawowy adres URL aplikacji. Na przykład ciąg `https://localhost:31544` może być adresem URL aplikacji internetowej uruchomionej na komputerze lokalnym. Użytkownicy mogą użyć tego adresu URL, aby zalogować się do aplikacji klienta internetowego.
    * W przypadku publicznych aplikacji klienckich podaj identyfikator URI używany przez usługę Azure AD do zwracania odpowiedzi tokenu. Podaj wartość specyficzną dla Twojej aplikacji, np. `myapp://auth`.

1. Po zakończeniu wybierz pozycję **Rejestruj**.

    ![Wyświetla ekran, aby zarejestrować nową aplikację w Azure Portal](../media/new-app-registration.png)

Usługa Azure AD przypisuje do aplikacji unikatową aplikację lub identyfikator klienta. W portalu zostanie otwarta strona **Przegląd** aplikacji. Zanotuj wartość **identyfikatora aplikacji (klienta)** .

Aby dodać możliwości do aplikacji, możesz wybrać inne opcje konfiguracji, w tym znakowanie, certyfikaty i wpisy tajne, uprawnienia interfejsu API i inne.

![Przykład strony przeglądu nowo zarejestrowanej aplikacji](../media//new-app-overview-page-expanded.png)

Przed nawiązaniem jakichkolwiek wywołań z aplikacji do magazynu kluczy należy poinformować Magazyn kluczy o aplikacji i jej uprawnieniach. Następujące polecenie używa nazwy magazynu i **identyfikatora aplikacji (klienta)** z aplikacji Azure Active Directory, aby umożliwić aplikacji **uzyskanie** dostępu do magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Wszystko jest teraz gotowe do rozpoczęcia kompilowania wywołań aplikacji. W aplikacji należy zainstalować pakiety NuGet wymagane do współpracy z Azure Key Vault i Azure Active Directory. W konsoli Menedżera pakietów programu Visual Studio wprowadź następujące polecenia. W trakcie pisania tego artykułu bieżąca wersja pakietu Azure Active Directory to 3.10.305231913, więc Potwierdź najnowszą wersję i zaktualizuj ją zgodnie z wymaganiami.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

W kodzie aplikacji Utwórz klasę, aby pomieścić metodę uwierzytelniania Azure Active Directory. W tym przykładzie Klasa **jest nazywana**narzędziami. Dodaj następującą `using` instrukcję:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Następnie Dodaj następującą metodę, aby pobrać token JWT z Azure Active Directory. Aby zapewnić łatwość utrzymania, możesz chcieć przenieść stałe kodowane wartości ciągu do konfiguracji sieci Web lub aplikacji.

```csharp
public async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);

    ClientCredential clientCred = new ClientCredential("<AzureADApplicationClientID>","<AzureADApplicationClientKey>");

    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)

    throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```

Dodaj kod niezbędny do wywołania Key Vault i pobrania wartości klucza tajnego. Najpierw należy dodać następującą `using` instrukcję:

```csharp
using Microsoft.Azure.KeyVault;
```

Dodaj wywołania metody w celu wywołania Key Vault i pobrania klucza tajnego. W tej metodzie podajesz identyfikator URI tajny zapisany w poprzednim kroku. Zwróć uwagę na użycie metody **GetToken** **z utworzonej wcześniej klasy narzędzia** .

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po uruchomieniu aplikacji należy teraz uwierzytelniać się w usłudze Azure Active Directory a następnie pobrać wartość klucza tajnego z Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotacja kluczy przy użyciu Azure Automation

> [!IMPORTANT]
> Elementy Runbook Azure Automation nadal wymagają użycia `AzureRM` modułu.

Teraz można przystąpić do konfigurowania strategii rotacji dla wartości przechowywanych jako Key Vault Secret. Wpisy tajne można obracać na kilka sposobów:

- W ramach procesu ręcznego
- Programowo przy użyciu wywołań interfejsu API
- Za pomocą skryptu Azure Automation

Na potrzeby tego artykułu użyjesz programu PowerShell połączonego z Azure Automation, aby zmienić klucz dostępu konta usługi Azure Storage. Następnie należy zaktualizować klucz tajny magazynu kluczy przy użyciu nowego klucza.

Aby zezwolić Azure Automation na ustawianie wartości tajnych w magazynie kluczy, należy uzyskać identyfikator klienta dla połączenia o nazwie **AzureRunAsConnection**. To połączenie zostało utworzone po ustanowieniu wystąpienia Azure Automation. Aby znaleźć ten identyfikator, wybierz pozycję **elementy zawartości** z wystąpienia Azure Automation. W tym miejscu wybierz pozycję **połączenia**, a następnie wybierz nazwę główną usługi **AzureRunAsConnection** . Zanotuj wartość w polu Identyfikator **aplikacji** .

![Identyfikator klienta Azure Automation](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

W obszarze **zasoby**wybierz pozycję **moduły**. Wybierz pozycję **Galeria**, a następnie wyszukaj i zaimportuj zaktualizowane wersje każdego z następujących modułów:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Na piśmie z tego artykułu tylko wcześniej wymienione moduły wymagały aktualizacji dla następującego skryptu. Jeśli zadanie automatyzacji zakończy się niepowodzeniem, upewnij się, że zaimportowano wszystkie wymagane moduły i ich zależności.

Po pobraniu identyfikatora aplikacji dla połączenia Azure Automation należy poinformować Magazyn kluczy, że ta aplikacja ma uprawnienia do aktualizowania wpisów tajnych w magazynie. Użyj następującego polecenia programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Następnie wybierz pozycję **elementy Runbook** w obszarze Azure Automation, a następnie wybierz pozycję **Dodaj element Runbook**. Wybierz pozycję **Szybkie tworzenie**. Nadaj nazwę elementowi Runbook, a następnie wybierz opcję **PowerShell** jako typ elementu Runbook. Możesz dodać opis. Na koniec wybierz pozycję **Utwórz**.

![Tworzenie elementu runbook](../media/keyvault-keyrotation/Create_Runbook.png)

Wklej następujący skrypt programu PowerShell w okienku edytora dla nowego elementu Runbook:

```powershell
$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection"
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Connect-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
    "Login complete."
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}

# Optionally you can set the following as parameters
$StorageAccountName = <storageAccountName>
$RGName = <storageAccountResourceGroupName>
$VaultName = <keyVaultName>
$SecretName = <keyVaultSecretName>

#Key name. For example key1 or key2 for the storage account
New-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName -KeyName "key2" -Verbose
$SAKeys = Get-AzureRmStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName

$secretvalue = ConvertTo-SecureString $SAKeys[1].Value -AsPlainText -Force

$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secretvalue
```

W okienku Edytora wybierz pozycję **okienko testowania** , aby przetestować skrypt. Gdy skrypt zostanie uruchomiony bez błędu, można wybrać pozycję **Publikuj**, a następnie zastosować harmonogram dla elementu Runbook w okienku Konfiguracja elementu Runbook.

## <a name="key-vault-auditing-pipeline"></a>Key Vault potoku inspekcji

Podczas konfigurowania magazynu kluczy można włączyć inspekcję w celu zbierania dzienników w przypadku żądań dostępu wysyłanych do magazynu kluczy. Te dzienniki są przechowywane na wydzielonym koncie usługi Azure Storage i mogą być ściągane, monitorowane i analizowane. W poniższym scenariuszu usługa Azure Functions, Azure Logic Apps i dzienniki inspekcji magazynu kluczy są używane do tworzenia potoku, który wysyła wiadomość e-mail, gdy aplikacja niezgodna z IDENTYFIKATORem aplikacji w aplikacji sieci Web pobiera wpisy tajne z magazynu.

Najpierw należy włączyć rejestrowanie w magazynie kluczy. Użyj następujących poleceń programu PowerShell. (W tym artykule znajdują się szczegółowe informacje [o rejestrowaniu magazynu kluczy](../general/logging.md)).

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po włączeniu rejestrowania dzienniki inspekcji zaczynają być przechowywane na wskazanym koncie magazynu. Te dzienniki zawierają zdarzenia dotyczące sposobu i czasu dostępu do Twoich magazynów kluczy oraz przez kogo.

> [!NOTE]
> Możesz uzyskać dostęp do informacji o rejestrowaniu 10 minut po operacji magazynu kluczy. Często będzie ona dostępna wcześniej.

Następnym krokiem jest [utworzenie kolejki Azure Service Bus](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ta Kolejka wskazuje, że dzienniki inspekcji magazynu kluczy są wypychane. Gdy komunikaty dziennika inspekcji znajdują się w kolejce, aplikacja logiki wybiera je i wykonuje na nich działania. Utwórz wystąpienie Service Bus, wykonując następujące czynności:

1. Utwórz Service Bus przestrzeń nazw (Jeśli masz już tę, której chcesz użyć, przejdź do kroku 2).
2. Przejdź do wystąpienia Service Bus w Azure Portal i wybierz przestrzeń nazw, w której chcesz utworzyć kolejkę.
3. Wybierz pozycję **Utwórz zasób** > **integracja dla przedsiębiorstw** > **Service Bus**, a następnie wprowadź wymagane szczegóły.
4. Znajdź Service Bus informacje o połączeniu, wybierając przestrzeń nazw, a następnie wybierając pozycję **Informacje o połączeniu**. Te informacje będą potrzebne w następnej sekcji.

Następnie [Utwórz funkcję platformy Azure](../../azure-functions/functions-create-first-azure-function.md) w celu sondowania dzienników magazynu kluczy w ramach konta magazynu i pobrania nowych zdarzeń. Ta funkcja zostanie wyzwolona zgodnie z harmonogramem.

Aby utworzyć aplikację funkcji platformy Azure, wybierz pozycję **Utwórz zasób**, Wyszukaj w witrynie Marketplace **aplikacja funkcji**, a następnie wybierz pozycję **Utwórz**. Podczas tworzenia możesz użyć istniejącego planu hostingu lub utworzyć nowy. Możesz również wybrać opcję hostingu dynamicznego. Aby uzyskać więcej informacji na temat opcji hostingu dla Azure Functions, zobacz [Jak skalować Azure Functions](../../azure-functions/functions-scale.md).

Po utworzeniu aplikacji funkcji platformy Azure przejdź do niej, a następnie wybierz scenariusz **czasomierz** i **C\# ** dla języka. Następnie wybierz pozycję **Utwórz tę funkcję**.

![Azure Functions blok początkowy](../media/keyvault-keyrotation/Azure_Functions_Start.png)

Na karcie **programowanie** Zamień kod Run. CSX na następujący:

```csharp
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.ServiceBus.Messaging;
using System.Text;

public static void Run(TimerInfo myTimer, TextReader inputBlob, TextWriter outputBlob, TraceWriter log)
{
    log.Info("Starting");

    CloudStorageAccount sourceStorageAccount = new CloudStorageAccount(new StorageCredentials("<STORAGE_ACCOUNT_NAME>", "<STORAGE_ACCOUNT_KEY>"), true);

    CloudBlobClient sourceCloudBlobClient = sourceStorageAccount.CreateCloudBlobClient();

    var connectionString = "<SERVICE_BUS_CONNECTION_STRING>";
    var queueName = "<SERVICE_BUS_QUEUE_NAME>";

    var sbClient = QueueClient.CreateFromConnectionString(connectionString, queueName);

    DateTime dtPrev = DateTime.UtcNow;
    if(inputBlob != null)
    {
        var txt = inputBlob.ReadToEnd();

        if(!string.IsNullOrEmpty(txt))
        {
            dtPrev = DateTime.Parse(txt);
            log.Verbose($"SyncPoint: {dtPrev.ToString("O")}");
        }
        else
        {
            dtPrev = DateTime.UtcNow;
            log.Verbose($"Sync point file didn't have a date. Setting to now.");
        }
    }

    var now = DateTime.UtcNow;

    string blobPrefix = "insights-logs-auditevent/resourceId=/SUBSCRIPTIONS/<SUBSCRIPTION_ID>/RESOURCEGROUPS/<RESOURCE_GROUP_NAME>/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/<KEY_VAULT_NAME>/y=" + now.Year +"/m="+now.Month.ToString("D2")+"/d="+ (now.Day).ToString("D2")+"/h="+(now.Hour).ToString("D2")+"/m=00/";

    log.Info($"Scanning:  {blobPrefix}");

    IEnumerable<IListBlobItem> blobs = sourceCloudBlobClient.ListBlobs(blobPrefix, true);

    log.Info($"found {blobs.Count()} blobs");

    foreach(var item in blobs)
    {
        if (item is CloudBlockBlob)
        {
            CloudBlockBlob blockBlob = (CloudBlockBlob)item;

            log.Info($"Syncing: {item.Uri}");

            string sharedAccessUri = GetContainerSasUri(blockBlob);

            CloudBlockBlob sourceBlob = new CloudBlockBlob(new Uri(sharedAccessUri));

            string text;
            using (var memoryStream = new MemoryStream())
            {
                sourceBlob.DownloadToStream(memoryStream);
                text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
            }

            dynamic dynJson = JsonConvert.DeserializeObject(text);

            //Required to order by time as they might not be in the file
            var results = ((IEnumerable<dynamic>) dynJson.records).OrderBy(p => p.time);

            foreach (var jsonItem in results)
            {
                DateTime dt = Convert.ToDateTime(jsonItem.time);

                if(dt>dtPrev){
                    log.Info($"{jsonItem.ToString()}");

                    var payloadStream = new MemoryStream(Encoding.UTF8.GetBytes(jsonItem.ToString()));
                    //When sending to ServiceBus, use the payloadStream and set keeporiginal to true
                    var message = new BrokeredMessage(payloadStream, true);
                    sbClient.Send(message);
                    dtPrev = dt;
                }
            }
        }
    }
    outputBlob.Write(dtPrev.ToString("o"));
}

static string GetContainerSasUri(CloudBlockBlob blob)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();

    sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
    sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
    sasConstraints.Permissions = SharedAccessBlobPermissions.Read;

    //Generate the shared access signature on the container, setting the constraints directly on the signature.
    string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

    //Return the URI string for the container, including the SAS token.
    return blob.Uri + sasBlobToken;
}
```

> [!NOTE]
> Zmień zmienne w powyższym kodzie, aby wskazywały na konto magazynu, w którym zapisywane są dzienniki magazynu kluczy, do wystąpienia Service Bus utworzonego wcześniej oraz do określonej ścieżki do dzienników magazynu kluczy.

Funkcja pobiera najnowszy plik dziennika z konta magazynu, w którym zapisywane są dzienniki magazynu kluczy, pobiera najnowsze zdarzenia z tego pliku i umieszcza je w kolejce Service Bus. 

Ponieważ pojedynczy plik może mieć wiele zdarzeń, należy utworzyć plik synchroniz. txt, który funkcja sprawdza także, aby określić sygnaturę czasową ostatniego zdarzenia, które zostało pobrane. Użycie tego pliku gwarantuje, że te same zdarzenia nie będą wypychane wielokrotnie. 

Plik Sync. txt zawiera sygnaturę czasową ostatniego zdarzenia. Po załadowaniu dzienników muszą one być sortowane na podstawie ich sygnatur czasowych, aby upewnić się, że są one prawidłowo uporządkowane.

W przypadku tej funkcji odwołujemy się do kilku dodatkowych bibliotek, które nie są dostępne w Azure Functions. Aby uwzględnić te biblioteki, musimy Azure Functions, aby ściągnąć je za pomocą narzędzia NuGet. W polu **kod** wybierz pozycję **Wyświetl pliki**.

![Opcja "Wyświetl pliki"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Dodaj plik o nazwie Project. JSON z następującą zawartością:

```json
    {
      "frameworks": {
        "net46":{
          "dependencies": {
                "WindowsAzure.Storage": "7.0.0",
                "WindowsAzure.ServiceBus":"3.2.2"
          }
        }
       }
    }
```

Po wybraniu opcji **zapisz**Azure Functions pobierze wymagane pliki binarne.

Przejdź do karty **integracja** i nadaj parametry Czasomierzowi zrozumiałą nazwę, która ma być używana w ramach funkcji. W poprzednim kodzie funkcja oczekuje czasomierza, który ma *zostać wywołany*. Określ [wyrażenie firmy CRONUS](../../app-service/webjobs-create.md#CreateScheduledCRON) dla czasomierza w następujący sposób `0 * * * * *`:. To wyrażenie spowoduje uruchomienie funkcji raz na minutę.

Na tej samej karcie **integracja** Dodaj dane wejściowe typu **Azure Blob Storage**. Dane wejściowe będą wskazywały na plik Sync. txt zawierający sygnaturę czasową ostatniego zdarzenia, przeszukiwanego przez funkcję. Te dane wejściowe będą dostępne w ramach funkcji przy użyciu nazwy parametru. W powyższym kodzie dane wejściowe magazynu obiektów blob platformy Azure oczekują nazwy parametru, który ma być *inputBlob*. Wybierz konto magazynu, na którym będzie znajdować się plik Sync. txt (może to być takie samo lub inne konto magazynu). W polu Ścieżka podaj ścieżkę do pliku w formacie `{container-name}/path/to/sync.txt`.

Dodaj dane wyjściowe typu **Azure Blob Storage**. Dane wyjściowe będą wskazywały plik Sync. txt, który został zdefiniowany w danych wejściowych. Dane wyjściowe są używane przez funkcję w celu zapisania sygnatury czasowej ostatniego zdarzenia. Poprzedni kod oczekuje, że ten parametr jest wywoływany *outputBlob*.

Funkcja jest teraz gotowa. Upewnij się, że przełączono z powrotem do karty **programowanie** i Zapisz kod. Sprawdź okno danych wyjściowych pod kątem błędów kompilacji i popraw je w razie potrzeby. W przypadku kompilowania kodu, kod powinien teraz sprawdzać dzienniki magazynu kluczy co minutę i wypchnąć nowe zdarzenia do zdefiniowanej kolejki Service Bus. Informacje o rejestrowaniu powinny być widoczne w oknie dziennika przy każdym wyzwoleniu funkcji.

### <a name="azure-logic-app"></a>Aplikacja logiki platformy Azure

Następnie musisz utworzyć aplikację logiki platformy Azure, która pobiera zdarzenia, które funkcja jest wypychana do kolejki Service Bus, analizuje zawartość i wysyła wiadomość e-mail na podstawie dopasowanego warunku.

[Utwórz aplikację logiki](../../logic-apps/quickstart-create-first-logic-app-workflow.md) , wybierając kolejno pozycje **Utwórz zasób** > **Integration** > **Aplikacje logiki**.

Po utworzeniu aplikacji logiki przejdź do niej, a następnie wybierz pozycję **Edytuj**. W edytorze aplikacji logiki wybierz **kolejno pozycje Service Bus Queue** i wprowadź poświadczenia Service Bus, aby połączyć je z kolejką.

![Usługa Azure Logic App Service Bus](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Wybierz pozycję **Dodaj warunek**. W warunku Przełącz się do edytora zaawansowanego i wprowadź Poniższy kod. Zastąp *app_id* wartością rzeczywistego identyfikatora aplikacji sieci Web:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

To wyrażenie zasadniczo zwraca **wartość false** , jeśli *identyfikator appid* ze zdarzenia przychodzącego (który jest treścią komunikatu Service Bus) nie jest *identyfikatorem* aplikacji.

Teraz Utwórz akcję w obszarze **Jeśli nie, nic nie rób**.

![Azure Logic Apps wybierz akcję](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Dla akcji wybierz pozycję **Office 365 — Wyślij wiadomość e-mail**. Wypełnij pola, aby utworzyć wiadomość e-mail do wysłania, gdy zdefiniowany warunek zwróci **wartość false**. Jeśli nie masz pakietu Office 365, poszukaj rozwiązań alternatywnych, aby osiągnąć te same wyniki.

Masz teraz kompleksowy potok, który wyszukuje nowe dzienniki inspekcji magazynu kluczy raz na minutę. Spowoduje to wypchnięcie nowych dzienników znalezionych do kolejki Service Bus. Aplikacja logiki jest wyzwalana po pojawieniu się nowej wiadomości w kolejce. Jeśli identyfikator *AppID* w ramach zdarzenia nie pasuje do identyfikatora aplikacji wywołującej, wysyła wiadomość e-mail.
