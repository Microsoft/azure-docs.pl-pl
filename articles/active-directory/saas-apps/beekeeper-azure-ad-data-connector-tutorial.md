---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą pszczelarzy Azure AD SSO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Pszczelarzer Logowanie jednokrotne usługi Azure AD.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/14/2020
ms.author: jeedes
ms.openlocfilehash: 82fced2d504af1823b3e6e1561ee875067a962c5
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91741418"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beekeeper-azure-ad-sso"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) w usłudze Azure AD — Logowanie jednokrotne

W ramach tego samouczka dowiesz się, jak zintegrować program pszczelarski usługi Azure AD SSO z usługą Azure Active Directory (Azure AD). W przypadku integrowania usługi Azure AD z logowaniem jednokrotnym w usłudze Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do pszczelarza rejestracji jednokrotnej usługi Azure AD.
* Zezwól użytkownikom na automatyczne logowanie do pszczelarza usługi Azure AD Logowanie jednokrotne przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Niezależna subskrypcja usługi Azure AD Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program pszczelarzy usługi Azure AD SSO obsługuje logowanie jednokrotne **w usłudze SP i dostawcy tożsamości**
* Program pszczelarzy usługi Azure AD SSO obsługuje funkcję aprowizacji użytkowników **just in Time**
* Po skonfigurowaniu programu Pszczelarzer Logowanie jednokrotne w usłudze Azure AD można wymusić kontrolki sesji, które chronią eksfiltracji i niefiltrowanie danych poufnych w organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-beekeeper-azure-ad-sso-from-the-gallery"></a>Dodawanie pszczelarza usługi Azure AD SSO z galerii

Aby skonfigurować integrację usługi Azure AD SSO z logowaniem jednokrotnym w usłudze Azure AD, musisz dodać program pszczelarski do usługi Azure AD SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **pszczelarzer Logowanie jednokrotne usługi Azure AD** w polu wyszukiwania.
1. Wybierz pozycję **pszczelarzer Logowanie jednokrotne usługi Azure AD** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-beekeeper-azure-ad-sso"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu pszczelarzy w usłudze Azure AD SSO

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu pszczelarzy usługi Azure AD SSO przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w pszczelarstwie usługi Azure AD SSO.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą programu Pszczelarzer rejestracji jednokrotnej usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj pszczelarzy usługi Azure AD Logowanie jednokrotne](#configure-beekeeper-azure-ad-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz pszczelarzy dla użytkownika testowego logowania jednokrotnego usługi Azure AD](#create-beekeeper-azure-ad-sso-test-user)** , aby uzyskać odpowiednik B. Simon w pszczelarstwie usługi Azure AD SSO, który jest połączony z reprezentacją usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie programu **pszczelarstwa usługi Azure AD SSO** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** , jeśli masz **plik metadanych dostawcy usług** i chcesz skonfigurować w trybie zainicjowania programu **dostawcy tożsamości** , wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML.

    ![Zrzut ekranu przedstawia podstawową konfigurację języka SAML, w której można wprowadzić identyfikator, odpowiedź U R L i wybrać pozycję Zapisz.](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your_company>.beekeeper.io/login`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Aby uzyskać tę wartość, skontaktuj się z biurem [obsługi klienta rejestracji jednokrotnej usługi Azure AD](mailto:support@beekeeper.io) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Program pszczelarzer aplikacji rejestracji jednokrotnej usługi Azure AD oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawia ustawienie dodatkowego U R LS, gdzie można wprowadzić znak U R L.](common/default-attributes.png)

1. Oprócz powyższych, program Pszczelarzer aplikacji SSO usługi Azure AD oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------ | --------- |
    | firstname | user.givenname |
    | lastname | user.surname |
    | poczta e-mail | user.mail |
    | nazwa użytkownika | User. PrincipalName |
    | pozycja | user.jobtitle |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie programu pszczelarskiego usługi Azure AD SSO** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Pszczelarzer Logowanie jednokrotne usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **pszczelarzer Logowanie jednokrotne usługi Azure AD**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-beekeeper-azure-ad-sso"></a>Konfigurowanie pszczelarzy usługi Azure AD SSO

Aby skonfigurować Logowanie jednokrotne w programie **pszczelarzer po stronie logowania jednokrotnego usługi Azure AD** , musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [pszczelarzy zespołu pomocy technicznej usługi Azure AD SSO](mailto:support@beekeeper.io). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-beekeeper-azure-ad-sso-test-user"></a>Tworzenie pszczelarzy użytkownika testowego logowania jednokrotnego usługi Azure AD

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w programie Pszczelarzer usługi Azure AD SSO. Program pszczelarzy usługi Azure AD SSO obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie pszczelarzy Azure AD SSO, po uwierzytelnieniu zostanie utworzony nowy.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka rejestracji jednokrotnej usługi Azure AD w panelu dostępu należy automatycznie zalogować się do programu Pszczelarzer usługi Azure AD SSO, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Pszczelarzer usługi Azure AD SSO z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)