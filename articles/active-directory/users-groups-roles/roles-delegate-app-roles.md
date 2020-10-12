---
title: Delegowanie uprawnień administratora zarządzania aplikacjami — usługa Azure AD | Microsoft Docs
description: Przyznawanie uprawnień do zarządzania dostępem do aplikacji w Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2527e4e630e7bf9cce0c68261bfa1c1797fbf795
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84731821"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegowanie uprawnień rejestracji aplikacji w Azure Active Directory

W tym artykule opisano, jak używać uprawnień udzielonych przez role niestandardowe w usłudze Azure Active Directory (Azure AD) w celu rozwiązania potrzeb związanych z zarządzaniem aplikacjami. W usłudze Azure AD można delegować uprawnienia do tworzenia aplikacji i zarządzania nimi w następujący sposób:

- [Ograniczenie użytkowników, którzy mogą tworzyć aplikacje](#restrict-who-can-create-applications) i zarządzać tworzonymi przez nie aplikacjami. Domyślnie w usłudze Azure AD wszyscy użytkownicy mogą rejestrować rejestracje aplikacji i zarządzać wszystkimi aspektami tworzonych przez nie aplikacji. Może to być ograniczone tylko do wybranych osób, które mają uprawnienia.
- [Przypisywanie jednego lub większej liczby właścicieli do aplikacji](#assign-application-owners). Jest to prosty sposób udzielenia komuś możliwości zarządzania wszystkimi aspektami konfiguracji usługi Azure AD dla określonej aplikacji.
- [Przypisanie wbudowanej roli administracyjnej](#assign-built-in-application-admin-roles) , która przyznaje dostęp do zarządzania konfiguracją w usłudze Azure AD dla wszystkich aplikacji. Jest to zalecany sposób udzielania ekspertom IT dostępu do zarządzania szerokimi uprawnieniami konfiguracji aplikacji bez udzielania dostępu do zarządzania innymi częściami usługi Azure AD niezwiązanych z konfiguracją aplikacji.
- [Tworzenie roli niestandardowej](#create-and-assign-a-custom-role-preview) Definiowanie bardzo konkretnych uprawnień i przypisywanie jej do zakresu jednej aplikacji jako ograniczonej właściciela lub zakresu katalogu (wszystkie aplikacje) jako ograniczonego administratora.

Ważne jest, aby rozważyć udzielenie dostępu przy użyciu jednej z powyższych metod z dwóch przyczyn. Po pierwsze Delegowanie możliwości wykonywania zadań administracyjnych zmniejsza obciążenie administratora globalnego. Po drugie użycie ograniczonych uprawnień usprawnia stan zabezpieczeń i zmniejsza możliwości nieautoryzowanego dostępu. Zagadnienia dotyczące delegowania i ogólne wytyczne zostały omówione w temacie [delegowanie administracji w Azure Active Directory](roles-concept-delegation.md).

## <a name="restrict-who-can-create-applications"></a>Ogranicz, kto może tworzyć aplikacje

Domyślnie w usłudze Azure AD wszyscy użytkownicy mogą rejestrować rejestracje aplikacji i zarządzać wszystkimi aspektami tworzonych przez nie aplikacji. Wszyscy mogą również wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu. Możesz wybrać opcję selektywnego przydzielenia tych uprawnień przez ustawienie przełączników globalnych na wartość "No" (nie) i dodanie wybranych użytkowników do roli Deweloper aplikacji.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Aby wyłączyć domyślną możliwość tworzenia rejestracji aplikacji lub wyrażania zgody na aplikacje

1. Zaloguj się do swojej organizacji usługi Azure AD przy użyciu konta uprawniającego do roli administratora globalnego w organizacji usługi Azure AD.
1. Ustaw jedną lub obie następujące czynności:

    - Na [stronie Ustawienia użytkownika organizacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings)ustaw opcję **Użytkownicy mogą rejestrować aplikacje** na wartość nie. Spowoduje to wyłączenie domyślnej możliwości tworzenia rejestracji aplikacji przez użytkowników.
    - Na stronie [Ustawienia użytkownika dla aplikacji dla przedsiębiorstw](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)ustaw, aby **użytkownicy mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** na wartość nie. Dzięki temu użytkownicy będą mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Udzielanie osobnych uprawnień do tworzenia i wyrażania zgody na aplikacje, gdy domyślna możliwość jest wyłączona

Przypisz rolę Deweloper aplikacji, aby umożliwić tworzenie rejestracji aplikacji, gdy ustawienie **Użytkownicy mogą rejestrować aplikacje** jest ustawione na wartość nie. Ta rola przyznaje także uprawnienia do wyrażania zgody w imieniu użytkownika, gdy **Użytkownicy będą mogli wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** . Jako zachowanie systemowe, gdy użytkownik tworzy nową rejestrację aplikacji, są one automatycznie dodawane jako pierwszy właściciel. Uprawnienia własności umożliwiają użytkownikowi zarządzanie wszystkimi aspektami rejestracji aplikacji lub aplikacji korporacyjnych.

## <a name="assign-application-owners"></a>Przypisywanie właścicieli aplikacji

Przypisywanie właścicieli to prosty sposób na umożliwienie zarządzania wszystkimi aspektami konfiguracji usługi Azure AD na potrzeby rejestracji aplikacji lub aplikacji korporacyjnych. Jako zachowanie systemowe, gdy użytkownik tworzy nową rejestrację aplikacji, jest automatycznie dodawana jako pierwszy właściciel. Uprawnienia własności umożliwiają użytkownikowi zarządzanie wszystkimi aspektami rejestracji aplikacji lub aplikacji korporacyjnych. Oryginalny właściciel można usunąć i można dodać dodatkowych właścicieli.

### <a name="enterprise-application-owners"></a>Właściciele aplikacji dla przedsiębiorstw

Jako właściciel użytkownik może zarządzać konfiguracją aplikacji przedsiębiorstwa, taką jak konfiguracja logowania jednokrotnego, Inicjowanie obsługi administracyjnej i przypisania użytkowników. Właściciel może również dodawać i usuwać innych właścicieli. W przeciwieństwie do administratorów globalnych właściciele mogą zarządzać tylko tymi aplikacjami przedsiębiorstwa.

W niektórych przypadkach aplikacje dla przedsiębiorstw utworzone za pomocą galerii aplikacji obejmują zarówno aplikację przedsiębiorstwa, jak i rejestrację aplikacji. Jeśli ta wartość jest prawdziwa, Dodawanie właściciela do aplikacji przedsiębiorstwa automatycznie dodaje właściciela do odpowiedniej rejestracji aplikacji jako właściciel.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Aby przypisać właściciela do aplikacji dla przedsiębiorstw

1. Zaloguj się do [swojej organizacji usługi Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)   przy użyciu konta uprawniającego do administratora aplikacji lub administratora aplikacji w chmurze w organizacji.
1. Na [stronie rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)   organizacji wybierz aplikację, aby otworzyć stronę przegląd dla aplikacji.
1. Wybierz pozycję **właściciele** , aby wyświetlić listę właścicieli aplikacji.
1. Wybierz pozycję **Dodaj** , aby wybrać co najmniej jednego właściciela, który ma zostać dodany do aplikacji.

> [!IMPORTANT]
> Użytkownicy i podmioty usługi mogą być właścicielami rejestracji aplikacji. Tylko użytkownicy mogą być właścicielami aplikacji przedsiębiorstwa. Grupy nie mogą być przypisane jako właściciele jednego z nich.
>
> Właściciele mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Aplikacja może mieć więcej uprawnień niż właściciel i w ten sposób mieć podniesienie uprawnień do tego, co właściciel ma dostęp jako nazwę główną użytkownika lub usługi. Właściciel aplikacji może potencjalnie utworzyć lub zaktualizować użytkowników lub inne obiekty, jednocześnie personifikując aplikację, w zależności od uprawnień aplikacji.

## <a name="assign-built-in-application-admin-roles"></a>Przypisywanie wbudowanych ról administratora aplikacji

Usługa Azure AD ma zestaw wbudowanych ról administracyjnych służących do udzielania dostępu do zarządzania konfiguracją w usłudze Azure AD dla wszystkich aplikacji. Te role są zalecanym sposobem przyznania ekspertom IT dostępu do zarządzania szerokimi uprawnieniami konfiguracji aplikacji bez udzielania dostępu do zarządzania innymi częściami usługi Azure AD niezwiązanych z konfiguracją aplikacji.

- Administrator aplikacji: Użytkownicy w tej roli mogą tworzyć wszystkie aspekty aplikacji przedsiębiorstwa, rejestracji aplikacji i ustawień serwera proxy aplikacji oraz zarządzać nimi. Ta rola umożliwia również zgodę na uprawnienia delegowane i uprawnienia aplikacji z wyłączeniem Microsoft Graph. Użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.
- Administrator aplikacji w chmurze: Użytkownicy w tej roli mają takie same uprawnienia jak rola administratora aplikacji, z wyłączeniem możliwości zarządzania serwerem proxy aplikacji. Użytkownicy przypisani do tej roli nie są dodawani jako właściciele podczas tworzenia nowych rejestracji aplikacji lub aplikacji dla przedsiębiorstw.

Aby uzyskać więcej informacji i wyświetlić opis tych ról, zobacz [dostępne role](directory-assign-admin-roles.md#available-roles).

Postępuj zgodnie z instrukcjami podanymi w temacie [Assign role to users with Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) przewodnik, aby przypisać rolę administratora aplikacji lub administratora aplikacji w chmurze.

> [!IMPORTANT]
> Administratorzy aplikacji i Administratorzy aplikacji w chmurze mogą dodawać poświadczenia do aplikacji i używać tych poświadczeń do personifikacji tożsamości aplikacji. Aplikacja może mieć uprawnienia, które są podniesieniem uprawnień za pośrednictwem uprawnień roli administratora. Administrator w tej roli może potencjalnie utworzyć lub zaktualizować użytkowników lub inne obiekty podczas personifikacji aplikacji, w zależności od uprawnień aplikacji.
> Żadna rola nie przyznaje możliwości zarządzania ustawieniami dostępu warunkowego.

## <a name="create-and-assign-a-custom-role-preview"></a>Tworzenie i przypisywanie roli niestandardowej (wersja zapoznawcza)

Tworzenie ról niestandardowych i przypisywanie ról niestandardowych są osobnymi krokami:

- [Utwórz niestandardową *definicję roli* ](roles-create-custom.md) i [Dodaj do niej uprawnienia z listy ustawień wstępnych](roles-custom-available-permissions.md). Są to te same uprawnienia, które są używane w rolach wbudowanych.
- [Utwórz *przypisanie roli* ](roles-assign-powershell.md) , aby przypisać rolę niestandardową.

Ta separacja umożliwia utworzenie jednej definicji roli, a następnie przypisanie jej wiele razy w różnych *zakresach*. Rolę niestandardową można przypisać w zakresie całej organizacji lub można ją przypisać do zakresu, jeśli pojedynczy obiekt usługi Azure AD. Przykładem zakresu obiektu jest rejestracja pojedynczej aplikacji. Korzystając z różnych zakresów, można przypisać tę samą definicję roli do Sally przez wszystkie rejestracje aplikacji w organizacji, a następnie Naveen tylko na potrzeby rejestracji aplikacji w raportach wydatków firmy Contoso.

Porady dotyczące tworzenia i używania ról niestandardowych do delegowania zarządzania aplikacjami:
- Role niestandardowe udzielają dostępu tylko w najpopularniejszych blokach rejestracji aplikacji w portalu usługi Azure AD. Nie przyznają one dostępu w blokach rejestracji starszych aplikacji.
- Role niestandardowe nie zapewniają dostępu do portalu usługi Azure AD, gdy ustawienie użytkownika "Ogranicz dostęp do portalu administracyjnego usługi Azure AD" ma wartość tak.
- Rejestracje aplikacji użytkownik ma dostęp do korzystania z przypisań ról tylko na karcie "wszystkie aplikacje" na stronie rejestracji aplikacji. Nie są one wyświetlane na karcie "posiadane aplikacje".

Aby uzyskać więcej informacji na temat podstawowych ról niestandardowych, zobacz [Omówienie ról niestandardowych](roles-custom-overview.md), a także sposób [tworzenia roli niestandardowej](roles-create-custom.md) i sposobu [przypisywania roli](roles-assign-powershell.md).

## <a name="next-steps"></a>Następne kroki

- [Podtypy rejestracji aplikacji i uprawnienia](roles-custom-available-permissions.md)
- [Dokumentacja roli administratora usługi Azure AD](directory-assign-admin-roles.md)
