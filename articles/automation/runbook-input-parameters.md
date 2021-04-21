---
title: Konfigurowanie parametrów wejściowych runbook w programie Azure Automation
description: W tym artykule opisano sposób konfigurowania parametrów wejściowych runbook, które zezwalają na przekazywane dane do runbook po jego uruchomieniu.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 95a6cc87471fcf2209d452f90e1e5f52cae122c5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831298"
---
# <a name="configure-runbook-input-parameters"></a>Konfigurowanie parametrów wejściowych elementu runbook

Parametry wejściowe runbook zwiększają elastyczność runbook, umożliwiając do niego przekazywane dane podczas jego uruchamiania. Te parametry umożliwiają kierowanie akcji do określonych scenariuszy i środowisk. W tym artykule opisano konfigurację i użycie parametrów wejściowych w twoich runbookach.

Parametry wejściowe można skonfigurować dla elementów Runbook programu PowerShell, przepływu pracy programu PowerShell, elementów graficznych i elementów Runbook języka Python. Runbook może mieć wiele parametrów z różnymi typami danych lub w ogóle nie mieć parametrów. Parametry wejściowe mogą być obowiązkowe lub opcjonalne i można użyć wartości domyślnych dla parametrów opcjonalnych.

Wartości są przypisywane do parametrów wejściowych dla runbook po jego uruchomieniu. Możesz uruchomić runbook z Azure Portal, usługi internetowej lub programu PowerShell. Można również uruchomić jeden z nich jako podrzędny podręcznik Runbook, który jest wywoływany w innym.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>Konfigurowanie parametrów wejściowych w programie PowerShell Runbook

Elementy Runbook programu PowerShell i przepływu pracy programu PowerShell Azure Automation obsługują parametry wejściowe zdefiniowane za pomocą poniższych właściwości. 

| **Właściwość** | **Opis** |
|:--- |:--- |
| Typ |Wymagane. Typ danych oczekiwany dla wartości parametru. Dowolny typ .NET jest prawidłowy. |
| Nazwa |Wymagane. Nazwa parametru. Ta nazwa musi być unikatowa w obrębie tego podręcznika, rozpoczynać się literą i może zawierać tylko litery, cyfry lub znaki podkreślenia. |
| Obowiązkowy |Opcjonalny. Wartość logiczna określająca, czy parametr wymaga wartości. W przypadku ustawienia wartości True należy podać wartość podczas uruchamiania tego podręcznika. Jeśli ustawisz wartość False, wartość będzie opcjonalna. Jeśli nie określisz wartości właściwości, program PowerShell domyślnie uzna parametr `Mandatory` wejściowy za opcjonalny. |
| Wartość domyślna |Opcjonalny. Wartość, która jest używana dla parametru , jeśli po uruchomieniu runbook nie zostanie przekazana żadna wartość wejściowa. Dla dowolnego parametru można ustawić wartość domyślną. |

Windows PowerShell obsługuje więcej atrybutów parametrów wejściowych niż wymienione powyżej, takich jak walidacja, aliasy i zestawy parametrów. Jednak Azure Automation obsługuje tylko właściwości parametrów wejściowych na liście.

Na przykład przyjrzyjmy się definicji parametru w elementach Runbook przepływu pracy programu PowerShell. Ta definicja ma następującą postać ogólną, w której wiele parametrów jest rozdzielonych przecinkami.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

Teraz skonfigurujemy parametry wejściowe dla runbook przepływu pracy programu PowerShell, który wyprowadza szczegółowe informacje o maszynach wirtualnych: pojedynczej maszynie wirtualnej lub wszystkich maszynach wirtualnych w grupie zasobów. Ten podręcznik Runbook ma dwa parametry, jak pokazano na poniższym zrzucie ekranu: nazwę maszyny wirtualnej ( ) i nazwę `VMName` grupy zasobów ( `resourceGroupName` ).

![Przepływ pracy programu PowerShell dla usługi Automation](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

W tej definicji parametru parametry wejściowe są prostymi parametrami typu ciąg.

Pamiętaj, że runbook programu PowerShell i przepływu pracy programu PowerShell obsługują wszystkie typy proste i złożone, takie jak `Object` lub `PSCredential` dla parametrów wejściowych. Jeśli element Runbook ma parametr wejściowy obiektu, należy użyć tablicy skrótów programu PowerShell z parami nazwa-wartość, aby przekazać wartość. Na przykład w elementy runbook znajduje się następujący parametr.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

W takim przypadku można przekazać następującą wartość do parametru .

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Jeśli nie przekażemy wartości do opcjonalnego parametru String z domyślną wartością null, wartość parametru jest pustym ciągiem, a nie wartością Null.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>Konfigurowanie parametrów wejściowych w graficznych elementach Runbook

Aby zilustrować konfigurację parametrów wejściowych dla graficznego elementów Runbook, utwórzmy element Runbook, który wyprowadza szczegółowe informacje o maszynach wirtualnych, pojedynczej maszynie wirtualnej lub wszystkich maszynach wirtualnych w grupie zasobów. Aby uzyskać szczegółowe informacje, [zobacz Mój pierwszy graficzny element Runbook.](./learn/automation-tutorial-runbook-graphical.md)

Graficzny element Runbook używa tych głównych działań elementów Runbook:

* Konfiguracja konta Uruchom jako platformy Azure do uwierzytelniania na platformie Azure. 
* Definicja polecenia [cmdlet Get-AzVM](/powershell/module/az.compute/get-azvm) służącego do uzyskania właściwości maszyny wirtualnej.
* Użyj działania [Write-Output,](/powershell/module/microsoft.powershell.utility/write-output) aby wyprowadzić nazwy maszyn wirtualnych. 

Działanie `Get-AzVM` definiuje dwa dane wejściowe: nazwę maszyny wirtualnej i nazwę grupy zasobów. Ponieważ te nazwy mogą się różnić przy każdym uruchomieniu runbook, musisz dodać parametry wejściowe do swojego runbooka, aby akceptować te dane wejściowe. Zapoznaj się z [graficznym tworzeniem w Azure Automation](automation-graphical-authoring-intro.md).

Wykonaj następujące kroki, aby skonfigurować parametry wejściowe.

1. Wybierz graficzny element Runbook na stronie Elementy Runbook, a następnie kliknij pozycję **Edytuj.**
2. W edytorze graficznym kliknij przycisk **Input and output (Dane** wejściowe i wyjściowe), a następnie pozycję Add input (Dodaj dane **wejściowe),** aby otworzyć okienko parametru wejściowego runbook.

   ![Graficzny element Runbook automatyzacji](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. Kontrolka Dane wejściowe i wyjściowe wyświetla listę parametrów wejściowych, które są zdefiniowane dla tego typu elementów Runbook. W tym miejscu możesz dodać nowy parametr wejściowy lub edytować konfigurację istniejącego parametru wejściowego. Aby dodać nowy parametr dla tego  obiektu Runbook, kliknij pozycję Dodaj dane wejściowe, aby otworzyć blok parametrów wejściowych **runbook,** w którym można skonfigurować parametry przy użyciu właściwości zdefiniowanych w graficznym tworzenia w [programie Azure Automation](automation-graphical-authoring-intro.md).

    ![Dodawanie nowych danych wejściowych](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. Utwórz dwa parametry z następującymi właściwościami, które będą używane przez `Get-AzVM` działanie, a następnie kliknij przycisk **OK.**

   * Parametr 1:
        * **Nazwa**  --  **Nazwa maszyny wirtualnej**
        * **Typ** -- Ciąg
        * **Obowiązkowy**  --  **Nie**

   * Parametr 2:
        * **Nazwa**  --  **resourceGroupName**
        * **Typ** -- Ciąg
        * **Obowiązkowy**  --  **Nie**
        * **Wartość domyślna**  --  **Niestandardowe**
        * Niestandardowa wartość domyślna — nazwa grupy zasobów zawierającej maszyny wirtualne

5. Wyświetl parametry w kontrolce Dane wejściowe i wyjściowe. 
6. Kliknij ponownie przycisk **OK,** a następnie kliknij przycisk **Zapisz.**
7. Kliknij **pozycję Publikuj,** aby opublikować swój runbook.

### <a name="configure-input-parameters-in-python-runbooks"></a>Konfigurowanie parametrów wejściowych w podręcznikach Runbook języka Python

W przeciwieństwie do programu PowerShell, przepływu pracy programu PowerShell i graficznych elementów Runbook elementy Runbook języka Python nie mają nazwanych parametrów. Edytor elementów Runbook analizuje wszystkie parametry wejściowe jako tablicę wartości argumentu. Dostęp do tablicy można uzyskać, importując `sys` moduł do skryptu języka Python, a następnie używając `sys.argv` tablicy . Należy pamiętać, że pierwszy element tablicy, `sys.argv[0]` , to nazwa skryptu. W związku z tym pierwszym rzeczywistym parametrem wejściowym jest `sys.argv[1]` .

Aby uzyskać przykład użycia parametrów wejściowych w elementach Runbook języka Python, zobacz Mój pierwszy element [Runbook języka Python](./learn/automation-tutorial-runbook-textual-python2.md)w Azure Automation .

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Przypisywanie wartości do parametrów wejściowych w podręcznikach Runbook

W tej sekcji opisano kilka sposobów przekazania wartości do parametrów wejściowych w runbook. Wartości parametrów można przypisywać w przypadku:

* [Uruchamianie elementu Runbook](#start-a-runbook-and-assign-parameters)
* [Testowanie elementu Runbook](#test-a-runbook-and-assign-parameters)
* [Łączenie harmonogramu dla tego runbook](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Tworzenie elementu webhook dla elementu runbook](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Uruchamianie runbook i przypisywanie parametrów

Runbook można uruchomić na wiele sposobów: za pośrednictwem interfejsu Azure Portal, za pomocą zestawu webhook, poleceń cmdlet programu PowerShell, interfejsu API REST lub zestawu SDK. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Uruchamianie opublikowanego podręcznika Runbook przy użyciu Azure Portal i przypisywanie parametrów

Po [uruchomieniu runbook w](start-runbooks.md#start-a-runbook-with-the-azure-portal) Azure Portal zostanie otwarty blok Uruchamianie **runbook,** w którym można wprowadzić wartości utworzonych parametrów.

![Rozpoczynanie korzystania z portalu](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

W etykiecie pod polem wejściowym można zobaczyć właściwości, które zostały ustawione w celu zdefiniowania atrybutów parametrów, na przykład obowiązkowe lub opcjonalne, typ, wartość domyślna. Balon pomocy obok nazwy parametru definiuje również kluczowe informacje potrzebne do podejmowania decyzji dotyczących wartości wejściowych parametrów. 

> [!NOTE]
> Parametry ciągu obsługują puste wartości typu Ciąg. Wprowadzenie `[EmptyString]` w polu parametru wejściowego przekazuje pusty ciąg do parametru . Ponadto parametry ciągu nie obsługują wartości Null. Jeśli nie przekażemy żadnej wartości do parametru ciągu, program PowerShell zinterpretuje ją jako wartość null.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>Uruchamianie opublikowanego podręcznika Runbook przy użyciu poleceń cmdlet programu PowerShell i przypisywanie parametrów

* **Azure Resource Manager cmdlet:** Element Runbook usługi Automation, który został utworzony w grupie zasobów, można uruchomić za pomocą [start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Polecenia cmdlet klasycznego modelu wdrażania platformy Azure:** Element Runbook automatyzacji, który został utworzony w domyślnej grupie zasobów, można uruchomić przy użyciu [start-AzureAutomationRunbook.](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> Po uruchomieniu runbook przy użyciu poleceń cmdlet programu PowerShell tworzony jest domyślny parametr `MicrosoftApplicationManagementStartedBy` o wartości `PowerShell` . Ten parametr można wyświetlić w okienku Szczegóły zadania.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>Uruchamianie runbook przy użyciu zestawu SDK i przypisywanie parametrów

* **Azure Resource Manager metoda:** Możesz uruchomić runbook przy użyciu zestawu SDK języka programowania. Poniżej znajduje się fragment kodu w języku C# do uruchamiania podręcznika Runbook na koncie usługi Automation. Cały kod można wyświetlić w naszym [repozytorium GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

* **Metoda klasycznego modelu wdrażania platformy Azure:** Możesz uruchomić runbook przy użyciu zestawu SDK języka programowania. Poniżej znajduje się fragment kodu w języku C# do uruchamiania podręcznika Runbook na koncie usługi Automation. Cały kod można wyświetlić w naszym [repozytorium GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)

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

   Aby rozpocząć tę metodę, utwórz słownik do przechowywania parametrów i ich wartości dla `VMName`  `resourceGroupName` runbook. Następnie uruchom runbook. Poniżej znajduje się fragment kodu języka C# do wywoływania metody zdefiniowanej powyżej.

   ```csharp
   IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
   // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
   RunbookParameters.Add("resourceGroupName", "WSSC1");
  
   //Call the StartRunbook method with parameters
   StartRunbook("Get-AzureVMGraphical", RunbookParameters);
   ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>Uruchamianie runbook przy użyciu interfejsu API REST i przypisywanie parametrów

Zadanie runbook można utworzyć i uruchomić za pomocą interfejsu API REST Azure Automation, używając metody z `PUT` następującym URI żądania: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

W żądaniu URI zastąp następujące parametry:

* `subscriptionId`: Identyfikator subskrypcji platformy Azure.  
* `resourceGroupName`: nazwa grupy zasobów dla konta usługi Automation.
* `automationAccountName`: nazwa konta usługi Automation, które jest hostowane w określonej usłudze w chmurze.  
* `jobName`: identyfikator GUID zadania. Identyfikatory GUID w programie PowerShell można utworzyć przy użyciu polecenia `[GUID]::NewGuid().ToString()*` .

Aby przekazać parametry do zadania runbook, użyj treści żądania. Przyjmuje ona następujące informacje w formacie JSON:

* Nazwa runbook: wymagane. Nazwa runbook zadania do uruchomienia.  
* Parametry runbook: opcjonalne. Słownik listy parametrów w formacie (nazwa, wartość), gdzie nazwa jest typu Ciąg, a wartość może być dowolną prawidłową wartością JSON.

Jeśli chcesz uruchomić utworzony wcześniej podręcznik **Get-AzureVMTextual** z parametrami i , użyj następującego formatu `VMName` `resourceGroupName` JSON dla treści żądania.

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

Kod stanu HTTP 201 jest zwracany, jeśli zadanie zostało pomyślnie utworzone. Aby uzyskać więcej informacji na temat nagłówków odpowiedzi i treści odpowiedzi, zobacz create a runbook job by using the REST API (Tworzenie zadania [runbook przy użyciu interfejsu API REST).](/rest/api/automation/job/create)

### <a name="test-a-runbook-and-assign-parameters"></a>Testowanie runbook i przypisywanie parametrów

Podczas [testowania wersji roboczej runbook](./manage-runbooks.md) przy użyciu opcji testu zostanie otwarta strona Test. Ta strona umożliwia skonfigurowanie wartości utworzonych parametrów.

![Testowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Łączenie harmonogramu z programem Runbook i przypisywanie parametrów

Harmonogram można [połączyć z programem](./shared-resources/schedules.md) Runbook, aby uruchomić go o określonej godzinie. Parametry wejściowe są przypisywane podczas tworzenia harmonogramu, a program Runbook używa tych wartości podczas uruchamiania zgodnie z harmonogramem. Nie można zapisać harmonogramu, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Planowanie i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Tworzeniehook webhook dla runbook i przypisywanie parametrów

Możesz utworzyć dla swojego [runbooka webhook](automation-webhooks.md) i skonfigurować parametry wejściowe runbook. Nie można zapisać element webhook, dopóki nie zostaną podane wszystkie obowiązkowe wartości parametrów.

![Tworzenie urządzenia webhook i przypisywanie parametrów](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

Podczas wykonywania runbook przy użyciu webhook jest wysyłany wstępnie zdefiniowany parametr wejściowy wraz z zdefiniowanymi `[WebhookData](automation-webhooks.md)` parametrami wejściowymi. 

![Parametr WebhookData](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>Przekaż obiekt JSON do runbook

Przydatne może być przechowywanie danych, które chcesz przekazać do runbook, w pliku JSON. Na przykład możesz utworzyć plik JSON zawierający wszystkie parametry, które chcesz przekazać do runbook. W tym celu należy przekonwertować kod JSON na ciąg, a następnie przekonwertować ciąg na obiekt programu PowerShell przed przekazaniem go do runbook.

W tej sekcji użyto przykładu, w którym skrypt programu PowerShell wywołuje element [Runbook Start-AzAutomationRunbook,](/powershell/module/az.automation/start-azautomationrunbook) przekazując zawartość pliku JSON do tego element runbook. Element Runbook programu PowerShell uruchamia maszynę wirtualną platformy Azure przez pobieranie parametrów maszyny wirtualnej z obiektu JSON.

### <a name="create-the-json-file"></a>Tworzenie pliku JSON

Wpisz następujący kod w pliku tekstowym i zapisz go jako **test.jsna komputerze** lokalnym.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Tworzenie runbook

Utwórz nowy runbook programu PowerShell o **nazwie Test-Json** w Azure Automation. Zobacz [Mój pierwszy runbook programu PowerShell.](./learn/automation-tutorial-runbook-textual-powershell.md)

Aby zaakceptować dane JSON, element Runbook musi przyjąć obiekt jako parametr wejściowy. Następnie można użyć właściwości zdefiniowanych w pliku JSON.

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

Zapisz i opublikuj ten podręcznik Runbook na koncie usługi Automation.

### <a name="call-the-runbook-from-powershell"></a>Wywołanie runbook z programu PowerShell

Teraz możesz wywołać runbook z komputera lokalnego przy użyciu Azure PowerShell. 

1. Zaloguj się do platformy Azure, jak pokazano. Następnie zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >W przypadku podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Należy pamiętać, że te aliasy nie są dostępne dla graficznych elementów Runbook. Graficzny element Runbook może używać tylko `Connect-AzAccount` samego siebie.

1. Pobierz zawartość zapisanego pliku JSON i przekonwertuj go na ciąg. `JsonPath` wskazuje ścieżkę, w której zapisano plik JSON.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. Przekonwertuj `$json` zawartość ciągu obiektu na obiekt programu PowerShell.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. Utwórz tabelę skrótów dla parametrów dla `Start-AzAutomationRunbook` . 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   Zwróć uwagę, że ustawiasz wartość na obiekt programu PowerShell, który zawiera wartości `Parameters` z pliku JSON.
1. Uruchom runbook.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>Następne kroki

* Aby przygotować tekstowy podręcznik runbook, zobacz [Edit textual runbook in Azure Automation](automation-edit-textual-runbook.md)(Edytowanie tekstowych podręczników runbook w programie Azure Automation ).
* Aby przygotować graficzny element Runbook, zobacz Author graphical runbook in Azure Automation (Tworzenie [graficznych elementów Runbook w programie Azure Automation).](automation-graphical-authoring-intro.md)
