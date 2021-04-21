---
title: Uruchamianie Azure Automation runbook z elementem webhook
description: W tym artykule opisano sposób używania element webhook do uruchamiania element runbook w Azure Automation wywołaniu HTTP.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 338fb56c4af5c24b7b746ffd6508c2fe7d52b131
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830200"
---
# <a name="start-a-runbook-from-a-webhook"></a>Uruchamianie elementu runbook z poziomu elementu webhook

Element webhook umożliwia usłudze zewnętrznej uruchamianie określonego Azure Automation za pośrednictwem pojedynczego żądania HTTP. Usługi zewnętrzne obejmują Azure DevOps Services, GitHub, Azure Monitor i aplikacje niestandardowe. Taka usługa może używać webhook do uruchamiania runbook bez implementowania pełnego interfejsu API Azure Automation API. Możesz porównać webhook z innymi metodami uruchamiania runbook w części Uruchamianie runbook w [programie Azure Automation](./start-runbooks.md).

> [!NOTE]
> Uruchamianie runbook języka Python przy użyciu webhook nie jest obsługiwane.

![WebhookOverview](media/automation-webhooks/webhook-overview-image.png)

Aby poznać wymagania klienta dotyczące usługi TLS 1.2 z webhookami, zobacz wymuszanie [TLS 1.2](automation-managing-data.md#tls-12-enforcement-for-azure-automation)dla Azure Automation .

## <a name="webhook-properties"></a>Właściwości obiektu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla obiektu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa |Nazwa urządzenia webhook. Możesz podać dowolną nazwę, ponieważ nie jest ona ujawniona klientowi. Jest on używany tylko do identyfikowania runbook w Azure Automation. Najlepszym rozwiązaniem jest nadaj mu nazwę powiązaną z klientem, który go używa. |
| Adres URL |Adres URL tego webhook. Jest to unikatowy adres, który klient wywołuje za pomocą żądania POST protokołu HTTP, aby uruchomić element runbook połączony z elementem webhook. Jest on generowany automatycznie podczas tworzenia webhook. Nie można określić niestandardowego adresu URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia systemowi innej firmy wywoływanie runbook bez dalszego uwierzytelniania. Z tego powodu adres URL należy traktować jak hasło. Ze względów bezpieczeństwa adres URL można wyświetlić tylko w Azure Portal podczas tworzenia tego linku. Zanotuj adres URL w bezpiecznej lokalizacji do użycia w przyszłości. |
| Data wygaśnięcia | Data wygaśnięcia tego webhook, po którym nie można go już używać. Możesz zmodyfikować datę wygaśnięcia po utworzeniu webhook, o ile nie wygasł. |
| Enabled (Włączony) | Ustawienie wskazujące, czy po utworzeniu jest on domyślnie włączony. Jeśli ustawisz tę właściwość na wartość Wyłączone, żaden klient nie będzie może używać tego obiektu webhook. Tę właściwość można ustawić podczas tworzenia obiektu webhook lub w dowolnym innym momencie po jego utworzeniu. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametry używane podczas uruchamiania runbook przez ten webhook

Webhook może definiować wartości parametrów runbook, które są używane podczas uruchamiania. Element webhook musi zawierać wartości wszystkich obowiązkowych parametrów runbook i może zawierać wartości parametrów opcjonalnych. Wartość parametru skonfigurowaną jako webhook można zmodyfikować nawet po utworzeniu tego typu. Wiele elementów webhook połączonych z pojedynczymi elementów runbook może używać różnych wartości parametrów elementów Runbook. Gdy klient uruchamia runbook przy użyciu webhook, nie może zastąpić wartości parametrów zdefiniowanych w tym webhook.

Aby odbierać dane od klienta, runbook obsługuje pojedynczy parametr o nazwie `WebhookData` . Ten parametr definiuje obiekt zawierający dane, które klient zawiera w żądaniu POST.

![Właściwości webhookData](media/automation-webhooks/webhook-data-properties.png)

Parametr `WebhookData` ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| `WebhookName` | Nazwa urządzenia webhook. |
| `RequestHeader` | Tabela skrótów zawierająca nagłówki przychodzącego żądania POST. |
| `RequestBody` | Treść przychodzącego żądania POST. Ta treść zachowuje dowolne formatowanie danych, takie jak ciąg, JSON, XML lub zakodowane w postaci. Aby można było pracować z oczekiwanym formatem danych, należy go napisano w języku Runbook. |

Do obsługi parametru nie jest wymagana żadna konfiguracja urządzenia webhook, a do jego zaakceptowania nie jest `WebhookData` wymagany. Jeśli runbook nie definiuje parametru, wszystkie szczegóły żądania wysyłanego od klienta są ignorowane.

> [!NOTE]
> Podczas wywoływania webhook klient powinien zawsze przechowywać wszystkie wartości parametrów w przypadku niepowodzenia wywołania. Jeśli występuje problem z siecią lub połączeniem, aplikacja nie może pobrać nieudanych wywołań webhook.

Jeśli określisz wartość dla podczas tworzenia webhook, zostanie ona zastąpiona, gdy ten webhook uruchomi runbook przy użyciu danych z żądania `WebhookData` POST klienta. Dzieje się tak nawet wtedy, gdy aplikacja nie zawiera żadnych danych w treści żądania. 

Jeśli uruchamiasz runbook, który definiuje przy użyciu mechanizmu innego niż webhook, możesz podać wartość `WebhookData` rozpoznaną `WebhookData` przez ten typ. Ta wartość powinna być obiektem o tych samych właściwościach co parametr, aby element runbook działał z nim tak samo jak z rzeczywistymi obiektami przekazanymi przez element [](#webhook-properties) `WebhookData` `WebhookData` webhook.

Jeśli na przykład uruchamiasz następujący elementy Runbook z interfejsu Azure Portal i chcesz przekazać przykładowe dane elementy webhook do testowania, musisz przekazać dane w for sposób JSON w interfejsie użytkownika.

![Parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

W następnym przykładzie runbook zdefiniujmy następujące właściwości dla `WebhookData` :

* **WebhookName:** MyWebhook
* **RequestBody:**`*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Teraz przekażemy następujący obiekt JSON w interfejsie użytkownika dla `WebhookData` parametru . Ten przykład, ze znakami powrotu karetki i znakami nowego linii, pasuje do formatu przekazywanego zhook webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Uruchamianie parametru WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation rejestruje wartości wszystkich parametrów wejściowych za pomocą zadania runbook. W związku z tym wszystkie dane wejściowe dostarczone przez klienta w żądaniu dotyczącym webhook są rejestrowane i dostępne dla każdej osoby z dostępem do zadania automatyzacji. Z tego powodu należy zachować ostrożność w zakresie do uwzględnienia poufnych informacji w wywołaniach webhook.

## <a name="webhook-security"></a>Zabezpieczenia webhook

Bezpieczeństwo elementy webhook zależy od prywatności jego adresu URL, który zawiera token zabezpieczający umożliwiający wywoływanie tego elementy. Azure Automation nie wykonuje żadnego uwierzytelniania w żądaniu, o ile jest ono dokonywane pod prawidłowym adresem URL. Z tego powodu klienci nie powinni używać elementami webhook dla elementami Runbook, które wykonują operacje wysoce poufne bez użycia alternatywnej metody sprawdzania poprawności żądania.

Rozważ następujące strategie:

* Logikę można dołączyć do runbook, aby określić, czy jest on wywoływany przez webhook. Za pomocą runbook sprawdź `WebhookName` właściwość `WebhookData` parametru . Ten runbook może przeprowadzić dalszą walidację, szukając określonych informacji we `RequestHeader` właściwościach `RequestBody` i .

* Element runbook musi wykonać weryfikację warunku zewnętrznego po otrzymaniu żądania element webhook. Rozważmy na przykład runbook, który jest wywoływany przez serwis GitHub za każdym razem, gdy w repozytorium GitHub zostanie uruchomione nowe zatwierdzenie. Przed kontynuowaniem ten runbook może połączyć się z serwisem GitHub, aby sprawdzić, czy nastąpiło nowe zatwierdzenie.

* Azure Automation obsługuje tagi usługi sieci wirtualnej platformy Azure, w szczególności [GuestAndHybridManagement.](../virtual-network/service-tags-overview.md) Tagi usług mogą być używane do [](../virtual-network/network-security-groups-overview.md#security-rules) definiowania kontroli dostępu do sieci w sieciowych grupach zabezpieczeń [lub](../firewall/service-tags.md) Azure Firewall i wyzwalania elementów webhook z poziomu sieci wirtualnej. Tagi usługi mogą być używane w miejsce określonych adresów IP podczas tworzenia reguł zabezpieczeń. Określając nazwę tagu usługi **GuestAndHybridManagement**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla usługi Automation lub go odrzucić. Ten tag usługi nie obsługuje zezwalania na bardziej szczegółową kontrolę przez ograniczenie zakresów adresów IP do określonego regionu.

## <a name="create-a-webhook"></a>Tworzeniehook webhook

Użyj poniższej procedury, aby utworzyć nowy elementy webhook połączone z elementyem Runbook w Azure Portal.

1. Na stronie Runbook w witrynie Azure Portal kliknij pozycję Runbook, który uruchamia się w celu wyświetlenia szczegółów tego runbook. Upewnij się, że pole **Stan runbook** ma wartość **Opublikowano.**
2. Kliknij **pozycję Webhook** w górnej części strony, aby otworzyć stronę Dodawanie.
3. Kliknij **pozycję Utwórz nowy webhook,** aby otworzyć stronę Tworzenie.
4. Wypełnij pola **Nazwa i** Data **wygaśnięcia** dla urządzenia webhook i określ, czy ma być włączony. Aby uzyskać więcej informacji na temat tych właściwości, zobacz Właściwości obiektu [webhook.](#webhook-properties)
5. Kliknij ikonę kopiowania i naciśnij klawisze Ctrl+C, aby skopiować adres URL webhook. Następnie zanotuj go w bezpiecznym miejscu. 

    > [!IMPORTANT]
    > Po utworzeniu tego webhook nie będzie można ponownie pobrać adresu URL. Upewnij się, że skopiuj i zanotuj je zgodnie z powyższymi informacjami.

   ![Webhook URL](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij **pozycję Parametry,** aby podać wartości parametrów runbook. Jeśli element Runbook ma obowiązkowe parametry, nie można utworzyć tego element webhook, chyba że zostaną podane wartości.

2. Kliknij pozycję **Utwórz**, aby utworzyć element webhook.

## <a name="use-a-webhook"></a>Korzystanie z webhook

Aby użyć element webhook po jego utworzeniu, klient musi wytworzyć żądanie HTTP z adresem URL tego `POST` elementu. Składnia jest następująca:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient otrzymuje jeden z następujących kodów powrotnych z `POST` żądania.

| Kod | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowano |Żądanie zostało zaakceptowane, a runbook został pomyślnie do kolejki. |
| 400 |Nieprawidłowe żądanie |Żądanie nie zostało zaakceptowane z jednej z następujących przyczyn: <ul> <li>Ten webhook wygasł.</li> <li>Ten webhook jest wyłączony.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Żądanie nie zostało zaakceptowane z jednej z następujących przyczyn: <ul> <li>Nie znaleziono tego webhook.</li> <li>Nie znaleziono tego runbook.</li> <li>Nie znaleziono konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL był prawidłowy, ale wystąpił błąd. Prześlij ponownie żądanie. |

Przy założeniu, że żądanie zostanie pomyślnie wyświetlone, odpowiedź z elementyem webhook zawiera identyfikator zadania w formacie JSON, jak pokazano poniżej. Zawiera on pojedynczy identyfikator zadania, ale format JSON umożliwia potencjalne przyszłe ulepszenia.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić, kiedy zadanie runbook zakończy się lub kiedy jego stan ukończenia będzie pochodzić z tego webhook. Te informacje można znaleźć przy użyciu identyfikatora zadania z innym mechanizmem, takim jak Windows PowerShell [lub](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) interfejs API [Azure Automation .](/rest/api/automation/job)

### <a name="use-a-webhook-from-an-arm-template"></a>Używanie element webhook z szablonu usługi ARM

Webhook usługi Automation można również wywoływać za [pomocą szablonów Azure Resource Manager (ARM).](/azure/azure-resource-manager/templates/overview) Szablon usługi ARM wysyła `POST` żądanie i otrzymuje kod powrotny, podobnie jak każdy inny klient. Zobacz [Use a webhook (Korzystanie z webhook).](#use-a-webhook)

   > [!NOTE]
   > Ze względów bezpieczeństwa ten URI jest zwracany tylko przy pierwszym wdrożeniu szablonu.

Ten przykładowy szablon tworzy środowisko testowe i zwraca wartość URI dla tego urządzenia webhook, który tworzy.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "automationAccountName": {
            "type": "String",
            "metadata": {
                "description": "Automation account name"
            }
        },
        "webhookName": {
            "type": "String",
            "metadata": {
                "description": "Webhook Name"
            }
        },
        "runbookName": {
            "type": "String",
            "metadata": {
                "description": "Runbook Name for which webhook will be created"
            }
        },
        "WebhookExpiryTime": {
            "type": "String",
            "metadata": {
                "description": "Webhook Expiry time"
            }
        },
        "_artifactsLocation": {
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
            "type": "String",
            "metadata": {
                "description": "URI to artifacts location"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Automation/automationAccounts",
            "apiVersion": "2020-01-13-preview",
            "name": "[parameters('automationAccountName')]",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": {
                    "name": "Free"
                }
            },
            "resources": [
                {
                    "type": "runbooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('runbookName')]",
                    "location": "[resourceGroup().location]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]"
                    ],
                    "properties": {
                        "runbookType": "Python2",
                        "logProgress": "false",
                        "logVerbose": "false",
                        "description": "Sample Runbook",
                        "publishContentLink": {
                            "uri": "[uri(parameters('_artifactsLocation'), 'scripts/AzureAutomationTutorialPython2.py')]",
                            "version": "1.0.0.0"
                        }
                    }
                },
                {
                    "type": "webhooks",
                    "apiVersion": "2018-06-30",
                    "name": "[parameters('webhookName')]",
                    "dependsOn": [
                        "[parameters('automationAccountName')]",
                        "[parameters('runbookName')]"
                    ],
                    "properties": {
                        "isEnabled": true,
                        "expiryTime": "[parameters('WebhookExpiryTime')]",
                        "runbook": {
                            "name": "[parameters('runbookName')]"
                        }
                    }
                }
            ]
        }
    ],
    "outputs": {
        "webhookUri": {
            "type": "String",
            "value": "[reference(parameters('webhookName')).uri]"
        }
    }
}
```

## <a name="renew-a-webhook"></a>Odnawianiehook webhook

Po utworzeniu webhook ma on okres ważności dziesięciu lat, po którym automatycznie wygasa. Po wygaśnięciu webhook nie można go ponownie uaktywnić. Można go tylko usunąć, a następnie utworzyć ponownie. 

Możesz rozszerzyć webhook, który nie osiągnął czasu wygaśnięcia. Aby rozszerzyć webhook:

1. Przejdź do runbook, który zawiera elementy webhook. 
2. Wybierz **pozycję Webhook w** obszarze **Zasoby.** 
3. Kliknij pozycję webhook, który chcesz rozszerzyć. 
4. Na stronie Webhook wybierz nową datę i czas wygaśnięcia, a następnie kliknij przycisk **Zapisz.**

## <a name="sample-runbook"></a>Przykładowy podręcznik Runbook

Poniższy przykładowy runbook akceptuje dane elementy webhook i uruchamia maszyny wirtualne określone w treści żądania. Aby przetestować ten podręcznik Runbook, na koncie usługi Automation w obszarze **Runbook** kliknij pozycję **Utwórz.** Jeśli nie wiesz, jak utworzyć runbook, zobacz [Creating a runbook (Tworzenie runbook).](automation-quickstart-create-runbook.md)

> [!NOTE]
> W przypadku niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami polecenia [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Możesz użyć tych polecenia cmdlet lub zaktualizować [moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto usługi Automation.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData
)

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

    # Check header for message to validate request
    if ($WebhookData.RequestHeader.message -eq 'StartedbyContoso')
    {
        Write-Output "Header has required information"}
    else
    {
        Write-Output "Header missing required information";
        exit;
    }

    # Retrieve VMs from Webhook request body
    $vms = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.

    Write-Output "Authenticating to Azure with service principal and certificate"
    $ConnectionAssetName = "AzureRunAsConnection"
    Write-Output "Get connection asset: $ConnectionAssetName"

    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null)
            {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Write-Output "Authenticating to Azure with service principal."
            Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Output

        # Start each virtual machine
        foreach ($vm in $vms)
        {
            $vmName = $vm.Name
            Write-Output "Starting $vmName"
            Start-AzVM -Name $vm.Name -ResourceGroup $vm.ResourceGroup
        }
}
else {
    # Error
    write-Error "This runbook is meant to be started from an Azure alert webhook only."
}
```

## <a name="test-the-sample"></a>Testowanie przykładu

W poniższym przykładzie użyto Windows PowerShell, aby uruchomić elementy Runbook przy użyciu elementy webhook. Każdy język, który może wykonać żądanie HTTP, może używać element webhook. Windows PowerShell jest tu używany jako przykład.

W treści żądania jest oczekiwana lista maszyn wirtualnych sformatowanych w formacie JSON. Elementy runbook również weryfikują, czy nagłówki zawierają zdefiniowany komunikat w celu sprawdzenia, czy wywołujący elementy webhook są prawidłowe.

```azurepowershell-interactive
$uri = "<webHook Uri>"

$vms  = @(
            @{ Name="vm01";ResourceGroup="vm01"},
            @{ Name="vm02";ResourceGroup="vm02"}
        )
$body = ConvertTo-Json -InputObject $vms
$header = @{ message="StartedbyContoso"}
$response = Invoke-WebRequest -Method Post -Uri $uri -Body $body -Headers $header
$jobid = (ConvertFrom-Json ($response.Content)).jobids[0]
```

W poniższym przykładzie pokazano treść żądania dostępnego dla tego obiektu Runbook we `RequestBody` właściwości `WebhookData` . Ta wartość jest formatowana w formacie JSON, aby była zgodna z formatem uwzględnionym w treści żądania.

```json
[
    {
        "Name":  "vm01",
        "ResourceGroup":  "myResourceGroup"
    },
    {
        "Name":  "vm02",
        "ResourceGroup":  "myResourceGroup"
    }
]
```

Na poniższej ilustracji przedstawiono żądanie wysyłane z Windows PowerShell i wynikową odpowiedź. Identyfikator zadania jest wyodrębniony z odpowiedzi i konwertowany na ciąg.

![Przycisk Webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Następne kroki

* Aby wyzwolić element Runbook z alertu, zobacz Używanie alertu do wyzwalania Azure Automation [runbook.](automation-create-alert-triggered-runbook.md)
