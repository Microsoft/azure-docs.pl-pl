---
title: 'Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją FreshDesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e75a45bc4ccd2614520fda39e09996c2286bdbd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74227611"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk

Z tego samouczka dowiesz się, jak zintegrować aplikację FreshDesk z usługą Azure Active Directory (Azure AD).
Zintegrowanie aplikacji FreshDesk z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować za pomocą usługi Azure AD, kto ma dostęp do aplikacji FreshDesk.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji FreshDesk (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją FreshDesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja aplikacji FreshDesk z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja FreshDesk obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="adding-freshdesk-from-the-gallery"></a>Dodawanie aplikacji FreshDesk z galerii

Aby skonfigurować integrację aplikacji FreshDesk z usługą Azure AD, należy dodać aplikację FreshDesk z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać aplikację FreshDesk z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz ciąg **FreshDesk**, wybierz pozycję **FreshDesk** z panelu wyników, a następnie kliknij przycisk **Dodaj**, aby dodać aplikację.

     ![Aplikacja FreshDesk na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w aplikacji FreshDesk, korzystając z danych użytkownika testowego **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji FreshDesk.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji FreshDesk, należy wykonać czynności opisane w poniższych blokach konstrukcyjnych:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego w aplikacji FreshDesk](#configure-freshdesk-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego aplikacji FreshDesk](#create-freshdesk-test-user)** — aby mieć w aplikacji FreshDesk odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD w aplikacji FreshDesk, wykonaj następujące kroki:

1. W witrynie [Azure Portal](https://portal.azure.com/) na stronie integracji aplikacji **FreshDesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Informacje dotyczące domeny i adresów URL logowania jednokrotnego w aplikacji FreshDesk](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub wprowadź inną wartość podaną przez dostawcę aplikacji FreshDesk.

    b. W polu tekstowym **Identyfikator (Identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub wprowadź inną wartość podaną przez dostawcę aplikacji FreshDesk.

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej aplikacji FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja FreshDesk oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna atrybutu **Unikatowy identyfikator użytkownika** to **user.userprincipalname**, ale aplikacja FreshDesk oczekuje, że zostanie ona zamapowana na adres e-mail użytkownika. Do tego celu można użyć atrybutu **user.mail** z listy lub odpowiedniej wartości atrybutu zgodnie z konfiguracją organizacji. 

    ![image](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** edytuj oświadczenia, korzystając z **ikony edycji**, lub dodaj je za pomocą opcji **Dodaj nowe oświadczenie**, aby skonfigurować atrybut tokenu języka SAML, jak pokazano na ilustracji powyżej, a następnie wykonaj następujące czynności:
    
    | Nazwa | Atrybut źródłowy |
    | ---------------| --------------- |
    | Unikatowy identyfikator użytkownika | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK** .

    g. Kliknij przycisk **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. Otwórz **wiersz polecenia** i uruchom następujące polecenia:

    a. Wprowadź wartość `certutil.exe -dump FreshDesk.cer` w wierszu polecenia.

    > [!NOTE]
    > Tutaj plik **FreshDesk.cer** to certyfikat, który został pobrany z witryny Azure Portal.

    b. Skopiuj wartość **Cert Hash(sha256)** i wklej ją w Notatniku. 

9. W sekcji **Konfigurowanie aplikacji FreshDesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-freshdesk-single-sign-on"></a>Konfigurowanie logowania jednokrotnego aplikacji FreshDesk

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji FreshDesk jako administrator.

2. Wybierz **ikonę Settings** (Ustawienia) i w sekcji **Security** (Zabezpieczenia) wykonaj następujące kroki:

    ![Logowanie jednokrotne](./media/freshdesk-tutorial/IC776770.png "Logowanie jednokrotne")
  
    a. Dla opcji **Single Sign On (SSO)** (Logowanie jednokrotne) ustaw wartość **On** (Włączone).

    b. Wybierz pozycję **SAML SSO** (Logowanie jednokrotne SAML).

    c. W polu tekstowym **SAML Login URL** (Adres URL logowania SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    e. W polu tekstowym **Security Certificate Fingerprint** (Odcisk palca certyfikatu cyfrowego) wklej uzyskaną wcześniej wartość **Cert Hash(sha256)**.
  
    f. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon\@yourcompanydomain. Extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz użytkownikowi Britta Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji FreshDesk.

1. W witrynie Azure Portal wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz pozycję **Wszystkie aplikacje**, a następnie wybierz pozycję **FreshDesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wpisz i wybierz **FreshDesk**.

    ![Link aplikacji FreshDesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz, że masz dowolną wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-freshdesk-test-user"></a>Tworzenie użytkownika testowego aplikacji FreshDesk

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji FreshDesk, należy ich aprowizować w aplikacji FreshDesk.  
W przypadku aplikacji FreshDesk aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do swojej dzierżawy aplikacji **FreshDesk**.

2. W menu u góry kliknij pozycję **Admin** (Administrator).

    ![Administracja](./media/freshdesk-tutorial/IC776772.png "Administrator")

3. Na karcie **General Settings** (Ustawienia ogólne) kliknij pozycję **Agents** (Agenci).
  
    ![Agenci](./media/freshdesk-tutorial/IC776773.png "Agenci")

4. Kliknij pozycję **New Agent** (Nowy agent).

    ![Nowy Agent](./media/freshdesk-tutorial/IC776774.png "Nowy Agent")

5. W oknie dialogowym Agent Information (Informacje o agencie) wykonaj następujące kroki:

    ![Informacje o agencie](./media/freshdesk-tutorial/IC776775.png "Informacje o agencie")

    a. W polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta usługi Azure AD, które chcesz aprowizować.

    b. W polu tekstowym **Full Name** (Pełna nazwa) wpisz nazwę konta usługi Azure AD, które chcesz aprowizować.

    c. W polu tekstowym **Title** (Tytuł) wpisz tytuł konta usługi Azure AD, które chcesz aprowizować.

    d. Kliknij przycisk **Zapisz**.

    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim zostanie ono aktywowane.
    >
    >[!NOTE]
    >Do obsługi kont użytkowników usługi Azure AD w usłudze usługi FreshDesk można użyć innych narzędzi do tworzenia kont użytkowników usługi FreshDesk i interfejsów API udostępnionych przez usługę usługi FreshDesk.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FreshDesk na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji FreshDesk, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

