---
title: Dodawanie połączonej organizacji w usłudze Azure AD entitlement management — Azure Active Directory
description: Dowiedz się, jak umożliwić osobom spoza organizacji żądanie pakietów dostępu, aby można było współpracować nad projektami.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/11/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b4e4bccde07d078c9749ee76c1653e6d431e63
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532076"
---
# <a name="add-a-connected-organization-in-azure-ad-entitlement-management"></a>Dodawanie połączonej organizacji w usłudze Azure AD entitlement management

Dzięki Azure Active Directory (Azure AD) możesz współpracować z osobami spoza organizacji. Jeśli często współpracujesz z użytkownikami w zewnętrznym katalogu lub domenie usługi Azure AD, możesz dodać ich jako połączną organizację. W tym artykule opisano sposób dodawania połączonej organizacji, aby umożliwić użytkownikom spoza organizacji żądanie zasobów w katalogu.

## <a name="what-is-a-connected-organization"></a>Co to jest połączona organizacja?

Połączona organizacja to inna organizacja, z która masz relację.  Aby użytkownicy w tej organizacji mogli uzyskać dostęp do twoich zasobów, takich jak witryny lub aplikacje usługi SharePoint Online, potrzebujesz reprezentacji użytkowników tej organizacji w tym katalogu.  Ponieważ w większości przypadków użytkownicy w tej organizacji nie są jeszcze w katalogu usługi Azure AD, możesz użyć zarządzania uprawnieniami, aby w razie potrzeby wprowadzić ich do katalogu usługi Azure AD.  

Istnieją trzy sposoby zarządzania uprawnieniami, które umożliwiają określenie użytkowników tworzących połączną organizację.  Może to być

* użytkowników w innym katalogu usługi Azure AD,
* użytkowników w innym katalogu spoza usługi Azure AD, który został skonfigurowany do federacji bezpośredniej, lub
* użytkownicy w innym katalogu spoza usługi Azure AD, których adresy e-mail mają wspólną nazwę domeny.

Załóżmy na przykład, że pracujesz w banku Woodgrove Bank i chcesz współpracować z dwiema organizacjami zewnętrznymi. Te dwie organizacje mają różne konfiguracje:

- Projekt graficzny Institute korzysta z usługi Azure AD, a użytkownicy mają główną nazwę użytkownika, która kończy się *graphicdesigninstitute.com*.
- Firma Contoso nie korzysta jeszcze z usługi Azure AD. Użytkownicy firmy Contoso mają główną nazwę użytkownika, która kończy się na *contoso.com*.

W takim przypadku można skonfigurować dwie połączone organizacje. Tworzysz jedną połączeniową organizację dla Instytutu projektowania grafiki i jedną dla firmy Contoso. Jeśli następnie dodasz dwie połączone organizacje do zasad, użytkownicy z każdej organizacji o głównej nazwie użytkownika, która jest taka sama jak zasady, mogą żądać pakietów dostępu. Użytkownicy z główną nazwą użytkownika, która ma domenę contoso.com będą odpowiadać organizacji połączonej z contoso i mogą również żądać pakietów. Użytkownicy z główną nazwą użytkownika, która ma domenę *graphicdesigninstitute.com* będą odpowiadać organizacji połączonej z instytucją Graphic Design i mogą przesyłać żądania. Ponieważ projekt graficzny Institute korzysta z usługi Azure AD, [](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) każdy użytkownik o głównej nazwie, która jest taka sama jak zweryfikowana domena dodana do dzierżawy, taki jak *graphicdesigninstitute.example,* będzie również mógł zażądać pakietów dostępu przy użyciu tych samych zasad. Jeśli masz włączone uwierzytelnianie za pomocą jednorazowego kodu dostępu [(OTP, one-time passcode)](../external-identities/one-time-passcode.md) wiadomości e-mail, obejmuje to użytkowników z tych domen, którzy nie mają jeszcze kont usługi Azure AD, którzy będą uwierzytelniani przy użyciu poczty e-mail OTP podczas uzyskiwania dostępu do zasobów. 

![Przykład połączonej organizacji](./media/entitlement-management-organization/connected-organization-example.png)

Sposób uwierzytelniania użytkowników z katalogu lub domeny usługi Azure AD zależy od typu uwierzytelniania. Typy uwierzytelniania dla połączonych organizacji to:

- Azure AD
- [Federacja bezpośrednia](../external-identities/direct-federation.md)
- [Kod dostępu (domena)](../external-identities/one-time-passcode.md)

Aby obejrzeć pokaz sposobu dodawania połączonej organizacji, obejrzyj następujące wideo:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4dskS]

## <a name="add-a-connected-organization"></a>Dodawanie połączonej organizacji

Aby dodać zewnętrzny katalog lub domenę usługi Azure AD jako połączną organizację, postępuj zgodnie z instrukcjami w tej sekcji.

**Rola wymagań wstępnych:** *administrator globalny* *lub administrator użytkowników*

1. W Azure Portal wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Nadzór nad tożsamościami.**

1. W okienku po lewej stronie wybierz **pozycję Połączone organizacje,** a następnie wybierz **pozycję Dodaj połączną organizację.**

    ![Przycisk "Dodaj połączną organizację"](./media/entitlement-management-organization/connected-organization.png)

1. Wybierz **kartę Podstawowe,** a następnie wprowadź nazwę wyświetlaną i opis organizacji.

    ![Okienko "Dodawanie połączonej organizacji" — podstawy](./media/entitlement-management-organization/organization-basics.png)

1. Stan zostanie automatycznie ustawiony na **Skonfigurowano** podczas tworzenia nowej połączonej organizacji. Aby uzyskać więcej informacji na temat właściwości stanu, zobacz [State properties of connected organizations (Właściwości stanu połączonych organizacji).](#state-properties-of-connected-organizations)

1. Wybierz **kartę Katalog i domena,** a następnie wybierz **pozycję Dodaj katalog i domenę.**

    Zostanie **otwarte okienko Wybieranie katalogów** i domen.

1. W polu wyszukiwania wprowadź nazwę domeny, aby wyszukać katalog lub domenę usługi Azure AD. Pamiętaj, aby wprowadzić całą nazwę domeny.

1. Sprawdź, czy nazwa organizacji i typ uwierzytelniania są poprawne. Sposób logowania użytkowników zależy od typu uwierzytelniania.

    ![Okienko "Wybieranie katalogów i domen"](./media/entitlement-management-organization/organization-select-directories-domains.png)

1. Wybierz **pozycję Dodaj,** aby dodać katalog lub domenę usługi Azure AD. Obecnie można dodać tylko jeden katalog lub domenę usługi Azure AD dla podłączonej organizacji.

    > [!NOTE]
    > Wszyscy użytkownicy z katalogu lub domeny usługi Azure AD będą mogli zażądać tego pakietu dostępu. Dotyczy to również użytkowników w usłudze Azure AD ze wszystkich domen podrzędnych skojarzonych z katalogiem, chyba że te domeny są blokowane przez listę zezwalania lub odmowy usługi Azure AD business to business (B2B). Aby uzyskać więcej informacji, zobacz Zezwalanie na zaproszenia dla użytkowników [B2B](../external-identities/allow-deny-list.md)z określonych organizacji lub blokowanie ich.

1. Po dodaniu katalogu lub domeny usługi Azure AD wybierz pozycję **Wybierz**.

    Organizacja zostanie wyświetlona na liście.

    ![Okienko "Katalog i domena"](./media/entitlement-management-organization/organization-directory-domain.png)

1. Wybierz **kartę Sponsorzy,** a następnie dodaj opcjonalnych sponsorów dla tej połączonej organizacji.

    Sponsorzy to użytkownicy wewnętrzni lub zewnętrzni, którzy znajdują się już w Twoim katalogu i są punktem kontaktu dla relacji z tą połączną organizacją. Sponsorzy wewnętrzni są użytkownikami członkowskimi w Twoim katalogu. Zewnętrzni sponsorzy to użytkownicy-goście z połączonej organizacji, którzy zostali wcześniej zaproszeni i znajdują się już w Twoim katalogu. Sponsorzy mogą być wykorzystani jako osoby zatwierdzające, gdy użytkownicy w tej połączonej organizacji zażądają dostępu do tego pakietu dostępu. Aby uzyskać informacje na temat zapraszania użytkownika-gościa do katalogu, zobacz Azure Active Directory użytkowników współpracy [B2B](../external-identities/add-users-administrator.md)w Azure Portal .

    Po wybraniu opcji **Dodaj/Usuń** zostanie otwarte okienko, w którym można wybrać sponsorów wewnętrznych lub zewnętrznych. W okienku zostanie wyświetlona niefiltrowana lista użytkowników i grup w katalogu.

    ![Okienko Sponsorzy](./media/entitlement-management-organization/organization-sponsors.png)

1. Wybierz **kartę Przeglądanie + tworzenie,** przejrzyj ustawienia organizacji, a następnie wybierz pozycję **Utwórz.**

    ![Okienko "Przeglądanie + tworzenie"](./media/entitlement-management-organization/organization-review-create.png)

## <a name="update-a-connected-organization"></a>Aktualizowanie połączonej organizacji 

Jeśli połączona organizacja zmieni się na inną domenę, nazwa organizacji zmieni się lub chcesz zmienić sponsorów, możesz zaktualizować podłączona organizację, zgodnie z instrukcjami w tej sekcji.

**Rola wymagań wstępnych:** *administrator globalny* *lub administrator użytkowników*

1. W Azure Portal wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Nadzór nad tożsamościami.**

1. W okienku po lewej stronie wybierz **pozycję Połączone organizacje,** a następnie wybierz połączną organizację, aby ją otworzyć.

1. W okienku przeglądu połączonej organizacji wybierz **pozycję** Edytuj, aby zmienić nazwę, opis lub stan organizacji.  

1. W **okienku Katalog i** domena wybierz pozycję Aktualizuj **katalog i domenę,** aby zmienić katalog lub domenę.

1. W **okienku Sponsorzy** wybierz pozycję **Dodaj sponsorów wewnętrznych** lub Dodaj **sponsorów zewnętrznych,** aby dodać użytkownika jako sponsora. Aby usunąć sponsora, wybierz sponsora, a następnie w okienku po prawej stronie wybierz pozycję **Usuń**.


## <a name="delete-a-connected-organization"></a>Usuwanie połączonej organizacji

Jeśli nie masz już relacji z zewnętrznym katalogiem lub domeną usługi Azure AD, możesz usunąć połączną organizację.

**Rola wymagań wstępnych:** *administrator globalny* *lub administrator użytkowników*

1. W Azure Portal wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Nadzór nad tożsamościami.**

1. W okienku po lewej stronie wybierz **pozycję Połączone organizacje,** a następnie wybierz połączną organizację, aby ją otworzyć.

1. W okienku przeglądu połączonej organizacji wybierz pozycję **Usuń,** aby je usunąć.

    ![Przycisk Usuń połączonej organizacji](./media/entitlement-management-organization/organization-delete.png)

## <a name="managing-a-connected-organization-programmatically"></a>Programowe zarządzanie połączeniową organizacją

Możesz również tworzyć, tworzyć, aktualizować i usuwać połączone organizacje przy użyciu Microsoft Graph. Użytkownik w odpowiedniej roli z aplikacją, która ma delegowane uprawnienia, może wywołać interfejs API, aby zarządzać `EntitlementManagement.ReadWrite.All` [obiektami connectedOrganization](/graph/api/resources/connectedorganization?view=graph-rest-beta&preserve-view=true) i ustawiać dla nich sponsorów.

## <a name="state-properties-of-connected-organizations"></a>Właściwości stanu połączonych organizacji

Obecnie istnieją dwa różne typy właściwości stanu dla połączonych organizacji w zarządzaniu uprawnieniami w usłudze Azure AD: skonfigurowane i proponowane: 

- Skonfigurowana połączona organizacja to w pełni funkcjonalna połączona organizacja, która umożliwia użytkownikom w tej organizacji dostęp do pakietów dostępu. Gdy administrator utworzy nową połączeniową organizację w Azure Portal,  będzie ona domyślnie w stanie skonfigurowanym od czasu utworzenia przez administratora i chce korzystać z tej połączonej organizacji. Ponadto podczas programowego tworzenia połączonej organizacji za pośrednictwem interfejsu  API należy skonfigurować stan domyślny, chyba że jawnie ustawisz inny stan. 

    Skonfigurowane połączone organizacje będą wyświetlane w s wyboru dla połączonych organizacji i będą w zakresie dla wszystkich zasad, które są kierowane do "wszystkich" połączonych organizacji.

- Proponowana połączona organizacja to połączona organizacja, która została utworzona automatycznie, ale nie utworzyła ani nie zatwierdziła organizacji przez administratora. Gdy użytkownik tworzy konto w celu uzyskania pakietu dostępu spoza skonfigurowanej połączonej  organizacji, wszystkie automatycznie utworzone połączone organizacje będą w proponowanym stanie, ponieważ żaden administrator w dzierżawie nie skonfigurował tego partnerstwa. 
    
    Proponowane połączone organizacje nie są objęte ustawieniem "wszystkie skonfigurowane połączone organizacje" dla żadnych zasad, ale mogą być używane w zasadach tylko w przypadku zasad przeznaczonych dla określonych organizacji. 

Tylko użytkownicy ze skonfigurowanych połączonych organizacji mogą żądać pakietów dostępu, które są dostępne dla użytkowników ze wszystkich skonfigurowanych organizacji. Użytkownicy z proponowanych połączonych organizacji mają środowisko tak, jakby dla tej domeny nie było połączonej organizacji; Może tylko zobaczyć i zażądać pakietów dostępu ograniczonych do ich określonej organizacji lub ograniczonych do dowolnego użytkownika.

> [!NOTE]
> W ramach tej nowej funkcji wszystkie połączone organizacje utworzone przed 9.09.20 zostały uznane za **skonfigurowane.** Jeśli masz pakiet dostępu, który umożliwiał użytkownikom z dowolnej organizacji logowanie się, przejrzyj listę połączonych organizacji utworzonych przed tym dniem, aby upewnić się, że żadna z nich nie została błędnie sklasyfikowana zgodnie z **konfiguracją**.  Administrator może odpowiednio **zaktualizować właściwość** State. Aby uzyskać wskazówki, [zobacz Aktualizowanie połączonej organizacji.](#update-a-connected-organization)

## <a name="next-steps"></a>Następne kroki

- [Zarządzanie dostępem dla użytkowników zewnętrznych](./entitlement-management-external-users.md)
- [Reguluj dostęp dla użytkowników, którzy nie mają dostępu do katalogu](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
