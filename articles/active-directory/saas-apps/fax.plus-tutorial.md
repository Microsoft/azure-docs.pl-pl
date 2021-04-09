---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z FAKSem. ZNAK PLUS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i FAKSem. Dłużon.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 528e1056574379f922b5de15f442b7fd92d8cf8c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592459"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-faxplus"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z FAKSem. DŁUŻON

W tym samouczku dowiesz się, jak zintegrować faks. PLUS z Azure Active Directory (Azure AD). Po zintegrowaniu programu Faks. Ponadto dzięki usłudze Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do faksu. Dłużon.
* Zezwól użytkownikom na automatyczne logowanie do faksu. PLUS z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Fax. I subskrypcja obsługująca Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Fax. Obsługuje także usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO.
* Fax. Dodatkowo obsługuje Inicjowanie obsługi użytkowników **just in Time** .

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-faxplus-from-the-gallery"></a>Dodaj faks. PLUS z galerii

Aby skonfigurować integrację faksu. Oprócz usługi Azure AD należy dodać faks. A także z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Fax. PLUS** w polu wyszukiwania.
1. Wybierz pozycję **faks. Następnie w panelu** wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-faxplus"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby faksu. DŁUŻON

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą faksu. Dodatkowo przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w FAKSie. Dłużon.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą faksu. Dodatkowo wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj faks. I logowanie jednokrotne](#configure-faxplus-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz faks. Ponadto Użytkownik testowy](#create-faxplus-test-user)** — ma odpowiedni odpowiednik B. Simon w faksie. Dodatkowo połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na **faksie.** Na stronie integracja aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://www.fax.plus/login`

1. Kliknij pozycję **Zapisz**.

1. Fax. Aplikacja i oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, faks. Aplikacja i oczekuje, że kilka więcej atrybutów zostanie przekazana z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa | Atrybut źródłowy|
    | --------------- | --------- |
    | firstname  | user.givenname |
    | lastname   | user.surname   |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Na stronie **Konfigurowanie faksu. Sekcja PLUS** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user&quot;></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name=&quot;assign-the-azure-ad-test-user&quot;></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do faksu. Dłużon.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **faks. PLUS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola &quot;domyślny dostęp&quot;.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name=&quot;configure-faxplus-sso&quot;></a>Skonfiguruj faks. LOGOWANIE JEDNOKROTNE

1. Zaloguj się do faksu. Dodatkowo lokacja firmy jako administrator.

2. Przejdź do sekcji **zabezpieczenia** w profilu administratora i przewiń w dół do pozycji **Zaawansowane**.

3. W panelu **Konfiguracja** kliknij przycisk **Aktywuj logowanie** jednokrotne, a następnie wykonaj poniższe kroki.
    
    ![Koncie](./media/fax.plus-tutorial/configuration.png &quot;Konto") 

    a. W polu tekstowym **Identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    b. W polu tekstowym **adres URL pojedynczego Sign-On** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu X. 509** .

    d. Jeśli chcesz zalogować się za pomocą logowania jednokrotnego, zaznacz pole wyboru **Zezwalaj tylko na logowanie JEDNOkrotne dla użytkownika administracyjnego** . 

    e. Kliknij pozycję **Potwierdź**.

### <a name="create-faxplus-test-user"></a>Utwórz faks. PLUS Użytkownik testowy

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w FAKSie. Dłużon. Fax. Dodatkowo obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w FAKSie. Dodatkowo po uwierzytelnieniu zostanie utworzone nowe konto.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do faksu. I adres URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź do faksu. Bezpośredni adres URL logowania i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do faksu. Dodatkowo, dla którego skonfigurowano Logowanie jednokrotne. 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu faksu. Dodatkowo kafelek w obszarze Moje aplikacje, jeśli jest skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do faksu. Dodatkowo, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu faksu. Dodatkowo można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).