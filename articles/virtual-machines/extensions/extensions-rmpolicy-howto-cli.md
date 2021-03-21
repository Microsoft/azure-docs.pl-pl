---
title: Używanie Azure Policy do ograniczania instalacji rozszerzenia maszyny wirtualnej (Linux)
description: Użyj Azure Policy, aby ograniczyć wdrożenia rozszerzenia maszyny wirtualnej.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/23/2018
ms.openlocfilehash: 582f690c00ff8bdd06e4d2867724aa482bcacaf3
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559974"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-linux-vms"></a>Używanie Azure Policy do ograniczania instalacji rozszerzeń na maszynach wirtualnych z systemem Linux

Aby uniemożliwić używanie lub instalację niektórych rozszerzeń na maszynach wirtualnych z systemem Linux, można utworzyć definicję Azure Policy przy użyciu interfejsu wiersza polecenia, aby ograniczyć rozszerzenia dla maszyn wirtualnych w grupie zasobów. 

W tym samouczku jest używany interfejs wiersza polecenia w Azure Cloud Shell, który jest stale aktualizowany do najnowszej wersji. Jeśli chcesz uruchomić interfejs wiersza polecenia platformy Azure lokalnie, musisz zainstalować wersję 2.0.26 lub nowszą. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-rules-file"></a>Utwórz plik reguł

Aby ograniczyć rozszerzenia, które można zainstalować, musisz mieć [regułę](../../governance/policy/concepts/definition-structure.md#policy-rule) umożliwiającą określenie logiki w celu zidentyfikowania rozszerzenia.

W tym przykładzie pokazano, jak odmówić instalacji rozszerzeń opublikowanych przez element "Microsoft. OSTCExtensions", tworząc plik reguł w Azure Cloud Shell, ale jeśli pracujesz lokalnie w interfejsie wiersza polecenia, możesz również utworzyć plik lokalny i zastąpić ścieżkę (~/CloudDrive) ścieżką do pliku lokalnego na komputerze.

W [Cloud Shell bash](https://shell.azure.com/bash)wpisz:

```bash
vim ~/clouddrive/azurepolicy.rules.json
```

Skopiuj i wklej następujący plik JSON do pliku.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.OSTCExtensions/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/publisher",
                "equals": "Microsoft.OSTCExtensions"
            },
            {
                "field": "Microsoft.OSTCExtensions/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Gdy skończysz, naciśnij klawisz **ESC** , a następnie wpisz **: WQ** , aby zapisać i zamknąć plik.


## <a name="create-a-parameters-file"></a>Utwórz plik parametrów

Potrzebny jest również plik [parametrów](../../governance/policy/concepts/definition-structure.md#parameters) , który tworzy strukturę do przekazywania na liście rozszerzeń do zablokowania. 

W tym przykładzie pokazano, jak utworzyć plik parametrów dla maszyn wirtualnych z systemem Linux w Cloud Shell, ale jeśli pracujesz lokalnie w interfejsie wiersza polecenia, możesz również utworzyć plik lokalny i zastąpić ścieżkę (~/CloudDrive) ścieżką do pliku lokalnego na komputerze.

W [Cloud Shell bash](https://shell.azure.com/bash)wpisz:

```bash
vim ~/clouddrive/azurepolicy.parameters.json
```

Skopiuj i wklej następujący plik JSON do pliku.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied. Example: CustomScriptForLinux, VMAccessForLinux etc.",
            "displayName": "Denied extension"
        }
    }
}
```

Gdy skończysz, naciśnij klawisz **ESC** , a następnie wpisz **: WQ** , aby zapisać i zamknąć plik.

## <a name="create-the-policy"></a>Tworzenie zasad

Definicja zasad to obiekt służący do przechowywania konfiguracji, której chcesz użyć. Definicja zasad używa plików reguł i parametrów do definiowania zasad. Utwórz definicję zasad za pomocą polecenia [AZ Policy Definition Create](/cli/azure/role/assignment).

W tym przykładzie reguły i parametry są plikami utworzonymi i przechowywanymi jako pliki JSON w usłudze Cloud Shell.

```azurecli-interactive
az policy definition create \
   --name 'not-allowed-vmextension-linux' \
   --display-name 'Block VM Extensions' \
   --description 'This policy governs which VM extensions that are blocked.' \
   --rules '~/clouddrive/azurepolicy.rules.json' \
   --params '~/clouddrive/azurepolicy.parameters.json' \
   --mode All
```


## <a name="assign-the-policy"></a>Przypisywanie zasad

Ten przykład przypisuje zasady do grupy zasobów za pomocą polecenia [AZ Policy przypisanie Create](/cli/azure/policy/assignment). W przypadku wszystkich maszyn wirtualnych utworzonych w **grupie zasobów grupy zasobu nie** będzie można zainstalować dostępu do maszyny wirtualnej z systemem Linux ani rozszerzeń niestandardowego skryptu dla systemu Linux. Aby można było przypisać zasady, musi istnieć Grupa zasobów.

Użyj [AZ Account List](/cli/azure/account) , aby skorzystać z identyfikatora subskrypcji zamiast tego w przykładzie.


```azurecli-interactive
az policy assignment create \
   --name 'not-allowed-vmextension-linux' \
   --scope /subscriptions/<subscription Id>/resourceGroups/myResourceGroup \
   --policy "not-allowed-vmextension-linux" \
   --params '{
        "notAllowedExtensions": {
            "value": [
                "VMAccessForLinux",
                "CustomScriptForLinux"
            ]
        }
    }'
```

## <a name="test-the-policy"></a>Testowanie zasad

Przetestuj zasady, tworząc nową maszynę wirtualną i próbując dodać nowego użytkownika.


```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --generate-ssh-keys
```

Spróbuj utworzyć nowego użytkownika o nazwie **myNewUser** przy użyciu rozszerzenia dostępu do maszyny wirtualnej.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --password 'mynewuserpwd123!'
```



## <a name="remove-the-assignment"></a>Usuń przypisanie

```azurecli-interactive
az policy assignment delete --name 'not-allowed-vmextension-linux' --resource-group myResourceGroup
```
## <a name="remove-the-policy"></a>Usuń zasady

```azurecli-interactive
az policy definition delete --name 'not-allowed-vmextension-linux'
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure Policy](../../governance/policy/overview.md).
