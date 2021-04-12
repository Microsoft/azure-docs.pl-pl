---
title: Przypadki testowe dla narzędzia Test Toolkit
description: Opisuje testy uruchamiane przez zestaw narzędzi do testowania szablonów ARM.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 31e30b4853da03e28a4a2d15292050805f5bc292
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064153"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Domyślne przypadki testowe dla zestawu narzędzi testów dla szablonu ARM

W tym artykule opisano domyślne testy, które są uruchamiane przy użyciu [zestawu narzędzi do testowania](test-toolkit.md) szablonów dla szablonów Azure Resource Manager (szablony ARM). Zawiera przykłady, które przechodzą lub kończą testy. Zawiera nazwę każdego testu. Aby uruchomić określony test, zobacz [parametry testu](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Użyj poprawnego schematu

Nazwa testu: **schemat DeploymentTemplate jest prawidłowy**

W szablonie należy określić prawidłową wartość schematu.

Poniższy przykład **przekazuje** ten test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Właściwość schematu w szablonie musi być ustawiona na jeden z następujących schematów:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parametry muszą istnieć

Nazwa testu: **Właściwość Parameters musi istnieć**

Szablon powinien mieć element parameters. Parametry są niezbędne do wielokrotnego użycia szablonów w różnych środowiskach. Dodaj do szablonu parametry, które zmieniają się w przypadku wdrażania w różnych środowiskach.

Poniższy przykład **przekazuje** ten test:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Należy użyć zadeklarowanych parametrów

Nazwa testu: **należy odwołać się do parametrów**

Aby zmniejszyć pomyłkę w szablonie, Usuń wszystkie parametry, które są zdefiniowane, ale nie są używane. Ten test umożliwia znalezienie wszelkich parametrów, które nie są używane w dowolnym miejscu szablonu. Eliminowanie nieużywanych parametrów ułatwia również wdrożenie szablonu, ponieważ nie trzeba podawać niepotrzebnych wartości.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Parametry Secure nie mogą mieć stałe domyślnego

Nazwa testu: **Parametry bezpiecznego ciągu nie mogą mieć domyślnego**

Nie dostarczaj zakodowanej wartości domyślnej dla bezpiecznego parametru w szablonie. Pusty ciąg jest prawidłowy dla wartości domyślnej.

Używasz typów **SecureString** lub **secureobject** dla parametrów, które zawierają wartości poufne, takie jak hasła. Gdy parametr używa typu bezpiecznego, wartość parametru nie jest rejestrowana ani przechowywana w historii wdrożenia. Ta akcja uniemożliwia złośliwemu użytkownikowi odnajdywanie poufnej wartości.

Jednak w przypadku podania wartości domyślnej ta wartość jest wykrywalna dla każdego, kto może uzyskać dostęp do szablonu lub historii wdrażania.

Następujący przykład **kończy się niepowodzeniem** tego testu:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Następny przykład **przekazuje** ten test:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Adresy URL środowiska nie mogą być stałe

Nazwa testu: **DeploymentTemplate nie może zawierać identyfikatora URI stałe**

Nie umieszczajj adresów URL środowiska w szablonie. Zamiast tego należy użyć [funkcji środowiska](template-functions-deployment.md#environment) , aby dynamicznie pobrać te adresy URL podczas wdrażania. Aby uzyskać listę blokowanych hostów adresów URL, zobacz [przypadek testowy](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Poniższy przykład **kończy się niepowodzeniem** tego testu, ponieważ adres URL to stałe.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Test **kończy się niepowodzeniem** , gdy jest używany z [concat](template-functions-string.md#concat) lub [URI](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Poniższy przykład **przekazuje** ten test.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Lokalizacja używa parametru

Nazwa testu: **Lokalizacja nie powinna być stałe**

Szablony powinny mieć parametr o nazwie Location. Użyj tego parametru, aby ustawić lokalizację zasobów w szablonie. W szablonie głównym (o nazwie _azuredeploy.json_ lub _mainTemplate.json_) ten parametr może być domyślnie lokalizacją grupy zasobów. W połączonych lub zagnieżdżonych szablonach parametr Location nie powinien mieć domyślnej lokalizacji.

Użytkownicy szablonu mogą mieć ograniczone regiony. Po wprowadzeniu twardej lokalizacji zasobów użytkownicy mogą mieć zablokowaną możliwość tworzenia zasobów w tym regionie. Użytkownicy mogą być blokowani, nawet jeśli ustawisz lokalizację zasobu na `"[resourceGroup().location]"` . Grupa zasobów mogła zostać utworzona w regionie, do którego inni użytkownicy nie mogą uzyskać dostępu. Ci użytkownicy nie mogą korzystać z szablonu.

Dostarczając parametr lokalizacji, który jest wartością domyślną lokalizacji grupy zasobów, użytkownicy mogą użyć wartości domyślnej, jeśli jest to wygodne, ale także określić inną lokalizację.

W poniższym przykładzie ten test **kończy się niepowodzeniem** , ponieważ lokalizacja w zasobie jest ustawiona na `resourceGroup().location` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

W następnym przykładzie jest używany parametr Location, ale ten test **kończy się niepowodzeniem** , ponieważ parametr Location ma wartość domyślną lokalizacji stałe.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Zamiast tego należy utworzyć parametr, który jest wartością domyślną dla lokalizacji grupy zasobów, ale umożliwia użytkownikom podanie innej wartości. Poniższy przykład **przekazuje** ten test, gdy szablon jest używany jako główny szablon.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Jeśli jednak poprzedni przykład jest używany jako połączony szablon, test **zakończy się niepowodzeniem**. Jeśli jest używany jako połączony szablon, usuń wartość domyślną.

## <a name="resources-should-have-location"></a>Zasoby powinny mieć lokalizację

Nazwa testu: **zasoby powinny mieć lokalizację**

Lokalizacja zasobu powinna być ustawiona na [wyrażenie szablonu](template-expressions.md) lub `global` . W wyrażeniu szablonu zwykle jest używany parametr Location opisany w poprzednim teście.

Następujący przykład **kończy się niepowodzeniem** tego testu, ponieważ lokalizacja nie jest wyrażeniem lub `global` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Poniższy przykład **przekazuje** ten test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Następny przykład **przekazuje** także ten test.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Rozmiar maszyny wirtualnej używa parametru

Nazwa testu: **rozmiar maszyny wirtualnej powinien być parametrem**

Nie umieszczaj rozmiaru maszyny wirtualnej. Podaj parametr, aby użytkownicy szablonu mogli modyfikować rozmiar wdrożonej maszyny wirtualnej.

Poniższy przykład **kończy się niepowodzeniem** tego testu.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Zamiast tego należy podać parametr.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Następnie ustaw rozmiar maszyny wirtualnej na ten parametr.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Wartości minimalna i maksymalna są liczbami

Nazwa testu: **wartość minimalna i maksymalna są liczbami**

Jeśli określisz minimalną i maksymalną wartość dla parametru, określ je jako liczby.

Następujący przykład **kończy się niepowodzeniem** tego testu:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Zamiast tego podaj wartości jako liczby. Poniższy przykład **przekazuje** ten test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

To ostrzeżenie jest również wyświetlane, jeśli podano wartość minimalną lub maksymalną, ale nie inne.

## <a name="artifacts-parameter-defined-correctly"></a>Parametr artefaktów został zdefiniowany prawidłowo

Nazwa testu: **parametr artefaktów**

W przypadku dołączania parametrów dla `_artifactsLocation` i `_artifactsLocationSasToken` należy użyć prawidłowych wartości domyślnych i typów. Aby przekazać ten test, muszą zostać spełnione następujące warunki:

* w przypadku podania jednego parametru należy podać drugi
* `_artifactsLocation` musi być **ciągiem**
* `_artifactsLocation` musi mieć wartość domyślną w szablonie głównym
* `_artifactsLocation` nie można mieć wartości domyślnej w zagnieżdżonym szablonie
* `_artifactsLocation``"[deployment().properties.templateLink.uri]"`wartość domyślna musi mieć adres URL lub niesformatowanego repozytorium.
* `_artifactsLocationSasToken` musi być elementem **secureString**
* `_artifactsLocationSasToken` może mieć tylko pusty ciąg dla jego wartości domyślnej
* `_artifactsLocationSasToken` nie można mieć wartości domyślnej w zagnieżdżonym szablonie

## <a name="declared-variables-must-be-used"></a>Zadeklarowane zmienne muszą być używane

Nazwa testu: **należy odwołać się do zmiennych**

Aby zmniejszyć pomyłkę w szablonie, Usuń wszelkie zmienne, które są zdefiniowane, ale nie używane. Ten test umożliwia znalezienie wszelkich zmiennych, które nie są używane w dowolnym miejscu w szablonie.

## <a name="dynamic-variable-should-not-use-concat"></a>Zmienna dynamiczna nie powinna używać Concat

Nazwa testu: **odwołania do zmiennych dynamicznych nie powinny używać concat**

Czasami konieczne jest dynamiczne konstruowanie zmiennej na podstawie wartości innej zmiennej lub parametru. Nie używaj funkcji [concat](template-functions-string.md#concat) podczas ustawiania wartości. Zamiast tego należy użyć obiektu, który zawiera dostępne opcje i dynamicznie pobrać jedną z właściwości obiektu podczas wdrażania.

Poniższy przykład **przekazuje** ten test. Zmienna **currentImage** jest ustawiana dynamicznie podczas wdrażania.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Użyj najnowszej wersji interfejsu API

Nazwa testu: **ApiVersions powinna być ostatnią**

Wersja interfejsu API dla każdego zasobu powinna używać najnowszej wersji. Test szacuje używaną wersję w porównaniu z wersjami dostępnymi dla tego typu zasobu.

## <a name="use-hardcoded-api-version"></a>Użyj wersji interfejsu API stałe

Nazwa testu: **dostawcy ApiVersions jest niedozwolona**

Wersja interfejsu API dla typu zasobu określa, które właściwości są dostępne. Podaj w szablonie zakodowaną wersję interfejsu API. Nie pobieraj wersji interfejsu API, która jest określana podczas wdrażania. Nie wiadomo, które właściwości są dostępne.

Poniższy przykład **kończy się niepowodzeniem** tego testu.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Poniższy przykład **przekazuje** ten test.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Właściwości nie mogą być puste

Nazwa testu: **szablon nie powinien zawierać pustych** wartości

Nie umieszczaj właściwości do pustej wartości. Puste wartości obejmują ciągi o wartości null i puste, obiekty lub tablice. Jeśli ustawisz właściwość na wartość pustą, Usuń tę właściwość z szablonu. Jednak po wdrożeniu nie można ustawić właściwości na wartość pustą, na przykład za pomocą parametru.

## <a name="use-resource-id-functions"></a>Korzystanie z funkcji identyfikatora zasobu

Nazwa testu: **identyfikatory powinny pochodzić od ResourceID**

Podczas określania identyfikatora zasobu należy użyć jednej z funkcji identyfikatora zasobu. Dozwolone są następujące funkcje:

* [Identyfikator](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Nie należy używać funkcji concat do tworzenia identyfikatora zasobu. Poniższy przykład **kończy się niepowodzeniem** tego testu.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Następny przykład **przekazuje** ten test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Funkcja ResourceId ma poprawne parametry

Nazwa testu: **identyfikatory zasobów nie powinny zawierać**

Podczas generowania identyfikatorów zasobów nie należy używać zbędnych funkcji dla parametrów opcjonalnych. Domyślnie funkcja [ResourceID](template-functions-resource.md#resourceid) używa bieżącej subskrypcji i grupy zasobów. Nie musisz podawać tych wartości.

Poniższy przykład **kończy się niepowodzeniem** tego testu, ponieważ nie trzeba podawać bieżącego identyfikatora subskrypcji i nazwy grupy zasobów.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Następny przykład **przekazuje** ten test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Ten test ma zastosowanie do:

* [Identyfikator](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [odwoła](template-functions-resource.md#reference)
* [staw](template-functions-resource.md#list)

W przypadku `reference` i `list*` , test **kończy się niepowodzeniem** , gdy jest używany `concat` do konstruowania identyfikatora zasobu.

## <a name="dependson-best-practices"></a>najlepsze rozwiązania dependsOn

Nazwa testu: **najlepsze rozwiązania DependsOn**

Podczas ustawiania zależności wdrożenia nie należy używać funkcji [if](template-functions-logical.md#if) do testowania warunku. Jeśli jeden z zasobów zależy od zasobu, który jest [wdrażany warunkowo](conditional-resource-deployment.md), należy ustawić zależność tak jak w przypadku dowolnego zasobu. Gdy zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usuwa go z wymaganych zależności.

Poniższy przykład **kończy się niepowodzeniem** tego testu.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Następny przykład **przekazuje** ten test.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>W przypadku wdrożeń zagnieżdżonych lub połączonych nie można używać debugowania

Nazwa testu: **zasoby wdrożenia nie mogą być debugowane**

Podczas definiowania [szablonu zagnieżdżone lub połączonego](linked-templates.md) z typem zasobu **Microsoft. resources/Deployments** można włączyć debugowanie dla tego szablonu. Debugowanie jest bardzo precyzyjne, gdy konieczne jest przetestowanie tego szablonu, ale należy je włączyć, gdy wszystko jest gotowe do użycia szablonu w środowisku produkcyjnym.

## <a name="admin-user-names-cant-be-literal-value"></a>Nazwa użytkownika administratora nie może być wartością literału

Nazwa testu: **AdminUsername nie może być literałem**

Podczas ustawiania nazwy użytkownika administratora nie należy używać wartości literału.

Następujący przykład **kończy się niepowodzeniem** tego testu:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Zamiast tego należy użyć parametru. Poniższy przykład **przekazuje** ten test:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Użyj najnowszego obrazu maszyny wirtualnej

Nazwa testu: **obrazy maszyn wirtualnych powinny używać najnowszej wersji**

Jeśli szablon zawiera maszynę wirtualną z obrazem, upewnij się, że jest używana najnowsza wersja obrazu.

## <a name="use-stable-vm-images"></a>Korzystanie z stabilnych obrazów maszyn wirtualnych

Nazwa testu: **Virtual Machines nie powinna być zapoznawcza**

W przypadku maszyn wirtualnych nie należy używać obrazów w wersji zapoznawczej.

Poniższy przykład **kończy się niepowodzeniem** tego testu.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Poniższy przykład **przekazuje** ten test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Nie używaj rozszerzenia ManagedIdentity

Nazwa testu: **ManagedIdentityExtension nie może być użyta**

Nie stosuj rozszerzenia ManagedIdentity do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Jak zatrzymać korzystanie z rozszerzenia tożsamości zarządzanej maszyny wirtualnej i rozpocząć korzystanie z usługi Azure instance Metadata Service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Dane wyjściowe nie mogą zawierać wpisów tajnych

Nazwa testu: dane **wyjściowe nie mogą zawierać wpisów tajnych**

Nie dołączaj żadnych wartości w sekcji danych wyjściowych, które potencjalnie ujawniają wpisy tajne. Dane wyjściowe szablonu są przechowywane w historii wdrożenia, dlatego złośliwy użytkownik może znaleźć te informacje.

W poniższym przykładzie test **nie powiedzie się** , ponieważ zawiera on bezpieczny parametr w wartości wyjściowej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Poniższy przykład **nie powiedzie się** , ponieważ używa funkcji [list *](template-functions-resource.md#list) w danych wyjściowych.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Użyj protectedSettings dla wpisów tajnych sekcji commandtoexecute

Nazwa testu: **sekcji commandtoexecute musi używać ProtectedSettings dla wpisów tajnych**

W niestandardowym rozszerzeniu skryptu Użyj właściwości Encrypted, `protectedSettings` gdy program `commandToExecute` zawiera dane tajne, takie jak hasło. Przykładami typów danych tajnych są `secureString` , `secureObject` , `list()` funkcje lub skrypty.

Aby uzyskać więcej informacji o rozszerzeniu niestandardowego skryptu dla maszyn wirtualnych, zobacz [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)i Schema [Microsoft. COMPUTE virtualMachines/Extensions](/azure/templates/microsoft.compute/virtualmachines/extensions).

W tym przykładzie szablon z parametrem o nazwie `adminPassword` i Type `secureString` **przekazuje** test, ponieważ właściwość Encrypted `protectedSettings` zawiera `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

Test **kończy się niepowodzeniem** , jeśli właściwość unencrypted `settings` zawiera `commandToExecute` .

```json
"properties": [
  {
    "settings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o uruchamianiu zestawu narzędzi testowych, zobacz [Use the ARM Template test Toolkit](test-toolkit.md).
* W przypadku modułu Microsoft Learn, który obejmuje użycie zestawu narzędzi test Toolkit, zobacz [Podgląd zmian i sprawdzanie poprawności zasobów platformy Azure przy użyciu zestawu narzędzi do działania i szablonu ARM](/learn/modules/arm-template-test/).
