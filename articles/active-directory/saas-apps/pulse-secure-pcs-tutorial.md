---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO, Single Sign-on) z usługą Pulse Secure komputery | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory a bezpiecznymi komputerami.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: c46565f7eaa060a060183cf321bb1df1903b945c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91000902"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Samouczek Azure Active Directory: integracja z logowaniem jednokrotnym (SSO) przy użyciu programu Pulse Secure PC

W tym samouczku dowiesz się, jak zintegrować bezpieczne komputery z usługą Azure Active Directory (Azure AD). Gdy integrujesz pulsy z bezpiecznymi komputerami z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do pulsu bezpiecznych komputerów.
* Zezwól użytkownikom na automatyczne logowanie się, aby pulsować bezpieczne komputery z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) bezpiecznego komputerów.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Pulse Secure komputery obsługują usługę **SP** zainicjowaną przez usługę SSO

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Dodawanie pulsu Secure komputery z galerii

Aby skonfigurować integrację komputerów z usługą Azure AD, należy dodać Pulse bezpieczne komputery z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **Pulse Secure komputery** w polu wyszukiwania.
1. Wybierz pozycję **Pulse bezpieczne komputery** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla pulsu Secure PC

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą pulsu Secure PC przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze Pulse Secure PC.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą pulsu Secure komputery, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Pulse logowanie JEDNOkrotne bezpiecznego komputerów](#configure-pulse-secure-pcs-sso)** — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    1. **[Tworzenie pulsu użytkownika testowego](#create-pulse-secure-pcs-test-user)** o wysokiej wydajności, aby dysponować odpowiednikiem B. Simon w pulsie zabezpieczonych komputerów, które są połączone z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Pulse bezpieczne komputery** z integracją aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, adresu URL odpowiedzi i identyfikatora. Aby uzyskać te wartości, należy skontaktować się z [zespołem pomocy technicznej bezpiecznego komputery klienckie](mailto:support@pulsesecure.net) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie pulsu bezpiecznego komputerów** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do komputerów z bezpiecznymi komputerami.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Pulse bezpieczne komputery**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-pulse-secure-pcs-sso"></a>Konfigurowanie pulsu bezpieczne logowanie jednokrotne komputerów

W tej sekcji omówiono konfiguracje protokołu SAML wymagane do skonfigurowania komputerów jako SAML SP. Inne konfiguracje podstawowe, takie jak tworzenie obszarów i ról, nie są objęte.

**Bezpieczne konfiguracje połączeń pulsu obejmują:**

* Konfigurowanie usługi Azure AD jako dostawcy metadanych SAML
* Konfigurowanie serwera uwierzytelniania SAML
* Przypisywanie do odpowiednich obszarów i ról

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy metadanych SAML

Wykonaj następujące czynności na poniższej stronie:

![Bezpieczna konfiguracja połączeń pulsu](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Zaloguj się do konsoli administratora pulsu Connect Secure
1. Przejdź do **konfiguracji systemu > — > SAML**
1. Kliknij pozycję **nowy Dostawca metadanych**
1. Podaj prawidłową nazwę w polu tekstowym **Nazwa**
1. Przekaż pobrany plik XML metadanych z Azure Portal do **pliku metadanych usługi Azure AD**.
1. Wybierz pozycję **Zaakceptuj niepodpisane metadane**
1. Wybierz role jako **dostawcę tożsamości**
1. Kliknij przycisk **Zapisz zmiany**.

#### <a name="steps-to-create-a-saml-auth-server"></a>Procedura tworzenia serwera uwierzytelniania SAML:

1. Przejdź do **uwierzytelniania — serwery uwierzytelniania >**
1. Wybierz pozycję **Nowy: serwer SAML** , a następnie kliknij pozycję **nowy serwer**

    ![Serwer pulsu z bezpiecznym połączeniem](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Na stronie Ustawienia wykonaj następujące czynności:

    ![Ustawienia pulsu Połącz bezpieczne serwera uwierzytelniania](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Podaj **nazwę serwera** w polu tekstowym.

    b. Wybierz pozycję **SAML w wersji 2,0** i **Tryb konfiguracji** jako **metadane**.

    c. Skopiuj wartość **Identyfikator połączonej jednostki** i wklej ją do pola **Identyfikator URL** w oknie dialogowym **podstawowe konfiguracje SAML** w Azure Portal.

    d. Wybierz pozycję wartość identyfikatora jednostki usługi Azure AD z **listy rozwijanej identyfikator jednostki dostawcy tożsamości**.

    e. Wybierz pozycję adres URL logowania do usługi Azure AD z **listy rozwijanej adres URL dostawcy tożsamości Single Sign-On**.

    f. **Wylogowanie jednokrotne** jest ustawieniem opcjonalnym. W przypadku wybrania tej opcji zostanie wyświetlony komunikat z prośbą o nowe uwierzytelnienie po wylogowaniu. Jeśli ta opcja nie jest zaznaczona i nie zamknięto przeglądarki, możesz ponownie nawiązać połączenie bez uwierzytelniania.

    przykład Wybierz **żądaną klasę kontekstu Authn** jako **hasło** i **metodę porównania** jako **dokładną**.

    h. Ustaw **ważność metadanych** w postaci liczby dni.
    
    i. Kliknij przycisk **Zapisz zmiany**

### <a name="create-pulse-secure-pcs-test-user"></a>Utwórz użytkownika testowego pulsu Secure PC

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w programie Pulse Secure komputery. Powspółpracuje z [zespołem pomocy technicznej bezpiecznego komputerów](mailto:support@pulsesecure.net) , aby dodać użytkowników na platformie Pulse Secure PC. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania za pomocą bezpiecznego komputerów, na którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do pozycji Pulse bezpieczne logowanie do adresu URL na komputerze.

3. Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka Zabezpiecz komputery w panelu dostępu zostanie on przekierowany do adresu URL logowania za pomocą bezpiecznego komputerów. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu pulsu Secure komputery można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


