---
title: 'Samouczek: Azure Active Directory integracji z logowaniem jednokrotnym przy użyciu narzędzia Citrix datascaleer (uwierzytelnianie oparte na nagłówkach) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne (SSO) między usługami Azure Active Directory i Citrix datascaleer przy użyciu uwierzytelniania opartego na nagłówkach.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.openlocfilehash: de02e8ea995482b815734565f839c4cb1c348782
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92445651"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego za pomocą narzędzia Citrix datascaleer (uwierzytelnianie oparte na nagłówkach)

W tym samouczku dowiesz się, jak zintegrować program Citrixow z Azure Active Directory (Azure AD). Po zintegrowaniu usługi Citrix datascaleer z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Citrix.
* Zezwól użytkownikom na automatyczne logowanie do usługi Citrixow przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji oprogramowania jako usługi (SaaS) z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Citrix.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. Samouczek obejmuje następujące scenariusze:

* **Zainicjowane przez program SP** Logowanie jednokrotne dla platformy Citrix

* Inicjowanie obsługi użytkowników **just in Time** dla programu Citrix

* [Uwierzytelnianie oparte na nagłówkach dla skalowania firmy Citrix](#publish-the-web-server)

* [Uwierzytelnianie oparte na protokole Kerberos dla skalowania firmy Citrix](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Dodawanie programu citrixow z galerii

Aby zintegrować program Citrix Cordova z usługą Azure AD, najpierw Dodaj aplikację Citrix Cordova do listy zarządzanych aplikacji SaaS z galerii:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.

1. W sekcji **Dodaj z galerii** w polu wyszukiwania wprowadź ciąg **Citrix** .

1. W wynikach wybierz pozycję **Citrix**Cordova, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Citrix

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą narzędzia Citrix Bier przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Citrix Work.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Citrix, wykonaj następujące bloki konstrukcyjne:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne usługi Azure AD za pomocą B. Simon.

    1. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.

1. [Skonfiguruj Logowanie jednokrotne](#configure-citrix-netscaler-sso) w usłudze Citrix, aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.

    * [Utwórz użytkownika testowego platformy Citrix](#create-a-citrix-netscaler-test-user) , aby dysponować odpowiednikiem B. Simon w usłudze Citrix, która jest połączona z reprezentacją usługi Azure AD.

1. [Przetestuj Logowanie jednokrotne](#test-sso) — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Aby włączyć logowanie jednokrotne w usłudze Azure AD przy użyciu Azure Portal, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)w okienku integracja aplikacji **Citrix** , w obszarze **Zarządzaj**wybierz pozycję **Logowanie jednokrotne**.

1. W okienku **Wybierz metodę logowania** jednokrotnego wybierz pozycję **SAML**.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** wybierz ikonę **edycji** pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** w celu skonfigurowania aplikacji w trybie **inicjowanym przez dostawcy tożsamości** :

    1. W polu tekstowym **Identyfikator** wprowadź adres URL, który ma następujący wzorzec: `https://<Your FQDN>`

    1. W polu tekstowym **adres URL odpowiedzi** wprowadź adres URL, który ma następujący wzorzec: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Aby skonfigurować aplikację w trybie **inicjowania programu Sp** , wybierz opcję **Ustaw dodatkowe adresy URL** i wykonaj następujące czynności:

    * W polu tekstowym **adres URL logowania** wprowadź adres URL, który ma następujący wzorzec: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Adresy URL używane w tej sekcji nie są wartościami rzeczywistymi. Zaktualizuj te wartości przy użyciu wartości rzeczywistych dla identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta Citrix](https://www.citrix.com/contact/technical-support.html) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.
    > * Aby skonfigurować Logowanie jednokrotne, adresy URL muszą być dostępne z publicznych witryn sieci Web. Aby enble usługę Azure AD w celu opublikowania tokenu pod skonfigurowanym adresem URL, należy włączyć zaporę lub inne ustawienia zabezpieczeń na stronie Citrix.

1. W okienku **Skonfiguruj pojedyncze Sign-On przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** dla **adresu URL metadanych federacji aplikacji**Skopiuj adres URL i Zapisz go w Notatniku.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aplikacja Citrix do skalowania oczekuje, że potwierdzenia SAML mają być w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Wybierz ikonę **Edytuj** i Zmień mapowania atrybutów.

    ![Edytuj mapowanie atrybutu SAML](common/edit-attribute.png)

1. Aplikacja Citrixow oczekuje również, że kilka atrybutów będzie przekazywać z powrotem do odpowiedzi SAML. W oknie dialogowym **atrybuty użytkownika** w obszarze **oświadczenia użytkownika**wykonaj następujące kroki, aby dodać atrybuty tokenu SAML, jak pokazano w tabeli:

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Wybierz pozycję **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    1. W polu tekstowym **Nazwa** wprowadź nazwę atrybutu, który jest pokazywany dla tego wiersza.

    1. Pozostaw pole **Przestrzeń nazw** puste.

    1. Dla **atrybutu**wybierz pozycję **Źródło**.

    1. Na liście **atrybutów źródłowych** wprowadź wartość atrybutu, która jest wyświetlana dla tego wiersza.

    1. Wybierz przycisk **OK**.

    1. Wybierz pozycję **Zapisz**.

1. W sekcji **Konfigurowanie platformy Citrix** , skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W menu po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

1. Wybierz pozycję **nowy użytkownik** w górnej części okienka.

1. We właściwościach **użytkownika** wykonaj następujące czynności:

   1. W obszarze **Nazwa**wprowadź `B.Simon` .  

   1. W obszarze **Nazwa użytkownika**wprowadź _username@companydomain.extension_ . Na przykład `B.Simon@contoso.com`.

   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie napisz lub skopiuj wartość wyświetlaną w polu **hasło**.

   1. Wybierz pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można umożliwić użytkownikowi B. Simon korzystanie z logowania jednokrotnego na platformie Azure przez przyznanie użytkownikowi dostępu do programu Citrix.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

1. Na liście Aplikacje wybierz pozycję **Citrix**.

1. Na stronie Przegląd aplikacji w obszarze **Zarządzanie**wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** . Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz, że jakakolwiek wartość roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie wybierz **pozycję Wybierz**.

1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurowanie logowania jednokrotnego dla platformy Citrix

Wybierz łącze dla rodzaju uwierzytelniania, które chcesz skonfigurować:

- [Konfigurowanie logowania jednokrotnego dla usługi Citrix dataskalować na potrzeby uwierzytelniania opartego na nagłówkach](#publish-the-web-server)

- [Konfigurowanie logowania jednokrotnego dla platformy Citrix dla uwierzytelniania opartego na protokole Kerberos](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publikowanie serwera sieci Web 

Aby utworzyć serwer wirtualny:

1. Wybierz **Traffic Management**opcję  >  **usługi równoważenia obciążenia**zarządzania ruchem  >  **Services**.
    
1. Wybierz pozycję **Dodaj**.

    ![Konfiguracja platformy Citrix — panel usług](./media/header-citrix-netscaler-tutorial/web01.png)

1. Ustaw następujące wartości dla serwera sieci Web, na którym są uruchomione aplikacje:

   * **Nazwa usługi**
   * **Adres IP serwera/istniejący serwer**
   * **Protokół**
   * **Port**

     ![Okienko konfiguracji Citrix skaler](./media/header-citrix-netscaler-tutorial/web01.png)

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

    ![Konfiguracja serwera Citrix — podstawowe okienko ustawień](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Powiąż serwer wirtualny

Aby powiązać moduł równoważenia obciążenia z serwerem wirtualnym:

1. W okienku **usługi i grupy usług** wybierz pozycję **Brak powiązania usługi serwera wirtualnego równoważenia obciążenia**.

   ![Konfiguracja programu Citrix teleskaler — okienko powiązania usługi serwera wirtualnego równoważenia obciążenia](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz przycisk **Zamknij**.

   ![Konfiguracja programu Citrix DataScale — Sprawdź powiązanie usług serwera wirtualnego](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Powiąż certyfikat

Aby opublikować tę usługę jako protokół TLS, powiąż certyfikat serwera, a następnie przetestuj aplikację:

1. W obszarze **certyfikat**wybierz pozycję **Brak certyfikatu serwera**.

   ![Konfiguracja programu Citrix webscalec — okienko certyfikatu serwera](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Sprawdź ustawienia, jak pokazano na poniższym zrzucie ekranu, a następnie wybierz przycisk **Zamknij**.

   ![Konfiguracja programu Citrix The](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Profil protokołu SAML Citrix ADC

Aby skonfigurować profil protokołu SAML programu Citrix ADC, wykonaj następujące sekcje:

### <a name="create-an-authentication-policy"></a>Tworzenie zasad uwierzytelniania

Aby utworzyć zasady uwierzytelniania:

1. Przejdź do pozycji **zabezpieczenia**  >  **AAA —**  >  **Policies**  >  **Authentication**  >  **zasady uwierzytelniania**uwierzytelniania zasad ruchu aplikacji.

1. Wybierz pozycję **Dodaj**.

1. W okienku **Tworzenie zasad uwierzytelniania** wprowadź lub wybierz następujące wartości:

    * **Nazwa**: Wprowadź nazwę zasad uwierzytelniania.
    * **Akcja**: wprowadź **SAML**, a następnie wybierz pozycję **Dodaj**.
    * **Wyrażenie**: wprowadź **wartość true**.     
    
    ![Konfiguracja serwera Citrix — Tworzenie zasad uwierzytelniania — okienko](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Wybierz pozycję **Utwórz**.

### <a name="create-an-authentication-saml-server"></a>Tworzenie serwera SAML uwierzytelniania

Aby utworzyć serwer uwierzytelniania SAML, przejdź do okienka **Tworzenie uwierzytelniania SAML Server** , a następnie wykonaj następujące czynności:

1. W polu **Nazwa**wprowadź nazwę serwera SAML uwierzytelniania.

1. W obszarze **Eksportowanie metadanych SAML**:

   1. Zaznacz pole wyboru **Importuj metadane** .

   1. Wprowadź adres URL metadanych Federacji z wcześniej skopiowanego interfejsu użytkownika usługi Azure SAML.
    
1. W polu **Nazwa wystawcy**wprowadź odpowiedni adres URL.

1. Wybierz pozycję **Utwórz**.

![Konfiguracja programu Citrix webscalenia — tworzenie okienka uwierzytelniania serwera SAML](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Tworzenie serwera wirtualnego uwierzytelniania

Aby utworzyć serwer wirtualny uwierzytelniania:

1.  Przejdź do pozycji **zabezpieczenia**  >  **AAA-zasady ruchu aplikacji**uwierzytelnianie  >  **Policies**  >  **Authentication**  >  **serwerów wirtualnych**.

1.  Wybierz pozycję **Dodaj**, a następnie wykonaj następujące czynności:

    1. W polu **Nazwa**wprowadź nazwę wirtualnego serwera uwierzytelniania.

    1. Zaznacz pole wyboru **nieadresowane** .

    1. W obszarze **Protokół**wybierz pozycję **SSL**.

    1. Wybierz przycisk **OK**.

    ![Konfiguracja programu Citrix The configurationer — okienko uwierzytelniania wirtualnego serwera](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurowanie serwera wirtualnego uwierzytelniania do korzystania z usługi Azure AD

Zmodyfikuj dwie sekcje dla wirtualnego serwera uwierzytelniania:

1.  W okienku **Zaawansowane zasady uwierzytelniania** wybierz pozycję **brak zasad uwierzytelniania**.

    ![Konfiguracja serwera Citrix — zaawansowane zasady uwierzytelniania — okienko](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. W okienku **powiązanie zasad** wybierz zasady uwierzytelniania, a następnie wybierz pozycję **bind**.

    ![Konfiguracja platformy Citrix — okienko powiązania zasad](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. W okienku **serwery wirtualne oparte na formularzach** wybierz pozycję **Brak serwera wirtualnego równoważenia obciążenia**.

    ![Konfiguracja platformy Citrix — formularz z serwerami wirtualnymi](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. W obszarze Nazwa **FQDN uwierzytelniania**wprowadź w pełni kwalifikowaną nazwę domeny (FQDN) (wymagane).

1. Wybierz serwer wirtualny równoważenia obciążenia, który ma być chroniony za pomocą uwierzytelniania usługi Azure AD.

1. Wybierz pozycję **bind**.

    ![Konfiguracja programu Citrix webscalenia — okienko powiązania serwera wirtualnego równoważenia obciążenia](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Upewnij się, że wybrano opcję **gotowe** w okienku **Konfiguracja serwera wirtualnego uwierzytelniania** .

1. Aby zweryfikować zmiany, w przeglądarce przejdź do adresu URL aplikacji. Powinna zostać wyświetlona strona logowania dzierżawy zamiast nieuwierzytelnionego dostępu, który był wcześniej widoczny.

    ![Konfiguracja Citrix Hook — Strona logowania w przeglądarce sieci Web](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Konfigurowanie logowania jednokrotnego dla usługi Citrix dataskalować na potrzeby uwierzytelniania opartego na nagłówkach

### <a name="configure-citrix-adc"></a>Konfigurowanie narzędzi Citrix ADC

Aby skonfigurować program Citrix ADC na potrzeby uwierzytelniania opartego na nagłówkach, wykonaj następujące sekcje.

#### <a name="create-a-rewrite-action"></a>Utwórz akcję ponownego zapisu

1. Przejdź do obszaru **AppExpert**  >  **ponownie zapisuj**  >  **Akcje zapisywania**.
 
    ![Konfiguracja platformy Citrix — operacje ponownego zapisywania — okienko akcje](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Wybierz pozycję **Dodaj**, a następnie wykonaj następujące czynności:

    1. W polu **Nazwa**wprowadź nazwę akcji ponownego zapisu.

    1. W obszarze **Typ wpisz** **INSERT_HTTP_HEADER**.

    1. W polu **Nazwa nagłówka**wprowadź nazwę nagłówka (w tym przykładzie używamy _SecretID_).

    1. W obszarze **wyrażenie**wprowadź **AAA. Użytkownicy. ATRYBUT ("mySecretID")**, gdzie **mySecretID** to Deklaracja SAML usługi Azure AD, która została wysłana do programu Citrix ADC.

    1. Wybierz pozycję **Utwórz**.

    ![Konfiguracja platformy Citrix — tworzenie okienka akcja Utwórz ponownie](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Tworzenie zasad ponownego zapisywania

1.  Przejdź do pozycji **AppExpert**ponownie  >  **Zapisz**  >  **zasady ponownego zapisywania**.
 
    ![Konfiguracja serwera Citrix dla skalowania — okienko zasady ponownego zapisywania](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Wybierz pozycję **Dodaj**, a następnie wykonaj następujące czynności:

    1. W polu **Nazwa**wprowadź nazwę zasad ponownego zapisywania.

    1. W polu **Akcja**wybierz akcję ponownego zapisu utworzoną w poprzedniej sekcji.

    1. Dla **wyrażenia**wprowadź **wartość true**.

    1. Wybierz pozycję **Utwórz**.

    ![Konfiguracja platformy Citrix — tworzenie okienka zasad](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Powiąż zasady ponownego zapisu z serwerem wirtualnym

Aby powiązać zasady ponownego zapisywania z serwerem wirtualnym przy użyciu graficznego interfejsu użytkownika:

1. Przejdź do obszaru **Zarządzanie ruchem**  >  **równoważenie obciążenia**  >  **serwery wirtualne**.

1. Z listy serwerów wirtualnych wybierz serwer wirtualny, do którego chcesz powiązać zasady ponownego zapisu, a następnie wybierz pozycję **Otwórz**.

1. W okienku **serwer wirtualny równoważenia obciążenia** w obszarze **Ustawienia zaawansowane**wybierz pozycję **zasady**. Wszystkie zasady, które są skonfigurowane dla Twojego wystąpienia programu, zostaną wyświetlone na liście.
 
    ![Zrzut ekranu pokazujący kartę "Konfiguracja" z wyróżnionymi polami "name", "Action" i "Expression" oraz wybranym przyciskiem "Utwórz".](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Konfiguracja programu Citrix dla modułu równoważenia obciążenia — okienko serwera wirtualnego](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Zaznacz pole wyboru obok nazwy zasad, które chcesz powiązać z tym serwerem wirtualnym.
 
    ![Konfiguracja programu Citrix The Trafficer — okienko powiązania zasad ruchu wirtualnego serwera równoważenia obciążenia](./media/header-citrix-netscaler-tutorial/header08.png)

1. W oknie dialogowym **Wybierz typ** :

    1. W obszarze **Wybieranie zasad**wybierz pozycję **ruch**.

    1. W obszarze **Wybierz typ**wybierz pozycję **żądanie**.

    ![Konfiguracja platformy Citrix — okno dialogowe zasady](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Wybierz przycisk **OK**. Komunikat na pasku stanu wskazuje, że zasady zostały pomyślnie skonfigurowane.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Modyfikowanie serwera SAML w celu wyodrębnienia atrybutów z roszczeń

1.  Przejdź do pozycji **zabezpieczenia**  >  **AAA —** zasady  >  **Policies**  >  **Zaawansowane uwierzytelnianie**zasad ruchu aplikacji  >  **Advanced Policies**  >  **Actions**  >  **serwery**.

1.  Wybierz odpowiedni serwer uwierzytelniania SAML dla aplikacji.
 
    ![Konfiguracja programu Citrix datascalec — Konfigurowanie okienka uwierzytelniania serwera SAML](./media/header-citrix-netscaler-tutorial/header09.png)

1. W obszarze **atrybuty** bólu wprowadź atrybuty SAML, które mają zostać wyodrębnione, oddzielone przecinkami. W naszym przykładzie wprowadzimy atrybut `mySecretID` .
 
    ![Konfiguracja platformy Citrix — atrybuty okienka](./media/header-citrix-netscaler-tutorial/header10.png)

1. Aby sprawdzić dostęp, pod adresem URL w przeglądarce poszukaj atrybutu SAML w obszarze **Kolekcja nagłówków**.

    ![Konfiguracja programu Citrix The The Scaleer — Kolekcja nagłówków pod adresem URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Tworzenie użytkownika testowego platformy Citrix

W tej sekcji użytkownik o nazwie B. Simon został utworzony w programie Citrix. Program citrixow obsługuje Inicjowanie obsługi użytkowników just-in-Time, która jest domyślnie włączona. Nie ma żadnych akcji, które należy wykonać w tej sekcji. Jeśli użytkownik nie istnieje jeszcze w usłudze Citrix, zostanie utworzony nowy po uwierzytelnieniu.

> [!NOTE]
> Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem obsługi klienta Citrix](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację rejestracji jednokrotnej usługi Azure AD za pomocą panelu dostępu.

Po wybraniu kafelka Citrix datascaleer w panelu dostępu należy automatycznie zalogować się do usługi Citrix, w której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj program Citrixow w usłudze Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego dla platformy Citrix dla uwierzytelniania opartego na protokole Kerberos](citrix-netscaler-tutorial.md)