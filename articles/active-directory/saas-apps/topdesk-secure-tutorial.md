---
title: 'Samouczek: integracja Azure Active Directory z usługą TOPdesk-Secure | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją TOPdesk - Secure.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 5ed23889d8648c65ea0887d2f0f3406b50291f12
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654305"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Samouczek: integracja Azure Active Directory z usługą TOPdesk — zabezpieczenia

W tym samouczku dowiesz się, jak zintegrować usługę TOPdesk-Secure z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi TOPdesk-Secure z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TOPdesk-Secure.
* Zezwól użytkownikom na automatyczne logowanie do usługi TOPdesk-Secure (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:
 
 * Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne (SSO) TOPdesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja TOPdesk - Secure obsługuje logowanie jednokrotne inicjowane przez **dostawcę usługi**

## <a name="add-topdesk---secure-from-the-gallery"></a>Dodaj TOPdesk-Secure z galerii

Aby skonfigurować integrację aplikacji TOPdesk - Secure z usługą Azure AD, musisz dodać aplikację TOPdesk - Secure z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TOPdesk-Secure** w polu wyszukiwania.
1. Wybierz pozycję **TOPdesk — Zabezpiecz** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---secure"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla TOPdesk — bezpieczne

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD z aplikacją TOPdesk - Secure, korzystając z danych testowego użytkownika **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację połączenia między użytkownikiem usługi Azure AD i powiązanym użytkownikiem aplikacji TOPdesk - Secure.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TOPdesk-Secure, należy wykonać następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    2. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj TOPdesk — bezpieczne logowanie jednokrotne](#configure-topdesk---secure-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego aplikacji TOPdesk - Secure](#create-topdesk---secure-test-user)** — aby mieć w aplikacji TOPdesk - Secure odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD z aplikacją TOPdesk - Secure, wykonaj następujące kroki:

1. W Azure Portal na stronie **TOPdesk — bezpieczna** integracja aplikacji wybierz pozycję **Logowanie jednokrotne**.

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

3. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę ołówka, aby otworzyć okno dialogowe **podstawowe ustawienia SAML** .

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.topdesk.net`

    b. W polu **adres URL identyfikatora** wprowadź adres URL metadanych TOPdesk, który można pobrać z konfiguracji TOPdesk. Powinien on używać następującego wzorca: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta TOPdesk - Secure](https://www.topdesk.com/us/support/), aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Skonfiguruj aplikację TOPdesk - Secure** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do TOPdesk-Secure.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **TOPdesk-Secure**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-topdesk---secure-sso"></a>Konfigurowanie TOPdesk — bezpieczne logowanie jednokrotne

1. Zaloguj się do firmowej witryny aplikacji **TOPdesk - Secure** jako administrator.

2. W menu **TOPdesk** kliknij pozycję **Settings** (Ustawienia).

    ![Ustawienia](./media/topdesk-secure-tutorial/ic790598.png "Ustawienia")

3. Kliknij pozycję **Login Settings** (Ustawienia logowania).

    ![Ustawienia logowania](./media/topdesk-secure-tutorial/ic790599.png "Ustawienia logowania")

4. Rozwiń menu **Login Settings** (Ustawienia logowania), a następnie kliknij pozycję **General** (Ogólne).

    ![Ogólne](./media/topdesk-secure-tutorial/ic790600.png "Ogólne")

5. W części **Secure** (Bezpieczne) sekcji konfiguracji **SAML login** (Logowanie SAML) wykonaj następujące kroki:

    ![Ustawienia techniczne](./media/topdesk-secure-tutorial/ic790855.png "Ustawienia techniczne")

    a. Kliknij pozycję **Download** (Pobierz), aby pobrać publiczny plik metadanych, a następnie zapisz go lokalnie na komputerze.

    b. Otwórz plik metadanych i znajdź węzeł **AssertionConsumerService**.

    ![Usługa konsumencka potwierdzenia](./media/topdesk-secure-tutorial/ic790856.png "Usługa konsumencka potwierdzenia")

    c. Skopiuj wartość **AssertionConsumerService** i wklej ją w polu tekstowym adresu URL odpowiedzi w sekcji **TOPdesk - Secure Domain and URLs** (Domena i adresy URL aplikacji TOPdesk - Secure).

6. Aby utworzyć plik certyfikatu, wykonaj następujące kroki:

    ![Certyfikat](./media/topdesk-secure-tutorial/ic790606.png "Certyfikat")

    a. Otwórz plik metadanych pobrany w witrynie Azure Portal.

    b. Rozwiń węzeł **RoleDescriptor**, w którym element **xsi:type** ma wartość **fed:ApplicationServiceType**.

    c. Skopiuj wartość węzła **X509Certificate**.

    d. Zapisz skopiowaną wartość **X509Certificate** lokalnie na komputerze w pliku.

7. W sekcji **Public** (Publiczne) kliknij przycisk **Add** (Dodaj).

    ![Dodaj](./media/topdesk-secure-tutorial/ic790607.png "Dodaj")

8. W oknie dialogowym **SAML configuration assistant** (Asystent konfiguracji SAML) wykonaj następujące kroki:

    ![Asystent konfiguracji SAML](./media/topdesk-secure-tutorial/ic790608.png "Asystent konfiguracji SAML")

    a. Aby przekazać plik metadanych pobrany w witrynie Azure Portal, w obszarze **Federation Metadata** (Metadane federacji) kliknij przycisk **Browse** (Przeglądaj).

    b. Aby przekazać plik certyfikatu, w obszarze **Certificate (RSA)** (Certyfikat — RSA) kliknij przycisk **Browse** (Przeglądaj).

    c. W obszarze **Private key (RSA, PKCS8, DER)** (Klucz prywatny — RSA, PKCS8, DER) możesz przekazać własny klucz prywatny lub klucz uzyskany od [zespołu pomocy technicznej klienta TOPdesk - Secure](https://www.topdesk.com/us/support).

    d. Aby przekazać plik logo uzyskany od zespołu pomocy technicznej TOPdesk, w obszarze **Logo icon** (Ikona logo) kliknij przycisk **Browse** (Przeglądaj).

    e. W polu tekstowym **User name attribute** (Atrybut nazwy użytkownika) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. W polu tekstowym **Display name** (Nazwa wyświetlana) wpisz nazwę konfiguracji.

    przykład Kliknij pozycję **Zapisz**.

### <a name="create-topdesk---secure-test-user"></a>Tworzenie użytkownika testowego aplikacji TOPdesk - Secure

Aby umożliwić użytkownikom usługi Azure AD logowanie się do aplikacji TOPdesk - Secure, należy ich aprowizować w aplikacji TOPdesk - Secure.  
W przypadku aplikacji TOPdesk - Secure jest to zadanie ręczne.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Aby skonfigurować aprowizację użytkowników, wykonaj następujące kroki:

1. Zaloguj się do firmowej witryny aplikacji **TOPdesk - Secure** jako administrator.

2. W menu u góry kliknij kolejno pozycje **TOPdesk \> New \> Support Files \> Operator** (TOPdesk > Nowy > Pliki pomocnicze > Operator).

    ![Operator](./media/topdesk-secure-tutorial/ic790610.png "Operator")

3. W oknie dialogowym **New Operator** (Nowy operator) wykonaj następujące kroki:

    ![Operator new](./media/topdesk-secure-tutorial/ic790611.png "Nowy operator")

    a. Kliknij kartę **General** (Ogólne).

    b. W polu tekstowym **Surname** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W sekcji **Location** (Lokalizacja) w polu **Site** (Lokacja) wybierz lokację dla konta.

    d. W sekcji **TOPdesk Login** (Logowanie w aplikacji TOPdesk) w polu tekstowym **Login Name** (Nazwa logowania) wpisz nazwę logowania użytkownika.

    e. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Za pomocą innych narzędzi do tworzenia kont użytkowników TOPdesk i interfejsów API zapewnianych przez usługę TOPdesk — zabezpieczenia można udostępniać kontom użytkowników usługi Azure AD.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania za pomocą TOPdesk, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do TOPdesk — adres URL logowania bezpiecznego i zainicjuj tam przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka TOPdesk-Secure w obszarze Moje aplikacje należy automatycznie zalogować się do TOPdesk-Secure, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu TOPdesk zabezpieczenia można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).