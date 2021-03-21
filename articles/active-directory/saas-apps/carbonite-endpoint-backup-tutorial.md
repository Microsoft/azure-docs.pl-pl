---
title: 'Samouczek: integracja Azure Active Directory z kopiami zapasowymi punktów końcowych Carbonite | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i kopii zapasowej punktu końcowego Carbonite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: ff19275270e5b6572fb7d637b88c4736a3aa6ea0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92456487"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Samouczek: Integrowanie kopii zapasowych punktu końcowego Carbonite z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować kopię zapasową punktu końcowego Carbonite z Azure Active Directory (Azure AD). W przypadku integrowania kopii zapasowych punktu końcowego Carbonite z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do kopii zapasowej Carbonite Endpoint.
* Zezwól użytkownikom na automatyczne logowanie do usługi Kopia zapasowa Carbonite Endpoint przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej (SSO) usługi Carbonite.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Kopia zapasowa Carbonite Endpoint obsługuje usługę **SP i dostawcy tożsamości** zainicjowana przez usługę SSO

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Dodawanie kopii zapasowej punktu końcowego Carbonite z galerii

Aby skonfigurować integrację kopii zapasowych punktu końcowego Carbonite w usłudze Azure AD, musisz dodać kopię zapasową punktu końcowego Carbonite z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Carbonite Backup Endpoint** w polu wyszukiwania.
1. Wybierz pozycję **kopia zapasowa punktu końcowego Carbonite** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą kopii zapasowej Carbonite Endpoint przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Kopia zapasowa punktu końcowego Carbonite.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą kopii zapasowej Carbonite Endpoint, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj kopię zapasową punktu końcowego Carbonite](#configure-carbonite-endpoint-backup-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego kopii zapasowej Carbonite](#create-carbonite-endpoint-backup-test-user)** , aby uzyskać odpowiednik B. Simon w kopii zapasowej Carbonite punktu końcowego, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **kopia zapasowa punktu końcowego Carbonite** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz jeden z następujących adresów URL:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z następujących adresów URL:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **adres URL logowania** wpisz jeden z następujących adresów URL:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Skonfiguruj kopię zapasową punktu końcowego Carbonite** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurowanie rejestracji jednokrotnej Carbonite punktu końcowego

1. Aby zautomatyzować konfigurację w ramach kopii zapasowej Carbonite Endpoint, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij kolejno pozycje **Konfiguracja Carbonite kopia zapasowa punktu końcowego** spowoduje przekierowanie do aplikacji do tworzenia kopii zapasowych Carbonite. W tym miejscu podaj poświadczenia administratora, aby zalogować się do usługi Kopia zapasowa punktu końcowego Carbonite. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Carbonite kopii zapasowej punktu końcowego, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do kopii zapasowej punktu końcowego Carbonite jako administrator i wykonaj następujące czynności:

4. Kliknij **firmę** w okienku po lewej stronie.

    ![Zrzut ekranu przedstawia punkt końcowy Carbonite z wybranym przez firmę.](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Kliknij pozycję Logowanie **jednokrotne**.

    ![Zrzut ekranu przedstawia firmę z wybranym logowaniem jednokrotnym.](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Kliknij pozycję **Włącz** , a następnie kliknij pozycję **Edytuj ustawienia** do skonfigurowania.

    ![Zrzut ekranu przedstawia kartę Logowanie jednokrotne z ustawieniami włączania i edycji wywoływanymi jako out.](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Na stronie ustawienia **rejestracji** jednokrotnej wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia kartę Logowanie jednokrotne z informacjami opisanymi w tym kroku.](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. W polu tekstowym **Nazwa dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    1. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    1. Kliknij pozycję **Wybierz plik** , aby przekazać pobrany plik **certyfikatu (Base64)** z Azure Portal.

    1. Kliknij pozycję **Zapisz**.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do kopii zapasowej Carbonite Endpoint Protection.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **kopia zapasowa punktu końcowego Carbonite**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-carbonite-endpoint-backup-test-user"></a>Utwórz użytkownika testowego kopii zapasowej Carbonite Endpoint

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny sieci firmowej punktu końcowego Carbonite jako administrator.

1. Kliknij pozycję **Użytkownicy** w lewym okienku, a następnie kliknij pozycję **Dodaj użytkownika**.

    ![Zrzut ekranu przedstawia stronę punktu końcowego Carbonite z użytkownikami i Dodaj wybranych użytkowników.](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. Na stronie **Dodawanie użytkownika** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Dodawanie użytkownika, na której można wykonać kroki opisane tutaj.](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Wprowadź **adres e-mail**, **imię** i nazwisko **użytkownika, a** następnie podaj wymagane uprawnienia dla użytkownika zgodnie z wymaganiami organizacji.

    1. Kliknij pozycję **Dodaj użytkownika**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka kopia zapasowa punktu końcowego Carbonite w panelu dostępu należy automatycznie zalogować się do kopii zapasowej punktu końcowego Carbonite, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)