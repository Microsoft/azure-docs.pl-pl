---
title: Konfigurowanie usługi Azure Key Vault z rotacją i inspekcją kluczy end-to-end | Dokumenty firmy Microsoft
description: Skorzystaj z tego przewodnika, aby skonfigurować obrót klawiszy i monitorować dzienniki magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: ''
ms.service: key-vault
ms.subservice: secrets
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d2981495a256ce5fb8f8f3584e68ac91541f9d62
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430255"
---
# <a name="set-up-azure-key-vault-with-key-rotation-and-auditing"></a>Konfigurowanie usługi Azure Key Vault z rotacją kluczy i inspekcją

## <a name="introduction"></a>Wprowadzenie

Po przechowalni kluczy można rozpocząć używanie go do przechowywania kluczy i wpisów tajnych. Aplikacje nie muszą już zachowywać kluczy lub wpisów tajnych, ale mogą żądać ich od magazynu w razie potrzeby. Magazyn kluczy umożliwia aktualizowanie kluczy i wpisów tajnych bez wpływu na zachowanie aplikacji, co otwiera szeroki zakres możliwości zarządzania kluczami i kluczami tajnymi.

W tym artykule opisano, jak zaimplementować zaplanowane obracanie kluczy kont magazynu, monitorować dzienniki inspekcji magazynu kluczy i zgłaszać alerty po zaliczeniu nieoczekiwanych żądań. 

Najpierw należy utworzyć magazyn kluczy przy użyciu wybranej metody:

- [konfigurowanie i pobieranie wpisów tajnych z usługi Azure Key Vault przy użyciu interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
- [Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu programu Azure PowerShell](quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu witryny Azure portal](quick-create-portal.md)


## <a name="store-a-secret"></a>Przechowywanie tajemnicy

Aby umożliwić aplikacji pobranie klucza tajnego z usługi Key Vault, należy najpierw utworzyć klucz tajny i przekazać go do magazynu.

Uruchom sesję programu PowerShell Azure i zaloguj się na konto platformy Azure przy użyciu następującego polecenia:

```powershell
Connect-AzAccount
```

W wyskakującym oknie przeglądarki wprowadź nazwę użytkownika i hasło do konta platformy Azure. Program PowerShell otrzyma wszystkie subskrypcje skojarzone z tym kontem. Program PowerShell domyślnie używa pierwszego.

Jeśli masz wiele subskrypcji, może być trzeba określić ten, który został użyty do utworzenia magazynu kluczy. Wprowadź następujące elementy, aby wyświetlić subskrypcje konta:

```powershell
Get-AzSubscription
```

Aby określić subskrypcję skojarzoną z magazynem kluczy, który będzie rejestrowany, wprowadź:

```powershell
Set-AzContext -SubscriptionId <subscriptionID>
```

Ponieważ w tym artykule pokazano przechowywanie klucza konta magazynu jako klucz tajny, należy uzyskać ten klucz konta magazynu.

```powershell
Get-AzStorageAccountKey -ResourceGroupName <resourceGroupName> -Name <storageAccountName>
```

Po pobraniu klucza tajnego (w tym przypadku klucza konta magazynu) należy przekonwertować ten klucz na bezpieczny ciąg, a następnie utworzyć klucz tajny z tą wartością w magazynie kluczy.

```powershell
$secretvalue = ConvertTo-SecureString <storageAccountKey> -AsPlainText -Force

Set-AzKeyVaultSecret -VaultName <vaultName> -Name <secretName> -SecretValue $secretvalue
```

Następnie pobierz identyfikator URI dla utworzonego klucza tajnego. Ten identyfikator URI będzie potrzebny w późniejszym kroku, aby wywołać magazyn kluczy i pobrać klucz tajny. Uruchom następujące polecenie programu PowerShell i zanotuj wartość identyfikatora, która jest identyfikatorem URI klucza tajnego:

```powershell
Get-AzKeyVaultSecret –VaultName <vaultName>
```

## <a name="set-up-the-application"></a>Konfigurowanie aplikacji

Teraz, gdy masz klucz tajny przechowywany, można użyć kodu, aby pobrać i używać go po wykonaniu kilku kroków więcej.

Najpierw należy zarejestrować aplikację w usłudze Azure Active Directory. Następnie poinformuj magazyn kluczy o informacjach aplikacji, aby mogła zezwalać na żądania z aplikacji.

> [!NOTE]
> Aplikacja musi być utworzona w tej samej dzierżawie usługi Azure Active Directory co magazyn kluczy.

1. Otwórz **usługę Azure Active Directory**.
2. Wybierz pozycję **Rejestracje aplikacji**. 
3. Wybierz **nową rejestrację aplikacji,** aby dodać aplikację do usługi Azure Active Directory.

    ![Otwieranie aplikacji w usłudze Azure Active Directory](../media/keyvault-keyrotation/azure-ad-application.png)

4. W obszarze **Utwórz**pozostaw typ aplikacji jako **aplikacja /INTERFEJS API sieci Web** i nadaj aplikacji nazwę. Nadaj aplikacji **adres URL logowania**. Ten adres URL może być wszystkim, co chcesz dla tego demo.

    ![Tworzenie rejestracji aplikacji](../media/keyvault-keyrotation/create-app.png)

5. Po dodaniu aplikacji do usługi Azure Active Directory zostanie otwarta strona aplikacji. Wybierz **pozycję Ustawienia**, a następnie wybierz pozycję **Właściwości**. Skopiuj wartość **identyfikatora aplikacji.** Będzie potrzebny w późniejszych krokach.

Następnie wygeneruj klucz dla aplikacji, aby mogła ona wchodzić w interakcje z usługą Azure Active Directory. Aby utworzyć klawisz, wybierz pozycję **Klawisze** w obszarze **Ustawienia**. Zanotuj nowo wygenerowany klucz aplikacji usługi Azure Active Directory. Będą one potrzebne w kolejnym kroku. Klucz nie będzie dostępny po opuszczeniu tej sekcji. 

![Klucze aplikacji usługi Azure Active Directory](../media/keyvault-keyrotation/create-key.png)

Przed ustanowieniem żadnych wywołań z aplikacji do magazynu kluczy, należy poinformować magazyn kluczy o aplikacji i jej uprawnień. Następujące polecenie używa nazwy magazynu i identyfikatora aplikacji z aplikacji usługi Azure Active Directory, aby udzielić aplikacji **Uzyskaj** dostęp do magazynu kluczy.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <clientIDfromAzureAD> -PermissionsToSecrets Get
```

Teraz możesz rozpocząć tworzenie wywołań aplikacji. W aplikacji należy zainstalować pakiety NuGet, które są wymagane do interakcji z usługą Azure Key Vault i usługi Azure Active Directory. W konsoli Programu Visual Studio Package Manager wprowadź następujące polecenia. Podczas pisania tego artykułu bieżąca wersja pakietu usługi Azure Active Directory to 3.10.305231913, więc potwierdź najnowszą wersję i zaktualizuj w razie potrzeby.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 3.10.305231913

Install-Package Microsoft.Azure.KeyVault
```

W kodzie aplikacji utwórz klasę, aby pomieścić metodę uwierzytelniania usługi Azure Active Directory. W tym przykładzie ta klasa nosi nazwę **Utils**. Dodaj następującą `using` instrukcję:

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Następnie dodaj następującą metodę, aby pobrać token JWT z usługi Azure Active Directory. Aby zapewnić możliwość konserwacji, można przenieść wartości ciągów zakodowanych na stałe do konfiguracji sieci web lub aplikacji.

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

Dodaj kod niezbędny do wywołania usługi Key Vault i odzyskania wartości tajnej. Najpierw należy dodać następującą `using` instrukcję:

```csharp
using Microsoft.Azure.KeyVault;
```

Dodaj wywołania metody, aby wywołać magazyn kluczy i pobrać klucz tajny. W tej metodzie należy podać tajny identyfikator URI, który został zapisany w poprzednim kroku. Należy zwrócić uwagę na użycie **GetToken** metody z **Utils** klasy utworzonej wcześniej.

```csharp
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));

var sec = kv.GetSecretAsync(<SecretID>).Result.Value;
```

Po uruchomieniu aplikacji, teraz należy uwierzytelnić do usługi Azure Active Directory, a następnie pobieranie wartości tajnej z usługi Azure Key Vault.

## <a name="key-rotation-using-azure-automation"></a>Rotacja kluczy przy użyciu automatyzacji platformy Azure

> [!IMPORTANT]
> Elementy runbook usługi Azure Automation `AzureRM` nadal wymagają użycia modułu.

Teraz możesz skonfigurować strategię rotacji dla wartości, które przechowujesz jako wpisy tajne magazynu kluczy. Wpisy tajne można obracać na kilka sposobów:

- W ramach procesu ręcznego
- Programowo przy użyciu wywołań INTERFEJSU API
- Za pośrednictwem skryptu usługi Azure Automation

Na potrzeby tego artykułu użyjesz programu PowerShell w połączeniu z usługą Azure Automation, aby zmienić klucz dostępu konta magazynu platformy Azure. Następnie zaktualizujesz klucz tajny magazynu kluczy za pomocą tego nowego klucza.

Aby umożliwić usłudze Azure Automation ustawianie wartości tajnych w magazynie kluczy, należy uzyskać identyfikator klienta dla połączenia o nazwie **AzureRunAsConnection**. To połączenie zostało utworzone podczas ustanawiania wystąpienia usługi Azure Automation. Aby znaleźć ten identyfikator, wybierz **zasoby** z wystąpienia usługi Azure Automation. W tym miejscu wybierz pozycję **Połączenia**, a następnie wybierz jednostkę usługi **AzureRunAsConnection.** Zanotuj wartość **Identyfikator aplikacji.**

![Identyfikator klienta usługi Azure Automation](../media/keyvault-keyrotation/Azure_Automation_ClientID.png)

W **obszarze Zasoby**wybierz pozycję **Moduły**. Wybierz **pozycję Galeria**, a następnie wyszukaj i zaimportuj zaktualizowane wersje każdego z następujących modułów:

    Azure
    Azure.Storage
    AzureRM.Profile
    AzureRM.KeyVault
    AzureRM.Automation
    AzureRM.Storage

> [!NOTE]
> Podczas pisania tego artykułu tylko wcześniej wymienione moduły musiały zostać zaktualizowane dla następującego skryptu. Jeśli zadanie automatyzacji nie powiedzie się, upewnij się, że zostały zaimportowane wszystkie niezbędne moduły i ich zależności.

Po pobraniu identyfikatora aplikacji dla połączenia usługi Azure Automation należy poinformować magazyn kluczy, że ta aplikacja ma uprawnienia do aktualizowania wpisów tajnych w magazynie. Użyj następującego polecenia programu PowerShell:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName <vaultName> -ServicePrincipalName <applicationIDfromAzureAutomation> -PermissionsToSecrets Set
```

Następnie wybierz pozycję **Elementy runbook** w wystąpieniu usługi Azure Automation, a następnie wybierz pozycję **Dodaj zestaw runbook**. Wybierz pozycję **Szybkie tworzenie**. Nadaj nazwę emujcztownikowi i wybierz program **PowerShell** jako typ przewodniczącego. Możesz dodać opis. Na koniec wybierz pozycję **Utwórz**.

![Tworzenie elementu runbook](../media/keyvault-keyrotation/Create_Runbook.png)

Wklej następujący skrypt programu PowerShell w okienku edytora dla nowego elementów runbook:

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

W okienku edytora wybierz **okienko testu,** aby przetestować skrypt. Po uruchomieniu skryptu bez błędu można wybrać pozycję **Publikuj**, a następnie można zastosować harmonogram dla systemu runbook w okienku konfiguracji systemu runbook.

## <a name="key-vault-auditing-pipeline"></a>Potok inspekcji magazynu kluczy

Podczas konfigurowania magazynu kluczy można włączyć inspekcję w celu zbierania dzienników żądań dostępu do magazynu kluczy. Te dzienniki są przechowywane na wyznaczonym koncie magazynu platformy Azure i mogą być wyciągane, monitorowane i analizowane. W poniższym scenariuszu używa funkcji platformy Azure, aplikacji logiki platformy Azure i dzienników inspekcji magazynu kluczy, aby utworzyć potok, który wysyła wiadomość e-mail, gdy aplikacja, która nie pasuje do identyfikatora aplikacji sieci web pobiera wpisy tajne z magazynu.

Najpierw należy włączyć rejestrowanie w magazynie kluczy. Użyj następujących poleceń programu PowerShell. (Możesz zobaczyć wszystkie szczegóły w [tym artykule na temat rejestrowania magazynu kluczy](../general/logging.md).)

```powershell
$sa = New-AzStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Type Standard\_LRS -Location 'East US'
$kv = Get-AzKeyVault -VaultName '<vaultName>'
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Po włączeniu rejestrowania dzienniki inspekcji zaczynają być przechowywane na wyznaczonym koncie magazynu. Te dzienniki zawierają zdarzenia dotyczące sposobu i kiedy są dostępne magazyny kluczy i przez kogo.

> [!NOTE]
> Dostęp do informacji rejestrowania można uzyskać 10 minut po operacji magazynu kluczy. Często będzie dostępny wcześniej.

Następnym krokiem jest [utworzenie kolejki usługi Azure Service Bus](../../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md). Ta kolejka jest miejscem, w którym są wypychane dzienniki inspekcji magazynu kluczy. Gdy komunikaty dziennika inspekcji znajdują się w kolejce, aplikacja logiki odbiera je i działa na nich. Utwórz wystąpienie usługi Service Bus, wykonując następujące czynności:

1. Utwórz obszar nazw usługi Service Bus (jeśli masz już taki, którego chcesz użyć, przejdź do kroku 2).
2. Przejdź do wystąpienia usługi Service Bus w witrynie Azure portal i wybierz obszar nazw, w której chcesz utworzyć kolejkę.
3. Wybierz **pozycję Utwórz zasób** > **Magistrala usługi****integracji** > przedsiębiorstwa, a następnie wprowadź wymagane szczegóły.
4. Informacje o połączeniu usługi Service Bus można znaleźć, wybierając obszar nazw, a następnie wybierając pozycję **Informacje o połączeniu**. Te informacje będą potrzebne do następnej sekcji.

Następnie [utwórz funkcję platformy Azure,](../../azure-functions/functions-create-first-azure-function.md) aby sondować dzienniki magazynu kluczy na koncie magazynu i odbierać nowe zdarzenia. Ta funkcja zostanie wyzwolona zgodnie z harmonogramem.

Aby utworzyć aplikację funkcji platformy Azure, wybierz pozycję **Utwórz zasób**, wyszukaj w portalu Marketplace **aplikację funkcji,** a następnie wybierz pozycję **Utwórz**. Podczas tworzenia można użyć istniejącego planu hostingowego lub utworzyć nowy. Możesz również zdecydować się na hosting dynamiczny. Aby uzyskać więcej informacji na temat opcji hostingu dla usług Azure Functions, zobacz [Jak skalować usługi Azure Functions](../../azure-functions/functions-scale.md).

Po utworzeniu aplikacji funkcji platformy Azure przejdź do niej i wybierz scenariusz **czasomierza** i **C\# ** dla języka. Następnie wybierz **pozycję Utwórz tę funkcję**.

![Blok Uruchamianie funkcji Platformy Azure](../media/keyvault-keyrotation/Azure_Functions_Start.png)

Na karcie **Rozwijanie** zastąp kod pliku run.csx następującymi:

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
> Change the variables in the preceding code to point to your storage account where the key vault logs are written, to the Service Bus instance you created earlier, and to the specific path to the key-vault storage logs.

Funkcja pobiera najnowszy plik dziennika z konta magazynu, na którym są zapisywane dzienniki magazynu kluczy, pobiera najnowsze zdarzenia z tego pliku i wypycha je do kolejki usługi Service Bus. 

Ponieważ pojedynczy plik może mieć wiele zdarzeń, należy utworzyć plik sync.txt, który funkcja również analizuje, aby określić sygnaturę czasową ostatniego zdarzenia, które zostało odebrane. Użycie tego pliku gwarantuje, że nie wypchniesz tego samego zdarzenia wiele razy. 

Plik sync.txt zawiera sygnaturę czasową dla ostatniego zdarzenia. Po załadowaniu dzienników muszą być sortowane na podstawie ich sygnatur czasowych, aby upewnić się, że są one uporządkowane poprawnie.

W przypadku tej funkcji odwołujemy się do kilku dodatkowych bibliotek, które nie są dostępne po wyjęciu z pudełka w usłudze Azure Functions. Aby uwzględnić te biblioteki, musimy usługi Azure Functions do ciągnięcia ich przy użyciu NuGet. W polu **Kod** wybierz pozycję **Wyświetl pliki**.

![Opcja "Wyświetl pliki"](../media/keyvault-keyrotation/Azure_Functions_ViewFiles.png)

Dodaj plik o nazwie project.json z następującą zawartością:

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

Po **wybraniu opcji Zapisz**usługa Azure Functions pobierze wymagane pliki binarne.

Przełącz się na kartę **Integruj** i nadaj parametrowi czasomierza znaczącą nazwę do użycia w ramach funkcji. W poprzednim kodzie funkcja oczekuje, że czasomierz będzie nazywany *myTimer*. Określ [wyrażenie CRON](../../app-service/webjobs-create.md#CreateScheduledCRON) dla czasomierza w następujący sposób: `0 * * * * *`. To wyrażenie spowoduje, że funkcja będzie działać raz na minutę.

Na tej samej karcie **Integracja** dodaj dane wejściowe typu **Magazyn obiektów Blob platformy Azure**. To dane wejściowe wskażą plik sync.txt, który zawiera sygnaturę czasową ostatniego zdarzenia, którą funkcja jest analizowana. To dane wejściowe będą dostępne w ramach funkcji przy użyciu nazwy parametru. W poprzednim kodzie dane wejściowe magazynu obiektów Blob platformy Azure oczekuje, że nazwa parametru ma być *inputBlob*. Wybierz konto magazynu, na którym będzie znajdować się plik sync.txt (może to być to samo lub inne konto magazynu). W polu ścieżki podaj ścieżkę do `{container-name}/path/to/sync.txt`pliku w formacie .

Dodaj dane wyjściowe typu **magazyn obiektów blob platformy Azure**. To dane wyjściowe wskażą plik sync.txt zdefiniowany w danych wejściowych. To dane wyjściowe jest używany przez funkcję do zapisu sygnatury czasowej ostatniego zdarzenia spojrzał. Poprzedni kod oczekuje, że ten parametr będzie nazywany *outputBlob*.

Funkcja jest teraz gotowa. Upewnij się, aby przełączyć się z powrotem do **rozwijania** kartę i zapisać kod. Sprawdź okno danych wyjściowych pod kątem błędów kompilacji i popraw je w razie potrzeby. Jeśli kod zostanie skompilowany, kod powinien teraz sprawdzać dzienniki magazynu kluczy co minutę i wypychać nowe zdarzenia do zdefiniowanej kolejki usługi Service Bus. Za każdym razem, gdy funkcja jest wyzwalana, powinny być widoczne zapisywanie informacji zapisywanych w oknie dziennika.

### <a name="azure-logic-app"></a>Aplikacja logiki platformy Azure

Następnie należy utworzyć aplikację logiki platformy Azure, która odbiera zdarzenia, które funkcja jest wypychanie do kolejki usługi Service Bus, analizuje zawartość i wysyła wiadomość e-mail na podstawie warunku są zgodne.

[Utwórz aplikację logiki,](../../logic-apps/quickstart-create-first-logic-app-workflow.md) wybierając **pozycję Utwórz aplikację** > **logiki****integracji** > zasobów .

Po utworzeniu aplikacji logiki przejdź do niej i wybierz pozycję **Edytuj**. W edytorze aplikacji logiki wybierz pozycję **Kolejka usługi Service Bus** i wprowadź poświadczenia usługi Service Bus, aby połączyć ją z kolejką.

![Azure Logic App Service Bus](../media/keyvault-keyrotation/Azure_LogicApp_ServiceBus.png)

Wybierz **pozycję Dodaj warunek**. W warunkach przełącz się do edytora zaawansowanego i wprowadź następujący kod. Zastąp *APP_ID* rzeczywistym identyfikatorem aplikacji internetowej:

```
@equals('<APP_ID>', json(decodeBase64(triggerBody()['ContentData']))['identity']['claim']['appid'])
```

To wyrażenie zasadniczo zwraca **false,** jeśli *appid* z przychodzącego zdarzenia (który jest treścią komunikatu usługi Service Bus) nie jest *appid* aplikacji.

Teraz utwórz akcję w obszarze **JEŚLI NIE, NIC NIE RÓB**.

![Usługa Azure Logic Apps wybiera akcję](../media/keyvault-keyrotation/Azure_LogicApp_Condition.png)

Aby uzyskać tę czynność, wybierz pozycję **Office 365 - wyślij wiadomość e-mail**. Wypełnij pola, aby utworzyć wiadomość e-mail do wysłania, gdy zdefiniowany warunek zwróci **wartość false**. Jeśli nie masz usługi Office 365, poszukaj alternatyw, aby osiągnąć takie same wyniki.

Masz teraz potok end-to-end, który szuka nowych dzienników inspekcji magazynu kluczy raz na minutę. Wypycha nowe dzienniki, które znajdzie do kolejki usługi Service Bus. Aplikacja logiki jest wyzwalana, gdy nowa wiadomość ląduje w kolejce. Jeśli *appid* w ramach zdarzenia nie jest zgodny z identyfikatorem aplikacji wywołującej aplikacji, wysyła wiadomość e-mail.
