---
title: Konfigurowanie tożsamości zarządzanych przy użyciu Azure Portal — Azure AD
description: Instrukcje krok po kroku dotyczące konfigurowania tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej platformy Azure przy użyciu Azure Portal.
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
ms.openlocfilehash: 78c973537e958b7199c137c81e72091f70b84ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97590991"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-vm-using-the-azure-portal"></a>Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Zarządzane tożsamości dla zasobów platformy Azure zapewniają usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, bez poświadczeń w kodzie. 

W tym artykule dowiesz się, jak włączyć i wyłączyć zarządzane tożsamości systemowe i przypisane przez użytkownika dla maszyny wirtualnej platformy Azure (VM) przy użyciu Azure Portal. 

## <a name="prerequisites"></a>Wymagania wstępne

- Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [sekcją przegląd](overview.md).
- Jeśli nie masz jeszcze konta platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed kontynuowaniem.

## <a name="system-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez system

W tej sekcji dowiesz się, jak włączyć i wyłączyć zarządzaną tożsamość przypisaną przez system dla maszyny wirtualnej przy użyciu Azure Portal.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system podczas tworzenia maszyny wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej podczas jej tworzenia, konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

- Na karcie **Zarządzanie** w sekcji **tożsamość** Przełącz opcję **tożsamość usługi zarządzanej** na wartość **włączone**.  

![Włącz tożsamość przypisaną przez system podczas tworzenia maszyny wirtualnej](./media/msi-qs-configure-portal-windows-vm/enable-system-assigned-identity-vm-creation.png)

Aby utworzyć maszynę wirtualną, zobacz następujące przewodniki szybki start: 

- [Tworzenie maszyny wirtualnej z systemem Windows za pomocą witryny Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine) 
- [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


### <a name="enable-system-assigned-managed-identity-on-an-existing-vm"></a>Włączanie tożsamości zarządzanej przypisanej przez system na istniejącej maszynie wirtualnej

Aby włączyć tożsamość zarządzaną przypisaną przez system na maszynie wirtualnej, która została pierwotnie zainicjowana bez niej, konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.

2. Przejdź do odpowiedniej maszyny wirtualnej i wybierz pozycję **tożsamość**.

3. W obszarze **przypisano system**, **stan**, wybierz opcję **włączone** , a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu przedstawiający stronę "tożsamość (wersja zapoznawcza)" ze stanem "on" przypisanym do systemu.](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade.png)  

### <a name="remove-system-assigned-managed-identity-from-a-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez system z maszyny wirtualnej

Aby usunąć tożsamość zarządzaną przypisaną przez system z maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) .  Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

Jeśli masz maszynę wirtualną, która nie wymaga już tożsamości zarządzanej przypisanej przez system:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną. 

2. Przejdź do odpowiedniej maszyny wirtualnej i wybierz pozycję **tożsamość**.

3. W obszarze **przypisano system**, **stan**, wybierz pozycję **wyłączone** , a następnie kliknij przycisk **Zapisz**:

   ![Zrzut ekranu strony konfiguracji](./media/msi-qs-configure-portal-windows-vm/create-windows-vm-portal-configuration-blade-disable.png)

## <a name="user-assigned-managed-identity"></a>Tożsamość zarządzana przypisana przez użytkownika

 W tej sekcji dowiesz się, jak dodać i usunąć tożsamość zarządzaną przez użytkownika z maszyny wirtualnej przy użyciu Azure Portal.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-a-vm"></a>Przypisywanie tożsamości przypisanej do użytkownika podczas tworzenia maszyny wirtualnej

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

Obecnie Azure Portal nie obsługuje przypisywania tożsamości zarządzanej przypisanej przez użytkownika podczas tworzenia maszyny wirtualnej. Zamiast tego zapoznaj się z jednym z poniższych artykułów szybkiego startu tworzenia maszyn wirtualnych, aby najpierw utworzyć maszynę wirtualną, a następnie przejdź do następnej sekcji, aby uzyskać szczegółowe informacje na temat przypisywania tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej:

- [Tworzenie maszyny wirtualnej z systemem Windows za pomocą witryny Azure Portal](../../virtual-machines/windows/quick-create-portal.md#create-virtual-machine)
- [Tworzenie maszyny wirtualnej z systemem Linux za pomocą witryny Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-vm"></a>Przypisywanie tożsamości zarządzanej przypisanej przez użytkownika do istniejącej maszyny wirtualnej

Do przypisywania tożsamości przypisanej przez użytkownika do maszyny wirtualnej wymagane są przypisania ról współautor i [operator tożsamości zarządzanej](../../role-based-access-control/built-in-roles.md#managed-identity-operator) [maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do odpowiedniej maszyny wirtualnej i kliknij pozycję **tożsamość**, **przypisane przez użytkownika** , a następnie **\+ Dodaj**.

   ![Zrzut ekranu pokazujący stronę "tożsamość" z wybraną pozycją "użytkownik przypisany" oraz wyróżniony przycisk "Dodaj".](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot1.png)

3. Kliknij tożsamość przypisaną przez użytkownika, którą chcesz dodać do maszyny wirtualnej, a następnie kliknij przycisk **Dodaj**.

    ![Dodawanie tożsamości zarządzanej przypisanej przez użytkownika do maszyny wirtualnej](./media/msi-qs-configure-portal-windows-vm/add-user-assigned-identity-vm-screenshot2.png)

### <a name="remove-a-user-assigned-managed-identity-from-a-vm"></a>Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej

Aby usunąć tożsamość przypisaną przez użytkownika z maszyny wirtualnej, Twoje konto wymaga przypisania roli [współautor maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) . Nie są wymagane żadne dodatkowe przypisania ról w katalogu usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta skojarzonego z subskrypcją platformy Azure, która zawiera maszynę wirtualną.
2. Przejdź do odpowiedniej maszyny wirtualnej i kliknij pozycję **tożsamość**, **przypisane przez użytkownika**, nazwę zarządzanej tożsamości przypisanej przez użytkownika, która ma zostać usunięta, a następnie kliknij przycisk **Usuń** (kliknij przycisk **tak** w okienku potwierdzenia).

   ![Usuwanie tożsamości zarządzanej przypisanej przez użytkownika z maszyny wirtualnej](./media/msi-qs-configure-portal-windows-vm/remove-user-assigned-identity-vm-screenshot.png)

## <a name="next-steps"></a>Następne kroki

- Korzystając z Azure Portal, udziel zarządzanej tożsamości maszyny wirtualnej platformy Azure [do innego zasobu platformy Azure](howto-assign-access-portal.md).