---
title: Użyj Key Vault podczas wdrażania aplikacji zarządzanej
description: Pokazuje, jak używać wpisów tajnych dostępu w Azure Key Vault podczas wdrażania aplikacji zarządzanych
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81458287"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Dostęp do Key Vault klucza tajnego podczas wdrażania Azure Managed Applications

Gdy musisz przekazać bezpieczną wartość (na przykład hasło) jako parametr podczas wdrażania, możesz pobrać wartość z [Azure Key Vault](../../key-vault/general/overview.md). Aby uzyskać dostęp do Key Vault podczas wdrażania aplikacji zarządzanych, należy przyznać dostęp do jednostki usługi **dostawcy zasobów urządzenia** . Usługa Managed Applications używa tej tożsamości do uruchamiania operacji. Aby pomyślnie pobrać wartość z Key Vault podczas wdrażania, jednostka usługi musi mieć dostęp do Key Vault.

W tym artykule opisano sposób konfigurowania Key Vault do pracy z zarządzanymi aplikacjami.

## <a name="enable-template-deployment"></a>Włącz wdrożenie szablonu

1. W portalu wybierz Key Vault.

1. Wybierz pozycję **Zasady dostępu**.   

   ![Wybieranie zasad dostępu](./media/key-vault-access/select-access-policies.png)

1. Wybierz pozycję **Kliknij, aby wyświetlić zaawansowane zasady dostępu**.

   ![Pokaż zaawansowane zasady dostępu](./media/key-vault-access/advanced.png)

1. Wybierz pozycję **Włącz dostęp do Azure Resource Manager na potrzeby wdrożenia szablonu**. Następnie wybierz pozycję **Zapisz**.

   ![Włącz wdrożenie szablonu](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Dodaj usługę jako współautor

1. Wybierz pozycję **Kontrola dostępu (IAM)** .

   ![Wybieranie kontroli dostępu](./media/key-vault-access/access-control.png)

1. Wybierz pozycję **Dodaj przypisanie roli**.

   ![Wybieranie opcji dodawania](./media/key-vault-access/add-access-control.png)

1. Wybierz opcję **współautor** dla roli. Wyszukaj **dostawcę zasobów urządzenia** i wybierz go z dostępnych opcji.

   ![Wyszukaj dostawcę](./media/key-vault-access/search-provider.png)

1. Wybierz pozycję **Zapisz**.

## <a name="reference-key-vault-secret"></a>Odwołanie Key Vault wpis tajny

Aby przekazać klucz tajny z Key Vault do szablonu w aplikacji zarządzanej, należy użyć [połączonego lub zagnieżdżonego szablonu](../templates/linked-templates.md) i odwołać się do Key Vault w parametrach dla szablonu połączonego lub zagnieżdżonego. Podaj identyfikator zasobu Key Vault i nazwę wpisu tajnego.

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

Key Vault skonfigurowany do dostępności podczas wdrażania aplikacji zarządzanej.

* Aby uzyskać informacje o przekazywaniu wartości z Key Vault jako parametru szablonu, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](../templates/key-vault-parameter.md).
* Aby zapoznać się z przykładami aplikacji zarządzanych, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
* Aby dowiedzieć się, jak utworzyć plik definicji interfejsu użytkownika dla aplikacji zarządzanej, zobacz [Rozpoczynanie pracy z plikiem CreateUiDefinition](create-uidefinition-overview.md).
