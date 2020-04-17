---
title: Klucz tajny przechowalni z szablonem
description: Pokazuje, jak przekazać klucz tajny z magazynu kluczy jako parametr podczas wdrażania.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: d21a7d727091b427fee59e22db6a77a495a4eab7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458270"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Przekazywanie wartości bezpiecznego parametru za pomocą usługi Azure Key Vault podczas wdrażania

Zamiast umieszczać bezpieczną wartość (np. hasło) bezpośrednio w pliku szablonu lub parametru, można pobrać wartość z [usługi Azure Key Vault](../../key-vault/general/overview.md) podczas wdrażania. Można pobrać wartość, odwołując się do magazynu kluczy i klucz tajny w pliku parametrów. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Magazyn kluczy może istnieć w innej subskrypcji niż grupa zasobów, do której wdrażasz.

W tym artykule koncentruje się na scenariuszu przekazywania poufnej wartości jako parametru szablonu. Nie obejmuje scenariusza ustawiania właściwości maszyny wirtualnej na adres URL certyfikatu w magazynie kluczy. Aby uzyskać szablon przewodnika Szybki start w tym scenariuszu, zobacz [Instalowanie certyfikatu z usługi Azure Key Vault na maszynie wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Wdrażanie magazynów kluczy i wpisów tajnych

Aby uzyskać dostęp do magazynu `enabledForTemplateDeployment` kluczy podczas `true`wdrażania szablonu, ustaw w magazynie kluczy na .

Jeśli masz już magazyn kluczy, upewnij się, że zezwala na wdrażanie szablonów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Aby utworzyć nową przechowalnię kluczy i dodać klucz tajny, należy użyć:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name ExampleGroup --location centralus
az keyvault create \
  --name ExampleVault \
  --resource-group ExampleGroup \
  --location centralus \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name ExampleVault --name "ExamplePassword" --value "hVFkk965BuUv"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name ExampleGroup -Location centralus
New-AzKeyVault `
  -VaultName ExampleVault `
  -resourceGroupName ExampleGroup `
  -Location centralus `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
$secret = Set-AzKeyVaultSecret -VaultName ExampleVault -Name 'ExamplePassword' -SecretValue $secretvalue
```

---

Jako właściciel magazynu kluczy automatycznie masz dostęp do tworzenia wpisów tajnych. Jeśli użytkownik pracujący z wpisami tajnymi nie jest właścicielem magazynu kluczy, udziel dostępu za pomocą:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Aby uzyskać więcej informacji na temat tworzenia magazynów kluczy i dodawania wpisów tajnych, zobacz:

- [Ustawianie i pobieranie klucza tajnego przy użyciu interfejsu wiersza polecenia](../../key-vault/secrets/quick-create-cli.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu programu Powershell](../../key-vault/secrets/quick-create-powershell.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu portalu](../../key-vault/secrets/quick-create-portal.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu platformy .NET](../../key-vault/secrets/quick-create-net.md)
- [Ustawianie i pobieranie klucza tajnego przy użyciu pliku Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Udziel dostępu do tajemnic

Użytkownik, który wdraża szablon, `Microsoft.KeyVault/vaults/deploy/action` musi mieć uprawnienia do zakresu grupy zasobów i magazynu kluczy. [Zarówno rola właściciela,](../../role-based-access-control/built-in-roles.md#owner) jak i [współautora](../../role-based-access-control/built-in-roles.md#contributor) udziela tego dostępu. Jeśli utworzono magazyn kluczy, jesteś właścicielem, więc masz uprawnienia.

W poniższej procedurze pokazano, jak utworzyć rolę z minimalnym uprawnieniem i jak przypisać użytkownika

1. Tworzenie niestandardowego pliku JSON definicji roli:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Zastąp "00000000-0000-0000-0000000000000" identyfikatorem subskrypcji.

2. Utwórz nową rolę przy użyciu pliku JSON:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Przykłady przypisują rolę niestandardową do użytkownika na poziomie grupy zasobów.

W przypadku korzystania z magazynu kluczy z szablonem dla [aplikacji zarządzanej](../managed-applications/overview.md)należy udzielić dostępu do jednostki usługi **dostawcy zasobów urządzenia.** Aby uzyskać więcej informacji, zobacz [Access Key Vault secret podczas wdrażania aplikacji zarządzanych platformy Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Wpisy tajne odwołań ze statycznym identyfikatorem

Za pomocą tego podejścia odwołujesz się do magazynu kluczy w pliku parametrów, a nie do szablonu. Na poniższej ilustracji pokazano, jak plik parametru odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Diagram identyfikatora statycznego integracji magazynu kluczy Menedżera zasobów](./media/key-vault-parameter/statickeyvault.png)

[Samouczek: Integracja usługi Azure Key Vault we wdrożeniu szablonu usługi Resource Manager](./template-tutorial-use-key-vault.md) używa tej metody.

Poniższy szablon wdraża serwer SQL zawierający hasło administratora. Parametr hasła jest ustawiony na bezpieczny ciąg. Ale szablon nie określa, skąd pochodzi ta wartość.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "name": "[parameters('sqlServerName')]",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Teraz utwórz plik parametrów dla poprzedniego szablonu. W pliku parametrów określ parametr, który pasuje do nazwy parametru w szablonie. Dla wartości parametru odwołaj się do klucza tajnego z magazynu kluczy. Klucz tajny odwołujesz się, przekazując identyfikator zasobu magazynu kluczy i nazwę klucza tajnego:

W poniższym pliku parametru klucz magazyn kluczy musi już istnieć, a wartość statyczna dla jego identyfikatora zasobu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "adminLogin": {
        "value": "exampleadmin"
      },
      "adminPassword": {
        "reference": {
          "keyVault": {
          "id": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<vault-name>"
          },
          "secretName": "ExamplePassword"
        }
      },
      "sqlServerName": {
        "value": "<your-server-name>"
      }
  }
}
```

Jeśli chcesz użyć wersji klucza tajnego innego niż `secretVersion` bieżąca wersja, użyj właściwości.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Wdrażanie szablonu i przekazywanie w pliku parametrów:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Wpisy tajne odwołań z dynamicznym identyfikatorem

W poprzedniej sekcji pokazano, jak przekazać identyfikator zasobu statycznego dla klucza tajnego magazynu z parametru. Jednak w niektórych scenariuszach należy odwołać się do klucza tajnego magazynu kluczy, który różni się w zależności od bieżącego wdrożenia. Można też przekazać wartości parametrów do szablonu, a nie utworzyć parametr referencyjny w pliku parametrów. W obu przypadkach można dynamicznie wygenerować identyfikator zasobu dla klucza tajnego magazynu przy użyciu połączonego szablonu.

Nie można dynamicznie wygenerować identyfikatora zasobu w pliku parametrów, ponieważ wyrażenia szablonu nie są dozwolone w pliku parametrów.

W szablonie nadrzędnym należy dodać szablon zagnieżdżony i przekazać parametr zawierający dynamicznie generowany identyfikator zasobu. Na poniższej ilustracji pokazano, jak parametr w połączonym szablonie odwołuje się do klucza tajnego.

![Identyfikator dynamiczny](./media/key-vault-parameter/dynamickeyvault.png)

Poniższy szablon dynamicznie tworzy identyfikator magazynu kluczy i przekazuje go jako parametr.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "The location where the resources will be deployed."
        }
      },
      "vaultName": {
        "type": "string",
        "metadata": {
          "description": "The name of the keyvault that contains the secret."
        }
      },
      "secretName": {
        "type": "string",
        "metadata": {
          "description": "The name of the secret."
        }
      },
      "vaultResourceGroupName": {
        "type": "string",
        "metadata": {
          "description": "The name of the resource group that contains the keyvault."
        }
      },
      "vaultSubscription": {
        "type": "string",
        "defaultValue": "[subscription().subscriptionId]",
        "metadata": {
          "description": "The name of the subscription that contains the keyvault."
        }
      }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać ogólne informacje o magazynach kluczy, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).
- Aby uzyskać pełne przykłady odwoływania się do kluczowych wpisów tajnych, zobacz [przykłady magazynu kluczy](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
