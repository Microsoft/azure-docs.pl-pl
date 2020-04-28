---
title: Kreator zabezpieczeń ról usługi Azure AD w usłudze PIM — usługa Azure Active Directory | Dokumenty firmy Microsoft
description: W tym artykule opisano kreatora zabezpieczeń, którego można użyć do konwersji stałych uprzywilejowanych przypisań ról usługi Azure AD na kwalifikujące się przy użyciu usługi Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9f12b2b31da4c7fe67eef9674d96b517d4e2bfa
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867751"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Kreator zabezpieczeń ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi

Jeśli jesteś pierwszą osobą, która używa zarządzania tożsamościami uprzywilejowanymi (PIM) w organizacji usługi Azure Active Directory (Azure AD), zostanie wyświetlony kreator, aby rozpocząć. Kreator pomaga zrozumieć zagrożenia bezpieczeństwa tożsamości uprzywilejowanych i jak używać zarządzania tożsamościami uprzywilejowanymi w celu zmniejszenia tych zagrożeń. Nie trzeba wprowadzać żadnych zmian w istniejących przypisaniach ról w kreatorze, jeśli wolisz to zrobić później.

> [!Important]
> Kreator zabezpieczeń jest tymczasowo niedostępny. Dziękuję za cierpliwość.

## <a name="wizard-overview"></a>Omówienie kreatora

Zanim organizacja zacznie korzystać z zarządzania tożsamościami uprzywilejowanymi, wszystkie przypisania ról są trwałe: użytkownicy są zawsze w tych rolach, nawet jeśli obecnie nie potrzebują swoich uprawnień. W pierwszym kroku kreatora przedstawiono listę ról o wysokich uprzywilejowanich oraz liczbę użytkowników, którzy obecnie pełnią te role. Można przejść do określonej roli, aby dowiedzieć się więcej o użytkownikach, jeśli jeden lub więcej z nich nie jest zaznajomionych.

Drugi krok kreatora umożliwia zmianę przypisania ról administratora.  

> [!WARNING]
> Ważne jest, aby mieć co najmniej jednego administratora globalnego i więcej niż jednego administratora ról uprzywilejowanych z kontem służbowym (a nie kontem Microsoft). Jeśli istnieje tylko jeden administrator ról uprzywilejowanych, organizacja nie może zarządzać zarządzaniem tożsamościami uprzywilejowanymi, jeśli to konto zostanie usunięte.
> Ponadto należy zachować przypisania ról na stałe, jeśli użytkownik ma konto Microsoft (innymi słowy konto, którego używa do logowania się do usług firmy Microsoft, takich jak Skype i Outlook.com). Jeśli planujesz wymagać uwierzytelniania wieloskładnikowego do aktywacji dla tej roli, ten użytkownik zostanie zablokowany.

## <a name="run-the-wizard"></a>Uruchamianie kreatora

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **zarządzanie tożsamościami uprzywilejowanymi usługą Azure AD**.

1. Wybierz **role usługi Azure AD,** a następnie wybierz pozycję **Kreator**.

    ![Role usługi Azure AD — strona kreatora zawierająca 3 kroki do uruchomienia kreatora](./media/pim-security-wizard/wizard-start.png)

1. Wybierz **1 Odkryj role uprzywilejowane**.

1. Przejrzyj listę ról uprzywilejowanych, aby zobaczyć, którzy użytkownicy są stali lub uprawnieni.

    ![Odnajduj role uprzywilejowane — okienko roli przedstawiające stałych i kwalifikujących się członków](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Wybierz **przycisk Dalej,** aby wybrać użytkowników lub grupy, które chcesz zakwalifikować.

    ![Konwertuj członków na kwalifikującą się stronę z opcjami wyboru członków, których chcesz zakwalifikować do ról](./media/pim-security-wizard/convert-members-eligible.png)

1. Po wybraniu użytkowników lub grup wybierz pozycję **Dalej**.

    ![Przejrzyj stronę zmian przedstawiającą członków ze stałymi przypisaniami ról, które zostaną przekonwertowane](./media/pim-security-wizard/review-changes.png)

1. Wybierz **przycisk OK,** aby przekonwertować przypisania stałe na kwalifikujące się.

    Po zakończeniu konwersji zobaczysz powiadomienie.

    ![Powiadomienie o stanie konwersji](./media/pim-security-wizard/notification-completion.png)

Jeśli konieczne jest przekonwertowanie innych przypisania ról uprzywilejowanych na kwalifikujące się, można ponownie uruchomić kreatora. Jeśli chcesz użyć interfejsu zarządzania tożsamościami uprzywilejowanymi zamiast kreatora, zobacz [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w zarządzania tożsamościami uprzywilejowanymi](pim-how-to-add-role-to-user.md)
- [Udzielanie dostępu innym administratorom do zarządzania zarządzaniem tożsamościami uprzywilejowanymi](pim-how-to-give-access-to-pim.md)
