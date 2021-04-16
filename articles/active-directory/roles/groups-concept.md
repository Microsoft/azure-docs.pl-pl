---
title: Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory | Microsoft Docs
description: Wyświetlanie wersji zapoznawczej niestandardowych ról usługi Azure AD w celu delegowania zarządzania tożsamościami. Zarządzanie przypisaniami ról platformy Azure w Azure Portal, PowerShell lub interfejs Graph API.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: article
ms.date: 11/05/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1fea64305d4735c5bf1bf59b86ae5283600e622
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502202"
---
# <a name="use-cloud-groups-to-manage-role-assignments-in-azure-active-directory-preview"></a>Używanie grup w chmurze do zarządzania przypisaniami ról w Azure Active Directory (wersja zapoznawcza)

Azure Active Directory (Azure AD) jest wprowadzona publiczna wersja zapoznawcza, w której można przypisać grupę chmury do wbudowanych ról usługi Azure AD. Dzięki tej funkcji można używać grup do udzielania dostępu administratora w usłudze Azure AD przy minimalnym nakładzie pracy ze strony administratorów ról globalnych i uprzywilejowanych.

Rozważmy ten przykład: Firma Contoso zatrudniła osoby w różnych lokalizacjach geograficznych w celu zarządzania hasłami pracowników w organizacji usługi Azure AD i ich resetowania. Zamiast prosić administratora ról uprzywilejowanych lub administratora globalnego o przypisanie roli administratora pomocy technicznej do każdej osoby indywidualnie, może ona utworzyć grupę Contoso_Helpdesk_Administrators i przypisać ją do roli. Gdy ludzie dołączają do grupy, mają pośrednio przypisaną rolę. Istniejący przepływ pracy nadzoru może następnie zająć się procesem zatwierdzania i inspekcją członkostwa w grupie, aby upewnić się, że tylko prawi użytkownicy są członkami grupy i w związku z tym są przypisani do roli administratora pomocy technicznej.

## <a name="how-this-feature-works"></a>Jak działa ta funkcja

Utwórz nową Microsoft 365 zabezpieczeń z właściwością "isAssignableToRole" ustawioną na wartość "true". Tę właściwość można również włączyć podczas tworzenia grupy w Azure Portal, włączając role usługi **Azure AD,** można przypisać do grupy . W obu tych sposób można następnie przypisać grupę do co najmniej jednej roli usługi Azure AD w taki sam sposób, jak w przypadku przypisywania ról do użytkowników. W jednej organizacji (dzierżawie) usługi Azure AD można utworzyć maksymalnie 250 grup przypisanych do ról.

Jeśli nie chcesz, aby członkowie grupy mieli stały dostęp do roli, możesz użyć Azure AD Privileged Identity Management. Przypisz grupę jako uprawnionego członka roli usługi Azure AD. Każdy członek grupy jest następnie uprawniony do aktywowania swojego przypisania dla roli, do która jest przypisana grupa. Następnie mogą aktywować przypisanie roli na stały czas trwania.

> [!Note]
> Aby móc przypisać grupę do roli usługi Azure AD za pośrednictwem usługi PIM, musisz mieć zaktualizowaną wersję usługi Privileged Identity Management. Możesz korzystać ze starszej wersji usługi PIM, ponieważ Twoja organizacja usługi Azure AD korzysta z Privileged Identity Management API. Skontaktuj się z aliasem, pim_preview@microsoft.com aby przenieść organizację i zaktualizować interfejs API. Aby dowiedzieć się [więcej, zobacz Azure AD roles and features in PIM (Role i funkcje usługi Azure AD w usłudze PIM).](../privileged-identity-management/azure-ad-roles-features.md)

## <a name="why-we-enforce-creation-of-a-special-group-for-assigning-it-to-a-role"></a>Dlaczego wymuszamy utworzenie specjalnej grupy w celu przypisania jej do roli

Jeśli grupa ma przypisaną rolę, każdy administrator IT, który może zarządzać członkostwem w grupie, może pośrednio zarządzać członkostwem w tej roli. Załóżmy na przykład, że grupa Contoso_User_Administrators przypisana do roli administratora konta użytkownika. Administrator programu Exchange, który może modyfikować członkostwo w grupie, może dodać się do grupy Contoso_User_Administrators i w ten sposób zostać administratorem konta użytkownika. Jak widać, administrator może podnieść swoje uprawnienia w sposób, który nie był przez Ciebie spodziewany.

Usługa Azure AD umożliwia ochronę grupy przypisanej do roli przy użyciu nowej właściwości o nazwie isAssignableToRole dla grup. Do roli można przypisać tylko grupy w chmurze, które miały właściwość isAssignableToRole ustawioną na wartość "true" podczas tworzenia. Ta właściwość jest niezmienna; Po utworzeniu grupy z tą właściwością ustawioną na wartość "true" nie można jej zmienić. Nie można ustawić właściwości dla istniejącej grupy.
Zaprojektowaliśmy sposób przypisywania grup do ról, aby zapobiec tego rodzaju potencjalnym naruszeniom:

- Tylko administratorzy globalni i administratorzy ról uprzywilejowanych mogą tworzyć grupę przypisaną do roli (z włączoną właściwością "isAssignableToRole").
- Nie może to być grupa dynamiczna usługi Azure AD. oznacza to, że musi mieć typ członkostwa "Przypisane". Automatyczne zaludnianie grup dynamicznych może prowadzić do dodania niechcianego konta do grupy i przypisania w ten sposób do roli.
- Domyślnie tylko administratorzy globalni i administratorzy ról uprzywilejowanych mogą zarządzać członkostwem w grupie przypisanej do roli, ale można delegować zarządzanie grupami przypisanymi do ról, dodając właścicieli grup.
- Aby zapobiec podniesieniu uprawnień, poświadczenia członków i właścicieli grupy przypisanej do roli mogą być zmieniane tylko przez uprzywilejowany administrator uwierzytelniania lub administrator globalny.
- Brak zagnieżdżania. Grupy nie można dodać jako członka grupy przypisanej do roli.

## <a name="limitations"></a>Ograniczenia

Następujące scenariusze nie są obecnie obsługiwane:  

- Przypisywanie grup lokalnych do ról usługi Azure AD (wbudowanych lub niestandardowych)

## <a name="known-issues"></a>Znane problemy

- Tylko licencjonowani klienci usługi *Azure AD P2:* nie przypisuj grupy jako aktywnej do roli zarówno za pośrednictwem usługi Azure AD, jak i Privileged Identity Management (PIM). W szczególności nie przypisuj roli do grupy możliwej do  przypisania podczas jej tworzenia i przypisz ją do grupy później przy użyciu usługi PIM. Spowoduje to problemy, w przypadku których użytkownicy nie będą widzieć aktywnych przypisań ról w uciece PIM, a także nie będzie można usunąć tego przypisania usługi PIM. W tym scenariuszu nie ma to wpływu na kwalifikujące się przypisania. Jeśli spróbujemy wykonać to przypisanie, może wystąpić nieoczekiwane zachowanie, takie jak:
  - Czas zakończenia przypisania roli może być wyświetlany niepoprawnie.
  - W portalu PIM w witrynie **Moje** role można wyświetlić tylko jedno przypisanie roli niezależnie od liczby metod, za pomocą których przypisanie jest udzielane (za pośrednictwem co najmniej jednej grupy i bezpośrednio).
- *Tylko licencjonowani klienci usługi Azure AD P2* Nawet po usunięciu grupy jest ona nadal wyświetlana jako uprawniony członek roli w interfejsie użytkownika pim. Funkcjonalnie nie ma problemu; Jest to po prostu problem z pamięcią podręczną w Azure Portal.  
- Użyj nowego centrum [administracyjnego programu Exchange do](https://admin.exchange.microsoft.com/) przypisywania ról za pośrednictwem członkostwa w grupie. Stara funkcja centrum administracyjnego programu Exchange nie obsługuje jeszcze tej funkcji. Polecenia cmdlet programu Exchange PowerShell będą działać zgodnie z oczekiwaniami.
- Azure Information Protection Portal (portal klasyczny) nie rozpoznaje jeszcze członkostwa roli za pośrednictwem grupy. Możesz przeprowadzić [migrację na ujednoliconą](/azure/information-protection/configure-policy-migrate-labels) platformę etykietowania czułości, a następnie użyć Centrum zgodności usługi Office 365 Security &, aby zarządzać rolami za pomocą przypisań grup.
- [Centrum administracyjne aplikacji](https://config.office.com/) nie obsługuje jeszcze tej funkcji. Przypisywanie użytkowników bezpośrednio do roli administratora usługi Office Apps.
- [Centrum zgodności M365](https://compliance.microsoft.com/) nie obsługuje jeszcze tej funkcji. Przypisz użytkowników bezpośrednio do odpowiednich ról usługi Azure AD, aby korzystać z tego portalu.

Naprawiamy te problemy.

## <a name="required-license-plan"></a>Wymagany plan licencjonowania

Korzystanie z tej funkcji wymaga, aby mieć dostępną Azure AD — wersja Premium P1 w organizacji usługi Azure AD. Aby można było Privileged Identity Management aktywacji roli just in time, musisz mieć dostępną Azure AD — wersja Premium P2. Aby znaleźć odpowiednią licencję dla swoich wymagań, zobacz [Porównanie ogólnie dostępnych funkcji planów w wersjach Bezpłatna i Premium.](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie grupy z możliwością przypisania do roli](groups-create-eligible.md)
- [Przypisywanie roli do grupy przypisanej do roli](groups-assign-role.md)
