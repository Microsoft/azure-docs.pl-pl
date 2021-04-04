---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu oprogramowania w chmurze | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a chmurą oprogramowania.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/09/2020
ms.author: jeedes
ms.openlocfilehash: c2a2446da0e1ccf1b3dab88fe898b179291ddaba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98726145"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-software-ag-cloud"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z chmurą oprogramowania

W tym samouczku dowiesz się, jak zintegrować chmurę oprogramowania z usługą Azure Active Directory (Azure AD). Po zintegrowaniu integracji oprogramowania z chmurą z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury oprogramowania.
* Zezwól użytkownikom na automatyczne logowanie do chmury oprogramowania w chmurze przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w chmurze.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie AG Cloud obsługuje zainicjowanie rejestracji jednokrotnej w programie **SP**
* Oprogramowanie AG Cloud obsługuje funkcję aprowizacji użytkowników **just in Time**

## <a name="adding-software-ag-cloud-from-the-gallery"></a>Dodawanie chmury Software AG z galerii

Aby skonfigurować integrację oprogramowania w chmurze z usługą Azure AD, należy dodać chmurę oprogramowania w chmurze z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Cloud w chmurze** .
1. Wybierz pozycję **oprogramowanie AG Cloud** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-software-ag-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla oprogramowania w chmurze

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą chmury Software AG przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze Software AG.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury Software AG, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj oprogramowanie do rejestracji jednokrotnej w chmurze](#configure-software-ag-cloud-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego w chmurze dla oprogramowania](#create-software-ag-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w chmurze oprogramowania Cloud, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji w **chmurze oprogramowania** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME/broker/IDENTITY-PROVIDER-NAME/endpoint`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 

        `https://*.softwareag.cloud/auth/realms/TENANT-NAME`

        > [!NOTE]
        > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej usługi Cloud Client](mailto:support@softwareag.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie chmury oprogramowania w chmurze** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **oprogramowanie AG Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-software-ag-cloud-sso"></a>Konfigurowanie oprogramowania do chmury w chmurze — Logowanie jednokrotne

1. W innym oknie przeglądarki sieci Web Zaloguj się w witrynie internetowej programu Software AG Cloud jako administrator.

1.  Kliknij pozycję **Administracja**

    ![Konfigurowanie administrowania chmurą oprogramowania w chmurze](./media/software-ag-cloud-tutorial/admin.png)

1. Przejdź do logowania jednokrotnego **> Dodawanie dostawcy tożsamości**

    ![Konfigurowanie dostawcy tożsamości w chmurze oprogramowania Cloud](./media/software-ag-cloud-tutorial/add-identity-provider.png)

1. Wykonaj następujące czynności na poniższej stronie.

    ![Konfigurowanie oprogramowania Cloud w chmurze wykonaj kroki](./media/software-ag-cloud-tutorial/saml-1.png)

    a. W polu tekstowym **Nazwa wyświetlana dostawcy tożsamości** Nadaj nazwę, taką jak `azure ad` .

    b. W polu tekstowym identyfikator **URI przekierowania w chmurze dla dostawcy tożsamości** wpisz unikatową nazwę dla dostawcy tożsamości. Pole **identyfikatora URI przekierowania w chmurze oprogramowania** jest odświeżane i WYPEŁNIAne identyfikatorem URI. Skopiuj ten identyfikator URI i użyj go do skonfigurowania **identyfikatora jednostki** i innych informacji w Azure Portal zgodnie ze zdefiniowanymi wzorcami.

    c. Zaimportuj plik **XML metadanych Federacji** w **konfiguracji dostawcy tożsamości** i kliknij przycisk **dalej**.

    d. Przejdź do strony **Konfiguracja** i wypełnij pola w razie potrzeby.

### <a name="create-software-ag-cloud-test-user"></a>Utwórz użytkownika testowego chmury dla oprogramowania

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w chmurze Software AG. Chmura oprogramowania obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w chmurze oprogramowania, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następującej opcji. 

Przy założeniu, że Microsoft Azure jest skonfigurowany jako dostawca w programie Software AG Cloud, przejdź do, `www.softwareag.cloud` a następnie kliknij przycisk Zaloguj się i wprowadź nazwę środowiska. Na następnym ekranie kliknij link "Zaloguj się przy użyciu <IDP NAME> " i wprowadź poświadczenia. Po uwierzytelnieniu użytkownik zostanie zalogowany i przejdzie do strony głównej w chmurze oprogramowania.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu oprogramowania w chmurze można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).