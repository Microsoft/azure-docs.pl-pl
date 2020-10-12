---
title: Aktywuj rolę niestandardową usługi Azure AD — Privileged Identity Management (PIM)
description: Jak aktywować rolę niestandardową usługi Azure AD na potrzeby przypisywania Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 398db465a6cc383f8869c549761afc0947e7ed03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421165"
---
# <a name="activate-an-azure-ad-custom-role-in-privileged-identity-management"></a>Aktywuj rolę niestandardową usługi Azure AD w Privileged Identity Management

Privileged Identity Management w usłudze Azure Active Directory (Azure AD) obsługuje teraz przypisanie "just-in-Time" do ról niestandardowych utworzonych na potrzeby zarządzania aplikacjami w środowisku administracyjnym zarządzania tożsamościami i dostępem. Aby uzyskać więcej informacji na temat tworzenia ról niestandardowych w celu delegowania zarządzania aplikacjami w usłudze Azure AD, zobacz [Niestandardowe role administratorów w Azure Active Directory (wersja zapoznawcza)](../users-groups-roles/roles-custom-overview.md).

> [!NOTE]
> Role niestandardowe usługi Azure AD nie są zintegrowane z wbudowanymi rolami katalogu w wersji zapoznawczej. Gdy funkcja jest ogólnie dostępna, zarządzanie rolami będzie odbywać się w interfejsie wbudowanym. Jeśli zobaczysz poniższy transparent, te role powinny być zarządzane [przy użyciu wbudowanej roli środowisko](pim-how-to-activate-role.md) i ten artykuł nie ma zastosowania:
>
> :::image type="content" source="media/pim-how-to-add-role-to-user/pim-new-version.png" alt-text="Wybierz pozycję Privileged Identity Management w usłudze Azure AD." lightbox="media/pim-how-to-add-role-to-user/pim-new-version.png":::

## <a name="activate-a-role"></a>Aktywuj rolę

Gdy musisz aktywować rolę niestandardową usługi Azure AD, Żądaj aktywacji, wybierając opcję nawigacji moje role w Privileged Identity Management.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Otwórz [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart)usługi Azure AD.

1. Wybierz **role niestandardowe usługi Azure AD** , aby wyświetlić listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD.

   ![Zobacz listę kwalifikujących się przypisań ról niestandardowych usługi Azure AD](./media/azure-ad-custom-roles-activate/view-preview-roles.png)

> [!Note] 
> Przed przypisaniem roli należy utworzyć/skonfigurować rolę. Aby uzyskać więcej informacji na temat konfigurowania ról niestandardowych usługi AAD, zobacz [Konfigurowanie ról niestandardowych w usłudze Azure AD w Privileged Identity Management](azure-ad-custom-roles-configure.md).

1. Na stronie **role niestandardowe usługi Azure AD (wersja zapoznawcza)** Znajdź wymagane przypisanie.
1. Wybierz pozycję **Aktywuj rolę** , aby otworzyć stronę **Aktywacja** .
1. Jeśli Twoja rola wymaga uwierzytelniania wieloskładnikowego, wybierz opcję **Weryfikuj swoją tożsamość przed kontynuowaniem**. Wymagane jest uwierzytelnienie tylko raz na sesję.
1. Wybierz pozycję **Weryfikuj moją tożsamość** i postępuj zgodnie z instrukcjami, aby zapewnić dodatkową weryfikację zabezpieczeń.
1. Aby określić niestandardowy zakres aplikacji, wybierz pozycję **zakres** , aby otworzyć okienko filtru. Należy zażądać dostępu do roli w wymaganym minimalnym zakresie. Jeśli przypisanie znajduje się w zakresie aplikacji, można aktywować tylko w tym zakresie.

   ![Przypisywanie zakresu zasobów usługi Azure AD do przypisania roli](./media/azure-ad-custom-roles-activate/assign-scope.png)

1. W razie konieczności Określ czas rozpoczęcia aktywacji niestandardowej. Gdy jest używany, członek roli jest aktywowany o określonej godzinie.
1. W polu **Przyczyna** wprowadź przyczynę żądania aktywacji. Są one wymagane lub nie są dostępne w ustawieniu roli.
1. Wybierz pozycję **Aktywuj**.

Jeśli rola nie wymaga zatwierdzenia, zostanie aktywowana zgodnie z ustawieniami i zostanie dodana do listy aktywnych ról. Jeśli chcesz użyć aktywowanej roli, Zacznij od kroków z sekcji [Przypisywanie roli niestandardowej usługi Azure AD w Privileged Identity Management](azure-ad-custom-roles-assign.md).

Jeśli rola wymaga zatwierdzenia do aktywacji, otrzymasz powiadomienie na platformie Azure z informacją o tym, że żądanie oczekuje na zatwierdzenie.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie roli niestandardowej usługi Azure AD](azure-ad-custom-roles-assign.md)
- [Usuń lub zaktualizuj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-update-remove.md)
- [Skonfiguruj niestandardowe przypisanie roli usługi Azure AD](azure-ad-custom-roles-configure.md)
- [Definicje ról w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
