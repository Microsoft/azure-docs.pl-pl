---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą TravelPerk | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i TravelPerk.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/23/2020
ms.author: jeedes
ms.openlocfilehash: eefc47e1308dc5b59d8b57d7481a5250a7d2af93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99475746"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-travelperk"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą TravelPerk

W tym samouczku dowiesz się, jak zintegrować usługę TravelPerk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi TravelPerk z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do TravelPerk.
* Zezwól użytkownikom na automatyczne logowanie się do usługi TravelPerk przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Konto TravelPerk z subskrypcją Premium.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* TravelPerk obsługuje logowanie jednokrotne w usłudze **SP**

* TravelPerk obsługuje Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-travelperk-from-the-gallery"></a>Dodawanie TravelPerk z galerii

Aby skonfigurować integrację programu TravelPerk z usługą Azure AD, musisz dodać TravelPerk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **TravelPerk** w polu wyszukiwania.
1. Wybierz pozycję **TravelPerk** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-travelperk"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla TravelPerk

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą TravelPerk przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w TravelPerk.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą TravelPerk, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-travelperk-sso)** w usłudze TravelPerk, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego TravelPerk](#create-travelperk-test-user)** , aby dysponować odpowiednikiem B. Simon w TravelPerk, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **TravelPerk** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<COMPANY>.travelperk.com/`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<COMPANY>.travelperk.com/accounts/saml2/metadata/<APPLICATION_ID>`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<COMPANY>.travelperk.com/accounts/saml2/callback/<APPLICATION_ID>/?acs`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi i identyfikatora. Wartości można znaleźć w ramach konta TravelPerk: Przejdź do pozycji **ustawienia firmowe**  >  **integracja**  >  **rejestracji** jednokrotnej. Aby uzyskać pomoc, odwiedź stronę [TravelPerk HelpCenter](https://support.travelperk.com/hc/en-us/articles/360052450271-How-can-I-setup-SSO-for-Azure-SAML-).

1. Aplikacja TravelPerk oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. W domyślnym mapowaniu **EmailAddress** jest mapowany z **User. mail**. Jednak aplikacja TravelPerk oczekuje, że **EmailAddress** mają być mapowane przy użyciu elementu **User. userPrincipalName**. W przypadku TravelPerk należy edytować Mapowanie atrybutów: kliknij ikonę **Edytuj** , a następnie zmień mapowanie atrybutu. Aby edytować atrybut, po prostu kliknij atrybut, aby otworzyć tryb edycji.

    ![image (obraz)](common/default-attributes.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie TravelPerk** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi TravelPerk.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **TravelPerk**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-travelperk-sso"></a>Konfigurowanie logowania jednokrotnego TravelPerk

Aby skonfigurować Logowanie jednokrotne na stronie **TravelPerk** , musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej TravelPerk](mailto:trex@travelperk.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-travelperk-test-user"></a>Utwórz użytkownika testowego TravelPerk

W tej sekcji użytkownik o nazwie B. Simon został utworzony w TravelPerk. TravelPerk obsługuje obsługę just-in-Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze TravelPerk, zostanie utworzony nowy, gdy zostanie podjęta próba uzyskania dostępu do TravelPerk.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania TravelPerk, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania TravelPerk i zainicjuj w nim przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka TravelPerk w panelu dostępu zostanie on przekierowany do adresu URL logowania TravelPerk. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu TravelPerk można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
