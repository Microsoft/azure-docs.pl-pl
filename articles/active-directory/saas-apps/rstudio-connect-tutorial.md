---
title: 'Samouczek: integracja Azure Active Directory z usługą RStudio łączenie protokołu SAML — uwierzytelnianie | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i RStudio Connect SAML Authentication.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182397"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Samouczek: integracja Azure Active Directory z uwierzytelnianiem SAML przy użyciu programu RStudio

W tym samouczku dowiesz się, jak zintegrować usługę RStudio Connect Authentication SAML z usługą Azure Active Directory (Azure AD).
Integrowanie RStudio łączenie protokołu SAML z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do RStudio łączenie SAML Authentication.
* Możesz zezwolić użytkownikom na automatyczne logowanie do usługi RStudio łączenie SAML Authentication (Logowanie jednokrotne) przy użyciu kont w usłudze Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD za pomocą RStudio Connect SAML Authentication, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* RStudio Połącz z uwierzytelnianiem SAML. Istnieje 45- [dniowa bezpłatna wersja ewaluacyjna.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* RStudio łączenie SAML Authentication obsługuje uwierzytelnianie SSO z użyciem protokołu **SP i dostawcy tożsamości**

* Uwierzytelnianie SAML RStudio Connect obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Dodawanie RStudio Połącz z uwierzytelnianiem SAML z galerii

Aby skonfigurować integrację usługi RStudio łączenie z uwierzytelnianiem SAML w usłudze Azure AD, należy dodać RStudio połączenie SAML z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **RStudio Connect SAML Authentication** w polu wyszukiwania.
1. Wybierz pozycję **RStudio Połącz z uwierzytelnianiem SAML** z poziomu panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD dla usługi RStudio łączenie SAML — uwierzytelnianie

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą RStudio Connect SAML Authentication przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie RStudio Connect SAML Authentication.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą RStudio Connect SAML Authentication, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj RStudio Połącz uwierzytelnianie SAML](#configure-rstudio-connect-saml-authentication-sso)** za pomocą logowania jednokrotnego, aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    * **[Utwórz użytkownika testowego RStudio połączenia SAML](#create-rstudio-connect-saml-authentication-test-user)** — Aby uzyskać odpowiednik Britta Simon w RStudio, Połącz uwierzytelnianie SAML, które jest połączone z reprezentacją usługi Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **RStudio łączenie aplikacji uwierzytelnianie SAML** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowanym przy użyciu programu **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące kroki, zastępując `<example.com>` je adresem i portem serwera uwierzytelniania SAML RStudio:

    ![RStudio łączenie protokołu SAML domeny uwierzytelniania i adresów URL logowania jednokrotnego — informacje](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<example.com>/__login__/saml`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<example.com>/__login__/saml/acs`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    ![RStudio łączenie metadanych uwierzytelniania SAML](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<example.com>/`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Są one określane na podstawie adresu serwera uwierzytelniania SAML RStudio Connect ( `https://example.com` w przykładach powyżej). Jeśli masz problemy, skontaktuj się z [zespołem obsługi uwierzytelniania SAML RStudio Connect](mailto:support@rstudio.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja do uwierzytelniania SAML RStudio Connect oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. RStudio łączenie aplikacji uwierzytelniania SAML oczekuje, że **NameIdentifier** mają być mapowane przy użyciu elementu **User. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiowania, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi RStudio łączenie z uwierzytelnianiem SAML.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **RStudio Połącz z uwierzytelnianiem SAML**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Konfigurowanie RStudio łączenie protokołu SAML uwierzytelnianie logowania jednokrotnego

Aby skonfigurować Logowanie jednokrotne w usłudze **RStudio**, należy użyć **adresu URL metadanych federacji aplikacji** oraz **adresu serwera** użytego powyżej. Jest to wykonywane w pliku konfiguracji uwierzytelniania SAML RStudio Connect w lokalizacji `/etc/rstudio-connect.rstudio-connect.gcfg` .

Jest to przykładowy plik konfiguracji:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

W polu wartość Zapisz **adres serwera** `Server.Address` i **adres URL metadanych federacji aplikacji** `SAML.IdPMetaData` . Ta Przykładowa konfiguracja korzysta z nieszyfrowanego połączenia HTTP, natomiast usługa Azure AD wymaga użycia szyfrowanego połączenia HTTPS. Możesz użyć [zwrotnego serwera proxy](https://docs.rstudio.com/connect/admin/proxy/) przed RSTUDIO połączenie SAML lub skonfigurować RStudio Połącz uwierzytelnianie SAML, aby [bezpośrednio korzystać z protokołu HTTPS](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Jeśli masz problemy z konfiguracją, możesz przeczytać [Podręcznik administratora uwierzytelniania protokołu SAML RStudio Connect](https://docs.rstudio.com/connect/admin/authentication/saml/) lub wysłać wiadomość e-mail do [zespołu pomocy technicznej RStudio](mailto:support@rstudio.com) w celu uzyskania pomocy.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Utwórz użytkownika testowego uwierzytelniania SAML RStudio Connect

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w RStudio Connect SAML Authentication. Uwierzytelnianie SAML RStudio Connect obsługuje obsługę just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie RStudio Connect SAML Authentication, zostanie utworzony nowy podczas próby dostępu do RStudio połączenia SAML.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do RStudio łączenie protokołu SAML logowania przy użyciu adresu URL, gdzie można zainicjować przepływ logowania.  

* Przejdź do RStudio, aby połączyć się bezpośrednio z adresem URL logowania przy użyciu protokołu SAML i zainicjować w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do usługi RSTUDIO Connect SAML Authentication, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka RStudio Connect SAML Authentication w panelu dostępu, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do usługi RStudio Connect SAML Authentication, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu RStudio Połącz uwierzytelnianie SAML można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).