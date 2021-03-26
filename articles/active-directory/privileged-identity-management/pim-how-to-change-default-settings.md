---
title: Konfigurowanie ustawień roli usługi Azure AD w programie PIM-Azure AD | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia roli usługi Azure AD w Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 02/28/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 676fdf264cb930069425a330c3da9cd753bf8e0a
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105567037"
---
# <a name="configure-azure-ad-role-settings-in-privileged-identity-management"></a>Konfigurowanie ustawień roli usługi Azure AD w Privileged Identity Management

Administrator ról uprzywilejowanych może dostosować Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD), w tym zmianę środowiska użytkownika, który uaktywnia kwalifikujące się przypisanie roli.

## <a name="determine-your-version-of-pim"></a>Określanie używanej wersji programu PIM

Od listopada 2019 część Privileged Identity Management ról usługi Azure AD jest aktualizowana do nowej wersji, która jest zgodna z środowiskami dla ról zasobów platformy Azure. Spowoduje to utworzenie dodatkowych funkcji, a także [zmian w istniejącym interfejsie API](azure-ad-roles-features.md#api-changes). Gdy nowa wersja jest wdrażana, procedury, które należy wykonać w tym artykule, zależą od aktualnie posiadanej wersji Privileged Identity Management. Wykonaj kroki opisane w tej sekcji, aby określić, która wersja Privileged Identity Management masz. Po uzyskaniu informacji o wersji Privileged Identity Management możesz wybrać procedury w tym artykule, które pasują do tej wersji.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika, który znajduje się w roli [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .
1. Otwórz **Azure AD Privileged Identity Management**. Jeśli masz baner w górnej części strony przegląd, postępuj zgodnie z instrukcjami wyświetlanymi na karcie **Nowa wersja** tego artykułu. W przeciwnym razie postępuj zgodnie z instrukcjami podanymi w **poprzedniej wersji** karty.

  [![Wybierz Privileged Identity Management usługi Azure AD >.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

Wykonaj kroki opisane w tym artykule, aby zatwierdzić lub odrzucić żądania dla ról usługi Azure AD.

# <a name="new-version"></a>[Nowa wersja](#tab/new)

## <a name="open-role-settings"></a>Otwórz ustawienia roli

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu użytkownika z rolą [administrator ról uprzywilejowanych](../roles/permissions-reference.md#privileged-role-administrator) .

1. Otwórz  &gt; Ustawienia roli **role usługi Azure AD** Azure AD Privileged Identity Management &gt; .

    ![Strona ustawień roli lista ról usługi Azure AD](./media/pim-how-to-change-default-settings/role-settings.png)

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Strona szczegółów ustawienia roli wyświetlanie kilku ustawień przypisywania i aktywacji](./media/pim-how-to-change-default-settings/role-settings-page.png)

1. Wybierz pozycję **Edytuj** , aby otworzyć stronę Ustawienia roli.

    ![Strona edytowania ustawień roli z opcjami aktualizowania ustawień przypisywania i aktywacji](./media/pim-how-to-change-default-settings/role-settings-edit.png)

    W okienku Ustawienia roli dla każdej roli istnieje kilka ustawień, które można skonfigurować.

## <a name="assignment-duration"></a>Czas trwania przypisywania

Można wybrać jedną z dwóch opcji czasu trwania przypisania dla każdego typu przypisania (kwalifikujące się i aktywne) podczas konfigurowania ustawień dla roli. Te opcje stają się domyślnym maksymalnym czasem trwania, gdy użytkownik jest przypisany do roli w Privileged Identity Management.

Można wybrać jedną **z następujących opcji** czasu trwania przypisania:

| | Opis |
| --- | --- |
| **Zezwalaj na trwałe przypisanie** | Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą przypisywać trwałe, kwalifikujące się przypisanie. |
| **Unieważnij przypisanie kwalifikujące się po** | Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą wymagać, aby wszystkie kwalifikujące się przypisania miały określoną datę początkową i końcową. |

Można też wybrać jedną z następujących opcji **aktywnego** przydziału czasu trwania:

| | Opis |
| --- | --- |
| **Zezwalaj na trwałe aktywne przypisanie** | Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą przypisywać trwałe aktywne przydziały. |
| **Wygasa aktywne przypisanie po** | Administratorzy globalni i Administratorzy ról uprzywilejowanych mogą wymagać, aby wszystkie aktywne przypisania miały określoną datę początkową i końcową. |

> [!NOTE]
> Wszystkie przypisania o określonej dacie końcowej mogą zostać odnowione przez administratorów globalnych i administratorów ról uprzywilejowanych. Ponadto użytkownicy mogą inicjować żądania samoobsługi w celu [przedłużenia lub odnowienia przypisań ról](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Wymagaj uwierzytelniania wieloskładnikowego

Privileged Identity Management zapewnia opcjonalne Wymuszanie usługi Azure AD Multi-Factor Authentication dla dwóch różnych scenariuszy.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Wymagaj Multi-Factor Authentication przy aktywnym przypisaniu

W niektórych przypadkach można chcieć przypisać użytkownika do roli przez krótki czas trwania (na przykład jeden dzień). W takim przypadku przypisani użytkownicy nie muszą zażądać aktywacji. W tym scenariuszu Privileged Identity Management nie może wymusić uwierzytelniania wieloskładnikowego, gdy użytkownik korzysta z przypisania roli, ponieważ jest już aktywne w roli od momentu, w którym jest przypisany.

Aby upewnić się, że administrator spełniający przypisanie, można wymusić uwierzytelnianie wieloskładnikowe przy aktywnym przypisaniu, zaznaczając pole **wymagaj Multi-Factor Authentication w aktywnym przypisaniu** .

### <a name="require-multi-factor-authentication-on-activation"></a>Wymagaj Multi-Factor Authentication podczas aktywacji

Możesz wymagać od użytkowników, którzy kwalifikują się do roli, aby udowodnić, kto używa usługi Azure AD Multi-Factor Authentication, zanim będzie można aktywować. Uwierzytelnianie wieloskładnikowe gwarantuje, że użytkownik jest odpowiedzialny za to, że są one z rozsądną pewnością. Wymuszanie tej opcji chroni zasoby o znaczeniu krytycznym w sytuacjach, gdy naruszone konto użytkownika mogło ulec naruszeniu.

Aby wymagać uwierzytelniania wieloskładnikowego przed aktywacją, zaznacz pole **wymagaj Multi-Factor Authentication przy aktywacji** na karcie przypisanie **Ustawienia Edytuj rolę**.

Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie wieloskładnikowe i Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Maksymalny czas trwania aktywacji

Suwak **Maksymalny czas trwania aktywacji** umożliwia ustawienie maksymalnego czasu (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do przemieścić od jednej do 24 godzin.

## <a name="require-justification"></a>Wymagaj uzasadnienia

Możesz wymagać od użytkowników wprowadzenia uzasadnienia biznesowego podczas aktywacji. Aby wymagać uzasadnienia, zaznacz pole wyboru **Wymagaj uzasadnienia przy aktywnym przypisaniu** lub wybierz opcję **Wymagaj uzasadnienia przy aktywacji** .

## <a name="require-approval-to-activate"></a>Wymagaj zatwierdzenia do aktywacji

W przypadku ustawienia wielu osób zatwierdzających zatwierdzenie zostanie zakończone zaraz po zatwierdzeniu lub odmowie. Nie można wymagać zatwierdzenia od co najmniej dwóch użytkowników. Aby wymagać zatwierdzenia w celu aktywowania roli, wykonaj następujące czynności.

1. Zaznacz pole wyboru **Wymagaj zatwierdzenia do uaktywnienia** .

1. Wybierz pozycję **Wybierz osoby zatwierdzające**.

    ![Wybierz okienko użytkownika lub grupy, aby wybrać osoby zatwierdzające](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Wybierz co najmniej jednego użytkownika, a następnie kliknij przycisk **Wybierz**. Musisz wybrać co najmniej jedną osobę zatwierdzającą. Brak domyślnych osób zatwierdzających.

    Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Aktualizuj** , aby zapisać zmiany.

# <a name="previous-version"></a>[Poprzednia wersja](#tab/previous)

## <a name="open-role-settings-previous-version"></a>Otwórz ustawienia roli (poprzednia wersja)

Wykonaj następujące kroki, aby otworzyć ustawienia roli usługi Azure AD.

1. Otwórz **Azure AD Privileged Identity Management**.

1. Wybierz pozycję **role usługi Azure AD**.

1. Wybierz pozycję **Ustawienia**.

    ![Role usługi Azure AD — ustawienia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Wybierz pozycję **role**.

1. Wybierz rolę, której ustawienia chcesz skonfigurować.

    ![Role usługi Azure AD — role ustawień](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Na stronie Ustawienia dla każdej roli istnieje kilka ustawień, które można skonfigurować. Te ustawienia mają wpływ tylko na użytkowników, którzy są **uprawnionymi** przypisaniami, a nie **trwałymi** .

## <a name="activations"></a>Aktywacji

Za pomocą suwaka **aktywacje** można ustawić maksymalny czas (w godzinach), przez który rola pozostaje aktywna przed wygaśnięciem. Ta wartość może należeć do przedziału od 1 do 72 godzin.

## <a name="notifications"></a>Powiadomienia

Użyj przełącznika **powiadomienia** , aby określić, czy administratorzy będą otrzymywać powiadomienia e-mail po aktywowaniu ról. To powiadomienie może być przydatne do wykrywania aktywacji nieautoryzowanych lub illegitimate.

Po wybraniu opcji **Włącz** powiadomienia są wysyłane do:

- Administrator ról uprzywilejowanych
- Administrator zabezpieczeń
- Administrator globalny

Aby uzyskać więcej informacji, zobacz [powiadomienia e-mail w Privileged Identity Management](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Bilet zdarzenia/żądania

Użyj przełącznika **bilet zdarzeń/żądania** , aby wymagać od uprawnionych administratorów dołączenia numeru biletu podczas aktywowania ich roli. To rozwiązanie może zwiększyć efektywność inspekcji dostępu do roli.

## <a name="multi-factor-authentication"></a>Uwierzytelnianie wieloskładnikowe

Użyj przełącznika **Multi-Factor Authentication** , aby określić, czy użytkownicy muszą weryfikować swoją tożsamość za pomocą usługi MFA, zanim będą mogli aktywować swoje role. Muszą oni tylko weryfikować swoją tożsamość tylko raz na sesję, a nie za każdym razem, gdy aktywują rolę. Po włączeniu usługi MFA należy wziąć pod uwagę dwie wskazówki:

- Użytkownicy, którzy mają konta Microsoft dla swoich adresów e-mail (zazwyczaj @outlook.com ale nie zawsze), nie mogą zarejestrować się w usłudze Azure AD Multi-Factor Authentication. Jeśli chcesz przypisać role do użytkowników z kontami Microsoft, należy udostępnić im trwałych administratorów lub wyłączyć uwierzytelnianie wieloskładnikowe dla tej roli.
- Nie można wyłączyć usługi Azure AD Multi-Factor Authentication dla ról o wysokim poziomie uprawnień dla usługi Azure AD i Microsoft 365. Ta funkcja zabezpieczeń pomaga chronić następujące role:  
  
  - Azure Information Protection administrator
  - Administrator rozliczeń
  - Administrator aplikacji w chmurze
  - Administrator zgodności
  - Administrator dostępu warunkowego
  - Administrator systemu Dynamics 365
  - Osoba zatwierdzająca dostęp do skrytki klienta
  - Autorzy katalogów
  - Administrator programu Exchange
  - Administrator globalny
  - Administrator usługi Intune
  - Power BI administrator
  - Administrator ról uprzywilejowanych
  - Administrator zabezpieczeń
  - Administrator programu SharePoint
  - Administrator programu Skype dla firm
  - Administrator użytkowników

Aby uzyskać więcej informacji, zobacz temat [uwierzytelnianie wieloskładnikowe i Privileged Identity Management](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Wymagaj zatwierdzenia

Jeśli chcesz delegować wymagane zatwierdzenie w celu aktywowania roli, wykonaj następujące czynności.

1. Ustaw przełącznik **Wymagaj zatwierdzenia** na **włączony**. Okienko rozwija się z opcjami wyboru osób zatwierdzających.

    ![Zrzut ekranu pokazujący, że opcja "Wymagaj zatwierdzenia" zostanie zaznaczona z opcją "Włącz".](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Jeśli nie określisz żadnych osób zatwierdzających, administrator ról uprzywilejowanych będzie domyślną osobą zatwierdzającą i jest wymagany do zatwierdzenia wszystkich żądań aktywacji dla tej roli.

1. Aby dodać osoby zatwierdzające, kliknij przycisk **Wybierz osoby zatwierdzające**.

    ![Role usługi Azure AD — ustawienia — wymaga zatwierdzenia](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wybierz co najmniej jedną osobę zatwierdzającą oprócz roli administrator ról uprzywilejowanych, a następnie kliknij przycisk **Wybierz**. Zalecamy dodanie co najmniej dwóch osób zatwierdzających. Nawet jeśli dodasz Cię jako osobę zatwierdzającą, nie możesz samodzielnie zatwierdzać aktywacji roli. Wybrane opcje zostaną wyświetlone na liście wybranych osób zatwierdzających.

1. Po określeniu wszystkich ustawień roli wybierz pozycję **Zapisz** , aby zapisać zmiany.

---

## <a name="next-steps"></a>Następne kroki

- [Przypisywanie ról usługi Azure AD w Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Konfigurowanie alertów zabezpieczeń dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-configure-security-alerts.md)
