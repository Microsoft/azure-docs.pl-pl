---
title: Korzystanie z mojego personelu do delegowania zarządzania użytkownikami (wersja zapoznawcza) — Azure AD | Microsoft Docs
description: Delegowanie zarządzania użytkownikami przy użyciu funkcji mój personel i jednostki administracyjne
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 05/08/2020
ms.author: curtand
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 927d1f6dff9a24fb8427bb9c353e6cc61ac6a1f0
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376308"
---
# <a name="manage-your-users-with-my-staff-preview"></a>Zarządzanie użytkownikami za pomocą mojego personelu (wersja zapoznawcza)

Moi pracownicy umożliwiają delegowanie do zespołu ds. usług, takich jak kierownik sklepu lub potencjalny klient, uprawnienia do zapewnienia, że ich członkowie będą mieli dostęp do swoich kont usługi Azure AD. Zamiast polegać na centralnej pomocy technicznej, organizacje mogą delegować typowe zadania, takie jak resetowanie haseł lub zmienianie numerów telefonów z menedżerem zespołu. W przypadku mojego personelu użytkownik, który nie może uzyskać dostępu do swojego konta, może odzyskać dostęp tylko w kilku kliknięciach, bez pomocy technicznej lub personelu działu IT.

Przed skonfigurowaniem personelu dla organizacji zalecamy zapoznanie się z tą dokumentacją oraz [dokumentację dotyczącą użytkowników](../user-help/my-staff-team-manager.md) , aby poznać funkcje i wpływ tej funkcji na użytkowników. Można wykorzystać dokumentację użytkownika do uczenia i przygotowania użytkowników do nowego środowiska i zapewnienia pomyślnego wdrożenia.

Uwierzytelnianie oparte na programie SMS dla użytkowników jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) zapoznawczych Microsoft Azure

## <a name="how-my-staff-works"></a>Jak działa mój personel

Mój personel jest oparty na jednostkach administracyjnych (Australia), które są kontenerami zasobów, których można użyć do ograniczenia zakresu kontroli administracyjnej przypisania roli. W obszarze mój personel należy określić podzbiór użytkowników organizacji, takich jak sklep lub dział. Następnie na przykład menedżer zespołu może zostać przypisany do roli, której zakres jest jeden lub więcej. W poniższym przykładzie użytkownikowi została udzielona rola administracyjna uwierzytelniania, a trzecia jest zakresem roli. Aby uzyskać więcej informacji o jednostkach administracyjnych, zobacz [Zarządzanie jednostkami administracyjnymi w Azure Active Directory](administrative-units.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.

  * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z Twoją subskrypcją.

  * W razie konieczności [Utwórz dzierżawę Azure Active Directory](../fundamentals/sign-up-organization.md) lub [skojarz subskrypcję platformy Azure z Twoim kontem](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD, aby włączyć uwierzytelnianie oparte na programie SMS.
* Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi być licencjonowany, nawet jeśli nie używa tego programu. Każdy włączony użytkownik musi mieć jedną z następujących licencji usługi Azure AD lub Microsoft 365:

  * [Azure AD — wersja Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 lub F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 lub E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) lub [Microsoft 365 (M365) E3 lub E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Jak włączyć personel

Po skonfigurowaniu określania zespołu, możesz zastosować ten zakres do użytkowników, którzy uzyskują dostęp do mojego personelu. Tylko użytkownicy, którym przypisano rolę administracyjną, mogą uzyskać dostęp do mojego personelu. Aby włączyć personel, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal jako administrator użytkownika.
2. Przejdź do **Azure Active Directory**  >  **Ustawienia użytkownika**wersje  >  **User feature previews**  >  **zapoznawcze funkcji użytkownika Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.
3. W obszarze **Administratorzy mogą uzyskać dostęp do mojego personelu**, możesz wybrać opcję Włącz dla wszystkich użytkowników, wybranych użytkowników lub bez dostępu użytkownika.

> [!Note]
> Tylko użytkownicy, którym przypisano rolę administratora, mogą uzyskać dostęp do mojego personelu. Jeśli włączysz personel dla użytkownika, który nie ma przypisanej roli administratora, nie będzie on mógł uzyskać dostępu do mojego personelu.

## <a name="conditional-access"></a>Dostęp warunkowy

Portal mojego personelu można chronić za pomocą zasad dostępu warunkowego usługi Azure AD. Użyj jej do zadań, takich jak wymaganie uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do mojego personelu.

Zdecydowanie zalecamy ochronę mojego personelu przy użyciu [zasad dostępu warunkowego usługi Azure AD](../conditional-access/index.yml). Aby zastosować zasady dostępu warunkowego do mojego personelu, musisz ręcznie utworzyć główną nazwę usługi Personaler przy użyciu programu PowerShell.

### <a name="apply-a-conditional-access-policy-to-my-staff"></a>Zastosuj zasady dostępu warunkowego do mojego personelu

1. Zainstaluj [polecenia cmdlet programu PowerShell w programie Microsoft Graph beta](https://github.com/microsoftgraph/msgraph-sdk-powershell/blob/dev/samples/0-InstallModule.ps1).
1. Uruchom następujące polecenia:

   ```powershell
   Connect-Graph -Scopes "Directory.AccessAsUser.All"
   New-MgServicePrincipal -DisplayName "My Staff" -AppId "ba9ff945-a723-4ab5-a977-bd8c9044fe61"
   ```
1. Utwórz zasady dostępu warunkowego, które mają zastosowanie do aplikacji w chmurze my-personel.

    ![Tworzenie zasad dostępu warunkowego dla aplikacji My Personal](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Korzystanie z mojego personelu

Gdy użytkownik przechodzi do mojego pracownika, są wyświetlane nazwy [jednostek administracyjnych](administrative-units.md) , do których mają uprawnienia administracyjne. W [dokumentacji użytkownika mojego personelu](../user-help/my-staff-team-manager.md)do odwoływania się do jednostek administracyjnych używamy terminu "lokalizacja". Jeśli uprawnienia administratora nie mają zakresu funkcji AU, uprawnienia są stosowane w całej organizacji. Po włączeniu mojego pracownika użytkownicy, którzy zostali włączeni i mają przypisaną rolę administracyjną, mogą uzyskać do niej dostęp [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Można wybrać pozycję "Uruchom", aby wyświetlić użytkowników w tej funkcji, i wybrać użytkownika, aby otworzyć swój profil.

## <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Następujące role mają uprawnienia do resetowania hasła użytkownika:

- [Administrator uwierzytelniania](permissions-reference.md#authentication-administrator)
- [Administrator uprzywilejowanego uwierzytelniania](permissions-reference.md#privileged-authentication-administrator)
- [Administrator globalny](permissions-reference.md#global-administrator--company-administrator)
- [Administrator pomocy technicznej](permissions-reference.md#helpdesk-administrator)
- [Administrator użytkowników](permissions-reference.md#user-administrator)
- [Administrator haseł](permissions-reference.md#password-administrator)

W obszarze **mój personel**Otwórz profil użytkownika. Wybierz pozycję **Resetuj hasło**.

- Jeśli użytkownik jest tylko w chmurze, można wyświetlić hasło tymczasowe, które można nadać użytkownikowi.
- Jeśli użytkownik jest synchronizowany z Active Directory lokalnego, można wprowadzić hasło spełniające Twoje lokalne zasady usługi AD. Następnie możesz nadać temu użytkownikowi hasło.

    ![Wskaźnik postępu resetowania hasła i powiadomienia o powodzeniu](./media/my-staff-configure/reset-password.png)

Użytkownik musi zmienić hasło przy następnym logowaniu.

## <a name="manage-a-phone-number"></a>Zarządzanie numerem telefonu

W obszarze **mój personel**Otwórz profil użytkownika.

- Wybierz sekcję **Dodawanie numeru telefonu** , aby dodać numer telefonu użytkownika
- Wybierz pozycję **Edytuj numer telefonu** , aby zmienić numer telefonu
- Wybierz pozycję **Usuń numer telefonu** , aby usunąć numer telefonu użytkownika

W zależności od ustawień użytkownik może następnie użyć numeru telefonu skonfigurowanego do logowania za pomocą wiadomości SMS, przeprowadzenia uwierzytelniania wieloskładnikowego i przeprowadzenia samoobsługowego resetowania hasła.

Aby zarządzać numerem telefonu użytkownika, należy przypisać jedną z następujących ról:

- [Administrator uwierzytelniania](permissions-reference.md#authentication-administrator)
- [Administrator uprzywilejowanego uwierzytelniania](permissions-reference.md#privileged-authentication-administrator)
- [Administrator globalny](permissions-reference.md#global-administrator--company-administrator)

## <a name="search"></a>Wyszukiwanie

Na pasku wyszukiwania w obszarze Moi pracownicy można wyszukiwać w swojej organizacji jednostki organizacyjne i użytkowników. Możesz przeszukiwać wszystkich jednostek i użytkowników w organizacji, ale możesz wprowadzać zmiany tylko dla użytkowników, którzy znajdują się w usłudze AU, w której masz uprawnienia administratora.

Możesz również wyszukać użytkownika w ramach funkcji aktualizacji automatycznych. Aby to zrobić, użyj paska wyszukiwania w górnej części listy użytkowników.

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji można wyświetlić w przypadku akcji podejmowanych w obszarze Moi pracownicy w portalu Azure Active Directory. Jeśli dziennik inspekcji został wygenerowany przez akcję wykonywaną w ramach mojego personelu, zobaczysz to w obszarze dodatkowe szczegóły w zdarzeniu inspekcji.

## <a name="next-steps"></a>Następne kroki

[Dokumentacja dotycząca](../user-help/my-staff-team-manager.md) 
 użytkowników mojego personelu [Dokumentacja jednostek administracyjnych](administrative-units.md)