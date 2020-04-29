---
title: Utwórz sieć wirtualną dla wystąpienia zarządzanego
description: W tym artykule opisano sposób tworzenia sieci wirtualnej, w której można wdrożyć Azure SQL Database wystąpienie zarządzane.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 09/12/2019
ms.openlocfilehash: 0ce88f9a61b8aa7c2588a6e077d694afa6fb8631
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878854"
---
# <a name="create-a-virtual-network-for-azure-sql-database-managed-instance"></a>Utwórz sieć wirtualną dla Azure SQL Database wystąpienia zarządzanego

W tym artykule wyjaśniono, jak utworzyć prawidłową sieć wirtualną i podsieć, w której można wdrożyć Azure SQL Database wystąpienie zarządzane.

Azure SQL Database wystąpienie zarządzane musi być wdrożone w ramach [sieci wirtualnej](../virtual-network/virtual-networks-overview.md)platformy Azure. To wdrożenie umożliwia realizację następujących scenariuszy:

- Bezpieczny prywatny adres IP
- Nawiązywanie połączenia z wystąpieniem zarządzanym bezpośrednio z sieci lokalnej
- Łączenie wystąpienia zarządzanego z serwerem połączonym lub innym lokalnym magazynem danych
- Łączenie wystąpienia zarządzanego z zasobami platformy Azure  

> [!NOTE]
> Przed wdrożeniem pierwszego wystąpienia należy [określić rozmiar podsieci dla wystąpienia zarządzanego](sql-database-managed-instance-determine-size-vnet-subnet.md) . Nie można zmienić rozmiaru podsieci po umieszczeniu zasobów wewnątrz.
>
> Jeśli planujesz użycie istniejącej sieci wirtualnej, musisz zmodyfikować tę konfigurację sieci w celu uwzględnienia wystąpienia zarządzanego. Aby uzyskać więcej informacji, zobacz [modyfikowanie istniejącej sieci wirtualnej dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
>
> Po utworzeniu wystąpienia zarządzanego przeniesienie wystąpienia zarządzanego lub sieci wirtualnej do innej grupy zasobów lub subskrypcji nie jest obsługiwane.  Przeniesienie wystąpienia zarządzanego do innej podsieci również nie jest obsługiwane.
>

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Najprostszym sposobem tworzenia i konfigurowania sieci wirtualnej jest użycie szablonu wdrażania Azure Resource Manager.

1. Zaloguj się do witryny Azure Portal.

2. Wybierz przycisk **Wdróż na platformie Azure** :

   <a target="_blank" href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-sql-managed-instance-azure-environment%2Fazuredeploy.json" rel="noopener" data-linktype="external"> <img src="https://azuredeploy.net/deploybutton.png" data-linktype="external"> </a>

   Ten przycisk otwiera formularz, którego można użyć do skonfigurowania środowiska sieciowego, w którym można wdrożyć wystąpienie zarządzane.

   > [!Note]
   > Ten szablon Azure Resource Manager spowoduje wdrożenie sieci wirtualnej z dwiema podsieciami. Jedna podsieć o nazwie **ManagedInstances**jest zarezerwowana dla wystąpienia zarządzanego i ma wstępnie skonfigurowaną tabelę tras. Druga podsieć o nazwie **default**jest używana w przypadku innych zasobów, które powinny mieć dostęp do wystąpienia zarządzanego (na przykład Azure Virtual Machines).

3. Skonfiguruj środowisko sieciowe. W poniższej postaci można skonfigurować parametry środowiska sieciowego:

   ![Menedżer zasobów szablon służący do konfigurowania sieci platformy Azure](./media/sql-database-managed-instance-vnet-configuration/create-mi-network-arm.png)

   Można zmienić nazwy sieci wirtualnej i podsieci, a następnie dostosować zakresy adresów IP skojarzone z zasobami sieciowymi. Po wybraniu przycisku **Kup** ten formularz spowoduje utworzenie i skonfigurowanie środowiska. Jeśli nie są potrzebne dwie podsieci, można usunąć wartość domyślną.

## <a name="next-steps"></a>Następne kroki

- Aby zapoznać się z omówieniem, zobacz [co to jest wystąpienie zarządzane?](sql-database-managed-instance.md)
- Dowiedz się więcej o [architekturze łączności w wystąpieniu zarządzanym](sql-database-managed-instance-connectivity-architecture.md).
- Dowiedz się, jak [zmodyfikować istniejącą sieć wirtualną dla wystąpienia zarządzanego](sql-database-managed-instance-configure-vnet-subnet.md).
- Samouczek pokazujący sposób tworzenia sieci wirtualnej, tworzenia wystąpienia zarządzanego i przywracania bazy danych z kopii zapasowej bazy danych znajduje się w temacie [tworzenie Azure SQL Database wystąpienia zarządzanego](sql-database-managed-instance-get-started.md).
- W przypadku problemów z usługą DNS zobacz [Konfigurowanie niestandardowego serwera DNS](sql-database-managed-instance-custom-dns.md).
