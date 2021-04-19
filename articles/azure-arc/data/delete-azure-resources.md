---
title: Usuwanie zasobów z platformy Azure
description: Usuwanie zasobów z platformy Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716083"
---
# <a name="delete-resources-from-azure"></a>Usuwanie zasobów z platformy Azure

W tym artykule opisano sposób usuwania zasobów z platformy Azure.

> [!WARNING]
> Po usunięciu zasobów zgodnie z opisem w tym artykule te akcje są nieodwracalne.

W trybie połączenia pośredniego usunięcie wystąpienia z platformy Kubernetes nie spowoduje usunięcia go z platformy Azure, a usunięcie wystąpienia z platformy Azure nie spowoduje usunięcia go z platformy Kubernetes. W przypadku trybu połączenia pośredniego usuwanie zasobu jest procesem dwuetapowym, który zostanie ulepszony w przyszłości. Źródłem prawdy będzie kubernetes, a portal zostanie zaktualizowany w celu jego odzwierciedlenia.

W niektórych przypadkach może być konieczne ręczne usunięcie zasobów usługi danych z obsługą usługi Azure Arc platformie Azure.  Te zasoby można usunąć przy użyciu dowolnej z poniższych opcji.

- [Usuwanie zasobów z platformy Azure](#delete-resources-from-azure)
  - [Usuwanie całej grupy zasobów](#delete-an-entire-resource-group)
  - [Usuwanie określonych zasobów w grupie zasobów](#delete-specific-resources-in-the-resource-group)
  - [Usuwanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-resources-using-the-azure-cli)
    - [Usuwanie zasobów wystąpienia zarządzanego SQL przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Usuwanie zasobów grupy serwerów PostgreSQL w chmurze w chmurze za pomocą interfejsu wiersza polecenia platformy Azure](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Usuwanie Azure Arc kontrolera danych przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Usuwanie grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Usuwanie całej grupy zasobów

Jeśli używasz określonej i dedykowanej grupy zasobów dla usługi  usługi danych z obsługą usługi Azure Arc i chcesz usunąć wszystkie zasoby w grupie zasobów, możesz usunąć grupę zasobów, co spowoduje usunięcie wszystkich jej zasobów.  

Możesz usunąć grupę zasobów w Azure Portal, wykonując następujące czynności:

- Przejdź do grupy zasobów w Azure Portal, usługi danych z obsługą usługi Azure Arc zasoby zostały utworzone.
- Kliknij przycisk **Usuń grupę** zasobów.
- Potwierdź usunięcie, wprowadzając nazwę grupy zasobów, a następnie kliknij pozycję **Usuń.**

## <a name="delete-specific-resources-in-the-resource-group"></a>Usuwanie określonych zasobów w grupie zasobów

Możesz usunąć określone usługi danych z obsługą usługi Azure Arc zasobów w grupie zasobów w Azure Portal, wykonując następujące czynności:

- Przejdź do grupy zasobów w Azure Portal, usługi danych z obsługą usługi Azure Arc zasoby zostały utworzone.
- Wybierz wszystkie zasoby do usunięcia.
- Kliknij przycisk Usuń.
- Potwierdź usunięcie, wpisując "yes" (tak), a następnie kliknij pozycję **Delete (Usuń).**

## <a name="delete-resources-using-the-azure-cli"></a>Usuwanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz usunąć określone zasoby usługi danych z obsługą usługi Azure Arc pomocą interfejsu wiersza polecenia platformy Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Usuwanie zasobów wystąpienia zarządzanego SQL przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć zasoby wystąpienia zarządzanego SQL z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, zastąp wartości symboli zastępczych w poniższym poleceniu i uruchom je.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Usuwanie zasobów grupy serwerów PostgreSQL w chmurze w chmurze przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć zasób grupy serwerów PostgreSQL w chmurze w chmurze z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, zastąp wartości symboli zastępczych w poniższym poleceniu i uruchom go.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Usuwanie Azure Arc kontrolera danych przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Przed usunięciem Azure Arc danych należy usunąć wszystkie zasoby wystąpienia bazy danych, które zarządza.

Aby usunąć kontroler Azure Arc z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, zastąp wartości symboli zastępczych w poniższym poleceniu i uruchom go.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Usuwanie grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz również użyć interfejsu wiersza polecenia platformy [Azure, aby usunąć grupę zasobów](../../azure-resource-manager/management/delete-resource-group.md).