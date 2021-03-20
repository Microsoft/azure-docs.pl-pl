---
title: Przypisywanie zarządzanej tożsamości dostępu do zasobu przy użyciu Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące przypisywania tożsamości zarządzanej w jednym zasobie dostępu do innego zasobu przy użyciu Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6584754edf3ff7ae31c3b9ace72baf16459dbc44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93359998"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Przypisywanie zarządzanej tożsamości dostępu do zasobu za pomocą Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej można przyznać zarządzanej tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. W tym artykule pokazano, jak udostępnić maszynę wirtualną platformy Azure lub zarządzaną tożsamość zestawu skalowania maszyn wirtualnych do konta usługi Azure Storage przy użyciu Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md). **Pamiętaj, aby zapoznać się z [różnicą między przypisaną przez system i tożsamością zarządzaną przez użytkownika](overview.md#managed-identity-types)**.
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Korzystanie z usługi Azure RBAC do przypisywania zarządzanej tożsamości do innego zasobu

Po włączeniu tożsamości zarządzanej w zasobie platformy Azure, na przykład w przypadku [maszyny wirtualnej platformy Azure](qs-configure-portal-windows-vm.md) lub [zestawu skalowania maszyn wirtualnych platformy Azure](qs-configure-portal-windows-vmss.md):

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, w ramach której skonfigurowano tożsamość zarządzaną.

2. Przejdź do żądanego zasobu, w którym chcesz zmodyfikować kontrolę dostępu. W tym przykładzie przydajemy maszynom wirtualnym platformy Azure dostęp do konta magazynu, więc przejdźmy do konta magazynu.

3. Wybierz stronę **Kontrola dostępu (IAM)** zasobu, a następnie wybierz pozycję **+ Dodaj przypisanie roli**. Następnie określ **rolę**, **Przypisz dostęp do** i określ odpowiednią **subskrypcję**. W obszarze kryteria wyszukiwania powinien zostać wyświetlony zasób. Wybierz zasób, a następnie wybierz pozycję **Zapisz**. 

   ![Zrzut ekranu kontroli dostępu (IAM)](./media/msi-howto-assign-access-portal/assign-access-control-iam-blade-before.png)  
     
## <a name="next-steps"></a>Następne kroki

- [Tożsamość zarządzana dla zasobów platformy Azure — omówienie](overview.md)
- Aby włączyć zarządzaną tożsamość na maszynie wirtualnej platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](qs-configure-portal-windows-vm.md).
- Aby włączyć zarządzaną tożsamość w zestawie skalowania maszyn wirtualnych platformy Azure, zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu Azure Portal](qs-configure-portal-windows-vmss.md).


