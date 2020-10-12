---
title: Udzielanie dostępu do zarządzania usługą PIM Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak udzielić dostępu innym administratorom do zarządzania usługą Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/06/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95f8991d5ba9efb8e3223dd44a8d037acf2de849
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009566"
---
# <a name="delegate-access-to-privileged-identity-management"></a>Delegowanie dostępu do Privileged Identity Management

Aby delegować dostęp do Privileged Identity Management (PIM), Administrator globalny może przypisywać innych użytkowników do roli administrator ról uprzywilejowanych. Domyślnie administratorzy zabezpieczeń i czytelnicy zabezpieczeń mają dostęp tylko do odczytu do Privileged Identity Management. Aby udzielić dostępu do Privileged Identity Management, pierwszy użytkownik może przypisać inne osoby do roli **administrator ról uprzywilejowanych** . Rola Administrator ról uprzywilejowanych jest wymagana do zarządzania tylko rolami usługi Azure AD. Uprawnienia administratora roli uprzywilejowanej nie są wymagane do zarządzania ustawieniami zasobów platformy Azure.

> [!NOTE]
> Zarządzanie Privileged Identity Management wymaga platformy Azure Multi-Factor Authentication. Ponieważ konta Microsoft nie mogą zarejestrować się w usłudze Azure Multi-Factor Authentication, użytkownik, który zaloguje się za pomocą konto Microsoft, nie może uzyskać dostępu do Privileged Identity Management.

Upewnij się, że w roli administratora roli uprzywilejowanej są zawsze co najmniej dwóch użytkowników, na wypadek gdy jeden użytkownik jest zablokowany lub jego konto zostanie usunięte.

## <a name="delegate-access-to-manage-pim"></a>Delegowanie dostępu do zarządzania usługą PIM

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W usłudze Azure AD Otwórz **Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **role**.

    ![Privileged Identity Management ról usługi Azure AD — role](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Wybierz rolę **administrator ról uprzywilejowanych** , aby otworzyć stronę członkowie.

    ![Administrator ról uprzywilejowanych — członkowie](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Wybierz pozycję **Dodaj członka**  , aby otworzyć okienko **Dodawanie elementów zarządzanych** .

1. Wybierz pozycję **Wybierz członków** , aby otworzyć okienko **Wybierz członków** .

    ![Administrator ról uprzywilejowanych — Wybieranie członków](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Wybierz element członkowski, a następnie kliknij przycisk **Wybierz**.

1. Wybierz **przycisk OK** , aby członek mógł być uprawniony do roli **administratora roli uprzywilejowanej** .

    Po przypisaniu nowej roli do kogoś w Privileged Identity Management są one automatycznie konfigurowane jako **kwalifikujące** się do aktywowania roli.

1. Aby element członkowski był trwały, wybierz użytkownika z listy członków administratora roli uprzywilejowanej.

1. Wybierz pozycję **więcej** , a następnie **Ustaw trwałe** , aby przypisywać trwałe.

    ![Administrator ról uprzywilejowanych — Ustaw jako trwały](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Wyślij użytkownikowi link, aby [rozpocząć korzystanie z Privileged Identity Management](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Usuwanie dostępu w celu zarządzania usługą PIM

Przed usunięciem kogoś z roli administrator ról uprzywilejowanych zawsze upewnij się, że nadal są przypisane co najmniej dwóch użytkowników.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **role**.

1. Wybierz rolę **administrator ról uprzywilejowanych** , aby otworzyć stronę członkowie.

1. Zaznacz pole wyboru obok użytkownika, który chcesz usunąć, a następnie wybierz pozycję **Usuń członka**.

    ![Administrator ról uprzywilejowanych — usuwanie elementu członkowskiego](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Gdy zostanie wyświetlony monit o potwierdzenie usunięcia elementu członkowskiego z roli, wybierz pozycję **tak**.

## <a name="next-steps"></a>Następne kroki

- [Rozpoczęcie korzystania z usługi Privileged Identity Management](pim-getting-started.md)
