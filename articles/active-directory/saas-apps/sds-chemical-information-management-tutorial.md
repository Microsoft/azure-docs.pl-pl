---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą SDS & Chemical Information Management | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedynczej aplikacji Azure Active Directory SDS & information management.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: e83274c404980549063b9a94a9118540f1ec3bb3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509335"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sds--chemical-information-management"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą SDS & Information Management

Z tego samouczka dowiesz się, jak zintegrować usługę SDS & Chemical Information Management z usługą Azure Active Directory (Azure AD). Po zintegrowaniu rozwiązania SDS & informacji chemicznych z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do magazynu SDS & informacji chemicznych.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji SDS & Information Management przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja & SDS & z obsługą logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Magazyn SDS & Information Management obsługuje logowanie **jednokrotne inicjowane** przez sp.

* Magazyn SDS & information management obsługuje **aprowizowanie** użytkowników just in time.


## <a name="adding-sds--chemical-information-management-from-the-gallery"></a>Dodawanie aplikacji SDS & do zarządzania informacjami chemicznymi z galerii

Aby skonfigurować integrację aplikacji SDS & Chemical Information Management z usługą Azure AD, należy dodać aplikację SDS & Chemical Information Management z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **SDS & Zarządzanie** informacjami chemicznymi w polu wyszukiwania.
1. Wybierz **pozycję SDS & Information Management** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-sds--chemical-information-management"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji SDS & informacji chemicznych

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z & SDS information Management przy użyciu użytkownika **testowego O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji SDS & Information Management.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z & SDS, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji SDS & SSO do zarządzania informacjami](#configure-sds--chemical-information-management-sso)** chemicznymi — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego](#create-sds--chemical-information-management-test-user)** aplikacji SDS & Chemical Information Management — aby mieć w aplikacji SDS & Chemical Information Management odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji SDS & Do** zarządzania informacjami chemicznymi znajdź  sekcję Zarządzanie i wybierz pozycję **Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://cs.cloudsds.com/saml/<ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://cs.cloudsds.com/saml/<ID>/consumeAssertion`
    
    c. W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z następującego wzorca: `https://cs.cloudsds.com/saml/<ID>/Login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i Sign-On URL. W celu uzyskania tych & skontaktuj się z zespołem pomocy technicznej klienta ds. zarządzania informacjami chemicznymi firmy [SDS.](mailto:info@cloudsds.com) Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** w sekcji Certyfikat podpisywania **SAML** kliknij przycisk kopiowania, aby skopiować adres **URL** metadanych federacji aplikacji i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)
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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji SDS & Chemiczne zarządzanie informacjami.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **SDS & Informacje chemiczne.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-sds--chemical-information-management-sso"></a>Konfigurowanie logowania jednokrotnego & SDS & Information Management

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **SDS & Chemical Information Management,** należy wysłać adres **URL** metadanych federacji aplikacji do zespołu pomocy technicznej aplikacji [SDS & Chemical Information Management.](mailto:info@cloudsds.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-sds--chemical-information-management-test-user"></a>Tworzenie użytkownika testowego & SDS information Management

W tej sekcji w aplikacji SDS & Information Management jest tworzony użytkownik o nazwie Britta Simon. Magazyn SDS & information management obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w programie SDS & information management, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowywanie do & SDS i adresu URL logowania do zarządzania informacjami chemicznymi, gdzie można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do & SDS i zainicjuj przepływ logowania za pomocą adresu URL logowania do zarządzania informacjami chemicznymi.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka SDS & Informacje chemiczne na stronie Moje aplikacje spowoduje to przekierowanie do adresu URL logowania & SDS & informacji chemicznych. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu modelu SDS & informacji chemicznych można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


