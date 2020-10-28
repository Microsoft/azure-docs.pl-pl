---
title: Wdrażanie za pomocą Terraform z Azure Cloud Shell | Microsoft Docs
description: Wdrażanie za pomocą Terraform z Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: dbbd1511a6063223a2b76786b3c7c4394e2fa407
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92745439"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Wdrażanie za pomocą Terraform z bash w Azure Cloud Shell
W tym artykule przedstawiono sposób tworzenia grupy zasobów za pomocą [dostawcy Terraform AzureRM](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp Terraform](https://www.terraform.io/) to narzędzie Open Source, które zawiera kodyfikacje interfejsów API w plikach konfiguracji deklaracyjnej, które mogą być współużytkowane przez członków zespołu do edytowania, recenzowania i obsługi wersji. Dostawca Microsoft AzureRM jest używany do współpracy z zasobami obsługiwanymi przez Azure Resource Manager za pośrednictwem interfejsów API AzureRM.

## <a name="automatic-authentication"></a>Automatyczne uwierzytelnianie
Program Terraform jest domyślnie zainstalowany w programie bash w Cloud Shell. Ponadto Cloud Shell automatycznie uwierzytelnia domyślną subskrypcję interfejsu wiersza polecenia platformy Azure w celu wdrożenia zasobów za pomocą modułów platformy Azure Terraform.

Terraform używa domyślnej subskrypcji interfejsu wiersza polecenia platformy Azure, która jest ustawiona. Aby zaktualizować domyślne subskrypcje, uruchom polecenie:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Przewodnik
### <a name="launch-bash-in-cloud-shell"></a>Uruchom bash w Cloud Shell
1. Uruchom Cloud Shell z preferowanej lokalizacji
2. Sprawdź, czy ustawiono preferowaną subskrypcję

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Tworzenie szablonu Terraform
Utwórz nowy szablon Terraform o nazwie main.tf z preferowanym edytorem tekstu.

```
vim main.tf
```

Skopiuj/wklej poniższy kod do Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Zapisz plik i Zamknij Edytor tekstu.

### <a name="terraform-init"></a>Terraform init
Zacznij od uruchomienia `terraform init` .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

[Polecenie init Terraform](https://www.terraform.io/docs/commands/init.html) służy do inicjowania katalogu roboczego zawierającego pliki konfiguracji Terraform. `terraform init`Polecenie to pierwsze polecenie, które powinno być uruchamiane po zapisaniu nowej konfiguracji Terraform lub klonowania istniejącej z kontroli wersji. Można bezpiecznie uruchomić to polecenie wiele razy.

### <a name="terraform-plan"></a>Terraform plan
Wyświetl podgląd zasobów, które zostaną utworzone przez szablon Terraform za pomocą programu `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

[Polecenie terraform plan](https://www.terraform.io/docs/commands/plan.html) służy do tworzenia planu wykonania. Terraform wykonuje odświeżanie, chyba że zostanie jawnie wyłączone, a następnie określa, jakie akcje są niezbędne do osiągnięcia żądanego stanu określonego w plikach konfiguracji. Plan może być zapisany przy użyciu, a następnie dostarczany do terraform ma mieć zastosowanie tylko w celu zapewnienia, że wykonywane są tylko wstępnie zaplanowane akcje.

### <a name="terraform-apply"></a>Terraform apply
Inicjowanie obsługi administracyjnej zasobów platformy Azure za pomocą programu `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

[Polecenie Terraform Apply](https://www.terraform.io/docs/commands/apply.html) służy do zastosowania zmian wymaganych do osiągnięcia żądanego stanu konfiguracji.

### <a name="verify-deployment-with-azure-cli"></a>Weryfikowanie wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure
Uruchom `az group show -n myRgName` , aby sprawdzić, czy zasób zakończył się pomyślnie.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Oczyść przy użyciu Terraform niszczenia
Wyczyść grupę zasobów utworzoną za pomocą [polecenia Destroy Terraform](https://www.terraform.io/docs/commands/destroy.html) , aby oczyścić infrastrukturę Terraform.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

Zasób platformy Azure został pomyślnie utworzony za pomocą Terraform. Aby kontynuować uczenie się Cloud Shell, zobacz następne kroki.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej na temat dostawcy platformy Azure Terraform](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash w Cloud Shell Szybki Start](quickstart.md)
