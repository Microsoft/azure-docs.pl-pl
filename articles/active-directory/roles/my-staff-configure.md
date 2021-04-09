---
title: Korzystanie z mojego personelu do delegowania zarządzania użytkownikami — Azure AD | Microsoft Docs
description: Delegowanie zarządzania użytkownikami przy użyciu funkcji mój personel i jednostki administracyjne
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.topic: how-to
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.date: 03/11/2021
ms.author: rolyon
ms.reviewer: sahenry
ms.custom: oldportal;it-pro;
ms.openlocfilehash: 1a380c8a3d766c3c11d8cba1148383d924f65a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103225000"
---
# <a name="manage-your-users-with-my-staff"></a>Zarządzanie użytkownikami za pomocą mojego personelu

Moi pracownicy umożliwiają delegowanie uprawnień do zespołu ds. usług, takich jak Menedżer sklepu lub potencjalny klient, aby mieć pewność, że członkowie personelu będą mogli uzyskać dostęp do swoich kont usługi Azure AD. Zamiast polegać na centralnej pomocy technicznej, organizacje mogą delegować typowe zadania, takie jak resetowanie haseł lub zmiana numerów telefonów w lokalnym programie Team Manager. W przypadku mojego personelu użytkownik, który nie może uzyskać dostępu do swojego konta, może odzyskać dostęp tylko w kilku kliknięciach, bez pomocy technicznej lub personelu działu IT.

Przed skonfigurowaniem pracowników w organizacji zalecamy zapoznanie się z tą dokumentacją oraz [dokumentację dotyczącą użytkownika](../user-help/my-staff-team-manager.md) , aby zrozumieć, jak to działa i jak ma to wpływ na użytkowników. Można wykorzystać dokumentację użytkownika do uczenia i przygotowania użytkowników do nowego środowiska i zapewnienia pomyślnego wdrożenia.

## <a name="how-my-staff-works"></a>Jak działa mój personel

Mój personel jest oparty na jednostkach administracyjnych, które są kontenerami zasobów, których można użyć do ograniczenia zakresu kontroli administracyjnej przypisania roli. Aby uzyskać więcej informacji, zobacz [Zarządzanie jednostkami administracyjnymi w Azure Active Directory](administrative-units.md). W obszarze mój personel jednostki administracyjne mogą zawierać grupę użytkowników w sklepie lub dziale. Menedżer zespołów można następnie przypisać do roli administracyjnej w zakresie jednej lub większej liczby jednostek.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Aby wykonać ten artykuł, potrzebne są następujące zasoby i uprawnienia:

* Aktywna subskrypcja platformy Azure.

  * Jeśli nie masz subskrypcji platformy Azure, [Utwórz konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Dzierżawa usługi Azure Active Directory skojarzona z Twoją subskrypcją.

  * W razie konieczności [Utwórz dzierżawę Azure Active Directory](../fundamentals/sign-up-organization.md) lub [skojarz subskrypcję platformy Azure z Twoim kontem](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
* Musisz mieć uprawnienia *administratora globalnego* w dzierżawie usługi Azure AD, aby włączyć uwierzytelnianie oparte na programie SMS.
* Każdy użytkownik, który jest włączony w zasadach metody uwierzytelniania wiadomości tekstowych musi być licencjonowany, nawet jeśli nie używa tego programu. Każdy włączony użytkownik musi mieć jedną z następujących licencji usługi Azure AD lub Microsoft 365:

  * [Usługa Azure AD — wersja Premium P1 lub P2](https://azure.microsoft.com/pricing/details/active-directory/)
  * [Microsoft 365 (M365) F1 lub F3](https://www.microsoft.com/licensing/news/m365-firstline-workers)
  * [Enterprise Mobility + Security (EMS) E3 lub E5](https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing) lub [Microsoft 365 (M365) E3 lub E5](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans)

## <a name="how-to-enable-my-staff"></a>Jak włączyć personel

Po skonfigurowaniu jednostek administracyjnych możesz zastosować ten zakres do użytkowników, którzy uzyskują dostęp do mojego personelu. Tylko użytkownicy, którym przypisano rolę administracyjną, mogą uzyskać dostęp do mojego personelu. Aby włączyć personel, wykonaj następujące czynności:

1. Zaloguj się do Azure Portal jako administrator użytkownika.
2. Przejdź do **Azure Active Directory**  >  **Ustawienia użytkownika** wersje  >    >  **zapoznawcze funkcji użytkownika Zarządzaj ustawieniami wersji zapoznawczej funkcji użytkownika**.
3. W obszarze **Administratorzy mogą uzyskać dostęp do mojego personelu**, możesz wybrać opcję Włącz dla wszystkich użytkowników, wybranych użytkowników lub bez dostępu użytkownika.

> [!Note]
> Tylko użytkownicy, którym przypisano rolę administratora, mogą uzyskać dostęp do mojego personelu. Jeśli włączysz personel dla użytkownika, który nie ma przypisanej roli administratora, nie będzie on mógł uzyskać dostępu do mojego personelu.

## <a name="conditional-access"></a>Dostęp warunkowy

Portal mojego personelu można chronić za pomocą zasad dostępu warunkowego usługi Azure AD. Użyj jej do zadań, takich jak wymaganie uwierzytelniania wieloskładnikowego przed uzyskaniem dostępu do mojego personelu.

Zdecydowanie zalecamy ochronę mojego personelu przy użyciu [zasad dostępu warunkowego usługi Azure AD](../conditional-access/index.yml). Aby zastosować zasady dostępu warunkowego do mojego personelu, należy najpierw odwiedzić witrynę My Personal, aby automatycznie zainicjować obsługę administracyjną jednostki usługi w dzierżawie do użycia przez dostęp warunkowy.

Podczas tworzenia zasad dostępu warunkowego, które mają zastosowanie do aplikacji w chmurze my personel, zostanie wyświetlona nazwa główna usługi.

![Tworzenie zasad dostępu warunkowego dla aplikacji My Personal](./media/my-staff-configure/conditional-access.png)

## <a name="using-my-staff"></a>Korzystanie z mojego personelu

Gdy użytkownik przechodzi do mojego pracownika, są wyświetlane nazwy [jednostek administracyjnych](administrative-units.md) , do których mają uprawnienia administracyjne. W [dokumentacji użytkownika mojego personelu](../user-help/my-staff-team-manager.md)do odwoływania się do jednostek administracyjnych używamy terminu "lokalizacja". Jeśli uprawnienia administratora nie mają zakresu jednostki administracyjnej, uprawnienia są stosowane w całej organizacji. Po włączeniu mojego pracownika użytkownicy, którzy zostali włączeni i mają przypisaną rolę administracyjną, mogą uzyskać do niej dostęp [https://mystaff.microsoft.com](https://mystaff.microsoft.com) . Mogą wybrać jednostkę administracyjną, aby wyświetlić użytkowników w tej jednostce, a następnie wybrać użytkownika, aby otworzyć swój profil.

## <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby hasła były przechowywane dla użytkowników lokalnych, należy spełnić następujące warunki wstępne. Aby uzyskać szczegółowe instrukcje, zobacz artykuł Włączanie samoobsługowego [resetowania hasła](../authentication/tutorial-enable-sspr-writeback.md) .

* Konfigurowanie uprawnień do zapisywania zwrotnego haseł
* Włącz funkcję zapisywania zwrotnego haseł w Azure AD Connect
* Włącz funkcję zapisywania zwrotnego haseł w funkcji samoobsługowego resetowania hasła w usłudze Azure AD (SSPR)

Następujące role mają uprawnienia do resetowania hasła użytkownika:

* [Administrator uwierzytelniania](permissions-reference.md#authentication-administrator)
* [Administrator uprzywilejowanego uwierzytelniania](permissions-reference.md#privileged-authentication-administrator)
* [Administrator globalny](permissions-reference.md#global-administrator)
* [Administrator pomocy technicznej](permissions-reference.md#helpdesk-administrator)
* [Administrator użytkowników](permissions-reference.md#user-administrator)
* [Administrator haseł](permissions-reference.md#password-administrator)

W obszarze **mój personel** Otwórz profil użytkownika. Wybierz pozycję **Resetuj hasło**.

* Jeśli użytkownik jest tylko w chmurze, można wyświetlić hasło tymczasowe, które można nadać użytkownikowi.
* Jeśli użytkownik jest synchronizowany z Active Directory lokalnego, można wprowadzić hasło spełniające Twoje lokalne zasady usługi AD. Następnie możesz nadać temu użytkownikowi hasło.

    ![Wskaźnik postępu resetowania hasła i powiadomienia o powodzeniu](./media/my-staff-configure/reset-password.png)

Użytkownik musi zmienić hasło przy następnym logowaniu.

## <a name="manage-a-phone-number"></a>Zarządzanie numerem telefonu

W obszarze **mój personel** Otwórz profil użytkownika.

* Wybierz sekcję **Dodawanie numeru telefonu** , aby dodać numer telefonu użytkownika
* Wybierz pozycję **Edytuj numer telefonu** , aby zmienić numer telefonu
* Wybierz pozycję **Usuń numer telefonu** , aby usunąć numer telefonu użytkownika

W zależności od ustawień użytkownik może następnie użyć numeru telefonu skonfigurowanego do logowania za pomocą wiadomości SMS, przeprowadzenia uwierzytelniania wieloskładnikowego i przeprowadzenia samoobsługowego resetowania hasła.

Aby zarządzać numerem telefonu użytkownika, należy przypisać jedną z następujących ról:

* [Administrator uwierzytelniania](permissions-reference.md#authentication-administrator)
* [Administrator uprzywilejowanego uwierzytelniania](permissions-reference.md#privileged-authentication-administrator)
* [Administrator globalny](permissions-reference.md#global-administrator)

## <a name="search"></a>Wyszukaj

Możesz wyszukiwać jednostki administracyjne i użytkowników w organizacji za pomocą paska wyszukiwania w obszarze mój personel. Możesz przeszukiwać wszystkie jednostki administracyjne i użytkowników w organizacji, ale możesz wprowadzać zmiany tylko dla użytkowników, którzy znajdują się w jednostce administracyjnej, w której masz uprawnienia administratora.

Możesz również wyszukać użytkownika w jednostce administracyjnej. Aby to zrobić, użyj paska wyszukiwania w górnej części listy użytkowników.

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji można wyświetlić w przypadku akcji podejmowanych w obszarze Moi pracownicy w portalu Azure Active Directory. Jeśli dziennik inspekcji został wygenerowany przez akcję wykonywaną w ramach mojego personelu, zobaczysz to w obszarze dodatkowe szczegóły w zdarzeniu inspekcji.

## <a name="next-steps"></a>Następne kroki

[Dokumentacja dotycząca](../user-help/my-staff-team-manager.md) 
 użytkowników mojego personelu [Dokumentacja jednostek administracyjnych](administrative-units.md)
