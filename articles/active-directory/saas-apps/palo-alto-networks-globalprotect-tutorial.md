---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Palo Alto Networks-GlobalProtect | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Palo Alto Networks - GlobalProtect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03bef6f2-3ea2-4eaa-a828-79c5f1346ce5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aa6c799535db21a9d40f2eb8b74300a8c6b00f0
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739978"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Palo Alto Networks — GlobalProtect

W tym samouczku dowiesz się, jak zintegrować Palo Alto Networks-GlobalProtect z usługą Azure Active Directory (Azure AD). W przypadku integrowania Palo Alto Networks-GlobalProtect z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Palo Alto Networks-GlobalProtect.
* Zezwól użytkownikom na automatyczne logowanie do Palo Alto Networks-GlobalProtect z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Palo Alto Networks — subskrypcja z włączonym logowaniem jednokrotnym (SSO) GlobalProtect.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Palo Alto Networks - GlobalProtect obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**
* Aplikacja Palo Alto Networks - GlobalProtect obsługuje aprowizację użytkownika **Just in time**

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>Dodawanie aplikacji Palo Alto Networks - GlobalProtect z galerii

Aby skonfigurować integrację aplikacji Palo Alto Networks - GlobalProtect w usłudze Azure AD, należy dodać pozycję Palo Alto Networks - GlobalProtect z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Palo Alto Networks-GlobalProtect** w polu wyszukiwania.
1. Wybierz pozycję **Palo Alto Networks-GlobalProtect** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-palo-alto-networks---globalprotect"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Palo Alto Networks-GlobalProtect

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Palo Alto Networks-GlobalProtect przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Palo Alto Networks-GlobalProtect.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Palo Alto Networks-GlobalProtect, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Palo Alto Networks-GlobalProtect Logowanie jednokrotne](#configure-palo-alto-networks---globalprotect-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Palo Alto Networks-GlobalProtect test użytkownika](#create-palo-alto-networks---globalprotect-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w Palo Alto Networks-GlobalProtect, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie **Palo Alto Networks-GlobalProtect** Application Integration Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej aplikacji Palo Alto Networks - GlobalProtect](https://support.paloaltonetworks.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie Palo Alto Networks-GlobalProtect** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Palo Alto Networks-GlobalProtect.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Palo Alto Networks-GlobalProtect**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurowanie Palo Alto Networks — GlobalProtect Logowanie jednokrotne

1. W innym oknie przeglądarki otwórz interfejs użytkownika administratora zapory w aplikacji Palo Alto Networks z uprawnieniami administracyjnymi.

2. Kliknij pozycję **Device** (Urządzenie).

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML) w lewym pasku nawigacyjnym, a następnie kliknij przycisk Import (Importuj), aby zaimportować plik metadanych.

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Wykonaj następujące czynności w oknie importowania

    ![Konfigurowanie logowania jednokrotnego w rozwiązaniu Palo Alto](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W polu tekstowym **Profile Name** (Nazwa profilu) podaj nazwę np. Azure AD GlobalProtect.

    b. W polu **Identity Provider Metadata** (Metadane dostawcy tożsamości) kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z witryny Azure Portal.

    c. Kliknij przycisk **OK** .

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks - GlobalProtect

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Palo Alto Networks-GlobalProtect. Aplikacja Palo Alto Networks - GlobalProtect obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Palo Alto Networks - GlobalProtect, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Palo Alto Networks - GlobalProtect w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Palo Alto Networks - GlobalProtect, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wypróbuj Palo Alto Networks-GlobalProtect z usługą Azure AD](https://aad.portal.azure.com/)
