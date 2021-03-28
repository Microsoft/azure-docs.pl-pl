---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą narzędzia ScaleX Enterprise | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ScaleX Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: fecb7381786c36b077d329f68ca48ab10f65a984
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642962"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą ScaleX Enterprise

W tym samouczku dowiesz się, jak zintegrować polecenie ScaleX Enterprise z Azure Active Directory (Azure AD). Po zintegrowaniu programu ScaleX Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do ScaleX Enterprise.
* Zezwól użytkownikom na automatyczne logowanie do ScaleX Enterprise przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* W przypadku subskrypcji z włączonym logowaniem jednokrotnym (SSO) w przedsiębiorstwie.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ScaleX Enterprise obsługuje usługę **SP i dostawcy tożsamości** zainicjowane Logowanie jednokrotne

## <a name="adding-scalex-enterprise-from-the-gallery"></a>Dodawanie klasy ScaleX Enterprise z galerii

Aby skonfigurować integrację ScaleX Enterprise z usługą Azure AD, należy dodać ScaleX Enterprise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **scaleX Enterprise** w polu wyszukiwania.
1. Wybierz pozycję **scaleX Enterprise** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla elementu ScaleX Enterprise

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji ScaleX Enterprise przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w obszarze ScaleX Enterprise.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą ScaleX Enterprise, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj scaleX Enterprise SSO](#configure-scalex-enterprise-sso)** -aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    * **[Utwórz obiekt scaleX Enterprise test User](#create-scalex-enterprise-test-user)** -by miał odpowiednik B. Simon w elemencie scaleX Enterprise, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Enterprise dla przedsiębiorstw** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://platform.rescale.com/saml2/<company id>/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://platform.rescale.com/saml2/<company id>/sso/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z firmą [scaleX Enterprise Client Support Team](https://about.rescale.com/contactus.html) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja ScaleX Enterprise oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych, gdzie **EmailAddress** jest zamapowany z **użytkownikiem. mail**. Aplikacja ScaleX Enterprise oczekuje mapowania **EmailAddress** z elementem **User. userPrincipalName**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu scaleX Enterprise** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do ScaleX Enterprise.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **scaleX Enterprise**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-scalex-enterprise-sso"></a>Konfigurowanie ScaleX Enterprise SSO

1. Aby zautomatyzować konfigurację w ramach rozszerzenia ScaleX Enterprise, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguracja scaleX Enterprise** , aby skierować do aplikacji scaleX Enterprise. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do ScaleX Enterprise. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz skonfigurować polecenie ScaleX Enterprise ręcznie, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej witryny programu ScaleX Enterprise jako administrator i wykonaj następujące czynności:

1. Kliknij menu w prawym górnym rogu i wybierz pozycję **Administracja contoso**.

    > [!NOTE]
    > Firma Contoso jest tylko przykładem. Powinna to być rzeczywista nazwa firmy.

    ![Zrzut ekranu pokazujący przykładową nazwę firmy wybraną z menu w prawym górnym rogu.](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. Wybierz opcję **integracji** z górnego menu i wybierz pozycję **Logowanie jednokrotne**.

    ![Zrzut ekranu pokazujący, że wybrano opcję "integracje" i "Logowanie jednokrotne" wybrane z menu rozwijanego.](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. Wypełnij formularz w następujący sposób:

    ![Konfigurowanie logowania jednokrotnego](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. Wybierz opcję **Utwórz dowolnego użytkownika, który może uwierzytelniać się za pomocą logowania**

    b. **Dostawca usług SAML**: wklej wartość **_urn: języka Oasis: names: TC: SAML: 2.0: NameID-format: persistent_**

    c. **Nazwa pola adresu E-mail dostawcy tożsamości w odpowiedzi ACS**: wklej wartość `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identyfikator jednostki elementem EntityDescriptor dostawcy tożsamości:** Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    e. **Adres URL SingleSignOnService dostawcy tożsamości:** Wklej **adres URL logowania** z Azure Portal.

    f. **Publiczny certyfikat x509 dostawcy tożsamości:** Otwórz certyfikat x509 pobrany z platformy Azure w programie Notepad i wklej zawartość w tym polu. Upewnij się, że w środku zawartości certyfikatu nie ma podziałów wierszy.

    przykład Zaznacz następujące pola wyboru: **włączone, Szyfruj NameID i podpisz AuthnRequests.**

    h. Kliknij przycisk **Aktualizuj ustawienia rejestracji jednokrotnej** , aby zapisać ustawienia.

### <a name="create-scalex-enterprise-test-user"></a>Utwórz użytkownika "ScaleX Enterprise test User"

Aby umożliwić użytkownikom usługi Azure AD logowanie się do ScaleX Enterprise, muszą one być obsługiwane w programie do ScaleX Enterprise. W przypadku ScaleX Enterprise, Inicjowanie obsługi jest zadaniem automatycznym i nie są wymagane żadne czynności ręczne. Każdy użytkownik, który może pomyślnie uwierzytelnić się przy użyciu poświadczeń logowania jednokrotnego, zostanie automatycznie zainicjowany po stronie ScaleX.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ScaleX Enterprise w panelu dostępu należy automatycznie zalogować się do elementu ScaleX Enterprise, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj program ScaleX Enterprise z usługą Azure AD](https://aad.portal.azure.com/)
