---
title: Uruchamianie Azure Automation elementu Runbook z elementu webhook
description: W tym artykule opisano sposób używania elementu webhook do uruchamiania elementu Runbook w Azure Automation z wywołania HTTP.
services: automation
ms.subservice: process-automation
ms.date: 03/18/2021
ms.topic: conceptual
ms.openlocfilehash: c46a8753c87e981d9e3d6ecdd698bbbe6cba9894
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775786"
---
# <a name="start-a-runbook-from-a-webhook"></a>Uruchamianie elementu runbook z poziomu elementu webhook

Element webhook umożliwia zewnętrznej usłudze uruchamianie określonego elementu Runbook w Azure Automation za pośrednictwem pojedynczego żądania HTTP. Usługi zewnętrzne obejmują Azure DevOps Services, GitHub, dzienniki Azure Monitor i aplikacje niestandardowe. Taka usługa może użyć elementu webhook do uruchomienia elementu Runbook bez implementacji pełnego interfejsu API Azure Automation. Elementy webhook można porównać z innymi metodami uruchamiania elementu Runbook w programie w [Azure Automation](./start-runbooks.md).

> [!NOTE]
> Korzystanie z elementu webhook do uruchamiania elementów Runbook języka Python nie jest obsługiwane.

![WebhooksOverview](media/automation-webhooks/webhook-overview-image.png)

Aby zrozumieć wymagania klienta dotyczące protokołu TLS 1,2 z elementami webhook, zobacz [Wymuszanie protokołu tls 1,2 dla Azure Automation](automation-managing-data.md#tls-12-enforcement-for-azure-automation).

## <a name="webhook-properties"></a>Właściwości elementu webhook

W poniższej tabeli opisano właściwości, które należy skonfigurować dla elementu webhook.

| Właściwość | Opis |
|:--- |:--- |
| Nazwa |Nazwa elementu webhook. Możesz podać dowolną nazwę, ponieważ nie jest ona udostępniana Klientowi. Jest on używany tylko do identyfikowania elementu Runbook w Azure Automation. Najlepszym rozwiązaniem jest nadanie elementu webhook nazwy powiązanej z klientem, który go używa. |
| Adres URL |Adres URL elementu webhook. Jest to unikatowy adres wywoływany przez klienta z WPISem HTTP w celu uruchomienia elementu Runbook połączonego z elementem webhook. Jest on generowany automatycznie podczas tworzenia elementu webhook. Nie można określić niestandardowego adresu URL. <br> <br> Adres URL zawiera token zabezpieczający, który umożliwia systemowi innej firmy wywoływanie elementu Runbook bez żadnego dodatkowego uwierzytelniania. Z tego powodu należy traktować adres URL, na przykład hasło. Ze względów bezpieczeństwa można wyświetlić tylko adres URL w Azure Portal podczas tworzenia elementu webhook. Zanotuj adres URL w bezpiecznej lokalizacji do użytku w przyszłości. |
| Data wygaśnięcia | Data wygaśnięcia elementu webhook, po upływie którego nie można już jej używać. Datę wygaśnięcia można zmodyfikować po utworzeniu elementu webhook, o ile element webhook nie wygasł. |
| Enabled (Włączony) | Ustawienie wskazujące, czy element webhook jest domyślnie włączony podczas jego tworzenia. Ustawienie tej właściwości na wyłączone spowoduje, że żaden klient nie będzie mógł używać elementu webhook. Tę właściwość można ustawić podczas tworzenia elementu webhook lub dowolnego innego czasu po jego utworzeniu. |

## <a name="parameters-used-when-the-webhook-starts-a-runbook"></a>Parametry używane, gdy element webhook uruchamia element Runbook

Element webhook może definiować wartości parametrów elementu Runbook, które są używane podczas uruchamiania elementu Runbook. Element webhook musi zawierać wartości parametrów obowiązkowych elementu Runbook i może zawierać wartości parametrów opcjonalnych. Wartość parametru skonfigurowana dla elementu webhook można modyfikować nawet po utworzeniu elementu webhook. Wiele elementów webhook połączonych z pojedynczym elementem Runbook może używać różnych wartości parametrów elementu Runbook. Gdy klient uruchamia element Runbook przy użyciu elementu webhook, nie może zastąpić wartości parametrów zdefiniowanych w elemencie webhook.

Aby odbierać dane od klienta, element Runbook obsługuje jeden parametr o nazwie `WebhookData` . Ten parametr definiuje obiekt zawierający dane, które klient dołącza do żądania POST.

![Właściwości WebhookData](media/automation-webhooks/webhook-data-properties.png)

`WebhookData`Parametr ma następujące właściwości:

| Właściwość | Opis |
|:--- |:--- |
| `WebhookName` | Nazwa elementu webhook. |
| `RequestHeader` | Hashtable zawierający nagłówki przychodzącego żądania POST. |
| `RequestBody` | Treść przychodzącego żądania POST. Ta treść zachowuje dowolne formatowanie danych, takie jak String, JSON, XML lub kodowane w postaci formy. Element Runbook musi być zapisany, aby można było korzystać z oczekiwanego formatu danych. |

Nie istnieje konfiguracja elementu webhook wymaganego do obsługi `WebhookData` parametru, a element Runbook nie jest wymagany do jego zaakceptowania. Jeśli element Runbook nie definiuje parametru, wszystkie szczegóły żądania wysyłanego z klienta zostaną zignorowane.

> [!NOTE]
> Podczas wywoływania elementu webhook klient powinien zawsze przechowywać wszystkie wartości parametrów w przypadku niepowodzenia wywołania. Jeśli wystąpi awaria sieci lub problem z połączeniem, aplikacja nie może pobrać nieudanych wywołań elementu webhook.

Jeśli określisz wartość dla `WebhookData` tworzenia elementu webhook, zostanie ona zastąpiona, gdy element webhook rozpocznie działanie elementu Runbook przy użyciu danych z żądania post klienta. Dzieje się tak nawet wtedy, gdy aplikacja nie zawiera żadnych danych w treści żądania. 

W przypadku uruchomienia elementu Runbook, który definiuje `WebhookData` użycie mechanizmu innego niż element webhook, można podać wartość `WebhookData` , którą rozpoznaje element Runbook. Ta wartość powinna być obiektem z tymi samymi [właściwościami](#webhook-properties) co `WebhookData` parametr, aby element Runbook mógł pracować z nim tak samo, jak działa z rzeczywistymi `WebhookData` obiektami przekazaną przez element webhook.

Na przykład, jeśli uruchamiasz następujący element Runbook z Azure Portal i chcesz przekazać niektóre przykładowe dane elementu webhook do testowania, musisz przekazać dane w formacie JSON w interfejsie użytkownika.

![WebhookData parametr z interfejsu użytkownika](media/automation-webhooks/WebhookData-parameter-from-UI.png)

Dla następnego przykładu elementu Runbook zdefiniujmy następujące właściwości dla `WebhookData` :

* **Element webhookname**: MyWebhook
* **Elemencie requestbody**: `*[{'ResourceGroup': 'myResourceGroup','Name': 'vm01'},{'ResourceGroup': 'myResourceGroup','Name': 'vm02'}]*`

Teraz przekażemy następujący obiekt JSON w interfejsie użytkownika dla `WebhookData` parametru. Ten przykład z znakami powrotu karetki i znaków nowego wiersza pasuje do formatu, który jest przesyłany z elementu webhook.

```json
{"WebhookName":"mywebhook","RequestBody":"[\r\n {\r\n \"ResourceGroup\": \"vm01\",\r\n \"Name\": \"vm01\"\r\n },\r\n {\r\n \"ResourceGroup\": \"vm02\",\r\n \"Name\": \"vm02\"\r\n }\r\n]"}
```

![Uruchom parametr WebhookData z interfejsu użytkownika](media/automation-webhooks/Start-WebhookData-parameter-from-UI.png)

> [!NOTE]
> Azure Automation rejestruje wartości wszystkich parametrów wejściowych za pomocą zadania elementu Runbook. W rezultacie wszystkie dane wejściowe dostarczone przez klienta w żądaniu elementu webhook są rejestrowane i dostępne dla wszystkich użytkowników mających dostęp do zadania automatyzacji. Z tego powodu należy zachować ostrożność wraz z uwzględnieniem poufnych informacji w wywołaniach elementu webhook.

## <a name="webhook-security"></a>Zabezpieczenia elementu webhook

Bezpieczeństwo elementu webhook polega na poufności jego adresu URL, który zawiera token zabezpieczający, który umożliwia wywołanie elementu webhook. Azure Automation nie wykonuje żadnych uwierzytelnień na żądanie tak długo, jak jest wprowadzony prawidłowy adres URL. Z tego powodu klienci nie powinni używać elementu webhook dla elementów Runbook, które wykonują wysoce poufne operacje bez użycia alternatywnego sposobu weryfikacji żądania.

Należy wziąć pod uwagę następujące strategie:

* Można uwzględnić logikę w elemencie Runbook, aby określić, czy jest wywoływana przez element webhook. Element Runbook sprawdza `WebhookName` Właściwość `WebhookData` parametru. Element Runbook może przeprowadzić dalsze sprawdzanie poprawności, szukając określonych informacji we `RequestHeader` `RequestBody` właściwościach i.

* Czy element Runbook ma wykonać weryfikację zewnętrznego warunku, gdy odbierze żądanie elementu webhook. Rozważmy na przykład element Runbook, który jest wywoływany przez witrynę GitHub w dowolnym momencie, gdy istnieje nowe zatwierdzenie do repozytorium GitHub. Element Runbook może połączyć się z usługą GitHub, aby zweryfikować, że nastąpiło nowe zatwierdzenie przed kontynuowaniem.

* Azure Automation obsługuje znaczniki usługi sieci wirtualnej platformy Azure, w tym [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). Możesz użyć tagów usługi do definiowania kontroli dostępu do sieci dla [sieciowych grup zabezpieczeń](../virtual-network/network-security-groups-overview.md#security-rules) lub [zapory platformy Azure](../firewall/service-tags.md) i wyzwalania elementów webhook z poziomu sieci wirtualnej. Podczas tworzenia reguł zabezpieczeń można używać tagów usługi zamiast określonych adresów IP. Określając nazwę tagu usługi **GuestAndHybridManagement**  w odpowiednim polu źródłowym lub docelowym reguły, można zezwolić na ruch dla usługi Automation lub go odrzucić. Ten tag usługi nie obsługuje bardziej szczegółowej kontroli przez ograniczenie zakresów adresów IP do określonego regionu.

## <a name="create-a-webhook"></a>Tworzenie elementu webhook

Poniższa procedura umożliwia utworzenie nowego elementu webhook połączonego z elementem Runbook w Azure Portal.

1. Na stronie elementy Runbook w Azure Portal kliknij element Runbook, który zostanie uruchomiony przez element webhook, aby wyświetlić szczegóły elementu Runbook. Upewnij się, że pole **stan** elementu Runbook ma ustawioną wartość **opublikowany**.
2. Kliknij pozycję **element webhook** w górnej części strony, aby otworzyć stronę Dodawanie elementu webhook.
3. Kliknij pozycję **Utwórz nowy element webhook** , aby otworzyć stronę Tworzenie elementu webhook.
4. Wypełnij pola **Nazwa** i **Data wygaśnięcia** dla elementu webhook i określ, czy powinien być włączony. Aby uzyskać więcej informacji o tych właściwościach, zobacz [właściwości elementu webhook](#webhook-properties) .
5. Kliknij ikonę kopiowania i naciśnij klawisze CTRL + C, aby skopiować adres URL elementu webhook. Następnie zapisz go w bezpiecznym miejscu. 

    > [!IMPORTANT]
    > Po utworzeniu elementu webhook nie można ponownie pobrać adresu URL. Upewnij się, że skopiujesz i zapiszesz ją jak powyżej.

   ![Adres URL elementu webhook](media/automation-webhooks/copy-webhook-url.png)

1. Kliknij pozycję **Parametry** , aby podać wartości parametrów elementu Runbook. Jeśli element Runbook ma obowiązkowe parametry, nie można utworzyć elementu webhook, chyba że podano wartości.

2. Kliknij pozycję **Utwórz**, aby utworzyć element webhook.

## <a name="use-a-webhook"></a>Używanie elementu webhook

Aby można było użyć elementu webhook po jego utworzeniu, klient musi wydać `POST` żądanie HTTP z adresem URL elementu webhook. Składnia jest następująca:

```http
http://<Webhook Server>/token?=<Token Value>
```

Klient otrzymuje jeden z następujących kodów powrotu z `POST` żądania.

| Kod | Tekst | Opis |
|:--- |:--- |:--- |
| 202 |Zaakceptowano |Żądanie zostało zaakceptowane, a element Runbook został pomyślnie umieszczony w kolejce. |
| 400 |Nieprawidłowe żądanie |Żądanie nie zostało zaakceptowane z jednego z następujących powodów: <ul> <li>Element webhook wygasł.</li> <li>Element webhook jest wyłączony.</li> <li>Token w adresie URL jest nieprawidłowy.</li>  </ul> |
| 404 |Nie znaleziono |Żądanie nie zostało zaakceptowane z jednego z następujących powodów: <ul> <li>Nie znaleziono elementu webhook.</li> <li>Nie znaleziono elementu Runbook.</li> <li>Nie znaleziono konta.</li>  </ul> |
| 500 |Wewnętrzny błąd serwera |Adres URL był prawidłowy, ale wystąpił błąd. Prześlij ponownie żądanie. |

Przy założeniu, że żądanie zakończy się pomyślnie, odpowiedź elementu webhook zawiera identyfikator zadania w formacie JSON, jak pokazano poniżej. Zawiera on pojedynczy identyfikator zadania, ale format JSON pozwala na potencjalne udoskonalenia w przyszłości.

```json
{"JobIds":["<JobId>"]}
```

Klient nie może określić momentu zakończenia zadania elementu Runbook lub jego stanu ukończenia z elementu webhook. Informacje te można znaleźć za pomocą identyfikatora zadania innego mechanizmu, takiego jak [Windows PowerShell](/powershell/module/servicemanagement/azure.service/get-azureautomationjob) lub [interfejsu API Azure Automation](/rest/api/automation/job).

### <a name="use-a-webhook-from-an-arm-template"></a>Używanie elementu webhook z szablonu ARM

Elementy webhook usługi Automation mogą być również wywoływane przez [szablony Azure Resource Manager (ARM)](/azure/azure-resource-manager/templates/overview). Szablon ARM wysyła `POST` żądanie i odbiera kod powrotny podobnie jak każdy inny klient. Zobacz [Używanie elementu webhook](#use-a-webhook).

   > [!NOTE]
   > Ze względów bezpieczeństwa identyfikator URI jest zwracany tylko przy pierwszym wdrożeniu szablonu.

Ten przykładowy szablon umożliwia utworzenie środowiska testowego i zwrócenie identyfikatora URI dla tworzonego elementu webhook.

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

## <a name="renew-a-webhook"></a>Odnawianie elementu webhook

Po utworzeniu elementu webhook obowiązuje okres ważności wynoszący dziesięć lat, po upływie którego zostanie automatycznie wygaśnie. Po wygaśnięciu elementu webhook nie można go uaktywnić ponownie. Można go usunąć, a następnie utworzyć ponownie. 

Można rozciągnąć element webhook, który nie osiągnął czasu wygaśnięcia. Aby rozwinąć element webhook:

1. Przejdź do elementu Runbook, który zawiera element webhook. 
2. Wybierz pozycję elementy **webhook** w obszarze **zasoby**. 
3. Kliknij element webhook, który chcesz zwiększyć. 
4. Na stronie elementu webhook wybierz nową datę i godzinę wygaśnięcia, a następnie kliknij przycisk **Zapisz**.

## <a name="sample-runbook"></a>Przykładowy element Runbook

Następujący przykładowy element Runbook akceptuje dane elementu webhook i uruchamia maszyny wirtualne określone w treści żądania. Aby przetestować ten element Runbook, na koncie usługi Automation w obszarze **elementy Runbook** kliknij pozycję **Utwórz element Runbook**. Jeśli nie wiesz, jak utworzyć element Runbook, zobacz [Tworzenie elementu Runbook](automation-quickstart-create-runbook.md).

> [!NOTE]
> Dla niegraficznych elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są aliasami dla polecenia [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.

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

Poniższy przykład używa środowiska Windows PowerShell, aby uruchomić element Runbook za pomocą elementu webhook. Dowolny język, który może spowodować żądanie HTTP, może korzystać z elementu webhook. W przykładzie użyto programu Windows PowerShell.

Element Runbook oczekuje listy maszyn wirtualnych sformatowanych w formacie JSON w treści żądania. Element Runbook weryfikuje, a nagłówki zawierają zdefiniowany komunikat, aby sprawdzić, czy obiekt wywołujący elementu webhook jest prawidłowy.

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

Poniższy przykład przedstawia treść żądania, które jest dostępne dla elementu Runbook we `RequestBody` właściwości `WebhookData` . Ta wartość jest formatowana w formacie JSON, aby była zgodna z formatem zawartym w treści żądania.

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

Na poniższej ilustracji przedstawiono żądanie wysyłane z programu Windows PowerShell i otrzymanej odpowiedzi. Identyfikator zadania jest wyodrębniany z odpowiedzi i konwertowany na ciąg.

![Przycisk elementów webhook](media/automation-webhooks/webhook-request-response.png)

## <a name="next-steps"></a>Następne kroki

* Aby wyzwolić element Runbook z poziomu alertu, zobacz [Używanie alertu do wyzwalania elementu runbook Azure Automation](automation-create-alert-triggered-runbook.md).