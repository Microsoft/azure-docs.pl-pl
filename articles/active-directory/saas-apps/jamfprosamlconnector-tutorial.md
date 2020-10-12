---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Jamf Pro | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem Jamf Pro.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.openlocfilehash: 780421d93916c7da7897dfa15d09dc895cf56280
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88552666"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym przy użyciu Jamf Pro

W tym samouczku dowiesz się, jak zintegrować usługę Jamf Pro z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Jamf Pro z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do usługi Jamf Pro.
* Automatyczne logowanie użytkowników do Jamf Pro z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Logowanie jednokrotne za pomocą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja usługi Jamf Pro, która jest włączona (Logowanie jednokrotne).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Usługa Jamf Pro obsługuje logowanie jednokrotne **zainicjowane przez usługę Sp** i **dostawcy tożsamości** .
* Po skonfigurowaniu programu Jamf Pro można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozszerzy od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Dodaj Jamf Pro z galerii

Aby skonfigurować integrację oprogramowania Jamf Pro z usługą Azure AD, musisz dodać oprogramowanie Jamf Pro z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft osobistych.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź *Jamf Pro* w polu wyszukiwania.
1. Wybierz pozycję **Jamf Pro** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Konfigurowanie i testowanie logowania jednokrotnego w usłudze Azure AD dla Jamf Pro

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Jamf Pro przy użyciu użytkownika testowego o nazwie B. Simon. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Jamf Pro.

W tej sekcji konfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą Jamf Pro.

1. [Skonfiguruj Logowanie jednokrotne w usłudze Azure AD](#configure-sso-in-azure-ad) , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. [Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) , aby przetestować Logowanie jednokrotne usługi Azure AD przy użyciu konta B. Simon.
    1. [Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) , tak aby usługa B. Simon mogła korzystać z logowania jednokrotnego w usłudze Azure AD.
1. [Skonfiguruj logowanie JEDNOkrotne w programie Jamf Pro](#configure-sso-in-jamf-pro) , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. [Utwórz użytkownika testowego Jamf Pro](#create-a-jamf-pro-test-user) , aby dysponować odpowiednikiem B. Simon w Jamf Pro, która jest połączona z reprezentacją usługi Azure AD.
1. [Przetestuj konfigurację logowania jednokrotnego](#test-the-sso-configuration) , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-sso-in-azure-ad"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Jamf Pro** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** wybierz ikonę pióra dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edytuj stronę Podstawowa konfiguracja protokołu SAML.](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie **inicjowanym** przy użyciu usługi dostawcy tożsamości, w sekcji **Podstawowa konfiguracja SAML** wpisz wartości następujących pól:

    a. W polu tekstowym **Identyfikator** wprowadź adres URL, który używa następującej formuły: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. W polu tekstowym **adres URL odpowiedzi** wprowadź adres URL, który używa następującej formuły: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Wybierz pozycję **Ustaw dodatkowe adresy URL**. Jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** , w polu tekstowym **adres URL logowania** wprowadź adres URL, który używa następującej formuły: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Rzeczywista wartość identyfikatora zostanie pobrana z sekcji **Logowanie** jednokrotne w portalu Jamf Pro, która została omówiona w dalszej części tego samouczka. Wartość rzeczywistej domeny podrzędnej można wyodrębnić z wartości identyfikatora i użyć jej jako adresu URL logowania i adresu URL odpowiedzi. Można również odwołać się do formuł przedstawionych w sekcji podstawowe informacje o **konfiguracji SAML** w Azure Portal.

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** przejdź do sekcji **certyfikat podpisywania SAML** , wybierz przycisk **Kopiuj** , aby skopiować **adres URL metadanych federacji aplikacji**, a następnie zapisz go na komputerze.

    ![Link pobierania certyfikatu podpisywania SAML](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.
   1. W polu **Nazwa użytkownika** wprowadź wartość [name] @ [formacie]. [rozszerzenie]. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji przyznano użytkownikowi B. Simon dostęp do Jamf Pro.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Jamf Pro**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Wybierz przycisk Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika. Następnie wybierz przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** wybierz przycisk **Przypisz** .

## <a name="configure-sso-in-jamf-pro"></a>Konfigurowanie logowania jednokrotnego w programie Jamf Pro

1. Aby zautomatyzować konfigurację w ramach Jamf Pro, zainstaluj **rozszerzenie moje aplikacje bezpieczne logowanie do przeglądarki** , wybierając pozycję **Zainstaluj rozszerzenie**.

    ![Strona rozszerzenia przeglądarki Moje aplikacje bezpieczne logowanie](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki wybierz pozycję **Konfiguruj Jamf Pro**. Po otwarciu aplikacji Jamf Pro podaj poświadczenia administratora, aby się zalogować. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki od 3 do 7.

    ![Strona konfiguracji Instalatora w programie Jamf Pro](common/setup-sso.png)

3. Aby ręcznie skonfigurować Jamf Pro, Otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Jamf Pro jako administrator. Następnie wykonaj poniższe czynności.

4. Wybierz **ikonę Ustawienia** w prawym górnym rogu strony.

    ![Wybierz ikonę ustawień w programie Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Wybierz pozycję **Logowanie jednokrotne**.

    ![Wybierz jedną Sign-On w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Na stronie **Logowanie** jednokrotne wykonaj następujące czynności.

    ![Strona pojedynczej Sign-On w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Kliknij pozycję **Edytuj**.

    b. Zaznacz pole wyboru **Włącz uwierzytelnianie pojedynczej Sign-On** .

  c. Wybierz pozycję **Azure** jako opcję z menu rozwijanego **dostawca tożsamości** .

  d. Skopiuj wartość **Identyfikator jednostki** i wklej ją do pola **Identyfikator (identyfikator jednostki)** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

> [!NOTE]
> Użyj wartości w polu, `<SUBDOMAIN>` Aby uzupełnić adres URL logowania i adres URL odpowiedzi w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

  e. Wybierz pozycję **adres URL metadanych** z menu rozwijanego **Źródło metadanych dostawcy tożsamości** . W wyświetlonym polu wklej wartość **adresu URL metadanych federacji aplikacji** skopiowaną z Azure Portal.

  f. Obowiązkowe Edytuj wartość wygaśnięcia tokenu lub wybierz opcję "Wyłącz wygaśnięcie tokenu SAML".

7. Na tej samej stronie przewiń w dół do sekcji **Mapowanie użytkownika** . Następnie wykonaj poniższe czynności.

    ![Sekcja mapowanie użytkownika na stronie pojedynczej Sign-On w programie Jamf Pro.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Wybierz opcję **NameID** dla **mapowania użytkownika dostawcy tożsamości**. Domyślnie ta opcja jest ustawiona na **NameID**, ale można zdefiniować atrybut niestandardowy.

    b. Wybierz pozycję **poczta e-mail** dla **mapowania użytkowników Jamf Pro**. Jamf Pro mapuje atrybuty SAML, wysyłane przez dostawcy tożsamości najpierw przez użytkowników, a następnie według grup. Gdy użytkownik próbuje uzyskać dostęp do programu Jamf Pro, Jamf Pro pobiera informacje o użytkowniku od dostawcy tożsamości i dopasowuje je do wszystkich kont użytkowników usługi Jamf Pro. Jeśli nie można znaleźć konta użytkownika przychodzącego, program Jamf Pro podejmie próbę dopasowania go do nazwy grupy.

    c. Wklej wartość `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` w polu **nazwa atrybutu grupy dostawcy tożsamości** .

    d. Na tej samej stronie przewiń w dół do sekcji **zabezpieczenia** i wybierz opcję **zezwól użytkownikom na pomijanie jednego Sign-On uwierzytelniania**. W związku z tym użytkownicy nie będą przekierowywani do strony logowania dostawcy tożsamości w celu uwierzytelnienia i mogą bezpośrednio logować się do usługi Jamf Pro. Gdy użytkownik spróbuje uzyskać dostęp do oprogramowania Jamf Pro za pośrednictwem dostawcy tożsamości, nastąpi uwierzytelnianie i autoryzacja za pomocą logowania jednokrotnego zainicjowanego przez dostawcę tożsamości.

    e. Wybierz pozycję **Zapisz**.

### <a name="create-a-jamf-pro-test-user"></a>Tworzenie użytkownika testowego Jamf Pro

Aby użytkownicy usługi Azure AD mogli logować się do programu Jamf Pro, muszą być obsługiwani w Jamf Pro. Inicjowanie obsługi w programie Jamf Pro to zadanie ręczne.

Aby zainicjować obsługę administracyjną konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Jamf Pro jako administrator.

2. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Wybierz pozycję **konta użytkowników Jamf Pro grupy &**.

    ![Ikona grup & kont użytkowników Jamf Pro w ustawieniach Jamf Pro](./media/jamfprosamlconnector-tutorial/user1.png)

4. Wybierz pozycję **Nowy**.

    ![Konto użytkownika Jamf Pro Strona Ustawienia systemu & grupy](./media/jamfprosamlconnector-tutorial/user2.png)

5. Wybierz pozycję **Utwórz standardowe konto**.

    ![Opcja Utwórz konto standardowe na stronie grupy & kont użytkowników Jamf Pro](./media/jamfprosamlconnector-tutorial/user3.png)

6. W oknie dialogowym **nowe konto** wykonaj następujące czynności:

    ![Nowe opcje konfiguracji konta w ustawieniach systemu Jamf Pro](./media/jamfprosamlconnector-tutorial/user4.png)

    a. W polu **Nazwa użytkownika** wprowadź `Britta Simon` pełną nazwę użytkownika testowego.

    b. Wybierz opcje **poziom dostępu**, **zestaw uprawnień**i **stan dostępu** , które są zgodne z Twoją organizacją.

    c. W polu **imię i nazwisko** wprowadź `Britta Simon` .

    d. W polu **adres e-mail** wprowadź adres E-mail konta Britta Simon.

    e. W polu **hasło** wprowadź hasło użytkownika.

    f. W polu **Weryfikuj hasło** ponownie wprowadź hasło użytkownika.

    przykład Wybierz pozycję **Zapisz**.

## <a name="test-the-sso-configuration"></a>Testowanie konfiguracji logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Jamf Pro w panelu dostępu należy automatycznie zalogować się do konta Jamf Pro, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Co to jest kontrola sesji w Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Wypróbuj usługę Jamf Pro z usługą Azure AD](https://aad.portal.azure.com/)