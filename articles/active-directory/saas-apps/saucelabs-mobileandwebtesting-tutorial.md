---
title: 'Samouczek: integracja z Azure Active Directoryami za pomocą programu sosy Labs — testy mobilne i sieci Web | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i sosów Labs — testowaniem urządzeń przenośnych i sieci Web.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 087e2d7f2db8f27378b54675095e97256d6aae9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895119"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Samouczek: integracja z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować laboratoria programu sosów — testowanie urządzeń przenośnych i sieci Web z usługą Azure Active Directory (Azure AD).
Integracja z programem sosów Labs — testowanie urządzeń przenośnych i sieci Web za pomocą usługi Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do laboratoriów sosy — mobilnych i internetowych.
* Możesz pozwolić użytkownikom na automatyczne logowanie do pakietu sosów Labs — testowanie mobilne i sieci Web (Logowanie jednokrotne) za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą programu sosy Labs — testowania mobilnego i sieci Web, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Sosy — laboratoria — testy mobilne i sieci Web obsługujące Logowanie jednokrotne

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Sosy Labs — testowanie urządzeń przenośnych i sieci Web obsługuje **dostawcy tożsamości** zainicjowane Logowanie jednokrotne
* Sosy Labs — testowanie urządzeń przenośnych i sieci Web obsługuje **Justowanie użytkowników w czasie**

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Dodawanie laboratoriów sosów — testowania mobilnego i internetowego z galerii

Aby skonfigurować integrację z laboratorium sosów — testowaniem urządzeń przenośnych i sieci Web w usłudze Azure AD, musisz dodać do niej laboratorium sosów — testy mobilne i internetowe z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać do laboratoriów sosów — testy mobilne i internetowe z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz polecenie **sosy Labs — testy mobilne i sieci Web**, wybierz pozycję **sosy Labs — testy mobilne i sieci Web** z panelu wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Sosy Labs — testy mobilne i sieci Web na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego w usłudze Azure AD za pomocą laboratoriów sosy — mobilnych i sieci Web na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, relacja linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach programu sosy Labs — należy ustanowić testy mobilne i sieci Web.

Aby skonfigurować i przetestować Logowanie jednokrotne w usłudze Azure AD przy użyciu programu sosy Labs — testowania mobilnego i sieci Web, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie programu sosy Labs — Rejestracja jednokrotna w ramach testów mobilnych i sieci Web](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie laboratoriów programu sosów — Użytkownik testowy testowania dla urządzeń przenośnych i sieci Web](#create-sauce-labs---mobile-and-web-testing-test-user)** , który ma odpowiednik Britta Simon w ramach programu sosy Labs — testowania mobilnego i sieci Web, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować Logowanie jednokrotne usługi Azure AD za pomocą programu sosy Labs — testowania mobilnego i sieci Web, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **niezgodność aplikacji do testowania — urządzenia przenośne i testy sieci Web** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Sosy Labs — w przypadku domen i adresów URL testowania aplikacji mobilnych i sieci Web](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie programu sosy Labs — urządzenia przenośne i testy sieci Web** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Konfigurowanie oprogramowania sosy Labs — pojedyncze Sign-On testów mobilnych i sieci Web

1. W innym oknie przeglądarki sieci Web Zaloguj się do witryny firmy sosów Labs-Mobile and Web test, jako administrator.

2. Kliknij **ikonę użytkownika** i wybierz kartę **Zarządzanie zespołem** .

    ![Zrzut ekranu pokazujący ikonę "użytkownik" i wybraną listę rozwijaną "Zarządzanie zespołem".](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Wprowadź **nazwę domeny** w polu tekstowym.

    ![Zrzut ekranu pokazujący przykładową nazwę domeny w polu tekstowym.](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Kliknij przycisk **Konfiguruj** kartę.

    ![Zrzut ekranu pokazujący kartę "Konfigurowanie" wybraną w obszarze "Logowanie jednokrotne jest włączone".](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności.

    ![Konfigurowanie logowania jednokrotnego](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Kliknij przycisk **Przeglądaj** i przekaż pobrany plik metadanych z usługi Azure AD.

    b. Zaznacz pole wyboru **Zezwalaj na inicjowanie obsługi just-in-Time** .

    c. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`  
    Na przykład BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia włączenie usługi Britta Simon w celu korzystania z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do laboratorium sosów — testowania mobilnego i sieci Web.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję **sosy Labs — urządzenia przenośne i testy sieci Web**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **sosy Labs — testy mobilne i sieci Web**.

    ![Link do programu sosy Labs — urządzenia przenośne i testy sieci Web na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Tworzenie laboratoriów sosów — użytkownika testowego testowania mobilnego i sieci Web

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w obszarze sosy Labs-Mobile and Web test. Sosy Labs — testowanie urządzeń przenośnych i sieci Web obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w obszarze sosy Labs — testy mobilne i sieci Web, po uwierzytelnieniu zostanie utworzony nowy.

> [!Note]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z pomocą [techniczną — zespół ds. testowania aplikacji mobilnych i internetowych](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka wypadaj laboratoria — urządzenia przenośne i testy w sieci Web w panelu dostępu należy automatycznie zalogować się do usługi sosy Labs — testy mobilne i sieci Web, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)