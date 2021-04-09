---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą AutoWatch | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i ponownie monitorować.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: jeedes
ms.openlocfilehash: 032957b49c7ef9170a7e1aaa63eaa66a3c3e8401
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-rewatch"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z ponownym oglądaniem

W tym samouczku dowiesz się, jak zintegrować z usługą Azure Active. Katalog (Azure AD). Gdy integrujesz się z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do monitorowania.
* Zezwól użytkownikom na automatyczne logowanie się, aby ponownie monitorować je za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Obejrzyj subskrypcję obsługującą Logowanie jednokrotne (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rewatch obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO
* Czujka obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-rewatch-from-the-gallery"></a>Dodawanie czujki z galerii

Aby skonfigurować integrację z usługą Azure AD, musisz dodać ponownie Obejrzyj z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **Watch** w polu wyszukiwania.
1. Wybierz pozycję ponownie **Obejrzyj** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-rewatch"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu rewatch

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą funkcji monitorowania przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy nawiązać relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w obszarze ponowne monitorowanie.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu instrukcji Watch, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj ponownie funkcję logowania jednokrotnego](#configure-rewatch-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz ponownie Obejrzyj użytkownika testowego](#create-rewatch-test-user)** , aby uzyskać odpowiednika B. Simon w obserwie, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie ponowne **monitorowanie** integracji aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://rewatch.tv/login`

1. Aplikacja do monitorowania oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, ponowne monitorowanie aplikacji oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | --------------- | --------- |
    | Group (Grupa) | User. Groups |


1. Kliknij pozycję **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie czujki** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do monitorowania.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję ponownie **Obejrzyj**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-rewatch-sso"></a>Konfigurowanie ponownego monitorowania logowania jednokrotnego

1. W innym oknie przeglądarki sieci Web Zaloguj się do swojej strony jako administrator.

1. Kliknij pozycję **Konsola administracyjna** w menu po lewej stronie.

    ![Obejrzyj konsolę administracyjną na stronie głównej.](./media/rewatch-tutorial/admin-console.png)

1. Przejdź do obszaru **zabezpieczenia** i wykonaj poniższe kroki w sekcji "Logowanie jednokrotne **protokołu SAML"** .

    ![Sekcja rejestracji jednokrotnej protokołu SAML.](./media/rewatch-tutorial/security.png)

    a. W polu tekstowym **adres URL elementu docelowego logowania jednokrotnego dostawcy tożsamości** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    b. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikatu dostawcy tożsamości** .

    c. Zaznacz pole wyboru **Włącz logowanie za pośrednictwem protokołu SAML dla tego kanału** i kliknij pozycję **Zapisz**.

### <a name="create-rewatch-test-user"></a>Tworzenie ponownie oglądanego użytkownika testowego

W tej sekcji użytkownik o nazwie Britta Simon został utworzony w sekcji monitorowanie. Funkcja monitorowania obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie na adres URL logowania, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do pozycji ponownie Obejrzyj adres URL logowania i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do monitorowania, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka czujka w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do monitorowania, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu przeglądarki można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).