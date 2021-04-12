---
title: Konfigurowanie zarządzanych tożsamości w zestawie skalowania maszyn wirtualnych — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych przy użyciu Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73c2f4167d6ce5465a879f6b2ef7e96d91065c4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590924"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-vmss-using-the-azure-portal"></a>Skonfiguruj zarządzane tożsamości dla zasobów platformy Azure w zestawie skalowania maszyn wirtualnych (VMSS) przy użyciu Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule, korzystając z programu PowerShell, dowiesz się, jak wykonywać następujące tożsamości zarządzane dla operacji zasobów platformy Azure na zestawie skalowania maszyn wirtualnych:

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.
- Aby wykonać operacje zarządzania w tym artykule, Twoje konto wymaga następujących przypisań ról platformy Azure:

    > [!NOTE]
    > Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

    - [Współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) w celu włączenia i usunięcia tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

Ta sekcja zawiera informacje na temat włączania i wyłączania tożsamości zarządzanej przypisanej przez system przy użyciu Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zestawu skalowania maszyn wirtualnych

Obecnie Azure Portal nie obsługuje włączania tożsamości zarządzanej przypisanej przez system podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego zapoznaj się z poniższym artykułem szybki start tworzenia zestawu skalowania maszyn wirtualnych, aby najpierw utworzyć zestaw skalowania maszyn wirtualnych, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat włączania tożsamości zarządzanej przypisanej do systemu w zestawie skalowania maszyn wirtualnych:

- [Utwórz zestaw skalowania maszyn wirtualnych w Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)  

### <a name="enable-system-assigned-managed-identity-on-an-existing-virtual-machine-scale-set"></a>Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zestawie skalowania maszyn wirtualnych

Aby włączyć tożsamość zarządzaną przypisaną przez system na zestawie skalowania maszyn wirtualnych, który został pierwotnie zainicjowany bez:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.

2. Przejdź do żądanego zestawu skalowania maszyn wirtualnych.

3. W obszarze **przypisano system**, **stan**, wybierz opcję **włączone** , a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu przedstawia stronę "tożsamość (wersja zapoznawcza)" z wybraną pozycją "przypisany system", stan "włączone" i "Zapisz".](./media/msi-qs-configure-portal-windows-vmss/create-windows-vmss-portal-configuration-blade.png) 

### <a name="remove-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez system z zestawu skalowania maszyn wirtualnych

Jeśli masz zestaw skalowania maszyn wirtualnych, który nie potrzebuje już tożsamości zarządzanej przypisanej do systemu:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych. Upewnij się również, że Twoje konto należy do roli zapewniającej uprawnienia do zapisu w zestawie skalowania maszyn wirtualnych.

2. Przejdź do żądanego zestawu skalowania maszyn wirtualnych.

3. W obszarze **przypisano system**, **stan**, wybierz pozycję **wyłączone** , a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vmss/disable-windows-vmss-portal-configuration-blade.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z zestawu skalowania maszyn wirtualnych przy użyciu Azure Portal.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych

Obecnie Azure Portal nie obsługuje przypisywania tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia zestawu skalowania maszyn wirtualnych. Zamiast tego zapoznaj się z poniższym artykułem szybki start tworzenia zestawu skalowania maszyn wirtualnych, aby najpierw utworzyć zestaw skalowania maszyn wirtualnych, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania do niej tożsamości zarządzanej przypisanej przez użytkownika:

- [Utwórz zestaw skalowania maszyn wirtualnych w Azure Portal](../../virtual-machine-scale-sets/quick-create-portal.md)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-virtual-machine-scale-set"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącego zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera zestaw skalowania maszyn wirtualnych.
2. Przejdź do żądanego zestawu skalowania maszyn wirtualnych, a następnie kliknij pozycję **tożsamość**, **przypisane przez użytkownika** , a następnie **\+ Dodaj**.

   ![Zrzut ekranu pokazujący stronę "tożsamość" z wybraną pozycją "użytkownik przypisany" oraz wyróżniony przycisk "Dodaj".](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vmss-screenshot1.png)

3. Kliknij tożsamość przypisaną przez użytkownika, którą chcesz dodać do zestawu skalowania maszyn wirtualnych, a następnie kliknij przycisk **Dodaj**.
   
   ![Dodawanie tożsamości przypisanej do użytkownika do VMSS](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z zestawu skalowania maszyn wirtualnych

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do żądanego zestawu skalowania maszyn wirtualnych, a następnie kliknij pozycję **tożsamość**, **przypisana** do użytkownika, nazwę zarządzanej tożsamości przypisanej przez użytkownika, która ma zostać usunięta, a następnie kliknij przycisk **Usuń** (kliknij przycisk **tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości przypisanej przez użytkownika z VMSS](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vmss-screenshot.png)


## <a name="next-steps"></a>Następne kroki

- Korzystając z Azure Portal, nadaj zestawowi skalowania maszyn wirtualnych platformy Azure [dostęp do tożsamości zarządzanej do innego zasobu platformy Azure](howto-assign-access-portal.md).
