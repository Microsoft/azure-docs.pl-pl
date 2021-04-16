---
title: 'Samouczek: Azure Active Directory integracji z usługą Workfront | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Workfront.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/23/2021
ms.author: jeedes
ms.openlocfilehash: cebfca387f1a7dc34f70085966f5b6e8e212f953
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478476"
---
# <a name="tutorial-azure-active-directory-integration-with-workfront"></a>Samouczek: Azure Active Directory integracji z usługą Workfront

Z tego samouczka dowiesz się, jak zintegrować usługę Workfront z usługą Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Workfront z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Workfront.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Workfront przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Workfront potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Workfront z obsługą logowania pojedynczego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Workfront obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="add-workfront-from-the-gallery"></a>Dodawanie aplikacji Workfront z galerii

Aby skonfigurować integrację aplikacji Workfront z usługą Azure AD, musisz dodać aplikację Workfront z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Workfront** w polu wyszukiwania.
1. Wybierz **pozycję Workfront** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-workfront"></a>Konfigurowanie i testowanie usługi Azure AD SSO for Workfront

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą Workfront przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Workfront.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Workfront, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w](#configure-workfront-sso)** aplikacji Workfront — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Workfront](#create-workfront-test-user)** — aby mieć w aplikacji Workfront odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji Workfront** znajdź sekcję Zarządzanie  i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.attask-ondemand.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.attasksandbox.com/SAML2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z zespołem pomocy technicznej klienta aplikacji [Workfront,](https://www.workfront.com/services-and-support) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W **sekcji Konfigurowanie aplikacji Workfront** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Workfront.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Workfront.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-workfront-sso"></a>Konfigurowanie logowania jednokrotnego w aplikacji Workfront

1. Zaloguj się do firmowej witryny aplikacji Workfront jako administrator.

2. Przejdź do **opcji Single Sign On Configuration (Konfiguracja logowania pojedynczego).**

3. W **oknie dialogowym Single Sign-On (Logowanie** pojedynczej) wykonaj następujące kroki
    
    ![Konfigurowanie logowania jednokrotnego](./media/workfront-tutorial/single-sign-on.png)
   
    a. Jako **typ** wybierz **pozycję SAML 2.0.**
   
    b. Wybierz **pozycję Service Provider ID (Identyfikator dostawcy usług).**
   
    c. Wklej adres **URL logowania** w **polu tekstowym Adres URL** portalu logowania.
   
    d. Wklej adres URL wylogowywu **w polu tekstowym Sign-Out URL (Adres URL** wylogowywu). 
   
    e. Wklej **pole wyboru Zmień adres URL** hasła w polu **tekstowym Zmień** adres URL hasła.
   
    f. Kliknij pozycję **Zapisz**.

### <a name="create-workfront-test-user"></a>Tworzenie użytkownika testowego aplikacji Workfront

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Workfront.

**Aby utworzyć użytkownika o nazwie Britta Simon w aplikacji Workfront, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji Workfront jako administrator.
 
2. W menu u góry kliknij pozycję **People (Osoby).**
 
3. Kliknij **pozycję Nowa osoba.** 

4. W oknie dialogowym Nowa osoba wykonaj następujące czynności:
   
    ![Tworzenie użytkownika testowego aplikacji Workfront](./media/workfront-tutorial/add-person.png)
   
    a. W polu **tekstowym First Name** (Imię) wpisz "Britta".
   
    b. W polu **tekstowym Last Name** (Nazwisko) wpisz "Simon".
   
    c. W polu **tekstowym Email Address** (Adres e-mail) wpisz adres e-mail użytkownika Britta Simon w Azure Active Directory.
   
    d. Kliknij przycisk **Add Person** (Dodaj osobę).

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Workfront, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania w aplikacji Workfront i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Kliknięcie kafelka Workfront w Moje aplikacje spowoduje przekierowanie do adresu URL logowania w aplikacji Workfront. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Workfront można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozszerza zakres dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
