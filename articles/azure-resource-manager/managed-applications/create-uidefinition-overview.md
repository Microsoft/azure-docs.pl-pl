---
title: CreateUiDefinition.jsw pliku dla okienka portalu
description: Opisuje sposób tworzenia definicji interfejsu użytkownika dla Azure Portal. Używane podczas definiowania Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 0e2aee194d3c97655dd4ec5aaeea46fb607c4c5e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210959"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Plik CreateUiDefinition.json dla środowiska tworzenia aplikacji zarządzanej platformy Azure

W tym dokumencie przedstawiono podstawowe koncepcje **createUiDefinition.js** pliku. Azure Portal używa tego pliku do definiowania interfejsu użytkownika podczas tworzenia zarządzanej aplikacji.

Szablon jest następujący:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

CreateUiDefinition zawsze zawiera trzy właściwości: 

* jścia
* version
* parameters

Program obsługi powinien zawsze mieć wartość `Microsoft.Azure.CreateUIDef` , a Najnowsza obsługiwana wersja to `0.1.2-preview` .

Schemat właściwości Parameters zależy od kombinacji określonej procedury obsługi i wersji. W przypadku aplikacji zarządzanych obsługiwane właściwości to `basics` ,, `steps` `outputs` i `config` . Właściwości podstawowe i kroki zawierają [elementy](create-uidefinition-elements.md) , takie jak pola tekstowe i listy rozwijane, do wyświetlenia w Azure Portal. Właściwość Outputs służy do mapowania wartości wyjściowych określonych elementów na parametry szablonu Azure Resource Manager. Można używać `config` tylko wtedy, gdy zachodzi potrzeba przesłonięcia domyślnego zachowania `basics` kroku.

Włączenie `$schema` jest zalecane, ale opcjonalne. Jeśli jest określona, wartość `version` musi być zgodna z wersją w `$schema` identyfikatorze URI.

Możesz użyć edytora JSON do utworzenia createUiDefinition, a następnie przetestowania go w [piaskownicy createUiDefinition](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) w celu uzyskania podglądu. Aby uzyskać więcej informacji na temat piaskownicy, zobacz [testowanie interfejsu portalu dla Azure Managed Applications](test-createuidefinition.md).

## <a name="basics"></a>Podstawy

**Podstawowy** krok to pierwszy krok generowany, gdy Azure Portal przeanalizuje plik. Domyślnie krok podstawowe umożliwia użytkownikom wybranie subskrypcji, grupy zasobów i lokalizacji wdrożenia.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="Domyślne podstawy":::

W tej sekcji możesz dodać więcej elementów. Jeśli to możliwe, należy dodać elementy, które wykonują zapytania dotyczące parametrów obejmujących całe wdrożenie, takich jak nazwa klastra lub poświadczenia administratora.

Poniższy przykład pokazuje pole tekstowe, które zostało dodane do elementów domyślnych.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Config

Należy określić element config, gdy zachodzi potrzeba zastąpienia domyślnego zachowania dla podstawowych kroków. W poniższym przykładzie przedstawiono dostępne właściwości.

```json
"config": {
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

W przypadku programu `description` Podaj ciąg z obsługą promocji, który opisuje zasób. Obsługiwane jest formatowanie wielowierszowe i łącza.

Dla programu `location` Określ właściwości kontrolki lokalizacji, która ma zostać przesłonięta. Wszystkie właściwości, które nie zostały zastąpione, są ustawiane na wartości domyślne. `resourceTypes` akceptuje tablicę ciągów zawierających w pełni kwalifikowane nazwy typów zasobów. Opcje lokalizacji są ograniczone tylko do regionów, które obsługują typy zasobów.  `allowedValues`   akceptuje tablicę ciągów regionów. Tylko te regiony pojawiają się na liście rozwijanej.Można ustawić zarówno `allowedValues`   , jak i  `resourceTypes` . Wynikiem jest przecięcie obu list. Na koniec `visible` Właściwość może służyć warunkowo lub całkowicie wyłączyć listę rozwijaną lokalizacji.  

`subscription`Elementy i `resourceGroup` umożliwiają określenie dodatkowych walidacji. Składnia służąca do określania walidacji jest taka sama jak niestandardowe sprawdzanie poprawności dla [pola tekstowego](microsoft-common-textbox.md). Możesz również określić `permission` walidacje dla subskrypcji lub grupy zasobów.  

Kontrola subskrypcji akceptuje listę przestrzeni nazw dostawcy zasobów. Na przykład można określić **Microsoft. COMPUTE**. Wyświetla komunikat o błędzie, gdy użytkownik wybierze subskrypcję, która nie obsługuje dostawcy zasobów. Ten błąd występuje, gdy dostawca zasobów nie jest zarejestrowany w ramach tej subskrypcji, a użytkownik nie ma uprawnienia do rejestrowania dostawcy zasobów.  

Dla kontrolki Grupa zasobów jest opcja `allowExisting` . Gdy `true` Użytkownicy mogą wybrać grupy zasobów, które mają już zasoby. Ta flaga jest najbardziej stosowana do szablonów rozwiązań, w których domyślne zachowanie zezwala użytkownikom na wybór nowej lub pustej grupy zasobów. W większości innych scenariuszy określenie tej właściwości nie jest konieczne.  

## <a name="steps"></a>Kroki

Właściwość kroki zawiera zero lub więcej dodatkowych kroków do wyświetlenia po stronie podstawy. Każdy krok zawiera jeden lub więcej elementów. Rozważ dodanie kroków na rolę lub warstwę wdrażanej aplikacji. Na przykład Dodaj krok dla danych wejściowych węzła głównego i krok dla węzłów procesu roboczego w klastrze.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>Dane wyjściowe

Azure Portal używa `outputs` właściwości do mapowania elementów z `basics` i `steps` do parametrów szablonu wdrożenia Azure Resource Manager. Klucze tego słownika są nazwami parametrów szablonu, a wartości są właściwościami obiektów wyjściowych z elementów, do których się odwołuje.

Aby ustawić nazwę zasobu aplikacji zarządzanej, należy uwzględnić wartość o nazwie `applicationResourceName` we właściwości Outputs. Jeśli ta wartość nie zostanie ustawiona, aplikacja przypisze identyfikator GUID dla nazwy. W interfejsie użytkownika można uwzględnić pole tekstowe, które żąda nazwy od użytkownika.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Typy zasobów

Aby odfiltrować dostępne lokalizacje tylko do tych lokalizacji, które obsługują typy zasobów do wdrożenia, podaj tablicę typów zasobów. Jeśli podano więcej niż jeden typ zasobu, zwracane są tylko te lokalizacje, które obsługują wszystkie typy zasobów. Ta właściwość jest opcjonalna.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Funkcje

CreateUiDefinition udostępnia [funkcje](create-uidefinition-functions.md) do pracy z danymi wejściowymi i wyjściowymi elementów oraz funkcjami, takimi jak Conditional. Te funkcje są podobne zarówno do składni, jak i funkcji do Azure Resource Manager funkcji szablonu.

## <a name="next-steps"></a>Następne kroki

createUiDefinition.jssamego pliku ma prosty schemat. Rzeczywista głębokość jest powiązana ze wszystkimi obsługiwanymi elementami i funkcjami. Te elementy są szczegółowo opisane w:

- [Elements](create-uidefinition-elements.md)
- [Funkcje](create-uidefinition-functions.md)

Bieżący schemat JSON dla createUiDefinition jest dostępny tutaj: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json` .

Przykładowy plik interfejsu użytkownika można znaleźć w temacie [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json).
