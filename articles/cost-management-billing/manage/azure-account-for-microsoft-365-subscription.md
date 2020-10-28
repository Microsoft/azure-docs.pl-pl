---
title: Zarejestruj się na platformie Microsoft 365 przy użyciu konta platformy Azure
description: Dowiedz się, jak utworzyć subskrypcję platformy Microsoft 365 przy użyciu konta platformy Azure. Możesz także skojarzyć istniejące konta platform Azure i Microsoft 365 ze sobą.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: db9c8d18d975661edf4a120a2e82b073c884d2a9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369679"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>Rejestracja przy użyciu konta platformy Azure w celu uzyskania subskrypcji platformy Microsoft 365

Jeśli jesteś subskrybentem platformy Azure, możesz użyć swojego konta platformy Azure do zarejestrowania subskrypcji platformy Microsoft 365. Jeśli reprezentujesz organizację, która ma subskrypcję platformy Azure, możesz utworzyć subskrypcje platformy Microsoft 365 dla użytkowników w istniejącej usłudze Azure Active Directory (Azure AD). Zarejestruj się w usłudze Office 365 przy użyciu konta z uprawnieniami administratora globalnego lub administratora rozliczeń w dzierżawie usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [Sprawdzanie uprawnień konta w usłudze Azure AD](#RoleInAzureAD) i [Przypisywanie ról administratorów w usłudze Azure Active Directory](../../active-directory/roles/permissions-reference.md).

Jeśli masz już konto platformy Microsoft 365 i subskrypcję platformy Azure, możesz [skojarzyć dzierżawę platformy Microsoft 365 z subskrypcją platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Uzyskiwanie subskrypcji usługi Office 365 przy użyciu konta platformy Azure

1. Przejdź do [strony produktu Microsoft 365](https://www.microsoft.com/microsoft-365/business/all-business) i wybierz plan.
2. Kliknij pozycję **Zaloguj się** w prawym górnym rogu strony.

    ![Zrzut ekranu przedstawiający stronę wersji próbnej platformy Microsoft 365](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Zaloguj się przy użyciu poświadczeń konta platformy Azure. Jeśli tworzysz subskrypcję dla swojej organizacji, użyj konta platformy Azure, które ma uprawnienia roli katalogu Administrator globalny lub Administrator rozliczeń w dzierżawie usługi Azure Active Directory.

    ![Zrzut ekranu przedstawiający logowanie firmy Microsoft](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Kliknij pozycję **Wypróbuj teraz** .

    ![Zrzut ekranu z potwierdzeniem zamówienia platformy Microsoft 365.](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. Na stronie potwierdzenia zamówienia kliknij pozycję **Kontynuuj** .

    ![Zrzut ekranu przedstawiający potwierdzenie zamówienia platformy Microsoft 365](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Teraz wszystko jest już gotowe. Jeśli utworzono subskrypcję platformy Microsoft 365 dla organizacji, wykonaj następujące kroki, aby sprawdzić, czy użytkownicy usługi Azure AD mają dostęp do platformy Microsoft 365.

1. Otwórz Centrum administracyjne platformy Microsoft 365.
2. Rozwiń węzeł **UŻYTKOWNICY** , a następnie kliknij pozycję **Aktywni użytkownicy** .

    ![Zrzut ekranu przedstawiający użytkowników Centrum administracyjnego platformy Microsoft 365](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

Po zarejestrowaniu subskrypcja platformy Microsoft 365 jest dodawana do tego samego wystąpienia usługi Azure Active Directory, do którego należy subskrypcja platformy Azure. Aby uzyskać więcej informacji, zobacz [Więcej informacji o subskrypcjach platformy Azure i platformy Microsoft 365](microsoft-365-account-for-azure-subscription.md#more-about-subs) i [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Sprawdzanie uprawnień konta w usłudze Azure AD
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Kliknij pozycję **Wszystkie usługi** , a następnie wyszukaj usługę **Active Directory** .

    ![Zrzut ekranu przedstawiający usługę Active Directory w witrynie Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Kliknij pozycję **Użytkownicy i grupy** > **Wszyscy użytkownicy** .
4. Wybierz nazwę użytkownika.

    ![Zrzut ekranu przedstawiający użytkowników usługi Azure Active Directory](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Kliknij pozycję **Rola katalogu** .

    ![Zrzut ekranu przedstawiający rolę katalogu w witrynie Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  Aby utworzyć subskrypcję platformy Microsoft 365 dla użytkowników w istniejącej usłudze Azure Active Directory, wymagana jest rola **Administrator globalny** lub rola **Administrator z ograniczonymi uprawnieniami** > **Administrator rozliczeń** .

    ![Zrzut ekranu przedstawiający rolę katalogu Administrator rozliczeń w witrynie Azure Portal](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

- [Kojarzenie dzierżawy platformy Microsoft 365 z subskrypcją platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
