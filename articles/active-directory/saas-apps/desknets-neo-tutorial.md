---
title: 'Samouczek: Azure Active Directory (SSO) z desknets NEO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między sieciami Azure Active Directory desknets NEO.
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
ms.openlocfilehash: ef3e024e1b704cc9f8711d9ca78ee916acf72680
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580766"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z integracją aplikacji Desknet's NEO

Z tego samouczka dowiesz się, jak zintegrować narzędzie NEO firmy Desknet z usługą Azure Active Directory (Azure AD). Integrowanie rozwiązania NEO firmy Desknet z usługą Azure AD umożliwia:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji DESKNET's NEO.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji DESKNET's NEO przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Desknet z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* System NEO firmy Desknet obsługuje logowanie **jednokrotne inicjowane** przez sp.

## <a name="adding-desknets-neo-from-the-gallery"></a>Dodawanie aplikacji Desknet's NEO z galerii

Aby skonfigurować integrację rozwiązania NEO firmy Desknet z usługą Azure AD, należy dodać rozwiązanie NEO aplikacji Desknet z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługę.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz w polu wyszukiwania nazwę NEO aplikacji **Desknet.**
1. Wybierz **pozycję DESK firmy Desknet** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla rozwiązania DESKNET

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z wykorzystaniem rozwiązania DESKnet przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w aplikacji DESKNET.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD przy użyciu rozwiązania DESKNET, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji DESKNET —](#configure-desknets-neo-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego APLIKACJI DESKNET](#create-desknets-neo-test-user)** — aby mieć w aplikacji DESKnet odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **DESKnet** znajdź sekcję Zarządzanie i wybierz **pozycję Logowanie pojedynczej.** 
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<CUSTOMER_NAME>.dn-cloud.com`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z zespołem pomocy technicznej klienta [aplikacji DESKnet.](mailto:cloudsupport@desknets.com) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą saml w sekcji Certyfikat podpisywania  **SAML** znajdź pozycję Certyfikat  **(Base64)** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W **sekcji Konfigurowanie aplikacji Desknet's NEO** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji DESKNET's NEO.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **NEO firmy Desknet.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i** grupy w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-desknets-neo-sso"></a>Konfigurowanie logowania jednokrotnego NEO firmy Desknet

1. Zaloguj się do firmowej witryny APLIKACJI DESKNET w witrynie INTERNETOWEJ jako administrator.

1. W menu kliknij ikonę **ustawień linku uwierzytelniania SAML.**

    ![Zrzut ekranu przedstawiający ustawienia linku uwierzytelniania SAML.](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. W **ustawieniach wspólnych kliknij** pozycję **Użyj w uwierzytelniania** SAML Collaboration.

    ![Zrzut ekranu przedstawiający sposób użycia uwierzytelniania SAML.](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Wykonaj poniższe kroki w sekcji **SAML authentication link settings (Ustawienia linku uwierzytelniania SAML).**

    ![Zrzut ekranu przedstawiający sekcję ustawień linku uwierzytelniania SAML.](./media/desknets-neo-tutorial/saml-authentication.png)

    a. W polu **tekstowym Access URL** (Adres URL dostępu) wklej wartość adresu **URL** logowania skopiowaną z Azure Portal.

    b. W polu **tekstowym SP** Entity  ID (Identyfikator jednostki sp) wklej wartość identyfikatora skopiowaną z Azure Portal.

    c. Kliknij **pozycję Wybierz** plik, aby przekazać pobrany plik certyfikatu **(Base64)** z Azure Portal w polu **tekstowym x.509 Certificate (Certyfikat x.509).**

    d. Kliknij **pozycję Zmień**.

### <a name="create-desknets-neo-test-user"></a>Tworzenie użytkownika testowego APLIKACJI DESKNET

1. Zaloguj się do firmowej witryny APLIKACJI DESKNET jako administrator.

1. W **menu kliknij** **ikonę Ustawienia** administratora.

    ![Zrzut ekranu przedstawiający ustawienia administratora.](./media/desknets-neo-tutorial/administrator-settings.png)

1. Kliknij **ikonę** ustawienia i wybierz **pozycję Zarządzanie użytkownikami** w **ustawieniach niestandardowych.**

    ![Zrzut ekranu przedstawiający ustawienia zarządzania użytkownikami.](./media/desknets-neo-tutorial/user-management.png)

1. Kliknij **pozycję Utwórz informacje o użytkowniku.**

    ![Zrzut ekranu przedstawiający przycisk Informacje o użytkowniku.](./media/desknets-neo-tutorial/create-new-user.png)

1. Wypełnij wymagane pola na poniższej stronie i kliknij przycisk **tworzenia**.

    ![Zrzut ekranu przedstawiający sekcję Tworzenie użytkownika.](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania APLIKACJI DESKNET, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania DO APLIKACJI DESKNET i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Kliknięcie kafelka DESKNET NEO w aplikacji Moje aplikacje spowoduje przekierowanie do adresu URL logowania DO APLIKACJI DESKNET. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu układu NEO firmy Desknet można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


