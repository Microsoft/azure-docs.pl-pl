---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym przy użyciu programu Citrix ADC (uwierzytelnianie oparte na protokole Kerberos) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne (SSO) między Azure Active Directory i Citrix ADC przy użyciu uwierzytelniania opartego na protokole Kerberos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: jeedes
ms.openlocfilehash: 75d46edb332fb28132592e414e78bad64e75fef5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736443"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-kerberos-based-authentication"></a>Samouczek: Azure Active Directory integracji z logowaniem jednokrotnym przy użyciu narzędzia Citrix ADC (uwierzytelnianie oparte na protokole Kerberos)

W tym samouczku dowiesz się, jak zintegrować program Citrix ADC z Azure Active Directory (Azure AD). Gdy integrujesz program Citrix ADC z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Citrix ADC.
* Zezwól użytkownikom na automatyczne logowanie do programu Citrix ADC przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) Citrix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Samouczek obejmuje następujące scenariusze:

* **Zainicjowane przez program SP** Logowanie jednokrotne dla programu Citrix ADC

* Inicjowanie obsługi użytkowników **just in Time** dla programu Citrix ADC

* [Uwierzytelnianie oparte na protokole Kerberos dla programu Citrix ADC](#publish-the-web-server)

* [Uwierzytelnianie oparte na nagłówkach dla programu Citrix ADC](header-citrix-netscaler-tutorial.md#publish-the-web-server)


## <a name="add-citrix-adc-from-the-gallery"></a>Dodawanie programu Citrix ADC z galerii

Aby zintegrować program Citrix ADC z usługą Azure AD, należy najpierw dodać Citrix ADC do listy zarządzanych aplikacji SaaS z galerii:

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

1. W sekcji **Dodaj z galerii** wprowadź ciąg **Citrix ADC** w polu wyszukiwania.

1. W wynikach wybierz pozycję **Citrix ADC**, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu Citrix ADC

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Citrix ADC przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Citrix ADC.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Citrix ADC, wykonaj następujące czynności:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne usługi Azure AD za pomocą B. Simon.

    1. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.

1. [Skonfiguruj usługę Citrix ADC SSO](#configure-citrix-adc-sso) -aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    * [Utwórz użytkownika testowego Citrix ADC](#create-a-citrix-adc-test-user) , aby dysponować odpowiednikiem B. Simon w Citrix ADC, która jest połączona z reprezentacją usługi Azure AD.

1. [Przetestuj Logowanie jednokrotne](#test-sso) — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne w usłudze Azure AD przy użyciu Azure Portal, wykonaj następujące czynności:

1. W Azure Portal w okienku integracja aplikacji **Citrix ADC** w obszarze **Zarządzaj** wybierz pozycję **Logowanie jednokrotne**.

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz ikonę **edycji** pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** w celu skonfigurowania aplikacji w trybie **inicjowanym przez dostawcy tożsamości** :

    1. W polu tekstowym **Identyfikator** wprowadź adres URL, który ma następujący wzorzec: `https://<Your FQDN>`

    1. W polu tekstowym **adres URL odpowiedzi** wprowadź adres URL, który ma następujący wzorzec: `http(s)://<Your FQDN>.of.vserver/cgi/samlauth`

1. Aby skonfigurować aplikację w trybie **inicjowania programu Sp** , wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:

    * W polu tekstowym **adres URL logowania** wprowadź adres URL, który ma następujący wzorzec: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Adresy URL używane w tej sekcji nie są wartościami rzeczywistymi. Zaktualizuj te wartości przy użyciu wartości rzeczywistych dla identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta Citrix ADC](https://www.citrix.com/contact/technical-support.html) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
    > * Aby skonfigurować Logowanie jednokrotne, adresy URL muszą być dostępne z publicznych witryn sieci Web. Należy włączyć zaporę lub inne ustawienia zabezpieczeń na stronie Citrix ADC, aby enble usługę Azure AD w celu opublikowania tokenu pod skonfigurowanym adresem URL.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** dla **adresu URL metadanych federacji aplikacji** Skopiuj adres URL i Zapisz go w Notatniku.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu Citrix ADC** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

1. Wybierz pozycję **nowy użytkownik** w górnej części okienka.

1. We właściwościach **użytkownika** wykonaj następujące czynności:

   1. W obszarze **Nazwa** wprowadź `B.Simon` .  

   1. W obszarze **Nazwa użytkownika** wprowadź _username@companydomain.extension_ . Na przykład `B.Simon@contoso.com`.

   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie napisz lub skopiuj wartość wyświetlaną w polu **hasło**.

   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

Ta sekcja umożliwia użytkownikowi B. Simon korzystanie z logowania jednokrotnego platformy Azure przez przyznanie użytkownikowi dostępu do programu Citrix ADC.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

1. Na liście Aplikacje wybierz pozycję **Citrix ADC**.

1. Na stronie Przegląd aplikacji w obszarze **Zarządzanie** wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** . Wybierz pozycję **Wybierz**.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-citrix-adc-sso"></a>Konfigurowanie logowania jednokrotnego Citrix ADC

Wybierz łącze dla rodzaju uwierzytelniania, które chcesz skonfigurować:

- [Konfigurowanie logowania jednokrotnego Citrix ADC na potrzeby uwierzytelniania opartego na protokole Kerberos](#publish-the-web-server)

- [Konfigurowanie logowania jednokrotnego Citrix ADC na potrzeby uwierzytelniania opartego na nagłówkach](header-citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publikowanie serwera sieci Web 

Aby utworzyć serwer wirtualny:

1. Wybierz opcję  >  **usługi równoważenia obciążenia** zarządzania ruchem  >  .
    
1. Wybierz pozycję **Dodaj**.

    ![Konfiguracja Citrix ADC — okienko usług](./media/citrix-netscaler-tutorial/web01.png)

1. Ustaw następujące wartości dla serwera sieci Web, na którym są uruchomione aplikacje:

   * **Nazwa usługi**
   * **Adres IP serwera/istniejący serwer**
   * **Protokół**
   * **Port**

### <a name="configure-the-load-balancer"></a>Konfigurowanie usługi równoważenia obciążenia

Aby skonfigurować moduł równoważenia obciążenia:

1. Przejdź do obszaru **Zarządzanie ruchem**  >  **równoważenie obciążenia**  >  **serwery wirtualne**.

1. Wybierz pozycję **Dodaj**.

1. Ustaw następujące wartości zgodnie z opisem w poniższym zrzucie ekranu:

    * **Nazwa**
    * **Protokół**
    * **Adres IP**
    * **Port**

1. Wybierz przycisk **OK**.

    ![Konfiguracja Citrix ADC — podstawowe okienko ustawień](./media/citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Powiąż serwer wirtualny

Aby powiązać moduł równoważenia obciążenia z serwerem wirtualnym:

1. W okienku **usługi i grupy usług** wybierz pozycję **Brak powiązania usługi serwera wirtualnego równoważenia obciążenia**.

   ![Konfiguracja programu Citrix ADC — okienko powiązania usługi serwera wirtualnego równoważenia obciążenia](./media/citrix-netscaler-tutorial/bind01.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz przycisk **Zamknij**.

   ![Konfiguracja programu Citrix ADC — sprawdzanie powiązania usług serwera wirtualnego](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Powiąż certyfikat

Aby opublikować tę usługę jako protokół TLS, powiąż certyfikat serwera, a następnie przetestuj aplikację:

1. W obszarze **certyfikat** wybierz pozycję **Brak certyfikatu serwera**.

   ![Konfiguracja programu Citrix ADC — okienko certyfikatu serwera](./media/citrix-netscaler-tutorial/bind03.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz przycisk **Zamknij**.

   ![Konfiguracja programu Citrix ADC — Weryfikowanie certyfikatu](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil protokołu SAML Citrix ADC

Aby skonfigurować profil protokołu SAML programu Citrix ADC, wykonaj następujące sekcje.

### <a name="create-an-authentication-policy"></a>Tworzenie zasad uwierzytelniania

Aby utworzyć zasady uwierzytelniania:

1. Przejdź do pozycji **zabezpieczenia**  >  **AAA —**  >    >    >  **zasady uwierzytelniania** uwierzytelniania zasad ruchu aplikacji.

1. Wybierz pozycję **Dodaj**.

1. W okienku **Tworzenie zasad uwierzytelniania** wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę zasad uwierzytelniania.
    * **Akcja**: wprowadź **SAML**, a następnie wybierz pozycję **Dodaj**.
    * **Wyrażenie**: wprowadź **wartość true**.     
    
    ![Konfiguracja Citrix ADC — tworzenie okienka zasady uwierzytelniania](./media/citrix-netscaler-tutorial/policy01.png)

1. Wybierz przycisk **Utwórz**.

### <a name="create-an-authentication-saml-server"></a>Tworzenie serwera SAML uwierzytelniania

Aby utworzyć serwer uwierzytelniania SAML, przejdź do okienka **Tworzenie uwierzytelniania SAML Server** , a następnie wykonaj następujące czynności:

1. W polu **Nazwa** wprowadź nazwę serwera SAML uwierzytelniania.

1. W obszarze **Eksportowanie metadanych SAML**:

   1. Zaznacz pole wyboru **Importuj metadane** .

   1. Wprowadź adres URL metadanych Federacji z wcześniej skopiowanego interfejsu użytkownika usługi Azure SAML.
    
1. W polu **Nazwa wystawcy** wprowadź odpowiedni adres URL.

1. Wybierz przycisk **Utwórz**.

![Konfiguracja programu Citrix ADC — tworzenie okienka uwierzytelniania serwera SAML](./media/citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Tworzenie serwera wirtualnego uwierzytelniania

Aby utworzyć serwer wirtualny uwierzytelniania:

1.  Przejdź do pozycji **zabezpieczenia**  >  **AAA-zasady ruchu aplikacji** uwierzytelnianie  >    >    >  **serwerów wirtualnych**.

1.  Wybierz pozycję **Dodaj**, a następnie wykonaj następujące czynności:

    1. W polu **Nazwa** wprowadź nazwę wirtualnego serwera uwierzytelniania.

    1. Zaznacz pole wyboru **nieadresowane** .

    1. W obszarze **Protokół** wybierz pozycję **SSL**.

    1. Wybierz przycisk **OK**.
    
1. Wybierz opcję **Kontynuuj**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurowanie serwera wirtualnego uwierzytelniania do korzystania z usługi Azure AD

Zmodyfikuj dwie sekcje dla wirtualnego serwera uwierzytelniania:

1.  W okienku **Zaawansowane zasady uwierzytelniania** wybierz pozycję **brak zasad uwierzytelniania**.

    ![Konfiguracja Citrix ADC — zaawansowane okienko zasady uwierzytelniania](./media/citrix-netscaler-tutorial/virtual01.png)

1. W okienku **powiązanie zasad** wybierz zasady uwierzytelniania, a następnie wybierz pozycję **bind**.

    ![Konfiguracja Citrix ADC — okienko powiązania zasad](./media/citrix-netscaler-tutorial/virtual02.png)

1. W okienku **serwery wirtualne oparte na formularzach** wybierz pozycję **Brak serwera wirtualnego równoważenia obciążenia**.

    ![Konfiguracja Citrix ADC — okienko serwerów wirtualnych opartych na formularzach](./media/citrix-netscaler-tutorial/virtual03.png)

1. W obszarze Nazwa **FQDN uwierzytelniania** wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) (wymagane).

1. Wybierz serwer wirtualny równoważenia obciążenia, który ma być chroniony za pomocą uwierzytelniania usługi Azure AD.

1. Wybierz pozycję **bind**.

    ![Konfiguracja programu Citrix ADC — okienko powiązania równoważenia obciążenia serwera wirtualnego](./media/citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Upewnij się, że wybrano opcję **gotowe** w okienku **Konfiguracja serwera wirtualnego uwierzytelniania** .

1. Aby zweryfikować zmiany, w przeglądarce przejdź do adresu URL aplikacji. Powinna zostać wyświetlona strona logowania dzierżawy zamiast nieuwierzytelnionego dostępu, który był wcześniej widoczny.

    ![Konfiguracja programu Citrix ADC — Strona logowania w przeglądarce sieci Web](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-kerberos-based-authentication"></a>Konfigurowanie logowania jednokrotnego Citrix ADC na potrzeby uwierzytelniania opartego na protokole Kerberos

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Tworzenie konta delegowania protokołu Kerberos dla programu Citrix ADC

1. Utwórz konto użytkownika (w tym przykładzie korzystamy z _AppDelegation_).

    ![Konfiguracja Citrix ADC — okienko właściwości](./media/citrix-netscaler-tutorial/kerberos01.png)

1. Skonfiguruj nazwę SPN hosta dla tego konta. 

    Przykład: `setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation`
    
    W tym przykładzie:

    * `IDENTT.WORK` jest nazwą FQDN domeny.
    * `identt` jest nazwą NetBIOS domeny.
    * `appdelegation` jest nazwą konta użytkownika delegowania.

1. Skonfiguruj delegowanie dla serwera sieci Web, jak pokazano na poniższym zrzucie ekranu:
 
    ![Konfiguracja Citrix ADC — delegowanie w obszarze właściwości okienka](./media/citrix-netscaler-tutorial/kerberos02.png)

    > [!NOTE]
    > W przykładzie zrzutu ekranu wewnętrzna nazwa serwera sieci Web z uruchomioną witryną uwierzytelniania zintegrowanego systemu Windows (WIA) to _CWEB2_.

### <a name="citrix-adc-aaa-kcd-kerberos-delegation-accounts"></a>Citrix ADC AAA KCD (konta delegowania protokołu Kerberos)

Aby skonfigurować konto Citrix ADC AAA KCD:

1.  Przejdź do kont **Citrix Gateway**  >  **AAA KCD (ograniczone delegowanie protokołu Kerberos)**.

1.  Wybierz pozycję **Dodaj**, a następnie wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę dla konta KCD.

    * **Obszar**: Wprowadź nazwę domeny i rozszerzenie na wielką literę.

    * **Nazwa SPN usługi**: `http/<host/fqdn>@<DOMAIN.COM>` .
    
        > [!NOTE]
        > `@DOMAIN.COM` jest wymagana i musi być wielką literą. Przykład: `http/cweb2@IDENTT.WORK`.

    * **Delegowany użytkownik**: Wprowadź nazwę delegowanego użytkownika.

    * Zaznacz pole wyboru **hasło dla delegowanego użytkownika** , a następnie wprowadź i Potwierdź hasło.

1. Wybierz przycisk **OK**.
 
    ![Konfiguracja Citrix ADC — Konfigurowanie okienka konta KCD](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Zasady ruchu Citrix i profil ruchu

Aby skonfigurować zasady ruchu Citrix i profil ruchu:

1.  Przejdź do pozycji **zabezpieczenia**  >  **AAA-aplikacje** zasady ruchu  >    >  **, profile i zasady logowania jednokrotnego ProfilesTraffic**.

1.  Wybierz pozycję **profile ruchu**.

1.  Wybierz pozycję **Dodaj**.

1.  Aby skonfigurować profil ruchu, wprowadź lub wybierz następujące wartości.

    * **Nazwa**: Wprowadź nazwę profilu ruchu.

    * **Logowanie jednokrotne**: wybierz pozycję **włączone**.

    * **Konto KCD**: wybierz konto KCD utworzone w poprzedniej sekcji.

1. Wybierz przycisk **OK**.

    ![Konfiguracja Citrix ADC — Konfigurowanie okienka profilu ruchu](./media/citrix-netscaler-tutorial/kerberos04.png)
 
1.  Wybierz pozycję **zasady ruchu**.

1.  Wybierz pozycję **Dodaj**.

1.  Aby skonfigurować zasady ruchu, wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę zasady ruchu.

    * **Profil**: Wybierz profil ruchu utworzony w poprzedniej sekcji.

    * **Wyrażenie**: wprowadź **wartość true**.

1. Wybierz przycisk **OK**.

    ![Konfiguracja Citrix ADC — Konfigurowanie okienka zasad ruchu](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="bind-a-traffic-policy-to-a-virtual-server-in-citrix"></a>Powiązywanie zasad ruchu z serwerem wirtualnym w programie Citrix

Aby powiązać zasady ruchu z serwerem wirtualnym przy użyciu graficznego interfejsu użytkownika:

1. Przejdź do obszaru **Zarządzanie ruchem**  >  **równoważenie obciążenia**  >  **serwery wirtualne**.

1. Z listy serwerów wirtualnych wybierz serwer wirtualny, do którego chcesz powiązać zasady ponownego zapisu, a następnie wybierz pozycję **Otwórz**.

1. W okienku **serwer wirtualny równoważenia obciążenia** w obszarze **Ustawienia zaawansowane** wybierz pozycję **zasady**. Wszystkie zasady, które są skonfigurowane dla Twojego wystąpienia programu, zostaną wyświetlone na liście.
 
    ![Konfiguracja programu Citrix ADC — okienko równoważenia obciążenia dla serwera wirtualnego](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Konfiguracja Citrix ADC — okno dialogowe zasady](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Zaznacz pole wyboru obok nazwy zasad, które chcesz powiązać z tym serwerem wirtualnym.
 
    ![Konfiguracja programu Citrix ADC — okienko powiązania zasad ruchu wirtualnego serwera równoważenia obciążenia](./media/citrix-netscaler-tutorial/kerberos09.png)

1. W oknie dialogowym **Wybierz typ** :

    1. W obszarze **Wybieranie zasad** wybierz pozycję **ruch**.

    1. W obszarze **Wybierz typ** wybierz pozycję **żądanie**.

    ![Konfiguracja Citrix ADC — Wybieranie okienka Typ](./media/citrix-netscaler-tutorial/kerberos08.png)

1. Gdy zasady są powiązane, wybierz pozycję **gotowe**.
 
    ![Konfiguracja Citrix ADC — okienko zasady](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Przetestuj powiązanie przy użyciu witryny sieci Web WIA.

    ![Konfiguracja programu Citrix ADC — Strona testowa w przeglądarce internetowej](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-a-citrix-adc-test-user"></a>Tworzenie użytkownika testowego Citrix ADC

W tej sekcji użytkownik o nazwie B. Simon został utworzony w programie Citrix ADC. Program Citrix ADC obsługuje funkcję aprowizacji użytkowników just-in-Time, która jest domyślnie włączona. Nie ma żadnych akcji, które należy wykonać w tej sekcji. Jeśli użytkownik nie istnieje już w narzędziu Citrix ADC, po uwierzytelnieniu zostanie utworzony nowy.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem obsługi klienta Citrix ADC](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Citrix ADC, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Citrix ADC i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Citrix ADC w obszarze Moje aplikacje zostanie przekierowany na adres URL logowania Citrix ADC. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Citrix ADC można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).