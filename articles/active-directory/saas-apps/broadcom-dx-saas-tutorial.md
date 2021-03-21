---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) za pomocą narzędzia Broadcom DX SaaS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Broadcom DX SaaS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/19/2021
ms.author: jeedes
ms.openlocfilehash: 35ab4d335560689a7e55bf1a8dff9fcb0bcc7d7f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601520"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-broadcom-dx-saas"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu narzędzia Broadcom DX SaaS

W tym samouczku dowiesz się, jak zintegrować program Broadcom DX SaaS z Azure Active Directory (Azure AD). W przypadku integracji z usługą Azure AD za pomocą programu Broadcom DX SaaS można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Broadcom DX SaaS.
* Zezwól użytkownikom na automatyczne logowanie do programu Broadcom DX SaaS z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) SaaS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Broadcom DX SaaS obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

* Program Broadcom DX SaaS obsługuje funkcję udostępniania **just in Time** użytkownika.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-broadcom-dx-saas-from-the-gallery"></a>Dodawanie programu Broadcom DX SaaS z galerii

Aby skonfigurować integrację programu Broadcom DX SaaS z usługą Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS aplikację Broadcom DX SaaS z galerii.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Broadcom DX SaaS** .
1. Wybierz pozycję **Broadcom DX SaaS** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-broadcom-dx-saas"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Broadcom DX SaaS

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą polecenia Broadcom DX SaaS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Broadcom DX SaaS.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Broadcom DX SaaS, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. Konfigurowanie ustawień logowania jednokrotnego w programie **[Broadcom DX SaaS](#configure-broadcom-dx-saas-sso)** na stronie aplikacji.
    1. **[Utwórz użytkownika testowego typu Broadcom DX SaaS](#create-broadcom-dx-saas-test-user)** , aby dysponować odpowiednikiem B. Simon w Broadcom DX SaaS, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Broadcom DX SaaS** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `DXI_<TENANT_NAME>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://axa.dxi-na1.saas.broadcom.com/ess/authn/<TENANT_NAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem obsługi klienta Broadcom DX SaaS](mailto:dxi-na1@saas.broadcom.com) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja Broadcom DX SaaS oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Broadcom DX SaaS oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
    
    | Nazwa |  Atrybut źródłowy|
    | --------------- | --------- |
    | Group (Grupa) | User. Groups |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie programu Broadcom DX SaaS** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi Broadcom DX SaaS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Broadcom DX SaaS**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-broadcom-dx-saas-sso"></a>Konfigurowanie logowania jednokrotnego w programie Broadcom DX SaaS

1. Zaloguj się do firmowej witryny firmy SaaS jako administrator.

2. Przejdź do obszaru **Ustawienia** i kliknij kartę **Użytkownicy** .

    ![Użytkownicy](./media/broadcom-dx-saas-tutorial/settings.png "Użytkownicy")

3. W sekcji **Zarządzanie użytkownikami** kliknij pozycję wielokropek i wybierz pozycję **SAML**.

    ![Zarządzanie użytkownikami](./media/broadcom-dx-saas-tutorial/local.png "Zarządzanie użytkownikami")

4. W sekcji **Identyfikowanie konta SAML** wykonaj następujące czynności.
   
    ![Koncie](./media/broadcom-dx-saas-tutorial/broadcom-1.png "Konto") 

    a. W polu tekstowym **wystawca** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z Azure Portal.

    b. W polu tekstowym **adres URL logowania dostawcy tożsamości (dostawcy tożsamości)** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL wylogowania dostawcy tożsamości (dostawcy tożsamości)** wklej wartość **adresu URL wylogowania** , która została skopiowana z Azure Portal.

    d. Otwórz pobrany **certyfikat (base64)** z Azure Portal do Notatnika i wklej zawartość do pola tekstowego **certyfikat dostawcy tożsamości** .

    e. Kliknij przycisk **dalej**.

5. W sekcji **atrybuty mapy** Wypełnij wymagane atrybuty SAML na poniższej stronie i kliknij przycisk **dalej**.

    ![Atrybuty](./media/broadcom-dx-saas-tutorial/broadcom-2.png "Atrybuty") 


6. W sekcji **Zidentyfikuj grupę użytkowników** wprowadź identyfikator obiektu tej grupy, a następnie kliknij przycisk **dalej**.

    ![Dodawanie grupy](./media/broadcom-dx-saas-tutorial/broadcom-3.png "Dodawanie grupy") 

7. W sekcji **Wypełnij konto SAML** Sprawdź, czy wypełniono wartości, a następnie kliknij przycisk **Zapisz**.

    ![Konto SAML](./media/broadcom-dx-saas-tutorial/broadcom-4.png "Konto SAML") 

### <a name="create-broadcom-dx-saas-test-user"></a>Utwórz użytkownika testowego Broadcom DX SaaS

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w języku Broadcom DX SaaS. Polecenie Broadcom DX SaaS obsługuje funkcję aprowizacji użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w programie Broadcom DX SaaS, zostanie utworzony nowy po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do programu Broadcom DX SaaS, dla którego skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Broadcom DX SaaS w obszarze Moje aplikacje należy automatycznie zalogować się do programu Broadcom DX SaaS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Broadcom DX SaaS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).