---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu usługi Forts Change Cloud | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Fort Zmień chmurę.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: jeedes
ms.openlocfilehash: c80d61e367288edfc207fb0160b5d03152ad6ce4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583789"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortes-change-cloud"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu usługi Forts Change Cloud

W tym samouczku dowiesz się, jak zintegrować w chmurze zmiany chmury z Azure Active Directory (Azure AD). Po zintegrowaniu usługi Fort w chmurze Zmień chmurę z usługą Azure AD możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do usługi Forts Change Cloud.
* Zezwól użytkownikom na automatyczne logowanie do platformy Fort Zmień chmurę za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Fort. Zmień subskrypcję z włączonym logowaniem jednokrotnym w chmurze.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Forts Change Cloud obsługuje usługę **SP i dostawcy tożsamości,** która zainicjowała Logowanie jednokrotne.

## <a name="adding-fortes-change-cloud-from-the-gallery"></a>Dodawanie usługi Forts Zmień chmurę z galerii

Aby skonfigurować integrację z usługą Azure AD w chmurze, należy dodać do listy zarządzanych aplikacji SaaS usługi w chmurze Zmień chmurę.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Forts Change Cloud** w polu wyszukiwania.
1. Wybierz pozycję **Forts Zmień** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-fortes-change-cloud"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla systemu Fort Zmień chmurę

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą platformy Fort Zmień chmurę przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w sieci Fort.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu systemu Fort Zmień chmurę, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Konfigurowanie usługi Fort Zmień Logowanie jednokrotne w chmurze](#configure-fortes-change-cloud-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Fort. Zmień użytkownika testowego na chmurę](#create-fortes-change-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w Fort, Zmień chmurę, która jest połączona z usługą Azure AD reprezentacją użytkownika.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Fort Zmień** integrację aplikacji w chmurze Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<UNIQUE_IDENTIFIER>.fortes-online.com/saml/metadata`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<UNIQUE_IDENTIFIER>.fortes-online.com/`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<UNIQUE_IDENTIFIER>.fortes-online.com/saml/SSO`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z firmą [Fort. Zmień zespół pomocy technicznej w chmurze](mailto:support@fortes.nl) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. W przypadku zmiany aplikacji w chmurze oczekuje potwierdzenia SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Firma Czterdziesta zmiana aplikacji w chmurze oczekuje, że **unikatowy identyfikator użytkownika** ma być mapowany z **użytkownikiem. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę **Edytuj** i zmieniając mapowanie atrybutu.

    ![image (obraz)](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** kliknij przycisk Kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** i zapisać go na komputerze.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi Forts Change Cloud.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Forts Zmień chmurę**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-fortes-change-cloud-sso"></a>Konfigurowanie usługi Fort Zmień Logowanie jednokrotne w chmurze

Aby skonfigurować Logowanie jednokrotne w **chmurze** , należy wysłać **adres URL metadanych federacji aplikacji** do [czterdziestu w przypadku zmiany zespołu pomocy technicznej w chmurze](mailto:support@fortes.nl). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-fortes-change-cloud-test-user"></a>Utwórz Fort. Zmień użytkownika testowego w chmurze

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w czterdziestych zmianach w chmurze. Współpraca z usługą [Fort powoduje zmianę zespołu pomocy technicznej chmury](mailto:support@fortes.nl) w celu dodania użytkowników na platformie. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Fort w przypadku zmiany adresu URL logowania w chmurze, w którym można zainicjować przepływ logowania.  

* Przejdź do obszaru Fort Zmień adres URL logowania w chmurze bezpośrednio i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do usługi Fort w chmurze Zmień chmurę, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu przycisku "Forts Change Cloud" w obszarze Moje aplikacje, jeśli skonfigurowano w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do usługi Fort w chmurze Zmień chmurę, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu usługi Forts Zmień chmurę można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).