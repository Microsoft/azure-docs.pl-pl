---
title: 'Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal — RBAC'
description: W tym samouczku dowiesz się, jak udzielić użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal i kontroli dostępu opartej na rolach (Azure RBAC).
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/22/2019
ms.author: rolyon
ms.openlocfilehash: fcba9cad208c2ac170f91cc06a6db22e271f2a70
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100559327"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure Portal

[Kontrola dostępu oparta na rolach (Azure RBAC)](overview.md) umożliwia zarządzanie dostępem do zasobów platformy Azure. W tym samouczku udzielisz użytkownikowi praw dostępu do tworzenia maszyn wirtualnych i zarządzania nimi w grupie zasobów.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Udzielanie dostępu użytkownikowi w zakresie grupy zasobów
> * Usuwanie dostępu

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

1. Na liście nawigacji kliknij pozycję **Grupy zasobów**.

1. Kliknij pozycję **Dodaj**, aby otworzyć blok **Grupa zasobów**.

   ![Dodawanie nowej grupy zasobów](./media/quickstart-assign-role-user-portal/resource-group.png)

1. W polu **Nazwa grupy zasobów** wprowadź nazwę **rbac-resource-group**.

1. Wybierz subskrypcję i lokalizację.

1. Kliknij pozycję **Utwórz**, aby utworzyć grupę zasobów.

1. Kliknij pozycję **Odśwież**, aby odświeżyć listę grup zasobów.

   Nowa grupa zasobów zostanie wyświetlona na liście grup zasobów.

   ![Lista grup zasobów](./media/quickstart-assign-role-user-portal/resource-group-list.png)

## <a name="grant-access"></a>Udzielanie dostępu

W celu udzielenia dostępu w usłudze Azure RBAC należy przypisać rolę platformy Azure.

1. Na liście **Grupy zasobów** kliknij nową grupę zasobów **rbac-resource-group**.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **Przypisania ról**, aby wyświetlić bieżącą listę przypisań ról.

   ![Blok Kontrola dostępu (IAM) dla grupy zasobów](./media/quickstart-assign-role-user-portal/access-control.png)

1. Kliknij pozycję **Dodaj** > **Dodaj przypisanie roli**, aby otworzyć okienko Dodawanie przypisania roli.

   Jeśli nie masz uprawnień do przypisywania ról, opcja Dodaj przypisanie roli będzie wyłączona.

   ![Menu Dodaj przypisanie roli](./media/shared/add-role-assignment-menu.png)

    Zostanie otwarte okienko Dodawanie przypisania roli.

   ![Okienko Dodawanie przypisania roli](./media/quickstart-assign-role-user-portal/add-role-assignment.png)

1. Z listy rozwijanej **Rola** wybierz pozycję **Współautor·maszyny·wirtualnej**.

1. Z listy **Wybierz** wybierz siebie lub innego użytkownika.

1. Kliknij przycisk **Zapisz**, aby przypisać rolę.

   Po kilku chwilach użytkownik zostanie przypisany do roli Współautor maszyny wirtualnej w zakresie grupy zasobów rbac-resource-group.

   ![Przypisanie roli Współautor maszyny wirtualnej](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>Usuwanie dostępu

Aby usunąć dostęp za pomocą kontroli dostępu na podstawie ról platformy Azure, należy usunąć przypisanie roli.

1. Na liście przypisań ról dodaj znacznik wyboru obok użytkownika z rolą Współautor maszyny wirtualnej.

1. Kliknij przycisk **Usuń**.

   ![Komunikat dotyczący usuwania przypisania roli](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. W wyświetlonym komunikacie dotyczącym usuwania przypisania roli wybierz pozycję **Tak**.

## <a name="clean-up"></a>Czyszczenie

1. Na liście nawigacji kliknij pozycję **Grupy zasobów**.

1. Kliknij pozycję **rbac-resource-group**, aby otworzyć grupę zasobów.

1. Kliknij pozycję **Usuń grupę zasobów**, aby usunąć grupę zasobów.

   ![Usuwanie grupy zasobów](./media/quickstart-assign-role-user-portal/delete-resource-group.png)

1. W bloku **Czy na pewno chcesz usunąć** wpisz nazwę grupy zasobów: **rbac-resource-group**.

1. Kliknij pozycję **Usuń**, aby usunąć grupę zasobów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: udzielanie użytkownikom dostępu do zasobów platformy Azure przy użyciu Azure PowerShell](tutorial-role-assignments-user-powershell.md)
