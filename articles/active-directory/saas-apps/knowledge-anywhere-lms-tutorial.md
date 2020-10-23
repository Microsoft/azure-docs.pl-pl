---
title: 'Samouczek: integracja Azure Active Directory z usługą Knowledge gdziekolwiek w systemie LMS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Knowledge Anywhere LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92458987"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Samouczek: integracja systemu LMS z usługą Knowledge gdziekolwiek z Azure Active Directory

W ramach tego samouczka dowiesz się, jak zintegrować system LMS z usługą Knowledge gdziekolwiek z Azure Active Directory (Azure AD). W przypadku integrowania systemu LMS z usługą Azure AD w dowolnym miejscu możesz:

* Kontrola w usłudze Azure AD, która ma dostęp do wiedzy z dowolnego miejsca w systemie LMS.
* Zezwól użytkownikom na automatyczne logowanie do usługi Knowledge gdziekolwiek w systemie LMS z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną obsługą logowania jednokrotnego (SSO) w dowolnym miejscu.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Usługa LMS w dowolnym miejscu obsługuje usługę **SP** zainicjowałą Logowanie jednokrotne i obsługuje Inicjowanie obsługi użytkowników **just in Time** .

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Dodawanie aplikacji Knowledge Anywhere LMS z galerii

Aby skonfigurować integrację aplikacji Knowledge Anywhere LMS z usługą Azure AD, należy dodać aplikację Knowledge Anywhere LMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **wiedza w dowolnym miejscu** .
1. Wybierz pozycję **wiedza w dowolnym miejscu** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą usługi Knowledge BI w dowolnym miejscu przy użyciu użytkownika testowego o nazwie **B. Simon** Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Knowledge gdziekolwiek w systemie LMS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu wiedzy z dowolnego miejsca w systemie LMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
2. Konfigurowanie ustawień rejestracji jednokrotnej w systemie LMS w usłudze **[Knowledge gdziekolwiek](#configure-knowledge-anywhere-lms)** w przypadku aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie informacji o użytkowniku testowym w dowolnym miejscu](#create-knowledge-anywhere-lms-test-user)** w systemie LMS w celu uzyskania odpowiedników B. Simon w bazie wiedzy gdziekolwiek w systemie LMS, która jest połączona z reprezentacją usługi Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z usługą **LMS z dowolnego miejsca** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL odpowiedzi, co zostało opisane w dalszej części tego samouczka.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfiguracja systemu LMS w dowolnym miejscu** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurowanie systemu LMS w usłudze Knowledge Anywhere

1. Aby zautomatyzować konfigurację w ramach systemu LMS w bazie wiedzy z dowolnego miejsca, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Zainstaluj wiedzę z dowolnego miejsca system LMS** przeprowadzi Cię do aplikacji LMS z dowolnego miejsca. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do systemu LMS w usłudze Knowledge gdziekolwiek. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować wiedzę z systemu LMS w dowolnym miejscu, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmowej systemu LMS w dowolnym miejscu jako administrator i wykonaj następujące czynności:

4. Wybierz kartę **Site** (Witryna).

    ![Zrzut ekranu przedstawia kartę lokacja.](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Wybierz kartę **SAML Settings** (Ustawienia języka SAML).

    ![Zrzut ekranu przedstawia stronę z dowolnego miejsca z wybranymi ustawieniami protokołu SAML.](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Kliknij pozycję **Add New** (Dodaj nowe).

    ![Zrzut ekranu przedstawia przycisk Dodaj nowy w ustawieniach dostawcy usług.](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na stronie **Add/Update SAML Settings** (Dodawanie/aktualizowanie ustawień języka SAML), wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę Dodawanie/aktualizowanie ustawień języka SAML, na której można wprowadzić zmiany opisane w tym miejscu.](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Wprowadź nazwę dostawcy tożsamości zgodnie z wymaganiami organizacji. Na przykład:- `Azure`.

    b. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    d. Otwórz plik certyfikatu pobrany z witryny Azure Portal do Notatnika, skopiuj zawartość certyfikatu i wklej go w polu tekstowym **Certificate** (Certyfikat).

    e. W polu tekstowym **adres URL wylogowywania** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    f. Wybierz pozycję **Main Site** (Witryna główna) z listy rozwijanej w polu **Domain** (Domena).

    przykład Skopiuj wartość **SP Entity ID** (Identyfikator jednostki dostawcy usługi) i wklej ją w polu tekstowym **Identifier** (Identyfikator) w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    h. Skopiuj wartość **SP Response(ACS) URL** (Adres URL odpowiedzi dostawcy usługi (ACS)) i wklej go w polu tekstowym **Adres URL odpowiedzi** sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    i. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do wiedzy z dowolnego miejsca w systemie LMS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Knowledge Anywhere LMS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tworzenie użytkownika testowego aplikacji Knowledge Anywhere LMS

W tej sekcji użytkownik o nazwie B. Simon został utworzony w bazie wiedzy z dowolnego miejsca. Aplikacja Knowledge Anywhere LMS obsługuje aprowizację użytkowników typu „just in time”, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Knowledge Anywhere LMS, zostanie utworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka wiedza w dowolnym miejscu w panelu dostępu należy automatycznie zalogować się do systemu LMS w dowolnym miejscu, w którym skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)