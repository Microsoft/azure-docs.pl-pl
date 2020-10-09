---
title: 'Samouczek: integracja Azure Active Directory z usługą Syncplicity | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Syncplicity.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: da532adfa2d4ab97edb44de9ae49c646ccdff381
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88544897"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Samouczek: integracja Syncplicity z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Syncplicity z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Syncplicity z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Syncplicity.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Syncplicity przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Syncplicity.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa Syncplicity obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie Syncplicity z galerii

Aby skonfigurować integrację programu Syncplicity z usługą Azure AD, musisz dodać Syncplicity z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Syncplicity** w polu wyszukiwania.
1. Wybierz pozycję **Syncplicity** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Syncplicity przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Syncplicity.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Syncplicity, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-syncplicity-sso)** w usłudze Syncplicity, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
5. **[Utwórz użytkownika testowego Syncplicity](#create-syncplicity-test-user)** , aby dysponować odpowiednikiem B. Simon w Syncplicity, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Syncplicity** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Syncplicity](https://www.syncplicity.com/contact-us) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Syncplicity** skopiuj odpowiednie adresy URL na podstawie wymagania.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurowanie logowania jednokrotnego Syncplicity

1. Zaloguj się do dzierżawy **Syncplicity** .

1. W menu u góry kliknij pozycję **Administracja**, wybierz pozycję **Ustawienia**, a następnie kliknij pozycję **domena niestandardowa i logowanie jednokrotne**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na stronie okna dialogowego **pojedyncze Sign-On (SSO)** wykonaj następujące czynności:

    ![Pojedyncze Sign-On \( Logowanie jednokrotne\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. W polu tekstowym **domena niestandardowa** wpisz nazwę domeny.
  
    b. Wybierz opcję **włączone** jako **stan pojedynczej Sign-On**.

    c. W polu tekstowym **Identyfikator jednostki** wklej wartość **Identyfikator (identyfikator jednostki)** , która została użyta w **podstawowej konfiguracji SAML** w Azure Portal.

    d. W polu tekstowym **adres URL strony logowania** wklej **adres URL logowania** , który został skopiowany z Azure Portal.

    e. W polu tekstowym **adres URL strony wylogowywania** wklej **adres URL wylogowania** , który został skopiowany z Azure Portal.

    f. W obszarze **certyfikat dostawcy tożsamości**kliknij pozycję **Wybierz plik**, a następnie przekaż certyfikat pobrany z Azure Portal.

    przykład Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Syncplicity.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Syncplicity**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-syncplicity-test-user"></a>Utwórz użytkownika testowego Syncplicity

Aby użytkownicy usługi Azure AD mogli się zalogować, muszą być obsługiwani do aplikacji Syncplicity. W tej sekcji opisano sposób tworzenia kont użytkowników usługi Azure AD w programie Syncplicity.

**Aby udostępnić konto użytkownika w usłudze Syncplicity, wykonaj następujące czynności:**

1. Zaloguj się do dzierżawy usługi **Syncplicity** (na przykład: `https://company.Syncplicity.com` ).

1. Kliknij pozycję **administrator** i wybierz pozycję **konta użytkowników** , a następnie kliknij pozycję **Dodaj użytkownika**.

    ![Zarządzanie użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

1. Wpisz **adresy e-mail** konta usługi Azure AD, które chcesz udostępnić, wybierz pozycję **użytkownik** jako **rola**, a następnie kliknij przycisk **dalej**.

    ![Informacje o koncie](./media/syncplicity-tutorial/ic769765.png "Informacje o koncie")

    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail, w tym link do potwierdzenia i aktywowania konta.

1. Wybierz grupę w firmie, do której ma zostać członkiem nowy użytkownik, a następnie kliknij przycisk **dalej**.

    ![Członkostwo w grupie](./media/syncplicity-tutorial/ic769772.png "Członkostwo w grupie")

    > [!NOTE]
    > Jeśli na liście nie ma żadnych grup, kliknij przycisk **dalej**.

1. Wybierz foldery, które chcesz umieścić pod kontrolą Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **dalej**.

    ![Foldery Syncplicity](./media/syncplicity-tutorial/ic769773.png "Foldery Syncplicity")

> [!NOTE]
> Do aprowizacji kont użytkowników usługi Azure AD można używać innych narzędzi do tworzenia kont użytkowników Syncplicity i interfejsów API udostępnionych przez usługę Syncplicity.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Syncplicity w panelu dostępu należy automatycznie zalogować się do Syncplicity, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)