---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu programu ADP | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a rozwiązaniem ADP.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: c170581496241ab3be0fe54bb484549ab4b1f976
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88542908"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adp"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z ADP

W tym samouczku dowiesz się, jak zintegrować ADP z Azure Active Directory (Azure AD). Po zintegrowaniu programu ADP z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do ADP.
* Zezwól użytkownikom na automatyczne logowanie do programu ADP przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Rozwiązanie ADP obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-adp-from-the-gallery"></a>Dodawanie rozwiązania ADP z galerii

Aby skonfigurować integrację rozwiązania ADP z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ADP** w polu wyszukiwania.
1. Wybierz pozycję **ADP** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-adp"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla ADP

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu ADP przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ADP.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu ADP, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Konfigurowanie logowania jednokrotnego](#configure-adp-sso)** dla programu ADP — w celu skonfigurowania pojedynczych ustawień Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego ADP](#create-adp-test-user)** , aby dysponować odpowiednikiem B. Simon w ADP, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę ** Właściwości**, a następnie wykonaj następujące kroki: 

    ![Właściwości logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_prop.png)

    a. Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b. Skopiuj wartość pola **Adres URL dostępu użytkownika** i wklej ją w **sekcji Adres URL logowania jednokrotnego**, co zostało opisane w dalszej części tego samouczka.

    c. Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    d. Ustaw pole **Widoczne dla użytkowników** na wartość **Nie**.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ADP** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL:  `https://fed.adp.com`

4. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie ADP** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu ADP.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **ADP**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-adp-sso"></a>Konfigurowanie rejestracji jednokrotnej w programie ADP

Aby skonfigurować logowanie jednokrotne po stronie rozwiązania **ADP**, należy przekazać pobrany **plik XML metadanych** w [witrynie internetowej rozwiązania ADP](https://adpfedsso.adp.com/public/login/index.fcc).

> [!NOTE]  
> Ten proces może potrwać kilka dni.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurowanie usług ADP pod kątem dostępu federacyjnego

>[!Important]
> Twoi pracownicy, dla których wymagany jest dostęp federacyjny do usług ADP, muszą zostać przypisani do aplikacji usługi ADP, co wiąże się z koniecznością ponownego przypisania użytkowników do określonej usługi ADP.
Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP skonfiguruj swoje usługi ADP, a następnie przypisz użytkowników lub zarządzaj nimi, aby kontrolować ich dostęp do określonej usługi ADP.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **ADP** w polu wyszukiwania.
1. Wybierz pozycję **ADP** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.
1. W witrynie Azure Portal na stronie integracji aplikacji **ADP** kliknij kartę ** Właściwości**, a następnie wykonaj następujące kroki:  

    ![Połączone właściwości logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_linkedproperties.png)

    a.  Ustaw pole **Możliwe logowanie użytkowników** na wartość **Tak**.

    b.  Ustaw pole **Wymagane przypisanie użytkownika** na wartość **Tak**.

    c.  Ustaw pole **Widoczne dla użytkowników** na wartość **Tak**.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **ADP** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.

1. W oknie dialogowym **Wybierz metodę logowania jednokrotnego** w polu **Tryb** wybierz pozycję **Połączony**. w celu połączenia aplikacji z rozwiązaniem **ADP**.

    ![Połączone logowanie jednokrotne](./media/adpfederatedsso-tutorial/tutorial_adp_linked.png)

1. Przejdź do sekcji **Konfigurowanie adresu URL logowania**, a następnie wykonaj następujące kroki:

    ![Właściwość logowania jednokrotnego](./media/adpfederatedsso-tutorial/tutorial_adp_linkedsignon.png)

    a. Wklej wartość pola **Adres URL dostępu użytkownika**, która została skopiowana z przedstawionej powyżej **karty właściwości** (z głównej aplikacji ADP).
                                                             
    b. Poniżej przedstawiono 5 aplikacji, które obsługują różne **adresy URL stanu przekaźnika**. Należy ręcznie dołączyć odpowiednią wartość **adresu URL stanu przekaźnika** dla określonej aplikacji w polu **Adres URL dostępu użytkownika**.
    
    * **ADP Workforce Now**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

    * **ADP Workforce Now Enhanced Time**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`
    
    * **ADP Vantage HCM**
        
        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

    * **ADP Enterprise HR**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

    * **MyADP**

        `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

9. **Zapisz** zmiany.

10. Po otrzymaniu potwierdzenia od przedstawiciela firmy ADP rozpocznij testowanie przez jednego lub dwóch użytkowników.

    a. Przypisz kilku użytkowników do aplikacji usługi ADP, aby przetestować dostęp federacyjny.

    b. Test zostanie zakończony pomyślnie, gdy użytkownik uzyska dostęp do aplikacji usługi ADP w galerii i będzie mógł uzyskać dostęp do usługi ADP.
 
11. Jako potwierdzenie pomyślnego wykonania testu przypisz federacyjną usługę ADP do poszczególnych użytkowników lub grup użytkowników, co zostało wyjaśnione w dalszej części tego samouczka, i przekaż o tym informacje swoim pracownikom.

### <a name="create-adp-test-user"></a>Tworzenie użytkownika testowego rozwiązania ADP

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w ADP. Skontaktuj się z [zespołem pomocy technicznej rozwiązania ADP](https://www.adp.com/contact-us/overview.aspx) w celu dodania użytkowników w ramach konta rozwiązania ADP. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka ADP w panelu dostępu powinno nastąpić automatyczne zalogowanie do rozwiązania ADP, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program ADP z usługą Azure AD](https://aad.portal.azure.com)
