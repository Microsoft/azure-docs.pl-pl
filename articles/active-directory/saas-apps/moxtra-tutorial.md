---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą Moxtra | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Moxtra.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: be63ea7ebf905b825ffe73dfc2b37dcdc3bb88fd
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92507594"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą Moxtra

W tym samouczku dowiesz się, jak zintegrować usługę Moxtra z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Moxtra z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Moxtra.
* Zezwól użytkownikom na automatyczne logowanie się do usługi Moxtra przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Moxtra.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Moxtra obsługuje logowanie jednokrotne w usłudze **SP**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-moxtra-from-the-gallery"></a>Dodawanie Moxtra z galerii

Aby skonfigurować integrację programu Moxtra z usługą Azure AD, musisz dodać Moxtra z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Moxtra** w polu wyszukiwania.
1. Wybierz pozycję **Moxtra** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Moxtra

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Moxtra przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Moxtra.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Moxtra, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-moxtra-sso)** w usłudze Moxtra, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Moxtra](#create-moxtra-test-user)** , aby dysponować odpowiednikiem B. Simon w Moxtra, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Moxtra** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL logowania** wpisz adres URL:  `https://www.moxtra.com/service/#login`

1. Aplikacja Moxtra oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image (obraz)](common/edit-attribute.png)

1. Oprócz powyższych, aplikacja Moxtra oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML. W sekcji Oświadczenia użytkownika w oknie dialogowym Atrybuty użytkownika wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa | Atrybut źródłowy|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < identyfikator usługi Azure AD >

    > [!Note]
    > Wartość atrybutu **idpid** nie jest prawdziwa. Wartość rzeczywistą można uzyskać z sekcji **Konfiguracja Moxtra** w kroku nr 8. 

    1. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    1. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.

    1. Dla opcji Źródło wybierz wartość **Atrybut**.

    1. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    1. Kliknij przycisk **OK** .

    1. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Moxtra** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Moxtra.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Moxtra**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-moxtra-sso"></a>Konfigurowanie logowania jednokrotnego Moxtra

1. W innym oknie przeglądarki Zaloguj się do witryny firmy Moxtra jako administrator.

2. Na pasku narzędzi po lewej stronie kliknij kolejno pozycje **Konsola administracyjna > protokół SAML logowanie**jednokrotne, a następnie kliknij pozycję **Nowy**.
   
    ![Zrzut ekranu przedstawia stronę rejestracji jednokrotnej protokołu SAML z opcją tworzenia nowego logowania jednokrotnego SAML.](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Na stronie **SAML** wykonaj następujące czynności:
   
    ![Zrzut ekranu przedstawia stronę SAML, na której można wprowadzić podane wartości.](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. W polu tekstowym **Nazwa** wpisz nazwę konfiguracji (np.: *SAML*). 
  
    b. W polu tekstowym **Identyfikator jednostki dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD** , który został skopiowany z Azure Portal. 
 
    c. W polu tekstowym **Login URL** (Adres URL logowania) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal. 
 
    d. W polu tekstowym **AuthnContextClassRef** wpisz **urn: języka Oasis: names: TC: SAML: 2.0: AC: Classes: Password**. 
 
    e. W polu tekstowym **Format NameID** wpisz **urn: języka Oasis: names: TC: SAML: 1.1: NameID-format: EmailAddress**. 
 
    f. Otwórz certyfikat pobrany z Azure Portal w Notatniku, skopiuj zawartość, a następnie wklej ją do pola tekstowego **certyfikatu** .    
 
    przykład W polu tekstowym domena e-mail protokołu SAML wpisz swoją domenę poczty e-mail protokołu SAML.    
  
    >[!NOTE]
    >Aby wyświetlić kroki weryfikacji domeny, kliknij przycisk "**i**" poniżej.

    h. Kliknij przycisk **Update** (Aktualizuj).

### <a name="create-moxtra-test-user"></a>Utwórz użytkownika testowego Moxtra

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w Moxtra.

**Aby utworzyć użytkownika o nazwie B. Simon w Moxtra, wykonaj następujące czynności:**

1. Zaloguj się do firmowej witryny Moxtra jako administrator.

1. Na pasku narzędzi po lewej stronie kliknij pozycję **Konsola administracyjna > zarządzanie użytkownikami**, a następnie **Dodaj użytkownika**.
   
    ![Zrzut ekranu przedstawia stronę Zarządzanie użytkownikami z wybraną pozycją Dodaj użytkownika.](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:
  
    a. W polu tekstowym **imię i nazwisko** wpisz **B**.
  
    b. W polu **tekstowym nazwisko** wpisz **Simon**.
  
    c. W polu tekstowym **adres e-mail** wpisz adres e-mail B. Simon, który jest taki sam jak na Azure Portal.
  
    d. W polu tekstowym **dzielenie** wpisz **dev**.
  
    e. W polu tekstowym **dział** wpisz **ją**.
  
    f. Wybierz pozycję **administrator**.
  
    przykład Kliknij pozycję **Dodaj**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Moxtra w panelu dostępu należy automatycznie zalogować się do Moxtra, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Moxtra z usługą Azure AD](https://aad.portal.azure.com/)