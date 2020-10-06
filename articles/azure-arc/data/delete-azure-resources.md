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
ms.openlocfilehash: 46c1df8a175657e20dce984d0c8825ae99170246
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91761655"
---
# <a name="delete-resources-from-azure"></a>Usuwanie zasobów z platformy Azure

> [!NOTE]
>  Opcje usuwania zasobów w tym artykule są nieodwracalne.

> [!NOTE]
>  Ponieważ jedynym trybem łączności oferowanym dla usług danych z włączoną funkcją Azure Arc jest obecnie tryb połączonego pośredniego, usunięcie wystąpienia z Kubernetes nie spowoduje usunięcia go z platformy Azure, a usunięcie wystąpienia z platformy Azure nie spowoduje usunięcia go z Kubernetes.  Na potrzeby teraz usuwanie zasobu jest procesem dwuetapowym i zostanie to udoskonalone w przyszłości.  Dzięki temu Kubernetes będzie źródłem prawdziwości, a platforma Azure zostanie zaktualizowana w celu odzwierciedlenia tej usługi.

W niektórych przypadkach może być konieczne ręczne usunięcie zasobów usług danych z obsługą usługi Azure Arc w Azure Resource Manager (ARM).  Możesz usunąć te zasoby przy użyciu dowolnej z poniższych opcji.

- [Usuwanie zasobów z platformy Azure](#delete-resources-from-azure)
  - [Usuwanie całej grupy zasobów](#delete-an-entire-resource-group)
  - [Usuwanie określonych zasobów w grupie zasobów](#delete-specific-resources-in-the-resource-group)
  - [Usuwanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-resources-using-the-azure-cli)
    - [Usuwanie zasobów wystąpienia zarządzanego SQL przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Usuwanie zasobów grupy serwerów PostgreSQL ze skalowaniem przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Usuwanie zasobów usługi Azure Arc Data Controller przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Usuwanie grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Usuwanie całej grupy zasobów
Jeśli używasz konkretnej i dedykowanej grupy zasobów dla usług danych z obsługą usługi Azure Arc i chcesz usunąć *wszystkie elementy* w grupie zasobów, możesz usunąć grupę zasobów, która spowoduje usunięcie wszystkich elementów.  

Grupę zasobów można usunąć w Azure Portal, wykonując następujące czynności:

- Przejdź do grupy zasobów w Azure Portal, w której zostały utworzone zasoby usług danych z obsługą usługi Azure Arc.
- Kliknij przycisk **Usuń grupę zasobów** .
- Potwierdź usunięcie, wprowadzając nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**.

## <a name="delete-specific-resources-in-the-resource-group"></a>Usuwanie określonych zasobów w grupie zasobów

W grupie zasobów w Azure Portal można usunąć określone zasoby usługi Azure ARC z włączonymi danymi, wykonując następujące czynności:

- Przejdź do grupy zasobów w Azure Portal, w której zostały utworzone zasoby usług danych z obsługą usługi Azure Arc.
- Wybierz wszystkie zasoby do usunięcia.
- Kliknij przycisk Usuń.
- Potwierdź usunięcie, wpisując ciąg "yes", a następnie klikając przycisk **Usuń**.

## <a name="delete-resources-using-the-azure-cli"></a>Usuwanie zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

W interfejsie wiersza polecenia platformy Azure można usunąć konkretne zasoby usług danych z włączonym Łukem Azure.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Usuwanie zasobów wystąpienia zarządzanego SQL przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć zasoby wystąpienia zarządzanego SQL z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, Zastąp wartości symboli zastępczych w poleceniu poniżej i uruchom je.

```console
az resource delete --name <sql instance name> --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>Usuwanie zasobów grupy serwerów PostgreSQL ze skalowaniem przy użyciu interfejsu wiersza polecenia platformy Azure

Aby usunąć zasób grupy serwerów PostgreSQL ze skalowaniem z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, Zastąp wartości symboli zastępczych w poleceniu poniżej i uruchom go.

```console
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Usuwanie zasobów usługi Azure Arc Data Controller przy użyciu interfejsu wiersza polecenia platformy Azure

> [!NOTE]
> Przed usunięciem kontrolera danych usługi Azure Arc należy usunąć wszystkie zasoby wystąpienia bazy danych, którymi zarządza.

Aby usunąć kontroler danych usługi Azure ARC z platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, Zastąp wartości symboli zastępczych w poleceniu poniżej i uruchom go.

```console
az resource delete --name <data controller name> --resource-type Microsoft.AzureData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Usuwanie grupy zasobów przy użyciu interfejsu wiersza polecenia platformy Azure

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby [usunąć grupę zasobów](/azure/azure-resource-manager/management/delete-resource-group).
