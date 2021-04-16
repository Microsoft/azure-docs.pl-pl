---
title: 'Samouczek: Azure Active Directory integracja z usługą Gigya | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Gigya.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/06/2021
ms.author: jeedes
ms.openlocfilehash: 6956b8ba2dcad0e67caac797a47e5308e649d042
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516073"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Samouczek: Azure Active Directory integracji z usługą Gigya

Z tego samouczka dowiesz się, jak zintegrować platformę Gigya z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Gigya z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Gigya.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Gigya przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Gigya z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Gigya obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="add-gigya-from-the-gallery"></a>Dodawanie aplikacji Gigya z galerii

Aby skonfigurować integrację aplikacji Gigya z usługą Azure AD, musisz dodać aplikację Gigya z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Gigya** w polu wyszukiwania.
1. Wybierz **pozycję Gigya** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-gigya"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Gigya

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacji Gigya przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Gigya.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD w aplikacji Gigya, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji Gigya SSO](#configure-gigya-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Gigya](#create-gigya-test-user)** — aby mieć w aplikacji Gigya odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **Gigya** znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `http://<companyname>.gigya.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Gigya](https://developers.gigya.com/display/GD/Opening+A+Support+Incident). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie aplikacji Gigya** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Gigya.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Gigya**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-gigya-sso"></a>Konfigurowanie aplikacji Gigya SSO

1. W innym oknie przeglądarki internetowej zaloguj się jako administrator do witryny firmowej aplikacji Gigya.

2. Wybierz pozycję **Settings \> SAML Login** (Ustawienia > Logowanie SAML), a następnie kliknij przycisk **Add** (Dodaj).
   
    ![Logowanie SAML](./media/gigya-tutorial/login.png "Logowanie SAML")

3. W sekcji **SAML Login** (Logowanie SAML) wykonaj następujące czynności:
   
    ![Konfiguracja SAML](./media/gigya-tutorial/configuration.png "Konfiguracja SAML")
   
    a. W polu tekstowym **Name** (Nazwa) wpisz nazwę konfiguracji.
   
    b. W **polu** tekstowym Issuer (Wystawca) wklej wartość identyfikatora usługi **Azure AD** skopiowaną z Azure Portal. 
   
    c. W **polu tekstowym Sign-On URL** usługi Single Sign-On Service wklej wartość adresu **URL** logowania skopiowaną z Azure Portal.
   
    d. W **polu tekstowym Format** identyfikatora nazwy wklej wartość formatu identyfikatora nazwy skopiowaną z Azure Portal. 
   
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj zawartość do schowka, a następnie wklej ją w polu tekstowym **Certyfikat X.509**.
   
    f. Kliknij **pozycję Zapisz ustawienia.**

## <a name="create-gigya-test-user"></a>Tworzenie użytkownika testowego aplikacji Gigya

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Gigya, należy aprowizować ich w aplikacji Gigya. W przypadku aplikacji Gigya aprowizacja jest zadaniem ręcznym.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Aby aprowizować konta użytkowników, wykonaj następujące czynności:

1. Zaloguj się do firmowej witryny aplikacji **Gigya** jako administrator.

2. Wybierz pozycję **Admin \> Manage Users** (Administrator > Zarządzaj użytkownikami), a następnie kliknij pozycję **Invite Users** (Zaproś użytkowników).
   
    ![Zarządzanie użytkownikami](./media/gigya-tutorial/users.png "Zarządzanie użytkownikami")

3. W oknie dialogowym Invite users (Zaproś użytkowników) wykonaj następujące kroki:
   
    ![Zapraszanie użytkowników](./media/gigya-tutorial/invite-user.png "Zapraszanie użytkowników")
   
    a. W polu tekstowym **Email** (Adres e-mail) wpisz alias e-mail prawidłowego konta usługi Azure Active Directory, które chcesz aprowizować.
    
    b. Kliknij przycisk **Invite User** (Zaproś użytkownika).
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.
    > 

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Gigya, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania gigya i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Gigya w Moje aplikacje nastąpi przekierowanie do adresu URL logowania gigya. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Gigya można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
