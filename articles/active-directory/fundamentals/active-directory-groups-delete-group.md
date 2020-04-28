---
title: Usuwanie grupy Azure Active Directory | Microsoft Docs
description: Instrukcje dotyczące usuwania grupy przy użyciu Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdba55e0655a13e65e403f5da73fcb69db5dbca5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "68561918"
---
# <a name="delete-a-group-using-azure-active-directory"></a>Usuwanie grupy przy użyciu Azure Active Directory
Grupę Azure Active Directory (Azure AD) można usunąć z dowolnej liczby powodów, ale zazwyczaj jest to spowodowane tym, że:

- Niepoprawnie Ustaw **Typ grupy** na niewłaściwą opcję.

- Wystąpił błąd podczas tworzenia nieprawidłowej lub zduplikowanej grupy. 

- Grupa nie jest już potrzebna.

## <a name="to-delete-a-group"></a>Aby usunąć grupę
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego dla katalogu.

2. Wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **grupy**.

3. Na stronie **grupy — wszystkie grupy** Wyszukaj i wybierz grupę, którą chcesz usunąć. W przypadku tych kroków będziemy używać **zasad zarządzania urządzeniami przenośnymi — wschód**.

    ![Grupy — Strona wszystkie grupy, wyróżniona nazwa grupy](media/active-directory-groups-delete-group/group-all-groups-screen.png)

4. Na stronie **Omówienie zasad zarządzania urządzeniami przenośnymi — wschód** , a następnie wybierz pozycję **Usuń**.

    Grupa zostanie usunięta z dzierżawy Azure Active Directory.

    ![Zasady zarządzania urządzeniami przenośnymi — Strona przeglądu wschodniego, wyróżniona opcja usuwania](media/active-directory-groups-delete-group/group-overview-blade.png)

## <a name="next-steps"></a>Następne kroki

- Jeśli usuniesz grupę przez pomyłkę, możesz ją utworzyć ponownie. Aby uzyskać więcej informacji, zobacz [jak utworzyć podstawową grupę i dodać członków](active-directory-groups-create-azure-portal.md).

- Jeśli usuniesz grupę pakietu Office 365 przez pomyłkę, może być możliwe jej przywrócenie. Aby uzyskać więcej informacji, zobacz [przywracanie usuniętej grupy Office 365](../users-groups-roles/groups-restore-deleted.md).
