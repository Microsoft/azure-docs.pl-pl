---
title: Szybki Start — sprawdzanie dostępu użytkownika do zasobów platformy Azure — RBAC
description: W tym przewodniku szybki start dowiesz się, jak sprawdzić dostęp dla siebie lub innego użytkownika do zasobów platformy Azure przy użyciu Azure Portal i kontroli dostępu opartej na rolach (RBAC) na platformie Azure.
services: role-based-access-control
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: quickstart
ms.workload: identity
ms.date: 12/09/2020
ms.author: rolyon
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7cf4020ad38224b25ea8bb7dc7f0fdea7dd6f3b1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034007"
---
# <a name="quickstart-check-access-for-a-user-to-azure-resources"></a>Szybki Start: sprawdzanie dostępu dla użytkownika do zasobów platformy Azure

Czasami musisz sprawdzić dostęp użytkownika do zestawu zasobów platformy Azure. Dostęp można sprawdzić, wyświetlając listę ich przypisań. Szybkim sposobem sprawdzenia dostępu dla jednego użytkownika jest użycie funkcji **Sprawdź dostęp** na stronie **kontroli dostępu (IAM)** .

## <a name="step-1-open-the-azure-resources"></a>Krok 1. Otwieranie zasobów platformy Azure

Aby sprawdzić dostęp dla użytkownika, musisz najpierw otworzyć zasoby platformy Azure, dla których chcesz sprawdzić dostęp. Zasoby platformy Azure są zorganizowane w poziomy, które są zwykle nazywane *zakresem*. Na platformie Azure można określić zakres na czterech poziomach od szerokiego do wąskiego: grupy zarządzania, subskrypcji, grupy zasobów i zasobu.

![Poziomy zakresu dla usługi Azure RBAC](../../includes/role-based-access-control/media/scope-levels.png)

Wykonaj następujące kroki, aby otworzyć zestaw zasobów platformy Azure, dla których chcesz sprawdzić dostęp.

1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

1. Otwórz zestaw zasobów platformy Azure, takich jak **grupy zarządzania**, **subskrypcje**, **grupy zasobów** lub określony zasób.

1. Kliknij konkretny zasób w tym zakresie.

    Poniżej przedstawiono przykładową grupę zasobów.

    ![Przegląd grupy zasobów](./media/check-access/rg-overview.png)

## <a name="step-2-check-access-for-a-user"></a>Krok 2. sprawdzenie dostępu dla użytkownika

Wykonaj następujące kroki, aby sprawdzić dostęp dla pojedynczego użytkownika, grupy, nazwy głównej usługi lub tożsamości zarządzanej do wcześniej wybranych zasobów platformy Azure.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

    Poniżej przedstawiono przykład strony kontroli dostępu (IAM) dla grupy zasobów.

    ![Kontrola dostępu do grupy zasobów — sprawdzanie dostępu do karty](./media/check-access/rg-access-control.png)

1. Na karcie **sprawdzanie dostępu** na liście **Znajdź** wybierz użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną, dla której chcesz sprawdzić dostęp.

1. W polu wyszukiwania wprowadź ciąg, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail lub identyfikatory obiektów.

    ![Sprawdzanie dostępu — lista wyboru](./media/shared/rg-check-access-select.png)

1. Kliknij podmiot zabezpieczeń, aby otworzyć okienko **przypisania**.

    W tym okienku można zobaczyć dostęp do wybranego podmiotu zabezpieczeń w tym zakresie i dziedziczyć ten zakres. Przypisania w zakresach podrzędnych nie są wyświetlane. Zobaczysz następujące przypisania:

    - Przypisań ról dodano do usługi Azure RBAC.
    - Odmowa przypisań dodanych przy użyciu planów platformy Azure lub aplikacji zarządzanych przez platformę Azure.
    - Klasyczny administrator usługi lub Co-Administrator przypisań klasycznych wdrożeń. 

    ![Okienko roli i Odmów przypisań dla użytkownika](./media/shared/rg-check-access-assignments-user.png)

## <a name="step-3-check-your-access"></a>Krok 3. sprawdzanie dostępu

Wykonaj następujące kroki, aby sprawdzić dostęp do wcześniej wybranych zasobów platformy Azure.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Na karcie **sprawdzanie dostępu** kliknij przycisk **Wyświetl mój dostęp** .

    Zostanie wyświetlone okienko przypisania, które wyświetla listę dostępu do tego zakresu i dziedziczy do tego zakresu. Przypisania w zakresach podrzędnych nie są wyświetlane.

    ![Okienko roli i Odmów przypisań](./media/check-access/rg-check-access-assignments.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyświetlanie listy przypisań ról platformy Azure przy użyciu Azure Portal](role-assignments-list-portal.md)
