---
title: Łączenie szablonów do wdrożenia
description: Opisuje sposób używania połączonych szablonów w szablonie Azure Resource Manager (szablon ARM) do tworzenia rozwiązania szablonu modularnego. Pokazuje, jak przekazać wartości parametrów, określić plik parametrów i dynamicznie tworzone adresy URL.
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 6076cbae43e420ac354b5c9d7d101a9c541c078d
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889182"
---
# <a name="using-linked-and-nested-templates-when-deploying-azure-resources"></a>Używanie połączonych i zagnieżdżonych szablonów podczas wdrażania zasobów platformy Azure

Aby wdrożyć złożone rozwiązania, możesz przerwać szablon Azure Resource Manager (szablon ARM) do wielu powiązanych szablonów, a następnie wdrożyć je przy użyciu szablonu głównego. Powiązane szablony mogą być osobnymi plikami lub składnią szablonów, które są osadzone w szablonie głównym. W tym artykule jest używany **połączony szablon** termin do odwoływania się do oddzielnego pliku szablonu, do którego odwołuje się link z głównego szablonu. Używa warunkowego **szablonu** , aby odwołać się do składni szablonu osadzonego w szablonie głównym.

W przypadku małych i średnich rozwiązań łatwiej jest zrozumieć i utrzymywać jeden szablon. Wszystkie zasoby i wartości są widoczne w jednym pliku. W przypadku zaawansowanych scenariuszy połączone szablony umożliwiają podzielenie rozwiązania na składniki przeznaczone do realizacji. Te szablony mogą być bez problemu używane wielokrotnie w innych scenariuszach.

Aby zapoznać się z samouczkiem, zobacz [Samouczek: wdrażanie połączonego szablonu](./deployment-tutorial-linked-template.md).

> [!NOTE]
> Dla połączonych lub zagnieżdżonych szablonów można ustawić tylko tryb wdrożenia na [przyrostowy](deployment-modes.md). Szablon główny można jednak wdrożyć w trybie kompletnym. Jeśli szablon główny zostanie wdrożony w trybie pełnym, a połączony lub zagnieżdżony szablon jest przeznaczony dla tej samej grupy zasobów, zasoby wdrożone w połączonym lub zagnieżdżonym szablonie są uwzględniane podczas oceny wdrożenia w trybie pełnym. Połączona kolekcja zasobów wdrożona w szablonie głównym oraz połączone lub zagnieżdżone szablony są porównywane z istniejącymi zasobami w grupie zasobów. Wszystkie zasoby, które nie są uwzględnione w tej połączonej kolekcji, są usuwane.
>
> Jeśli połączony lub zagnieżdżony szablon jest przeznaczony dla innej grupy zasobów, wdrożenie korzysta z trybu przyrostowego.
>

## <a name="nested-template"></a>Szablon zagnieżdżony

Aby zagnieździć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We `template` Właściwości określ składnię szablonu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          <nested-template-syntax>
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Poniższy przykład służy do wdrażania konta magazynu za pomocą szablonu zagnieżdżonego.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2019-04-01",
              "name": "[parameters('storageAccountName')]",
              "location": "West US",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "StorageV2"
            }
          ]
        }
      }
    }
  ],
  "outputs": {
  }
}
```

### <a name="expression-evaluation-scope-in-nested-templates"></a>Zakres oceny wyrażeń w zagnieżdżonych szablonach

Podczas korzystania z szablonu zagnieżdżonego można określić, czy wyrażenia szablonów mają być oceniane w zakresie szablonu nadrzędnego, czy szablonu zagnieżdżonego. Zakres określa, jak są rozwiązywane parametry, zmienne i funkcje, takie jak [resourceName](template-functions-resource.md#resourcegroup) i [Subscription](template-functions-resource.md#subscription) .

Należy ustawić zakres za pomocą `expressionEvaluationOptions` właściwości. Domyślnie `expressionEvaluationOptions` Właściwość jest ustawiona na `outer` , co oznacza, że używa zakresu szablonu nadrzędnego. Ustaw wartość na, aby spowodować, że `inner` wyrażenia mają być oceniane w zakresie szablonu zagnieżdżonego.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate1",
  "properties": {
    "expressionEvaluationOptions": {
      "scope": "inner"
    },
  ...
```

> [!NOTE]
>
> Gdy zakres jest ustawiony na `outer` , nie można użyć `reference` funkcji w sekcji dane wyjściowe szablonu zagnieżdżonego dla zasobu wdrożonego w zagnieżdżonym szablonie. Aby zwrócić wartości dla wdrożonego zasobu w zagnieżdżonym szablonie, użyj `inner` zakresu lub przekonwertuj zagnieżdżony szablon na połączony szablon.

Poniższy szablon pokazuje, jak są rozpoznawane wyrażenia szablonu zgodnie z zakresem. Zawiera zmienną o nazwie `exampleVar` , która jest zdefiniowana w szablonie nadrzędnym i zagnieżdżonym. Zwraca wartość zmiennej.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "exampleVar": "from parent template"
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "nestedTemplate1",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "variables": {
            "exampleVar": "from nested template"
          },
          "resources": [
          ],
          "outputs": {
            "testVar": {
              "type": "string",
              "value": "[variables('exampleVar')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
    "messageFromLinkedTemplate": {
      "type": "string",
      "value": "[reference('nestedTemplate1').outputs.testVar.value]"
    }
  }
}
```

Wartość zmian w `exampleVar` zależności od wartości `scope` właściwości w `expressionEvaluationOptions` . W poniższej tabeli przedstawiono wyniki dla obu zakresów.

| Zakres oceny | Dane wyjściowe |
| ----- | ------ |
| fabryk | z zagnieżdżonego szablonu |
| zewnętrzny (lub domyślny) | z szablonu nadrzędnego |

Poniższy przykład wdraża program SQL Server i pobiera wpis tajny magazynu kluczy do użycia dla hasła. Zakres jest ustawiany na, `inner` ponieważ dynamicznie tworzy identyfikator magazynu kluczy (zobacz `adminPassword.reference.keyVault` w szablonach zewnętrznych `parameters` ) i przekazuje go jako parametr do szablonu zagnieżdżonego.

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
        }
      }
    }
  ],
  "outputs": {
  }
}
```

Należy zachować ostrożność w przypadku używania bezpiecznych wartości parametrów w szablonach zagnieżdżonych. Jeśli ustawisz zakres na zewnętrzny, bezpieczne wartości są przechowywane w postaci zwykłego tekstu w historii wdrażania. Użytkownik przeglądający szablon w historii wdrożenia może zobaczyć bezpieczne wartości. Zamiast tego należy użyć wewnętrznego zakresu lub dodać do szablonu nadrzędnego zasoby, które potrzebują bezpiecznych wartości.

Poniższy fragment pokazuje, które wartości są bezpieczne i które nie są bezpieczne.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPasswordOrKey": {
      "type": "securestring",
      "metadata": {
        "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
      }
    }
  },
  ...
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2020-06-01",
      "name": "mainTemplate",
      "properties": {
        ...
        "osProfile": {
          "computerName": "mainTemplate",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in parent template
        }
      }
    },
    {
      "name": "outer",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "outer"
        },
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "outer",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "outer",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // No, not secure because resource is in nested template with outer scope
                }
              }
            }
          ]
        }
      }
    },
    {
      "name": "inner",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "properties": {
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "mode": "Incremental",
        "parameters": {
          "adminPasswordOrKey": {
              "value": "[parameters('adminPasswordOrKey')]"
          },
          "adminUsername": {
              "value": "[parameters('adminUsername')]"
          }
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminUsername": {
              "type": "string",
              "metadata": {
                "description": "Username for the Virtual Machine."
              }
            },
            "adminPasswordOrKey": {
              "type": "securestring",
              "metadata": {
                "description": "SSH Key or password for the Virtual Machine. SSH key is recommended."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2020-06-01",
              "name": "inner",
              "properties": {
                ...
                "osProfile": {
                  "computerName": "inner",
                  "adminUsername": "[parameters('adminUsername')]",
                  "adminPassword": "[parameters('adminPasswordOrKey')]" // Yes, secure because resource is in nested template and scope is inner
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="linked-template"></a>Szablon połączony

Aby połączyć szablon, Dodaj [zasób wdrożenia](/azure/templates/microsoft.resources/deployments) do szablonu głównego. We `templateLink` Właściwości Określ identyfikator URI szablonu do uwzględnienia. Poniższy przykład łączy się z szablonem znajdującym się na koncie magazynu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri":"https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
          "contentVersion":"1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

W przypadku odwoływania się do połączonego szablonu wartość `uri` nie może być plikiem lokalnym ani plikiem, który jest dostępny tylko w sieci lokalnej. Azure Resource Manager musi mieć możliwość uzyskania dostępu do szablonu. Podaj wartość identyfikatora URI pobieraną jako HTTP lub HTTPS.

Możesz odwoływać się do szablonów przy użyciu parametrów, które obejmują protokół HTTP lub HTTPS. Na przykład typowym wzorcem jest użycie `_artifactsLocation` parametru. Możesz ustawić połączony szablon z wyrażeniem takim jak:

```json
"uri": "[concat(parameters('_artifactsLocation'), '/shared/os-disk-parts-md.json', parameters('_artifactsLocationSasToken'))]"
```

Jeśli łączysz się z szablonem w usłudze GitHub, użyj pierwotnego adresu URL. Link ma format: `https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-with-templates/quickstart-template/azuredeploy.json` . Aby uzyskać link nieprzetworzony, wybierz opcję **RAW**.

:::image type="content" source="./media/linked-templates/select-raw.png" alt-text="Wybierz nieprzetworzony adres URL":::

### <a name="parameters-for-linked-template"></a>Parametry połączonego szablonu

Parametry powiązanego szablonu można podać w zewnętrznym pliku lub w wierszu. Podczas udostępniania zewnętrznego pliku parametrów należy użyć `parametersLink` Właściwości:

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.parameters.json",
        "contentVersion": "1.0.0.0"
      }
    }
  }
]
```

Aby przekazać wartości parametrów w tekście, użyj `parameters` właściwości.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
      "templateLink": {
        "uri": "https://mystorageaccount.blob.core.windows.net/AzureTemplates/newStorageAccount.json",
        "contentVersion": "1.0.0.0"
      },
      "parameters": {
        "storageAccountName": {
          "value": "[parameters('storageAccountName')]"
        }
      }
    }
  }
]
```

Nie można użyć obu parametrów wbudowanych i linku do pliku parametrów. Wdrożenie nie powiedzie się z powodu błędu `parametersLink` , gdy oba i `parameters` są określone.

### <a name="use-relative-path-for-linked-templates"></a>Użyj ścieżki względnej dla połączonych szablonów

`relativePath`Właściwość `Microsoft.Resources/deployments` ułatwia tworzenie połączonych szablonów. Ta właściwość może służyć do wdrożenia zdalnego szablonu połączonego w lokalizacji względnej względem elementu nadrzędnego. Ta funkcja wymaga, aby wszystkie pliki szablonów były przygotowane i dostępne przy użyciu zdalnego identyfikatora URI, takiego jak GitHub lub konto usługi Azure Storage. Gdy główny szablon jest wywoływany przy użyciu identyfikatora URI z Azure PowerShell lub interfejsu wiersza polecenia platformy Azure, identyfikator URI wdrożenia podrzędnego jest kombinacją elementów nadrzędnych i relativePath.

> [!NOTE]
> Podczas tworzenia templateSpec wszystkie szablony, do których odwołuje się `relativePath` Właściwość, są spakowane w zasobie templateSpec przez Azure PowerShell lub interfejs wiersza polecenia platformy Azure. Pliki nie wymagają przemieszczania. Aby uzyskać więcej informacji, zobacz [Tworzenie specyfikacji szablonu z połączonymi szablonami](./template-specs.md#create-a-template-spec-with-linked-templates).

Przyjmij strukturę folderu w następujący sposób:

![ścieżka względna szablonu połączonego z Menedżerem zasobów](./media/linked-templates/resource-manager-linked-templates-relative-path.png)

Poniższy szablon pokazuje, w jaki sposób *mainTemplate.js* wdrożenia *nestedChild.jsna* zilustrowany na powyższym obrazie.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "functions": [],
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "childLinked",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "children/nestedChild.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

W poniższym wdrożeniu identyfikator URI połączonego szablonu w poprzednim szablonie to **https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/children/nestedChild.json** .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/linked-template-relpath/mainTemplate.json"
```

---

Aby wdrożyć połączone szablony ze ścieżką względną przechowywaną na koncie usługi Azure Storage, użyj `QueryString` / `query-string` parametru, aby określić token sygnatury dostępu współdzielonego, który ma być używany z parametrem TemplateUri. Ten parametr jest obsługiwany tylko w interfejsie wiersza polecenia platformy Azure w wersji 2,18 lub nowszej i Azure PowerShell w wersji 5,4 lub nowszej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name linkedTemplateWithRelativePath `
  -ResourceGroupName "myResourceGroup" `
  -TemplateUri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" `
  -QueryString $sasToken
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

---

Upewnij się, że w ciągu QueryString nie występuje wiodący znak "?". Wdrożenie dodaje je podczas składania identyfikatora URI dla wdrożeń.

## <a name="template-specs"></a>Specyfikacje szablonu

Zamiast utrzymywać połączone szablony w dostępnym punkcie końcowym, można utworzyć [specyfikację szablonu](template-specs.md) , która pakuje szablon główny i jego połączone szablony w jedną jednostkę, którą można wdrożyć. Specyfikacja szablonu jest zasobem w ramach subskrypcji platformy Azure. Ułatwia to bezpieczne udostępnianie szablonu użytkownikom w organizacji. Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) można udzielić dostępu do specyfikacji szablonu. Ta funkcja jest obecnie dostępna w wersji zapoznawczej.

Aby uzyskać więcej informacji, zobacz:

- [Samouczek: Tworzenie specyfikacji szablonu z połączonymi szablonami](./template-specs-create-linked.md).
- [Samouczek: wdrażanie specyfikacji szablonu jako połączonego szablonu](./template-specs-deploy-linked-template.md).

## <a name="dependencies"></a>Zależności

Podobnie jak w przypadku innych typów zasobów, można ustawić zależności między połączonymi szablonami. Jeśli zasoby w jednym połączonym szablonie muszą zostać wdrożone przed zasobami w drugim połączonym szablonie, należy ustawić drugi szablon zależny od pierwszego.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/linked-dependency.json" highlight="10,22,24":::

## <a name="contentversion"></a>Contentversion —

Nie musisz podawać `contentVersion` właściwości `templateLink` lub `parametersLink` właściwości. Jeśli nie podano `contentVersion` , zostanie wdrożona bieżąca wersja szablonu. W przypadku podania wartości wersji zawartości musi ona być zgodna z wersją w połączonym szablonie; w przeciwnym razie wdrożenie zakończy się niepowodzeniem z powodu błędu.

## <a name="using-variables-to-link-templates"></a>Używanie zmiennych do łączenia szablonów

Poprzednie przykłady wykazywały zakodowane wartości adresu URL dla linków szablonów. Takie podejście może obsłużyć prosty szablon, ale nie działa prawidłowo w przypadku dużego zestawu szablonów modułowych. Zamiast tego można utworzyć zmienną statyczną, która przechowuje podstawowy adres URL dla szablonu głównego, a następnie dynamicznie tworzy adresy URL dla połączonych szablonów z tego podstawowego adresu URL. Zaletą tego podejścia jest możliwość łatwego przenoszenia szablonu lub rozwidlenia go, ponieważ należy zmienić tylko zmienną statyczną w szablonie głównym. Główny szablon przekazuje poprawne identyfikatory URI w całym rozłożonym szablonie.

Poniższy przykład pokazuje, jak użyć podstawowego adresu URL, aby utworzyć dwa adresy URL dla połączonych szablonów ( `sharedTemplateUrl` i `vmTemplateUrl` ).

```json
"variables": {
  "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
  "sharedTemplateUrl": "[uri(variables('templateBaseUrl'), 'shared-resources.json')]",
  "vmTemplateUrl": "[uri(variables('templateBaseUrl'), 'database-2disk-resources.json')]"
}
```

Możesz również użyć [wdrożenia ()](template-functions-deployment.md#deployment) , aby uzyskać podstawowy adres URL dla bieżącego szablonu i użyć go, aby uzyskać adres URL dla innych szablonów w tej samej lokalizacji. Takie podejście jest przydatne, jeśli lokalizacja szablonu ulegnie zmianie lub chcesz uniknąć twardych adresów URL kodowania w pliku szablonu. `templateLink`Właściwość jest zwracana tylko w przypadku łączenia się z szablonem zdalnym przy użyciu adresu URL. Jeśli używasz szablonu lokalnego, ta właściwość jest niedostępna.

```json
"variables": {
  "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
}
```

Ostatecznie należy użyć zmiennej we `uri` właściwości `templateLink` właściwości.

```json
"templateLink": {
 "uri": "[variables('sharedTemplateUrl')]",
 "contentVersion":"1.0.0.0"
}
```

## <a name="using-copy"></a>Używanie kopiowania

Aby utworzyć wiele wystąpień zasobu z zagnieżdżonym szablonem, Dodaj `copy` element na poziomie `Microsoft.Resources/deployments` zasobu. Lub, jeśli zakresem jest `inner` , można dodać kopię w szablonie zagnieżdżonym.

Poniższy przykładowy szablon pokazuje, jak używać `copy` z zagnieżdżonym szablonem.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "[concat('nestedTemplate', copyIndex())]",
    // yes, copy works here
    "copy": {
      "name": "storagecopy",
      "count": 2
    },
    "properties": {
      "mode": "Incremental",
      "expressionEvaluationOptions": {
        "scope": "inner"
      },
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(variables('storageName'), copyIndex())]",
            "location": "West US",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "StorageV2"
            // Copy works here when scope is inner
            // But, when scope is default or outer, you get an error
            // "copy": {
            //   "name": "storagecopy",
            //   "count": 2
            // }
          }
        ]
      }
    }
  }
]
```

## <a name="get-values-from-linked-template"></a>Pobieranie wartości z połączonego szablonu

Aby uzyskać wartość wyjściową z połączonego szablonu, Pobierz wartość właściwości z składnią, taką jak: `"[reference('deploymentName').outputs.propertyName.value]"` .

Podczas pobierania właściwości wyjściowej z połączonego szablonu, nazwa właściwości nie może zawierać kreski.

W poniższych przykładach pokazano, jak odwoływać się do połączonego szablonu i pobrać wartość wyjściową. Połączony szablon zwraca prosty komunikat. Pierwszy połączony szablon:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworld.json":::

Szablon główny wdraża połączony szablon i pobiera zwróconą wartość. Zwróć uwagę, że odwołuje się do zasobu wdrożenia według nazwy i używa nazwy właściwości zwróconej przez połączony szablon.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/helloworldparent.json" highlight="10,23":::

W poniższym przykładzie przedstawiono szablon, który wdraża publiczny adres IP i zwraca identyfikator zasobu zasobu platformy Azure dla tego publicznego adresu IP:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip.json" highlight="27":::

Aby użyć publicznego adresu IP z poprzedniego szablonu podczas wdrażania modułu równoważenia obciążenia, Połącz się z szablonem i Zadeklaruj zależność od `Microsoft.Resources/deployments` zasobu. Publiczny adres IP w module równoważenia obciążenia jest ustawiany na wartość wyjściową z połączonego szablonu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json" highlight="28,41":::

## <a name="deployment-history"></a>Historia wdrożenia

Menedżer zasobów przetwarza każdy szablon jako oddzielne wdrożenie w historii wdrażania. Szablon główny z trzema połączonymi lub zagnieżdżonymi szablonami pojawia się w historii wdrożenia jako:

![Historia wdrożenia](./media/linked-templates/deployment-history.png)

Możesz użyć tych oddzielnych wpisów w historii w celu pobrania wartości wyjściowych po wdrożeniu. Poniższy szablon tworzy publiczny adres IP i wyprowadza adres IP:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "publicIPAddresses_name": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Network/publicIPAddresses",
      "apiVersion": "2018-11-01",
      "name": "[parameters('publicIPAddresses_name')]",
      "location": "southcentralus",
      "properties": {
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Static",
        "idleTimeoutInMinutes": 4,
        "dnsSettings": {
          "domainNameLabel": "[concat(parameters('publicIPAddresses_name'), uniqueString(resourceGroup().id))]"
        }
      },
      "dependsOn": []
    }
  ],
  "outputs": {
    "returnedIPAddress": {
      "type": "string",
      "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]"
    }
  }
}
```

Poniższy szablon zawiera linki do poprzedniego szablonu. Tworzy trzy publiczne adresy IP.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "[concat('linkedTemplate', copyIndex())]",
      "copy": {
        "count": 3,
        "name": "ip-loop"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
        "uri": "[uri(deployment().properties.templateLink.uri, 'static-public-ip.json')]",
        "contentVersion": "1.0.0.0"
        },
        "parameters":{
          "publicIPAddresses_name":{"value": "[concat('myip-', copyIndex())]"}
        }
      }
    }
  ]
}
```

Po wdrożeniu można pobrać wartości wyjściowe z następującym skryptem programu PowerShell:

```azurepowershell-interactive
$loopCount = 3
for ($i = 0; $i -lt $loopCount; $i++)
{
  $name = 'linkedTemplate' + $i;
  $deployment = Get-AzResourceGroupDeployment -ResourceGroupName examplegroup -Name $name
  Write-Output "deployment $($deployment.DeploymentName) returned $($deployment.Outputs.returnedIPAddress.value)"
}
```

Lub skrypt interfejsu wiersza polecenia platformy Azure w bash Shell:

```azurecli-interactive
#!/bin/bash

for i in 0 1 2;
do
  name="linkedTemplate$i";
  deployment=$(az deployment group show -g examplegroup -n $name);
  ip=$(echo $deployment | jq .properties.outputs.returnedIPAddress.value);
  echo "deployment $name returned $ip";
done
```

## <a name="securing-an-external-template"></a>Zabezpieczanie szablonu zewnętrznego

Mimo że połączony szablon musi być dostępny zewnętrznie, nie musi być ogólnie dostępny publicznie. Szablon można dodać do prywatnego konta magazynu, które jest dostępne tylko dla właściciela konta magazynu. Następnie można utworzyć token sygnatury dostępu współdzielonego (SAS), aby umożliwić dostęp podczas wdrażania. Ten token sygnatury dostępu współdzielonego należy dodać do identyfikatora URI dla połączonego szablonu. Mimo że token jest przekazaniem jako bezpieczny ciąg, identyfikator URI połączonego szablonu, łącznie z tokenem sygnatury dostępu współdzielonego, jest rejestrowany w operacjach wdrażania. Aby ograniczyć widoczność, ustaw wartość opcji Wygaśnięcie dla tokenu.

Plik parametrów można także ograniczyć do dostępu za pomocą tokenu SAS.

Obecnie nie można połączyć się z szablonem na koncie magazynu, które znajduje się za [zaporą usługi Azure Storage](../../storage/common/storage-network-security.md).

> [!IMPORTANT]
> Zamiast zabezpieczać połączony szablon przy użyciu tokenu sygnatury dostępu współdzielonego, rozważ utworzenie [specyfikacji szablonu](template-specs.md). Specyfikacja szablonu bezpiecznie przechowuje szablon główny i jego połączone szablony jako zasób w ramach subskrypcji platformy Azure. Korzystając z usługi Azure RBAC, Udziel dostępu użytkownikom, którzy muszą wdrożyć szablon.

Poniższy przykład pokazuje, jak przekazać token SAS podczas tworzenia połączenia z szablonem:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerSasToken": { "type": "securestring" }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "linkedTemplate",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
          "contentVersion": "1.0.0.0"
        }
      }
    }
  ],
  "outputs": {
  }
}
```

W programie PowerShell otrzymujesz token dla kontenera i wdrażasz szablony przy użyciu następujących poleceń. Należy zauważyć, że `containerSasToken` parametr jest zdefiniowany w szablonie. Nie jest to parametr w `New-AzResourceGroupDeployment` poleceniu.

```azurepowershell-interactive
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
$token = New-AzStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
$url = (Get-AzStorageBlob -Container templates -Blob parent.json).ICloudBlob.uri.AbsoluteUri
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ($url + $token) -containerSasToken $token
```

W przypadku interfejsu wiersza polecenia platformy Azure w powłoce bash otrzymujesz token dla kontenera i wdrażasz szablony przy użyciu następującego kodu:

```azurecli-interactive
#!/bin/bash

expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
  --resource-group ManageGroup \
  --name storagecontosotemplates \
  --query connectionString)
token=$(az storage container generate-sas \
  --name templates \
  --expiry $expiretime \
  --permissions r \
  --output tsv \
  --connection-string $connection)
url=$(az storage blob url \
  --container-name templates \
  --name parent.json \
  --output tsv \
  --connection-string $connection)
parameter='{"containerSasToken":{"value":"?'$token'"}}'
az deployment group create --resource-group ExampleGroup --template-uri $url?$token --parameters $parameter
```

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono typowe zastosowania połączonych szablonów.

|Szablon główny  |Szablon połączony |Opis  |
|---------|---------| ---------|
|[Witaj, świecie](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworldparent.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/helloworld.json) | Zwraca ciąg z połączonego szablonu. |
|[Load Balancer z publicznym adresem IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) |[połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) |Zwraca publiczny adres IP z połączonego szablonu i ustawia tę wartość w module równoważenia obciążenia. |
|[Wiele adresów IP](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip-parent.json) | [połączony szablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/static-public-ip.json) |Tworzy kilka publicznych adresów IP w połączonym szablonie.  |

## <a name="next-steps"></a>Następne kroki

* Aby przejść przez samouczek, zobacz [Samouczek: wdrażanie połączonego szablonu](./deployment-tutorial-linked-template.md).
* Aby dowiedzieć się więcej na temat definiowania kolejności wdrażania zasobów, zobacz [Definiowanie kolejności wdrażania zasobów w usłudze ARM](define-resource-dependency.md).
* Aby dowiedzieć się, jak zdefiniować jeden zasób, ale utworzyć wiele jego wystąpień, zobacz [iteracja zasobów w szablonach ARM](copy-resources.md).
* Aby uzyskać instrukcje dotyczące konfigurowania szablonu na koncie magazynu i generowania tokenu SAS, zobacz [wdrażanie zasobów przy użyciu szablonów usługi ARM i Azure PowerShell](deploy-powershell.md) lub [wdrażanie zasobów za pomocą szablonów ARM i interfejsu wiersza polecenia platformy Azure](deploy-cli.md).
