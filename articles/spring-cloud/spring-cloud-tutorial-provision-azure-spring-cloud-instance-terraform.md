---
title: Aprowizowanie wystąpienia usługi Azure Spring Cloud przy użyciu narzędzia Terraform
description: Inicjowanie obsługi administracyjnej wystąpienia chmurowego platformy Azure z Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533795"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Inicjowanie obsługi administracyjnej wystąpienia chmury Azure wiosny with Terraform

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

W tym przykładzie tworzony jest wystąpienie chmury wiosennej platformy Azure przy użyciu Terraform. Procedury te przeprowadzą Cię przez proces tworzenia następujących zasobów:

> [!div class="checklist"]
> * Grupa zasobów
> * Wystąpienie chmury Azure wiosennej
> * Usługa Azure Storage dla Log Analytics

> [!NOTE]
> Aby uzyskać pomoc techniczną dla konkretnych Terraform, użyj jednego z kanałów pomocy technicznej społeczności HashiCorp do terraform:
>
> * Pytania, przypadki użycia i przydatne wzorce: [sekcja Terraform w portalu społeczności HashiCorp](https://discuss.hashicorp.com/c/terraform-core)
> * Pytania dotyczące dostawcy: [sekcja Terraform Providers The HashiCorp Community Portal](https://discuss.hashicorp.com/c/terraform-providers)

## <a name="prerequisites"></a>Wymagania wstępne

- **Subskrypcja platformy Azure**: jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-configuration-file"></a>Tworzenie pliku konfiguracji

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otwórz [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Konfiguracja w tym kroku obejmuje modele zasobów platformy Azure, w tym grupę zasobów platformy Azure i wystąpienie chmury wiosennej na platformie Azure.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Zapisz plik (**&lt; Ctrl>S**) i wyjdź z edytora (**&lt; Ctrl>Q**).

## <a name="apply-the-configuration"></a>Zastosuj konfigurację

W tej sekcji użyjesz kilku poleceń Terraform, aby uruchomić konfigurację.

1. Polecenie [terraform init](https://www.terraform.io/docs/commands/init.html) inicjuje katalog roboczy. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform init
    ```

1. [Plan Terraform](https://www.terraform.io/docs/commands/plan.html) polecenia jest używany do sprawdzania poprawności składni konfiguracji. `-out`Parametr kieruje wyniki do pliku. Plik wyjściowy może być później używany do zastosowania konfiguracji. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform plan --out plan.out
    ```

1. Zastosowanie polecenia [Terraform](https://www.terraform.io/docs/commands/apply.html) służy do zastosowania konfiguracji. Polecenie Określa plik wyjściowy z poprzedniego kroku. To polecenie tworzy zasoby platformy Azure. Uruchom następujące polecenie w Cloud Shell:

    ```bash
    terraform apply plan.out
    ```

1. Aby sprawdzić wyniki w Azure Portal, przejdź do nowej grupy zasobów. Nowe wystąpienie **chmury Azure wiosenne** zostanie wyświetlone w nowej grupie zasobów.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Aktualizowanie konfiguracji do dzienników konfiguracji i metryk

W tej sekcji pokazano, jak zaktualizować konfigurację, aby włączyć rejestrowanie i metryki dla chmury wiosennej platformy Azure za pomocą konta usługi Azure Storage.

1. Uruchom Edytor Cloud Shell:

    ```bash
    code main.tf
    ```

1. Dodaj następującą konfigurację na końcu pliku:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Zapisz plik (**&lt; Ctrl>S**) i wyjdź z edytora (**&lt; Ctrl>Q**).

1. Tak jak w poprzedniej sekcji, uruchom następujące polecenie, aby wprowadzić zmiany:

    ```bash
    terraform plan --out plan.out
    ```

1. Uruchom `terraform apply` polecenie, aby zastosować konfigurację.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, Usuń zasoby utworzone w tym artykule.

Uruchom polecenie [Terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) , aby usunąć zasoby platformy Azure utworzone w ramach tego ćwiczenia:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zainstaluj i skonfiguruj Terraform, aby udostępnić zasoby platformy Azure](/azure/developer/terraform/getting-started-cloud-shell).