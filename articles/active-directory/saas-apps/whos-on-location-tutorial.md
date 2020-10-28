---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą WhosOnLocation | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i WhosOnLocation.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: jeedes
ms.openlocfilehash: d0e5134da9083e97b3977b05d601c2cfba25f5d4
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636684"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosonlocation"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą WhosOnLocation

W tym samouczku dowiesz się, jak zintegrować usługę WhosOnLocation z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi WhosOnLocation z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do WhosOnLocation.
* Zezwól użytkownikom na automatyczne logowanie się do usługi WhosOnLocation przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) WhosOnLocation.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* WhosOnLocation obsługuje logowanie jednokrotne w usłudze **SP**

* Po skonfigurowaniu WhosOnLocation można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-whosonlocation-from-the-gallery"></a>Dodawanie WhosOnLocation z galerii

Aby skonfigurować integrację programu WhosOnLocation z usługą Azure AD, musisz dodać WhosOnLocation z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja** .
1. W sekcji **Dodaj z galerii** wpisz **WhosOnLocation** w polu wyszukiwania.
1. Wybierz pozycję **WhosOnLocation** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-whosonlocation"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla WhosOnLocation

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą WhosOnLocation przy użyciu użytkownika testowego o nazwie **B. Simon** . Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w WhosOnLocation.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą WhosOnLocation, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-whosonlocation-sso)** w usłudze WhosOnLocation, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego WhosOnLocation](#create-whosonlocation-test-user)** , aby dysponować odpowiednikiem B. Simon w WhosOnLocation, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **WhosOnLocation** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne** .
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML** .
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://login.whosonlocation.com/saml/login/<CUSTOM_ID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://login.whosonlocation.com/saml/metadata/<CUSTOM_ID>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.whosonlocation.com/saml/acs/<CUSTOM_ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi i identyfikatora. Skontaktuj się z [zespołem obsługi klienta WhosOnLocation](mailto:support@whosonlocation.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie WhosOnLocation** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory** , wybierz pozycję **Użytkownicy** , a następnie wybierz pozycję **Wszyscy użytkownicy** .
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło** .
   1. Kliknij pozycję **Utwórz** .

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi WhosOnLocation.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw** , a następnie wybierz pozycję **wszystkie aplikacje** .
1. Na liście Aplikacje wybierz pozycję **WhosOnLocation** .
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy** .

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika** , a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-whosonlocation-sso"></a>Konfigurowanie logowania jednokrotnego WhosOnLocation

1. W innym oknie przeglądarki Zaloguj się do witryny firmy WhosOnLocation jako administrator.

2. Kliknij pozycję **Narzędzia**  ->  **konto** .

    ![Zrzut ekranu przedstawia konto wybrane z menu Narzędzia w witrynie WhosOnLocation.](./media/WhosOnLocation-tutorial/config1.png)

3. W Nawigatorze po lewej stronie wybierz pozycję **dostęp pracownika** .

    ![Zrzut ekranu przedstawia dostęp pracownika wybrany z profilu konta.](./media/WhosOnLocation-tutorial/config2.png)

4. Wykonaj następujące czynności na poniższej stronie.

    ![Zrzut ekranu przedstawia kartę dostęp z employess, w której można wprowadzać dane użytkownika.](./media/WhosOnLocation-tutorial/config3.png)

    a. Zmień pozycję **Logowanie jednokrotne za pomocą protokołu SAML** na **wartość tak** .

    b. W polu tekstowym **adres URL wystawcy** wklej wartość **identyfikatora jednostki** skopiowaną z Azure Portal.

    c. W polu tekstowym **punkt końcowy logowania jednokrotnego** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu** .

    e. Kliknij pozycję **Zapisz konfigurację SAML** .

### <a name="create-whosonlocation-test-user"></a>Utwórz użytkownika testowego WhosOnLocation

W tej sekcji utworzysz użytkownika o nazwie B. Simon w WhosOnLocation. Aby dodać użytkowników na platformie WhosOnLocation, Pracuj z [zespołem pomocy technicznej WhosOnLocation](mailto:support@whosonlocation.com) . Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka WhosOnLocation w panelu dostępu należy automatycznie zalogować się do WhosOnLocation, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj WhosOnLocation z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chronić WhosOnLocation z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)