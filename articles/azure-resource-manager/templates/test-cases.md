---
title: Przypadki testowe dla zestawu narzędzi testowych
description: Opisuje testy uruchamiane przez zestaw narzędzi testowych szablonu usługi ARM.
ms.topic: conceptual
ms.date: 04/12/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7805d6dbdb8b93968a2792ed6dfaf2ac8fea9ae5
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363397"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Domyślne przypadki testowe dla zestawu narzędzi testowych szablonu usługi ARM

W tym artykule opisano testy domyślne uruchamiane przy użyciu zestawu narzędzi [testowych](test-toolkit.md) szablonu dla szablonów Azure Resource Manager usługi ARM. Zawiera przykłady, które przechodzą test lub go nie powiodą. Zawiera on nazwę każdego testu. Aby uruchomić określony test, zobacz [Parametry testu](test-toolkit.md#test-parameters).

## <a name="use-correct-schema"></a>Używanie poprawnego schematu

Nazwa testu: **DeploymentTemplate Schema Is Correct (Nazwa testu: Schemat DeploymentTemplate jest poprawny)**

W szablonie należy określić prawidłową wartość schematu.

Poniższy przykład pomyślnie **przejdzie** ten test.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Właściwość schema w szablonie musi być ustawiona na jeden z następujących schematów:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Parametry muszą istnieć

Nazwa testu: **Musi istnieć właściwość Parameters**

Szablon powinien mieć element parameters. Parametry są niezbędne do tworzenia szablonów wielokrotnego użytku w różnych środowiskach. Dodaj do szablonu parametry dla wartości, które zmieniają się podczas wdrażania w różnych środowiskach.

Poniższy przykład pomyślnie **przejdzie** ten test:

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

Nazwa testu: **Należy odwoływać się do parametrów**

Aby zmniejszyć liczbę nieporozumień w szablonie, usuń wszystkie parametry, które są zdefiniowane, ale nie są używane. Ten test umożliwia znalezienie parametrów, które nie są używane w szablonie. Wyeliminowanie nieużywanych parametrów ułatwia również wdrażanie szablonu, ponieważ nie trzeba po prostu podaniem niepotrzebnych wartości.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Bezpieczne parametry nie mogą mieć wartości domyślnej na dysku

Nazwa testu: **Parametry ciągu bezpiecznego nie mogą mieć wartości domyślnej**

Nie po podaj w szablonie zakodowanej wartości domyślnej dla bezpiecznego parametru. Pusty ciąg jest w porządku dla wartości domyślnej.

Typów **SecureString** lub **SecureObject** używa się w przypadku parametrów zawierających wartości poufne, takie jak hasła. Gdy parametr używa typu bezpiecznego, wartość parametru nie jest rejestrowana ani przechowywana w historii wdrażania. Ta akcja uniemożliwia złośliwemu użytkownikowi odnajdowanie wartości poufnej.

Jednak po poeniu wartości domyślnej ta wartość jest wykrywalna przez każdego, kto może uzyskać dostęp do szablonu lub historii wdrażania.

Poniższy przykład kończy **się niepowodzeniem** tego testu:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Następny przykład pomyślnie **przejdzie** ten test:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Adresów URL środowiska nie można kodować na dysku

Nazwa testu: **DeploymentTemplate nie może zawierać hardcoded URI**

Nie koduj adresów URL środowiska na dysku w szablonie. Zamiast tego użyj funkcji [environment,](template-functions-deployment.md#environment) aby dynamicznie uzyskać te adresy URL podczas wdrażania. Aby uzyskać listę zablokowanych hostów URL, zobacz [przypadek testowy](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Poniższy przykład kończy **się niepowodzeniem** tego testu, ponieważ adres URL jest na dysku twardym.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Test kończy się również **niepowodzeniem w** przypadku korzystania z metody [z concat](template-functions-string.md#concat) lub [uri](template-functions-string.md#uri).

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Poniższy przykład pomyślnie **przejdzie** ten test.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Lokalizacja używa parametru

Nazwa testu: **Lokalizacja nie powinna być na hardcoded**

Szablony powinny mieć parametr o nazwie location. Użyj tego parametru do ustawienia lokalizacji zasobów w szablonie. W głównym szablonie _(o nazwieazuredeploy.jslub_ mainTemplate.js _na_) ten parametr może domyślnie określać lokalizację grupy zasobów. W połączonych lub zagnieżdżonych szablonach parametr lokalizacji nie powinien mieć lokalizacji domyślnej.

Użytkownicy szablonu mogą mieć do dyspozycji ograniczone regiony. W przypadku zakodowania lokalizacji zasobu użytkownicy mogą mieć zablokowaną opcję tworzenia zasobu w tym regionie. Użytkownicy mogą zostać zablokowani, nawet jeśli lokalizacja zasobu zostanie ustawiona na `"[resourceGroup().location]"` wartość . Grupa zasobów może zostać utworzona w regionie, do których inni użytkownicy nie mogą uzyskać dostępu. Ci użytkownicy nie mogą korzystać z szablonu.

Podając parametr lokalizacji, który domyślnie określa lokalizację grupy zasobów, użytkownicy mogą używać wartości domyślnej, gdy jest to wygodne, ale także określać inną lokalizację.

Poniższy przykład kończy **się niepowodzeniem** tego testu, ponieważ lokalizacja w zasobie jest ustawiona na `resourceGroup().location` wartość .

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

W następnym przykładzie użyto  parametru lokalizacji, ale ten test kończy się niepowodzeniem, ponieważ parametr location domyślnie jest wykodowany na lokalizację.

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

Zamiast tego utwórz parametr, który domyślnie określa lokalizację grupy zasobów, ale umożliwia użytkownikom podanie innej wartości. Poniższy przykład pomyślnie **przejdzie** ten test, gdy szablon jest używany jako główny szablon.

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

Jeśli jednak poprzedni przykład jest używany jako połączony szablon, test zakończy się **niepowodzeniem.** W przypadku korzystania z szablonu połączonego usuń wartość domyślną.

## <a name="resources-should-have-location"></a>Zasoby powinny mieć lokalizację

Nazwa testu: **Zasoby powinny mieć lokalizację**

Lokalizacja zasobu powinna być ustawiona na wyrażenie [szablonu lub](template-expressions.md) `global` . Wyrażenie szablonu zwykle używa parametru lokalizacji opisanego w poprzednim teście.

Poniższy przykład kończy **się niepowodzeniem** tego testu, ponieważ lokalizacja nie jest wyrażeniem ani `global` .

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

Poniższy przykład pomyślnie **przejdzie** ten test.

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

Następny przykład również pomyślnie **przejdzie** ten test.

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

Nazwa testu: **Rozmiar maszyny wirtualnej powinien być parametrem**

Nie należy kodować rozmiaru maszyny wirtualnej na dysku twardym. Podaj parametr , aby użytkownicy szablonu mogą modyfikować rozmiar wdrożonej maszyny wirtualnej.

Poniższy przykład kończy **się niepowodzeniem** tego testu.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Zamiast tego podaj parametr .

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

## <a name="min-and-max-values-are-numbers"></a>Wartości minimalne i maksymalne to liczby

Nazwa testu: **minimalna i maksymalna wartość to liczby**

W przypadku definiowania wartości minimalnych i maksymalnych dla parametru należy określić je jako liczby.

Poniższy przykład kończy **się niepowodzeniem** tego testu:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Zamiast tego podaj wartości jako liczby. Poniższy przykład pomyślnie **przejdzie** ten test:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

To ostrzeżenie jest również wyświetlane, jeśli po podaj wartość minimalna lub maksymalna, ale nie inną.

## <a name="artifacts-parameter-defined-correctly"></a>Poprawnie zdefiniowano parametr Artifacts

Nazwa testu: **parametr artifacts**

W przypadku dołączania parametrów `_artifactsLocation` dla i `_artifactsLocationSasToken` użyj poprawnych wartości domyślnych i typów. Aby zdać ten test, muszą zostać spełnione następujące warunki:

* W przypadku podania jednego parametru należy podać drugi
* `_artifactsLocation` musi być **ciągiem**
* `_artifactsLocation` musi mieć wartość domyślną w głównym szablonie
* `_artifactsLocation` nie może mieć wartości domyślnej w zagnieżdżonych szablonach
* `_artifactsLocation` wartość domyślna musi mieć adres `"[deployment().properties.templateLink.uri]"` URL lub nieprzetworzonego repo
* `_artifactsLocationSasToken` musi być **secureString**
* `_artifactsLocationSasToken` może mieć tylko pusty ciąg dla jego wartości domyślnej
* `_artifactsLocationSasToken` nie może mieć wartości domyślnej w zagnieżdżonych szablonach

## <a name="declared-variables-must-be-used"></a>Zadeklarowane zmienne muszą być używane

Nazwa testu: **Zmienne muszą być przywołyne**

Aby zmniejszyć liczbę nieporozumień w szablonie, usuń wszystkie zmienne, które są zdefiniowane, ale nie są używane. Ten test wyszukuje wszelkie zmienne, które nie są używane w żadnym miejscu w szablonie.

## <a name="dynamic-variable-should-not-use-concat"></a>Zmienna dynamiczna nie powinna używać funkcji concat

Nazwa testu: **Odwołania do zmiennych dynamicznych nie powinny używać funkcji Concat**

Czasami trzeba dynamicznie skonstruować zmienną na podstawie wartości innej zmiennej lub parametru. Nie używaj funkcji [concat](template-functions-string.md#concat) podczas ustawiania wartości. Zamiast tego należy użyć obiektu, który zawiera dostępne opcje i dynamicznie pobrać jedną z właściwości z obiektu podczas wdrażania.

Poniższy przykład pomyślnie **przejdzie** ten test. Zmienna **currentImage** jest ustawiana dynamicznie podczas wdrażania.

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

## <a name="use-recent-api-version"></a>Korzystanie z najnowszej wersji interfejsu API

Nazwa testu: **apiVersions powinny być ostatnie**

Wersja interfejsu API dla każdego zasobu powinna używać najnowszej wersji. Test ocenia używaną wersję względem wersji dostępnych dla tego typu zasobu.

## <a name="use-hardcoded-api-version"></a>Korzystanie z hardcoded wersji interfejsu API

Nazwa testu: **Interfejs API DostawcówWersje są niedozwolone**

Wersja interfejsu API dla typu zasobu określa, które właściwości są dostępne. Podaj w szablonie zakodowaną wersję interfejsu API. Nie pobieraj wersji interfejsu API, która jest określana podczas wdrażania. Nie wiadomo, które właściwości są dostępne.

Poniższy przykład kończy **się niepowodzeniem** tego testu.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Poniższy przykład pomyślnie **przejdzie** ten test.

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

Nazwa testu: **Szablon nie powinien zawierać wartości pustych**

Nie koduj właściwości na wartość pustą. Puste wartości obejmują ciągi null i puste, obiekty lub tablice. Jeśli ustawisz właściwość na wartość pustą, usuń ją z szablonu. Można jednak ustawić właściwość na pustą wartość podczas wdrażania, na przykład za pomocą parametru .

## <a name="use-resource-id-functions"></a>Korzystanie z funkcji identyfikatora zasobu

Nazwa testu: **Identyfikatory powinny pochodzić od identyfikatorów zasobów**

Podczas określania identyfikatora zasobu użyj jednej z funkcji identyfikatora zasobu. Dozwolone funkcje to:

* [Resourceid](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Nie używaj funkcji concat do tworzenia identyfikatora zasobu. Poniższy przykład kończy **się niepowodzeniem** tego testu.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Następny przykład pomyślnie **przejdzie** ten test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Funkcja ResourceId ma poprawne parametry

Nazwa testu: **ResourceIds nie powinny zawierać**

Podczas generowania identyfikatorów zasobów nie używaj niepotrzebnych funkcji dla parametrów opcjonalnych. Domyślnie funkcja [resourceId](template-functions-resource.md#resourceid) używa bieżącej subskrypcji i grupy zasobów. Nie musisz podaniem tych wartości.

Poniższy przykład kończy **się** niepowodzeniem w tym teście, ponieważ nie trzeba po podaniem bieżącego identyfikatora subskrypcji i nazwy grupy zasobów.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Następny przykład pomyślnie **przejdzie** ten test.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Ten test dotyczy:

* [Resourceid](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [Odwołanie](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

W `reference` przypadku i test kończy się `list*` **niepowodzeniem,** gdy używasz `concat` do konstruowania identyfikatora zasobu.

## <a name="dependson-best-practices"></a>dependsOn best practices

Nazwa testu: **DependsOn Best Practices**

Podczas ustawiania zależności wdrożenia nie używaj funkcji [if](template-functions-logical.md#if) do testowania warunku. Jeśli jeden zasób zależy od zasobu, który jest [wdrażany warunkowo,](conditional-resource-deployment.md)ustaw zależność tak samo jak w przypadku dowolnego zasobu. Jeśli zasób warunkowy nie zostanie wdrożony, Azure Resource Manager automatycznie usunie go z wymaganych zależności.

Poniższy przykład kończy **się niepowodzeniem** tego testu.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Następny przykład pomyślnie **przejdzie** ten test.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Wdrożenia zagnieżdżone lub połączone nie mogą używać debugowania

Nazwa testu: **Zasoby wdrożenia nie mogą być debugowane**

Podczas definiowania [zagnieżdżonych lub połączonych](linked-templates.md) szablonów z typem zasobu **Microsoft.Resources/deployments** można włączyć debugowanie dla tego szablonu. Debugowanie jest w porządku, gdy musisz przetestować ten szablon, ale powinno być włączone, gdy wszystko będzie gotowe do użycia szablonu w środowisku produkcyjnym.

## <a name="admin-user-names-cant-be-literal-value"></a>Nazwy użytkowników administratorów nie mogą być wartością literału

Nazwa testu: **adminUsername nie powinna być literału**

Podczas ustawiania nazwy użytkownika administratora nie używaj wartości literału.

Poniższy przykład kończy **się niepowodzeniem** tego testu:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Zamiast tego należy użyć parametru . Poniższy przykład pomyślnie **przejdzie** ten test:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Używanie najnowszego obrazu maszyny wirtualnej

Nazwa testu: **Obrazy maszyn wirtualnych powinny używać najnowszej wersji**

Jeśli szablon zawiera maszynę wirtualną z obrazem, upewnij się, że używasz najnowszej wersji obrazu.

## <a name="use-stable-vm-images"></a>Używanie stabilnych obrazów maszyn wirtualnych

Nazwa testu: Virtual Machines **nie powinna być w wersji zapoznawczej**

Maszyny wirtualne nie powinny używać obrazów w wersji zapoznawczej.

Poniższy przykład kończy **się niepowodzeniem** tego testu.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Poniższy przykład pomyślnie **przejdzie** ten test.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Nie używaj rozszerzenia ManagedIdentity

Nazwa testu: Nie można używać nazwy **ManagedIdentityExtension**

Nie należy stosować rozszerzenia ManagedIdentity do maszyny wirtualnej. Rozszerzenie zostało wycofane w 2019 r. i nie powinno być już używane.

## <a name="outputs-cant-include-secrets"></a>Dane wyjściowe nie mogą zawierać wpisów tajnych

Nazwa testu: **dane wyjściowe nie mogą zawierać wpisów tajnych**

Nie uwzględniaj żadnych wartości w sekcji danych wyjściowych, które potencjalnie uwidoczniają wpisy tajne. Dane wyjściowe z szablonu są przechowywane w historii wdrażania, więc złośliwy użytkownik może znaleźć te informacje.

Poniższy przykład kończy **się niepowodzeniem** testu, ponieważ zawiera bezpieczny parametr w wartości wyjściowej.

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

Poniższy przykład kończy **się niepowodzeniem,** ponieważ w danych wyjściowych użyto funkcji [list*.](template-functions-resource.md#list)

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

## <a name="use-protectedsettings-for-commandtoexecute-secrets"></a>Używanie protectedSettings dla wpisów tajnych commandToExecute

Nazwa testu: **CommandToExecute musi używać protectedSettings dla wpisów tajnych**

W rozszerzeniu niestandardowego skryptu użyj zaszyfrowanej właściwości , jeśli zawiera `protectedSettings` `commandToExecute` ona tajne dane, takie jak hasło. Przykłady tajnych typów danych to `secureString` , , funkcje lub `secureObject` `list()` skrypty.

Aby uzyskać więcej informacji na temat rozszerzenia niestandardowego skryptu dla maszyn wirtualnych, zobacz [Windows](
/azure/virtual-machines/extensions/custom-script-windows), [Linux](/azure/virtual-machines/extensions/custom-script-linux)i schemat [Microsoft.Compute virtualMachines/extensions.](/azure/templates/microsoft.compute/virtualmachines/extensions)

W tym przykładzie szablon z parametrem o nazwie i typem pomyślnie `adminPassword` `secureString` **przejdzie** test, ponieważ zaszyfrowana właściwość `protectedSettings` zawiera wartość `commandToExecute` .

```json
"properties": [
  {
    "protectedSettings": {
      "commandToExecute": "[parameters('adminPassword')]"
    }
  }
]
```

Test kończy **się niepowodzeniem,** jeśli niezaszyfrowana właściwość `settings` zawiera właściwość `commandToExecute` .

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

* Aby dowiedzieć się więcej na temat uruchamiania zestawu narzędzi testowych, zobacz [Korzystanie z zestawu narzędzi testowych szablonu usługi ARM.](test-toolkit.md)
* Aby uzyskać Microsoft Learn, który obejmuje korzystanie z zestawu narzędzi testowych, zobacz Preview [changes and validate Azure resources by using what-if](/learn/modules/arm-template-test/)and the ARM template test toolkit (Podgląd zmian i weryfikowanie zasobów platformy Azure przy użyciu funkcji what-if i zestawu narzędzi testowych szablonu usługi ARM).
