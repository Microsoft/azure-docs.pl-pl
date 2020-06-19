---
title: Rejestracja na platformie Azure przy użyciu konta usługi Office 365
description: Dowiedz się, jak utworzyć subskrypcję platformy Azure przy użyciu konta usługi Office 365
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b42a5b83f5442755614a3cb2ae81cffda09b4b3b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84696766"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Rejestracja przy użyciu konta usługi Office 365 w celu uzyskania subskrypcji platformy Azure
Jeśli masz subskrypcję usługi Office 365, możesz utworzyć subskrypcję platformy Azure za pomocą swojego konta usługi Office 365. Zaloguj się do witryny [Azure Portal](https://portal.azure.com/) przy użyciu nazwy użytkownika i hasła do konta usługi Office 365. Jeśli chcesz skonfigurować maszyny wirtualne lub użyć innych usług platformy Azure, musisz zarejestrować się w celu uzyskania subskrypcji platformy Azure. Subskrypcję platformy Azure można udostępniać innym osobom i [korzystać z kontroli dostępu opartej na rolach w celu zarządzania dostępem do subskrypcji i zasobów platformy Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Jeśli masz już konto usługi Office 365 i subskrypcję platformy Azure, zobacz [Kojarzenie dzierżawy usługi Office 365 z subskrypcją platformy Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Uzyskiwanie subskrypcji platformy Azure przy użyciu konta usługi Office 365

Oszczędzaj czas i unikaj tworzenia nadmiernej liczby kont, rejestrując się na platformie Azure przy użyciu nazwy użytkownika i hasła do konta usługi Office 365.

1. Zarejestruj się w witrynie [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Zaloguj się przy użyciu swojej nazwy użytkownika i hasła do konta usługi Office 365. Używane konto nie musi mieć uprawnień administratora. Jeśli masz więcej niż jedno konto usługi Office 365, upewnij się, że używasz poświadczeń dla konta usługi Office 365, które chcesz skojarzyć z subskrypcją platformy Azure.

   ![Zrzut ekranu przedstawiający stronę logowania.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Wprowadź wymagane informacje i doprowadź do końca proces rejestracji. Niektóre informacje mogą nie być wymagane, jeśli masz już konto usługi Office 365.

    ![Zrzut ekranu pokazujący formularz rejestracji.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Jeśli musisz dodać inne osoby z organizacji do subskrypcji platformy Azure, zobacz [Wprowadzenie do zarządzania dostępem w witrynie Azure Portal](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Więcej informacji o subskrypcjach platformy Azure i usługi Office 365</a>
Zarządzanie użytkownikami oraz subskrypcjami usługi Office 365 i platformy Azure odbywa się za pomocą usługi Azure AD. Katalog platformy Azure jest podobny do kontenera, w którym można grupować użytkowników i subskrypcje. Aby korzystać z tych samych kont użytkowników w przypadku subskrypcji platformy Azure i usługi Office 365, należy się upewnić, że subskrypcje platformy Azure są tworzone w tym samym katalogu co subskrypcje usługi Office 365. Należy pamiętać o następujących kwestiach:

* Subskrypcja jest tworzona w katalogu
* Użytkownicy należą do katalogów
* Subskrypcja jest umieszczana w katalogu użytkownika, który ją tworzy. Dzięki temu subskrypcja usługi Office 365 jest powiązana z tym samym kontem co subskrypcja platformy Azure.
* Subskrypcje platformy Azure są własnością poszczególnych użytkowników w katalogu
* Subskrypcje usługi Office 365 są własnością samego katalogu. Użytkownicy z odpowiednimi uprawnieniami w katalogu mogą zarządzać tymi subskrypcjami.

![Zrzut ekranu przedstawiający relację katalogu, użytkowników i subskrypcji.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Aby uzyskać więcej informacji, zobacz [Jak subskrypcje platformy Azure są kojarzone z usługą Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).
