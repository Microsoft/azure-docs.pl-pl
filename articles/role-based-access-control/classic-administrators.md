---
title: Administratorzy subskrypcji klasycznej platformy Azure | Microsoft Docs
description: Opisuje, jak dodać lub zmienić role administratora współpracującego z platformą Azure oraz administrator usługi oraz jak wyświetlić administratora konta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243486"
---
# <a name="azure-classic-subscription-administrators"></a>Klasyczni administratorzy subskrypcji platformy Azure

Firma Microsoft zaleca, aby zarządzać dostępem do zasobów platformy Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Jeśli jednak nadal korzystasz z klasycznego modelu wdrażania, musisz użyć klasycznej roli administratora subskrypcji: administrator usługi i administrator współpracujący. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager a wdrożenie klasyczne](../azure-resource-manager/management/deployment-models.md).

W tym artykule opisano, jak dodać lub zmienić role administratora współadministratora i usługi oraz jak wyświetlić administratora konta.

## <a name="add-a-co-administrator"></a>Dodawanie współadministratora

> [!TIP]
> Wystarczy dodać współadministratora, jeśli użytkownik musi zarządzać klasycznymi wdrożeniami platformy Azure przy użyciu [modułu Azure Service Management PowerShell](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Jeśli użytkownik używa tylko Azure Portal do zarządzania zasobami klasycznymi, nie trzeba dodawać administratora klasycznego dla użytkownika.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi lub współadministrator.

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

    Współadministratory mogą być przypisani tylko w zakresie subskrypcji.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **klasyczne Administratorzy** .

    ![Zrzut ekranu, który otwiera administratorów klasycznych](./media/classic-administrators/classic-administrators.png)

1. Kliknij przycisk **Dodaj** > **Dodaj współadministrator** , aby otworzyć okienko Dodaj współadministratorów.

    Jeśli opcja Dodaj administratora współpracującego jest wyłączona, nie masz uprawnień.

1. Wybierz użytkownika, którego chcesz dodać, a następnie kliknij przycisk **Dodaj**.

    ![Zrzut ekranu, który dodaje współadministratora](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Dodawanie użytkownika-gościa jako współadministratora

Aby dodać użytkownika-gościa jako współadministratora, wykonaj te same czynności co w poprzedniej sekcji [Dodawanie współadministratora](#add-a-co-administrator) . Użytkownik-Gość musi spełniać następujące kryteria:

- Użytkownik-Gość musi mieć obecność w Twoim katalogu. Oznacza to, że użytkownik został zaproszony do katalogu i zaakceptował zaproszenie.

Aby uzyskać więcej informacji, jak dodać użytkownika-gościa do katalogu, zobacz [dodawanie Azure Active Directory użytkowników współpracy B2B w Azure Portal](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Różnice dla użytkowników-Gości

Użytkownicy-Goście, którym przypisano rolę współadministrator, mogą zobaczyć pewne różnice w porównaniu z użytkownikami należącymi do roli współadministratora. Poniżej przedstawiono przykładowy scenariusz:

- Użytkownik A za pomocą konta usługi Azure AD (konto służbowe) to administrator usługi dla subskrypcji platformy Azure.
- Użytkownik B ma konto Microsoft.
- Użytkownik A przypisuje rolę współadministratora użytkownikowi B.
- Użytkownik B może wykonać prawie wszystko, ale nie może zarejestrować aplikacji ani wyszukać użytkowników w katalogu usługi Azure AD.

Należy się spodziewać, że użytkownik B może zarządzać wszystkimi elementami. Przyczyna tej różnicy polega na tym, że konto Microsoft jest dodawana do subskrypcji jako użytkownik będący gościem, a nie użytkownikiem będącym członkiem. Użytkownicy-Goście mają różne domyślne uprawnienia w usłudze Azure AD w porównaniu z użytkownikami należącymi do członków. Na przykład użytkownicy z członkami mogą odczytywać innych użytkowników w usłudze Azure AD, a użytkownicy-Goście nie mogą. Użytkownicy będący członkami mogą rejestrować nowe jednostki usługi w usłudze Azure AD, a użytkownicy-Goście nie mogą.

Jeśli użytkownik-Gość musi być w stanie wykonać te zadania, możliwe jest przypisanie określonych ról administratora usługi Azure AD, których potrzebuje użytkownik gościa. Na przykład w poprzednim scenariuszu można przypisać rolę [czytelnicy Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) do odczytu innych użytkowników i przypisać rolę [Deweloper aplikacji](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) , aby móc tworzyć jednostki usługi. Aby uzyskać więcej informacji na temat członków i użytkowników-Gości i ich uprawnień, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Aby uzyskać więcej informacji na temat udzielania dostępu użytkownikom-Gościom, zobacz [Zarządzanie dostępem do zasobów platformy Azure dla użytkowników zewnętrznych Gości przy użyciu RBAC](role-assignments-external-users.md).

Należy pamiętać, że [wbudowane role dla zasobów platformy Azure](../role-based-access-control/built-in-roles.md) są inne niż [role administratorów usługi Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). Wbudowane role nie udzielają dostępu do usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Omówienie różnych ról](../role-based-access-control/rbac-and-directory-admin-roles.md).

Aby uzyskać informacje, które porównują użytkowników i użytkowników-Gości, zobacz [co to są domyślne uprawnienia użytkownika w Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Usuwanie współadministratora

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator usługi lub współadministrator.

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Kontrola dostępu (IAM)**.

1. Kliknij kartę **klasyczne Administratorzy** .

1. Dodaj znacznik wyboru obok administratora współpracującego, który chcesz usunąć.

1. Kliknij pozycję **Usuń**.

1. W wyświetlonym oknie komunikatu kliknij przycisk **tak**.

    ![Zrzut ekranu, który usuwa współadministratora](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Zmienianie administratora usługi

Tylko administrator konta może zmienić administratora usługi dla subskrypcji. Domyślnie podczas rejestrowania się w celu uzyskania subskrypcji platformy Azure administrator usługi jest taki sam jak administrator konta. Użytkownik z rolą administratora konta nie ma dostępu do Azure Portal. Użytkownik z rolą administratora usługi ma pełny dostęp do Azure Portal. Jeśli administrator konta i administrator usługi są tego samego użytkownika, a administrator usługi zostanie zmieniony na innego użytkownika, administrator konta utraci dostęp do Azure Portal. Jednak administrator konta zawsze może użyć centrum kont, aby zmienić administratora usługi z powrotem na siebie.

Wykonaj następujące kroki, aby zmienić administratora usługi w **centrum konta**.

### <a name="account-center"></a>Centrum konta

1. Upewnij się, że scenariusz jest obsługiwany, sprawdzając [ograniczenia dotyczące zmiany administratora usługi](#limitations-for-changing-the-service-administrator).

1. Zaloguj się do [centrum konta](https://account.windowsazure.com/subscriptions) jako administrator konta.

1. Kliknij subskrypcję.

1. Po prawej stronie kliknij pozycję **Edytuj szczegóły subskrypcji**.

    ![Zrzut ekranu przedstawiający przycisk Edytuj subskrypcję w centrum konta](./media/classic-administrators/editsub.png)

1. W polu **administrator usługi** wprowadź adres E-mail nowego administratora usługi.

    ![Zrzut ekranu przedstawiający pole umożliwiające zmianę adresu e-mail administratora usługi](./media/classic-administrators/change-service-admin.png)

1. Kliknij znacznik wyboru, aby zapisać zmiany.

### <a name="limitations-for-changing-the-service-administrator"></a>Ograniczenia dotyczące zmiany administratora usługi

Może istnieć tylko jeden administrator usługi dla każdej subskrypcji platformy Azure. Zmiana administratora usługi będzie zachowywać się inaczej w zależności od tego, czy administrator konta jest konto Microsoft, czy też jest kontem usługi Azure AD (konto służbowe).

| Konto administratora konta | Czy administrator usługi może zmienić inny konto Microsoft? | Czy można zmienić administratora usługi na konto usługi Azure AD w tym samym katalogu? | Czy można zmienić administratora usługi na konto usługi Azure AD w innym katalogu? |
| --- | --- | --- | --- |
| Konto Microsoft | Tak | Nie | Nie |
| Konto usługi Azure AD | Tak | Tak | Nie |

Jeśli administrator konta jest kontem usługi Azure AD, możesz zmienić administratora usługi na konto usługi Azure AD w tym samym katalogu, ale nie w innym katalogu. Na przykład program abby@contoso.com może zmienić administratora usługi na bob@contoso.com, ale nie może zmienić administratora usługi na john@notcontoso.com , chyba john@notcontoso.com że ma obecność w katalogu contoso.com.

Aby uzyskać więcej informacji o kontach Microsoft i kontach usługi Azure AD, zobacz [co to jest Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Wyświetlanie administratora konta

Administrator konta jest użytkownikiem, który początkowo zarejestrował się w ramach subskrypcji platformy Azure i jest odpowiedzialny za właściciela rozliczania subskrypcji. Aby zmienić administratora konta subskrypcji, zobacz [przenoszenie własności subskrypcji platformy Azure na inne konto](../cost-management-billing/manage/billing-subscription-transfer.md).

Wykonaj następujące kroki, aby wyświetlić administratora konta.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz blok [Subskrypcje](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i wybierz subskrypcję.

1. Kliknij pozycję **Właściwości**.

    Administrator konta w subskrypcji zostanie wyświetlony w polu **administrator konta** .

    ![Zrzut ekranu przedstawiający administratora konta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Następne kroki

* [Understand the different roles in Azure (Omówienie ról na platformie Azure)](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](../role-based-access-control/role-assignments-portal.md)
* [Dodaj lub Zmień administratorów subskrypcji platformy Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
