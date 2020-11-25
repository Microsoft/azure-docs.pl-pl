---
title: Programowe tworzenie subskrypcji platformy Azure przy użyciu szablonu usługi Azure Resource Manager
description: Dowiedz się, jak programowo tworzyć subskrypcje platformy Azure za pomocą szablonu usługi Azure Resource Manager.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 43a1d4c36771d025e7470f61c61caf633a51798b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94850976"
---
# <a name="programmatically-create-azure-subscriptions-with-an-azure-resource-manager-template"></a>Programowe tworzenie subskrypcji platformy Azure przy użyciu szablonu usługi Azure Resource Manager

Ten artykuł ułatwia programowe tworzenie subskrypcji platformy Azure przy użyciu szablonu usługi Azure Resource Manager (szablonu usługi ARM). Tworzenie subskrypcji za pomocą szablonów usługi ARM jest dostępne dla klientów platformy Azure z kontem rozliczeniowym dla następujących typów umów:

- Umowa Enterprise Agreement (EA)
    - Aby uzyskać więcej informacji, zobacz [Interfejsy API REST umowy Azure Enterprise](ea-portal-rest-apis.md)
- Umowa klienta firmy Microsoft (MCA)
    - [Interfejs API REST rozliczeń platformy Azure](/rest/api/billing)
    - [Interfejsy API subskrypcji platformy Azure](/rest/api/subscription)
- Umowa partnerska firmy Microsoft
    - [Interfejs API REST rozliczeń platformy Azure](/rest/api/billing)
    - [Interfejsy API subskrypcji platformy Azure](/rest/api/subscription)

Subskrypcja platformy Azure utworzona programowo przy użyciu szablonu podlega warunkom umowy, w ramach której nabyto usługi platformy Azure od firmy Microsoft lub autoryzowanego odsprzedawcy. Aby dowiedzieć się więcej, zobacz [informacje prawne dotyczące platformy Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-using-arm-templates"></a>Tworzenie subskrypcji przy użyciu szablonów usługi Resource Manager

Aby zautomatyzować procesy wdrażania w środowisku produkcyjnym/testowym, można tworzyć subskrypcje przy użyciu szablonu usługi Azure Resource Manager (szablonu usługi ARM). W poniższym przykładzie używany jest szablon usługi ARM do tworzenia subskrypcji i grupy zasobów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Aby tworzyć subskrypcje, musisz mieć jedną z następujących ról: 

- Właściciel subskrypcji platformy Azure w sekcji faktury
- Współautor subskrypcji platformy Azure w sekcji faktury
- Twórca subskrypcji platformy Azure w sekcji faktury
- Właściciel subskrypcji platformy Azure w ramach profilu rozliczeniowego lub konta rozliczeniowego
- Współautor subskrypcji platformy Azure w ramach profilu rozliczeniowego lub konta rozliczeniowego

Aby uzyskać więcej informacji, zobacz [Role rozliczeniowe i zadania w subskrypcji](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Ponadto, ponieważ wykonujesz wdrożenie przy użyciu szablonu usługi ARM, musisz mieć uprawnienia do zapisu w obiekcie głównym. Aby utworzyć wdrożenie za pomocą usługi ARM w grupie zarządzania, musisz mieć uprawnienia do zapisu w tej grupie zarządzania. Akcja ma na celu wyłącznie utworzenie wdrożenia ARM. Jeśli tworzona jest subskrypcja, jest ona tworzona tylko w grupie zarządzania określonej w szablonie usługi ARM.

W poniższych przykładach użyto interfejsów API REST. Obecnie program PowerShell i interfejs wiersza polecenia platformy Azure nie są obsługiwane.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Odnajdowanie kont rozliczeniowych, do których masz dostęp

Wykonaj następujące żądanie, aby wyświetlić listę wszystkich kont rozliczeniowych.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

Odpowiedź interfejsu API będzie zawierała listę wszystkich kont rozliczeniowych, do których masz dostęp.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Użyj właściwości `displayName`, aby określić konto rozliczeniowe, dla którego chcesz utworzyć subskrypcje. Upewnij się, że parametr agreeementType tego konta to *MicrosoftCustomerAgreement*. Skopiuj wartość `name` tego konta. Przykładowo aby utworzyć subskrypcję dla konta rozliczeniowego `Contoso`, skopiuj wartość `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Wklej tę wartość w dogodnym miejscu, aby można było jej użyć w następnym kroku.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Odnajdowanie profilów rozliczeniowych i sekcji faktury w celu utworzenia subskrypcji

Opłaty za subskrypcję są wyświetlane w sekcji faktury profilu rozliczeniowego. Użyj poniższego interfejsu API, aby uzyskać listę profilów rozliczeniowych i sekcji faktury, w ramach których masz uprawnienie do tworzenia subskrypcji platformy Azure.

Najpierw uzyskasz listę profilów rozliczeniowych w ramach konta rozliczeniowego, do którego masz dostęp.

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```

Odpowiedź interfejsu API zawiera listę wszystkich profilów rozliczeniowych, w ramach których masz dostęp do tworzenia subskrypcji:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Skopiuj wartość elementu `id`, aby dalej identyfikować sekcje faktury poniżej profilu rozliczeniowego. Na przykład skopiuj wartość `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` i wywołaj następujący interfejs API.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```

### <a name="response"></a>Reakcja

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Użyj właściwości `id`, aby określić sekcję faktury, dla której chcesz utworzyć subskrypcje. Skopiuj cały ciąg. Na przykład `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-and-resource-group-with-a-template"></a>Tworzenie subskrypcji i grupy zasobów przy użyciu szablonu

Poniższy szablon usługi ARM tworzy subskrypcję o nazwie *Dev Team subscription* dla sekcji faktury *Development*. Ta subskrypcja jest rozliczana w profilu rozliczeniowym *Contoso Billing Profile* i pojawia się w sekcji *Development* na tej fakturze. Użyjesz skopiowanego zakresu rozliczeniowego z poprzedniego kroku: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="request"></a>Żądanie

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="request-body"></a>Treść żądania

```json
{
  "properties":
    {
        "location": "westus",
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {
                    "uniqueAliasName": "sampleAlias"
                },
                "resources": [
                    {
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "name": "sampleTemplate",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "template": {
                                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                "contentVersion": "1.0.0.0",
                                "variables": {
                                    "uniqueAliasName": "sampleAlias"
                                },
                                "resources": [
                                    {
                                        "name": "[variables('uniqueAliasName')]",
                                        "type": "Microsoft.Subscription/aliases",
                                        "apiVersion": "2020-09-01",
                                        "properties": {
                                            "workLoad": "Production",
                                            "displayName": "Dev Team subscription",
                                            "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                                        },
                                        "dependsOn": [],
                                        "tags": {}
                                    }
                                ],
                                "outputs": {
                                    "subscriptionId": {
                                        "type": "string",
                                        "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                                    }
                                }
                            }
                        }
                    },
                    {
                        "name": "sampleOuterResource",
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "parameters": {
                                "subscriptionId": {
                                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                                }
                            },
                            "template": {
                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                            "contentVersion": "1.0.0.0",
                                "parameters": {
                                    "subscriptionId": {
                                        "type": "string"
                                    }
                                },
                                "variables": {},
                                "resources": [
                                    {
                                        "name": "sampleInnerResource",
                                        "type": "Microsoft.Resources/deployments",
                                        "subscriptionId": "[parameters('subscriptionId')]",
                                        "apiVersion": "2019-10-01",
                                        "location": "westus",
                                        "properties": {
                                            "expressionEvaluationOptions": {
                                                "scope": "inner"
                                            },
                                            "mode": "Incremental",
                                            "parameters": {},
                                            "template": {
                                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                                "contentVersion": "1.0.0.0",
                                                "parameters": {},
                                                "variables": {},
                                                "resources": [
                                                    {
                                                        "type": "Microsoft.Resources/resourceGroups",
                                                        "apiVersion": "2020-05-01",
                                                        "location": "[deployment().location]",
                                                        "name": "sampleRG",
                                                        "properties": {},
                                                        "tags": {}
                                                    }
                                                ],
                                                "outputs": {}
                                            }
                                        }
                                    }
                                ],
                                "outputs": {}
                            }
                        }
                    }
                ],
                "outputs": {
                    "messageFromLinkedTemplate": {
                        "type": "string",
                        "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                    }
                }
            },
            "mode": "Incremental"
        }
    }
}
```

### <a name="response"></a>Reakcja

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

Aby monitorować postęp, można za pomocą instrukcji GET uzyskać stan wdrożenia.

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="response"></a>Reakcja

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

W poprzednim przykładzie utworzono subskrypcję `16edf959-11fd-48bb-9a46-85190963ead9` i grupę zasobów o nazwie `sampleRG`.

## <a name="next-steps"></a>Następne kroki

* Po utworzeniu subskrypcji możesz przyznać tę możliwość innym użytkownikom i jednostkom usługi. Aby uzyskać więcej informacji, zobacz [Przyznawanie dostępu do tworzenia subskrypcji Enterprise platformy Azure (wersja zapoznawcza)](grant-access-to-create-subscription.md).
* Aby dowiedzieć się więcej na temat zarządzania dużą liczbą subskrypcji za pomocą grup zarządzania, zobacz [Organizowanie zasobów przy użyciu grup zarządzania platformy Azure](../../governance/management-groups/overview.md).
