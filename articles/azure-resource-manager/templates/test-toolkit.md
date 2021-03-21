---
title: Zestaw narzędzi do testowania szablonów ARM
description: Opisuje sposób uruchamiania zestawu narzędzi szablonu Azure Resource Manager (szablon ARM) dla szablonu. Zestaw narzędzi umożliwia sprawdzenie, czy wdrożono zalecane rozwiązania.
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e5ad0b6dca7718166517b52148fbc6dd49f38869
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674016"
---
# <a name="use-arm-template-test-toolkit"></a>Korzystanie z zestawu narzędzi do testowania szablonów ARM

[Zestaw szablonów Azure Resource Manager (szablon ARM) Test Toolkit](https://aka.ms/arm-ttk) sprawdza, czy szablon używa zalecanych praktyk. Jeśli szablon nie jest zgodny z zalecanymi praktykami, zwraca listę ostrzeżeń z sugerowanymi zmianami. Za pomocą zestawu narzędzi test Toolkit można dowiedzieć się, jak uniknąć typowych problemów związanych z tworzeniem szablonów.

Zestaw narzędzi test Toolkit zawiera [zbiór domyślnych testów](test-cases.md). Te testy są zaleceniami, ale nie wymaganiami. Można zdecydować, które testy są odpowiednie dla celów i dostosować testy, które są uruchamiane.

W tym artykule opisano sposób uruchomienia narzędzia Test Toolkit oraz sposobu dodawania lub usuwania testów. Aby uzyskać opis testów domyślnych, zobacz temat [przypadki testowe zestawu narzędzi](test-cases.md).

Zestaw narzędzi jest zestawem skryptów programu PowerShell, które można uruchomić za pomocą polecenia w programie PowerShell lub interfejsie CLI.

## <a name="install-on-windows"></a>Instalowanie w systemie Windows

1. Jeśli nie masz jeszcze programu PowerShell, [Zainstaluj program PowerShell w systemie Windows](/powershell/scripting/install/installing-powershell-core-on-windows).

1. [Pobierz najnowszy plik zip](https://aka.ms/arm-ttk-latest) dla zestawu narzędzi test i wyodrębnij go.

1. Uruchom program PowerShell.

1. Przejdź do folderu, w którym został wyodrębniony zestaw narzędzi test Toolkit. W tym folderze przejdź do folderu **ARM-TTK** .

1. Jeśli [zasady wykonywania](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokują skrypty z Internetu, należy odblokować pliki skryptów. Upewnij się, że jesteś w folderze **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Zaimportuj moduł.

   ```powershell
   Import-Module .\arm-ttk.psd1
   ```

1. Aby uruchomić testy, użyj następującego polecenia:

   ```powershell
   Test-AzTemplate -TemplatePath \path\to\template
   ```

## <a name="install-on-linux"></a>Instalowanie w systemie Linux

1. Jeśli nie masz jeszcze programu PowerShell, [Zainstaluj program PowerShell w systemie Linux](/powershell/scripting/install/installing-powershell-core-on-linux).

1. [Pobierz najnowszy plik zip](https://aka.ms/arm-ttk-latest) dla zestawu narzędzi test i wyodrębnij go.

1. Uruchom program PowerShell.

   ```bash
   pwsh
   ```

1. Przejdź do folderu, w którym został wyodrębniony zestaw narzędzi test Toolkit. W tym folderze przejdź do folderu **ARM-TTK** .

1. Jeśli [zasady wykonywania](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokują skrypty z Internetu, należy odblokować pliki skryptów. Upewnij się, że jesteś w folderze **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Zaimportuj moduł.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Aby uruchomić testy, użyj następującego polecenia:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="install-on-macos"></a>Instalowanie w systemie macOS

1. Jeśli nie masz jeszcze programu PowerShell, [Zainstaluj program PowerShell w programie macOS](/powershell/scripting/install/installing-powershell-core-on-macos).

1. Zainstaluj program `coreutils`:

   ```bash
   brew install coreutils
   ```

1. [Pobierz najnowszy plik zip](https://aka.ms/arm-ttk-latest) dla zestawu narzędzi test i wyodrębnij go.

1. Uruchom program PowerShell.

   ```bash
   pwsh
   ```

1. Przejdź do folderu, w którym został wyodrębniony zestaw narzędzi test Toolkit. W tym folderze przejdź do folderu **ARM-TTK** .

1. Jeśli [zasady wykonywania](/powershell/module/microsoft.powershell.core/about/about_execution_policies) blokują skrypty z Internetu, należy odblokować pliki skryptów. Upewnij się, że jesteś w folderze **ARM-TTK** .

   ```powershell
   Get-ChildItem *.ps1, *.psd1, *.ps1xml, *.psm1 -Recurse | Unblock-File
   ```

1. Zaimportuj moduł.

   ```powershell
   Import-Module ./arm-ttk.psd1
   ```

1. Aby uruchomić testy, użyj następującego polecenia:

   ```powershell
   Test-AzTemplate -TemplatePath /path/to/template
   ```

## <a name="result-format"></a>Format wyniku

Testy, które są przekazywane, są wyświetlane w **kolorze zielonym** i poprzedzone znakiem **[+]**.

Testy, które kończą się niepowodzeniem, są wyświetlane w **kolorze czerwonym** i poprzedzone znakiem **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="Wyświetl wyniki testu":::

Wyniki tekstu są następujące:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Parametry testu

Po podaniu parametru **-TemplatePath** zestaw narzędzi przegląda ten folder dla szablonu o nazwie azuredeploy.json lub maintemplate.json. Najpierw sprawdza ten szablon, a następnie testuje wszystkie inne szablony w folderze i jego podfolderach. Inne szablony są testowane jako połączone szablony. Jeśli ścieżka zawiera plik o nazwie [CreateUiDefinition.json](../managed-applications/create-uidefinition-overview.md), uruchamia testy, które są istotne dla definicji interfejsu użytkownika.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Aby przetestować jeden plik w tym folderze, należy dodać parametr **-File** . Jednak folder musi mieć szablon główny o nazwie azuredeploy.json lub maintemplate.json.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Domyślnie wszystkie testy są uruchamiane. Aby określić pojedyncze testy do uruchomienia, użyj parametru **-test** . Podaj nazwę testu. Aby uzyskać nazwy, zobacz [przypadki testowe dla zestawu narzędzi](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Dostosowywanie testów

W przypadku szablonów ARM zestaw narzędzi uruchamia wszystkie testy w folderze **\arm-ttk\testcases\deploymentTemplate**. Jeśli chcesz trwale usunąć test, Usuń ten plik z folderu.

W przypadku plików [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) uruchamia wszystkie testy w folderze **\arm-ttk\testcases\CreateUiDefinition**.

Aby dodać własny test, Utwórz plik z konwencją nazewnictwa: **Your-Custom-Test-Name.test.ps1**.

Test może pobrać szablon jako parametr obiektu lub parametr ciągu. Zazwyczaj należy użyć jednego lub drugiego, ale można użyć obu tych metod.

Użyj parametru Object, gdy musisz uzyskać sekcję szablonu i wykonać iterację jej właściwości. Test, który używa parametru Object, ma następujący format:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

Obiekt szablonu ma następujące właściwości:

* $schema
* Contentversion —
* parameters
* modyfikacj
* zasoby
* wydajności

Na przykład można pobrać kolekcję parametrów z `$TemplateObject.parameters` .

Użyj parametru String, gdy musisz wykonać operację na ciągu dla całego szablonu. Test, który używa parametru String ma następujący format:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Na przykład można uruchomić wyrażenie regularne parametru String, aby sprawdzić, czy jest używana określona składnia.

Aby dowiedzieć się więcej na temat implementowania testu, zapoznaj się z innymi testami w tym folderze.

## <a name="integrate-with-azure-pipelines"></a>Integracja z usługą Azure Pipelines

Składnik test Toolkit można dodać do potoku platformy Azure. Za pomocą potoku można uruchomić test za każdym razem, gdy szablon zostanie zaktualizowany lub uruchomić go w ramach procesu wdrażania.

Najprostszym sposobem dodania zestawu narzędzi testowych do potoku jest rozszerzenie innych firm. Dostępne są następujące dwa rozszerzenia:

* [Uruchamianie testów TTK ARM](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Tester szablonów ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Lub można zaimplementować własne zadania. Poniższy przykład pokazuje, jak pobrać zestaw narzędzi test Toolkit.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

W następnym przykładzie pokazano, jak uruchomić testy.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o testach domyślnych, zobacz [domyślne przypadki testowe dla zestawu narzędzi do testowania szablonów ARM](test-cases.md).
- W przypadku modułu Microsoft Learn, który obejmuje użycie zestawu narzędzi test Toolkit, zobacz [Podgląd zmian i sprawdzanie poprawności zasobów platformy Azure przy użyciu zestawu narzędzi do działania i szablonu ARM](/learn/modules/arm-template-test/).
