---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacji Cerby | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory Cerby.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: 53813bd692f1e621c89e234748f2f9a2d343684b
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520928"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cerby"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z aplikacji Cerby

Z tego samouczka dowiesz się, jak zintegrować narzędzie Cerby z usługą Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Cerby z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Cerby.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Cerby przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Cerby z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Cerby obsługuje logowanie **jednokrotne inicjowane** przez spedytora.

* Cerby obsługuje **aprowizowanie** użytkowników just in time.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="adding-cerby-from-the-gallery"></a>Dodawanie aplikacji Cerby z galerii

Aby skonfigurować integrację aplikacji Cerby z usługą Azure AD, musisz dodać aplikację Cerby z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Cerby** w polu wyszukiwania.
1. Wybierz **pozycję Cerby** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cerby"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Cerby

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacji Cerby przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Cerby.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacji Cerby, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w](#configure-cerby-sso)** aplikacji Cerby — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Cerby](#create-cerby-test-user)** — aby mieć w aplikacji Cerby odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W aplikacji Azure Portal na **stronie integracji aplikacji Cerby** znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie się.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `urn:amazon:cognito:sp:<ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CustomerName>-cerbyauth.auth.us-east-2.amazoncognito.com/saml2/idpresponse`

    c. W **polu tekstowym Adres URL** logowania wpisz adres URL, korzystając z jednego z następujących wzorców:

    | Adres URL logowania |
    |--------|
    | `https://app.cerby.com` |
    | `https://<CustomerName>.cerby.com` |
    |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z zespołem pomocy technicznej klienta aplikacji [Cerby,](mailto:help@cerby.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą saml w sekcji Certyfikat podpisywania  **SAML** znajdź pozycję Certyfikat  **(Base64)** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W **sekcji Konfigurowanie aplikacji Cerby** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji Azure Portal B.Simon.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy,** a następnie wybierz pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Cerby.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Cerby**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-cerby-sso"></a>Konfigurowanie aplikacji Cerby SSO

Aby skonfigurować logowanie pojedyncze po stronie aplikacji **Cerby,** musisz wysłać pobrany certyfikat **(Base64)** i odpowiednie adresy URL skopiowane z aplikacji Azure Portal do zespołu pomocy technicznej aplikacji [Cerby.](mailto:help@cerby.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cerby-test-user"></a>Tworzenie użytkownika testowego aplikacji Cerby

W tej sekcji w aplikacji Cerby jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Cerby obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Cerby, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Cerby, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania cerby i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Cerby w Moje aplikacje nastąpi przekierowanie do adresu URL logowania cerby. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Cerby można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


