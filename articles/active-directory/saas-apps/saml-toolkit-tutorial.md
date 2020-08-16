---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu zestawu Azure AD SAML Toolkit | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i usługi Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cae29bb4edaf7040b1cb41293c902f975a03f23
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258772"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-azure-ad-saml-toolkit"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Azure AD SAML Toolkit

W tym samouczku dowiesz się, jak zintegrować usługę Azure AD SAML Toolkit z usługą Azure Active Directory (Azure AD). Po zintegrowaniu zestawu narzędzi Azure AD SAML Toolkit z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do zestawu narzędzi SAML usługi Azure AD.
* Umożliwia użytkownikom automatyczne logowanie do usługi Azure AD SAML Toolkit przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) usługi Azure AD.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zestaw narzędzi języka SAML usługi Azure AD obsługuje protokół SSO zainicjowany przez usługę **SP**
* Po skonfigurowaniu programu Azure AD SAML Toolkit można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Dodawanie zestawu narzędzi SAML usługi Azure AD z galerii

Aby skonfigurować integrację usługi Azure AD SAML Toolkit z usługą Azure AD, musisz dodać zestaw narzędzi SAML usługi Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Azure AD SAML Toolkit** w polu wyszukiwania.
1. Wybierz pozycję **Azure AD SAML Toolkit** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-azure-ad-saml-toolkit"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla zestawu narzędzi SAML usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą zestawu Azure AD SAML Toolkit przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w narzędziu SAML webtoolkit usługi Azure AD.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą zestawu narzędzi SAML usługi Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Konfigurowanie ustawień logowania jednokrotnego w usłudze **[Azure AD przy użyciu zestawu narzędzi](#configure-azure-ad-saml-toolkit-sso)** .
    1. **[Utwórz użytkownika testowego usługi Azure AD SAML Toolkit](#create-azure-ad-saml-toolkit-test-user)** , aby dysponować odpowiednikiem B. Simon w narzędziu SAML Toolkit usługi Azure AD, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **usługi Azure AD SAML Toolkit** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://samltoolkit.azurewebsites.net/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `https://samltoolkit.azurewebsites.net`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

    > [!NOTE]
    > Te wartości nie są wartościami rzeczywistymi. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora i adresu URL odpowiedzi, który został wyjaśniony w dalszej części tego samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie zestawu Azure AD SAML Toolkit** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do zestawu narzędzi SAML usługi Azure AD.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Azure AD SAML Toolkit**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurowanie usługi Azure AD SAML Toolkit — Logowanie jednokrotne

1. Otwórz nowe okno przeglądarki sieci Web, jeśli nie zarejestrowano Cię w witrynie sieci Web usługi Azure AD SAML Toolkit, najpierw Zarejestruj się, klikając **Rejestr**. Jeśli zarejestrowano się już, zaloguj się do witryny firmy Microsoft Azure webtoolkit przy użyciu zarejestrowanych poświadczeń logowania.

    ![Rejestr zestawu Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/register.png)

1. Kliknij **konfigurację SAML**.

    ![Konfiguracja SAML zestawu Azure AD SAML Toolkit](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kliknij przycisk **Utwórz**.

    ![Usługa Azure AD SAML Toolkit — tworzenie rejestracji jednokrotnej](./media/saml-toolkit-tutorial/createsso.png)

1. Na stronie **Konfiguracja logowania jednokrotnego SAML** wykonaj następujące czynności:

    ![Usługa Azure AD SAML Toolkit — tworzenie rejestracji jednokrotnej](./media/saml-toolkit-tutorial/fill-details.png)

    1. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    1. W polu tekstowym **Identyfikator usługi Azure AD** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    1. W polu tekstowym **Adres URL wylogowywania** wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    1. Kliknij pozycję **Wybierz plik** i Przekaż plik **certyfikatu (RAW)** pobrany z Azure Portal.

    1. Kliknij przycisk **Utwórz**.

    1. Kopiuj adres URL logowania, identyfikator i wartości adresu URL usługi ACS na stronie Konfiguracja logowania jednokrotnego usługi SAML Toolkit i wklej do poszanowania pól tekstowych w **sekcji Podstawowa konfiguracja SAML** w Azure Portal.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD SAML Toolkit

W tej sekcji użytkownik o nazwie B. Simon został utworzony w narzędziu SAML Toolkit usługi Azure AD. Utwórz użytkownika testowego w narzędziu, rejestrując nowego użytkownika i podając wszystkie szczegóły użytkownika. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Azure AD SAML Toolkit w panelu dostępu należy automatycznie zalogować się do zestawu narzędzi SAML usługi Azure AD, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj usługę Azure AD SAML Toolkit z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić zestaw narzędzi SAML usługi Azure AD za pomocą zaawansowanej widoczności i kontrolek](https://docs.microsoft.com/cloud-app-security/protect-azure)