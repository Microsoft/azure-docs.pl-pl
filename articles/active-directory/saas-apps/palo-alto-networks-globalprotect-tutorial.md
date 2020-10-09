---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Palo Alto Networks-GlobalProtect | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Palo Alto Networks - GlobalProtect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 3c08694efafef315b0f5e96d293d883767860fef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Palo Alto Networks — GlobalProtect

W tym samouczku dowiesz się, jak zintegrować Palo Alto Networks-GlobalProtect z usługą Azure Active Directory (Azure AD). W przypadku integrowania Palo Alto Networks-GlobalProtect z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Palo Alto Networks-GlobalProtect.
* Zezwól użytkownikom na automatyczne logowanie do Palo Alto Networks-GlobalProtect z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

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

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Palo Alto Networks-GlobalProtect** w polu wyszukiwania.
1. Wybierz pozycję **Palo Alto Networks-GlobalProtect** from the Results panel, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla Palo Alto Networks-GlobalProtect

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Palo Alto Networks-GlobalProtect przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Palo Alto Networks-GlobalProtect.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Palo Alto Networks-GlobalProtect, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Palo Alto Networks-GlobalProtect Logowanie jednokrotne](#configure-palo-alto-networks---globalprotect-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Palo Alto Networks-GlobalProtect test użytkownika](#create-palo-alto-networks---globalprotect-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w Palo Alto Networks-GlobalProtect, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Palo Alto Networks-GlobalProtect** Application Integration Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
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
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Konfigurowanie Palo Alto Networks — GlobalProtect Logowanie jednokrotne

1. Otwórz Palo Alto Networks-GlobalProtect jako administrator w innym oknie przeglądarki.

2. Kliknij pozycję **Device** (Urządzenie).

    ![Konfigurowanie Palo Alto Logowanie jednokrotne 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML) w lewym pasku nawigacyjnym, a następnie kliknij przycisk Import (Importuj), aby zaimportować plik metadanych.

    ![Konfigurowanie Palo Alto Logowanie jednokrotne 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Wykonaj następujące czynności w oknie importowania

    ![Konfigurowanie Palo Alto Logowanie jednokrotne 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. W polu tekstowym **Profile Name** (Nazwa profilu) podaj nazwę np. Azure AD GlobalProtect.

    b. W polu **Identity Provider Metadata** (Metadane dostawcy tożsamości) kliknij przycisk **Przeglądaj** i wybierz plik metadata.xml, który został pobrany z witryny Azure Portal.

    c. Kliknij przycisk **OK** .

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks - GlobalProtect

W tej sekcji użytkownik o nazwie B. Simon został utworzony w Palo Alto Networks-GlobalProtect. Aplikacja Palo Alto Networks - GlobalProtect obsługuje aprowizację użytkowników just in time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Palo Alto Networks - GlobalProtect, zostanie utworzony po uwierzytelnieniu.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do Palo Alto Networks-GlobalProtect adres URL logowania, w którym można zainicjować przepływ logowania. 

2. Przejdź do adresu URL Palo Alto Networks-GlobalProtect Sign-on bezpośrednio i zainicjuj tam przepływ logowania.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka Palo Alto Networks-GlobalProtect w panelu dostępu należy automatycznie zalogować się do programu Palo Alto Networks-GlobalProtect, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Palo Alto Networks-GlobalProtect można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
