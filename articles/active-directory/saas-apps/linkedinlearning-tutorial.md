---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z uczeniem serwisu LinkedIn | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją LinkedIn Learning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 33ac21eb87b57487277e598e899061e8757e82fc
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98050412"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu uczenia LinkedIn

W tym samouczku dowiesz się, jak zintegrować uczenie serwisu LinkedIn z usługą Azure Active Directory (Azure AD). Gdy integrujesz uczenie serwisu LinkedIn z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do uczenia serwisu LinkedIn.
* Zezwól użytkownikom na automatyczne logowanie do usługi LinkedIn przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja obsługująca Logowanie jednokrotne w serwisie LinkedIn.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja LinkedIn Learning obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**
* Aplikacja LinkedIn Learning obsługuje aprowizowanie użytkowników typu **Just In Time**


## <a name="adding-linkedin-learning-from-the-gallery"></a>Dodawanie aplikacji LinkedIn Learning z galerii

Aby skonfigurować integrację aplikacji LinkedIn Learning z usługą Azure AD, należy dodać aplikację LinkedIn Learning z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **uczenie LinkedIn** .
1. Wybierz pozycję **LinkedIn nauka** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD na potrzeby uczenia LinkedIn

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą uczenia LinkedIn przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze LinkedIn.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą uczenia LinkedIn, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne do serwisu LinkedIn](#configure-linkedin-learning-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego uczenia LinkedIn](#create-linkedin-learning-test-user)** , aby uzyskać odpowiednika B. Simon w serwisie LinkedIn, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji do **uczenia LinkedIn** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

     a. W polu tekstowym **Identyfikator** wprowadź **Identyfikator jednostki** skopiowany z portalu LinkedIn. 

    b. W polu tekstowym **Adres URL odpowiedzi** wprowadź **Adres URL usługi Assertion Consumer Service (ACS)** skopiowany z portalu LinkedIn.

    c. Jeśli chcesz skonfigurować aplikację w trybie **inicjowanym przez dostawcę tożsamości**, kliknij opcję **Ustaw dodatkowe adresy URL** w sekcji **Podstawowa konfiguracja protokołu SAML**, w której można będzie określić adres URL logowania jednokrotnego. Aby utworzyć adres URL logowania, skopiuj **Adres URL usługi Assertion Consumer Service (ACS)**, po czym zamień ciąg /saml/ na /login/. Adres URL logowanie przybierze wówczas następującą postać:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informacje o domenie i adresach URL logowania jednokrotnego aplikacji LinkedIn](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Wartości te nie są wartościami rzeczywistymi. Te wartości zostaną zaktualizowane za pomocą rzeczywistego identyfikatora i adresu URL odpowiedzi, co zostało wyjaśnione w dalszej części tematu **Konfigurowanie logowania jednokrotnego w usłudze LinkedIn Learning** w samouczku.

1. Aplikacja do uczenia LinkedIn oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja LinkedIn Learning oczekuje, że atrybut **nameidentifier** będzie zamapowany na atrybut **user.mail**, dlatego należy zmodyfikować mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutów.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie uczenia serwisu LinkedIn** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do uczenia serwisu LinkedIn.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **LinkedIn nauka**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-linkedin-learning-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze LinkedIn

1. W oddzielnym oknie przeglądarki internetowej zaloguj się do dzierżawy aplikacji LinkedIn Learning jako administrator.

2. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Wybierz również opcję **Learning - Default** (Learning — domyślna) z listy rozwijanej.

    ![Zrzut ekranu przedstawia ustawienia globalne, w których można wybrać opcję domyślny.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Kliknij przycisk **LUB kliknij tutaj, aby załadować i skopiować poszczególne pola formularza**, a także skopiuj **identyfikator jednostki** oraz **adres URL usługi Assertion Consumer Service (ACS)** i wklej go w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, w których można wprowadzić podane wartości.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż plik XML pobrany z witryny Azure Portal, klikając opcję **Przekaż plik XML**.

    ![Zrzut ekranu przedstawia Konfigurowanie ustawień dostawcy usług LinkedIn, w których można przekazać plik X M L.](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan funkcji logowania jednokrotnego zmieni się z **Not Connected** (Niepołączona) na **Connected** (Połączona)

    ![Zrzut ekranu przedstawia pojedyncze Sign-On, na których można włączyć uwierzytelnianie użytkowników za pomocą S S O.](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Tworzenie użytkownika testowego aplikacji LinkedIn Learning

Aplikacja LinkedIn Learning obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy są automatycznie tworzeni w aplikacji. Na stronie ustawień administracyjnych w portalu aplikacji LinkedIn Learning portal przerzuć przełącznik **Automatically Assign licenses** (Automatycznie przypisuj licencje), aby aktywować aprowizowanie typu Just In Time. Spowoduje to również przypisanie licencji użytkownikowi.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL uczenia się w serwisie LinkedIn, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do serwisu LinkedIn adres URL logowania, a następnie zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i musisz automatycznie zalogować się do usługi LinkedIn, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka uczenie LinkedIn w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do usługi LinkedIn, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu uczenia serwisu LinkedIn można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).
