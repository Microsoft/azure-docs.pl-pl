---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Check Point Identity Awareness | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Check Point Identity Awareness.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 68705d4d1f870820e30563d2a197ac56349d2445
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520921"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-identity-awareness"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Check Point Identity Awareness

Z tego samouczka dowiesz się, jak zintegrować usługę Check Point Identity Awareness z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Check Point Identity Awareness z usługą Azure AD możesz:

* Kontroluj w usłudze Azure AD, kto ma dostęp do usługi Check Point Identity Awareness.
* Umożliwianie użytkownikom automatycznego zalogowania się do usługi Check Point Identity Awareness przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Check Point z obsługą logowania jednokrotnego (SSO) usługi Identity Awareness.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Check Point Identity Awareness obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="adding-check-point-identity-awareness-from-the-gallery"></a>Dodawanie Check Point Identity Awareness z galerii

Aby skonfigurować integrację usługi Check Point Identity Awareness z usługą Azure AD, należy dodać usługę Check Point Identity Awareness z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz Check Point **Identity Awareness** w polu wyszukiwania.
1. Wybierz **Check Point Identity Awareness** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-check-point-identity-awareness"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD na Check Point Identity Awareness

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z Check Point Identity Awareness przy użyciu użytkownika testowego **o nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Check Point Identity Awareness.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z Check Point Identity Awareness, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie Check Point identity awareness SSO](#configure-check-point-identity-awareness-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie Check Point testowego usługi Identity Awareness](#create-check-point-identity-awareness-test-user)** — aby mieć w usłudze Check Point Identity Awareness odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal aplikacji **Check Point Identity Awareness** znajdź sekcję Zarządzanie  i wybierz pozycję **Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<GATEWAY_IP>/connect/spPortal/ACS/ID/<IDENTIFIER_UID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<GATEWAY_IP>/connect/spPortal/ACS/Login/<IDENTIFIER_UID>`

    c. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<GATEWAY_IP>/connect`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby [uzyskać te Check Point, skontaktuj](mailto:support@checkpoint.com) się z zespołem pomocy technicznej klienta identity awareness. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą saml w sekcji Certyfikat podpisywania  **SAML** znajdź pozycję Certyfikat  **(Base64)** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W **sekcji Konfigurowanie Check Point tożsamości** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Check Point Identity Awareness.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Check Point Identity Awareness.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie **wybierz pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-check-point-identity-awareness-sso"></a>Konfigurowanie Check Point Identity Awareness SSO

1. Zaloguj się do Check Point usługi Identity Awareness jako administrator.

1. W widoku Serwerów & SmartConsole > **kliknij** pozycję Nowy > Więcej > **użytkownik/tożsamość**> dostawcy tożsamości.

    ![zrzut ekranu nowego dostawcy tożsamości.](./media/check-point-identity-awareness-tutorial/identity-provider.png)

1. Wykonaj następujące kroki w **oknie Nowy dostawca tożsamości.**

    ![zrzut ekranu przedstawiający sekcję Identity Provider (Dostawca tożsamości).](./media/check-point-identity-awareness-tutorial/new-identity-provider.png)

    a. W polu **Brama** wybierz bramę zabezpieczeń, która musi wykonać uwierzytelnianie SAML.

    b. W polu **Usługa** wybierz świadomość **tożsamości** z listy rozwijanej.

    c. Skopiuj **wartość Identyfikator(identyfikator jednostki),** wklej  tę wartość w polu tekstowym Identyfikator w sekcji Podstawowa konfiguracja **języka SAML** w Azure Portal.

    d. Skopiuj **wartość Adres URL** odpowiedzi, wklej ją w polu tekstowym Adres URL **odpowiedzi** w sekcji **Podstawowa konfiguracja saml** w Azure Portal.

    e. Wybierz **pozycję Importuj plik metadanych,** aby przekazać pobrany **certyfikat (Base64)** z Azure Portal.

    > [!NOTE]
    > Możesz również wybrać  opcję Wstaw ręcznie, aby ręcznie wkleić wartości **Identyfikator** jednostki i  Adres **URL** logowania do odpowiednich pól i przekazać plik certyfikatu z Azure Portal.

    f. Kliknij przycisk **OK**.

### <a name="create-check-point-identity-awareness-test-user"></a>Tworzenie Check Point testowego świadomości tożsamości

W tej sekcji utworzysz użytkownika Britta Simon w aplikacji Check Point Identity Awareness. We współpracy [Check Point pomocy technicznej aplikacji Identity Awareness](mailto:support@checkpoint.com) dodaj użytkowników na platformie Check Point Identity Awareness. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do Check Point URL logowania do usługi Identity Awareness, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio Check Point url logowania do usługi Identity Awareness i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Check Point Identity Awareness w Moje aplikacje nastąpi przekierowanie do adresu URL logowania Check Point Identity Awareness. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Check Point tożsamości można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


