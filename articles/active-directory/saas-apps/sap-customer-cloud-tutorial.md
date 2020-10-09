---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z chmurą SAP dla klienta | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i platformą SAP Cloud for Customer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.openlocfilehash: f9fd458ea19fa0dad2f630f94a67d5e1db96cee3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543316"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) z chmurą SAP dla klienta

W tym samouczku dowiesz się, jak zintegrować chmurę SAP dla klientów z Azure Active Directory (Azure AD). W przypadku integracji chmury SAP dla klienta z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury SAP dla klienta.
* Zezwól użytkownikom na automatyczne logowanie do chmury SAP dla klientów z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Chmura SAP dla subskrypcji z włączonym logowaniem jednokrotnym (SSO) klienta.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Platforma SAP Cloud for Customer obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Dodawanie platformy SAP Cloud for Customer z galerii

Aby skonfigurować integrację platformy SAP Cloud for Customer z usługą Azure AD, należy dodać tę platformę z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **SAP Cloud dla klienta** .
1. Wybierz pozycję **SAP Cloud dla klienta** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla chmury SAP dla klienta

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD z chmurą SAP dla klienta przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze SAP dla klienta.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu chmury SAP dla klienta, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj chmurę SAP na potrzeby logowania jednokrotnego klienta](#configure-sap-cloud-for-customer-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz chmurę SAP dla użytkownika testowego klienta](#create-sap-cloud-for-customer-test-user)** , aby dysponować odpowiednikiem B. Simon w chmurze SAP dla klienta, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z **chmurą SAP dla klienta** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta platformy SAP Cloud for Customer](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Usługa SAP Cloud for Customer oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image (obraz)](common/edit-attribute.png)

1. W sekcji **Atrybuty użytkownika** okna dialogowego **Atrybuty i oświadczenia użytkownika** wykonaj następujące kroki:

    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image (obraz)](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image (obraz)](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Wybierz pozycję **Przekształcanie** dla pola **Źródło**.

    c. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    d. Z listy **Parametr 1** wybierz atrybut użytkownika, którego chcesz użyć na potrzeby implementacji.
    Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    e. Kliknij przycisk **Zapisz**.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie chmury SAP dla klienta** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury SAP dla klienta.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAP Cloud dla klienta**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurowanie chmury SAP na potrzeby logowania jednokrotnego klienta

1. Otwórz nowe okno przeglądarki sieci Web i zaloguj się do chmury SAP dla klienta firmy jako administrator.

2. Z lewej strony menu kliknij pozycję **dostawcy tożsamości**   >  **firmy dostawcy tożsamości**  >  **Dodaj** i w podręcznym Dodaj nazwę dostawcy tożsamości, taką jak **Azure AD**, kliknij przycisk **Zapisz** , a następnie kliknij pozycję **Konfiguracja SAML 2,0**.

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure01.png)

3. W sekcji **Konfiguracja SAML 2,0** wykonaj następujące czynności:

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Kliknij przycisk **Przeglądaj** , aby przesłać plik XML metadanych Federacji pobrany z Azure Portal.

    b. Po pomyślnym przekazaniu pliku XML poniższe wartości staną się automatycznie wypełniane, a następnie kliknij przycisk **Zapisz**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Tworzenie użytkownika testowego klienta platformy SAP Cloud for Customer

Aby umożliwić użytkownikom usługi Azure AD logowanie się do chmury SAP dla klienta, muszą one być obsługiwane w chmurze SAP dla klienta. W chmurze SAP dla klienta Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do chmury SAP dla klienta jako administrator zabezpieczeń.

2. Z lewej strony menu kliknij pozycję **Użytkownicy & autoryzacje**   >  **zarządzania użytkownikami**  >  **Dodaj użytkownika**.

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure03.png)

3. W sekcji **Dodaj nowego użytkownika** wykonaj następujące czynności:

    ![Konfiguracja SAP](./media/sap-customer-cloud-tutorial/configure04.png)

    a. W polu tekstowym **imię** i nazwisko, wprowadź nazwę użytkownika, na przykład **B**.

    b. W polu **tekstowym nazwisko wprowadź nazwę użytkownika** , na przykład **Simon**.

    c. W polu tekstowym **adres e-mail** wprowadź adres e-mail użytkownika, np `B.Simon@contoso.com` .

    d. W polu tekstowym **Nazwa logowania** wprowadź nazwę użytkownika, na przykład **B. Simon**.

    e. Wybierz **Typ użytkownika** zgodnie z wymaganiami.

    f. Wybierz opcję **aktywacji konta** zgodnie z wymaganiami.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SAP Cloud for Customer na panelu dostępu powinno nastąpić automatyczne zalogowanie do platformy SAP Cloud for Customer, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [ Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj chmurę SAP dla klienta w usłudze Azure AD](https://aad.portal.azure.com/)

