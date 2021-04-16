---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą EasySSO for Confluence | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między Azure Active Directory i easySSO for Confluence.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: jeedes
ms.openlocfilehash: ef121112bb4ff3ff6a297677a5c713f642cb3b78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519541"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-confluence"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą EasySSO for Confluence

Z tego samouczka dowiesz się, jak zintegrować usługę EasySSO for Confluence z Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji EasySSO for Confluence z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji Confluence.
* Umożliwianie użytkownikom automatycznego zalogowania się do aplikacji Confluence przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja aplikacji EasySSO for Confluence z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program EasySSO for Confluence obsługuje logowanie jednokrotne inicjowane przez SP **i IDP.**
* Program EasySSO for Confluence obsługuje **aprowizowanie** użytkowników just in time.

## <a name="add-easysso-for-confluence-from-the-gallery"></a>Dodawanie aplikacji EasySSO for Confluence z galerii

Aby skonfigurować integrację aplikacji EasySSO for Confluence z usługą Azure AD, należy dodać aplikację EasySSO for Confluence z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **EasySSO for Confluence** w polu wyszukiwania.
1. Wybierz **pozycję EasySSO for Confluence** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-confluence"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji EasySSO for Confluence

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z aplikacji EasySSO for Confluence, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji EasySSO for Confluence.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z aplikacji EasySSO for Confluence, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie aplikacji EasySSO for Confluence SSO](#configure-easysso-for-confluence-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji EasySSO for Confluence](#create-easysso-for-confluence-test-user)** — aby mieć w aplikacji EasySSO for Confluence odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W aplikacji Azure Portal na **stronie integracji aplikacji EasySSO for Confluence** znajdź sekcję Zarządzanie i wybierz **pozycję Logowanie jednokrotne.** 
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym  przez dostawcy tożsamości, w sekcji Podstawowa konfiguracja protokołu **SAML** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** spjęcie:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W razie wątpliwości skontaktuj się z zespołem pomocy technicznej usługi [EasySSO,](mailto:support@techtime.co.nz) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja EasySSO for Confluence oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych aplikacja EasySSO for Confluence oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML, jak pokazano poniżej. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Jeśli użytkownicy usługi Azure AD mają skonfigurowany parametr **sAMAccountName,** należy zamapować adres **urn:oid:0.9.2342.19200300.100.1.1** na atrybut **sAMAccountName.**
    
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka SAML w sekcji  Certyfikat **podpisywania** **SAML** kliknij pozycję Linki pobierania dla opcji Certyfikat **(Base64)** lub Plik **XML** metadanych federacji i zapisz albo wszystkie na komputerze. Będzie on potrzebny później do skonfigurowania aplikacji Confluence EasySSO.

    ![Link do pobierania certyfikatu](./media/easysso-for-confluence-tutorial/certificate.png)
    
    Jeśli planujesz ręcznie przeprowadzić konfigurację aplikacji EasySSO for Confluence przy użyciu certyfikatu, musisz również skopiować adres **URL** logowania i identyfikator usługi **Azure AD** z poniższej sekcji i zapisać je na komputerze.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji EasySSO for Confluence.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **EasySSO for Confluence.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-easysso-for-confluence-sso"></a>Konfigurowanie aplikacji EasySSO for Confluence SSO

1. Aby zautomatyzować konfigurację w aplikacji EasySSO for Confluence, musisz zainstalować rozszerzenie przeglądarki **do** bezpiecznego logowania Moje aplikacje, klikając pozycję Zainstaluj **rozszerzenie**.

    ![Moje aplikacje rozszerzenia](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację EasySSO for Confluence,** aby skierować Cię do aplikacji EasySSO for Confluence. W tym miejscu podaj poświadczenia administratora, aby zalogować się do aplikacji EasySSO for Confluence. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–9.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować usługę EasySSO for Confluence, zaloguj się do wystąpienia usługi Atlassian Confluence z uprawnieniami administratora i przejdź do sekcji **Zarządzanie aplikacjami.** 

    ![Zarządzanie aplikacjami](./media/easysso-for-confluence-tutorial/confluence-admin-1.png)

2. Po lewej stronie znajdź pozycję **EasySSO** i kliknij ją. Następnie kliknij **przycisk Konfiguruj.**

    ![Easy SSO](./media/easysso-for-confluence-tutorial/confluence-admin-2.png)

3. Wybierz **opcję SAML.** Spowoduje to przejdę do sekcji konfiguracji saml.

    ![SAML](./media/easysso-for-confluence-tutorial/confluence-admin-3.png)

4. Wybierz **kartę** Certyfikaty u góry. Zostanie przedstawiony następujący ekran: 

    ![Adres URL metadanych](./media/easysso-for-confluence-tutorial/confluence-admin-4.png)

5. Teraz znajdź pozycję **Certyfikat (Base64)** lub Plik metadanych, które zostały zapisane we wcześniejszych krokach konfiguracji logowania **jednokrotnego usługi Azure AD.**  Aby kontynuować, dostępne są następujące opcje:

    a. Użyj pliku metadanych **federacji aplikacji** pobranego do pliku lokalnego na komputerze. Wybierz **przycisk radiowy** Przekaż i postępuj zgodnie z oknem dialogowym przekazywania pliku specyficznym dla twojego systemu operacyjnego

    **OR**

    b. Otwórz plik **metadanych federacji** aplikacji, aby wyświetlić zawartość pliku (w dowolnym edytorze zwykłego tekstu) i skopiować go do schowka. Wybierz **opcję Dane** wejściowe i wklej zawartość schowka w polu tekstowym.
 
    **OR**

    c. W pełni ręczna konfiguracja. Otwórz certyfikat federacji **aplikacji (Base64),** aby wyświetlić zawartość pliku (w dowolnym edytorze zwykłego tekstu) i skopiować ją do schowka. Wklej go w **polu tekstowym IdP Token Signing Certificates (Certyfikaty podpisywania tokenu** UD). Następnie przejdź do **karty Ogólne** i wypełnij pola POST Binding URL (Adres URL powiązania **POST)** i **Entity ID (Identyfikator** jednostki) odpowiednimi wartościami adresu **URL** logowania i identyfikatora usługi **Azure AD,** które zostały zapisane wcześniej.
 
6. Kliknij **przycisk** Zapisz w dolnej części strony. Zawartość plików metadanych lub certyfikatu jest analizowana w polach konfiguracji. Konfiguracja aplikacji EasySSO for Confluence jest ukończona.

7. Aby uzyskać najlepsze środowisko testowania, przejdź do karty **Look & Feel** (Wygląd i sposób testowania) i zaznacz opcję **SAML Login Button (Przycisk logowania SAML).** Spowoduje to włączenie osobnego przycisku na ekranie logowania aplikacji Confluence specjalnie w celu przetestowania integracji saml usługi Azure AD. Możesz pozostawić ten przycisk wł. i skonfigurować jego położenie, kolor i tłumaczenie również dla trybu produkcyjnego.

    ![Look & Feel](./media/easysso-for-confluence-tutorial/confluence-admin-5.png)

    > [!NOTE]
    > W przypadku jakichkolwiek problemów skontaktuj się z zespołem pomocy [technicznej usługi EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-easysso-for-confluence-test-user"></a>Tworzenie użytkownika testowego aplikacji EasySSO for Confluence

W tej sekcji w aplikacji Confluence jest tworzony użytkownik o nazwie Britta Simon. Aplikacja EasySSO for Confluence obsługuje aprowizowanie użytkowników just in time, które jest **domyślnie** wyłączone. Aby włączyć aprowizowanie użytkowników, należy jawnie wybrać opcję Utwórz użytkownika po pomyślnym zalogowaniu w sekcji Ogólne konfiguracji wtyczki EasySSO.  Jeśli użytkownik jeszcze nie istnieje w aplikacji Confluence, zostanie utworzony po uwierzytelnieniu.

Jeśli jednak nie chcesz włączyć automatycznego aprowizowania użytkowników przy pierwszym logowaniu użytkownika, użytkownicy muszą istnieć w katalogach użytkowników zaplecza, z których korzysta wystąpienie aplikacji Confluence, takich jak LDAP lub Atlassian Crowd.

![Aprowizowanie użytkowników](./media/easysso-for-confluence-tutorial/confluence-admin-6.png)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

### <a name="idp-initiated-workflow"></a>Przepływ pracy inicjowany przez idP

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu Moje aplikacje.

Po kliknięciu kafelka EasySSO for Confluence w aplikacji Moje aplikacje powinno na celu automatyczne zalogowanie się do wystąpienia aplikacji Confluence, dla którego została ustawiona logowanie jednokrotne. Aby uzyskać więcej informacji na Moje aplikacje, [zobacz Introduction to the Moje aplikacje (Wprowadzenie do Moje aplikacje](../user-help/my-apps-portal-end-user-access.md)).

### <a name="sp-initiated-workflow"></a>Przepływ pracy inicjowany przez sp

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu przycisku logowania confluence **SAML.**

![Logowanie SAML użytkownika](./media/easysso-for-confluence-tutorial/confluence-admin-7.png)

W tym scenariuszu zakłada się, że włączono przycisk **logowania SAML** na karcie **Look & Feel** na stronie konfiguracji aplikacji Confluence EasySSO (patrz powyżej). Otwórz adres URL logowania aplikacji Confluence w trybie incognito przeglądarki, aby uniknąć zakłóceń w istniejących sesjach. Kliknij **przycisk SAML Login (Logowanie SAML).** Nastąpi przekierowanie do przepływu uwierzytelniania użytkowników usługi Azure AD. Po pomyślnym zakończeniu nastąpi przekierowanie z powrotem do wystąpienia aplikacji Confluence jako uwierzytelniony użytkownik za pośrednictwem saml.

Po przekierowaniu z powrotem z usługi Azure AD może wystąpić następujący ekran

![Ekran błędu easySSO](./media/easysso-for-confluence-tutorial/confluence-admin-8.png)

W takim przypadku należy postępować zgodnie z instrukcjami na [tej stronie,]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) aby uzyskać dostęp do pliku **atlassian-confluence.log.** Szczegóły błędu będą dostępne za pomocą identyfikatora odwołania znalezionego na stronie błędu easySSO.

Jeśli masz jakiekolwiek problemy z podsumowaniem komunikatów dziennika, skontaktuj się z zespołem pomocy technicznej [aplikacji EasySSO.](mailto:support@techtime.co.nz)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu aplikacji EasySSO for Confluence można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).