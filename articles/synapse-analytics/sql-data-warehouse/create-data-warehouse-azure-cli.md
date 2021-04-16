---
title: 'Szybki start: tworzenie puli Synapse SQL za pomocą interfejsu wiersza polecenia platformy Azure'
description: Szybko utwórz pulę Synapse SQL za pomocą reguły zapory na poziomie serwera przy użyciu interfejsu wiersza polecenia platformy Azure.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/20/2020
ms.author: jrasnick
ms.custom: azure-synapse, devx-track-azurecli
ms.openlocfilehash: 532c06cc49f9fddab09c6678ee5fff9fe32347cb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565937"
---
# <a name="quickstart-create-a-synapse-sql-pool-with-azure-cli"></a>Szybki start: tworzenie puli Synapse SQL za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz pulę Synapse SQL (magazyn danych) w usłudze Azure Synapse Analytics użyciu interfejsu wiersza polecenia platformy Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="getting-started"></a>Wprowadzenie

Użyj tych poleceń, aby zalogować się do platformy Azure i skonfigurować grupę zasobów.

1. Jeśli używasz instalacji lokalnej, uruchom polecenie [az login,](/cli/azure/reference-index#az_login) aby zalogować się na platformie Azure:

   ```azurecli
   az login
   ```

1. W razie potrzeby użyj [polecenia az account set,](/cli/azure/account#az_account_set) aby wybrać subskrypcję:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Uruchom polecenie [az group create,](/cli/azure/group#az_group_create) aby utworzyć grupę zasobów:

   ```azurecli
   az group create --name myResourceGroup --location WestEurope
   ```

1. Utwórz serwer [logiczny SQL przy](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) użyciu [polecenia az sql server create:](/cli/azure/sql/server#az_sql_server_create)

   ```azurecli
   az sql server create --resource-group myResourceGroup --name mysqlserver \
      --admin-user ServerAdmin --admin-password ChangeYourAdminPassword1
   ```

   Serwer zawiera grupę baz danych zarządzanych jako grupa.

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurowanie reguły zapory na poziomie serwera

Utwórz [regułę zapory na poziomie serwera](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Reguła zapory na poziomie serwera umożliwia aplikacji zewnętrznej, takiej jak SQL Server Management Studio lub narzędzie SQLCMD, łączenie się z pulą SQL za pośrednictwem zapory usługi puli SQL.

Uruchom polecenie [az sql server firewall-rule create,](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) aby utworzyć regułę zapory:

```azurecli
az sql server firewall-rule create --resource-group myResourceGroup --name AllowSome \
   --server mysqlserver --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

W tym przykładzie zapora jest otwierana tylko dla innych zasobów platformy Azure. Aby włączyć łączność zewnętrzną, zmień adres IP na adres odpowiedni dla danego środowiska. Aby otworzyć wszystkie adresy IP, użyj wartości 0.0.0.0 jako początkowego adresu IP i wartości 255.255.255.255 jako adresu końcowego.

> [!NOTE]
> Punkty końcowe SQL komunikują się za pośrednictwem portu 1433. Jeśli próbujesz nawiązać połączenie z sieci firmowej, ruch wychodzący na porcie 1433 może być zablokowany przez zaporę sieciową. Jeśli tak, nie będzie można nawiązać połączenia z serwerem, chyba że dział IT otworzy port 1433.
>

## <a name="create-and-manage-your-sql-pool"></a>Tworzenie puli SQL i zarządzanie nimi

Utwórz pulę SQL. W tym przykładzie użyto dw100c jako celu usługi, który jest tańszym punktem początkowym dla puli SQL.

> [!NOTE]
> Potrzebujesz wcześniej utworzonego obszaru roboczego. Aby uzyskać więcej informacji, zobacz Szybki start: tworzenie obszaru roboczego [usługi Azure Synapse przy użyciu interfejsu wiersza polecenia platformy Azure.](../quickstart-create-workspace-cli.md)

Użyj polecenia [az synapse sql pool create,](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_create) aby utworzyć pulę SQL:

```azurecli
az synapse sql pool create --resource-group myResourceGroup --name mySampleDataWarehouse \
   --performance-level "DW1000c" --workspace-name testsynapseworkspace
```

Aby uzyskać więcej informacji na temat opcji parametrów, zobacz [az synapse sql pool](/cli/azure/ext/synapse/synapse/sql/pool).

Pule SQL można wyświetlić za pomocą [polecenia az synapse sql pool list:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_list)

```azurecli
az synapse sql pool list --resource-group myResourceGroup --workspace-name testsynapseworkspace
```

Użyj polecenia [az synapse sql pool update,](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_update) aby zaktualizować istniejącą pulę:

```azurecli
az synapse sql pool update --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Użyj polecenia [az synapse sql pool pause,](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_pause) aby wstrzymać pulę:

```azurecli
az synapse sql pool pause --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Użyj polecenia [az synapse sql pool resume,](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_resume) aby uruchomić wstrzymaną pulę:

```azurecli
az synapse sql pool resume --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

Aby usunąć istniejącą pulę SQL, użyj [polecenia az synapse sql pool delete:](/cli/azure/ext/synapse/synapse/sql/pool#ext_synapse_az_synapse_sql_pool_delete)

```azurecli
az synapse sql pool delete --resource-group myResourceGroup --name mySampleDataWarehouse \
   --workspace-name testsynapseworkspace
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Inne samouczki Szybki start w tej kolekcji bazują na tym przewodniku Szybki start.

> [!TIP]
> Jeśli planujesz kontynuować pracę z nowszymi samouczkami Szybki start, nie czyść zasobów utworzonych w tym przewodniku Szybki start. Jeśli nie planujesz kontynuować pracy, użyj [polecenia az group delete,](/cli/azure/group#az_group_delete) aby usunąć wszystkie zasoby utworzone w tym przewodniku Szybki start.
>

```azurecli
az group delete --ResourceGroupName MyResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Utworzono pulę SQL, utworzono regułę zapory i nałączono z pulą SQL. Aby dowiedzieć się więcej, przejdź do artykułu [Ładowanie danych do puli SQL.](./load-data-from-azure-blob-storage-using-copy.md)
