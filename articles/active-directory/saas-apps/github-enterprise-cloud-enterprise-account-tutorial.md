---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą GitHub Enterprise Cloud — konto przedsiębiorstwa | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i GitHub Enterprise Cloud — Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/12/2021
ms.author: jeedes
ms.openlocfilehash: 7360ad5abc7342043152c2da11038b624b0bfadc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101649992"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-cloud---enterprise-account"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą GitHub Enterprise Cloud — konto przedsiębiorstwa

W ramach tego samouczka dowiesz się, jak zintegrować konto w chmurze w usłudze GitHub Enterprise z Azure Active Directory (Azure AD). Po zintegrowaniu konta usługi GitHub Enterprise Cloud-Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do konta przedsiębiorstwa w serwisie GitHub i wszystkich organizacji w ramach konta przedsiębiorstwa.


## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto w serwisie [GitHub Enterprise](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/about-enterprise-accounts).
* Konto użytkownika usługi GitHub będące właścicielem konta przedsiębiorstwa. 

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa GitHub Enterprise Cloud — konto przedsiębiorstwa obsługuje logowanie jednokrotne w ramach usługi **SP** i **dostawcy tożsamości** .
* Usługa GitHub Enterprise Cloud — konto przedsiębiorstwa obsługuje funkcję inicjowania obsługi klienta **just in Time** .

## <a name="adding-github-enterprise-cloud---enterprise-account-from-the-gallery"></a>Dodawanie konta w chmurze usługi GitHub dla przedsiębiorstw z galerii

Aby skonfigurować integrację konta przedsiębiorstwa z usługą GitHub Enterprise Cloud-Enterprise w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS konto w chmurze dla przedsiębiorstw z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania opcję konto w chmurze w usłudze **GitHub Enterprise — przedsiębiorstwo** .
1. Wybierz pozycję **konto usługi GitHub Enterprise Cloud-Enterprise** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-cloud---enterprise-account"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla przedsiębiorstwa GitHub Enterprise Cloud-Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą konta w chmurze w usłudze GitHub dla przedsiębiorstw przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze GitHub Enterprise Cloud — konto przedsiębiorstwa.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu konta w chmurze w usłudze GitHub Enterprise, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika usługi Azure AD i konto użytkownika testowego do aplikacji GitHub](#assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app)** — aby włączyć konto użytkownika i użytkownika testowego `B.Simon` do korzystania z logowania jednokrotnego usługi Azure AD.
1. **[Włącz i przetestuj protokół SAML dla konta przedsiębiorstwa i jego organizacji](#enable-and-test-saml-for-the-enterprise-account-and-its-organizations)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Przetestuj Logowanie jednokrotne przy użyciu innego właściciela konta przedsiębiorstwa lub konta członka organizacji](#test-sso-with-another-enterprise-account-owner-or-organization-member-account)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji w **chmurze w przedsiębiorstwie** w serwisie GitHub Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://github.com/enterprises/<ENTERPRISE-SLUG>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://github.com/enterprises/<ENTERPRISE-SLUG>/saml/consume`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

     W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca:  `https://github.com/enterprises/<ENTERPRISE-SLUG>/sso`

    > [!NOTE]
    > Zamień `<ENTERPRISE-SLUG>` na rzeczywistą nazwę konta przedsiębiorstwa w serwisie GitHub.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateBase64.png)

1. W sekcji **Konfigurowanie konta usługi GitHub Enterprise Cloud-Enterprise** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie `B.Simon` .

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

<a name="assign-the-azure-ad-test-user"></a>

### <a name="assign-your-azure-ad-user-and-the-test-user-account-to-the-github-app"></a>Przypisywanie użytkownika usługi Azure AD i konta użytkownika testowego do aplikacji GitHub

W tej sekcji zostanie włączone `B.Simon` i Twoje konto użytkownika do korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do konta przedsiębiorstwa w witrynie GitHub Enterprise Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **GitHub Enterprise Cloud — konto przedsiębiorstwa**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** i konto użytkownika z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="enable-and-test-saml-for-the-enterprise-account-and-its-organizations"></a>Włączanie i testowanie języka SAML dla konta przedsiębiorstwa i jego organizacji

Aby skonfigurować Logowanie jednokrotne w usłudze **GitHub Enterprise Cloud-Enterprise** , wykonaj czynności opisane w [tej dokumentacji usługi GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/enforcing-security-settings-in-your-enterprise-account#enabling-saml-single-sign-on-for-organizations-in-your-enterprise-account). 
1. Zaloguj się do GitHub.com przy użyciu konta użytkownika, które jest [właścicielem konta przedsiębiorstwa](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-your-enterprise/roles-in-an-enterprise#enterprise-owner). 
1. Skopiuj wartość z `Login URL` pola w aplikacji z Azure Portal i wklej ją w `Sign on URL` polu Ustawienia SAML dla konta przedsiębiorstwa usługi GitHub. 
1. Skopiuj wartość z `Azure AD Identifier` pola w aplikacji z Azure Portal i wklej ją w `Issuer` polu Ustawienia SAML dla konta przedsiębiorstwa usługi GitHub. 
1. Skopiuj zawartość pliku **certyfikatu (base64)** pobranego w powyższych krokach z Azure Portal i wklej je w odpowiednim polu w ustawieniach SAML dla konta przedsiębiorstwa w serwisie GitHub. 
1. Kliknij `Test SAML configuration` i upewnij się, że możesz pomyślnie przeprowadzić uwierzytelnienie na koncie usługi GitHub dla przedsiębiorstw w usłudze Azure AD.
1. Po pomyślnym zakończeniu testu Zapisz ustawienia. 
1. Po uwierzytelnieniu za pośrednictwem protokołu SAML po raz pierwszy z poziomu konta w usłudze GitHub Enterprise zostanie utworzona _połączona tożsamość zewnętrzna_ na koncie usługi GitHub Enterprise, które kojarzy konto użytkownika w witrynie GitHub z kontem użytkownika w usłudze Azure AD.  
 
Po włączeniu protokołu SAML SSO dla konta przedsiębiorstwa w serwisie GitHub Logowanie jednokrotne SAML jest domyślnie włączone dla wszystkich organizacji należących do konta przedsiębiorstwa. Wszyscy członkowie będą zobowiązani do uwierzytelniania za pomocą logowania jednokrotnego SAML w celu uzyskania dostępu do organizacji, w których są członkami, a właściciele przedsiębiorstwa będą musieli uwierzytelniać się przy użyciu protokołu SAML SSO podczas uzyskiwania dostępu do konta przedsiębiorstwa.

<a name="test-sso"></a>

## <a name="test-sso-with-another-enterprise-account-owner-or-organization-member-account"></a>Testowanie logowania jednokrotnego przy użyciu innego właściciela konta przedsiębiorstwa lub konta członka organizacji

Po skonfigurowaniu integracji SAML dla konta przedsiębiorstwa usługi GitHub (które również dotyczy organizacji usługi GitHub na koncie przedsiębiorstwa), właściciele kont przedsiębiorstwa, którzy zostali przypisani do aplikacji w usłudze Azure AD, powinni mieć możliwość przejścia do adresu URL konta przedsiębiorstwa w serwisie GitHub ( `https://github.com/enterprises/<enterprise account>` ), uwierzytelniania za pośrednictwem protokołu SAML i uzyskiwania dostępu do zasad i ustawień w ramach konta usługi GitHub Enterprise. 

Właściciel organizacji w ramach konta przedsiębiorstwa powinien mieć możliwość [zapraszania użytkownika do wzięcia udziału w organizacji usługi GitHub](https://docs.github.com/en/free-pro-team@latest/github/setting-up-and-managing-organizations-and-teams/inviting-users-to-join-your-organization). Zaloguj się do GitHub.com przy użyciu konta właściciela organizacji i postępuj zgodnie z instrukcjami w artykule, aby zaprosić `B.Simon` do organizacji. Należy utworzyć konto użytkownika usługi GitHub, `B.Simon` Jeśli jeszcze nie istnieje. 

Aby przetestować dostęp do organizacji GitHub na koncie przedsiębiorstwa przy użyciu `B.Simon` konta użytkownika testowego:
1. Zaproś `B.Simon` do organizacji w ramach konta przedsiębiorstwa jako właściciel organizacji. 
1. Zaloguj się do usługi GitHub.com przy użyciu konta użytkownika, które chcesz połączyć z `B.Simon` kontem użytkownika usługi Azure AD.
1. Zaloguj się do usługi Azure AD przy użyciu `B.Simon` konta użytkownika.
1. Przejdź do organizacji usługi GitHub. Użytkownik powinien otrzymywać monit o uwierzytelnienie za pośrednictwem protokołu SAML. Po pomyślnym uwierzytelnieniu SAML `B.Simon` powinno być możliwe uzyskanie dostępu do zasobów organizacji. 

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu konta w usłudze GitHub Enterprise Cloud-Enterprise można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
