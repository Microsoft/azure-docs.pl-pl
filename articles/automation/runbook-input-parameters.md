---
title: Konfigurowanie parametrów wejściowych elementu Runbook w Azure Automation
description: W tym artykule opisano sposób konfigurowania parametrów wejściowych elementu Runbook, które umożliwiają przekazywanie danych do elementu Runbook po jego uruchomieniu.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: 73e4dbb24b4e7c0c651f7d082c75b0f4a17158b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98890885"
---
# <a name="configure-runbook-input-parameters"></a>Konfigurowanie parametrów wejściowych elementu runbook

Parametry wejściowe elementu Runbook zwiększają elastyczność elementu Runbook, umożliwiając przekazywanie danych do niego po jego uruchomieniu. Te parametry umożliwiają działanie elementu Runbook dla określonych scenariuszy i środowisk. W tym artykule opisano konfigurację i użycie parametrów wejściowych w elementach Runbook.

Można skonfigurować parametry wejściowe dla programu PowerShell, przepływu pracy programu PowerShell, grafiki i elementów Runbook w języku Python. Element Runbook może mieć wiele parametrów z różnymi typami danych lub nie ma żadnych parametrów. Parametry wejściowe mogą być obowiązkowe lub opcjonalne i można użyć wartości domyślnych dla parametrów opcjonalnych.

Po uruchomieniu można przypisać wartości do parametrów wejściowych dla elementu Runbook. Element Runbook można uruchomić z poziomu Azure Portal, usługi sieci Web lub programu PowerShell. Możesz również uruchomić jeden jako podrzędny element Runbook, który jest wywoływany w innym elemencie Runbook.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach Runbook programu PowerShell

Elementy Runbook programu PowerShell i programu PowerShell w programie Azure Automation obsługują parametry wejściowe, które są zdefiniowane za pomocą następujących właściwości. 

| **Właściwość** | **Opis** |
|:--- |:--- |
| Typ |Wymagane. Oczekiwano typu danych dla wartości parametru. Dowolny typ .NET jest prawidłowy. |
| Nazwa |Wymagane. Nazwa parametru. Ta nazwa musi być unikatowa w elemencie Runbook, musi zaczynać się od litery i może zawierać tylko litery, cyfry lub znaki podkreślenia. |
| Obowiązkowy |Opcjonalny. Wartość logiczna określająca, czy parametr wymaga wartości. W przypadku wybrania tej opcji na wartość true należy podać wartość, gdy element Runbook zostanie uruchomiony. W przypadku ustawienia tej opcji na wartość false wartość jest opcjonalna. Jeśli nie określisz wartości dla `Mandatory` właściwości, program PowerShell traktuje parametr wejściowy opcjonalny domyślnie. |
| Wartość domyślna |Opcjonalny. Wartość, która jest używana dla parametru, jeśli podczas uruchamiania elementu Runbook nie zostanie przeniesiona żadna wartość wejściowa. Element Runbook może ustawić wartość domyślną dla każdego parametru. |

Program Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych niż wymienione powyżej, takich jak Walidacja, aliasy i zestawy parametrów. Jednak Azure Automation obecnie obsługuje tylko wymienione właściwości parametru wejściowego.

Na przykład przyjrzyjmy się definicji parametrów w elemencie Runbook przepływu pracy programu PowerShell. Ta definicja ma następujący ogólny formularz, w którym wiele parametrów jest oddzielonych przecinkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Teraz Skonfigurujmy parametry wejściowe dla elementu Runbook przepływu pracy programu PowerShell, który wyprowadza szczegóły dotyczące maszyn wirtualnych, pojedynczej maszyny wirtualnej lub wszystkich maszyn wirtualnych w grupie zasobów. Ten element Runbook ma dwa parametry, jak pokazano na poniższym zrzucie ekranu: Nazwa maszyny wirtualnej ( `VMName` ) i nazwa grupy zasobów ( `resourceGroupName` ).

![Przepływ pracy programu PowerShell automatyzacji](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W tej definicji parametru parametry wejściowe są prostymi parametrami typu String.

Należy pamiętać, że elementy Runbook programu PowerShell i programu PowerShell obsługują wszystkie typy proste i typy złożone, takie jak `Object` lub `PSCredential` dla parametrów wejściowych. Jeśli element Runbook ma parametr wejściowy obiektu, musisz użyć elementu Hashtable programu PowerShell z parami nazwa-wartość, aby przekazać wartość. Na przykład w elemencie Runbook znajduje się następujący parametr.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

W takim przypadku można przekazać następującą wartość do parametru.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Jeśli nie przekażesz wartości do opcjonalnego parametru ciągu z wartością domyślną NULL, wartość parametru jest pustym ciągiem zamiast wartością null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurowanie parametrów wejściowych w graficznych elementach Runbook

Aby zilustrować konfigurację parametrów wejściowych dla graficznego elementu Runbook, Utwórzmy element Runbook, który będzie wyprowadzał szczegółowe informacje o maszynach wirtualnych, pojedynczą maszynę wirtualną lub wszystkie maszyny wirtualne w grupie zasobów. Aby uzyskać szczegółowe informacje, zobacz [mój pierwszy graficzny element Runbook](./learn/automation-tutorial-runbook-graphical.md).

Graficzny element Runbook używa tych głównych działań elementu Runbook:

* Konfiguracja konta Uruchom jako platformy Azure do uwierzytelniania na platformie Azure. 
* Definicja polecenia cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) , aby uzyskać właściwości maszyny wirtualnej.
* Użycie działania [Write-Output](/powershell/module/microsoft.powershell.utility/write-output) do wyprowadzania nazw maszyn wirtualnych. 

`Get-AzVM`Działanie definiuje dwa dane wejściowe, nazwę maszyny wirtualnej i nazwę grupy zasobów. Ponieważ te nazwy mogą być różne przy każdym uruchomieniu elementu Runbook, należy dodać parametry wejściowe do elementu Runbook, aby akceptowały te dane wejściowe. Zapoznaj się z tematem [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).

Wykonaj następujące kroki, aby skonfigurować parametry wejściowe.

1. Na stronie elementy Runbook wybierz graficzny element Runbook, a następnie kliknij przycisk **Edytuj**.
2. W edytorze graficznym kliknij przycisk **dane wejściowe i wyjściowe** , a następnie **Dodaj dane wejściowe** , aby otworzyć okienko parametru wejściowego elementu Runbook.

   ![Graficzny element Runbook automatyzacji](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Kontrolka dane wejściowe i wyjściowe wyświetla listę parametrów wejściowych, które są zdefiniowane dla elementu Runbook. W tym miejscu możesz dodać nowy parametr wejściowy lub edytować konfigurację istniejącego parametru wejściowego. Aby dodać nowy parametr dla elementu Runbook, kliknij przycisk **Dodaj dane wejściowe** , aby otworzyć blok **parametru wejściowego elementu Runbook** , w którym można skonfigurować parametry przy użyciu właściwości zdefiniowanych w temacie [Tworzenie graficzne w Azure Automation](automation-graphical-authoring-intro.md).

    ![Dodaj nowe dane wejściowe](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry o następujących właściwościach, które mają być używane przez `Get-AzVM` działanie, a następnie kliknij przycisk **OK**.

   * Parametr 1:
        * **Nazwa**  --  **VMName**
        * **Typ** — ciąg
        * **Wymagane**  --  **Nie**

   * Parametr 2:
        * **Nazwa**  --  **resourceGroupName**
        * **Typ** — ciąg
        * **Wymagane**  --  **Nie**
        * **Wartość domyślna**  --  **Niestandardowe**
        * Niestandardowa wartość domyślna — nazwa grupy zasobów zawierającej maszyny wirtualne

5. Wyświetl parametry w kontrolce dane wejściowe i wyjściowe. 
6. Kliknij ponownie przycisk **OK** , a następnie kliknij przycisk **Zapisz**.
7. Kliknij przycisk **Opublikuj** , aby opublikować element Runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurowanie parametrów wejściowych w elementach Runbook języka Python

W przeciwieństwie do programu PowerShell, przepływu pracy programu PowerShell i graficznych elementów Runbook, elementy Runbook języka Python nie pobierają parametrów nazwanych. Edytor elementów Runbook analizuje wszystkie parametry wejściowe jako tablicę wartości argumentów. Możesz uzyskać dostęp do tablicy przez zaimportowanie `sys` modułu do skryptu języka Python, a następnie użycie `sys.argv` tablicy. Należy zauważyć, że pierwszy element tablicy, `sys.argv[0]` , jest nazwą skryptu. W związku z tym pierwszy rzeczywisty parametr wejściowy to `sys.argv[1]` .

Aby zapoznać się z przykładem użycia parametrów wejściowych w elemencie Runbook w języku Python, zobacz [mój pierwszy element Runbook w języku Python w Azure Automation](./learn/automation-tutorial-runbook-textual-python2.md).

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Przypisywanie wartości do parametrów wejściowych w elementach Runbook

W tej sekcji opisano kilka sposobów przekazywania wartości do parametrów wejściowych w elementach Runbook. Można przypisać wartości parametrów, gdy:

* [Uruchamianie elementu Runbook](#start-a-runbook-and-assign-parameters)
* [Testowanie elementu Runbook](#test-a-runbook-and-assign-parameters)
* [Łączenie harmonogramu dla elementu Runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Tworzenie elementu webhook dla elementu runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchamianie elementu Runbook i przypisywanie parametrów

Element Runbook można uruchomić na wiele sposobów: za pomocą Azure Portal, z elementem webhook, za pomocą poleceń cmdlet programu PowerShell, z interfejsem API REST lub z zestawem SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Uruchom opublikowany element Runbook przy użyciu Azure Portal i przypisz parametry

Po [uruchomieniu elementu Runbook](start-runbooks.md#start-a-runbook-with-the-azure-portal) w Azure Portal zostanie otwarty blok **Uruchamianie elementu Runbook** , w którym można wprowadzić wartości parametrów, które zostały utworzone.

![Zacznij korzystać z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

W etykiecie poniżej pola wejściowego można zobaczyć właściwości, które zostały ustawione do definiowania atrybutów parametrów, na przykład obowiązkowe lub opcjonalne, typ, wartość domyślna. Dymek pomocy obok nazwy parametru definiuje również informacje o kluczach, które są konieczne, aby podejmować decyzje dotyczące wartości wejściowych parametrów. 

> [!NOTE]
> Parametry ciągu obsługują puste wartości typu String. Wprowadzenie `[EmptyString]` w polu parametru wejściowego przekazuje pusty ciąg do parametru. Ponadto parametry ciągu nie obsługują wartości null. Jeśli żadna wartość nie zostanie przekazana do parametru ciągu, program PowerShell interpretuje ją jako wartość null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Uruchom opublikowany element Runbook za pomocą poleceń cmdlet programu PowerShell i przypisz parametry

* **Polecenia cmdlet Azure Resource Manager:** Można uruchomić element Runbook usługi Automation, który został utworzony w grupie zasobów za pomocą polecenia [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Polecenia cmdlet klasycznego modelu wdrażania platformy Azure:** Można uruchomić element Runbook usługi Automation, który został utworzony w domyślnej grupie zasobów za pomocą polecenia [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook).
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Po uruchomieniu elementu Runbook za pomocą poleceń cmdlet programu PowerShell zostanie utworzony domyślny parametr, który `MicrosoftApplicationManagementStartedBy` ma wartość `PowerShell` . Ten parametr można wyświetlić w okienku Szczegóły zadania.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Uruchamianie elementu Runbook przy użyciu zestawu SDK i przypisywanie parametrów

* **Metoda Azure Resource Manager:** Element Runbook można uruchomić przy użyciu zestawu SDK języka programowania. Poniżej znajduje się fragment kodu języka C# służący do uruchamiania elementu Runbook na koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
   public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
      {
        var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
         {
            Properties = new JobCreateProperties
             {
                Runbook = new RunbookAssociationProperty
                 {
                   Name = runbookName
                 },
                   Parameters = parameters
             }
         });
      return response.Job;
      }
   ```

* **Metoda klasycznego modelu wdrażania platformy Azure:** Element Runbook można uruchomić za pomocą zestawu SDK języka programowania. Poniżej znajduje się fragment kodu języka C# służący do uruchamiania elementu Runbook na koncie usługi Automation. Możesz wyświetlić cały kod w naszym [repozytorium GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs).

   ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
    {
      var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
         {
           Runbook = new RunbookAssociationProperty
         {
           Name = runbookName
              },
                Parameters = parameters
              }
       });
      return response.Job;
    }
   ```

   Aby rozpocząć tę metodę, Utwórz słownik do przechowywania parametrów elementu Runbook `VMName` i  `resourceGroupName` ich wartości. Następnie Uruchom element Runbook. Poniżej znajduje się fragment kodu w języku C# służący do wywoływania metody, która została zdefiniowana powyżej.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Uruchamianie elementu Runbook przy użyciu interfejsu API REST i przypisywanie parametrów

Można utworzyć i uruchomić zadanie elementu Runbook za pomocą interfejsu API REST Azure Automation przy użyciu `PUT` metody z następującym identyfikatorem URI żądania: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

W identyfikatorze URI żądania Zastąp następujące parametry:

* `subscriptionId`: Identyfikator subskrypcji platformy Azure.  
* `resourceGroupName`: Nazwa grupy zasobów dla konta usługi Automation.
* `automationAccountName`: Nazwa konta usługi Automation hostowanego w ramach określonej usługi w chmurze.  
* `jobName`: Identyfikator GUID zadania. Identyfikatory GUID w programie PowerShell można tworzyć za pomocą polecenia `[GUID]::NewGuid().ToString()*` .

Aby przekazać parametry do zadania elementu Runbook, użyj treści żądania. W formacie JSON dostępne są następujące informacje:

* Nazwa elementu Runbook: wymagane. Nazwa elementu Runbook do uruchomienia zadania.  
* Parametry elementu Runbook: opcjonalne. Słownik listy parametrów w formacie (nazwa, wartość), gdzie nazwa jest typu ciąg, a wartość może być dowolną prawidłową wartością JSON.

Jeśli chcesz uruchomić element Runbook **Get-AzureVMTextual** utworzony wcześniej za pomocą `VMName` parametrów i `resourceGroupName` jako parametry, użyj następującego formatu JSON dla treści żądania.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

Kod stanu HTTP 201 jest zwracany, jeśli zadanie zostało pomyślnie utworzone. Aby uzyskać więcej informacji na temat nagłówków odpowiedzi i treści odpowiedzi, zobacz [Tworzenie zadania elementu Runbook za pomocą interfejsu API REST](/rest/api/automation/job/create).

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie elementu Runbook i przypisywanie parametrów

W przypadku [testowania wersji roboczej elementu Runbook](./manage-runbooks.md) przy użyciu opcji test zostanie otwarta strona test. Ta strona służy do konfigurowania wartości parametrów, które zostały utworzone.

![Testowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Łączenie harmonogramu z elementem Runbook i przypisywanie parametrów

Możesz [połączyć harmonogram z elementem](./shared-resources/schedules.md) Runbook, aby element Runbook uruchamiał się o określonej godzinie. Parametry wejściowe są przypisywane podczas tworzenia harmonogramu, a element Runbook używa tych wartości, gdy jest uruchamiany zgodnie z harmonogramem. Nie można zapisać harmonogramu, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Planowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Tworzenie elementu webhook dla elementu Runbook i przypisywanie parametrów

Można utworzyć element [webhook](automation-webhooks.md) dla elementu Runbook i skonfigurować parametry wejściowe elementu Runbook. Nie można zapisać elementu webhook, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Utwórz element webhook i przypisz parametry](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Po wykonaniu elementu Runbook za pomocą elementu webhook jest wysyłany wstępnie zdefiniowany parametr wejściowy `[WebhookData](automation-webhooks.md)` wraz ze zdefiniowanymi parametrami wejściowymi. 

![WebhookData — parametr](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Przekazywanie obiektu JSON do elementu Runbook

Może być przydatne do przechowywania danych, które mają zostać przekazane do elementu Runbook w pliku JSON. Można na przykład utworzyć plik JSON zawierający wszystkie parametry, które mają zostać przekazane do elementu Runbook. W tym celu należy przekonwertować kod JSON na ciąg, a następnie przekonwertować ciąg na obiekt programu PowerShell przed przekazaniem go do elementu Runbook.

W tej sekcji jest używany przykład, w którym skrypt programu PowerShell wywołuje polecenie [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) , aby uruchomić element Runbook programu PowerShell, przekazując zawartość pliku JSON do elementu Runbook. Element Runbook programu PowerShell uruchamia maszynę wirtualną platformy Azure przez pobranie parametrów dla maszyny wirtualnej z obiektu JSON.

### <a name="create-the-json-file"></a>Utwórz plik JSON

Wpisz następujący kod w pliku tekstowym i Zapisz go jako **test.jsna** komputerze lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Tworzenie elementu Runbook

Utwórz nowy element Runbook programu PowerShell o nazwie **test-JSON** w Azure Automation. Zobacz [mój pierwszy element Runbook programu PowerShell](./learn/automation-tutorial-runbook-textual-powershell.md).

Aby zaakceptować dane JSON, element Runbook musi przyjmować obiekt jako parametr wejściowy. Element Runbook może następnie użyć właściwości zdefiniowanych w pliku JSON.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
```

Zapisz i Opublikuj ten element Runbook na koncie usługi Automation.

### <a name="call-the-runbook-from-powershell"></a>Wywoływanie elementu Runbook z programu PowerShell

Teraz można wywołać element Runbook z komputera lokalnego przy użyciu Azure PowerShell. 

1. Zaloguj się do platformy Azure, jak pokazano. Następnie zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy zauważyć, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może korzystać tylko z `Connect-AzAccount` siebie.

1. Pobierz zawartość zapisanego pliku JSON i przekonwertuj go na ciąg. `JsonPath` wskazuje ścieżkę, w której zapisano plik JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Przekonwertuj zawartość ciągu `$json` na obiekt programu PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Utwórz tablicę skrótów dla parametrów dla `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Zwróć uwagę, że ustawiasz wartość `Parameters` na obiekt programu PowerShell, który zawiera wartości z pliku JSON.
1. Uruchom element Runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Następne kroki

* Aby przygotować tekstowy element Runbook, zobacz [Edytowanie tekstowych elementów Runbook w Azure Automation](automation-edit-textual-runbook.md).
* Aby przygotować graficzny element Runbook, zobacz [Tworzenie graficznych elementów Runbook w Azure Automation](automation-graphical-authoring-intro.md).
