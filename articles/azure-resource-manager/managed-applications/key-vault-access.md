---
title: Korzystanie z usługi Key Vault podczas wdrażania aplikacji zarządzanej
description: Pokazuje, jak używać wpisów tajnych dostępu w usłudze Azure Key Vault podczas wdrażania aplikacji zarządzanych
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458287"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Klucz tajny magazynu kluczy dostępu podczas wdrażania aplikacji zarządzanych platformy Azure

Jeśli musisz przekazać bezpieczną wartość (np. hasło) jako parametr podczas wdrażania, możesz pobrać wartość z [usługi Azure Key Vault](../../key-vault/general/overview.md). Aby uzyskać dostęp do magazynu kluczy podczas wdrażania aplikacji zarządzanych, należy udzielić dostępu do jednostki usługi **dostawcy zasobów urządzenia.** Usługa Aplikacje zarządzane używa tej tożsamości do uruchamiania operacji. Aby pomyślnie pobrać wartość z magazynu kluczy podczas wdrażania, podmiot usługi musi mieć dostęp do usługi Key Vault.

W tym artykule opisano sposób konfigurowania magazynu kluczy do pracy z aplikacjami zarządzanymi.

## <a name="enable-template-deployment"></a>Włączanie wdrażania szablonów

1. W portalu wybierz przechowalnię kluczy.

1. Wybierz pozycję **Zasady dostępu**.   

   ![Wybieranie zasad dostępu](./media/key-vault-access/select-access-policies.png)

1. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**.

   ![Pokaż zaawansowane zasady dostępu](./media/key-vault-access/advanced.png)

1. Wybierz **pozycję Włącz dostęp do usługi Azure Resource Manager w celu wdrożenia szablonu**. Następnie wybierz pozycję **Zapisz**.

   ![Włączanie wdrażania szablonów](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Dodawanie usługi jako współautora

1. Wybierz **pozycję Kontrola dostępu (IAM)**.

   ![Wybierz kontrolę dostępu](./media/key-vault-access/access-control.png)

1. Wybierz pozycję **Dodaj przypisanie roli**.

   ![Wybieranie opcji dodawania](./media/key-vault-access/add-access-control.png)

1. Wybierz **pozycję Współautor** dla roli. Wyszukaj **dostawcę zasobów urządzenia** i wybierz go z dostępnych opcji.

   ![Wyszukaj dostawcę](./media/key-vault-access/search-provider.png)

1. Wybierz pozycję **Zapisz**.

## <a name="reference-key-vault-secret"></a>Klucz tajny magazynu kluczy referencyjnych

Aby przekazać klucz tajny z magazynu kluczy do szablonu w aplikacji zarządzanej, należy użyć [szablonu połączonego lub zagnieżdżonego](../templates/linked-templates.md) i odwołać się do Magazynu kluczy w parametrach szablonu połączonego lub zagnieżdżonego. Podaj identyfikator zasobu magazynu kluczy i nazwę klucza tajnego.

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

Skonfigurowano magazyn kluczy jako dostępny podczas wdrażania aplikacji zarządzanej.

* Aby uzyskać informacje o przekazywaniu wartości z magazynu kluczy jako parametru szablonu, zobacz [Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault w celu przekazania bezpiecznej wartości parametru.](../templates/key-vault-parameter.md)
* W przypadku przykładów aplikacji zarządzanych zobacz [Przykładowe projekty dla aplikacji zarządzanych platformy Azure.](sample-projects.md)
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).
