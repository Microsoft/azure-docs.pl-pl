---
title: Zarządzanie dostępem do aplikacji przy użyciu usługi Azure AD
description: Opisuje, Azure Active Directory umożliwia organizacjom określenie aplikacji, do których każdy użytkownik ma dostęp.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2017
ms.author: iangithinji
ms.openlocfilehash: 6a1ae7dd1da2c7666c007194bf22bd980e41dc22
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376379"
---
# <a name="managing-access-to-apps"></a>Zarządzanie dostępem do aplikacji

Ciągłe zarządzanie dostępem, ocena użycia i raportowanie nadal stanowi wyzwanie po zintegrowaniu aplikacji z systemem tożsamości organizacji. W wielu przypadkach administratorzy IT lub pomoc techniczna muszą odgrywać aktywną rolę w zarządzaniu dostępem do aplikacji. Czasami przypisanie jest wykonywane przez ogólny lub oddziałowy zespół IT. Często decyzja o przypisaniu jest przeznaczona do delegowania do osoby, która podejmuje decyzje biznesowe, wymagając ich zatwierdzenia przed podjęciem przypisania przez firmę IT.  Inne organizacje inwestują w integrację z istniejącym zautomatyzowanym systemem zarządzania tożsamościami i dostępem, na przykład Role-Based Access Control (RBAC) lub Attribute-Based Access Control (ABAC). Zarówno proces tworzenia integracji, jak i tworzenia reguł jest wyspecjalizowany i kosztowny. Monitorowanie lub raportowanie dla obu podejść do zarządzania to oddzielne, kosztowne i złożone inwestycje.

## <a name="how-does-azure-active-directory-help"></a>Jak Azure Active Directory pomoc?

Usługa Azure AD obsługuje rozbudowane zarządzanie dostępem dla skonfigurowanych aplikacji, dzięki czemu organizacje mogą łatwo osiągnąć odpowiednie zasady dostępu, od automatycznego przypisania opartego na atrybutach (scenariusze kontroli dostępu opartej na atrybutach) przez delegowanie i zarządzanie administratorami. Dzięki usłudze Azure AD można łatwo realizować złożone zasady, łącząc wiele modeli zarządzania dla jednej aplikacji, a nawet ponownie używać reguł zarządzania w aplikacjach z tym samymi odbiorcami.

Dzięki usłudze Azure AD raportowanie użycia i przypisania jest w pełni zintegrowane, co umożliwia administratorom łatwe raportowanie stanu przypisania, błędów przypisania, a nawet użycia.

### <a name="assigning-users-and-groups-to-an-app"></a>Przypisywanie użytkowników i grup do aplikacji

Przypisanie aplikacji usługi Azure AD koncentruje się na dwóch podstawowych trybach przypisania:

* **Przypisanie indywidualne** Administrator IT z uprawnieniami administratora globalnego katalogu może wybrać indywidualne konta użytkowników i udzielić im dostępu do aplikacji.

* **Przypisanie oparte na grupie (wymaga Azure AD — wersja Premium P1 lub P2)** Administrator IT z uprawnieniami administratora globalnego katalogu może przypisać grupę do aplikacji. Dostęp określonych użytkowników zależy od tego, czy są oni członkami grupy podczas próby uzyskania dostępu do aplikacji. Innymi słowy, administrator może skutecznie utworzyć regułę przypisania z informacją "każdy bieżący członek przypisanej grupy ma dostęp do aplikacji". Korzystając z tej opcji przypisania, administratorzy mogą korzystać [](../fundamentals/active-directory-groups-create-azure-portal.md)z dowolnej opcji zarządzania grupami usługi Azure AD, w tym grup dynamicznych opartych na atrybutach, zewnętrznych grup systemowych (na przykład lokalna usługa Active Directory lub Workday) albo grup zarządzanych przez administratora lub zarządzanych samodzielnie. Pojedynczą grupę można łatwo przypisać do wielu aplikacji, upewnijąc się, że aplikacje z koligacją przypisania mogą współużytkować reguły przypisywania, co zmniejsza ogólną złożoność zarządzania. Należy pamiętać, że zagnieżdżone członkostwa w grupach nie są obecnie obsługiwane w przypadku przypisywania do aplikacji na podstawie grupy.

Korzystając z tych dwóch trybów przypisania, administratorzy mogą osiągnąć dowolne pożądane podejście do zarządzania przypisaniami.

### <a name="requiring-user-assignment-for-an-app"></a>Wymaganie przypisania użytkownika dla aplikacji

W przypadku niektórych typów aplikacji można wymagać przypisania użytkowników [do aplikacji.](assign-user-or-group-access-portal.md#configure-an-application-to-require-user-assignment) W ten sposób uniemożliwisz wszystkim użytkownikom logowanie się z wyjątkiem tych użytkowników, którzy są jawnie przypisani do aplikacji. Następujące typy aplikacji obsługują tę opcję:

* Aplikacje skonfigurowane do federowego logowania jednokrotnego (SSO) z uwierzytelnianiem opartym na saml
* serwer proxy aplikacji aplikacji, które używają Azure Active Directory wstępnego uwierzytelniania
* Aplikacje oparte na platformie aplikacji usługi Azure AD, które korzystają z uwierzytelniania OAuth 2.0/OpenID Connect, gdy użytkownik lub administrator wyraził zgodę na aplikację. Niektóre aplikacje dla przedsiębiorstw zapewniają dodatkową kontrolę nad tym, kto może się logować.

Jeśli przypisanie użytkownika nie jest *wymagane,* użytkownicy nieprzypisani nie widzą aplikacji na swoich urządzeniach Moje aplikacje, ale nadal mogą zalogować się do samej aplikacji (znanej także  jako logowanie inicjowane przez spjęzyka) lub mogą użyć adresu **URL** dostępu użytkowników na stronie Właściwości aplikacji (znanej również jako logowanie inicjowane przez dostawcy tożsamości).

W przypadku niektórych aplikacji opcja wymagania przypisania użytkownika nie jest dostępna we właściwościach aplikacji. W takich przypadkach można użyć programu PowerShell, aby ustawić właściwość appRoleAssignmentRequired dla jednostki usługi.

### <a name="determining-the-user-experience-for-accessing-apps"></a>Określanie interfejsu użytkownika na temat uzyskiwania dostępu do aplikacji

Usługa Azure AD [oferuje kilka modyfikowalnych sposobów wdrażania aplikacji](end-user-experiences.md) dla użytkowników końcowych w organizacji:

* Azure AD Moje aplikacje
* Microsoft 365 uruchamiania aplikacji
* Bezpośrednie logowanie do aplikacji federowanych (service-pr)
* Linki bezpośrednie do federacyjnych, opartych na hasłach lub istniejących aplikacjach

Możesz określić, czy użytkownicy przypisani do aplikacji przedsiębiorstwa mogą ją zobaczyć w Moje aplikacje i Microsoft 365 uruchamiania aplikacji.

## <a name="example-complex-application-assignment-with-azure-ad"></a>Przykład: złożone przypisanie aplikacji za pomocą usługi Azure AD
Rozważmy aplikację, np. Salesforce. W wielu organizacjach usługi Salesforce są używane głównie przez zespoły marketingowe i sprzedażowe. Często członkowie zespołu marketingowego mają wysoce uprzywilejowany dostęp do usługi Salesforce, a członkowie zespołu sprzedaży mają ograniczony dostęp. W wielu przypadkach szeroka populacja pracowników pracujących z informacjami ma ograniczony dostęp do aplikacji. Wyjątki od tych reguł komplikują sprawy. Często prerogatywą zespołów liderów marketingu lub sprzedaży jest udzielenie użytkownikowi dostępu lub zmiana ich ról niezależnie od tych ogólnych reguł.

W usłudze Azure AD aplikacje, takie jak Salesforce, można wstępnie skonfigurować na podstawie logowania jednokrotnego (SSO) i automatycznego aprowizowania. Po skonfigurowaniu aplikacji administrator może podjąć jednorazową akcję w celu utworzenia i przypisania odpowiednich grup. W tym przykładzie administrator może wykonać następujące przypisania:

* [Grupy dynamiczne](../fundamentals/active-directory-groups-create-azure-portal.md) można zdefiniować tak, aby automatycznie reprezentowały wszystkich członków zespołów marketingowych i sprzedażowych przy użyciu atrybutów, takich jak dział lub rola:
  
  * Wszyscy członkowie grup marketingowych zostaną przypisani do roli "marketing" w u usługi Salesforce
  * Wszyscy członkowie grup zespołu sprzedaży zostaną przypisani do roli "sales" (sprzedaż) w układzie Salesforce. W kolejnym udoskonalaniu można użyć wielu grup reprezentujących regionalne zespoły sprzedaży przypisane do różnych ról usługi Salesforce.

* Aby włączyć mechanizm wyjątków, dla każdej roli można utworzyć grupę samoobsługi. Na przykład grupę "Wyjątek marketingowy usługi Salesforce" można utworzyć jako grupę samoobsługową. Grupę można przypisać do roli marketingowej usługi Salesforce, a zespół liderów marketingu może zostać właścicielem. Członkowie zespołu liderów marketingu mogą dodawać lub usuwać użytkowników, ustawiać zasady dołączania, a nawet zatwierdzać lub odrzucać żądania dołączenia poszczególnych użytkowników. Ten mechanizm jest obsługiwany za pośrednictwem odpowiedniego doświadczenia pracownika informacji, które nie wymaga wyspecjalizowanego szkolenia dla właścicieli lub członków.

W takim przypadku wszyscy przypisani użytkownicy zostaną automatycznie aprowizować w uciece Salesforce, ponieważ zostaną dodani do różnych grup, do których przypisanie roli zostanie zaktualizowane w uciecie Salesforce. Użytkownicy będą mogli odnajdywać usługę Salesforce i uzyskać do niej dostęp za pośrednictwem usług Moje aplikacje, klientów internetowych pakietu Office, a nawet przechodząc do strony logowania organizacji w usłudze Salesforce. Administratorzy mogą łatwo wyświetlać stan użycia i przypisania przy użyciu raportowania usługi Azure AD.

Administratorzy mogą używać [dostępu warunkowego usługi Azure AD](../conditional-access/concept-conditional-access-users-groups.md) do ustawienia zasad dostępu dla określonych ról. Te zasady mogą obejmować to, czy dostęp jest dozwolony poza środowiskiem firmowym, a nawet wymagania dotyczące usługi Multi-Factor Authentication lub urządzeń w celu uzyskania dostępu w różnych przypadkach.

## <a name="access-to-microsoft-applications"></a>Dostęp do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innych firm lub inne aplikacje integrowane z usługą Azure AD w celu logowania jednokrotnego.

Istnieją trzy główne sposoby uzyskiwania dostępu przez użytkownika do aplikacji opublikowanej przez firmę Microsoft.

- W przypadku aplikacji w Microsoft 365 lub innych płatnych pakietów  użytkownicy mają dostęp za pośrednictwem przypisania licencji bezpośrednio do konta użytkownika lub za pośrednictwem grupy przy użyciu naszej funkcji przypisywania licencji opartej na grupach.
- W przypadku aplikacji publikowanych bezpłatnie przez firmę Microsoft lub inne osoby użytkownicy mogą uzyskać dostęp za [pośrednictwem zgody użytkownika.](configure-user-consent.md) Oznacza to, że logują się do aplikacji przy użyciu konta służbowego usługi Azure AD i umożliwiają jej dostęp do pewnego ograniczonego zestawu danych na koncie.
- W przypadku aplikacji publikowanych bezpłatnie przez firmę Microsoft lub inne osoby użytkownicy mogą również uzyskać dostęp za pośrednictwem [zgody administratora.](manage-consent-requests.md) Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich użytkowników w organizacji, dlatego loguje się do aplikacji przy użyciu konta administratora globalnego i udziela dostępu wszystkim użytkownikom w organizacji.

Niektóre aplikacje łączą te metody. Na przykład niektóre aplikacje firmy Microsoft są częścią subskrypcji Microsoft 365, ale nadal wymagają zgody.

Użytkownicy mogą uzyskać dostęp Microsoft 365 aplikacji za pośrednictwem portali usługi Office 365. Możesz również wyświetlać lub ukrywać aplikacje Microsoft 365 aplikacji w usłudze Moje aplikacje za pomocą przełącznika Widoczność usługi [Office 365](hide-application-from-user-portal.md) w ustawieniach **użytkownika katalogu**. 

Podobnie jak w przypadku [](assign-user-or-group-access-portal.md) aplikacji dla przedsiębiorstw, możesz przypisywać użytkowników do niektórych aplikacji firmy Microsoft za pośrednictwem witryny Azure Portal lub, jeśli opcja portalu jest niedostępny, przy użyciu programu PowerShell.

## <a name="next-steps"></a>Następne kroki
* [Ochrona aplikacji przy użyciu dostępu warunkowego](../conditional-access/concept-conditional-access-cloud-apps.md)
* [Samoobsługowe zarządzanie grupą/SSAA](../enterprise-users/groups-self-service-management.md)
