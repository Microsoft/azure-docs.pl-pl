---
title: Tworzenie sieci wirtualnej
titleSuffix: Azure SQL Managed Instance
description: W tym artykule opisano sposób tworzenia sieci wirtualnej skonfigurowanej do obsługi wdrażania wystąpienia zarządzanego Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 09/12/2019
ms.openlocfilehash: 2a23fc0b769727cab5a28d3d313a7791bcfa6eee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91617710"
---
# <a name="create-a-virtual-network-for-azure-sql-managed-instance"></a>Tworzenie sieci wirtualnej dla usługi Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

W tym artykule wyjaśniono, jak utworzyć prawidłową sieć wirtualną i podsieć, w której można wdrożyć wystąpienie zarządzane usługi Azure SQL.

Wystąpienie zarządzane Azure SQL należy wdrożyć w ramach [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)platformy Azure. To wdrożenie umożliwia realizację następujących scenariuszy:

- Bezpieczny prywatny adres IP
- Nawiązywanie połączenia z wystąpieniem zarządzanym SQL bezpośrednio z sieci lokalnej
- Łączenie wystąpienia zarządzanego SQL z serwerem połączonym lub innym lokalnym magazynem danych
- Łączenie wystąpienia zarządzanego SQL z zasobami platformy Azure  

> [!NOTE]
> Należy [określić rozmiar podsieci dla wystąpienia zarządzanego SQL](vnet-subnet-determine-size.md) przed wdrożeniem pierwszego wystąpienia. Nie można zmienić rozmiaru podsieci po umieszczeniu zasobów wewnątrz.
>
> Jeśli planujesz użycie istniejącej sieci wirtualnej, musisz zmodyfikować tę konfigurację sieci, aby uwzględnić wystąpienie zarządzane SQL. Aby uzyskać więcej informacji, zobacz [modyfikowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego SQL](vnet-existing-add-subnet.md).
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.  Przeniesienie wystąpienia zarządzanego do innej podsieci również nie jest obsługiwane.
>

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrażania Azure Resource Manager.

1. Zaloguj się do Portalu Azure.

2. Wybierz przycisk **Wdróż na platformie Azure** :

   [![Obraz przedstawiający przycisk "wdróż na platformie Azure".](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json)

   Ten przycisk otwiera formularz, którego można użyć do skonfigurowania środowiska sieciowego, w którym można wdrożyć wystąpienie zarządzane SQL.

   > [!Note]
   > Ten szablon Azure Resource Manager spowoduje wdrożenie sieci wirtualnej z dwiema podsieciami. Jedna podsieć o nazwie **ManagedInstances**jest zarezerwowana dla wystąpienia zarządzanego SQL i ma wstępnie skonfigurowaną tabelę tras. Druga podsieć o nazwie **default**jest używana w przypadku innych zasobów, które powinny mieć dostęp do wystąpienia zarządzanego SQL (na przykład Azure Virtual Machines).

3. Skonfiguruj środowisko sieciowe. W poniższej postaci można skonfigurować parametry środowiska sieciowego:

   ![Menedżer zasobów szablon służący do konfigurowania sieci platformy Azure](./media/virtual-network-subnet-create-arm-template/create-mi-network-arm.png)

   Można zmienić nazwy sieci wirtualnej i podsieci, a następnie dostosować zakresy adresów IP skojarzone z zasobami sieciowymi. Po wybraniu przycisku **Kup** ten formularz spowoduje utworzenie i skonfigurowanie środowiska. Jeśli nie są potrzebne dwie podsieci, można usunąć wartość domyślną.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane SQL?](sql-managed-instance-paas-overview.md)
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym SQL](connectivity-architecture-overview.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego SQL](vnet-existing-add-subnet.md).
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [Tworzenie wystąpienia zarządzanego](instance-create-quickstart.md).
- Problemy związane z usługą DNS można znaleźć w temacie [Configure a Custom DNS](custom-dns-configure.md).
