---
title: Key Vault klucz tajny z szablonem
description: Pokazuje, jak przekazać klucz tajny z magazynu kluczy jako parametr podczas wdrażania.
ms.topic: conceptual
ms.date: 12/17/2020
ms.openlocfilehash: 05749fe2e9179051c3183ea2e592cf7190ddb347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104889862"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Używanie usługi Azure Key Vault do przekazywania bezpiecznej wartości parametru podczas wdrażania

Zamiast umieszczać bezpieczną wartość (na przykład hasło) bezpośrednio w szablonie lub pliku parametrów, można pobrać wartość z [Azure Key Vault](../../key-vault/general/overview.md) podczas wdrażania. Możesz pobrać wartość, odwołując się do magazynu kluczy i wpisu tajnego w pliku parametrów. Wartość nigdy nie jest uwidoczniana, ponieważ używane jest tylko odwołanie do jej identyfikatora magazynu kluczy. Magazyn kluczy może istnieć w innej subskrypcji niż grupa zasobów, w której jest wdrażana.

Ten artykuł koncentruje się na scenariuszu przekazywania wartości poufnej jako parametru szablonu. Nie omówiono scenariusza ustawiania właściwości maszyny wirtualnej na adres URL certyfikatu w Key Vault. Aby zapoznać się z szablonem szybkiego startu tego scenariusza, zobacz [Instalowanie certyfikatu z Azure Key Vault na maszynie wirtualnej](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows).

## <a name="deploy-key-vaults-and-secrets"></a>Wdrażanie magazynów kluczy i wpisów tajnych

Aby uzyskać dostęp do magazynu kluczy podczas wdrażania szablonu, ustaw `enabledForTemplateDeployment` dla magazynu kluczy `true` .

Jeśli masz już Key Vault, upewnij się, że zezwala na wdrożenia szablonów.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault update  --name ExampleVault --enabled-for-template-deployment true
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName ExampleVault -EnabledForTemplateDeployment
```

---

Aby utworzyć nowy Key Vault i dodać wpis tajny, użyj:

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

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

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

Jako właściciel magazynu kluczy automatycznie masz dostęp do tworzenia wpisów tajnych. Jeśli użytkownik pracujący z wpisami tajnymi nie jest właścicielem magazynu kluczy, Udziel dostępu:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az keyvault set-policy \
  --upn <user-principal-name> \
  --name ExampleVault \
  --secret-permissions set delete get list
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$userPrincipalName = "<Email Address of the deployment operator>"

Set-AzKeyVaultAccessPolicy `
  -VaultName ExampleVault `
  -UserPrincipalName <user-principal-name> `
  -PermissionsToSecrets set,delete,get,list
```

---

Aby uzyskać więcej informacji na temat tworzenia magazynów kluczy i dodawania wpisów tajnych, zobacz:

- [Ustawianie i pobieranie wpisu tajnego za pomocą interfejsu wiersza polecenia](../../key-vault/secrets/quick-create-cli.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu programu PowerShell](../../key-vault/secrets/quick-create-powershell.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu portalu](../../key-vault/secrets/quick-create-portal.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu platformy .NET](../../key-vault/secrets/quick-create-net.md)
- [Ustawianie i pobieranie wpisu tajnego przy użyciu Node.js](../../key-vault/secrets/quick-create-node.md)

## <a name="grant-access-to-the-secrets"></a>Przyznaj dostęp do wpisów tajnych

Użytkownik, który wdraża szablon, musi mieć `Microsoft.KeyVault/vaults/deploy/action` uprawnienia do zakresu grupy zasobów i magazynu kluczy. Role [właściciela](../../role-based-access-control/built-in-roles.md#owner) i [współautora](../../role-based-access-control/built-in-roles.md#contributor) przyznają ten dostęp. Jeśli magazyn kluczy został utworzony, jesteś właścicielem i masz uprawnienia.

Poniższa procedura pokazuje, jak utworzyć rolę z minimalnym uprawnieniem i jak przypisać użytkownika

1. Utwórz niestandardowy plik JSON definicji roli:

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
    Zastąp ciąg "00000000-0000-0000-0000-000000000000" IDENTYFIKATORem subskrypcji.

2. Utwórz nową rolę przy użyciu pliku JSON:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

    ```azurecli-interactive
    az role definition create --role-definition "<path-to-role-file>"
    az role assignment create \
      --role "Key Vault resource manager template deployment operator" \
      --assignee <user-principal-name> \
      --resource-group ExampleGroup
    ```

    # <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

    ```azurepowershell-interactive
    New-AzRoleDefinition -InputFile "<path-to-role-file>"
    New-AzRoleAssignment `
      -ResourceGroupName ExampleGroup `
      -RoleDefinitionName "Key Vault resource manager template deployment operator" `
      -SignInName <user-principal-name>
    ```

    ---

    Przykłady umożliwiają przypisanie roli niestandardowej do użytkownika na poziomie grupy zasobów.

Korzystając z Key Vault z szablonem [aplikacji zarządzanej](../managed-applications/overview.md), należy przyznać dostęp do jednostki usługi **dostawcy zasobów urządzenia** . Aby uzyskać więcej informacji, zobacz [Access Key Vault Secret podczas wdrażania Azure Managed Applications](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Wpisy tajne z IDENTYFIKATORem statycznym

To podejście polega na odwoływaniu się do magazynu kluczy w pliku parametrów, a nie w szablonie. Na poniższej ilustracji przedstawiono sposób, w jaki plik parametrów odwołuje się do klucza tajnego i przekazuje tę wartość do szablonu.

![Diagram identyfikatorów statycznych Menedżer zasobów integracji magazynu kluczy](./media/key-vault-parameter/statickeyvault.png)

[Samouczek: integracja Azure Key Vault w Menedżer zasobów Template Deployment](./template-tutorial-use-key-vault.md) używa tej metody.

Poniższy szablon wdraża program SQL Server zawierający hasło administratora. Parametr password jest ustawiony na bezpieczny ciąg. Jednak szablon nie określa, skąd pochodzi ta wartość.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

Teraz Utwórz plik parametrów dla poprzedniego szablonu. W pliku parametrów określ parametr, który jest zgodny z nazwą parametru w szablonie. Dla wartości parametru należy odwołać się do wpisu tajnego z magazynu kluczy. Aby odwołać się do wpisu tajnego, należy przekazać identyfikator zasobu magazynu kluczy i nazwę klucza tajnego:

W poniższym pliku parametrów wpis tajny magazynu kluczy musi już istnieć i podano wartość statyczną dla tego identyfikatora zasobu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
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

Jeśli musisz użyć wersji klucza tajnego innego niż bieżąca wersja, użyj `secretVersion` właściwości.

```json
"secretName": "ExamplePassword",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Wdróż szablon i przekaż go do pliku parametrów:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create --name SqlGroup --location westus2
az deployment group create \
  --resource-group SqlGroup \
  --template-uri <template-file-URI> \
  --parameters <parameter-file>
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <template-file-URI> `
  -TemplateParameterFile <parameter-file>
```

---

## <a name="reference-secrets-with-dynamic-id"></a>Wpisy tajne z IDENTYFIKATORem dynamicznym

W poprzedniej sekcji pokazano, jak przekazać statyczny identyfikator zasobu dla wpisu tajnego magazynu kluczy z parametru. Jednak w niektórych scenariuszach należy odwołać się do wpisu tajnego magazynu kluczy, który zależy od bieżącego wdrożenia. Można również przekazać wartości parametrów do szablonu zamiast tworzyć parametr odwołania w pliku parametrów. W obu przypadkach można dynamicznie generować identyfikator zasobu dla wpisu tajnego magazynu kluczy przy użyciu połączonego szablonu.

Nie można dynamicznie wygenerować identyfikatora zasobu w pliku parametrów, ponieważ wyrażenia szablonów nie są dozwolone w pliku parametrów.

W szablonie nadrzędnym Dodaj zagnieżdżony szablon i Przekaż parametr zawierający dynamicznie wygenerowany identyfikator zasobu. Na poniższej ilustracji przedstawiono sposób, w jaki parametr w połączonym szablonie odwołuje się do klucza tajnego.

![Identyfikator dynamiczny](./media/key-vault-parameter/dynamickeyvault.png)

Następujący szablon dynamicznie tworzy identyfikator magazynu kluczy i przekazuje go jako parametr.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "apiVersion": "2020-10-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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

- Aby uzyskać ogólne informacje na temat magazynów kluczy, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md).
- Aby uzyskać pełne Przykłady odwoływania się do kluczy tajnych, zobacz [Key Vault przykładów](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- W przypadku modułu Microsoft Learn, który obejmuje przekazywanie bezpiecznej wartości z magazynu kluczy, zobacz [zarządzanie złożonymi wdrożeniami w chmurze za pomocą zaawansowanych funkcji szablonów usługi ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
