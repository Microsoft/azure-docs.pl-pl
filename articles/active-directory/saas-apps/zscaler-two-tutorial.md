---
title: 'Samouczek: Azure Active Directory integracji z usługą Zscaler Two | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie pojedyncze między Azure Active Directory i Zscaler Two.
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
ms.openlocfilehash: b4d39492a05ee90c0ba2e081336d7b04579a5c43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519098"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-two"></a>Samouczek: Azure Active Directory integracji z usługą Zscaler Two

Z tego samouczka dowiesz się, jak zintegrować usługę Zscaler Two z Azure Active Directory (Azure AD). Po zintegrowaniu rozwiązania Zscaler Two z usługą Azure AD można:

* Kontroluj w usłudze Azure AD, kto ma dostęp do rozwiązania Zscaler Two.
* Umożliwianie użytkownikom automatycznego zalogowania się do rozwiązania Zscaler Two przy użyciu kont usługi Azure AD.
* Zarządzanie kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji usługi Azure AD z usługą Zscaler Two potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).
* Zscaler Two single sign-on enabled subscription (Subskrypcja usługi Zscaler Two z obsługą logowania pojedynczego).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Zscaler Two obsługuje logowanie **jednokrotne inicjowane** przez sp.

* Zscaler Two obsługuje **aprowizowanie** użytkowników just in time.

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc w jednej dzierżawie można skonfigurować tylko jedno wystąpienie.

## <a name="add-zscaler-two-from-the-gallery"></a>Dodawanie aplikacji Zscaler Two z galerii

Aby skonfigurować integrację aplikacji Zscaler Two z usługą Azure AD, musisz dodać rozwiązanie Zscaler Two z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do konta Azure Portal przy użyciu konta służbowego lub osobistego konta konto Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **Azure Active Directory** usługi.
1. Przejdź do opcji **Aplikacje dla przedsiębiorstw,** a następnie wybierz **pozycję Wszystkie aplikacje.**
1. Aby dodać nową aplikację, wybierz **pozycję Nowa aplikacja.**
1. W **sekcji Dodaj z galerii** wpisz **Zscaler Two** w polu wyszukiwania.
1. Wybierz **pozycję Zscaler Two** z panelu wyników, a następnie dodaj aplikację. Zaczekaj kilka sekund na dodanie aplikacji do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-two"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla rozwiązania Zscaler Two

Skonfiguruj i przetestuj logowanie jednokrotne usługi Azure AD z usługą Zscaler Two, korzystając z użytkownika testowego **O nazwie B.Simon.** Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem rozwiązania Zscaler Two.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD z usługą Zscaler Two, wykonaj następujące kroki:

1. **[Konfigurowanie logowania jednokrotnego w usłudze Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie pojedynczej usługi Azure AD z użytkownikiem B.Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD —](#assign-the-azure-ad-test-user)** aby umożliwić aplikacji B.Simon korzystanie z logowania pojedynczego usługi Azure AD.
1. **[Konfigurowanie logowania jednokrotnego w aplikacji Zscaler Two SSO](#configure-zscaler-two-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji Zscaler Two](#create-zscaler-two-test-user)** — aby mieć w aplikacji Zscaler Two odpowiednik użytkownika B.Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji **Zscaler Two** znajdź sekcję **Zarządzanie** i wybierz **pozycję Logowanie pojedynczej.**
1. Na stronie **Select a single sign-on method (Wybieranie** metody logowania pojedynczego) wybierz pozycję **SAML**.
1. Na stronie Konfigurowanie logowania pojedynczego za pomocą **saml** kliknij ikonę ołówka dla opcji **Podstawowa konfiguracja saml,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja saml** wprowadź wartości następujących pól:

    W **polu tekstowym** Adres URL logowania wpisz adres URL używany przez użytkowników do logowania się do aplikacji ZScaler Two.

    > [!NOTE]
    > Zaktualizuj wartość przy użyciu rzeczywistego adresu URL Sign-On URL. Skontaktuj się z zespołem pomocy technicznej aplikacji [Zscaler Two Client,](https://www.zscaler.com/company/contact) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Zscaler Two oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij **ikonę** Edytuj, aby **otworzyć okno dialogowe Atrybuty** użytkownika.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z wybraną ikoną Edytuj.](common/edit-attribute.png)

6. Oprócz powyższych aplikacja Zscaler Two oczekuje jeszcze kilku atrybutów, które powinny zostać przekazane w odpowiedzi SAML. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:
    
    | Nazwa | Atrybut źródłowy |
    | ---------| ------------ |
    | memberOf | user.assignedroles |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawia oświadczenia użytkownika z opcją Dodaj nowe oświadczenie.](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawia okno dialogowe Zarządzanie oświadczeniami użytkownika, w którym można wprowadzić opisane wartości.](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.
    
    f. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Kliknij [tutaj, aby](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview) dowiedzieć się, jak skonfigurować rolę w usłudze Azure AD.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. W **sekcji Konfigurowanie aplikacji Zscaler Two** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz dla użytkownika B.Simon możliwość korzystania z logowania pojedynczego platformy Azure, udzielając dostępu do aplikacji Zscaler Two.

1. W Azure Portal pozycję **Aplikacje dla przedsiębiorstw,** a następnie pozycję **Wszystkie aplikacje.**
1. Na liście aplikacji wybierz pozycję **Zscaler Two**.
1. Na stronie przeglądu aplikacji znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy.**
1. Wybierz **pozycję Dodaj użytkownika,** a następnie wybierz **pozycję Użytkownicy i grupy** w **oknie dialogowym Dodawanie** przypisania.
1. W **oknie dialogowym** Użytkownicy i grupy wybierz **pozycję B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz przypisania roli do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz** rolę. Jeśli dla tej aplikacji nie została ustawiona żadna rola, zobaczysz wybraną rolę "Dostęp domyślny".
1. W **oknie dialogowym Dodawanie przypisania** kliknij przycisk **Przypisz.**

## <a name="configure-zscaler-two-sso"></a>Konfigurowanie logowania jednokrotnego w aplikacji Zscaler Two

1. Aby zautomatyzować konfigurację w środowisku Zscaler Two, musisz zainstalować rozszerzenie **Moje aplikacje przeglądarki** do bezpiecznego logowania, klikając **pozycję Zainstaluj rozszerzenie**.

    ![Moje aplikacje rozszerzenia](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję Setup Zscaler Two (Skonfiguruj aplikację **Zscaler Two),** aby skierować Cię do aplikacji Zscaler Two. W tym miejscu podaj poświadczenia administratora, aby zalogować się do usługi Zscaler Two. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–6.

    ![Konfigurowanie logowania jednokrotnego](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować usługę Zscaler Two, otwórz nowe okno przeglądarki internetowej i zaloguj się do firmowej witryny aplikacji Zscaler Two jako administrator i wykonaj następujące czynności:

4. Przejdź do obszaru **Administracja > Uwierzytelnianie > Ustawienia uwierzytelniania** i wykonaj następujące kroki:
   
    ![Zrzut ekranu przedstawiający witrynę Zscaler One z opisanymi krokami.](./media/zscaler-two-tutorial/administrator.png "Administracja")

    a. W obszarze Typ uwierzytelniania wybierz pozycję **SAML**.

    b. Kliknij pozycję **Skonfiguruj język SAML**.

5. W oknie **Edytowanie języka SAML** wykonaj następujące kroki i kliknij pozycję Zapisz.  
            
    ![Zarządzanie uwierzytelnianiem & użytkowników](./media/zscaler-two-tutorial/authentication.png "Zarządzanie uwierzytelnianiem & użytkowników")
    
    a. W polu tekstowym **Adres URL portalu języka SAML** wklej **adres URL logowania** skopiowany z witryny Azure Portal.

    b. W polu tekstowym **Atrybut nazwy logowania** wprowadź identyfikator **NameID**.

    c. Kliknij pozycję **Przekaż**, aby przekazać certyfikat podpisywania języka SAML na platformie Azure, który został pobrany z witryny Azure Portal w obrębie **publicznego certyfikatu SSL**.

    d. Przełącz element **Włącz automatyczne aprowizowanie języka SAML**.

    e. W polu tekstowym **Atrybut nazwy wyświetlanej użytkownika** wprowadź ciąg **displayName**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu displayName.

    f. W polu tekstowym **Atrybut nazwy grupy** wprowadź ciąg **memberOf**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu memberOf.

    przykład W polu **Atrybut nazwy działu** wprowadź ciąg **department**, jeśli chcesz włączyć automatyczne aprowizowanie języka SAML dla atrybutów elementu department.

    h. Kliknij pozycję **Zapisz**.

6. Na stronie okna dialogowanie **Konfigurowanie uwierzytelniania użytkownika** wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okno dialogowe Konfigurowanie uwierzytelniania użytkownika z wybraną oknie dialogowym Aktywuj.](./media/zscaler-two-tutorial/activation.png)

    a. Umieść kursor nad menu **Aktywacja** w lewym dolnym rogu.

    b. Kliknij pozycję **Aktywuj**.

## <a name="configuring-proxy-settings&quot;></a>Konfigurowanie ustawień serwera proxy

### <a name=&quot;to-configure-the-proxy-settings-in-internet-explorer&quot;></a>Konfigurowanie ustawień serwera proxy w programie Internet Explorer

1. Uruchom program **Internet Explorer**.

2. Wybierz pozycję **Opcje internetowe** z menu **Narzędzia**, aby otworzyć okno dialogowe **Opcje internetowe**.   
    
     ![Opcje internetowe](./media/zscaler-two-tutorial/internet.png &quot;Opcje internetowe")

3. Kliknij kartę **Połączenia**.   
  
     ![Połączenia](./media/zscaler-two-tutorial/ic769493.png "Połączenia")

4. Kliknij przycisk **Ustawienia sieci LAN**, aby otworzyć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

5. W sekcji Serwer proxy wykonaj następujące kroki:   
   
    ![Serwer proxy](./media/zscaler-two-tutorial/proxy.png "Serwer proxy")

    a. Zaznacz pole wyboru **Użyj serwera proxy dla sieci LAN**.

    b. W polu tekstowym Adres wpisz **gateway. Zscaler Two.net**.

    c. W polu tekstowym Port wpisz **80**.

    d. Zaznacz pole wyboru **Nie używaj serwera proxy dla adresów lokalnych**.

    e. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Ustawienia sieci lokalnej (LAN)**.

6. Kliknij przycisk **OK**, aby zamknąć okno dialogowe **Opcje internetowe**.


### <a name="create-zscaler-two-test-user"></a>Tworzenie użytkownika testowego aplikacji Zscaler Two

W tej sekcji w aplikacji Zscaler Two jest tworzony użytkownik o nazwie Britta Simon. Aplikacja Zscaler Two obsługuje aprowizowanie użytkowników just in time, które jest domyślnie włączone. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Zscaler Two, zostanie utworzony po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z zespołem pomocy technicznej [aplikacji Zscaler Two](https://www.zscaler.com/company/contact).

## <a name="test-sso"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujemy konfigurację logowania pojedynczego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację w** Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Zscaler Two, pod którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Zscaler Two i zainicjuj przepływ logowania.

* Możesz użyć usługi Microsoft Moje aplikacje. Po kliknięciu kafelka Zscaler Two w Moje aplikacje nastąpi przekierowanie do adresu URL logowania Zscaler Two. Aby uzyskać więcej informacji na temat Moje aplikacje, [zobacz Wprowadzenie do Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Zscaler Two można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji wykracza poza dostęp warunkowy. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
