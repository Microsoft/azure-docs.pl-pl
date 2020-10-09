---
title: 'Samouczek: integracja Azure Active Directory z usługą MVISION w chmurze Azure AD Logowanie jednokrotne | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i MVISION konfigurację logowania jednokrotnego w chmurze usługi Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: d529123b52e0e90702839f6822a586cf53dffa52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88546716"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Samouczek: Integrowanie konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować konfigurację SSO usługi Azure AD MVISION w chmurze z usługą Azure Active Directory (Azure AD). W przypadku integrowania konfiguracji usługi Azure AD SSO w chmurze MVISION z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION.
* Zezwól użytkownikom na automatyczne logowanie się w celu MVISION konfiguracji rejestracji jednokrotnej w chmurze usługi Azure AD przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* MVISION chmurową subskrypcję usługi Azure AD Logowanie jednokrotne (SSO).


## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Konfiguracja logowania jednokrotnego w usłudze Azure AD w chmurze MVISION obsługuje usługi **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Po skonfigurowaniu usługi Dropbox można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Dodawanie konfiguracji usługi Azure AD SSO w chmurze MVISION z galerii

Aby skonfigurować integrację konfiguracji rejestracji jednokrotnej usługi Azure AD w chmurze MVISION w usłudze Azure AD, musisz dodać konfigurację rejestracji jednokrotnej w chmurze MVISION usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **MVISION Cloud Azure AD SSO Configuration** w polu wyszukiwania.
1. Wybierz pozycję **MVISION w chmurze konfiguracja logowania jednokrotnego usługi Azure AD** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą konfiguracji usługi Azure AD SSO w chmurze MVISION przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze MVISION Cloud Azure AD SSO Configuration.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą konfiguracji logowania jednokrotnego w chmurze usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj konfigurację logowania jednokrotnego w chmurze usługi Azure AD](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** w usłudze MVISION, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego konfiguracji logowania jednokrotnego w usłudze Azure AD MVISION w chmurze](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** , aby uzyskać odpowiednik Britta Simon w usłudze MVISION w chmurze konfiguracja logowania jednokrotnego usługi Azure AD, która jest połączona z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi Datadog** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)


4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![MVISION w chmurze konfiguracja logowania jednokrotnego usługi Azure AD i adresy URL logowania jednokrotnego](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej usługi Azure AD MVISION Cloud Configuration Client](mailto:support@skyhighnetworks.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji Konfiguracja **usługi MVISION w chmurze Azure AD SSO** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do konfiguracji rejestracji jednokrotnej w chmurze usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję MVISION w **chmurze Azure AD SSO Configuration**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **MVISION Cloud Azure AD SSO Configuration**.

    ![Link konfiguracja logowania jednokrotnego w usłudze Azure AD w chmurze MVISION na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.


## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurowanie usługi MVISION w chmurze Azure AD SSO konfiguracja logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne na stronie **Konfiguracja logowania jednokrotnego w usłudze Azure AD w chmurze MVISION** , należy wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL z Azure Portal do [MVISION Cloud usługi Azure AD SSO Configuration support](mailto:support@skyhighnetworks.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.


### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Tworzenie użytkownika testowego konfiguracji logowania jednokrotnego w usłudze Azure AD w chmurze MVISION

W tej sekcji utworzysz użytkownika o nazwie B. Simon w usłudze MVISION Cloud Azure AD SSO Configuration. Współpracuj z [zespołem pomocy technicznej usługi Azure AD MVISION w chmurze](mailto:support@skyhighnetworks.com) , aby dodać użytkowników z platformy konfiguracji usługi Azure AD rejestracji jednokrotnej w chmurze MVISION. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka konfiguracja logowania jednokrotnego usługi Azure AD w chmurze MVISION w panelu dostępu należy automatycznie zalogować się do konfiguracji usługi Azure AD rejestracji jednokrotnej w chmurze MVISION, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę MVISION w chmurze Azure AD SSO Configuration with Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)