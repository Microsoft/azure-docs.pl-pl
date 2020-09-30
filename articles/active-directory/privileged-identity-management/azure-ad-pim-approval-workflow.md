---
title: Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w usłudze PIM — Azure AD | Microsoft Docs
description: Dowiedz się, jak zatwierdzać lub odrzucać żądania dla ról usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/07/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e0f74f98adcc8a4e8aabc96f7f35c9c55ae277f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91536362"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-privileged-identity-management"></a>Zatwierdzanie lub odrzucanie żądań dla ról usługi Azure AD w Privileged Identity Management

Za pomocą usługi Azure Active Directory (Azure AD) Privileged Identity Management (PIM) można skonfigurować role, aby wymagały zatwierdzenia dla aktywacji, i wybrać jednego lub wielu użytkowników lub grupy jako delegowane osoby zatwierdzające. Delegowane osoby zatwierdzające mają 24 godziny na zatwierdzanie żądań. Jeśli żądanie nie zostanie zatwierdzone w ciągu 24 godzin, wówczas uprawniony użytkownik musi ponownie przesłać nowe żądanie. Nie można skonfigurować okna czasu zatwierdzania 24-godzinnego.

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która pasuje do środowiska dla ról platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

    [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli usługi Azure AD oczekuje na zatwierdzenie. Te oczekujące żądania można wyświetlić w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **Zatwierdź żądania**.

    ![Zatwierdzanie żądań — strona przedstawiająca żądanie przejrzenia ról usługi Azure AD](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

    W sekcji **żądania aktywacji roli** zobaczysz listę żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Znajdź i wybierz żądanie, które chcesz zatwierdzić. Zostanie wyświetlona strona zatwierdzanie lub odmowa.

    ![Zrzut ekranu przedstawiający stronę "Zatwierdź żądania — role usługi Azure AD".](./media/azure-ad-pim-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Zatwierdź**. Otrzymasz powiadomienie o zatwierdzeniu na platformie Azure.

    ![Zatwierdź powiadomienie z zatwierdzeniem żądania](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png))

## <a name="deny-requests"></a>Odmów żądań

1. Znajdź i wybierz żądanie, które chcesz odmówić. Zostanie wyświetlona strona zatwierdzanie lub odmowa.

    ![Okienko zatwierdzanie żądań — zatwierdzanie lub odrzucanie z użyciem pola szczegóły i uzasadnienia](./media/pim-resource-roles-approval-workflow/resources-approve-pane.png)

1. W polu **uzasadnienie** wprowadź uzasadnienie biznesowe.

1. Wybierz pozycję **Odmów**. Zostanie wyświetlone powiadomienie z odmową.

## <a name="workflow-notifications"></a>Powiadomienia przepływu pracy

Oto kilka informacji o powiadomieniach dotyczących przepływu pracy:

- Osoby zatwierdzające są powiadamiane za pośrednictwem poczty e-mail, gdy żądanie dotyczące roli oczekuje na przegląd. Powiadomienia e-mail zawierają bezpośredni link do żądania, gdzie osoba zatwierdzająca może zatwierdzić lub odmówić.
- Żądania są rozwiązywane przez pierwszej osoby zatwierdzającej, która zatwierdza lub odmówi.
- Gdy osoba zatwierdzająca odpowie na żądanie, wszyscy osoby zatwierdzające są powiadamiani o akcji.
- Administratorzy globalni i Administratorzy ról uprzywilejowanych są powiadamiani, gdy zatwierdzony użytkownik zostanie uaktywniony w roli.

>[!NOTE]
>Administratorzy globalni lub administrator ról uprzywilejowanych, który uważa, że zatwierdzona osoba nie powinna być aktywna, może usunąć przypisanie aktywnej roli w Privileged Identity Management. Mimo że administratorzy nie są powiadamiani o oczekujących żądaniach, chyba że są osobami zatwierdzającymi, mogą wyświetlać i anulować wszystkie oczekujące żądania dla wszystkich użytkowników, wyświetlając oczekujące żądania w Privileged Identity Management.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="view-pending-requests"></a>Wyświetlanie oczekujących żądań

W przypadku delegowanej osoby zatwierdzającej otrzymasz powiadomienie e-mail, gdy żądanie roli usługi Azure AD oczekuje na zatwierdzenie. Te oczekujące żądania można wyświetlić w Privileged Identity Management.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Otwórz **Azure AD Privileged Identity Management**.

1. Kliknij pozycję **role usługi Azure AD**.

1. Kliknij pozycję **Zatwierdź żądania**.

    ![Role usługi Azure AD — zatwierdzanie żądań](./media/azure-ad-pim-approval-workflow/approve-requests.png)

    Zostanie wyświetlona lista żądań oczekujących na zatwierdzenie.

## <a name="approve-requests"></a>Zatwierdzanie żądań

1. Wybierz żądania, które chcesz zatwierdzić, a następnie kliknij przycisk **Zatwierdź** , aby otworzyć okienko Zatwierdź wybrane żądania.

    ![Zatwierdź listę żądań z wyróżnioną opcją zatwierdzania](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. W polu **Przyczyna zatwierdzenia** wpisz przyczynę.

    ![Zatwierdź okienko wybrane żądania z przyczyną zatwierdzenia](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Kliknij przycisk **Zatwierdź**.

    Symbol stanu zostanie zaktualizowany przy użyciu zatwierdzenia.

    ![Zatwierdź okienko wybrane żądania po kliknięciu przycisku Zatwierdź](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Odmów żądań

1. Wybierz żądania, które chcesz odmówić, a następnie kliknij przycisk **Odmów** , aby otworzyć okienko Odmów wybranych żądań.

    ![Zatwierdź listę żądań z wyróżnioną opcją Odmów](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. W polu **Przyczyna odrzucenia** wpisz przyczynę.

    ![Odmowa okienka wybrane żądania z powodu odrzucenia](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Wybierz pozycję **Odmów**.

    Symbol stanu zostanie zaktualizowany z odmową.

---

## <a name="next-steps"></a>Następne kroki

- [Powiadomienia e-mail w Privileged Identity Management](pim-email-notifications.md)
- [Zatwierdzanie lub odrzucanie żądań dotyczących ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-approval-workflow.md)
