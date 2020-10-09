---
title: 'Samouczek: integracja Azure Active Directory z adaptacyjnymi wglądami | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i adaptacyjne szczegółowe informacje.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.openlocfilehash: 35414820228b615816f2389b7a609f5d63f5830d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760788"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Samouczek: Integrowanie adaptacyjnych szczegółowych informacji za pomocą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować adaptacyjne szczegółowe informacje z Azure Active Directory (Azure AD). Gdy integrujesz szczegółowe informacje z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do szczegółowych informacji.
* Umożliwia użytkownikom automatyczne logowanie się w celu adaptacyjnego wglądu w szczegółowe dane przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją adaptacyjnego wglądu w szczegółowe dane.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Adaptacyjne wglądy w dane obsługują zainicjowane przez **dostawcy tożsamości** Logowanie jednokrotne

## <a name="adding-adaptive-insights-from-the-gallery"></a>Dodawanie adaptacyjnych szczegółowych informacji z galerii

Aby skonfigurować integrację adaptacyjnego wglądu w usługę Azure AD, musisz dodać adaptacyjne szczegółowe informacje z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **adaptacyjne szczegółowe** dane w polu wyszukiwania.
1. Wybierz pozycję **adaptacyjne szczegółowe** dane z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z adaptacyjnym wglądem przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oknie adaptacyjne szczegółowe dane.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu adaptacyjnego wglądu, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj adaptacyjne logowanie JEDNOkrotne](#configure-adaptive-insights-sso)** w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego adaptacyjnego wglądu w szczegółowe](#create-adaptive-insights-test-user)** dane, aby uzyskać odpowiedniki B. Simon w adaptacyjnych wglądach, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie z integracją **propoznawczej** aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Możesz uzyskać informacje o identyfikatorze (identyfikator jednostki) i adresie URL odpowiedzi ze strony **ustawień protokołu SAML SSO** usługi adaptacyjnej.

4. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie usługi adaptacyjnej Insights** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Konfigurowanie rejestracji jednokrotnej usługi adaptacyjnej Insights

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy z możliwością adaptacyjnej usługi Insights jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

    ![Zrzut ekranu, który wyróżnia administrację w panelu nawigacji.](./media/adaptivesuite-tutorial/ic805644.png "Administracja")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Ustawienia logowania jednokrotnego**dla protokołu SAML.

    ![Zarządzanie ustawieniami logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/ic805645.png "Zarządzanie ustawieniami logowania jednokrotnego SAML")

4. Na stronie **Ustawienia rejestracji jednokrotnej protokołu SAML** wykonaj następujące czynności:

    ![Ustawienia logowania jednokrotnego SAML](./media/adaptivesuite-tutorial/ic805646.png "Ustawienia logowania jednokrotnego SAML")

    a. W polu tekstowym **Nazwa dostawcy tożsamości** wpisz nazwę konfiguracji.

    b. Wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal do pola tekstowego **Identyfikator jednostki dostawcy tożsamości** .

    c. Wklej wartość **adresu URL logowania** skopiowaną z Azure Portal do pola tekstowego **adres URL logowania jednokrotnego dostawcy tożsamości** .

    d. Wklej wartość **adresu URL wylogowywania** skopiowaną z Azure Portal do pola tekstowego **adres URL wylogowywania niestandardowego** .

    e. Aby przekazać pobrany certyfikat, kliknij pozycję **Wybierz plik**.

    f. Wybierz następujące polecenie, aby:

     * **Identyfikator użytkownika SAML**, wybierz **nazwę użytkownika adaptacyjnego wglądu w dane użytkownika**.

     * **Lokalizacja identyfikatora użytkownika SAML**, wybierz pozycję **Identyfikator użytkownika w NameID tematu**.

     * **Format SAML NameID**, wybierz opcję **adres e-mail**.

     * **Włącz protokół SAML**, wybierz opcję **Zezwalaj na logowanie za pomocą rejestracji jednokrotnej i bezpośredniego adaptacyjnego wglądu w szczegółowe**dane.

    przykład Skopiuj **adres URL rejestracji jednokrotnej usługi adaptacyjnej Insights** i wklej do pól **identyfikatora (identyfikator jednostki)** i **adres URL odpowiedzi** w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

    h. Kliknij przycisk **Zapisz**.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do szczegółowych informacji.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **adaptacyjne szczegółowe**dane.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-adaptive-insights-test-user"></a>Utwórz użytkownika testowego adaptacyjnego wglądu w szczegółowe dane

Aby umożliwić użytkownikom usługi Azure AD logowanie się w celu uzyskania adaptacyjnego wglądu, muszą one być obsługiwane w ramach adaptacyjnego wglądu w szczegółowe dane. W przypadku adaptacyjnych szczegółowych informacji Inicjowanie obsługi jest zadaniem ręcznym.

**Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej witryny usługi **adaptacyjnej Insights** jako administrator.

2. Przejdź do sekcji **Administration** (Administracja).

   ![Administracja](./media/adaptivesuite-tutorial/IC805644.png "Administracja")

3. W sekcji **Użytkownicy i role** kliknij pozycję **Użytkownicy**.

   ![Dodaj użytkownika](./media/adaptivesuite-tutorial/IC805648.png "Dodaj użytkownika")

4. W sekcji **nowy użytkownik** wykonaj następujące czynności:

   ![Prześlij](./media/adaptivesuite-tutorial/IC805649.png "Prześlij")

   a. Wpisz **nazwę**, nazwa **użytkownika**, **adres e-mail**i **hasło** prawidłowego użytkownika Azure Active Directory, który chcesz udostępnić do powiązanych pól tekstowych.

   b. Wybierz **rolę**.

   c. Kliknij przycisk **Prześlij**.

> [!NOTE]
> Możesz użyć dowolnych innych narzędzi do tworzenia kont użytkowników i interfejsów API z możliwością adaptacyjnego wglądu w szczegółowe dane, aby udostępnić konta użytkowników usługi Azure AD.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka adaptacyjnego wglądu w panelu dostępu należy automatycznie zalogować się do szczegółowych informacji, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

