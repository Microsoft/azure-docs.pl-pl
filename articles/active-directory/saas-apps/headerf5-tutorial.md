---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu klawisza F5 | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i F5.
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
ms.openlocfilehash: 8706ac588f2cc868805de7126140a1edd4b8c735
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92445532"
---
# <a name="tutorial-configure-single-sign-on-sso-between-azure-active-directory-and-f5"></a>Samouczek: Konfigurowanie logowania jednokrotnego między Azure Active Directory i F5

W tym samouczku dowiesz się, jak zintegrować F5 z Azure Active Directory (Azure AD). Po zintegrowaniu z usługą Azure AD za pomocą klawisza F5 można:

* Kontrolka w usłudze Azure AD, która ma dostęp do klawisza F5.
* Zezwól użytkownikom na automatyczne logowanie do programu F5 przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z logowaniem jednokrotnym w usłudze Azure AD, zobacz Logowanie jednokrotne [do aplikacji w Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

* Wdrożenie wspólnego rozwiązania wymaga następującej licencji:

    * F5 BIG-IP® najlepszym pakietem (lub) 

    * F5 BIG-IP — licencja autonomiczna Menedżera zasad (APM)™ 

    * F5 BIG-IP Access Manager — licencja dodatku™ (APM) na istniejącym® lokalnym™ Traffic Managerm Big-IP F5 (LTM).

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

1. Na stronie **Konfiguracja z przewodnikiem** kliknij pozycję **Uaktualnij konfigurację z przewodnikiem** w lewym górnym rogu.

    ![Zrzut ekranu przedstawia stronę konfiguracji z przewodnikiem z linkiem konfiguracji z przewodnikiem aktualizacja.](./media/headerf5-tutorial/configure14.png) 

1. Na ekranie Konfiguracja przewodnika uaktualniania wybierz pozycję **Wybierz plik** , aby przekazać pobrany pakiet przypadków użycia, a następnie kliknij przycisk **Przekaż i zainstaluj** .

    ![Zrzut ekranu przedstawia okno dialogowe Konfiguracja z przewodnikiem po uaktualnieniu z wybraną opcją plik.](./media/headerf5-tutorial/configure15.png) 

1. Po zakończeniu uaktualniania kliknij przycisk **Kontynuuj** .

    ![Zrzut ekranu przedstawia okno dialogowe Konfiguracja przewodnika uaktualniania z komunikatem ukończenia.](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* F5 SSO można skonfigurować na trzy różne sposoby.

- [Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Scenariusze uwierzytelniania kluczy

* Poza Azure Active Directory natywną integracją usług w przypadku nowoczesnych protokołów uwierzytelniania, takich jak Open ID Connect, SAML i WS-karmione, F5 rozszerza bezpieczny dostęp do aplikacji uwierzytelniania opartych na starszych wersjach dla dostępu wewnętrznego i zewnętrznego z usługą Azure AD, umożliwiając nowoczesne scenariusze (np. dostęp bez hasła) do tych aplikacji. Obejmuje to:

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
1. Teraz możesz zobaczyć aplikację F5 jako zasób dla zasad urzędu certyfikacji i zastosować dowolny dostęp warunkowy, w tym uwierzytelnianie wieloskładnikowe, kontrolę dostępu na podstawie urządzenia lub zasady ochrony tożsamości.

## <a name="configure-f5-sso"></a>Konfigurowanie rejestracji jednokrotnej F5

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurowanie logowania jednokrotnego dla aplikacji na podstawie nagłówka

### <a name="guided-configuration"></a>Konfiguracja z przewodnikiem

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do firmowej firmy jako administrator i wykonaj następujące czynności:

1. Przejdź do **systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony. Określ **nazwę certyfikatu** (zostanie przywoływana później w konfiguracji). W **źródle certyfikatu**wybierz pozycję Przekaż plik Określ certyfikat pobrany z platformy Azure podczas konfigurowania logowania jednokrotnego SAML. Kliknij pozycję **Importuj**.

    ![Zrzut ekranu przedstawia listę certyfikatu S S L, w której można wybrać nazwę certyfikatu i źródło certyfikatu.](./media/headerf5-tutorial/configure12.png)
 
1. Ponadto wymagany jest **certyfikat SSL dla nazwy hosta aplikacji. Przejdź do systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony. **Typ importu** to **PKCS 12 (IIS)**. Określ **nazwę klucza** (zostanie przywoływana później w konfiguracji) i określ plik PFX. Określ **hasło** dla pliku PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie Nasza nazwa aplikacji jest `Headerapp.superdemo.live` używana jako certyfikat wieloznaczny nasz KeyName to `WildCard-SuperDemo.live` .

    ![Zrzut ekranu przedstawia stronę źródła certyfikatu/klucza S s.](./media/headerf5-tutorial/configure13.png)

1. Użyjemy środowiska z przewodnikiem, aby skonfigurować dostęp do Federacji i aplikacji usługi Azure AD. Przejdź do – F5 BIG-IP **Main** i wybierz pozycję **Access > konfiguracji z przewodnikiem > federacyjnej > dostawcy usług SAML**. Kliknij przycisk **dalej** , a następnie kliknij przycisk **dalej** , aby rozpocząć konfigurację.

    ![Zrzut ekranu przedstawia stronę konfiguracji z przewodnikiem z wybraną opcją Federacja.](./media/headerf5-tutorial/configure01.png)

    ![Zrzut ekranu przedstawia stronę Dostawca usługi SAML.](./media/headerf5-tutorial/configure02.png)
 
1. Podaj **nazwę konfiguracji**. Określ **Identyfikator jednostki** (taki sam jak skonfigurowany w konfiguracji aplikacji usługi Azure AD). Określ **nazwę hosta**. Dodaj **Opis** odwołania. Zaakceptuj pozostałe wpisy domyślne i wybierz, a następnie kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu przedstawia stronę właściwości dostawcy usług.](./media/headerf5-tutorial/configure03.png) 

1. W tym przykładzie tworzymy nowy serwer wirtualny jako 192.168.30.20 z portem 443. Określ adres IP serwera wirtualnego w **adresie docelowym**. Wybierz **profil SSL**klienta, a następnie wybierz pozycję Utwórz nowy. Określ wcześniej przekazany certyfikat aplikacji (certyfikat wieloznaczny w tym przykładzie) i skojarzony klucz, a następnie kliknij przycisk **zapisz & dalej**.

    >[!NOTE]
    >w tym przykładzie nasz wewnętrzny serwer WebServer jest uruchomiony na porcie 888 i chcemy opublikować go z 443.

    ![Zrzut ekranu przedstawia stronę właściwości serwera wirtualnego.](./media/headerf5-tutorial/configure04.png) 

1. W obszarze **Wybierz metodę, aby skonfigurować łącznik dostawcy tożsamości**, określ metadane, kliknij pozycję Wybierz plik i przekaż pobrany wcześniej plik XML metadanych z usługi Azure AD. Określ unikatową **nazwę** łącznika SAML dostawcy tożsamości. Wybierz **certyfikat podpisywania metadanych** , który został wcześniej przekazany. Kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu przedstawia stronę ustawień łącznika zewnętrzny dostawca tożsamości.](./media/headerf5-tutorial/configure05.png)
 
1. W obszarze **wybierz pulę**określ pozycję **Utwórz nową** (Alternatywnie wybierz już istniejącą pulę). Zezwól na ustawienie wartości domyślnej. W obszarze serwery puli wpisz adres IP w obszarze **adres IP/nazwa węzła**. Określ **port**. Kliknij przycisk **zapisz & dalej**.

    ![Zrzut ekranu przedstawia stronę właściwości puli.](./media/headerf5-tutorial/configure06.png)

1. Na ekranie Ustawienia pojedynczej Sign-On wybierz pozycję **Włącz logowanie jednokrotne**. W obszarze wybrany typ pojedynczego Sign-On wybierz opcję **oparty na nagłówku HTTP**. Zastąp element **Session. SAML. Last. Identity** identyfikatorem **Session. SAML. Last. ATTR. Name. Identity** w obszarze Źródło nazwy użytkownika (Ta zmienna jest ustawiana przy użyciu mapowania oświadczeń w usłudze Azure AD). W obszarze nagłówki rejestracji jednokrotnej.

    * **Nagłówek: Moja autoryzacja**

    * **Wartość nagłówka:% {Session. SAML. Last. ATTR. Name. Identity}**

    * Kliknij przycisk **zapisz & dalej**

    Zapoznaj się z dodatkiem, aby uzyskać pełną listę zmiennych i wartości. W razie potrzeby można dodać więcej nagłówków.

    >[!NOTE]
    >Nazwa konta to utworzone konto delegowania F5 (Sprawdź dokumentację dotyczącą F5).

    ![Zrzut ekranu przedstawia stronę ustawień pojedynczej Sign-On.](./media/headerf5-tutorial/configure07.png) 

1. Na potrzeby tych wskazówek pominiemy testy punktów końcowych.  Zapoznaj się z dokumentacją F5, aby uzyskać szczegółowe informacje. Wybierz pozycję **zapisz & dalej**.

    ![Zrzut ekranu przedstawia stronę właściwości sprawdzania punktów końcowych.](./media/headerf5-tutorial/configure08.png)

1. Zaakceptuj wartości domyślne i kliknij przycisk **zapisz & dalej**. Zapoznaj się z dokumentacją F5, aby uzyskać szczegółowe informacje dotyczące ustawień zarządzania sesją SAML.

    ![Zrzut ekranu przedstawia stronę Ustawienia limitu czasu.](./media/headerf5-tutorial/configure09.png)

1. Przejrzyj ekran podsumowanie i wybierz pozycję **Wdróż** , aby skonfigurować Big-IP. Kliknij przycisk **Zakończ**.

    ![Zrzut ekranu pokazuje, że aplikacja jest gotowa do wdrożenia.](./media/headerf5-tutorial/configure10.png)

    ![Zrzut ekranu przedstawia stronę wdrożona aplikacja.](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

Ta sekcja jest przeznaczona do użycia, jeśli nie można użyć konfiguracji z przewodnikiem lub dodać/zmodyfikować dodatkowe parametry. Wymagany jest certyfikat TLS/SSL dla nazwy hosta aplikacji.

1. Przejdź do **systemu > zarządzanie certyfikatami > zarządzania certyfikatami, > listy certyfikatów SSL**. Wybierz pozycję **Importuj** z prawej strony. **Typ importu** to **PKCS 12 (IIS)**. Określ **nazwę klucza** (zostanie przywoływana później w konfiguracji) i określ plik PFX. Określ **hasło** dla pliku PFX. Kliknij pozycję **Importuj**.

    >[!NOTE]
    >W przykładzie Nasza nazwa aplikacji jest `Headerapp.superdemo.live` używana jako certyfikat wieloznaczny nasz KeyName to `WildCard-SuperDemo.live` .
  
    ![Zrzut ekranu przedstawia stronę źródłową certyfikatu/klucza S dla konfiguracji zaawansowanej.](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Dodawanie nowego serwera sieci Web do BigIP-F5

1. Kliknij pozycję **Main > IApps > Usługi aplikacji > aplikacji > Utwórz**.

1. Podaj **nazwę** i w obszarze **szablon** wybierz **F5. http**.
 
    ![Zrzut ekranu przedstawia stronę Usługi aplikacji z zaznaczonym szablonem.](./media/headerf5-tutorial/configure18.png)

1. W takim przypadku będziemy publikować nasze HeaderApp2 zewnętrzne jako HTTPS, w **jaki sposób system Big-IP obsługuje ruch SSL**? określamy **przerwanie protokołu SSL z poziomu klienta, zwykłego tekstu na serwerach (odciążanie protokołu SSL)**. Określ certyfikat i klucz, pod **którym ma być używany certyfikat SSL?** i **którego klucza prywatnego SSL chcesz używać?**. Określ adres IP serwera wirtualnego w obszarze **jakiego adresu IP chcesz użyć dla serwera wirtualnego?**. 

    * **Określ inne szczegóły**

        * Nazwa FQDN  

        * Określ wyjście z puli aplikacji lub Utwórz nową.

        * W przypadku tworzenia nowego serwera aplikacji Określ **wewnętrzny adres IP** i **numer portu**.

        ![Zrzut ekranu przedstawia okienko, w którym można określić te szczegóły.](./media/headerf5-tutorial/configure19.png) 

1. Kliknij przycisk **Zakończono**.

    ![Zrzut ekranu przedstawia stronę po zakończeniu.](./media/headerf5-tutorial/configure20.png) 

1. Upewnij się, że właściwości aplikacji mogą być modyfikowane. Kliknij pozycję **Main > IApps > usługi aplikacji: aplikacje >> HeaderApp2**. Usuń zaznaczenie opcji **ścisłe aktualizacje** (zmodyfikujemy niektóre ustawienia poza graficznym interfejsem użytkownika). Kliknij przycisk **Aktualizuj** .

    ![Zrzut ekranu przedstawia stronę Usługi aplikacji z wybraną kartą właściwości.](./media/headerf5-tutorial/configure21.png) 

1. W tym momencie powinno być możliwe przeglądanie serwera wirtualnego.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Konfigurowanie F5 jako SP i platformy Azure jako dostawcy tożsamości

1.  Kliknij pozycję **dostęp > federacyjnego> dostawcy usług SAML > lokalnej usługi SP > kliknij pozycję Utwórz lub + podpisz**.

    ![Zrzut ekranu przedstawia stronę informacje o tej wielkiej I P. ](./media/headerf5-tutorial/configure22.png)

1. Określ szczegóły usługi Service Provider. Określ **nazwę** reprezentującą konfigurację F5 Sp. Określ **Identyfikator jednostki** (na ogół taki sam jak adres URL aplikacji).

    ![Zrzut ekranu przedstawia stronę Dostawca usługi SAML przy użyciu okna dialogowego Tworzenie nowej usługi SAML S P.](./media/headerf5-tutorial/configure23.png)

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowej usługi SAML S P z wybranymi ustawieniami punktu końcowego.](./media/headerf5-tutorial/configure24.png)

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowej usługi SAML S P z wybranymi ustawieniami zabezpieczeń.](./media/headerf5-tutorial/configure25.png)

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowej usługi SAML S P z wybranym kontekstem uwierzytelniania.](./media/headerf5-tutorial/configure26.png)

    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowej usługi SAML S P z wybranymi żądanymi atrybutami.](./media/headerf5-tutorial/configure27.png)

    ![Zrzut ekranu przedstawia okno dialogowe Edytowanie usługi SAML S P z wybranymi ustawieniami zaawansowanymi.](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Utwórz łącznik dostawcy tożsamości

1. Kliknij przycisk **Powiąż/Usuń powiązania dostawcy tożsamości łączników** , wybierz opcję **Utwórz nowy łącznik dostawcy tożsamości** i wybierz opcję z **metadanych** , a następnie wykonaj następujące czynności:
 
    ![Zrzut ekranu przedstawia edytowanie danych SAML I d PS, które korzystają z tego okna dialogowego, z wybranym łącznikiem Create New I d P.](./media/headerf5-tutorial/configure29.png)

    a. Przejdź do pliku metadata.xml pobranego z usługi Azure AD i podaj **nazwę dostawcy tożsamości**.

    b. Kliknij przycisk **OK**.

    c. Zostanie utworzony łącznik, a certyfikat jest automatycznie gotowy z pliku XML metadanych.
    
    ![Zrzut ekranu przedstawia okno dialogowe Tworzenie nowego łącznika SAML I d P.](./media/headerf5-tutorial/configure30.png)

    d. Skonfiguruj F5BIG-IP w celu wysłania żądania do usługi Azure AD.

    e. Kliknij przycisk **Dodaj nowy wiersz**, wybierz pozycję **AzureIDP** (zgodnie z poprzednimi krokami, określ 

    f. **Zgodne źródło =% {Session. Server. landinguri}** 

    przykład **Zgodna wartość =/**_

    h. Kliknij _*Update**

    i. Kliknij przycisk **OK** .

    j. **Ukończono instalację protokołu SAML dostawcy tożsamości**
    
    ![Zrzut ekranu przedstawia okno dialogowe Edycja danych SAML I d PS tego użytkownika.](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Konfigurowanie zasad F5 w celu przekierowywania użytkowników do usługi Azure SAML dostawcy tożsamości

1. Aby skonfigurować zasady F5 w celu przekierowywania użytkowników do usługi Azure SAML dostawcy tożsamości, wykonaj następujące czynności:

    a. Kliknij kolejno pozycje **główny > dostęp > profil/zasady > profile dostępu**.

    b. Kliknij przycisk **Utwórz** .

    ![Zrzut ekranu przedstawia stronę profile dostępu.](./media/headerf5-tutorial/configure32.png)
 
    c. Określ **nazwę** (HeaderAppAzureSAMLPolicy w przykładzie).

    d. Inne ustawienia można dostosować, zapoznaj się z dokumentacją F5.

    ![Zrzut ekranu przedstawia stronę właściwości ogólne.](./media/headerf5-tutorial/configure33.png)

    ![Zrzut ekranu przedstawia stronę właściwości ogólne, która jest kontynuowana.](./media/headerf5-tutorial/configure34.png) 

    e. Kliknij przycisk **Zakończono**.

    f. Po zakończeniu tworzenia zasad kliknij zasady i przejdź do karty **zasady dostępu** .

    ![Zrzut ekranu przedstawia kartę Zasady dostępu z ogólnymi właściwościami.](./media/headerf5-tutorial/configure35.png)
 
    przykład Kliknij **Edytor zasad wizualnych**, Edytuj **zasady dostępu dla** linku profilu.

    h. Kliknij znak + w edytorze zasad wizualnych i wybierz pozycję **uwierzytelnianie SAML**.

    ![Zrzut ekranu przedstawia zasady dostępu.](./media/headerf5-tutorial/configure36.png)

    ![Zrzut ekranu przedstawia okno dialogowe wyszukiwania z wybranym uwierzytelnianiem SAML.](./media/headerf5-tutorial/configure37.png)
 
    i. Kliknij pozycję **Dodaj element**.

    j. W obszarze **Właściwości** Określ **nazwę** i w obszarze **serwer AAA** wybierz wcześniej skonfigurowany SP, a następnie kliknij przycisk **Zapisz**.
 
    ![Zrzut ekranu przedstawia właściwości elementu, w tym jego serwer a.](./media/headerf5-tutorial/configure38.png)

    k. Zasady podstawowe są gotowe, można dostosować zasady w taki sposób, aby obejmowały dodatkowe źródła/magazyny atrybutów.

    ![Zrzut ekranu przedstawia niestandardowe zasady.](./media/headerf5-tutorial/configure39.png)
 
    l. Upewnij się, że w górnej części linku **Zastosuj zasady dostępu** .

### <a name="apply-access-profile-to-the-virtual-server"></a>Zastosuj Profil dostępu do serwera wirtualnego

1. Przypisz Profil dostępu do serwera wirtualnego, aby w usłudze F5 BIG-IP APM zastosować ustawienia profilu do ruchu przychodzącego i uruchomić wcześniej zdefiniowane zasady dostępu.

    a. Kliknij pozycję **główny**  >  **ruch lokalny lokalne**  >  **serwery wirtualne**.

    ![Zrzut ekranu przedstawia stronę listy serwery wirtualne.](./media/headerf5-tutorial/configure40.png)
 
    b. Kliknij pozycję serwer wirtualny, przewiń do sekcji **zasady dostępu** , na liście rozwijanej **Profil dostępu** i wybierz utworzone zasady SAML (w przykładzie HeaderAppAzureSAMLPolicy).

    c. Kliknij przycisk **Aktualizuj**
 
    ![Zrzut ekranu przedstawia okienko zasady dostępu.](./media/headerf5-tutorial/configure41.png)

    d. Utwórz F5 BIG-IP iRule®, aby wyodrębnić niestandardowe atrybuty SAML z potwierdzenia przychodzącego i przekazać je jako nagłówki HTTP do aplikacji testowej zaplecza. Kliknij pozycję **główny > ruchu lokalnego > iRules > IRule List > kliknij przycisk Utwórz** .

    ![Zrzut ekranu przedstawia listę iRule ruchu lokalnego.](./media/headerf5-tutorial/configure42.png)
 
    e. Wklej tekst F5 BIG-IP iRule poniżej do okna definicji.

    ![Zrzut ekranu przedstawia nową stronę iRule.](./media/headerf5-tutorial/configure43.png)
 
    gdy RULE_INIT {Set static::d ebug 0} podczas ACCESS_ACL_ALLOWED {

    Ustaw AZUREAD_USERNAME [dostęp:: dane sesji Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name "] Jeśli {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} Jeśli {! ( [HTTP:: Header istnieje "AZUREAD_USERNAME"]) } {HTTP:: Header INSERT "AZUREAD_USERNAME" $AZUREAD _USERNAME}

    Ustaw AZUREAD_DISPLAYNAME [dostęp:: dane sesji Get "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname "] Jeśli {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} Jeśli {! ( [HTTP:: Header istnieje "AZUREAD_DISPLAYNAME"]) } {HTTP:: Header INSERT "AZUREAD_DISPLAYNAME" $AZUREAD _DISPLAYNAME}

    Ustaw AZUREAD_EMAILADDRESS [dostęp:: dane sesji Get "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress "] Jeśli {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} Jeśli {! ( [HTTP:: Header istnieje "AZUREAD_EMAILADDRESS"]) } {HTTP:: Header INSERT "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Przykładowe dane wyjściowe poniżej**

    ![Zrzut ekranu przedstawia przykładowe dane wyjściowe.](./media/headerf5-tutorial/configure44.png)
 
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

- [Konfigurowanie logowania jednokrotnego dla aplikacji Kerberos](kerbf5-tutorial.md)

- [Konfigurowanie logowania jednokrotnego w przypadku zaawansowanej aplikacji Kerberos](advance-kerbf5-tutorial.md)