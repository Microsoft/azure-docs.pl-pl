---
title: Eksportowanie grup zasobów platformy Azure zawierających rozszerzenia maszyn wirtualnych
description: Eksportowanie szablonów Menedżer zasobów zawierających rozszerzenia maszyn wirtualnych.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 12/05/2016
ms.openlocfilehash: df1ae43b2c6a74448a6782a43fb86f8f4939b13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560008"
---
# <a name="exporting-resource-groups-that-contain-vm-extensions"></a>Eksportowanie grup zasobów zawierających rozszerzenia maszyn wirtualnych

Grupy zasobów platformy Azure można eksportować do nowego szablonu Menedżer zasobów, który można następnie ponownie wdrożyć. Proces eksportowania interpretuje istniejące zasoby i tworzy szablon Menedżer zasobów, który po wdrożeniu powoduje powstanie podobnej grupy zasobów. W przypadku korzystania z opcji eksportowania grupy zasobów dla grupy zasobów zawierającej rozszerzenia maszyn wirtualnych należy rozważyć kilka elementów, takich jak zgodność rozszerzenia i ustawienia chronione.

Ten dokument zawiera szczegółowe informacje o tym, jak działa proces eksportowania grupy zasobów w odniesieniu do rozszerzeń maszyn wirtualnych, w tym listy obsługiwanych rozszerzeń i szczegółowych informacji na temat obsługi zabezpieczonych danych.

## <a name="supported-virtual-machine-extensions"></a>Obsługiwane rozszerzenia maszyny wirtualnej

Dostępnych jest wiele rozszerzeń maszyn wirtualnych. Nie wszystkie rozszerzenia można eksportować do szablonu Menedżer zasobów przy użyciu funkcji "skrypt automatyzacji". Jeśli rozszerzenie maszyny wirtualnej nie jest obsługiwane, należy je ręcznie umieścić w wyeksportowanym szablonie.

Następujące rozszerzenia można eksportować za pomocą funkcji skryptu automatyzacji.

> Acronis Backup, Acronis Backup Linux, BG info, BMC CTM Agent Linux, BMC CTM Agent Windows, klient Chef, niestandardowy skrypt, rozszerzenie niestandardowego skryptu, niestandardowy skrypt dla systemu Linux, Agent usługi Datadog Linux, usługi Datadog Windows Agent, rozszerzenie usługi DSC, dynaTrace Linux, dynaTrace Windows, HPE Security Application Defender, IaaS chroniący przed złośliwym kodem, IaaS Diagnostics, Linux Chef Client, Linux , Site 24x7 Linux Server, site 24x7 Windows Server, Trend Micro DSA, Trend Micro DSA Linux, dostęp do maszyny wirtualnej dla systemu Linux, dostęp do maszyny wirtualnej dla systemu Linux, migawka maszyny wirtualnej, migawka maszyny wirtualnej Linux

## <a name="export-the-resource-group"></a>Eksportowanie grupy zasobów

Aby wyeksportować grupę zasobów do szablonu wielokrotnego użytku, wykonaj następujące czynności:

1. Logowanie się do witryny Azure Portal
2. W menu centrum kliknij pozycję grupy zasobów.
3. Wybierz docelową grupę zasobów z listy
4. W bloku grupy zasobów kliknij pozycję skrypt automatyzacji.

![Eksportowanie szablonu](./media/export-templates/template-export.png)

Skrypt Azure Resource Manager automations generuje szablon Menedżer zasobów, plik parametrów i kilka przykładowych skryptów wdrażania, takich jak PowerShell i interfejs wiersza polecenia platformy Azure. Na tym etapie wyeksportowany szablon można pobrać przy użyciu przycisku Pobierz, który został dodany jako nowy szablon do biblioteki szablonów lub ponownie wdrożony przy użyciu przycisku Wdróż.

## <a name="configure-protected-settings"></a>Konfigurowanie ustawień chronionych

Wiele rozszerzeń maszyn wirtualnych platformy Azure zawiera konfigurację ustawień chronionych, która szyfruje dane poufne, takie jak poświadczenia i ciągi konfiguracyjne. Ustawienia chronione nie są eksportowane ze skryptem automatyzacji. W razie potrzeby chronione ustawienia należy ponownie wstawić do wyeksportowanego szablonu.

### <a name="step-1---remove-template-parameter"></a>Krok 1. Usuwanie parametru szablonu

Po wyeksportowaniu grupy zasobów tworzony jest jeden parametr szablonu, aby podać wartość eksportowanych chronionych ustawień. Ten parametr można usunąć. Aby usunąć parametr, zapoznaj się z listą parametrów i Usuń parametr, który wygląda podobnie do tego przykładu JSON.

```json
"extensions_extensionname_protectedSettings": {
    "defaultValue": null,
    "type": "SecureObject"
}
```

### <a name="step-2---get-protected-settings-properties"></a>Krok 2. Uzyskiwanie właściwości ustawień chronionych

Ponieważ każde chronione ustawienie ma zestaw wymaganych właściwości, należy zebrać listę tych właściwości. Każdy parametr konfiguracji ustawień chronionych można znaleźć w [schemacie Azure Resource Manager w witrynie GitHub](https://raw.githubusercontent.com/Azure/azure-resource-manager-schemas/master/schemas/2015-08-01/Microsoft.Compute.json). Ten schemat zawiera tylko zestawy parametrów dla rozszerzeń wymienionych w sekcji przegląd tego dokumentu. 

W repozytorium schematów Wyszukaj odpowiednie rozszerzenie, w tym przykładzie `IaaSDiagnostics` . Po `protectedSettings` zlokalizowaniu obiektu rozszerzeń Zanotuj każdy parametr. W przykładzie `IaasDiagnostic` rozszerzenia wymagane są parametry `storageAccountName` , `storageAccountKey` i `storageAccountEndPoint` .

```json
"protectedSettings": {
    "type": "object",
    "properties": {
        "storageAccountName": {
            "type": "string"
        },
        "storageAccountKey": {
            "type": "string"
        },
        "storageAccountEndPoint": {
            "type": "string"
        }
    },
    "required": [
        "storageAccountName",
        "storageAccountKey",
        "storageAccountEndPoint"
    ]
}
```

### <a name="step-3---re-create-the-protected-configuration"></a>Krok 3. ponowne tworzenie chronionej konfiguracji

W wyeksportowanym szablonie Wyszukaj `protectedSettings` i Zastąp wyeksportowany obiekt chronionego ustawienia nowym, który zawiera wymagane parametry rozszerzenia i wartość dla każdej z nich.

W przykładzie `IaasDiagnostic` rozszerzenia Nowa konfiguracja chronionego ustawienia będzie wyglądać podobnie do poniższego przykładu:

```json
"protectedSettings": {
    "storageAccountName": "[parameters('storageAccountName')]",
    "storageAccountKey": "[parameters('storageAccountKey')]",
    "storageAccountEndPoint": "https://core.windows.net"
}
```

Końcowy zasób rozszerzenia wygląda podobnie do następującego przykładu JSON:

```json
{
    "name": "Microsoft.Insights.VMDiagnosticsSettings",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "[variables('apiVersion')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
    ],
    "tags": {
        "displayName": "AzureDiagnostics"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Diagnostics",
        "type": "IaaSDiagnostics",
        "typeHandlerVersion": "1.5",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "xmlCfg": "[base64(concat(variables('wadcfgxstart'), variables('wadmetricsresourceid'), variables('vmName'), variables('wadcfgxend')))]",
            "storageAccount": "[parameters('existingdiagnosticsStorageAccountName')]"
        },
        "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]",
            "storageAccountEndPoint": "https://core.windows.net"
        }
    }
}
```

W przypadku używania parametrów szablonu do podania wartości właściwości należy je utworzyć. Podczas tworzenia parametrów szablonu dla wartości ustawień chronionych upewnij się, że używasz `SecureString` typu parametru, aby zabezpieczyć wartości poufne. Aby uzyskać więcej informacji na temat używania parametrów, zobacz [Tworzenie szablonów Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md).

W przykładzie `IaasDiagnostic` rozszerzenia następujące parametry zostałyby utworzone w sekcji Parametry szablonu Menedżer zasobów.

```json
"storageAccountName": {
    "defaultValue": null,
    "type": "SecureString"
},
"storageAccountKey": {
    "defaultValue": null,
    "type": "SecureString"
}
```

W tym momencie szablon można wdrożyć przy użyciu dowolnej metody wdrażania szablonu.
