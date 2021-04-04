---
title: 'Samouczek: integracja Azure Active Directory z usługą ITRP | Microsoft Docs'
description: W tym samouczku dowiesz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ITRP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 2dff68f98e2922d5fc7a4fca1e6de8740bc2ae68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459683"
---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>Samouczek: integracja Azure Active Directory z usługą ITRP

W tym samouczku dowiesz się, jak zintegrować usługę ITRP z usługą Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi ITRP.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi ITRP (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą ITRP, należy dysponować:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi ITRP z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD w środowisku testowym.

* Usługa ITRP obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

## <a name="add-itrp-from-the-gallery"></a>Dodaj ITRP z galerii

Aby skonfigurować integrację programu ITRP z usługą Azure AD, musisz dodać ITRP z galerii do listy zarządzanych aplikacji SaaS.

1. W [Azure Portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory**:

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do pozycji **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**:

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wprowadź **ITRP**. W wynikach wyszukiwania wybierz pozycję **ITRP** , a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne w usłudze Azure AD za pomocą ITRP przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD i odpowiednim użytkownikiem w ITRP.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą ITRP, należy wykonać następujące czynności:

1. **[Skonfiguruj logowanie](#configure-azure-ad-single-sign-on)** jednokrotne w usłudze Azure AD, aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-itrp-single-sign-on)** w usłudze ITRP po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** , aby włączyć logowanie jednokrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego ITRP](#create-an-itrp-test-user)** , który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne w usłudze Azure AD w Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą ITRP, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji ITRP wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](common/select-sso.png)

2. W oknie dialogowym **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne:

    ![Wybierz metodę logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **podstawowe ustawienia SAML** :

    ![Zrzut ekranu przedstawia stronę Konfigurowanie pojedynczego Sign-On ze stroną SAML z wybraną ikoną Edytuj.](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML** wykonaj następujące czynności.

    ![Podstawowa konfiguracja SAML — okno dialogowe](common/sp-identifier.png)

    1. W polu **adres URL logowania** wprowadź adres URL w tym wzorcu:
    
       `https://<tenant-name>.itrp.com`

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL w tym wzorcu:

       `https://<tenant-name>.itrp.com`

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego adresu URL logowania i identyfikatora. Skontaktuj się z [zespołem pomocy technicznej ITRP](https://www.4me.com/support/) , aby uzyskać wartości. Można również odnieść się do wzorców przedstawionych w oknie dialogowym podstawowe informacje o **konfiguracji SAML** w Azure Portal.

5. W sekcji **certyfikat podpisywania SAML** wybierz ikonę **Edytuj** , aby otworzyć okno dialogowe **certyfikat podpisywania SAML** :

    ![Zrzut ekranu przedstawia stronę certyfikatu podpisywania SAML z wybraną ikoną Edytuj.](common/edit-certificate.png)

6. W oknie dialogowym **certyfikat podpisywania SAML** skopiuj wartość **odcisku palca** i Zapisz ją:

    ![Kopiuj wartość odcisku palca](common/copy-thumbprint.png)

7. W sekcji **Konfiguracja ITRP** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowywania**.

### <a name="configure-itrp-single-sign-on"></a>Konfigurowanie logowania jednokrotnego ITRP

1. W nowym oknie przeglądarki sieci Web Zaloguj się do firmowej witryny ITRP jako administrator.

1. W górnej części okna wybierz ikonę **Ustawienia** :

    ![Ikona Ustawienia](./media/itrp-tutorial/ic775570.png "Ikona Ustawienia")

1. W lewym okienku wybierz pozycję **Logowanie jednokrotne**:

    ![Wybierz Logowanie jednokrotne](./media/itrp-tutorial/ic775571.png "Wybierz pojedynczą Sign-On")

1. W sekcji Konfiguracja **logowania** jednokrotnego wykonaj następujące czynności.

    ![Zrzut ekranu pokazuje sekcję z opcją Single Sign-On z włączonym zaznaczeniem.](./media/itrp-tutorial/ic775572.png "Pojedyncza sekcja Sign-On")

    ![Zrzut ekranu przedstawia sekcję z pojedynczym Sign-On, w której można dodać informacje opisane w tym kroku.](./media/itrp-tutorial/ic775573.png "Pojedyncza sekcja Sign-On")

    1. Wybierz pozycję **Włączone**.

    1. W polu **adres URL wylogowywania zdalnego** wklej wartość **adresu URL wylogowywania** skopiowaną z Azure Portal.

    1. W polu **adres URL rejestracji jednokrotnej protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z Azure Portal.

    1. W polu **odcisk palca certyfikatu** wklej wartość **odcisku palca** certyfikatu skopiowanego z Azure Portal.

    1. Wybierz pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w Azure Portal.

1. W Azure Portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**:

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu:

    ![Wybierz nowego użytkownika](common/new-user.png)

3. W oknie dialogowym **użytkownik** wykonaj następujące czynności.

    ![Okno dialogowe użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon@ \<yourcompanydomain> . \<extension>**. (Na przykład BrittaSimon@contoso.com .)

    1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość znajdującą się w polu **hasło** .

    1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając swój dostęp do usługi ITRP.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **ITRP**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **ITRP**.

    ![Lista aplikacji](common/all-applications.png)

3. W lewym okienku wybierz pozycję **Użytkownicy i grupy**:

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części okna.

6. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Kliknij przycisk **Wybierz** w dolnej części okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-an-itrp-test-user"></a>Tworzenie użytkownika testowego ITRP

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze ITRP, należy dodać je do ITRP. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do dzierżawy ITRP.

1. W górnej części okna wybierz ikonę **rekordów** :

    ![Ikona rekordów](./media/itrp-tutorial/ic775575.png "Ikona rekordów")

1. Z menu wybierz **osoby**:

    ![Wybierz osoby](./media/itrp-tutorial/ic775587.png "Wybierz osoby")

1. Wybierz znak plus ( **+** ), aby dodać nową osobę:

    ![Wybierz znak plus](./media/itrp-tutorial/ic775576.png "Wybierz znak plus")

1. W oknie dialogowym **Dodaj nową osobę** wykonaj następujące czynności.

    ![Okno dialogowe Dodawanie nowej osoby](./media/itrp-tutorial/ic775577.png "Okno dialogowe Dodawanie nowej osoby")

    1. Wprowadź nazwę i adres e-mail prawidłowego konta usługi Azure AD, które chcesz dodać.

    1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Do udostępniania kont użytkowników usługi Azure AD można używać dowolnego narzędzia do tworzenia konta użytkownika lub interfejsu API dostarczonego przez ITRP.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka ITRP w panelu dostępu należy automatycznie zalogować się do wystąpienia ITRP, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)