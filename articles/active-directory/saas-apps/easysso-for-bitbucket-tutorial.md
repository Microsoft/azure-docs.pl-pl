---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EasySSO dla BitBucket | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EasySSO dla BitBucket.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.openlocfilehash: 0433c0fdc1584ce209eb0409b0e8f5cbfc2719ed
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454409"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EasySSO dla BitBucket

W tym samouczku dowiesz się, jak zintegrować usługę EasySSO for BitBucket z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EasySSO for BitBucket z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EasySSO dla BitBucket.
* Zezwól użytkownikom na automatyczne logowanie do usługi EasySSO dla BitBucket przy użyciu swoich kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja EasySSO dla BitBucket, która obsługuje logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa EasySSO for BitBucket obsługuje logowanie jednokrotne zainicjowane przez usługę SP i dostawcy tożsamości.
* EasySSO dla BitBucket obsługuje Inicjowanie obsługi użytkowników just-in-Time.
* Po skonfigurowaniu usługi EasySSO dla BitBucket można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-easysso-for-bitbucket-from-the-gallery"></a>Dodawanie EasySSO do BitBucket z galerii

Aby skonfigurować integrację EasySSO dla BitBucket z usługą Azure AD, musisz dodać EasySSO dla BitBucket z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EasySSO dla BitBucket** w polu wyszukiwania.
1. Wybierz pozycję **EasySSO dla BitBucket** z wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla EasySSO dla BitBucket

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EasySSO dla BitBucket przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EasySSO dla BitBucket.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EasySSO dla BitBucket, wykonaj następujące czynności:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user) aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. [Skonfiguruj EasySSO dla logowania jednokrotnego BitBucket](#configure-easysso-for-bitbucket-sso) w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. [Utwórz element EasySSO dla użytkownika testowego BitBucket](#create-an-easysso-for-bitbucket-test-user) , aby miał odpowiednik B. Simon w EasySSO dla BitBucket, połączony z reprezentacją użytkownika w usłudze Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **EasySSO for BitBucket** Znajdź sekcję **Zarządzanie** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie pojedynczej Sign-On ze stroną SAML z wyróżnioną ikoną ołówka](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Wybierz opcję **Ustaw dodatkowe adresy URL**i wykonaj następujące czynności, jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** :

    - W polu tekstowym **adres URL logowania** wpisz adres URL, który używa następującego wzorca: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej EasySSO](mailto:support@techtime.co.nz) , aby uzyskać te wartości w razie wątpliwości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja EasySSO for BitBucket oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu atrybutów domyślnych](common/default-attributes.png)

1. Aplikacja EasySSO for BitBucket oczekuje, że kilka atrybutów będzie przekazywać z powrotem do odpowiedzi SAML. W poniższej tabeli przedstawiono te elementy. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | ---------------| --------- |
    | urn: identyfikator OID: 0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn: identyfikator OID: 2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Jeśli użytkownicy usługi Azure AD mają skonfigurowaną wartość **sAMAccountName** , należy zmapować **nazwę urn: OID: 0.9.2342.19200300.100.1.1** na atrybut **sAMAccountName** .
    
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** wybierz linki do pobrania dla opcji **certyfikat (base64)** lub **XML metadanych Federacji** . Zapisz lub oba na komputerze. Będzie ona potrzebna później, aby skonfigurować BitBucket EasySSO.

    ![Zrzut ekranu przedstawiający sekcję certyfikat podpisywania SAML z wyróżnionymi linkami pobierania](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Jeśli planujesz skonfigurować EasySSO dla BitBucket ręcznie przy użyciu certyfikatu, musisz również skopiować **adres URL logowania** i **Identyfikator usługi Azure AD**, a następnie zapisać je na komputerze.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego, B. Simon, w Azure Portal.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W obszarze **Nazwa**wprowadź `B.Simon` .  
   1. W polu **Nazwa użytkownika**wprowadź username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie Zapisz hasło.
   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do EasySSO dla BitBucket.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EasySSO dla BitBucket**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Zrzut ekranu przedstawiający sekcję Zarządzanie z wyróżnionymi użytkownikami i grupami](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Zrzut ekranu strony użytkownicy i grupy z wyróżnioną pozycją Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie wybierz **pozycję Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz z listy odpowiednią rolę dla użytkownika. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurowanie EasySSO na potrzeby logowania jednokrotnego BitBucket

1. Zaloguj się do swojego wystąpienia programu Atlassian BitBucket z uprawnieniami administratora i przejdź do sekcji **Administracja** . 

    ![Zrzut ekranu wystąpienia BitBucket z wyróżnioną ikoną koła zębatego](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. Znajdź i wybierz pozycję **EasySSO**.

    ![Zrzut ekranu przedstawiający łatwą opcję logowania jednokrotnego](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Wybierz pozycję **SAML**. Spowoduje to przejście do sekcji Konfiguracja protokołu SAML.

    ![Zrzut ekranu strony administratora EasySSO z wyróżnioną pozycją SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Wybierz kartę **Certyfikaty** , a zobaczysz następujący ekran:

    ![Zrzut ekranu przedstawiający kartę certyfikaty z różnymi opcjami wyróżnionymi](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Znajdź **certyfikat (base64)** lub **plik metadanych** zapisany w poprzedniej sekcji tego samouczka. Możesz wykonać jedną z następujących czynności:

    - Użyj pobranego **pliku metadanych** Federacji aplikacji do pliku lokalnego na komputerze. Wybierz przycisk radiowy **Przekaż** i postępuj zgodnie ze ścieżką specyficzną dla danego systemu operacyjnego.

    - Otwórz **plik metadanych** Federacji aplikacji, aby wyświetlić zawartość pliku w dowolnym edytorze zwykłego tekstu. Skopiuj ją do Schowka. Wybierz pozycję **dane wejściowe**i wklej zawartość schowka do pola tekstowego.
 
    - Wykonaj pełną konfigurację ręczną. Otwórz certyfikat Federacji aplikacji **(base64)** , aby wyświetlić zawartość pliku w dowolnym edytorze zwykłego tekstu. Skopiuj ją do schowka, a następnie wklej ją do pola tekstowego **token dostawcy tożsamości certyfikaty podpisywania** . Następnie przejdź do karty **Ogólne** i wypełnij pola **adres URL** i **Identyfikator jednostki** z odpowiednimi wartościami dla **adresu URL logowania** i **identyfikatora usługi Azure AD** , które zostały zapisane wcześniej.
 
1. Wybierz pozycję **Zapisz** w dolnej części strony. Zobaczysz, że zawartość metadanych lub plików certyfikatów jest analizowana w pola konfiguracji. Konfiguracja EasySSO dla BitBucket została ukończona.

1. Aby przetestować konfigurację, przejdź do karty **szukaj & działanie** i wybierz pozycję **przycisk Zaloguj SAML**. Pozwala to na oddzielny przycisk na ekranie logowania BitBucket, w odniesieniu do kompleksowego testowania integracji protokołu SAML usługi Azure AD. Ten przycisk można opuścić, a także skonfigurować jego położenie, kolor i translację w trybie produkcyjnym.

    ![Zrzut ekranu przedstawiający stronę języka SAML & kartę działanie z wyróżnionym przyciskiem logowania SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Jeśli masz jakieś problemy, skontaktuj się z [zespołem pomocy technicznej EasySSO](mailto:support@techtime.co.nz).

### <a name="create-an-easysso-for-bitbucket-test-user"></a>Utwórz EasySSO dla użytkownika testowego BitBucket

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w BitBucket. EasySSO dla BitBucket obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie wyłączona. Aby ją włączyć, należy jawnie zaznaczyć pole wyboru **Utwórz użytkownika w przypadku pomyślnego zalogowania** w sekcji **Ogólne** konfiguracji wtyczki EasySSO. Jeśli użytkownik nie istnieje jeszcze w usłudze BitBucket, zostanie utworzony nowy po uwierzytelnieniu.

Jeśli jednak nie chcesz włączać automatycznej aprowizacji użytkowników, gdy użytkownik loguje się po raz pierwszy, użytkownicy muszą znajdować się w katalogach użytkowników, których wystąpienie BitBucket korzysta z programu. Na przykład może to być katalog LDAP lub Atlassian.

![Zrzut ekranu przedstawiający sekcję ogólne konfiguracji wtyczki EasySSO z wyróżnioną opcją Utwórz użytkownika po pomyślnym zalogowaniu](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

### <a name="idp-initiated-workflow"></a>Dostawcy tożsamości — przepływ pracy zainicjowany

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka EasySSO for BitBucket należy automatycznie zalogować się do wystąpienia BitBucket, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

### <a name="sp-initiated-workflow"></a>Przepływ pracy zainicjowany przez program SP

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD za pomocą przycisku BitBucket **SAML login** .

![Zrzut ekranu przedstawiający ekran logowania z wyróżnionym logowaniem SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

W tym scenariuszu przyjęto, że **przycisk Zaloguj SAML** został włączony na karcie **Wyszukiwanie &** na stronie Konfiguracja EasySSO BitBucket. Otwórz adres URL logowania do programu BitBucket w trybie incognito przeglądarki, aby uniknąć interwencji z istniejącymi sesjami. Wybierz pozycję Logowanie przy użyciu protokołu **SAML**i nastąpi przekierowanie do przepływu uwierzytelniania użytkownika usługi Azure AD. Po pomyślnym zakończeniu nastąpi przekierowanie do wystąpienia BitBucket jako użytkownik uwierzytelniony za pośrednictwem protokołu SAML.

Po przekierowaniu z powrotem z usługi Azure AD może wystąpić Poniższy ekran:

![Zrzut ekranu przedstawiający ekran błędu EasySSO z wyróżnionym numerem odwołania](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

Jeśli to zrobisz, postępuj zgodnie z [instrukcjami na tej stronie](https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) , aby uzyskać dostęp do pliku **Atlassian-BitBucket. log** . Szczegóły błędu będą dostępne przez identyfikator odwołania znaleziony na stronie błędu EasySSO.

Jeśli masz jakieś problemy, skontaktuj się z [zespołem pomocy technicznej EasySSO](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj EasySSO dla BitBucket z usługą Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Ochrona EasySSO dla BitBucket z zaawansowaną widocznością i kontrolkami](/cloud-app-security/proxy-intro-aad)