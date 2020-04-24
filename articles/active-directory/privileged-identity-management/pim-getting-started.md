---
title: Zacznij korzystać z usługi PIM Azure Active Directory | Microsoft Docs
description: Dowiedz się, jak włączyć i rozpocząć korzystanie z Azure AD Privileged Identity Management (PIM) w Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46f6c77916c7be62247c69b12dff1982e5781aff
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112222"
---
# <a name="start-using-privileged-identity-management"></a>Rozpoczęcie korzystania z usługi Privileged Identity Management

W tym artykule opisano sposób włączania Privileged Identity Management (PIM) i Rozpoczynanie korzystania z niego.

Użyj Privileged Identity Management (PIM) do zarządzania, kontrolowania i monitorowania dostępu w organizacji Azure Active Directory (Azure AD). Dzięki programowi PIM możesz zapewnić dostęp do zasobów platformy Azure, zasobów usługi Azure AD i innych Usługi online, takich jak pakiet Office 365 lub Microsoft Intune, w razie potrzeby.

## <a name="prerequisites"></a>Wymagania wstępne

Aby korzystać z Privileged Identity Management, musisz mieć jedną z następujących licencji:

- Usługa Azure AD — warstwa Premium P2
- Enterprise Mobility + Security (EMS) E5

Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do używania Privileged Identity Management](subscription-requirements.md).

## <a name="prepare-pim-for-azure-ad-roles"></a>Przygotowywanie usługi PIM dla ról usługi Azure AD

Po włączeniu Privileged Identity Management dla katalogu możesz przygotować Privileged Identity Management do zarządzania rolami usługi Azure AD.

Poniżej przedstawiono zadania, które zalecamy, aby przygotować się do ról usługi Azure AD w pożądanej kolejności:

1. [Skonfiguruj ustawienia roli usługi Azure AD](pim-how-to-change-default-settings.md).
1. [Udziel kwalifikujących się przypisań](pim-how-to-add-role-to-user.md).
1. [Zezwalaj uprawnionym użytkownikom na aktywowanie swojej roli usługi Azure AD just-in-Time](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Przygotowanie usługi PIM dla ról platformy Azure

Po włączeniu Privileged Identity Management dla katalogu możesz przygotować Privileged Identity Management do zarządzania rolami platformy Azure na potrzeby dostępu do zasobów platformy Azure w ramach subskrypcji.

Poniżej przedstawiono zadania, które zalecamy, aby przygotować się do ról platformy Azure w pożądanej kolejności:

1. [Odnajdywanie zasobów platformy Azure](pim-resource-roles-discover-resources.md)
1. [Skonfiguruj ustawienia roli platformy Azure](pim-resource-roles-configure-role-settings.md).
1. [Udziel kwalifikujących się przypisań](pim-resource-roles-assign-roles.md).
1. [Zezwól uprawnionym użytkownikom na aktywowanie ich ról platformy Azure just-in-Time](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Przejdź do zadań

Po skonfigurowaniu Privileged Identity Management można dowiedzieć się, jak to zrobić.

![Okno nawigacji w Privileged Identity Management pokazujące zadania i opcje zarządzania](./media/pim-getting-started/pim-quickstart-tasks.png)

| Zadanie + zarządzanie | Opis |
| --- | --- |
| **Moje role**  | Wyświetla listę kwalifikujących się i aktywnych ról przypisanych do użytkownika. Możesz tu aktywować dowolne przypisane, kwalifikujące się role. |
| **Moje żądania** | Wyświetla oczekujące żądania aktywowania kwalifikujących się przypisań ról. |
| **Zatwierdzanie żądań** | Przedstawia listę żądań, które umożliwiają aktywowanie kwalifikujących się ról użytkownikom w katalogu wskazanym do zatwierdzenia. |
| **Przegląd dostępu** | Wyświetla listę aktywnych przeglądów dostępu, które są przypisane do ukończenia, niezależnie od tego, czy przeglądasz dostęp dla siebie, czy do innej osoby. |
| **Role usługi Azure AD** | Wyświetla pulpit nawigacyjny i ustawienia administratorów ról uprzywilejowanych w celu zarządzania przypisaniami ról usługi Azure AD. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |
| **Zasoby platformy Azure** | Wyświetla pulpit nawigacyjny i ustawienia administratorów ról uprzywilejowanych w celu zarządzania przypisaniami ról zasobów platformy Azure. Z tego pulpitu nawigacyjnego nie mogą korzystać osoby, które nie są administratorami ról uprzywilejowanych. Tacy użytkownicy mają dostęp do specjalnego pulpitu nawigacyjnego zatytułowanego Mój widok. Pulpit nawigacyjny Mój widok zawiera tylko informacje o użytkowniku, który uzyskał dostęp do pulpitu nawigacyjnego, a nie całej dzierżawie. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Dodawanie kafelka PIM do pulpitu nawigacyjnego

Aby ułatwić otwieranie Privileged Identity Management, Dodaj kafelek PIM do pulpitu nawigacyjnego Azure Portal.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **wszystkie usługi** i znajdź usługę **Azure AD Privileged Identity Management** .

    ![Azure AD Privileged Identity Management we wszystkich usługach](./media/pim-getting-started/pim-all-services-find.png)

1. Wybierz Privileged Identity Management Szybki Start.

1. Zaznacz pole **Przypnij blok do pulpitu nawigacyjnego** , aby przypiąć blok Privileged Identity Management szybkiego startu do pulpitu nawigacyjnego.

    ![Ikona pinezki do przypinania Privileged Identity Management bloku do pulpitu nawigacyjnego](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Na pulpicie nawigacyjnym platformy Azure zobaczysz kafelek podobny do tego:

    ![Kafelek szybkiego startu Privileged Identity Management na pulpicie nawigacyjnym](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Zarządzanie dostępem do zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-discover-resources.md)
