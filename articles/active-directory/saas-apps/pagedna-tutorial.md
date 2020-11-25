---
title: 'Samouczek: integracja Azure Active Directory z usługą PageDNA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i PageDNA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: e1ccd337851f8242526362675a11280e33584082
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993928"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Samouczek: integracja Azure Active Directory z usługą PageDNA

W tym samouczku dowiesz się, jak zintegrować usługę PageDNA z usługą Azure Active Directory (Azure AD).

Integracja PageDNA z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do usługi PageDNA.
* Możesz umożliwić użytkownikom automatyczne logowanie się do usługi PageDNA (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji typu "oprogramowanie jako usługa" (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą PageDNA, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja PageDNA z włączonym logowaniem jednokrotnym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku należy skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD w środowisku testowym i zintegrować usługę PageDNA z usługą Azure AD.

Program PageDNA obsługuje następujące funkcje:

* Logowanie jednokrotne zainicjowane przez usługę SP (SSO).

* Inicjowanie obsługi użytkowników just in Time.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Dodawanie PageDNA z witryny Azure Marketplace

Aby skonfigurować integrację programu PageDNA z usługą Azure AD, musisz dodać PageDNA z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz pozycję **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja nowej aplikacji](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **PageDNA**. W wynikach wyszukiwania wybierz pozycję **PageDNA**, a następnie wybierz pozycję **Dodaj** , aby dodać aplikację.

    ![PageDNA na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą PageDNA na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy nawiązać połączenie między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w PageDNA.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi PageDNA, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-single-sign-on)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie](#configure-pagedna-single-sign-on)** jednokrotne w usłudze PageDNA, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą Britta Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego PageDNA](#create-a-pagedna-test-user)** , tak aby użytkownik o nazwie Britta Simon w PageDNA, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Przetestuj logowanie](#test-single-sign-on)** jednokrotne, aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą PageDNA, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **PageDNA** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz tryb **SAML/WS-karmione** , aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz opcję **Edytuj** (ikona ołówka), aby otworzyć okienko **podstawowe ustawienia protokołu SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W okienku **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

    ![PageDNA domenę i adresy URL Logowanie jednokrotne](common/sp-identifier.png)

    1. W polu **adres URL logowania** wprowadź adres URL przy użyciu jednego z następujących wzorców:

        ```https
        https://stores.pagedna.com/<your site>
        https://<your domain>
        https://<your domain>/<your site>
        https://www.nationsprint.com/<your site>
        ```

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL przy użyciu jednego z następujących wzorców:

        ```https
        https://stores.pagedna.com/<your site>/saml2ep.cgi
        https://www.nationsprint.com/<your site>/saml2ep.cgi
        ```

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej PageDNA](mailto:success@pagedna.com). Można również odwołać się do wzorców przedstawionych w okienku podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz** , aby pobrać **certyfikat (RAW)** z podanych opcji i Zapisz go na komputerze.

    ![Opcja pobierania certyfikatu (RAW)](common/certificateraw.png)

1. W sekcji **Konfigurowanie PageDNA** Skopiuj adres URL lub adresy URL, które są potrzebne:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiuj adresy URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurowanie logowania jednokrotnego PageDNA

Aby skonfigurować Logowanie jednokrotne po stronie PageDNA, Wyślij pobrany certyfikat (RAW) i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej PageDNA](mailto:success@pagedna.com). Zespół PageDNA sprawdzi, czy połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W Azure Portal w lewym okienku wybierz pozycję **Azure Active Directory**    >  **Użytkownicy**  >  **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. W górnej części ekranu wybierz pozycję **+ nowy użytkownik**.

    ![Opcja nowego użytkownika](common/new-user.png)

1. W okienku **użytkownika** wykonaj następujące czynności:

    ![Okienko użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BrittaSimon \@ \<yourcompanydomain> . \<extension>**. Na przykład **BrittaSimon \@ contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć funkcję logowania jednokrotnego dla użytkownika Britta Simon, udzielając Użytkownikowi dostępu do usługi PageDNA.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**  >  **PageDNA**.

    ![Okienko aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście Aplikacje wybierz pozycję **PageDNA**.

    ![PageDNA na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **Zarządzaj** wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **+ Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części okienka.

1. Jeśli oczekujesz wartości roli w potwierdzeniu SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. W dolnej części okienka wybierz **pozycję Wybierz**.

1. W okienku **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-pagedna-test-user"></a>Tworzenie użytkownika testowego PageDNA

Użytkownik o nazwie Britta Simon jest teraz tworzony w PageDNA. Nie trzeba wykonywać żadnych czynności w celu utworzenia tego użytkownika. PageDNA obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. Jeśli użytkownik o nazwie Britta Simon nie istnieje już w PageDNA, zostanie utworzony nowy po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą portalu My Apps.

Po wybraniu opcji **PageDNA** w portalu Moje aplikacje należy automatycznie zalogować się do subskrypcji usługi PageDNA, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz [dostęp i używanie aplikacji w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących integrowania aplikacji SaaS z Azure Active Directory](./tutorial-list.md)

* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](../manage-apps/what-is-single-sign-on.md)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)