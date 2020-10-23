---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu spotkań Cisco WebEx | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i spotkaniami Cisco WebEx.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/21/2019
ms.author: jeedes
ms.openlocfilehash: 1d53cfc874bca6529fdee821ce3173607d5f06b3
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456058"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu spotkań Cisco WebEx

W tym samouczku dowiesz się, jak zintegrować spotkania Cisco WebEx z usługą Azure Active Directory (Azure AD). W przypadku integrowania spotkań Cisco WebEx z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do spotkań Cisco WebEx.
* Zezwól użytkownikom na automatyczne logowanie do spotkań Cisco WebEx z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w systemie Cisco WebEx.

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Spotkania Cisco WebEx obsługują usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne

* Spotkania Cisco WebEx wspierają Inicjowanie obsługi użytkowników **just in Time**

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Dodawanie spotkań Cisco WebEx z galerii

Aby skonfigurować integrację spotkań Cisco WebEx w usłudze Azure AD, musisz dodać do listy zarządzanych aplikacji SaaS spotkania Cisco WebEx z galerii.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **spotkania Cisco WebEx** .
1. Wybierz pozycję **spotkania Cisco WebEx** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla spotkań Cisco WebEx

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą spotkań Cisco WebEx przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w ramach spotkań Cisco WebEx.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu spotkań Cisco WebEx, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj spotkania Cisco WebEx Logowanie jednokrotne](#configure-cisco-webex-meetings-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego spotkań Cisco WebEx](#create-cisco-webex-meetings-test-user)** , aby dysponować odpowiednikiem B. Simon w ramach spotkań Cisco WebEx, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracji aplikacji do **spotkań Cisco WebEx** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** można skonfigurować aplikację w trybie inicjowania **dostawcy tożsamości** , przenosząc plik **metadanych dostawcy usług** w następujący sposób:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    c. Po pomyślnym zakończeniu przekazywania pliku metadanych dostawcy usług wartości **identyfikatorów** i **adresów URL odpowiedzi** są uzyskiwane automatycznie wypełniane w sekcji **Podstawowa konfiguracja SAML** .

    >[!Note]
    >Zostanie pobrany plik metadanych dostawcy usług z sekcji **Konfigurowanie spotkań Cisco WebEx — logowanie JEDNOkrotne** , który został wyjaśniony w dalszej części tego samouczka. 

1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , wykonaj następujące czynności:  

    a. W sekcji **Podstawowa konfiguracja języka SAML** kliknij ikonę Edytuj/pióro.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)
    
    b. W polu tekstowym **adres URL logowania** wpisz adres URL, używając następującego wzorca: ` https://<customername>.my.webex.com`

5. Aplikacja do spotkań Cisco WebEx oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image (obraz)](common/edit-attribute.png)

6. Oprócz powyższych, aplikacja do spotkań Cisco WebEx oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji Oświadczenia użytkownika w oknie dialogowym Atrybuty użytkownika wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli: 

    | Nazwa | Atrybut źródłowy|
    | ---------------|  --------- |
    |   firstname    | user.givenname |
    |   lastname    | user.surname |
    |   poczta e-mail       | user.mail |
    |   Identyfikator UID    | user.mail |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Z listy **atrybutów źródłowych** wybierz wartość atrybutu wyświetlaną dla tego wiersza z listy rozwijanej.

    f. Kliknij pozycję **Zapisz**.

4. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. Na stronie **Konfigurowanie spotkań Cisco WebEx** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz funkcję B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do spotkań Cisco WebEx.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **spotkania Cisco WebEx**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurowanie spotkań Cisco WebEx Logowanie jednokrotne

1. Przejdź do `https://<customername>.webex.com/admin` adresu URL z poświadczeniami administracyjnymi.

2. Przejdź do obszaru **typowe ustawienia lokacji** i przejdź do **konfiguracji rejestracji jednokrotnej**.
 
    ![Zrzut ekranu przedstawia administrowanie Cisco WebEx z użyciem wspólnych ustawień lokacji i wybranej konfiguracji S-O.](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. Na stronie **Administracja WebEx** wykonaj następujące czynności:

    ![Zrzut ekranu przedstawia stronę administrowania Webexą z informacjami opisanymi w tym kroku.](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Wybierz pozycję **SAML 2,0** jako **Protokół federacyjny**.

    b. Kliknij link **Importuj metadane SAML** , aby przekazać plik metadanych, który został pobrany z Azure Portal.

    c. Kliknij przycisk **Eksportuj** , aby pobrać plik metadanych dostawcy usług i przekazać go w sekcji **podstawowe konfiguracje języka SAML** na Azure Portal.

    d. W polu tekstowym **AuthContextClassRef** wpisz `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` i, jeśli chcesz włączyć uwierzytelnianie wieloskładnikowe przy użyciu usługi Azure AD wpisz dwie wartości, takie jak `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Wybierz pozycję **Autotworzenie konta**.

    >[!NOTE]
    >Aby włączyć Inicjowanie obsługi klienta **just-in-Time** , należy sprawdzić, czy **Funkcja autouzupełniania konta**jest włączona. Oprócz atrybutów tokenów SAML należy je przesłać do odpowiedzi SAML.

    f. Kliknij pozycję **Zapisz**.

    >[!NOTE]
    >Ta konfiguracja jest tylko dla klientów korzystających z WebEx UserID w formacie poczty e-mail.

### <a name="create-cisco-webex-meetings-test-user"></a>Utwórz użytkownika testowego spotkań Cisco WebEx

Celem tej sekcji jest utworzenie użytkownika o nazwie B. Simon w ramach spotkań Cisco WebEx. W przypadku spotkań Cisco WebEx jest dostępna obsługa **just-in-Time** , która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w spotkaniach Cisco WebEx, zostanie utworzony nowy, gdy spróbujesz uzyskać dostęp do spotkań Cisco WebEx.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka spotkań Cisco WebEx w panelu dostępu należy automatycznie zalogować się do spotkań Cisco WebEx, dla których skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](./tutorial-list.md)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj usługi ServiceNow z usługą Azure AD](https://aad.portal.azure.com)