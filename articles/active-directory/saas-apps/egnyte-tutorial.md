---
title: 'Samouczek: Azure Active Directory integracji z programem Egnyte | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Egnyte.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: 2662b686102a1a4f6aa6db0f7a4052de329def60
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519849"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacji Egnyte

Z tego samouczka dowiesz się, jak zintegrować platformę Egnyte z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Egnyte z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Egnyte.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Egnyte przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Egnyte z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Egnyte obsługuje logowanie **jednokrotne inicjowane** przez sp.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-egnyte-from-the-gallery"></a>Dodawanie aplikacji Egnyte z galerii

Aby skonfigurować integrację aplikacji Egnyte z usługą Azure AD, musisz dodać aplikację Egnyte z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Egnyte** w polu wyszukiwania.
1. Wybierz **pozycję Egnyte** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Egnyte

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD Form.com przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Form.com.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD Form.com, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji Egnyte SSO](#configure-egnyte-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Egnyte](#create-egnyte-test-user)** — aby mieć w aplikacji Egnyte odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji Egnyte** znajdź sekcję Zarządzanie  i wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj wartość przy użyciu rzeczywistego adresu URL Sign-On i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Egnyte](https://www.egnyte.com/corp/contact_egnyte.html), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

5. W sekcji **Konfigurowanie aplikacji Egnyte** skopiuj odpowiednie adresy URL zgodnie ze swoimi wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Egnyte.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Egnyte**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola &quot;Dostęp domyślny&quot;.
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name=&quot;configure-egnyte-sso&quot;></a>Konfigurowanie aplikacji Egnyte SSO

1. W innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Egnyte jako administrator.

2. Kliknij polecenie **Ustawienia**.
   
    ![Ustawienia 1](./media/egnyte-tutorial/settings-tab.png &quot;Ustawienia")

3. W menu kliknij pozycję **Settings (Ustawienia)**.

    ![Menu 1](./media/egnyte-tutorial/menu-tab.png "Menu")

4. Kliknij kartę **Configuration (Konfiguracja)**, a następnie kliknij pozycję **Security (Zabezpieczenia)**.

    ![Bezpieczeństwo](./media/egnyte-tutorial/configuration.png "Zabezpieczenia")

5. W sekcji **Single Sign-On Authentication (Uwierzytelnianie logowania jednokrotnego)** wykonaj następujące kroki:

    ![Uwierzytelnianie przy użyciu logowania pojedynczego](./media/egnyte-tutorial/authentication.png "Uwierzytelnianie przy użyciu logowania pojedynczego")   
    
    a. Dla pozycji **Single sign-on authentication (Uwierzytelnianie logowania jednokrotnego)** wybierz pozycję **SAML 2.0**.
   
    b. Dla pozycji **Identity provider (Dostawca tożsamości)** wybierz pozycję **AzureAD**.
   
    c. Wklej **adres URL logowania** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider login URL (Adres URL logowania dostawcy tożsamości)**.
   
    d. Wklej **identyfikator usługi Azure AD** skopiowany z witryny Azure Portal do pola tekstowego **Identity provider entity ID (Identyfikator jednostki dostawcy tożsamości)**.
      
    e. Otwórz w Notatniku swój certyfikat zakodowany w formacie base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość do schowka, a następnie wklej ją w polu tekstowym **Identity provider certificate (Certyfikat dostawcy tożsamości)**.
   
    f. Dla pozycji **Default user mapping (Domyślne mapowanie użytkownika)** wybierz pozycję **Email address (Adres e-mail)**.
   
    przykład Dla pozycji **Use domain-specific issuer value (Użyj wartości wystawcy specyficznej dla domeny)** wybierz pozycję **disabled (wyłączone)**.
   
    h. Kliknij pozycję **Zapisz**.

### <a name="create-egnyte-test-user"></a>Tworzenie użytkownika testowego aplikacji Egnyte

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji Egnyte, należy ich aprowizować w aplikacji Egnyte. W przypadku aplikacji Egnyte aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do firmowej witryny aplikacji **Egnyte** jako administrator.

2. Przejdź do pozycji **Settings (Ustawienia) \> Users & Groups (Użytkownicy i grupy)**.

3. Kliknij pozycję **Add New User (Dodaj nowego użytkownika)**, a następnie wybierz typ użytkownika, którego chcesz dodać.
   
    ![Użytkownicy](./media/egnyte-tutorial/add-user.png "Użytkownicy")

4. W sekcji **New Power User (Nowy użytkownik zaawansowany)** wykonaj następujące kroki:
    
    ![Nowy użytkownik standardowy](./media/egnyte-tutorial/new-user.png "Nowy użytkownik standardowy")   

    a. W **polu tekstowym** Email (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład **Brittasimon \@ contoso.com**.

    b. W polu tekstowym **Username (Nazwa użytkownika)** wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    c. Wybierz pozycję **Single Sign-On (Logowanie jednokrotne)** dla pozycji **Authentication Type (Typ uwierzytelniania)**.
   
    d. Kliknij pozycję **Zapisz**.
    
    >[!NOTE]
    >Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z powiadomieniem.
    >

>[!NOTE]
>Aby aprowizować konta użytkowników usługi Azure AD, możesz użyć dowolnych innych interfejsów API lub narzędzi do tworzenia kont użytkowników aplikacji Egnyte dostarczonych przez tę firmę.
>

## <a name="test-sso"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Egnyte, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji Egnyte i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Egnyte w Moje aplikacje nastąpi przekierowanie do adresu URL logowania aplikacji Egnyte. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Egnyte można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
