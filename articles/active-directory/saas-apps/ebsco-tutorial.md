---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą EBSCO | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i EBSCO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92454341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>Samouczek: Azure Active Directory integracji logowania jednokrotnego (SSO) z usługą EBSCO

W tym samouczku dowiesz się, jak zintegrować usługę EBSCO z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi EBSCO z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do EBSCO.
* Zezwól użytkownikom na automatyczne logowanie się do usługi EBSCO przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) EBSCO.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* EBSCO obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* EBSCO obsługuje Inicjowanie obsługi użytkowników **just in Time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-ebsco-from-the-gallery"></a>Dodawanie EBSCO z galerii

Aby skonfigurować integrację programu EBSCO z usługą Azure AD, musisz dodać EBSCO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **EBSCO** w polu wyszukiwania.
1. Wybierz pozycję **EBSCO** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla EBSCO

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą EBSCO przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w EBSCO.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą EBSCO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-ebsco-sso)** w usłudze EBSCO, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    * **[Utwórz użytkownika testowego EBSCO](#create-ebsco-test-user)** , aby dysponować odpowiednikiem B. Simon w EBSCO, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **EBSCO** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Identyfikator** wpisz adres URL: `pingsso.ebscohost.com`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem obsługi klienta EBSCO](mailto:support@ebsco.com) , aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    o   **unikatowe elementy:**  

    o   **CustId** = wprowadź unikatowy identyfikator klienta EBSCO 

    o   **profilu** = klienci mogą dostosować łącze, aby kierować użytkowników do określonego profilu (w zależności od tego, co kupuje w usłudze EBSCO). Mogą one wprowadzić konkretny identyfikator profilu. Główne identyfikatory to EDS (EBSCO Discovery Service) i ehost (EBSOCOhost bazy danych). Instrukcje dotyczące tego samego są podane [tutaj](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

1. Aplikacja EBSCO oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

    > [!Note]
    > Atrybut **name** jest obowiązkowy i jest mapowany przy użyciu **wartości identyfikatora nazwy** w aplikacji EBSCO. To ustawienie jest domyślnie dodawane, aby nie trzeba było dodawać tego ręcznie.

1. Oprócz powyższych, aplikacja EBSCO oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ---------------| --------------- |
    | FirstName (Imię)   | user.givenname |
    | LastName (Nazwisko)   | user.surname |
    | E-mail   | user.mail |

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie EBSCO** skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi EBSCO.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **EBSCO**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-ebsco-sso"></a>Konfigurowanie logowania jednokrotnego EBSCO

Aby skonfigurować Logowanie jednokrotne na stronie **EBSCO** , musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowane adresy URL z Azure Portal do [zespołu pomocy technicznej EBSCO](mailto:support@ebsco.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ebsco-test-user"></a>Utwórz użytkownika testowego EBSCO

W przypadku EBSCO, Inicjowanie obsługi użytkowników odbywa się automatycznie.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

Usługa Azure AD przekazuje wymagane dane do aplikacji EBSCO. Inicjowanie obsługi użytkowników EBSCO może być automatyczne lub wymagać jednorazowego formularza. Jest to zależne od tego, czy klient ma wiele istniejących kont EBSCOhost z zapisywanymi ustawieniami osobistymi. Te same możliwości można omówić z [zespołem pomocy technicznej EBSCO](mailto:support@ebsco.com) podczas wdrażania. W obu przypadkach klient nie musi tworzyć żadnych kont EBSCOhost przed testowaniem.

   > [!Note]
   > Możesz zautomatyzować inicjowanie aprowizacji/personalizacji użytkowników EBSCOhost. Skontaktuj się z [zespołem pomocy technicznej EBSCO](mailto:support@ebsco.com) , aby dowiedzieć się więcej o aprowizacji użytkowników just in Time.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

1. Po kliknięciu kafelka EBSCO w panelu dostępu należy automatycznie zalogować się do aplikacji EBSCO.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

1. Po zalogowaniu się do aplikacji kliknij przycisk **Zaloguj** w prawym górnym rogu.

    ![Logowanie do EBSCO na liście aplikacji](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. Zostanie wyświetlony monit jednorazowy, aby połączyć się z logowaniem instytucjonalnym/SAML przy użyciu **linku do istniejącego konta MyEBSCOhost teraz** lub **utworzyć nowe konto MyEBSCOhost i połączyć je z kontem instytucji**. Konto jest używane do personalizacji aplikacji EBSCOhost. Wybierz opcję **Utwórz nowe konto** . zobaczysz, że formularz do personalizacji jest wstępnie zakończony wartościami z odpowiedzi SAML, jak pokazano na poniższym zrzucie ekranu. Kliknij przycisk **"Kontynuuj"** , aby zapisać to zaznaczenie.
    
     ![Użytkownik EBSCO na liście aplikacji](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. Po ukończeniu powyższej konfiguracji Wyczyść pliki cookie/pamięć podręczną i zaloguj się ponownie. Nie musisz logować się ręcznie, a ustawienia personalizacji zostaną zapamiętane.

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj EBSCO z usługą Azure AD](https://aad.portal.azure.com/)