---
title: 'Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą EasySSO dla aplikacji BitBucket | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą EasySSO dla aplikacji BitBucket.
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
ms.openlocfilehash: 56f2eea9dc485c69b6070fda6e9519887f7b30cb
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519660"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego z usługą EasySSO for BitBucket

Z tego samouczka dowiesz się, jak zintegrować usługę EasySSO for BitBucket z usługą Azure Active Directory (Azure AD). Po zintegrowaniu aplikacji EasySSO for BitBucket z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do aplikacji EasySSO for BitBucket.
* Umożliwianie użytkownikom automatycznego logowania do aplikacji EasySSO dla aplikacji BitBucket przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi EasySSO dla aplikacji BitBucket z włączoną obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujemy logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program EasySSO for BitBucket obsługuje logowanie jednokrotne inicjowane przez sp oraz przez idP.
* EasySSO for BitBucket obsługuje aprowizowanie użytkowników typu "just in time".

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Dodawanie aplikacji EasySSO for BitBucket z galerii

Aby skonfigurować integrację aplikacji EasySSO for BitBucket z usługą Azure AD, należy dodać aplikację EasySSO for BitBucket z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konta osobistego konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja.**
1. W sekcji **Dodaj z galerii** wpisz **EasySSO for BitBucket** w polu wyszukiwania.
1. Wybierz **pozycję EasySSO dla aplikacji BitBucket** z wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-bitbucket"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla aplikacji EasySSO dla aplikacji BitBucket

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą EasySSO dla aplikacji BitBucket, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji EasySSO for BitBucket.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą EasySSO dla aplikacji BitBucket, wykonaj następujące kroki:

1. [Skonfiguruj logowanie jednokrotne usługi Azure AD,](#configure-azure-ad-sso) aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Tworzenie użytkownika testowego usługi Azure AD w](#create-an-azure-ad-test-user) celu przetestowania logowania pojedynczego usługi Azure AD z użytkownikiem B.Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. [Skonfiguruj aplikację EasySSO for BitBucket SSO,](#configure-easysso-for-bitbucket-sso) aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego aplikacji EasySSO for BitBucket,](#create-an-easysso-for-bitbucket-test-user) aby mieć w aplikacji EasySSO for BitBucket odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. [Przetestuj logowanie jednokrotne,](#test-sso) aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na stronie Azure Portal **aplikacji EasySSO for BitBucket** znajdź **sekcję** Zarządzanie. Wybierz **pozycję Logowanie pojedyncze.**
1. Na stronie **Select a single sign-on method (Wybieranie metody logowania pojedynczego)** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania pojedynczego za pomocą saml** wybierz ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający konfigurowanie aplikacji Sign-On za pomocą strony SAML z wyróżniona ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **idP,** w sekcji Podstawowa konfiguracja **saml** wprowadź wartości następujących pól:

    a. W polu **tekstowym** Identyfikator wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. W polu **tekstowym Adres URL** odpowiedzi wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Wybierz **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez** spjęcie:

    - W **polu tekstowym Adres URL** logowania wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W razie wątpliwości skontaktuj się z zespołem pomocy technicznej usługi [EasySSO,](mailto:support@techtime.co.nz) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja EasySSO for BitBucket oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu przedstawiający atrybuty domyślne](common/default-attributes.png)

1. Aplikacja EasySSO for BitBucket oczekuje również jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W poniższej tabeli przedstawiono te informacje. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć według wymagań.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Jeśli użytkownicy usługi Azure AD mają skonfigurowany parametr **sAMAccountName,** należy zamapować adres **urn:oid:0.9.2342.19200300.100.1.1** na atrybut **sAMAccountName.**
    
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą języka **SAML** w sekcji Certyfikat podpisywania **SAML** wybierz linki pobierania dla opcji Certyfikat **(Base64)** lub Xml metadanych **federacji.** Zapisz albo na komputerze. Będzie on potrzebny później do skonfigurowania aplikacji BitBucket EasySSO.

    ![Zrzut ekranu przedstawiający sekcję Certyfikat podpisywania SAML z wyróżnionymi linkami pobierania](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Jeśli planujesz ręcznie skonfigurować usługę EasySSO dla usługi BitBucket przy użyciu certyfikatu, musisz również skopiować adres **URL** logowania i identyfikator usługi **Azure AD** i zapisać je na komputerze.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego B.Simon w Azure Portal.

1. W okienku po lewej stronie w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Wszyscy**  >  **użytkownicy.**
1. Wybierz **pozycję Nowy** użytkownik w górnej części ekranu.
1. We **właściwościach** Użytkownika wykonaj następujące kroki:
   1. W **nazwa**, wprowadź `B.Simon` .
   1. W **polach Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole **wyboru Pokaż hasło,** a następnie zapisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do aplikacji EasySSO for BitBucket.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw**  >  **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **EasySSO dla usługi BitBucket.**
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie,** a następnie wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz pozycję **Dodaj użytkownika**. W **oknie dialogowym** Dodawanie przypisania wybierz pozycję **Użytkownicy i grupy.**
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy **Użytkownicy,** a następnie wybierz pozycję **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie ustawiono żadnej roli, zostanie wybrana rola "Dostęp domyślny".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurowanie logowania jednokrotnego usługi EasySSO dla aplikacji BitBucket

1. Aby zautomatyzować konfigurację w aplikacji Zoom, należy Moje aplikacje **rozszerzenia** przeglądarki do bezpiecznego logowania, klikając **pozycję Zainstaluj rozszerzenie**.

    ![Moje aplikacje rozszerzenia](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj aplikację Zoom,** aby skierować Cię do aplikacji Zoom. W tym miejscu podaj poświadczenia administratora, aby zalogować się do aplikacji Zoom. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–10.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Zoom, w innym oknie przeglądarki internetowej zaloguj się do firmowej witryny aplikacji Zoom jako administrator.

1. Przejdź do sekcji **Administracja.**

    ![Zrzut ekranu przedstawiający wystąpienie usługi BitBucket z wyróżniona ikoną koła zębatego](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Znajdź i wybierz **pozycję EasySSO.**

    ![Zrzut ekranu przedstawiający opcję Easy SSO](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Wybierz **pozycję SAML.** W ten sposób zostanie przekierowynyny do sekcji konfiguracji saml.

    ![Zrzut ekranu przedstawiający stronę administratora aplikacji EasySSO z wyróżnionym kolorem SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Wybierz **kartę Certyfikaty** i zostanie przedstawiony następujący ekran:

    ![Zrzut ekranu przedstawiający kartę Certyfikaty z wyróżnieniami różnych opcji](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Znajdź **certyfikat (Base64)** lub plik **metadanych** zapisany w poprzedniej sekcji tego samouczka. Możesz kontynuować w jeden z następujących sposobów:

    - Użyj pliku **metadanych federacji aplikacji** pobranego do pliku lokalnego na komputerze. Wybierz przycisk **radiowy** Przekaż i postępuj zgodnie ze ścieżką specyficzną dla twojego systemu operacyjnego.

    - Otwórz plik **metadanych federacji aplikacji,** aby wyświetlić zawartość pliku w dowolnym edytorze zwykłego tekstu. Skopiuj go do schowka. Wybierz **pozycję Dane** wejściowe i wklej zawartość schowka w polu tekstowym.
 
    - Wykonaj w pełni ręczną konfigurację. Otwórz certyfikat federacji **aplikacji (Base64),** aby wyświetlić zawartość pliku w dowolnym edytorze zwykłego tekstu. Skopiuj go do schowka i wklej do pola tekstowego Certyfikaty podpisywania **tokenu** tożsamości. Następnie przejdź do karty **Ogólne** i wypełnij pola **ADRES URL** powiązania POST i **Identyfikator** jednostki odpowiednimi wartościami dla pól **Adres URL** logowania i Identyfikator usługi **Azure AD,** które zostały zapisane wcześniej.
 
1. Wybierz **pozycję** Zapisz w dolnej części strony. Zobaczysz, że zawartość plików metadanych lub certyfikatów jest analizowana w polach konfiguracji. Konfiguracja aplikacji BitBucket przy użyciu logowania jednokrotnego jest ukończona.

1. Aby przetestować konfigurację, przejdź do karty **Look & Feel (Look & Feel)** i wybierz pozycję SAML Login Button (Przycisk logowania **SAML).** Umożliwia to korzystanie z osobnego przycisku na ekranie logowania usługi BitBucket, w szczególności w celu testowania integracji SAML usługi Azure AD. Możesz pozostawić ten przycisk wł. i skonfigurować jego położenie, kolor i tłumaczenie również dla trybu produkcyjnego.

    ![Zrzut ekranu przedstawiający kartę Look & Feel (Wygląd strony SAML) z wyróżnionym przyciskiem logowania SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Jeśli masz jakiekolwiek problemy, skontaktuj się z zespołem [pomocy technicznej usługi EasySSO.](mailto:support@techtime.co.nz)

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Tworzenie użytkownika testowego aplikacji EasySSO for BitBucket

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji BitBucket. Aplikacja EasySSO for BitBucket obsługuje aprowizowanie użytkowników just in time, które jest domyślnie wyłączone. Aby ją włączyć, należy  jawnie sprawdzić opcję Utwórz użytkownika po pomyślnym zalogowaniu się w sekcji **Ogólne** konfiguracji wtyczki EasySSO. Jeśli użytkownik jeszcze nie istnieje w aplikacji BitBucket, zostanie utworzony po uwierzytelnieniu.

Jeśli jednak nie chcesz włączać automatycznego aprowizowania użytkowników przy pierwszym pobycie, użytkownicy muszą istnieć w katalogach użytkowników, z których korzysta wystąpienie usługi BitBucket. Na przykład może to być katalog LDAP lub Atlassian Crowd.

![Zrzut ekranu przedstawiający sekcję General (Ogólne) konfiguracji wtyczki EasySSO z wyróżnieniami Create user on successful login (Utwórz użytkownika po pomyślnym zalogowaniu)](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji.

#### <a name="sp-initiated"></a>Inicjowane przez sp:

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania usługi BitBucket easySSO, w którym można zainicjować przepływ logowania.

* Przejdź bezpośrednio do strony EasySSO for BitBucket Sign-on URL (Adres URL logowania usługi BitBucket) i z tego adresu zainicjuj przepływ logowania.

#### <a name="idp-initiated"></a>Inicjowane przez IDP:

* Kliknij pozycję **Test this application** in Azure Portal (Przetestuj tę aplikację w aplikacji) i powinno nas na przykład automatycznie zalogować się do aplikacji EasySSO for BitBucket, dla której została ustawiona logowanie jednokrotne

Możesz również użyć usługi Microsoft Moje aplikacje, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka EasySSO for BitBucket w aplikacji Moje aplikacje w przypadku skonfigurowania w trybie sp nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, powinno na celu automatyczne zalogowanie się do aplikacji EasySSO dla aplikacji BitBucket, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi EasySSO dla aplikacji BitBucket można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).