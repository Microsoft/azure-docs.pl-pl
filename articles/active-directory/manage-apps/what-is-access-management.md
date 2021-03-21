---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD
description: Opisuje, w jaki sposób Azure Active Directory umożliwia organizacjom Określanie aplikacji, do których każdy użytkownik ma dostęp.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: kenwith
ms.openlocfilehash: 5afc6aa8f52011eba6d7cfdfaa09b0ab995183e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257427"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji

Ciągłe zarządzanie dostępem, Ocena użycia i raportowanie nadal są wyzwaniem, gdy aplikacja jest zintegrowana z systemem tożsamości w organizacji. W wielu przypadkach Administratorzy IT lub pomoc techniczna muszą zająć się trwającą aktywną rolą zarządzania dostępem do aplikacji. Czasami przypisanie jest wykonywane przez zespół IT ogólnego lub rozdziałowego. Często decyzja o przypisaniu jest przeznaczona do delegowania do producenta decyzji biznesowej, wymagając do zatwierdzenia przed przypisaniem.  Inne organizacje zainwestowali w integrację z istniejącym automatycznym systemem zarządzania tożsamościami i dostępem, takimi jak Role-Based Access Control (RBAC) lub Attribute-Based Access Control (ABAC). Rozwój integracji i reguł jest wyspecjalizowany i kosztowny. Monitorowanie lub raportowanie w ramach podejścia do zarządzania to własne oddzielne, kosztowne i złożone inwestycje.

## <a name="how-does-azure-active-directory-help"></a>Jak Azure Active Directory pomoc?

Usługa Azure AD obsługuje szerokie zarządzanie dostępem do skonfigurowanych aplikacji, co umożliwia organizacjom łatwe osiąganie właściwych zasad dostępu w zakresie od automatycznego przypisania opartego na atrybutach (ABAC lub RBAC scenariuszy) przez delegowanie i zarządzanie administratorami. Za pomocą usługi Azure AD można łatwo uzyskać złożone zasady, łącząc wiele modeli zarządzania dla jednej aplikacji, a nawet ponownie używać reguł zarządzania w aplikacjach z tymi samymi odbiorcami.

W przypadku korzystania z usługi Azure AD raportowanie użycia i przypisywania jest w pełni zintegrowane, co umożliwia administratorom łatwe raportowanie stanu przypisania, błędów przypisań i nawet użycia.

### <a name="assigning-users-and-groups-to-an-app"></a>Przypisywanie użytkowników i grup do aplikacji

Przypisanie aplikacji usługi Azure AD koncentruje się na dwóch podstawowych trybach przypisania:

* **Przypisanie indywidualne** Administrator IT z uprawnieniami administratora globalnego katalogu może wybrać poszczególne konta użytkowników i udzielić im dostępu do aplikacji.

* **Przypisanie oparte na grupach (wymaga Azure AD — wersja Premium P1 lub P2)** Administrator IT z uprawnieniami administratora globalnego katalogu może przypisać grupę do aplikacji. Dostęp określonego użytkownika jest określany na podstawie tego, czy są członkami grupy w momencie próby uzyskania dostępu do aplikacji. Innymi słowy, administrator może efektywnie utworzyć regułę przypisania informującą o tym, że wszyscy bieżący członek przypisanej grupy ma dostęp do aplikacji. Za pomocą tej opcji przypisania Administratorzy mogą korzystać z dowolnej opcji zarządzania grupami usługi Azure AD, w tym [grup dynamicznych opartych na atrybutach](../fundamentals/active-directory-groups-create-azure-portal.md), zewnętrznych grup systemu (np. lokalnych Active Directory lub Workday) lub grup zarządzanych przez administratora lub samoobsługowych. Pojedyncza grupa może być łatwo przypisana do wielu aplikacji, co zapewnia, że aplikacje z koligacją przypisania mogą udostępniać reguły przypisywania, co zmniejsza ogólną złożoność zarządzania. Należy zauważyć, że członkostwa w grupach zagnieżdżonych nie są obsługiwane w przypadku przypisywania do aplikacji w tej chwili.

Korzystając z tych dwóch trybów przypisywania, Administratorzy mogą uzyskać wszelkie pożądane podejście do zarządzania przydziałami.

### <a name="requiring-user-assignment-for-an-app"></a>Wymaganie przypisania użytkownika do aplikacji

W przypadku niektórych typów aplikacji można wybrać opcję [wymagania, aby użytkownicy mogli być przypisani do aplikacji](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment). Dzięki temu wszyscy użytkownicy nie mogą się zalogować, z wyjątkiem tych, które zostały jawnie przypisane do aplikacji. Następujące typy aplikacji obsługują tę opcję:

* Aplikacje skonfigurowane do federacyjnego logowania jednokrotnego (SSO) przy użyciu uwierzytelniania opartego na protokole SAML
* Aplikacje serwera proxy aplikacji korzystające z Azure Active Directory wstępnego uwierzytelniania
* Aplikacje utworzone na platformie aplikacji usługi Azure AD, która korzysta z uwierzytelniania OAuth 2,0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na tę aplikację. Niektóre aplikacje dla przedsiębiorstw oferują dodatkową kontrolę nad tym, kto może się zalogować.

Jeśli przypisanie użytkownika *nie jest wymagane*, Nieprzypisani użytkownicy nie widzą aplikacji w swoich aplikacjach, ale mogą nadal logować się do aplikacji (nazywanej także logowaniem z użyciem programu SP) lub mogą używać **adresu URL dostępu użytkownika** na stronie **Właściwości** aplikacji (nazywanej także logowaniem inicjowanym przez dostawcy tożsamości).

W przypadku niektórych aplikacji opcja Wymagaj przypisania użytkownika nie jest dostępna we właściwościach aplikacji. W takich przypadkach można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired w jednostce usługi.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Określanie środowiska użytkownika w celu uzyskiwania dostępu do aplikacji

Usługa Azure AD oferuje [kilka dostosowywalnych metod wdrażania aplikacji](end-user-experiences.md) dla użytkowników końcowych w organizacji:

* Moje aplikacje w usłudze Azure AD
* Microsoft 365 uruchamiania aplikacji
* Bezpośrednie logowanie do aplikacji federacyjnych (Service-PR)
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Można określić, czy użytkownicy przypisani do aplikacji dla przedsiębiorstw mogą ją zobaczyć w obszarze Moje aplikacje i Microsoft 365 uruchamianiu aplikacji.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Przykład: złożone przypisanie aplikacji za pomocą usługi Azure AD
Weź pod uwagę aplikację, taką jak Salesforce. W wielu organizacjach usługi Salesforce są używane głównie przez zespoły marketingowe i sprzedaży. Często członkowie zespołu ds. marketingu mają wysoce uprzywilejowany dostęp do usługi Salesforce, natomiast członkowie zespołu ds. sprzedaży mają ograniczony dostęp. W wielu przypadkach szeroka populacja pracowników przetwarzających informacje ma ograniczony dostęp do aplikacji. Wyjątki dotyczące tych reguł komplikują się. Często jest to przywilej zespołu lidera marketingu lub sprzedaży, aby udzielić użytkownikowi dostępu lub zmienić jego role niezależnie od tych reguł ogólnych.

W usłudze Azure AD aplikacje, takie jak Salesforce, można wstępnie skonfigurować pod kątem logowania jednokrotnego (SSO) i automatycznej aprowizacji. Po skonfigurowaniu aplikacji administrator może wykonać akcję jednorazową w celu utworzenia i przypisania odpowiednich grup. W tym przykładzie administrator może wykonać następujące przypisania:

* [Grupy dynamiczne](../fundamentals/active-directory-groups-create-azure-portal.md) można zdefiniować do automatycznego reprezentowania wszystkich członków zespołów marketingowych i sprzedaży przy użyciu atrybutów, takich jak dział lub rola:
  
  * Wszyscy członkowie grup marketingowych zostaną przypisani do roli "Marketing" w usłudze Salesforce
  * Wszyscy członkowie grup zespołu sprzedaży zostaną przypisani do roli "sprzedaż" w usłudze Salesforce. Dodatkowe udoskonalenie mogą wykorzystywać wiele grup reprezentujących zespoły sprzedaży regionalnej przypisane do różnych ról usługi Salesforce.

* Aby włączyć mechanizm wyjątku, można utworzyć grupę samoobsługową dla każdej roli. Na przykład grupa "wyjątek marketingowy Marketing" może zostać utworzona jako Grupa samoobsługi. Grupa może być przypisana do roli marketingu usługi Salesforce, a zespół liderów marketingu może być właścicielem. Członkowie zespołu liderów marketingu mogą dodawać lub usuwać użytkowników, ustawiać zasady dołączania, a nawet zatwierdzać lub odrzucać żądania poszczególnych użytkowników do przyłączenia. Ten mechanizm jest obsługiwany przez pracownika przetwarzający informacje, który nie wymaga specjalistycznego szkolenia dla właścicieli lub członków.

W takim przypadku wszystkim przypisanym użytkownikom zostanie automatycznie przydzielona do usługi Salesforce, ponieważ zostaną one dodane do różnych grup, ich przypisanie roli zostanie zaktualizowane w usłudze Salesforce. Użytkownicy będą mogli odnajdywać usługi Salesforce i uzyskiwać do nich dostęp za pomocą aplikacji, klientów sieci Web pakietu Office, a nawet przez przejście na stronę logowania do swojej organizacji. Administratorzy mogą łatwo wyświetlać stan użytkowania i przypisywania za pomocą funkcji raportowania usługi Azure AD.

Administratorzy mogą [korzystać z dostępu warunkowego usługi Azure AD](../conditional-access/concept-conditional-access-users-groups.md) w celu ustawiania zasad dostępu dla konkretnych ról. Te zasady mogą obejmować, czy dostęp jest dozwolony poza środowiskiem korporacyjnym, a nawet Multi-Factor Authentication lub wymagania dotyczące urządzeń, aby uzyskać dostęp w różnych przypadkach.

## <a name="access-to-microsoft-applications"></a>Dostęp do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innej firmy lub inne aplikacje, które integrują się z usługą Azure AD w celu logowania jednokrotnego.

Istnieją trzy główne metody uzyskiwania dostępu do aplikacji opublikowanej przez firmę Microsoft.

- W przypadku aplikacji w Microsoft 365 lub innych płatnych pakietów użytkownicy uzyskują dostęp za pośrednictwem **przypisania licencji** bezpośrednio do konta użytkownika lub za pośrednictwem grupy przy użyciu naszej możliwości przypisania licencji opartej na grupach.
- W przypadku aplikacji, które firma Microsoft lub inna osoba publikuje bezpłatnie dla wszystkich użytkowników, użytkownicy mogą uzyskać dostęp za pomocą [zgody użytkownika](configure-user-consent.md). Oznacza to, że logują się do aplikacji przy użyciu konta służbowego usługi Azure AD i zezwalają na dostęp do pewnego ograniczonego zestawu danych na swoim koncie.
- W przypadku aplikacji, które firma Microsoft lub inna osoba publikuje bezpłatnie dla wszystkich użytkowników, może również uzyskać dostęp za pomocą [zgody administratora](manage-consent-requests.md). Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich użytkowników w organizacji, aby zalogować się do aplikacji przy użyciu konta administratora globalnego i udzielić dostępu wszystkim osobom w organizacji.

Niektóre aplikacje łączą te metody. Na przykład niektóre aplikacje firmy Microsoft są częścią subskrypcji Microsoft 365, ale nadal wymagają zgody.

Użytkownicy mogą uzyskiwać dostęp do aplikacji Microsoft 365 za poorednictwem portali pakietu Office 365. Możesz również wyświetlać lub ukrywać Microsoft 365 aplikacje w obszarze Moje aplikacje ze znakiem [widoczności pakietu Office 365](hide-application-from-user-portal.md) w **ustawieniach użytkownika** w katalogu. 

Podobnie jak w przypadku aplikacji dla przedsiębiorstw, można [przypisać użytkowników](assign-user-or-group-access-portal.md) do określonych aplikacji firmy Microsoft za pośrednictwem Azure Portal lub, jeśli opcja portalu jest niedostępna, przy użyciu programu PowerShell.

## <a name="next-steps"></a>Następne kroki
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Samoobsługowe zarządzanie grupami/SSAA](../enterprise-users/groups-self-service-management.md)
