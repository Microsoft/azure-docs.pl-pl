---
title: 'Azure AD Connect: obsługiwane topologie | Microsoft Docs'
description: W tym temacie opisano obsługiwane i nieobsługiwane topologie dla Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 1034c000-59f2-4fc8-8137-2416fa5e4bfe
ms.service: active-directory
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.topic: conceptual
ms.date: 11/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d3f8e9441064a5d2d1372e3f177534b8dfefb93
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92359836"
---
# <a name="topologies-for-azure-ad-connect"></a>Topologie obsługiwane w programie Azure AD Connect
W tym artykule opisano różne topologie lokalne i Azure Active Directory (Azure AD), które używają Azure AD Connect synchronizacji jako rozwiązania integracji z kluczami. Ten artykuł zawiera obsługiwane i nieobsługiwane konfiguracje.


Oto legenda obrazów w artykule:

| Opis | Symbol |
| --- | --- |
| Las Active Directory lokalnego |![Las Active Directory lokalnego](./media/plan-connect-topologies/legendad1.png) |
| Active Directory lokalnego z filtrowanym zaimportowaniem |![Active Directory z filtrowanym importem](./media/plan-connect-topologies/legendad2.png) |
| Serwer synchronizacji Azure AD Connect |![Serwer synchronizacji Azure AD Connect](./media/plan-connect-topologies/legendsync1.png) |
| Serwer synchronizacji Azure AD Connect "tryb przejściowy" |![Serwer synchronizacji Azure AD Connect "tryb przejściowy"](./media/plan-connect-topologies/legendsync2.png) |
| GALSync z programem Forefront Identity Manager (FIM) 2010 lub Microsoft Identity Manager (MIM) 2016 |![GALSync z programem FIM 2010 lub MIM 2016](./media/plan-connect-topologies/legendsync3.png) |
| Serwer synchronizacji Azure AD Connect, szczegółowy |![Serwer synchronizacji Azure AD Connect, szczegółowy](./media/plan-connect-topologies/legendsync4.png) |
| Azure AD |![Azure Active Directory](./media/plan-connect-topologies/legendaad.png) |
| Nieobsługiwany scenariusz |![Nieobsługiwany scenariusz](./media/plan-connect-topologies/legendunsupported.png) |


> [!IMPORTANT]
> Firma Microsoft nie obsługuje modyfikowania ani działania Azure AD Connect synchronizacji poza konfiguracją lub akcjami, które są formalnie udokumentowane. Wszystkie te konfiguracje i akcje mogą spowodować niespójny lub nieobsługiwany stan Azure AD Connect synchronizacji. W związku z tym firma Microsoft nie może zapewnić pomocy technicznej dotyczącej takich wdrożeń.


## <a name="single-forest-single-azure-ad-tenant"></a>Pojedynczy las, pojedyncza dzierżawa usługi Azure AD
![Topologia jednego lasu i pojedynczej dzierżawy](./media/plan-connect-topologies/singleforestsingledirectory.png)

Najbardziej typową topologią jest pojedynczy las lokalny, z co najmniej jedną domeną i jedną dzierżawą usługi Azure AD. W przypadku uwierzytelniania za pomocą usługi Azure AD jest używana synchronizacja skrótów haseł. Instalacja ekspresowa Azure AD Connect obsługuje tylko tę topologię.

### <a name="single-forest-multiple-sync-servers-to-one-azure-ad-tenant"></a>Pojedynczy las, wiele serwerów synchronizacji z jedną dzierżawą usługi Azure AD
![Nieobsługiwana, odfiltrowana topologia dla pojedynczego lasu](./media/plan-connect-topologies/singleforestfilteredunsupported.png)

Posiadanie wielu Azure AD Connect serwerów synchronizacji połączonych z tą samą dzierżawą usługi Azure AD nie jest obsługiwane, z wyjątkiem [serwera przejściowego](#staging-server). Jest ona nieobsługiwana, nawet jeśli te serwery są skonfigurowane do synchronizacji z wzajemnie wykluczające się zestaw obiektów. Ta topologia może zostać uznana za niedostępną dla wszystkich domen w lesie z jednego serwera lub jeśli chcesz rozpowszechnić obciążenie na kilku serwerach.

## <a name="multiple-forests-single-azure-ad-tenant"></a>Wiele lasów, pojedyncza dzierżawa usługi Azure AD
![Topologia dla wielu lasów i pojedynczej dzierżawy](./media/plan-connect-topologies/multiforestsingledirectory.png)

Wiele organizacji ma środowiska z wieloma lokalnymi Active Directory lasów. Istnieją różne przyczyny istnienia więcej niż jednego lasu lokalnego Active Directory. Typowymi przykładami są projekty z lasami zasobów konta oraz wynik fuzji lub pozyskiwania.

W przypadku wielu lasów wszystkie lasy muszą być dostępne za pomocą jednego serwera synchronizacji Azure AD Connect. Serwer musi być przyłączony do domeny. W razie potrzeby w celu uzyskania dostępu do wszystkich lasów można umieścić serwer w sieci obwodowej (znanej także jako Strefa DMZ, strefy zdemilitaryzowana i podsieć z osłoną).

Kreator instalacji Azure AD Connect oferuje kilka opcji konsolidacji użytkowników, którzy są reprezentowani w wielu lasach. Celem jest to, że użytkownik jest reprezentowany tylko raz w usłudze Azure AD. Istnieją pewne popularne topologie, które można skonfigurować w niestandardowej ścieżce instalacji w Kreatorze instalacji. Na stronie **jednoznacznie identyfikującej użytkowników** wybierz odpowiednią opcję, która reprezentuje topologię. Konsolidacja jest konfigurowana tylko dla użytkowników. Zduplikowane grupy nie są skonsolidowane z konfiguracją domyślną.

Popularne topologie zostały omówione w sekcjach dotyczących oddzielnych topologii, [pełnej siatki](#multiple-forests-full-mesh-with-optional-galsync)i [topologii zasobów konta](#multiple-forests-account-resource-forest).

Domyślną konfiguracją w Azure AD Connect synchronizacji założono:

* Każdy użytkownik ma tylko jedno włączone konto, a Las, w którym znajduje się to konto, jest używany do uwierzytelniania użytkownika. To założenie dotyczy synchronizacji skrótów haseł, uwierzytelniania przekazywanego i Federacji. UserPrincipalName i sourceAnchor/immutableID pochodzą z tego lasu.
* Każdy użytkownik ma tylko jedną skrzynkę pocztową.
* Las, który hostuje skrzynkę pocztową dla użytkownika, ma najlepszą jakość danych dla atrybutów widocznych na globalnej liście adresowej programu Exchange. Jeśli dla użytkownika nie ma skrzynki pocztowej, do współtworzenia tych wartości atrybutów można użyć dowolnego lasu.
* Jeśli masz połączoną skrzynkę pocztową, istnieje również konto w innym lesie używanym do logowania.

Jeśli środowisko nie jest zgodne z tymi założeniami, wykonywane są następujące czynności:

* Jeśli masz więcej niż jedno aktywne konto lub więcej niż jedna Skrzynka pocztowa, aparat synchronizacji wybiera jeden z nich i zignoruje go.
* Połączona Skrzynka pocztowa bez innych aktywnych kont nie została wyeksportowana do usługi Azure AD. Konto użytkownika nie jest reprezentowane jako członek w żadnej grupie. Połączona Skrzynka pocztowa w narzędziu DirSync jest zawsze reprezentowana jako zwykła Skrzynka pocztowa. Ta zmiana jest celowo innym zachowaniem, aby zapewnić lepszą obsługę scenariuszy obejmujących wiele lasów.

Więcej szczegółów można znaleźć w temacie [Omówienie konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md).

### <a name="multiple-forests-multiple-sync-servers-to-one-azure-ad-tenant"></a>Wiele lasów, wiele serwerów synchronizacji z jedną dzierżawą usługi Azure AD
![Nieobsługiwana topologia dla wielu lasów i wielu serwerów synchronizacji](./media/plan-connect-topologies/multiforestmultisyncunsupported.png)

Posiadanie więcej niż jednego serwera synchronizacji Azure AD Connect połączonego z jedną dzierżawą usługi Azure AD nie jest obsługiwane. Wyjątkiem jest użycie [serwera przejściowego](#staging-server).

Ta topologia różni się od przedstawionej poniżej w przypadku, gdy **wiele serwerów synchronizacji** podłączonych do pojedynczej dzierżawy usługi Azure AD nie jest obsługiwana.

### <a name="multiple-forests-single-sync-server-users-are-represented-in-only-one-directory"></a>Wiele lasów, serwer pojedynczego synchronizacji, użytkownicy są reprezentowani tylko w jednym katalogu
![Opcja reprezentowania użytkowników tylko raz dla wszystkich katalogów](./media/plan-connect-topologies/multiforestusersonce.png)

![Przedstawianie wielu lasów i oddzielnych topologii](./media/plan-connect-topologies/multiforestseparatetopologies.png)

W tym środowisku wszystkie lasy lokalne są traktowane jako osobne jednostki. Żaden użytkownik nie jest obecny w żadnym innym lesie. Każdy Las ma własną organizację programu Exchange i nie ma żadnych GALSync między lasami. Ta topologia może być sytuacje po operacji łączenia/pozyskiwania lub w organizacji, w której każda jednostka biznesowa działa niezależnie. Te lasy znajdują się w tej samej organizacji w usłudze Azure AD i są wyświetlane z ujednoliconą opcją. Na powyższym rysunku każdy obiekt w każdym lesie jest reprezentowany raz w magazynie Metaverse i agregowany w docelowej dzierżawie usługi Azure AD.

### <a name="multiple-forests-match-users"></a>Wiele lasów: dopasowanie do użytkowników
W przypadku wszystkich tych scenariuszy grupy dystrybucji i zabezpieczeń mogą zawierać kombinację użytkowników, kontaktów i obcych podmiotów zabezpieczeń (FSPs). FSPs są używane w Active Directory Domain Services (AD DS) do reprezentowania członków z innych lasów w grupie zabezpieczeń. Wszystkie FSPs są rozwiązywane do rzeczywistego obiektu w usłudze Azure AD.

### <a name="multiple-forests-full-mesh-with-optional-galsync"></a>Wiele lasów: pełna siatka z opcjonalną GALSync
![Opcja użycia atrybutu mail do dopasowania, gdy tożsamości użytkowników istnieją w wielu katalogach](./media/plan-connect-topologies/multiforestusersmail.png)

![Topologia pełnej sieci dla wielu lasów](./media/plan-connect-topologies/multiforestfullmesh.png)

Topologia pełnej sieci umożliwia użytkownikom i zasobom zlokalizowanie się w dowolnym lesie. Często istnieją dwukierunkowe relacje zaufania między lasami.

Jeśli program Exchange jest obecny w więcej niż jednym lesie, może to być (opcjonalnie) lokalne rozwiązanie GALSync. Każdy użytkownik jest następnie reprezentowany jako kontakt we wszystkich innych lasach. GALSync jest zwykle implementowana za poorednictwem programu FIM 2010 lub MIM 2016. Nie można użyć Azure AD Connect dla lokalnego GALSync.

W tym scenariuszu obiekty tożsamości są sprzężone za pośrednictwem atrybutu mail. Użytkownik, który ma skrzynkę pocztową w jednym lesie, jest przyłączony do kontaktów w innych lasach.

### <a name="multiple-forests-account-resource-forest"></a>Wiele lasów: Las zasobów konta
![Opcja używania atrybutów ObjectSID i msExchMasterAccountSID do dopasowania, gdy tożsamości istnieją w wielu katalogach](./media/plan-connect-topologies/multiforestusersobjectsid.png)

![Topologia lasu zasobów konta dla wielu lasów](./media/plan-connect-topologies/multiforestaccountresource.png)

W topologii lasu zasobów kont istnieje co najmniej jeden las *konta* z aktywnymi kontami użytkowników. Istnieje również jeden lub więcej lasów *zasobów* z wyłączonymi kontami.

W tym scenariuszu jeden (lub więcej) lasu zasobów ufa wszystkich lasów kont. Las zasobów zwykle ma rozszerzony schemat Active Directory z programem Exchange i Lync. Wszystkie usługi Exchange i Lync oraz inne usługi udostępnione znajdują się w tym lesie. Użytkownicy mają wyłączone konto użytkownika w tym lesie, a skrzynka pocztowa jest połączona z lasem konta.

## <a name="microsoft-365-and-topology-considerations"></a>Zagadnienia dotyczące Microsoft 365 i topologii
Niektóre Microsoft 365 obciążenia mają pewne ograniczenia dotyczące obsługiwanych topologii:

| Obciążenie | Ograniczenia |
| --------- | --------- |
| Exchange Online | Aby uzyskać więcej informacji o topologii hybrydowej obsługiwanej przez usługi Exchange Online, zobacz [wdrażanie hybrydowe z wieloma lasami Active Directory](/Exchange/hybrid-deployment/hybrid-with-multiple-forests). |
| Skype dla firm | W przypadku korzystania z wielu lasów lokalnych obsługiwana jest tylko topologia lasu zasobów konta. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące środowiska dla programu Skype dla firm Server 2015](/skypeforbusiness/plan-your-deployment/requirements-for-your-environment/environmental-requirements). |

Jeśli jesteś większą organizacją, należy rozważyć użycie funkcji [Microsoft 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) . Umożliwia zdefiniowanie w tym regionie centrum danych, w którym znajdują się zasoby użytkownika.

## <a name="staging-server"></a>Serwer przemieszczania
![Serwer przejściowy w topologii](./media/plan-connect-topologies/multiforeststaging.png)

Azure AD Connect obsługuje instalowanie drugiego serwera w *trybie przejściowym*. Serwer w tym trybie odczytuje dane ze wszystkich podłączonych katalogów, ale nie zapisuje niczego do podłączonych katalogów. Używa normalnego cyklu synchronizacji i w związku z tym ma zaktualizowaną kopię danych tożsamości.

W przypadku awarii, w której serwer podstawowy ulegnie awarii, można przejść do trybu failover na serwerze tymczasowym. W tym celu należy wykonać czynności w Kreatorze Azure AD Connect. Ten drugi serwer może znajdować się w innym centrum danych, ponieważ żadna infrastruktura nie jest współdzielona z serwerem podstawowym. Należy ręcznie skopiować wszystkie zmiany konfiguracji wprowadzone na serwerze podstawowym na drugi serwer.

Serwer przejściowy może służyć do testowania nowej konfiguracji niestandardowej i wpływu na dane. Możesz wyświetlić podgląd zmian i dostosować konfigurację. Gdy będziesz zadowolony z nowej konfiguracji, możesz ustawić serwer przejściowy jako aktywny serwer i ustawić stary aktywny serwer na tryb przejściowy.

Za pomocą tej metody można również zastąpić aktywny serwer synchronizacji. Przygotuj nowy serwer i ustaw go na tryb przejściowy. Upewnij się, że jest w dobrym stanie, wyłącz tryb przejściowy (Uaktywnij go) i Zamknij obecnie aktywny serwer.

Możliwe jest posiadanie więcej niż jednego serwera przejściowego, jeśli chcesz mieć wiele kopii zapasowych w różnych centrach danych.

## <a name="multiple-azure-ad-tenants"></a>Wiele dzierżawców usługi Azure AD
Zalecamy korzystanie z jednej dzierżawy w usłudze Azure AD dla organizacji.
Przed zaplanowaniem korzystania z wielu dzierżaw usługi Azure AD zapoznaj się z artykułem [Zarządzanie jednostkami administracyjnymi w usłudze Azure AD](../roles/administrative-units.md). Obejmuje to typowe scenariusze, w których można korzystać z jednej dzierżawy.

![Topologia dla wielu lasów i wielu dzierżawców](./media/plan-connect-topologies/multiforestmultidirectory.png)

Istnieje relacja 1:1 między serwerem synchronizacji Azure AD Connect i dzierżawą usługi Azure AD. Dla każdej dzierżawy usługi Azure AD potrzebna jest jedna Azure AD Connect instalacja serwera synchronizacji. Wystąpienia dzierżawy usługi Azure AD są izolowane według konstrukcji. Oznacza to, że użytkownicy w jednej dzierżawie nie widzą użytkowników w innej dzierżawie. W przypadku wybrania tej separacji jest to obsługiwana konfiguracja. W przeciwnym razie należy użyć jednego modelu dzierżawy usługi Azure AD.

### <a name="each-object-only-once-in-an-azure-ad-tenant"></a>Każdy obiekt tylko raz w dzierżawie usługi Azure AD
![Topologia filtrowana dla pojedynczego lasu](./media/plan-connect-topologies/singleforestfiltered.png)

W tej topologii jeden serwer synchronizacji Azure AD Connect jest połączony z każdą dzierżawą usługi Azure AD. Serwery synchronizacji Azure AD Connect muszą być skonfigurowane do filtrowania, tak aby każdy z nich miał wzajemnie wykluczające się zestaw obiektów do działania. Można na przykład ograniczyć zakres każdego serwera do konkretnej domeny lub jednostki organizacyjnej.

Domenę DNS można zarejestrować tylko w jednej dzierżawie usługi Azure AD. Nazwy UPN użytkowników w lokalnym wystąpieniu Active Directory muszą również używać oddzielnych przestrzeni nazw. Na przykład na powyższym rysunku trzy oddzielne sufiksy nazw UPN są rejestrowane w lokalnym wystąpieniu Active Directory: contoso.com, fabrikam.com i wingtiptoys.com. Użytkownicy w każdej lokalnej domenie Active Directory używają innej przestrzeni nazw.

>[!NOTE]
>Globalna Synchronizacja listy adresów (GalSync) nie jest wykonywana automatycznie w tej topologii i wymaga dodatkowej niestandardowej implementacji programu MIM, aby upewnić się, że każda dzierżawa ma kompletną globalną listę adresów (w usłudze Exchange Online i usługa Skype dla firm Online).


Ta topologia ma następujące ograniczenia dotyczące scenariuszy obsługiwanych w inny sposób:

* Maksymalnie 5 dzierżawców Azure Active Directory może mieć program Exchange hybrydowy z wystąpieniem Active Directory lokalnym. Ten scenariusz jest opisany we [wrześniu 2020 Kreatora konfiguracji hybrydowej](https://techcommunity.microsoft.com/t5/exchange-team-blog/september-2020-hybrid-configuration-wizard-update/ba-p/1687698).
* Kreator konfiguracji hybrydowej programu Exchange Server może mieć wartość 2016 CU18 lub 2019 CU7 lub nowszą.
* Każde wystąpienie Azure AD Connect powinno być uruchomione na komputerze przyłączonym do domeny.
* Azure AD Connect należy skonfigurować przy użyciu opcji filtrowania domeny/jednostki organizacyjnej, aby filtrować użytkowników z katalogu lokalnego. Użycie tej opcji zapewnia, że użytkownicy będą pojawiały się tylko w jednej dzierżawie usługi Exchange Online.
* Urządzenia z systemem Windows 10 mogą być skojarzone tylko z jedną dzierżawą usługi Azure AD.
* Opcja logowania jednokrotnego (SSO) dla synchronizacji skrótów haseł i uwierzytelniania przekazywanego może być używana tylko z jedną dzierżawą usługi Azure AD.

Wymóg dla wzajemnie wykluczających się zestawów obiektów ma zastosowanie również do zapisywania zwrotnego. Niektóre funkcje zapisywania zwrotnego nie są obsługiwane w przypadku tej topologii, ponieważ zakładają one jedną konfigurację lokalną. Są one następujące:

* Grupowanie zapisywania zwrotnego z domyślną konfiguracją.
* Zapisywanie zwrotne urządzeń.

### <a name="each-object-multiple-times-in-an-azure-ad-tenant"></a>Każdy obiekt wielokrotnie w dzierżawie usługi Azure AD
![Nieobsługiwana topologia dla jednego lasu i wielu dzierżawców](./media/plan-connect-topologies/singleforestmultidirectoryunsupported.png) ![Nieobsługiwana topologia dla jednego lasu i wielu łączników](./media/plan-connect-topologies/singleforestmulticonnectorsunsupported.png)

Te zadania nie są obsługiwane:

* Zsynchronizuj tego samego użytkownika z wieloma dzierżawami usługi Azure AD.
* Wprowadź zmianę konfiguracji, aby użytkownicy w jednej dzierżawie usługi Azure AD wyświetlali jako kontakty w innej dzierżawie usługi Azure AD.
* Zmodyfikuj synchronizację Azure AD Connect, aby nawiązać połączenie z wieloma dzierżawami usługi Azure AD.

### <a name="galsync-by-using-writeback"></a>GALSync przy użyciu funkcji zapisywania zwrotnego
![Nieobsługiwana topologia dla wielu lasów i wielu katalogów z GALSync koncentruje się na usłudze Azure AD](./media/plan-connect-topologies/multiforestmultidirectorygalsync1unsupported.png) ![Nieobsługiwana topologia dla wielu lasów i wielu katalogów z GALSync skoncentrowanym na Active Directory lokalnym](./media/plan-connect-topologies/multiforestmultidirectorygalsync2unsupported.png)

Dzierżawy usługi Azure AD są izolowane według konstrukcji. Te zadania nie są obsługiwane:

* Zmień konfigurację Azure AD Connect synchronizacji, aby odczytywać dane z innej dzierżawy usługi Azure AD.
* Eksportuj użytkowników jako kontakty do innego lokalnego wystąpienia Active Directory za pomocą funkcji Azure AD Connect Sync.

### <a name="galsync-with-on-premises-sync-server"></a>GALSync z lokalnym serwerem synchronizacji
![GALSync w topologii dla wielu lasów i wielu katalogów](./media/plan-connect-topologies/multiforestmultidirectorygalsync.png)

Aby synchronizować użytkowników (za pośrednictwem GALSync) między dwiema organizacjami programu Exchange, można użyć programu FIM 2010 lub MIM 2016. Użytkownicy w jednej organizacji są wyświetlani jako Użytkownicy zagraniczni/kontakty w innej organizacji. Te różne wystąpienia Active Directory lokalnego można następnie synchronizować z własnymi dzierżawami usługi Azure AD.

### <a name="using-unauthorized-clients-to-access-the-azure-ad-connect-backend"></a>Korzystanie z nieautoryzowanych klientów w celu uzyskania dostępu do Azure AD Connect zaplecza
![Korzystanie z nieautoryzowanych klientów w celu uzyskania dostępu do Azure AD Connect zaplecza](./media/plan-connect-topologies/other-client-unsupported.png)

Serwer Azure Active Directory Connect komunikuje się z Azure Active Directory za pomocą zaplecza Azure Active Directory Connect. Jedyne oprogramowanie, które może być używane do komunikacji z tym zapleczem, jest Azure Active Directory Connect. Komunikacja z zaplecem Azure Active Directory Connect przy użyciu innego oprogramowania lub metody nie jest obsługiwana. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się, jak zainstalować Azure AD Connect w tych scenariuszach, zobacz [Instalacja Niestandardowa Azure AD Connect](how-to-connect-install-custom.md).

Dowiedz się więcej o konfiguracji [synchronizacji Azure AD Connect](how-to-connect-sync-whatis.md) .

Dowiedz się więcej [na temat integrowania tożsamości lokalnych z Azure Active Directory](whatis-hybrid-identity.md).
