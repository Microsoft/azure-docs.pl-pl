---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą SuccessFactors | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem SuccessFactors.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/26/2020
ms.author: jeedes
ms.openlocfilehash: a903eb6000cdd5212ad358cae4952e27a4719070
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725264"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą SuccessFactors

W tym samouczku dowiesz się, jak zintegrować usługę SuccessFactors z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi SuccessFactors z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SuccessFactors.
* Zezwól użytkownikom na automatyczne logowanie się do usługi SuccessFactors przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SuccessFactors.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa SuccessFactors obsługuje usługę **SP** zainicjowaną przez usługę SSO.

## <a name="adding-successfactors-from-the-gallery"></a>Dodawanie rozwiązania SuccessFactors z galerii

Aby skonfigurować integrację rozwiązania SuccessFactors z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SuccessFactors** w polu wyszukiwania.
1. Wybierz pozycję **SuccessFactors** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SuccessFactors

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SuccessFactors przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SuccessFactors.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SuccessFactors, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne](#configure-successfactors-sso)** w usłudze SuccessFactors, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SuccessFactors](#create-successfactors-test-user)** , aby dysponować odpowiednikiem B. Simon w SuccessFactors, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SuccessFactors** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **adres URL logowania** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. W polu tekstowym **adres URL odpowiedzi** wpisz adres URL przy użyciu jednego z następujących wzorców:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej klienta rozwiązania SuccessFactors](https://www.sap.com/support.html), aby uzyskać te wartości.

4. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie SuccessFactors** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi SuccessFactors.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **SuccessFactors**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-successfactors-sso"></a>Konfigurowanie logowania jednokrotnego SuccessFactors

1. W innym oknie przeglądarki internetowej zaloguj się do **portalu administracyjnego rozwiązania SuccessFactors** jako administrator.

2. Przejdź na stronę **Application Security** (Zabezpieczenia aplikacji) zawierającą pole **Single Sign On Features** (Funkcje logowania jednokrotnego).

3. Umieść dowolną wartość w polu **Reset Token** (Token resetowania), a następnie kliknij przycisk **Save Token** (Zapisz token), aby włączyć logowanie jednokrotne protokołu SAML.

    ![Zrzut ekranu przedstawia kartę Zabezpieczenia aplikacji z funkcją logowania jednokrotnego o nazwie, w której można wprowadzić token.][11]

    > [!NOTE]
    > Ta wartość jest używana jako przełącznik włączania/wyłączania. Jeśli zostanie zapisana jakakolwiek wartość, logowanie jednokrotne protokołu SAML będzie włączone. Jeśli zostanie zapisana pusta wartość, logowanie jednokrotnego protokołu SAML będzie wyłączone.

4. Zapoznaj się z poniższym zrzutem ekranu i wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okienko dla języka SAML, w którym można wprowadzać podane wartości.][12]
  
    a. Zaznacz przycisk radiowy **SAML v2 SSO** (Logowanie jednokrotne protokołu SAML w wersji 2)
  
    b. Ustaw wartość pola **SAML Asserting Party Name** (Nazwa jednostki potwierdzającej protokołu SAML) na przykład na wystawcę protokołu SAML i nazwę firmy.

    c. W polu tekstowym **Issuer URL** (Adres URL wystawcy) wklej wartość pola **Identyfikatora usługi Azure AD** skopiowaną z witryny Azure Portal.

    d. W polu **Require Mandatory Signature** (Wymagaj obowiązkowego podpisu) wybierz wartość **Assertion** (Asercja).

    e. W polu **Enable SAML Flag** wybierz wartość **Enabled** (Włączone).

    f. W polu **Login Request Signature(SF Generated/SP/RP)** (Sygnatura żądania logowania: wygenerowane w rozwiązaniu SF/Dostawca usług/Dostawca zasobów) wybierz wartość **Nie**.

    przykład W polu **SAML Profile** (Profil protokołu SAML) wybierz wartość **Browser/Post Profile** (Przeglądarka/Opublikuj profil).

    h. W polu **Enforce Certificate Valid Period** (Wymuś prawidłowy okres certyfikatu) wybierz wartość **No** (Nie).

    i. Skopiuj zawartość pliku certyfikatu pobranego z witryny Azure Portal, a następnie wklej go do pola tekstowego **SAML Verifying Certificate** (Certyfikatu weryfikacji protokołu SAML).

    > [!NOTE] 
    > Zawartość certyfikatu musi rozpoczynać się od tagów początku i końca certyfikatu.

5. Przejdź do strony protokołu SAML w wersji 2, a następnie wykonaj następujące kroki:

    ![Zrzut ekranu przedstawia okienko wylogowywania zainicjowane przez protokół SAML V2 S, w którym można wprowadzić podane wartości.][13]

    a. W polu **Support SP-initiated Global Logout** (Obsługuj globalne wylogowywanie inicjowane przez dostawcę usług) wybierz pozycję **Yes** (Tak).

    b. W polu tekstowym **Global Logout Service URL (LogoutRequest destination)** (Adres URL usługi globalnego wylogowywania: miejsce docelowe elementu LogoutRequest) wklej wartość pola **Adres URL wylogowywania** skopiowaną z witryny Azure Portal.

    c. W polu **Require sp must encrypt all NameID element** (Wymagaj szyfrowania wszystkich elementów NameID przez dostawcę usług) wybierz wartość **No** (Nie).

    d. W polu **NameID Format** (Format identyfikatora NameID) wybierz pozycję **unspecified** (nieokreślony).

    e. W polu **Enable sp initiated login (AuthnRequest)** (Włącz logowanie inicjowane przez dostawcę usług: AuthnRequest) wybierz pozycję **Yes** (Tak).

    f. W polu tekstowym **Send request as Company-Wide issuer** (Wyślij żądanie jako wystawca firmowy) wklej wartość pola **Adres URL logowania** skopiowaną z witryny Azure Portal.

6. Wykonaj następujące kroki, aby w nazwach logowania użytkowników nie była uwzględniania wielkość liter.

    ![Konfigurowanie logowania jednokrotnego][29]

    a. Przejdź na stronę **Company Settings** (Ustawienia firmowe) (w dolnej części).

    b. Zaznacz pole wyboru obok pozycji **Enable Non-Case-Sensitive Username** (Zezwól na nazwę użytkownika bez uwzględnienia wielkości liter).

    c. Kliknij pozycję **Zapisz**.

    > [!NOTE]
    > Jeśli spróbujesz włączyć tę funkcję, system sprawdzi, czy utworzona nie zostanie zduplikowana nazwa logowania protokołu SAML. Może to mieć miejsce, jeśli klient ma użytkowników o nazwach Użytkownik1 i użytkownik1. Wyłączenie uwzględniania wielkości liter spowoduje występowanie takich duplikatów. W systemie zostanie wyświetlony komunikat i ta funkcja nie zostanie włączona. Klient musi zmienić jedną z nazw użytkowników na inną.

### <a name="create-successfactors-test-user"></a>Tworzenie użytkownika testowego rozwiązania SuccessFactors

Aby umożliwić użytkownikom usługi Azure AD logowanie się w usłudze SuccessFactors, muszą one być obsługiwane w usłudze SuccessFactors. W przypadku rozwiązania SuccessFactors aprowizacja to zadanie ręczne.

Aby utworzyć użytkownik w rozwiązaniu SuccessFactors, skontaktuj się z [zespołem pomocy technicznej tego rozwiązania](https://www.sap.com/support.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania SuccessFactors, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania SuccessFactors i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka SuccessFactors w obszarze Moje aplikacje zostanie on przekierowany do adresu URL logowania SuccessFactors. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SuccessFactors można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png