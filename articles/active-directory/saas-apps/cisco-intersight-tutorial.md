---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Cisco Intersight | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Cisco Intersight.
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
ms.openlocfilehash: 6efec61ec5866547ab6b432cb9c566642f3191ff
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107580760"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-intersight"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą Cisco Intersight

Z tego samouczka dowiesz się, jak zintegrować usługę Cisco Intersight z usługą Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji Cisco Intersight z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Cisco Intersight.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Cisco Intersight przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji Cisco Intersight z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Cisco Intersight obsługuje logowanie **jednokrotne inicjowane** przez sp.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="adding-cisco-intersight-from-the-gallery"></a>Dodawanie aplikacji Cisco Intersight z galerii

Aby skonfigurować integrację aplikacji Cisco Intersight z usługą Azure AD, musisz dodać aplikację Cisco Intersight z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **Cisco Intersight** w polu wyszukiwania.
1. Wybierz **pozycję Cisco Intersight** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-cisco-intersight"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji Cisco Intersight

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą Cisco Intersight przy użyciu użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji Cisco Intersight.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Cisco Intersight, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedyncze usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji Cisco Intersight SSO](#configure-cisco-intersight-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Cisco Intersight](#create-cisco-intersight-test-user)** — aby mieć w aplikacji Cisco Intersight odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal aplikacji na stronie **integracji aplikacji Cisco Intersight** znajdź sekcję Zarządzanie i wybierz **pozycję Logowanie pojedyncze.** 
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz następujący adres URL: `https://intersight.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL: `www.intersight.com`

1. Aplikacja Cisco Intersight oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia przykład tego działania. Wartość domyślna **unikatowego** identyfikatora użytkownika to **user.userprincipalname,** ale aplikacja Cisco Intersight oczekuje, że zostanie ona zamapowana na adres e-mail użytkownika. W tym celu możesz użyć **atrybutu user.mail** z listy lub użyć odpowiedniej wartości atrybutu na podstawie konfiguracji organizacji.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych aplikacja Cisco Intersight oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML, jak pokazano poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | -------------- | --------- |
    | Imię | user.givenname |
    | Last_name | user.surname |
    | memberOf | user.groups |

1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka SAML w sekcji Certyfikat podpisywania  **SAML** znajdź plik **XML** metadanych **federacji** i wybierz pozycję Pobierz, aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W **sekcji Konfigurowanie aplikacji Cisco Intersight** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w aplikacji o nazwie Azure Portal B.Simon.

1. W okienku po lewej stronie Azure Portal pozycję Azure Active Directory **pozycję** **Użytkownicy,** a następnie pozycję **Wszyscy użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownik wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **polu Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Cisco Intersight.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz **pozycję Cisco Intersight.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy w** **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-cisco-intersight-sso"></a>Konfigurowanie aplikacji Cisco Intersight SSO

Aby skonfigurować logowanie pojedyncze po stronie **aplikacji Cisco Intersight,** musisz wysłać pobrany plik XML metadanych federacji i odpowiednie adresy **URL** skopiowane z aplikacji Azure Portal zespołowi pomocy technicznej aplikacji [Cisco Intersight.](mailto:intersight-feedback@cisco.com) Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-cisco-intersight-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Intersight

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Cisco Intersight. We współpracy z zespołem pomocy technicznej aplikacji [Cisco Intersight](mailto:intersight-feedback@cisco.com) dodaj użytkowników na platformie Cisco Intersight. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania aplikacji Cisco Intersight, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania aplikacji Cisco Intersight i z tego adresu zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Cisco Intersight w Moje aplikacje nastąpi przekierowanie do adresu URL logowania aplikacji Cisco Intersight. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji Cisco Intersight można wymusić kontrolę sesji, która chroni eksfiltrację i przefiltrowanie poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


