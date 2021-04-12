---
title: Konfigurowanie ustawień roli zasobów platformy Azure w programie PIM-Azure AD | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia roli zasobów platformy Azure w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 01/01/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40779e4517b610e52e41caacc46fc613b72023e5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567139"
---
# <a name="configure-azure-resource-role-settings-in-privileged-identity-management"></a>Skonfiguruj ustawienia roli zasobów platformy Azure w Privileged Identity Management

Podczas konfigurowania ustawień roli zasobów platformy Azure należy zdefiniować ustawienia domyślne, które są stosowane do przypisań ról zasobów platformy Azure w usłudze Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Poniższe procedury służą do konfigurowania przepływu pracy zatwierdzania i określania, kto może zatwierdzać lub odrzucać żądania.

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli zasobów platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika z rolą [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **zasoby platformy Azure**.

1. Wybierz zasób, którym chcesz zarządzać, na przykład subskrypcję lub grupę zarządzania.

    ![Strona zasobów platformy Azure z listą zasobów, które mogą być zarządzane](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Wybierz pozycję **Ustawienia roli**.

    ![Strona ustawień roli z listą ról zasobów platformy Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Strona szczegółów ustawienia roli wyświetlanie kilku ustawień przypisywania i aktywacji](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Wybierz pozycję **Edytuj** , aby otworzyć okienko **Ustawienia roli** . Na pierwszej karcie można zaktualizować konfigurację aktywacji roli w Privileged Identity Management.

    ![Strona edytowania ustawień roli z otwartą kartą aktywacji](./media/pim-resource-roles-configure-role-settings/role-settings-activation-tab.png)

1. Wybierz kartę **przypisanie** lub przycisk **Dalej: przypisanie** w dolnej części strony, aby otworzyć kartę ustawienia przypisania. Te ustawienia kontrolują przypisania ról wykonane w interfejsie Privileged Identity Management.

    ![Karta Przypisanie roli na stronie Ustawienia roli](./media/pim-resource-roles-configure-role-settings/role-settings-assignment-tab.png)

1. Skorzystaj z karty **powiadomień** lub przycisku **Dalej: Aktywacja** w dolnej części strony, aby przejść do karty Ustawienia powiadomień dla tej roli. Te ustawienia kontrolują wszystkie powiadomienia e-mail powiązane z tą rolą.

    ![Karta powiadomienia o rolach na stronie ustawień roli](./media/pim-resource-roles-configure-role-settings/role-settings-notification-tab.png)

    Na karcie **powiadomienia** na stronie ustawienia roli Privileged Identity Management umożliwia szczegółową kontrolę nad tym, kto otrzymuje powiadomienia i jakie otrzymywane powiadomienia.

    - **Wyłączanie poczty e-mail**<br>Niektóre wiadomości e-mail można wyłączyć, czyszcząc pole wyboru domyślny odbiorca i usuwając wszystkich dodatkowych adresatów.  

    - **Ogranicz wiadomości e-mail do określonych adresów e-mail**<br>Możesz wyłączyć wiadomości e-mail wysyłane do domyślnych adresatów, czyszcząc pole wyboru domyślny odbiorca. Następnie można dodać kolejne adresy e-mail jako dodatkowych adresatów. Jeśli chcesz dodać więcej niż jeden adres e-mail, rozdziel je przy użyciu średnika (;).

    - **Wyślij wiadomości e-mail do domyślnych odbiorców i dodatkowych adresatów**<br>Wiadomości e-mail można wysyłać zarówno do domyślnego odbiorcy, jak i do dodatkowego adresata, zaznaczając pole wyboru domyślny odbiorca i dodając adresy e-mail dla dodatkowych adresatów.

    - **Tylko krytyczne wiadomości e-mail**<br>Dla każdego typu wiadomości e-mail można zaznaczyć pole wyboru, aby otrzymywać tylko krytyczne wiadomości e-mail. Oznacza to, że Privileged Identity Management będą nadal wysyłać wiadomości e-mail do skonfigurowanych adresatów tylko wtedy, gdy wiadomość e-mail wymaga natychmiastowego działania. Na przykład wiadomości e-mail z monitem o rozszerzenie przypisania roli nie będą wyzwalane, gdy zostaną wyzwolone wiadomości e-mail wymagające od administratorów zatwierdzenia żądania rozszerzenia.

1. Aby zaktualizować ustawienia roli, wybierz przycisk **Aktualizuj** w dowolnym momencie.

## <a name="assignment-duration"></a>Czas trwania przypisywania

Można wybrać jedną z dwóch opcji czasu trwania przypisania dla każdego typu przypisania (kwalifikujące się i aktywne) podczas konfigurowania ustawień dla roli. Te opcje stają się domyślnym maksymalnym czasem trwania, gdy użytkownik jest przypisany do roli w Privileged Identity Management.

Można wybrać jedną **z następujących opcji** czasu trwania przypisania:

| | Opis |
| --- | --- |
| **Zezwalaj na trwałe przypisanie** | Administratorzy zasobów mogą przypisywać trwałe, kwalifikujące się przypisanie. |
| **Unieważnij przypisanie kwalifikujące się po** | Administratorzy zasobów mogą wymagać, aby wszystkie kwalifikujące się przypisania mieli określoną datę początkową i końcową. |

Można też wybrać jedną z następujących opcji **aktywnego** przydziału czasu trwania:

| | Opis |
| --- | --- |
| **Zezwalaj na trwałe aktywne przypisanie** | Administratorzy zasobów mogą przypisywać trwałe aktywne przydziały. |
| **Wygasa aktywne przypisanie po** | Administratorzy zasobów mogą wymagać, aby wszystkie aktywne przypisania miały określoną datę początkową i końcową. |

> [!NOTE]
> Wszystkie przypisania o określonej dacie końcowej mogą zostać odnowione przez administratorów zasobów. Ponadto użytkownicy mogą inicjować żądania samoobsługi w celu [przedłużenia lub odnowienia przypisań ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Privileged Identity Management zapewnia opcjonalne Wymuszanie usługi Azure AD Multi-Factor Authentication dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj Multi-Factor Authentication przy aktywnym przypisaniu

W niektórych przypadkach może być konieczne przypisanie użytkownika lub grupy do roli przez krótki czas trwania (na przykład jeden dzień). W takim przypadku przypisani użytkownicy nie muszą zażądać aktywacji. W tym scenariuszu Privileged Identity Management nie może wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik korzysta z przypisania roli, ponieważ jest już aktywne w roli od momentu, w którym jest przypisany.

Aby upewnić się, że administrator zasobów spełniający przypisanie, należy wymusić uwierzytelnianie wieloskładnikowe przy aktywnym przypisaniu, zaznaczając pole **wymagaj Multi-Factor Authentication w aktywnym przypisaniu** .

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj Multi-Factor Authentication podczas aktywacji

Możesz wymagać od użytkowników, którzy kwalifikują się do roli, aby udowodnić, kto używa usługi Azure AD Multi-Factor Authentication, zanim będzie można aktywować. Uwierzytelnianie wieloskładnikowe gwarantuje, że użytkownik jest odpowiedzialny za to, że są one z rozsądną pewnością. Wymuszanie tej opcji chroni zasoby o znaczeniu krytycznym w sytuacjach, gdy naruszone konto użytkownika mogło ulec naruszeniu.

Aby wymagać uwierzytelniania wieloskładnikowego przed aktywacją, zaznacz pole **wymagaj Multi-Factor Authentication przy aktywacji** .

Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie wieloskładnikowe i Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Suwak **Maksymalny czas trwania aktywacji** umożliwia ustawienie maksymalnego czasu (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do przemieścić od jednej do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Możesz wymagać od użytkowników wprowadzenia uzasadnienia biznesowego podczas aktywacji. Aby wymagać uzasadnienia, zaznacz pole wyboru **Wymagaj uzasadnienia przy aktywnym przypisaniu** lub wybierz opcję **Wymagaj uzasadnienia przy aktywacji** .

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia do aktywacji

Jeśli chcesz wymagać zatwierdzenia w celu aktywowania roli, wykonaj następujące czynności.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia do uaktywnienia** .

1. Wybierz pozycję **Wybierz osoby zatwierdzające** , aby otworzyć stronę **Wybierz członka lub grupę** .

    ![Wybierz okienko użytkownika lub grupy, aby wybrać osoby zatwierdzające](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wybierz co najmniej jednego użytkownika lub grupę, a następnie kliknij przycisk **Wybierz**. Możesz dodać dowolną kombinację użytkowników i grup. Musisz wybrać co najmniej jedną osobę zatwierdzającą. Brak domyślnych osób zatwierdzających.

    Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Aktualizuj** , aby zapisać zmiany.

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-assign-roles.md)
- [Konfigurowanie alertów zabezpieczeń dla ról zasobów platformy Azure w Privileged Identity Management](pim-resource-roles-configure-alerts.md)
