---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z użytkownikiem zarządzanym przez firmę GitHub Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i użytkownikiem zarządzanym przez firmę GitHub Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/15/2021
ms.author: jeedes
ms.openlocfilehash: 864415f421f4fbecf31fd52a624ac568b4cf9c80
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574815"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-enterprise-managed-user"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z użytkownikiem zarządzanym przez firmę GitHub Enterprise

W ramach tego samouczka dowiesz się, jak zintegrować użytkownika zarządzanego przez firmę GitHub z usługą Azure Active Directory (Azure AD). Po zintegrowaniu użytkownika zarządzanego przez usługę GitHub Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do użytkownika zarządzanego przez usługę GitHub Enterprise.
* Zezwól użytkownikom na automatyczne logowanie do użytkownika zarządzanego przez firmę GitHub w ramach konta usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) użytkownika zarządzanego przez firmę GitHub.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Użytkownicy zarządzający usługą GitHub Enterprise obsługują usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.
* Użytkownik zarządzany przez firmę GitHub Enterprise obsługuje funkcję inicjowania obsługi użytkowników **just in Time** .
* Użytkownik zarządzany przez firmę GitHub Enterprise obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial).

## <a name="adding-github-enterprise-managed-user-from-the-gallery"></a>Dodawanie użytkownika zarządzanego przez firmę GitHub z galerii

Aby skonfigurować integrację użytkownika zarządzanego przez firmę GitHub z usługą Azure AD, musisz dodać użytkownika zarządzanego przez firmę GitHub z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **użytkownik zarządzany przez firmę GitHub Enterprise** .
1. Wybierz pozycję Aplikacja **zarządzana przez firmę GitHub Enterprise** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-github-enterprise-managed-user"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla użytkownika zarządzanego przez firmę GitHub Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą użytkownika zarządzanego przez firmę GitHub Enterprise przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze GitHub Enterprise użytkownik zarządzany.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu użytkownika zarządzanego przez usługę GitHub Enterprise, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie logowania JEDNOkrotnego użytkownika zarządzanego przez firmę GitHub](#configure-github-enterprise-managed-user-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego użytkownika zarządzanego przez usługę GitHub Enterprise](#create-github-enterprise-managed-user-test-user)** — Aby uzyskać odpowiednik elementu B. Simon w programie zarządzanym przez firmę GitHub Enterprise, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **użytkowników zarządzanych przez firmę GitHub Enterprise** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://github.com/enterprise-managed/<ENTITY>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://github.com/enterprises/<ENTITY>/saml/consume`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://github.com/enterprises/<ENTITY>/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej w witrynie GitHub Enterprise klienta](mailto:support@github.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfiguracja użytkownika zarządzanego w witrynie GitHub Enterprise** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu użytkownikowi zarządzanemu przez firmę GitHub Enterprise.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz opcję **użytkownik zarządzani przez firmę GitHub Enterprise**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-github-enterprise-managed-user-sso"></a>Konfigurowanie logowania jednokrotnego użytkownika zarządzanego przez usługę GitHub Enterprise

Aby skonfigurować Logowanie jednokrotne na stronie **użytkownika zarządzanego przez firmę GitHub Enterprise** , musisz wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej w witrynie GitHub Enterprise zarządzanej przez użytkownika](mailto:support@github.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-github-enterprise-managed-user-test-user"></a>Tworzenie użytkownika testowego użytkownika zarządzanego przez firmę GitHub Enterprise

W tej sekcji użytkownik o nazwie B. Simon został utworzony w ramach użytkownika zarządzanego przez firmę GitHub Enterprise. Użytkownik zarządzany przez firmę GitHub Enterprise obsługuje funkcję udostępniania just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze GitHub Enterprise Managed User, zostanie utworzony nowy podczas próby dostępu do użytkownika zarządzanego przez firmę GitHub Enterprise.

Użytkownik zarządzający usługą GitHub Enterprise obsługuje również automatyczne Inicjowanie obsługi użytkowników. więcej informacji można znaleźć w [tym miejscu](https://docs.microsoft.com/azure/active-directory/saas-apps/github-enterprise-managed-user-provisioning-tutorial) w artykule Jak skonfigurować automatyczne Inicjowanie obsługi użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania użytkownika zarządzanego przez firmę GitHub, w którym można zainicjować przepływ logowania.  

* Przejdź do adresu URL logowania użytkownika w witrynie GitHub Enterprise bezpośrednio i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do użytkownika zarządzanego przez usługę GitHub Enterprise, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka użytkownika zarządzanego przez firmę GitHub w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do użytkownika zarządzanego przez usługę GitHub Enterprise, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu użytkownika zarządzanego przez program GitHub Enterprise można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


