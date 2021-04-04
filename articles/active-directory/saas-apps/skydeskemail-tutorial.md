---
title: 'Samouczek: integracja Azure Active Directory z wiadomościami E-mail SkyDesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i wiadomości E-mail SkyDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 30e94c9737241ff49c29898adcc5e50c6b73a9b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516040"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Samouczek: integracja Azure Active Directory z adresem E-mail SkyDesk

W tym samouczku dowiesz się, jak zintegrować usługę SkyDesk email z usługą Azure Active Directory (Azure AD).
Integracja poczty E-mail SkyDesk z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do SkyDesk poczty E-mail.
* Możesz umożliwić użytkownikom automatyczne logowanie do SkyDesk poczty E-mail (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą poczty E-mail SkyDesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z włączonym logowaniem jednokrotnym w wiadomości E-mail SkyDesk

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Poczta E-mail SkyDesk obsługuje logowanie jednokrotne w usłudze **SP**

## <a name="adding-skydesk-email-from-the-gallery"></a>Dodawanie wiadomości E-mail SkyDesk z galerii

Aby skonfigurować integrację poczty E-mail SkyDesk w usłudze Azure AD, musisz dodać wiadomość E-mail SkyDesk z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać wiadomość E-mail SkyDesk z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SkyDesk email**, wybierz pozycję **SkyDesk e-mail** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

     ![SkyDesk E-mail na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą poczty E-mail SkyDesk na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w wiadomości E-mail SkyDesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą poczty E-mail SkyDesk, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Skonfiguruj Logowanie jednokrotne w **[wiadomości E-mail SkyDesk](#configure-skydesk-email-single-sign-on)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego poczty E-mail SkyDesk](#create-skydesk-email-test-user)** , aby uzyskać odpowiednik Britta Simon w wiadomości e-mail, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą poczty E-mail SkyDesk, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **poczty e-mail SkyDesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![SkyDesk domeny poczty E-mail i adresów URL Logowanie jednokrotne](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/) , aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie poczty E-mail SkyDesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurowanie pojedynczej Sign-On poczty E-mail SkyDesk

1. W innej przeglądarce internetowej Zaloguj się do swojego konta E-mail SkyDesk jako administrator.

1. W menu u góry kliknij pozycję **Konfiguracja**, a następnie wybierz pozycję **organizacja**.

    ![Zrzut ekranu przedstawia organizacja wybraną z menu Instalatora.](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Kliknij pozycję **domeny** w lewym panelu.

    ![Zrzut ekranu przedstawia domeny wybrane z panelu sterowania.](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Kliknij pozycję **Dodaj domenę**.

    ![Zrzut ekranu przedstawia wybraną domenę Dodaj.](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Wprowadź nazwę domeny, a następnie sprawdź domenę.

    ![Zrzut ekranu przedstawia kartę Dodaj domenę, w której można wprowadzić domenę.](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Kliknij pozycję **uwierzytelnianie SAML** w lewym panelu.

    ![Zrzut ekranu przedstawia uwierzytelnianie SAML wybrane z panelu sterowania.](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na stronie dialogowym **uwierzytelnianie SAML** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia okno dialogowe Szczegóły uwierzytelniania SAML, w którym można wprowadzić podane wartości.](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Aby można było korzystać z uwierzytelniania opartego na protokole SAML, należy mieć **zweryfikowaną** konfigurację **adresu URL** domeny lub portalu. Możesz ustawić adres URL portalu z unikatową nazwą.

    ![Zrzut ekranu przedstawia Portal U R L, gdzie wprowadzasz nazwę.](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. W polu tekstowym **adres URL logowania** wklej wartość **adresu URL logowania**, który został skopiowany z Azure Portal.

    b. W polu tekstowym adres URL **wylogowywania** wklej wartość **adres URL wylogowania**, który został skopiowany z Azure Portal.

    c. **Zmiana adresu URL hasła** jest opcjonalna, dlatego pozostaw to pole puste.

    d. Kliknij pozycję **Pobierz klucz z pliku** , aby wybrać pobrany certyfikat z Azure Portal a następnie kliknij pozycję **Otwórz** , aby przekazać certyfikat.

    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.

    f. Kliknij przycisk **OK** , aby zapisać zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do poczty E-mail SkyDesk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **wiadomość e-mail SkyDesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **SkyDesk e-mail**.

    ![Link E-mail SkyDesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-skydesk-email-test-user"></a>Utwórz użytkownika testowego wiadomości E-mail SkyDesk

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w wiadomości E-mail SkyDesk.

Kliknij pozycję **dostęp użytkownika** w lewym panelu w wiadomości e-mail SkyDesk, a następnie wprowadź nazwę użytkownika.

![Zrzut ekranu przedstawia dostęp użytkownika wybrany z panelu sterowania.](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Jeśli musisz utworzyć użytkowników zbiorczych, musisz skontaktować się z [zespołem obsługi klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka E-mail SkyDesk w panelu dostępu należy automatycznie zalogować się do wiadomości E-mail SkyDesk, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)