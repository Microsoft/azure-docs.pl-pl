---
title: 'Szybki Start: nowe przypisanie zasad z Terraform'
description: W tym przewodniku szybki start użyto składni Terraform i HCL do utworzenia przypisania zasad w celu zidentyfikowania niezgodnych zasobów.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93106397"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Szybki Start: Tworzenie przypisania zasad w celu zidentyfikowania niezgodnych zasobów przy użyciu Terraform

Pierwszym krokiem do zrozumienia pojęcia zgodności na platformie Azure jest określenie obecnej sytuacji dotyczącej Twoich zasobów.
Ten przewodnik Szybki start przeprowadzi Cię przez proces tworzenia przypisania zasad w celu zidentyfikowania maszyn wirtualnych, które nie korzystają z dysków zarządzanych.

Po zakończeniu tego procesu pomyślnie zidentyfikujesz maszyny wirtualne, które nie korzystają z dysków zarządzanych. Są one _niezgodne_ z przypisaniem zasad.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.
- [Terraform](https://www.terraform.io/) wersja 0.12.0 lub nowsza skonfigurowana w Twoim środowisku.
  Aby uzyskać instrukcje, zobacz [Konfigurowanie Terraform przy użyciu Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell).
- Ten przewodnik Szybki Start wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.13.0 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Tworzenie Terraform konfiguracji, zmiennej i pliku wyjściowego

W tym przewodniku szybki start utworzysz przypisanie zasad i przypiszesz **maszyny wirtualne inspekcji, które nie używają definicji Managed disks** ( `06a78e20-9358-41c9-923c-fb736d382a4d` ). Ta definicja zasad identyfikuje zasoby, które nie spełniają warunków określonych w definicji zasad.

Najpierw skonfiguruj Terraform konfigurację, zmienną i pliki wyjściowe. Zasoby Terraform dla Azure Policy korzystają z [dostawcy platformy Azure](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Utwórz nowy folder o nazwie `policy-assignment` i zmień katalog na nim.

1. Utwórz `main.tf` przy użyciu następującego kodu:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Utwórz `variables.tf` przy użyciu następującego kodu:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Zakres określa, jakie zasoby lub grupy zasobów są wymuszane w ramach przypisania zasad. Może ona przedziały od grupy zarządzania do pojedynczego zasobu. Pamiętaj, aby zastąpić `{scope}` jednym z następujących wzorców:

   - Subskrypcje: `/subscriptions/{subscriptionId}`
   - Grupa zasobów: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Zasoby `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Utwórz `output.tf` przy użyciu następującego kodu:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Zainicjuj Terraform i Utwórz plan

Następnie zainicjuj Terraform do pobrania wymaganych dostawców, a następnie utwórz plan.

1. Uruchom polecenie [init Terraform](https://www.terraform.io/docs/commands/init.html) . To polecenie umożliwia pobranie modułów platformy Azure wymaganych do utworzenia zasobów platformy Azure w konfiguracji Terraform.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Zrzut ekranu przedstawiający uruchamianie polecenia init Terraform, które pokazuje Pobieranie modułu azurerm i komunikat o powodzeniu.":::

1. Uwierzytelnianie za pomocą [interfejsu wiersza polecenia platformy Azure](/cli/azure/) dla Terraform. Aby uzyskać więcej informacji, zobacz [dostawca platformy Azure: uwierzytelnianie przy użyciu interfejsu wiersza polecenia platformy Azure](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Utwórz plan wykonywania za pomocą polecenia [Terraform plan](https://www.terraform.io/docs/commands/plan.html) i parametru **out** .

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Zrzut ekranu przedstawiający uruchamianie polecenia i parametru out planu Terraform w celu wyświetlenia zasobu platformy Azure, który ma zostać utworzony.":::

   > [!NOTE]
   > Aby uzyskać informacje na temat utrwalania planów wykonywania i zabezpieczeń, zobacz [Terraform plan: Ostrzeżenie o zabezpieczeniach](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Zastosowanie planu wykonania programu Terraform

Na koniec zastosuj plan wykonania.

Uruchom polecenie [Terraform Apply](https://www.terraform.io/docs/commands/apply.html) i określ `assignment.tfplan` już utworzone.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Zrzut ekranu przedstawiający uruchamianie polecenia Terraform Apply i powstającego tworzenia zasobów.":::

Z opcją "Zastosuj wszystko! Zasoby: 1 dodano, 0 zmieniono, 0 zniszczono. " zostanie utworzone przypisanie zasad. Ponieważ `outputs.tf` plik został zdefiniowany, zwracany jest _również \_ identyfikator przypisania_ .

## <a name="identify-non-compliant-resources"></a>Identyfikowanie niezgodnych zasobów

Aby wyświetlić zasoby, które nie są zgodne z tym nowym przypisaniem, użyj _\_ identyfikatora przypisania_ zwróconego przez `terraform apply` . W tym celu uruchom następujące polecenie, aby pobrać identyfikatory zasobów niezgodnych zasobów, które są wyprowadzane do pliku JSON:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

Wyniki powinny wyglądać podobnie do następujących:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

Wyniki są porównywalne z zawartością zwykle wyświetlaną w obszarze **Niezgodne zasoby** w widoku witryny Azure Portal.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć utworzone przypisanie, użyj interfejsu wiersza polecenia platformy Azure lub Wróć do planu wykonywania Terraform z `terraform destroy` .

- Interfejs wiersza polecenia platformy Azure

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start została przypisana definicja zasad mających na celu zidentyfikowanie niezgodnych zasobów w środowisku platformy Azure.

Aby dowiedzieć się więcej na temat przypisywania zasad w celu sprawdzenia zgodności nowych zasobów, przejdź do samouczka:

> [!div class="nextstepaction"]
> [Tworzenie zasad i zarządzanie nimi](./tutorials/create-and-manage.md)
