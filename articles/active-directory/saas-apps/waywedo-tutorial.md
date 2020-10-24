---
title: 'Samouczek: integracja Azure Active Directory ze sposobem | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i sposobami wykonywania tych czynności.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: fd9a04c2324af2ec36d8fa1d43a079e9f2c32dd8
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520306"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Samouczek: Integrujmy się z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować się z Azure Active Directory (Azure AD). Po zintegrowaniu metody z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do metody.
* Umożliwiaj użytkownikom automatyczne logowanie się w sposób umożliwiający korzystanie z kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Sposób obsługi rejestracji jednokrotnej (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* W ten sposób obsługujemy Logowanie jednokrotne zainicjowane przez usługę **SP**
* Sposób, w jaki obsługujemy Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-way-we-do-from-the-gallery"></a>Dodawanie sposobu z galerii

Aby skonfigurować integrację sposobu wykonywania w usłudze Azure AD, musisz dodać sposób z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **sposób** w polu wyszukiwania.
1. Wybierz **metodę** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w taki sposób.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj sposób rejestracji jednokrotnej](#configure-way-we-do-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz sposób testowania użytkownika](#create-way-we-do-test-user)** — Aby uzyskać odpowiednik usługi Britta Simon w taki sposób, że jest on połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **sposób** integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta](mailto:support@waywedo.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **certyfikat (RAW)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificateraw.png)

1. Na stronie **Konfigurowanie** należy skopiować odpowiednie adresy URL zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Skonfiguruj sposób logowania jednokrotnego

1. Aby zautomatyzować konfigurację w taki sposób, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguracja** , aby przekierować Cię do aplikacji. Z tego miejsca podaj poświadczenia administratora, aby zalogować się w sposób. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz skonfigurować sposób ręcznej instalacji, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do swojej firmy jako administrator i wykonaj następujące czynności:

1. Kliknij **ikonę osoby** w prawym górnym rogu dowolnej strony w sposób, a następnie kliknij pozycję **konto** w menu rozwijanym.

    ![Sposób konta](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kliknij **ikonę menu** , aby otworzyć menu nawigacji wypychanej, a następnie kliknij pozycję **Logowanie jednokrotne**.

    ![Jak to zrobisz](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na stronie **Konfiguracja logowania** jednokrotnego wykonaj następujące czynności:

    ![Sposób zapisywania](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kliknij przełącznik **Logowanie** jednokrotne **, aby włączyć** Logowanie jednokrotne.

    b. W polu tekstowym **Nazwa logowania** jednokrotnego wprowadź swoją nazwę.

    c. W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD**, który został skopiowany z Azure Portal.

    d. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    e. Przekaż certyfikat, klikając przycisk **Wybierz** obok pozycji **certyfikat**.

    f. **Ustawienia opcjonalne** -
    
    * Włącz hasła — Jeśli ta opcja jest wyłączona, funkcja regularnego hasła w taki sposób, aby użytkownicy mogli korzystać tylko z rejestracji jednokrotnej.

    * Włącz automatyczną obsługę administracyjną — Jeśli ta funkcja jest włączona, adres e-mail używany do logowania zostanie automatycznie porównany z listą użytkowników w taki sposób. Jeśli adres e-mail nie jest zgodny z aktywnym użytkownikiem w ten sposób, automatycznie dodaje nowe konto użytkownika dla osoby podpisującej, żądając wszelkich brakujących informacji.

      > [!NOTE]
      > Użytkownicy dodani za pomocą logowania jednokrotnego są dodawani jako użytkownicy ogólna i nie mają przypisanej roli w systemie. Administrator może przejść i zmodyfikować ich rolę zabezpieczeń jako edytora lub administratora, a także przypisać jedną lub kilka ról schematu organizacyjnego.

    przykład Kliknij przycisk **Zapisz** , aby zachować ustawienia.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do swoich potrzeb.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz metodę, którą **zrobimy**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-way-we-do-test-user"></a>Utwórz sposób testowania użytkownika

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w sposób. W ten sposób obsługujemy funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje, zostanie utworzony nowy po uwierzytelnieniu.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z nami, aby skontaktować się z [zespołem obsługi klienta](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu sposobu, w jaki przejdziemy do kafelka w panelu dostępu, należy zalogować się automatycznie w sposób, w jaki skonfigurujesz Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)