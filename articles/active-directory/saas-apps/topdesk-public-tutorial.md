---
title: 'Samouczek: Azure Active Directory integracji z usługą TOPdesk — public | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory toPdesk - Public.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: b787102065f93588cd796027123d61072e9d5aea
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107601019"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Samouczek: Azure Active Directory integracji z usługą TOPdesk - Public

Z tego samouczka dowiesz się, jak zintegrować usługę TOPdesk - Public z usługą Azure Active Directory (Azure AD). Integracja aplikacji TOPdesk - Public z usługą Azure AD umożliwia:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji TOPdesk - Public.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji TOPdesk - Public przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* TOPdesk — publiczna subskrypcja z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* ToPdesk - Public obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="add-topdesk---public-from-the-gallery"></a>Dodawanie aplikacji TOPdesk - Public z galerii

Aby skonfigurować integrację aplikacji TOPdesk - Public z usługą Azure AD, musisz dodać aplikację TOPdesk - Public z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługę.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **TOPdesk - Public** w polu wyszukiwania.
1. Wybierz **pozycję TOPdesk - Public z** panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji TOPdesk - Public

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą TOPdesk - Public, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji TOPdesk - Public.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą TOPdesk - Public, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji TOPdesk - Public SSO](#configure-topdesk---public-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji TOPdesk — Public](#create-topdesk---public-test-user)** — aby mieć w aplikacji TOPdesk — Public odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **integracji aplikacji TOPdesk - Public** znajdź sekcję Zarządzanie i wybierz pozycję Logowanie **pojedynczej.** 
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4.  W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    >[!NOTE]
    >Plik metadanych **dostawcy** usług można pobrać z sekcji Konfigurowanie aplikacji **TOPdesk - Public Single Sign-On,** co wyjaśniono w dalszej części tego samouczka.

    a. Kliknij pozycję **Przekaż plik metadanych**.
    
    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przesłaniu pliku metadanych wartości **Identyfikator** i Adres **URL** odpowiedzi zostaną automatycznie wypełnione w sekcji Podstawowa konfiguracja saml.

    d. W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net`

    e. W polu **tekstowym Identifier URL** (Adres URL identyfikatora) wypełnij adres URL metadanych aplikacji TOPdesk, który można pobrać z konfiguracji usługi TOPdesk. Powinien on używać następującego wzorca: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`
    
    f. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Jeśli wartości **Identyfikator i** Adres **URL odpowiedzi** nie zostaną wypełnione automatycznie, musisz wprowadzić je ręcznie. W polu Identyfikator postępuj zgodnie ze wzorcem, jak wspomniano powyżej. Wartość adresu URL odpowiedzi można uzyskać w sekcji Konfigurowanie **aplikacji TOPdesk — publiczne** logowanie pojedynczej, co wyjaśniono w dalszej części tego samouczka. Wartość **adresu URL logowania** nie jest prawdziwa, dlatego należy zaktualizować wartość przy użyciu rzeczywistego Sign-On URL. Skontaktuj się z zespołem pomocy technicznej klienta [TOPdesk - Public,](https://my.topdesk.com/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W **sekcji Konfigurowanie aplikacji TOPdesk - Public** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz **pozycję Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji TOPdesk - Public.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **TOPdesk - Public**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i** grupy w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-topdesk---public-sso"></a>Konfigurowanie aplikacji TOPdesk — publiczne logowanie jednokrotne

1. Zaloguj się do **firmowej witryny aplikacji TOPdesk - Public** jako administrator.

2. W menu **TOPdesk** kliknij pozycję **Settings** (Ustawienia).
   
    ![Ustawienia](./media/topdesk-public-tutorial/menu.png "Ustawienia")

3. Kliknij pozycję **Login Settings** (Ustawienia logowania).
   
    ![Ustawienia logowania](./media/topdesk-public-tutorial/login.png "Ustawienia logowania")

4. Rozwiń menu **Login Settings** (Ustawienia logowania), a następnie kliknij pozycję **General** (Ogólne).
   
    ![Ustawienia ogólne](./media/topdesk-public-tutorial/general.png "Ustawienia ogólne")

5. W sekcji **Public** (Publiczna) sekcji SAML login configuration (Konfiguracja logowania **SAML)** wykonaj następujące kroki:
   
    ![Ustawienia techniczne](./media/topdesk-public-tutorial/public.png "Ustawienia techniczne")
   
    a. Kliknij pozycję **Download** (Pobierz), aby pobrać publiczny plik metadanych, a następnie zapisz go lokalnie na komputerze.
   
    b. Otwórz pobrany plik metadanych, a następnie znajdź węzeł **AssertionConsumerService.**

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Skopiuj wartość **AssertionConsumerService** i wklej tę wartość w polu tekstowym **Adres URL** odpowiedzi w sekcji **Podstawowa konfiguracja protokołu SAML.**      
   
6. Aby utworzyć plik certyfikatu, wykonaj następujące kroki:
    
    ![Certyfikat](./media/topdesk-public-tutorial/certificate-file.png "Certyfikat")
    
    a. Otwórz plik metadanych pobrany w witrynie Azure Portal.
    
    b. Rozwiń węzeł **RoleDescriptor**, w którym element **xsi:type** ma wartość **fed:ApplicationServiceType**.
    
    c. Skopiuj wartość węzła **X509Certificate**.
    
    d. Zapisz skopiowaną wartość **X509Certificate** lokalnie na komputerze w pliku.

7. W sekcji **Public** (Publiczne) kliknij przycisk **Add** (Dodaj).
    
    ![Logowanie SAML](./media/topdesk-public-tutorial/add.png "Logowanie SAML")

8. W oknie dialogowym **SAML configuration assistant** (Asystent konfiguracji SAML) wykonaj następujące kroki:
    
    ![Asystent konfiguracji SAML](./media/topdesk-public-tutorial/configuration.png "Asystent konfiguracji SAML")
    
    a. Aby przekazać plik metadanych pobrany w witrynie Azure Portal, w obszarze **Federation Metadata** (Metadane federacji) kliknij przycisk **Browse** (Przeglądaj).

    b. Aby przekazać plik certyfikatu, w obszarze **Certificate (RSA)** (Certyfikat — RSA) kliknij przycisk **Browse** (Przeglądaj).

    c. Aby przekazać plik logo uzyskany od zespołu pomocy technicznej TOPdesk, w obszarze **Logo icon** (Ikona logo) kliknij przycisk **Browse** (Przeglądaj).

    d. W polu tekstowym **User name attribute** (Atrybut nazwy użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. W polu tekstowym **Display name** (Nazwa wyświetlana) wpisz nazwę konfiguracji.

    f. Kliknij pozycję **Zapisz**.

### <a name="create-topdesk---public-test-user"></a>Tworzenie użytkownika testowego aplikacji TOPdesk — Public

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji TOPdesk - Public, należy ich aprowizować w aplikacji TOPdesk - Public. W przypadku aplikacji TOPdesk — public aprowizowanie jest zadaniem ręcznym.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do **firmowej witryny aplikacji TOPdesk — Public** jako administrator.

2. W menu u góry kliknij pozycję **TOPdesk New Support Files Person (TOPdesk \> New Support Files \> \> Person).**
   
    ![Person (Osoba)](./media/topdesk-public-tutorial/files.png "Person (Osoba)")

3. W oknie dialogowym Nowa osoba wykonaj następujące kroki:
   
    ![Nowa osoba](./media/topdesk-public-tutorial/new.png "Nowa osoba")
   
    a. Kliknij kartę General (Ogólne).

    b. W polu **tekstowym Surname** (Nazwisko) wpisz nazwisko użytkownika, na przykład Simon
 
    c. Wybierz **lokację** dla konta.
 
    d. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Aby aprowizować konta użytkowników usługi Azure AD, możesz użyć dowolnych innych interfejsów API lub narzędzi TOPdesk - Public do tworzenia kont użytkowników usługi Azure AD.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowywanie do adresu URL logowania publicznego TOPdesk , pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do strony TOPdesk - Public Sign-on URL (TOPdesk — publiczny adres URL logowania) i z tego adresu zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka TOPdesk - Public w aplikacji Moje aplikacje nastąpi przekierowanie do adresu URL aplikacji TOPdesk - Public Sign-on. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji TOPdesk - Public można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
