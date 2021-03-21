---
title: 'Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją FreshDesk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651908"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Samouczek: integracja Azure Active Directory z usługą usługi FreshDesk

W tym samouczku dowiesz się, jak zintegrować usługę usługi FreshDesk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi usługi FreshDesk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi FreshDesk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi usługi FreshDesk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) usługi FreshDesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja FreshDesk obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-freshdesk-from-the-gallery"></a>Dodaj usługi FreshDesk z galerii

Aby skonfigurować integrację aplikacji FreshDesk z usługą Azure AD, należy dodać aplikację FreshDesk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **usługi FreshDesk** w polu wyszukiwania.
1. Wybierz pozycję **usługi FreshDesk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla usługi FreshDesk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą usługi FreshDesk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usługi FreshDesk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą usługi FreshDesk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-freshdesk-sso)** w usłudze usługi FreshDesk, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji FreshDesk](#create-freshdesk-test-user)** — aby mieć w aplikacji FreshDesk odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

1. W Azure Portal na stronie integracja aplikacji **usługi FreshDesk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub dowolna inna wartość usługi FreshDesk.

    b. W polu tekstowym **Identyfikator (Identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<tenant-name>.freshdesk.com` lub wprowadź inną wartość podaną przez dostawcę aplikacji FreshDesk.
     
    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<tenant-name>.freshdesk.com/login/saml`
    
    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej aplikacji FreshDesk](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja FreshDesk oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych, natomiast **unikatowy identyfikator użytkownika** jest mapowany na **User. userPrincipalName** , ale usługi FreshDesk oczekuje na mapowanie tego żądania z **użytkownikiem. mail**, dlatego należy edytować mapowanie atrybutu, klikając ikonę Edytuj i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **set-up usługi FreshDesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)


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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi usługi FreshDesk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **usługi FreshDesk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-freshdesk-sso"></a>Konfigurowanie logowania jednokrotnego usługi FreshDesk

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji FreshDesk jako administrator.

2. Wybierz **ikonę zabezpieczenia** i w sekcji **zabezpieczenia** wykonaj następujące czynności:

    ![Logowanie jednokrotne](./media/freshdesk-tutorial/configure-1.png "Logowanie jednokrotne")
  
    a. W przypadku **logowania jednokrotnego** wybierz pozycję **włączone**.

    b. W **metodzie logowania** wybierz pozycję **SAML SSO**.

    c. W **identyfikatorze jednostki podanej przez** pole tekstowe dostawcy tożsamości wklej wartość **identyfikatora jednostki** , która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL logowania JEDNOkrotnego SAML** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    e. W **opcjach podpisywania** wybierz **tylko podpisane potwierdzenia** z listy rozwijanej.

    f. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

    przykład W polu tekstowym **certyfikat zabezpieczeń** wklej wartość **certyfikatu (base64)** , którą uzyskano wcześniej.
  
    h. Kliknij pozycję **Zapisz**.

## <a name="create-freshdesk-test-user"></a>Tworzenie użytkownika testowego aplikacji FreshDesk

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji FreshDesk, należy ich aprowizować w aplikacji FreshDesk.  
W przypadku aplikacji FreshDesk aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do swojej dzierżawy aplikacji **FreshDesk**.

1. W menu po lewej stronie kliknij pozycję **administrator** , a następnie na karcie **Ustawienia ogólne** kliknij pozycję **agenci**.
  
    ![Agenci](./media/freshdesk-tutorial/create-user-1.png "Agenci")

1. Kliknij pozycję **New Agent** (Nowy agent).

    ![Nowy Agent](./media/freshdesk-tutorial/create-user-2.png "Nowy Agent")

1. W oknie dialogowym Informacje o agencie wprowadź wymagane pola i kliknij przycisk **Utwórz agenta**.

    ![Informacje o agencie](./media/freshdesk-tutorial/create-user-3.png "Informacje o agencie")

    >[!NOTE]
    >Właściciel konta usługi Azure AD otrzyma wiadomość e-mail zawierającą link do potwierdzenia konta, zanim zostanie ono aktywowane.
    >
    >[!NOTE]
    >Do obsługi kont użytkowników usługi Azure AD w usłudze usługi FreshDesk można użyć innych narzędzi do tworzenia kont użytkowników usługi FreshDesk i interfejsów API udostępnionych przez usługę usługi FreshDesk.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania usługi FreshDesk, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania usługi FreshDesk i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka usługi FreshDesk w obszarze Moje aplikacje należy automatycznie zalogować się do usługi FreshDesk, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi FreshDesk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).