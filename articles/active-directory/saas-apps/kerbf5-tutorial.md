---
title: 'Samouczek: integracja z logowaniem jednokrotnym w usłudze Azure AD za pomocą klawisza F5 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne (SSO) między Azure Active Directory i F5.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.openlocfilehash: 2fd96c47e98a6f8a66a5fda555304c4448d772e0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646107"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu klawisza F5

W tym samouczku dowiesz się, jak zintegrować F5 z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD za pomocą klawisza F5 można:

* Kontrolka w usłudze Azure AD, która ma dostęp do klawisza F5.
* Zezwól użytkownikom na automatyczne logowanie do programu F5 przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

* Wdrożenie wspólnego rozwiązania wymaga następującej licencji:
    * Najlepszy pakiet F5 BIG-IP &reg; (lub)

    * F5 BIG — licencja autonomiczna Menedżera zasad dostępu IP &trade; (APM)

    * Licencja dodatku F5 BIG-IP Access Manager &trade; (APM) na istniejącym Traffic Manager lokalnym Big-IP F5 &reg; &trade; (LTM).

    * Oprócz powyższej licencji system F5 może również mieć licencję na:

        * Subskrypcja filtrowania adresów URL do korzystania z bazy danych kategorii adresów URL

        * Subskrypcja analizy protokołu IP F5 na potrzeby wykrywania i blokowania znanych ataków i złośliwego ruchu

        * Sprzętowy moduł zabezpieczeń (HSM) służący do zabezpieczania i zarządzania kluczami cyfrowymi w celu silnego uwierzytelniania

* Obsługa systemu F5 BIG-IP w modułach APM (LTM jest opcjonalna)

* Chociaż jest to opcjonalne, zdecydowanie zaleca się wdrożenie systemów F5 w [grupie urządzeń synchronizacji/pracy w trybie failover](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), która obejmuje aktywną parę wstrzymania z ruchomym adresem IP w celu zapewnienia wysokiej dostępności. Więcej nadmiarowości interfejsu można uzyskać przy użyciu protokołu kontroli agregacji (LACP). LACP zarządza podłączonymi interfejsami fizycznymi jako pojedynczym interfejsem wirtualnym (Grupa agregowania) i wykrywa wszystkie błędy interfejsów w grupie.

* W przypadku aplikacji Kerberos lokalne konto usługi AD na potrzeby delegowania ograniczonego.  Zapoznaj się z [dokumentacją F5](https://support.f5.com/csp/article/K43063049) , aby utworzyć konto delegowania usługi AD.

## <a name="access-guided-configuration"></a>Dostęp do konfiguracji z przewodnikiem

* Dostęp do konfiguracji z przewodnikiem jest obsługiwany w F5 TMOS w wersji 13.1.0.8 i nowszych. Jeśli w systemie BIG-IP działa wersja poniżej 13.1.0.8, zapoznaj się z sekcją **Konfiguracja zaawansowana** .

* Dostęp do konfiguracji z przewodnikiem przedstawia zupełnie nowe i usprawnione środowisko użytkownika. Ta Architektura oparta na przepływie pracy zapewnia intuicyjne, ponowne czynności konfiguracyjne dostosowane do wybranej topologii.

* Przed przystąpieniem do konfiguracji należy uaktualnić konfigurację z przewodnikiem, pobierając najnowszy pakiet przypadków użycia z [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748). W celu uaktualnienia postępuj zgodnie z poniższą procedurą.

    >[!NOTE]
    >Poniższe zrzuty ekranu dotyczą najnowszej wersji wydania (BIG-IP 15,0 z AGC wersja 5,0). Poniższe kroki konfiguracji są prawidłowe dla tego przypadku użycia w zakresie od 13.1.0.8 do najnowszej wersji BIG-IP.

1. W interfejsie użytkownika sieci Web z F5 BIG-IP kliknij opcję **dostęp >> konfiguracja przewodnika**.

2. Na stronie **Konfiguracja z przewodnikiem** kliknij pozycję **Uaktualnij konfigurację z przewodnikiem** w lewym górnym rogu.

    ![Zrzut ekranu przedstawiający stronę "Konfiguracja z przewodnikiem" z wybraną akcją "Uaktualnij konfigurację z przewodnikiem".](./media/kerbf5-tutorial/configure14.png) 

3. Na ekranie Konfiguracja przewodnika uaktualniania wybierz pozycję **Wybierz plik** , aby przekazać pobrany pakiet przypadków użycia, a następnie kliknij przycisk **Przekaż i zainstaluj** .

    ![Zrzut ekranu, na którym jest wyświetlany ekran "Wybierz plik" i "Przekaż i zainstaluj".](./media/kerbf5-tutorial/configure15.png) 

4. Po zakończeniu uaktualniania kliknij przycisk **Kontynuuj** .

    ![Zrzut ekranu przedstawiający okno dialogowe "zakończenie aktualizacji konfiguracji z przewodnikiem" i wybrany przycisk "Kontynuuj".](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Klawisz F5 obsługuje funkcję SSO zainicjowaną przez usługę **SP i dostawcy tożsamości**
* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](#configure-f5-single-sign-on-for-kerberos-application)

- [Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenariusze uwierzytelniania kluczy

Poza Azure Active Directory natywną integracją usług w przypadku nowoczesnych protokołów uwierzytelniania, takich jak Open ID Connect, SAML i WS-karmione, F5 rozszerza bezpieczny dostęp do aplikacji uwierzytelniania opartych na starszych wersjach dla dostępu wewnętrznego i zewnętrznego z usługą Azure AD, umożliwiając nowoczesne scenariusze (np. dostęp bez hasła) do tych aplikacji. Obejmuje to:

* Aplikacje uwierzytelniania oparte na nagłówkach

* Aplikacje uwierzytelniania Kerberos

* Uwierzytelnianie anonimowe lub brak nieskompilowanych aplikacji uwierzytelniania

* Aplikacje uwierzytelniania NTLM (ochrona przy użyciu podwójnych wskazówek dla użytkownika)

* Aplikacja oparta na formularzach (ochrona przy użyciu podwójnych wskazówek dla użytkownika)

## <a name="adding-f5-from-the-gallery"></a>Dodawanie F5 z galerii

Aby skonfigurować integrację klawisza F5 z usługą Azure AD, musisz dodać F5 z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **F5** w polu wyszukiwania.
1. Wybierz pozycję **F5** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla F5

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą klawisza F5 przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w F5.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą klawisza F5, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj funkcję rejestracji jednokrotnej F5](#configure-f5-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego F5](#create-f5-test-user)** , aby dysponować odpowiednikiem B. Simon w F5, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **F5** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź pozycję **XML metadanych Federacji** i **certyfikat (base64)** , a następnie wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie F5** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do klawisza F5.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **F5**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .
1. Kliknij pozycję **dostęp warunkowy** .
1. Kliknij pozycję **nowe zasady**.
1. Teraz możesz zobaczyć aplikację F5 jako zasób dla zasad dostępu warunkowego i zastosować dowolny dostęp warunkowy, w tym uwierzytelnianie wieloskładnikowe, kontrolę dostępu na podstawie urządzenia lub zasady ochrony tożsamości.

## <a name="configure-f5-sso"></a>Konfigurowanie rejestracji jednokrotnej F5

- [Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos

### <a name="guided-configuration"></a>Konfiguracja z przewodnikiem

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy F5 (Kerberos) jako administrator i wykonaj następujące czynności:

1. Konieczne będzie zaimportowanie certyfikatu metadanych do F5, który będzie używany później w procesie instalacji.

1. Przejdź do **systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony. Określ **nazwę certyfikatu** (zostanie przywoływana później w konfiguracji). W **źródle certyfikatu** wybierz pozycję Przekaż plik Określ certyfikat pobrany z platformy Azure podczas konfigurowania logowania jednokrotnego SAML. Kliknij pozycję **Importuj**.

    ![Zrzut ekranu przedstawiający stronę "S S L/Źródło klucza" z wyróżnioną nazwą "Nazwa certyfikatu", "Przekaż plik" i wybranym przyciskiem "Importuj".](./media/kerbf5-tutorial/configure01.png) 

1. Ponadto wymagany jest **certyfikat SSL dla nazwy hosta aplikacji. Przejdź do systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony. **Typ importu** to **PKCS 12 (IIS)**. Określ **nazwę klucza** (zostanie przywoływana później w konfiguracji) i określ plik PFX. Określ **hasło** dla pliku PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie Nasza nazwa aplikacji jest `Kerbapp.superdemo.live` używana jako certyfikat wieloznaczny nasz KeyName to `WildCard-SuperDemo.live`

    ![Zrzut ekranu pokazujący stronę "S S L/Źródło klucza" z wprowadzonymi wartościami i wybranym przyciskiem "Importuj".](./media/kerbf5-tutorial/configure02.png) 
 
1. Użyjemy środowiska z przewodnikiem, aby skonfigurować dostęp do Federacji i aplikacji usługi Azure AD. Przejdź do – F5 BIG-IP **Main** i wybierz pozycję **Access > konfiguracji z przewodnikiem > federacyjnej > dostawcy usług SAML**. Kliknij przycisk **dalej** , a następnie kliknij przycisk **dalej** , aby rozpocząć konfigurację.

    ![Zrzut ekranu przedstawiający stronę "Konfiguracja z przewodnikiem" z wyróżnioną ikoną "Federacja" oraz "S A M L Service Provider".](./media/kerbf5-tutorial/configure03.png) 

    ![Zrzut ekranu przedstawiający stronę "Konfiguracja z przewodnikiem-S A M L Service Provider" z wybranym przyciskiem "dalej".](./media/kerbf5-tutorial/configure04.png)

1. Podaj **nazwę konfiguracji**. Określ **Identyfikator jednostki** (taki sam jak skonfigurowany w konfiguracji aplikacji usługi Azure AD). Określ **nazwę hosta**. Dodaj **Opis** odwołania. Zaakceptuj pozostałe wpisy domyślne i wybierz, a następnie kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu pokazujący właściwości "dostawca usług" z wyróżnionymi polami tekstowymi "Nazwa hosta" i "opis" oraz wybrany przycisk "Zapisz & dalej".](./media/kerbf5-tutorial/configure05.png) 

1. W tym przykładzie tworzymy nowy serwer wirtualny jako 192.168.30.200 z portem 443. Określ adres IP serwera wirtualnego w **adresie docelowym**. Wybierz **profil SSL** klienta, a następnie wybierz pozycję Utwórz nowy. Określ wcześniej przekazany certyfikat aplikacji (certyfikat wieloznaczny w tym przykładzie) i skojarzony klucz, a następnie kliknij przycisk **zapisz & dalej**.

    >[!NOTE]
    >w tym przykładzie nasz wewnętrzny serwer WebServer jest uruchomiony na porcie 80 i chcemy opublikować go z 443.

    ![Zrzut ekranu przedstawiający stronę "właściwości serwera wirtualnego" z wyróżnionym polem tekstowym "adres docelowy" i wybranym przyciskiem "Zapisz & dalej".](./media/kerbf5-tutorial/configure06.png)

1. W obszarze **Wybierz metodę, aby skonfigurować łącznik dostawcy tożsamości**, określ metadane, kliknij pozycję Wybierz plik i przekaż pobrany wcześniej plik XML metadanych z usługi Azure AD. Określ unikatową **nazwę** łącznika SAML dostawcy tożsamości. Wybierz **certyfikat podpisywania metadanych** , który został wcześniej przekazany. Kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu przedstawiający stronę "Ustawienia zewnętrznego łącznika dostawcy tożsamości" z wyróżnionym polem tekstowym "nazwa" i wybranym przyciskiem "Zapisz & dalej".](./media/kerbf5-tutorial/configure07.png)  

1. W obszarze **wybierz pulę** określ pozycję **Utwórz nową** (Alternatywnie wybierz już istniejącą pulę). Zezwól na ustawienie wartości domyślnej.    W obszarze serwery puli wpisz adres IP w obszarze **adres IP/nazwa węzła**. Określ **port**. Kliknij przycisk **zapisz & dalej**.
 
    ![Zrzut ekranu przedstawiający stronę "właściwości puli" z wyróżnionymi polami tekstowymi "adres IP/nazwa węzła" i "Port" oraz wybrany przycisk "Zapisz & dalej".](./media/kerbf5-tutorial/configure08.png)

1. Na ekranie Ustawienia pojedynczej Sign-On wybierz pozycję **Włącz logowanie jednokrotne**. W obszarze **wybrany typ pojedynczego Sign-On** wybierz pozycję **Kerberos**. Zastąp element **Session. SAML. Last. Identity**  identyfikatorem **Session. SAML. Last. ATTR. Name. Identity** w obszarze **Źródło nazwy użytkownika** (Ta zmienna jest ustawiana przy użyciu mapowania oświadczeń w usłudze Azure AD). Wybierz pozycję **Pokaż ustawienia zaawansowane**. W obszarze Typ **obszaru Kerberos** wpisz nazwę domeny. W obszarze **nazwa konta/hasło konta** Określ konto delegowania APM i hasło. Określ adres IP kontrolera domeny w polu **centrum dystrybucji kluczy** . Kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu pokazujący ustawienia pojedynczej Sign-On z wyróżnionymi polami tekstowymi i wybranym przyciskiem Zapisz & dalej.](./media/kerbf5-tutorial/configure09.png)   

1. Na potrzeby tych wskazówek pominiemy testy punktów końcowych.  Zapoznaj się z dokumentacją F5, aby uzyskać szczegółowe informacje.  Na ekranie wybierz pozycję **zapisz & dalej**.

    ![Zrzut ekranu pokazujący stronę "właściwości sprawdzania punktu końcowego" i wybrany przycisk "Zapisz & dalej".](./media/kerbf5-tutorial/configure10.png) 

1. Zaakceptuj wartości domyślne i kliknij przycisk **zapisz & dalej**. Szczegółowe informacje dotyczące ustawień zarządzania sesją SAML można znaleźć w dokumentacji dotyczącej języka F5.


    ![Zrzut ekranu, na którym zostanie wyświetlona strona "ustawienia limitu czasu" z wybranym przyciskiem Zapisz & dalej.](./media/kerbf5-tutorial/configure11.png) 
 
1. Przejrzyj ekran podsumowanie i wybierz pozycję **Wdróż** , aby skonfigurować Big-IP.
 
    ![Zrzut ekranu przedstawiający stronę "aplikacja jest gotowa do wdrożenia" z wyróżnioną sekcją "Podsumowanie" i wybranym przyciskiem "wdróż".](./media/kerbf5-tutorial/configure12.png)

1. Po skonfigurowaniu aplikacji kliknij przycisk **Zakończ**.

    ![Zrzut ekranu pokazujący stronę "wdrożona aplikacja" z wybranym przyciskiem "Zakończ".](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

>[!NOTE]
>Aby uzyskać odwołanie, kliknij [tutaj](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="configuring-an-active-directory-aaa-server"></a>Konfigurowanie serwera Active Directory AAA

Serwer Active Directory AAA można skonfigurować w Menedżerze zasad dostępu (APM), aby określić kontrolery domeny i poświadczenia dla usługi APM, które będą używane do uwierzytelniania użytkowników.

1. Na karcie Główny kliknij pozycję **zasady dostępu > serwery AAA > Active Directory**. Zostanie otwarty ekran listy serwerów Active Directory.

2. Kliknij pozycję **Utwórz**. Zostanie otwarty ekran nowe właściwości serwera.

3. W polu **Nazwa** wpisz unikatową nazwę serwera uwierzytelniania.

4. W polu **nazwa domeny** wpisz nazwę domeny systemu Windows.

5. Dla ustawienia **połączenie z serwerem** wybierz jedną z następujących opcji:

   * Wybierz pozycję **Użyj puli** , aby skonfigurować wysoką dostępność serwera AAA.

   * Wybierz pozycję **Direct** , aby skonfigurować serwer AAA dla funkcjonalności autonomicznej.

6. W przypadku wybrania **bezpośrednie** wpisz nazwę w polu **kontroler domeny** .

7. Jeśli wybrano opcję Użyj **puli**, skonfiguruj pulę:

   * Wpisz nazwę w polu **Nazwa puli kontrolera domeny** .

   * Określ **kontrolery domeny** w puli, wpisując adres IP i nazwę hosta dla każdego z nich, a następnie klikając przycisk **Dodaj** .

   * Aby monitorować kondycję serwera AAA, można wybrać monitor kondycji: w tym przypadku jest odpowiedni tylko monitor **gateway_icmp** . można wybrać ją z listy **Monitor puli serwerów** .

8. W polu **nazwa administratora** wpisz nazwę z uwzględnieniem wielkości liter dla administratora, który ma Active Directory uprawnienia administracyjne. System APM używa informacji w polach **nazwa administratora** i **hasło administratora** dla kwerendy AD. Jeśli Active Directory jest skonfigurowany dla zapytań anonimowych, nie trzeba podawać nazwy administratora. W przeciwnym razie system APM potrzebuje konta z wystarczającymi uprawnieniami do powiązania z serwerem Active Directory, pobierania informacji o grupie użytkowników i pobierania Active Directory zasad haseł w celu obsługi funkcji związanych z hasłami. (System APM musi pobrać zasady haseł, na przykład w przypadku wybrania opcji Monituj użytkownika o zmianę hasła przed wygaśnięciem w akcji kwerendy AD). Jeśli nie podasz informacji o koncie administratora w tej konfiguracji, system APM używa konta użytkownika do pobierania informacji. Ta wartość działa, jeśli konto użytkownika ma wystarczające uprawnienia.

9. W polu **hasło administratora** wpisz hasło administratora skojarzone z nazwą domeny.

10. W polu **Weryfikuj hasło administratora** wpisz ponownie hasło administratora skojarzone z ustawieniem **nazwa domeny** .

11. W polu **okres istnienia pamięci podręcznej grupy** wpisz liczbę dni. Domyślny okres istnienia to 30 dni.

12. W polu **okres istnienia pamięci podręcznej obiektu zabezpieczeń hasła** wpisz liczbę dni. Domyślny okres istnienia to 30 dni.

13. Z listy **typ szyfrowania wstępnego uwierzytelniania Kerberos** wybierz typ szyfrowania. Wartość domyślna to **none**. W przypadku określenia typu szyfrowania system BIG-IP zawiera dane wstępnego uwierzytelniania Kerberos w ramach pierwszego żądania usługi uwierzytelniania (zgodnie z żądaniem).

14. W polu **limit czasu** wpisz przedział czasu (w sekundach) dla serwera AAA. (To ustawienie jest opcjonalne).

15. Kliknij przycisk **Zakończono**. Nowy serwer zostanie wyświetlony na liście. Spowoduje to dodanie nowego serwera Active Directory do listy serwerów Active Directory.

    ![Zrzut ekranu pokazujący sekcje "właściwości ogólne" i "Konfiguracja".](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>Konfiguracja SAML

1. Konieczne będzie zaimportowanie certyfikatu metadanych do F5, który będzie używany później w procesie instalacji. Przejdź do **systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony.

    ![Zrzut ekranu przedstawiający stronę "import S certyfikatu/źródła klucza" z wybranym przyciskiem "Importuj".](./media/kerbf5-tutorial/configure18.png)

2. Aby skonfigurować dostawcy tożsamości SAML, **Przejdź do opcji dostęp > federacyjnej > SAML: Service Provider > zewnętrznych łączników dostawcy tożsamości**, a następnie kliknij pozycję **Utwórz > z metadanych**.

    ![Zrzut ekranu przedstawiający stronę "S A M L Service Provider" z opcją "z metadanych" wybraną z listy rozwijanej "Utwórz".](./media/kerbf5-tutorial/configure19.png)

    ![Zrzut ekranu przedstawiający okno dialogowe "Tworzenie nowego S A M d P łącznika".](./media/kerbf5-tutorial/configure20.png)

    ![Zrzut ekranu przedstawiający okno "Edycja S A M d P łącznik" z wybraną opcją "Ustawienia ogólne".](./media/kerbf5-tutorial/configure21.png)

    ![Zrzut ekranu przedstawiający okno "Edytuj S A M d P łącznik" "z wybraną opcją" Logowanie jednokrotne ".](./media/kerbf5-tutorial/configure22.png)

    ![Zrzut ekranu przedstawiający okno "Edycja S A M d P łącznik" z wybraną opcją "Ustawienia zabezpieczeń".](./media/kerbf5-tutorial/configure23.png)

    ![Zrzut ekranu przedstawiający okno "Edytuj S A M d P łącznik" z wybraną opcją "S L O" Ustawienia usługi ".](./media/kerbf5-tutorial/configure24.png)

1. Aby skonfigurować protokół SAML SP, przejdź do opcji **dostęp > federacyjnego > dostawcy usługi SAML > lokalnych usług Sp** , a następnie kliknij przycisk **Utwórz**. Uzupełnij poniższe informacje i kliknij przycisk **OK**.

    * Nazwa typu: KerbApp200SAML
    * Identyfikator jednostki *: https://kerb-app.com.cutestat.com
    * Ustawienia nazw SP
    * Schemat: https
    * Host: kerbapp200. predemonstracyjny. Live
    * Opis: kerbapp200. predemonstracyjny. Live

     ![Zrzut ekranu przedstawiający okno "Edytuj S A M L S P" z wybranymi "ustawieniami ogólnymi".](./media/kerbf5-tutorial/configure25.png)

     b. Wybierz pozycję Konfiguracja SP, KerbApp200SAML, a następnie kliknij pozycję **Powiązywanie/Usuwanie powiązania dostawcy tożsamości łączników**.

     ![Zrzut ekranu przedstawiający stronę "S A M L Service Provider-Local S P" z wybraną pozycją "KerbAPP200 S A M L".](./media/kerbf5-tutorial/configure26.png)

     ![Zrzut ekranu pokazujący wybrany przycisk "Powiązywanie/Usuwanie powiązania I d P".](./media/kerbf5-tutorial/configure27.png)

     c. Kliknij przycisk **Dodaj nowy wiersz** i wybierz **zewnętrzny łącznik dostawcy tożsamości** utworzony w poprzednim kroku, kliknij przycisk **Aktualizuj**, a następnie kliknij przycisk **OK**.

     ![Zrzut ekranu, na którym jest wyświetlany przycisk "Edytuj S A M I d PS, który korzysta z tego okna.](./media/kerbf5-tutorial/configure28.png)

1. Aby skonfigurować Logowanie jednokrotne protokołu Kerberos, przejdź do **dostępu > logowanie** jednokrotne > Kerberos, pełne informacje i kliknij przycisk **Zakończono**.

    >[!Note]
    > Konieczne będzie utworzenie i określenie konta delegowania protokołu Kerberos. Odwołaj się do KCD sekcji (zobacz dodatek dla odwołań do zmiennych)

    * **Źródło nazwy użytkownika**: Session. SAML. Last. ATTR. Name. http: \/ /schemas.xmlSOAP.org/ws/2005/05/identity/claims/givenName

    * **Źródło obszaru użytkownika**: Session. Logon. Last. domain

        ![Zrzut ekranu przedstawiający stronę "pojedyncze Sign-On-właściwości" z wyróżnionymi polami tekstowymi "username source" i "Źródło obszaru użytkownika".](./media/kerbf5-tutorial/configure29.png)

1. W celu skonfigurowania profilu dostępu przejdź do **dostępu > profilu/zasad > profilu dostępu (dla zasad sesji)**, kliknij przycisk **Utwórz**, uzupełnij poniższe informacje i kliknij przycisk **Zakończono**.

    * Nazwa: KerbApp200
    * Typ profilu: wszystkie
    * Zakres profilu: profil
    * Języki: angielski

        ![Zrzut ekranu przedstawiający stronę "Profile/zasady — właściwości" z wyróżnionymi polami tekstowymi "name", "Typ profilu" i "langauges".](./media/kerbf5-tutorial/configure30.png)

1. Kliknij nazwę, KerbApp200, wykonaj poniższe informacje, a następnie kliknij przycisk **Aktualizuj**.

    * Plik cookie domeny: niedemonstracja. Live
    * Konfiguracja logowania jednokrotnego: KerAppSSO_sso

        ![Zrzut ekranu przedstawiający stronę "S S/domeny uwierzytelniania" z wyróżnioną listą rozwijaną "domena cookie" i "Konfiguracja" S S ", a wybrany przycisk" Aktualizuj ".](./media/kerbf5-tutorial/configure31.png)

1. Kliknij pozycję **zasady dostępu** , a następnie kliknij pozycję **Edytuj zasady dostępu** dla profilu "KerbApp200".

    ![Zrzut ekranu przedstawiający stronę "zasady dostępu" z wybraną akcją "Edytuj zasady dostępu dla profilu KerbApp200".](./media/kerbf5-tutorial/configure32.png)

    ![Zrzut ekranu przedstawiający stronę "zasady dostępu" oraz okno dialogowe "S A M L Authentication S P".](./media/kerbf5-tutorial/configure33.png)

    ![Zrzut ekranu przedstawiający stronę "zasady dostępu" i okno dialogowe "zmienna Assign" z wyróżnionymi polami tekstowymi "przydział".](./media/kerbf5-tutorial/configure34.png)

    * **Session. Logon. Last. usernameUPN Expr {[mcget {Session. SAML. Last. Identity}]}**

    * **preprezentacja tekstu Session. AD. lastactualdomain. Live**

        ![Zrzut ekranu przedstawiający stronę "zasady dostępu" i okno dialogowe "Active Directory" z wyróżnionym polem tekstowym "SearchFilter".](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName =% {Session. Logon. Last. usernameUPN})**

        ![Zrzut ekranu przedstawiający stronę "zasady dostępu" z oknem dialogowym "A D reguły gałęzi zapytania".](./media/kerbf5-tutorial/configure36.png)

        ![Zrzut ekranu, który pokazuje wyróżnione pola tekstowe "zmienna niestandardowa" i "wyrażenie niestandardowe".](./media/kerbf5-tutorial/configure37.png)

    * **Session. Logon. Last. username Expr {"[mcget {Session. AD. Last. ATTR. sAMAccountName}]"}**

        ![Zrzut ekranu, na którym jest wyróżnione pole tekstowe "Nazwa użytkownika ze strony logowania".](./media/kerbf5-tutorial/configure38.png)

    * **mcget {Session. Logon. Last. username}**
    * **mcget {Session. Logon. Last. Password**

1. Aby dodać nowy węzeł, przejdź do węzła **ruch lokalny > węzły > listy węzłów, kliknij przycisk Utwórz**, uzupełnij poniższe informacje, a następnie kliknij przycisk **Zakończono**.

    * Nazwa: KerbApp200
    * Opis: KerbApp200
    * Adres: 192.168.20.200

        ![Zrzut ekranu pokazujący stronę "nowy węzeł" z wyróżnionymi polami tekstowymi "name", "Description" i "Address" oraz wybranym przyciskiem "gotowe".](./media/kerbf5-tutorial/configure39.png)

1. W celu utworzenia nowej puli przejdź do **Przystawka ruch lokalny > pule > puli, kliknij przycisk Utwórz**, uzupełnij poniższe informacje i kliknij przycisk **Zakończono**.

    * Nazwa: KerbApp200-Pool
    * Opis: KerbApp200-Pool
    * Monitory kondycji: http
    * Adres: 192.168.20.200
    * Port usługi: 81

        ![Zrzut ekranu pokazujący stronę "Nowa pula" z wprowadzonymi wartościami i wybranym przyciskiem "gotowe".](./media/kerbf5-tutorial/configure40.png)

1. W celu utworzenia serwera wirtualnego przejdź do obszarze **ruch lokalny > serwery wirtualne > listy serwerów wirtualnych > +**, uzupełnij poniższe informacje i kliknij przycisk **Zakończ**.

    * Nazwa: KerbApp200
    * Adres docelowy/maska: 192.168.30.200 hosta
    * Port usługi: port 443 HTTPS
    * Profil dostępu: KerbApp200
    * Określ Profil dostępu utworzony w poprzednim kroku

        ![Zrzut ekranu przedstawiający stronę "Lista serwerów wirtualnych" z wyróżnionymi polami tekstowymi "nazwa", "adres docelowy/maska" i "Port usługi".](./media/kerbf5-tutorial/configure41.png)

        ![Zrzut ekranu przedstawiający stronę "Lista serwerów wirtualnych" z wyróżnioną listą rozwijaną "Profil dostępu".](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>Konfigurowanie delegowania protokołu Kerberos 

>[!NOTE]
>Aby uzyskać odwołanie, kliknij [tutaj](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf)

*  **Krok 1:** Utwórz konto delegowania

    **Przykład:**
    * Nazwa domeny: **preprezentacja. Live**

    * Nazwa konta sam: **Big-ipuser**

    * New-ADUser-Name "konto delegowania APM"-UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -sAMAccountName "Big-ipuser"-PasswordNeverExpires $true-Enabled $true-AccountPassword (read-host-assecurestring "Password! 1234")

* **Krok 2:** Ustawianie nazwy SPN (na koncie delegowania APM)

    **Przykład:**
    * Setspn — **host/Big-ipuser. predemonstracyjny. Live** Big-ipuser

* **Krok 3.** Delegowanie nazwy SPN (dla konta App Service) skonfiguruj odpowiednie delegowanie dla konta delegowania F5.
    W poniższym przykładzie konto delegowania APM jest konfigurowane do KCD for FRP-APP1. predemonstracyjne. Aplikacja na żywo.

    ![Konfiguracja F5 (Kerberos)](./media/kerbf5-tutorial/configure43.png)

* Podaj szczegóły, jak wspomniano w powyższym dokumencie [referencyjnym.](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)

### <a name="create-f5-test-user"></a>Utwórz użytkownika testowego F5

W tej sekcji utworzysz użytkownika o nazwie B. Simon w F5. Pracuj z [zespołem pomocy technicznej dla klientów F5](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) , aby dodać użytkowników na platformie F5. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka F5 w panelu dostępu należy automatycznie zalogować się do F5, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługę Azure AD](https://aad.portal.azure.com/)

- [Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka](headerf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

- [F5 BIG-IP APM i integracja z usługą Azure AD na potrzeby bezpiecznego dostępu hybrydowego](../manage-apps/f5-aad-integration.md)

- [Samouczek pozwalający wdrożyć MASZYNę wirtualną z wirtualnym wydaniem F5 BIG-IP w usłudze Azure IaaS na potrzeby bezpiecznego dostępu hybrydowego](../manage-apps/f5-bigip-deployment-guide.md)

- [Samouczek dotyczący Azure Active Directory integracji z logowaniem jednokrotnym przy użyciu protokołu F5 BIG-IP dla sieci VPN bez hasła](../manage-apps/f5-aad-password-less-vpn.md)