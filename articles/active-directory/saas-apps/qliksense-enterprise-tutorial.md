---
title: 'Samouczek: integracja Azure Active Directory z Qlik wykrywania przedsiębiorstwa | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Qlik Sense Enterprise.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2020
ms.author: jeedes
ms.openlocfilehash: 4995f813bbfe7ef368f83085cdd61ab97fa330e8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108528"
---
# <a name="tutorial-integrate-qlik-sense-enterprise-with-azure-active-directory"></a>Samouczek: Integrowanie Qlik wykrywania przedsiębiorstwa z Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Qlik sens przedsiębiorstwa z Azure Active Directory (Azure AD). Po zintegrowaniu usługi Qlik Sense Enterprise z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Qlik wykrywania przedsiębiorstwa.
* Zezwól użytkownikom na automatyczne logowanie do Qlik wykrywania przedsiębiorstwa przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w przedsiębiorstwie Qlik.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 
* Qlik sens przedsiębiorstwa obsługuje logowanie jednokrotne w usłudze **SP** .
* Qlik wykrywa, że firma obsługuje **obsługę just-in-Time**

* Po skonfigurowaniu Qlik wykrywania przedsiębiorstwa można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-qlik-sense-enterprise-from-the-gallery"></a>Dodawanie usługi Qlik Sense Enterprise z galerii

Aby skonfigurować integrację usługi Qlik Sense Enterprise z usługą Azure AD, należy dodać usługę Qlik Sense Enterprise z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Qlik sens Enterprise** w polu wyszukiwania.
1. Wybierz pozycję **Qlik wykrywanie przedsiębiorstwa** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Qlik wykrywania przedsiębiorstwa przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Qlik sens przedsiębiorstwa.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Qlik wykrywania przedsiębiorstwa, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Qlik wykrywanie logowania jednokrotnego w przedsiębiorstwie](#configure-qlik-sense-enterprise-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    * **[Tworzenie użytkownika testowego usługi Qlik Sense Enterprise](#create-qlik-sense-enterprise-test-user)** — aby udostępnić w usłudze Qlik Sense Enterprise odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Qlik wykrywanie przedsiębiorstwa** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/hub`

    b. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:

    ```http
    https://<Fully Qualified Domain Name>.qlikpoc.com
    https://<Fully Qualified Domain Name>.qliksense.com
    ```

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 

    `https://<Fully Qualified Domain Name>:443{/virtualproxyprefix}/samlauthn/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami adresu URL logowania jednokrotnego, identyfikatora i adresu URL odpowiedzi (są one objaśnione w dalszej części tego samouczka) lub skontaktuj się z [zespołem pomocy technicznej klienta usługi Qlik Sense Enterprise](https://www.qlik.com/us/services/support), aby uzyskać te wartości. Domyślnym portem dla adresów URL jest 443, ale można go dostosować do potrzeb organizacji.

1. Na stronie **Konfigurowanie jednego Sign-On ze źródłem SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** z określonych opcji zgodnie z wymaganiami i Zapisz go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie Britta Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę Britta Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do przedsiębiorstwa z systemem Qlik.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Qlik wykrywanie przedsiębiorstwa**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-qlik-sense-enterprise-sso"></a>Konfigurowanie Qlik wykrywania rejestracji jednokrotnej przedsiębiorstwa

1. Przygotuj plik XML metadanych federacji, który można przekazać na serwer Qlik Sense.

    > [!NOTE]
    > Przed przekazaniem metadanych dostawcy tożsamości na serwer Qlik Sense należy zmodyfikować plik, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.

    ![Zrzut ekranu przedstawia okno Visual Studio Code z plikiem metadanych Federacji X M L.][qs24]

    a. Otwórz plik FederationMetaData.xml pobrany z witryny Azure Portal w edytorze tekstów.

    b. Wyszukaj wartość **RoleDescriptor**.  Istnieją cztery wpisy (dwie pary otwierających i zamykających tagów elementu).

    c. Usuń z pliku tagi RoleDescriptor i wszystkie informacje między nimi.

    d. Zapisz plik tak, aby był dostępny do użycia w dalszej części tego dokumentu.

2. Przejdź do konsoli Qlik Management Console (QMC) usługi Qlik Sense jako użytkownik, który może tworzyć konfiguracje wirtualnego serwera proxy.

3. W konsoli QMC kliknij pozycję menu **Virtual Proxies** (Wirtualne serwery proxy).

    ![Zrzut ekranu przedstawia wirtualne serwery proxy wybrane z KONFIGUROWANIA systemu.][qs6]

4. W dolnej części ekranu kliknij przycisk **Create new** (Utwórz nowy).

    ![Zrzut ekranu przedstawia opcję Utwórz nowy.][qs7]

5. Zostanie wyświetlony ekran edycji wirtualnych serwerów proxy.  Po prawej stronie ekranu jest dostępne menu umożliwiające włączenie widoczności opcji konfiguracji.

    ![Zrzut ekranu pokazuje, że identyfikacja została wybrana na podstawie właściwości.][qs9]

6. Przy zaznaczonej opcji menu Identification (Identyfikacja) podaj informacje identyfikujące konfigurację wirtualnego serwera proxy platformy Azure.

    ![Zrzut ekranu przedstawia sekcję Edytuj identyfikację wirtualnego serwera proxy, w której można wprowadzić podane wartości.][qs8]  

    a. Pole **Description** (Opis) to przyjazna nazwa dla konfiguracji wirtualnego serwera proxy.  Podaj wartość zawierającą opis.

    b. Pole **Prefix** (Prefiks) określa punkt końcowy wirtualnego serwera proxy umożliwiający funkcji logowania jednokrotnego usługi Azure AD łączenie się z usługą Qlik Sense.  Podaj unikatowy prefiks dla tego wirtualnego serwera proxy.

    c. Pole **Session inactivity timeout (minutes)** (Limit czasu bezczynności sesji w minutach) to limit czasu dla połączeń przechodzących przez wirtualny serwer proxy.

    d. Pole **Session cookie header name** (Nazwa nagłówka pliku cookie sesji) przechowuje identyfikator sesji usługi Qlik Sense, który użytkownik otrzymuje po pomyślnym uwierzytelnieniu.  Ta nazwa musi być unikatowa.

7. Kliknij opcję menu Authentication (Uwierzytelnianie), aby stała się widoczna.  Zostanie wyświetlony ekran Authentication (Uwierzytelnianie).

    ![Zrzut ekranu przedstawia sekcję Edycja uwierzytelniania wirtualnego serwera proxy, w której można wprowadzić podane wartości.][qs10]

    a. Lista rozwijana **Anonymous access mode** (Tryb dostępu anonimowego) określa, czy użytkownicy anonimowi mają dostęp do usługi Qlik Sense za pośrednictwem wirtualnego serwera proxy.  Opcja domyślna to No anonymous user (Bez użytkowników anonimowych).

    b. Lista rozwijana **Authentication method** (Metoda uwierzytelniania) określa schemat uwierzytelniania używany przez wirtualny serwer proxy.  Wybierz pozycję SAML z listy rozwijanej.  Zostaną wtedy wyświetlone kolejne opcje.

    c. W polu **SAML host URI** (Identyfikator URI hosta SAML) podaj nazwę hosta używaną przez użytkowników do uzyskiwania dostępu do usługi Qlik Sense za pomocą tego wirtualnego serwera proxy protokołu SAML.  Nazwa hosta to identyfikator URI serwera Qlik Sense.

    d. W polu **SAML entity ID** (Identyfikator jednostki SAML) podaj tę samą wartość co w polu SAML host URI (Identyfikator URI hosta SAML).

    e. Pole **SAML IdP metadata** (Metadane dostawcy tożsamości SAML) określa plik edytowany wcześniej w sekcji **Edytowanie metadanych federacji z konfiguracji usługi Azure AD**.  **Przed przekazaniem metadanych dostawcy tożsamości plik należy zmodyfikować**, aby usunąć informacje w celu zapewnienia odpowiedniego współdziałania usługi Azure AD i serwera Qlik Sense.  **Zapoznaj się z instrukcjami powyżej, jeśli plik nie został jeszcze zmodyfikowany.**  Jeśli plik został już zmodyfikowany, kliknij przycisk Browse (Przeglądaj) i wybierz edytowany plik metadanych, aby przekazać go do konfiguracji wirtualnego serwera proxy.

    f. Podaj nazwę atrybutu lub odwołanie do schematu dla atrybutu SAML reprezentującego wartość **UserID** wysyłaną przez usługę Azure AD do serwera Qlik Sense.  Informacje o odwołaniu do schematu są dostępne na ekranach aplikacji platformy Azure wyświetlanych po konfiguracji.  Aby użyć nazwy atrybutu, podaj ciąg `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    przykład Podaj wartość dla **katalogu użytkownika**, który będzie dołączany do użytkowników po uwierzytelnieniu względem serwera Qlik Sense za pomocą usługi Azure AD.  Zapisane na stałe wartości muszą być ujęte w **nawiasy kwadratowe — []**.  Aby użyć atrybutu wysyłanego w asercji SAML usługi Azure AD, wpisz nazwę atrybutu w tym polu tekstowym **bez** nawiasów kwadratowych.

    h. Pole **SAML signing algorithm** (Algorytm podpisywania SAML) umożliwia ustawienie podpisywania certyfikatu dostawcy usług (w tym przypadku serwera Qlik Sense) dla konfiguracji wirtualnego serwera proxy.  Jeśli serwer Qlik Sense używa certyfikatu zaufanego wygenerowanego za pomocą dostawcy Microsoft Enhanced RSA and AES Cryptographic Provider, zmień algorytm podpisywania SAML na **SHA-256**.

    i. Sekcja mapowania atrybutów SAML umożliwia wysyłanie dodatkowych atrybutów, takich jak grupy, do usługi Qlik Sense na potrzeby użycia w regułach zabezpieczeń.

8. Kliknij opcję menu **LOAD BALANCING** (RÓWNOWAŻENIE OBCIĄŻENIA), aby stała się widoczna.  Zostanie wyświetlony ekran Load Balancing (Równoważenie obciążenia).

    ![Zrzut ekranu przedstawia ekran edytowania wirtualnego serwera proxy na potrzeby równoważenia obciążenia, w którym można wybrać opcję Dodaj nowy węzeł serwera.][qs11]

9. Kliknij przycisk **Add new server node** (Dodaj nowy węzeł serwera), wybierz węzeł lub węzły aparatu, do których usługa Qlik Sense będzie wysyłać sesje na potrzeby równoważenia obciążenia, a następnie kliknij przycisk **Add** (Dodaj).

    ![Zrzut ekranu przedstawia przycisk okna dialogowego Dodawanie węzłów serwera do równoważenia obciążenia, który umożliwia dodawanie serwerów.][qs12]

10. Kliknij opcję menu Advanced (Zaawansowane), aby stała się widoczna. Zostanie wyświetlony ekran Advanced (Zaawansowane).

    ![Zrzut ekranu przedstawia ekran Zaawansowane edytowanie wirtualnego serwera proxy.][qs13]

    Lista dozwolonych hostów identyfikuje nazwy hostów, które są akceptowane podczas nawiązywania połączenia z serwerem wykrywania Qlik.  **Podaj nazwę hosta, którą użytkownicy będą określać podczas nawiązywania połączenia z serwerem Qlik Sense.** Nazwa hosta jest taka sama jak identyfikator URI hosta SAML, jednak bez prefiksu https://.

11. Następnie kliknij przycisk **Apply** (Zastosuj).

    ![Zrzut ekranu przedstawia przycisk Zastosuj.][qs14]

12. Kliknij przycisk OK, aby zaakceptować komunikat ostrzegawczy informujący o tym, że serwery proxy połączone z wirtualnym serwerem proxy zostaną uruchomione ponownie.

    ![Zrzut ekranu przedstawia komunikat potwierdzający zastosowanie zmian do wirtualnego serwera proxy.][qs15]

13. Po prawej stronie ekranu zostanie wyświetlone menu Associated items (Elementy skojarzone).  Kliknij opcję menu **Proxies** (Serwery proxy).

    ![Zrzut ekranu przedstawia proxy wybrane z elementów skojarzonych.][qs16]

14. Zostanie wyświetlony ekran serwera proxy.  Kliknij przycisk **Link** (Połącz) w dolnej części ekranu, aby połączyć serwer proxy z wirtualnym serwerem proxy.

    ![Zrzut ekranu przedstawia przycisk link.][qs17]

15. Wybierz węzeł serwera proxy, który będzie obsługiwać połączenie tego wirtualnego serwera proxy, a następnie kliknij przycisk **Link** (Połącz).  Po połączeniu serwer proxy zostanie wyświetlony w obszarze skojarzonych serwerów proxy.

    ![Zrzut ekranu przedstawia opcję Wybierz usługi proxy.][qs18]
  
    ![Zrzut ekranu przedstawia skojarzone serwery proxy w oknie dialogowym skojarzone elementy wirtualnego serwera proxy.][qs19]

16. Po około 5–10 sekundach zostanie wyświetlony komunikat odświeżenia konsoli QMC.  Kliknij przycisk **Refresh QMC** (Odśwież konsolę QMC).

    ![Zrzut ekranu przedstawia komunikat, że sesja została zakończona.][qs20]

17. Po odświeżeniu konsoli QMC kliknij pozycję menu **Virtual proxies** (Wirtualne serwery proxy). Pozycja nowego wirtualnego serwera proxy SAML zostanie wyświetlona w tabeli na ekranie.  Kliknij jednokrotnie pozycję wirtualnego serwera proxy.

    ![Zrzut ekranu przedstawia wirtualne serwery proxy z pojedynczym wpisem.][qs51]

18. W dolnej części ekranu zostanie aktywowany przycisk Download SP metadata (Pobierz metadane dostawcy usług).  Kliknij przycisk **Download SP metadata** (Pobierz metadane dostawcy usług), aby zapisać metadane do pliku.

    ![Zrzut ekranu przedstawia przycisk Pobierz metadane b.][qs52]

19. Otwórz plik metadanych dostawcy usług.  Zwróć uwagę na pozycje **entityID** i **AssertionConsumerService**.  Te wartości są równoważne polom **Identyfikator**, **Adres URL logowania** i **Adres URL odpowiedzi** w konfiguracji aplikacji usługi Azure AD. Wklej te wartości w sekcji **Domena i adresy URL usługi Qlik Sense Enterprise** w konfiguracji aplikacji usługi Azure AD, jeśli nie są zgodne, a następnie zastąp je w kreatorze konfiguracji aplikacji usługi Azure AD.

    ![Zrzut ekranu przedstawia Edytor zwykłego tekstu z elementem EntityDescriptor z entityID i AssertionConsumerService wywołana.][qs53]

### <a name="create-qlik-sense-enterprise-test-user"></a>Tworzenie użytkownika testowego usługi Qlik Sense Enterprise

Qlik Sense "Enterprise" obsługuje **Inicjowanie obsługi just-in-Time**, użytkownicy są automatycznie dodawani do REPOZYTORIUM "Users" w przedsiębiorstwie Qlike, ponieważ korzystają z funkcji logowania jednokrotnego. Ponadto klienci mogą korzystać z QMC i tworzyć UDC (łącznika katalogu użytkownika) w celu wstępnego wypełniania użytkowników w programie Active Directory Qlik

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

Po wybraniu kafelka Qlik na poziomie przedsiębiorstwa w panelu dostępu należy automatycznie zalogować się do przedsiębiorstwa Qlik wykrywania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[qs6]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_06.png
[qs7]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_07.png
[qs8]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_08.png
[qs9]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_09.png
[qs10]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_10.png
[qs11]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_11.png
[qs12]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_12.png
[qs13]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_13.png
[qs14]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_14.png
[qs15]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_15.png
[qs16]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_16.png
[qs17]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_17.png
[qs18]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_18.png
[qs19]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_19.png
[qs20]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_20.png
[qs24]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_24.png
[qs51]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_51.png
[qs52]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_52.png
[qs53]: ./media/qliksense-enterprise-tutorial/tutorial_qliksenseenterprise_53.png
