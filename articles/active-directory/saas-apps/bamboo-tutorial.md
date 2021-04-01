---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla Bamboo za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowanie jednokrotne SAML dla Bamboo za pomocą rozwiązania GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 8ec834d76692f78d1d7bc60ddbd4c73fe4adaede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646117"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla Bamboo przez rozwiązanie GmbH

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne SAML dla Bamboo za pomocą rozwiązania GmbH z Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne SAML dla Bamboo za pomocą rozwiązania GmbH z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do logowania jednokrotnego SAML dla Bamboo za pomocą rozwiązania GmbH.
* Zezwól użytkownikom na automatyczne logowanie do rejestracji jednokrotnej protokołu SAML dla Bamboo za pomocą rozwiązania GmbH z kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z logowaniem jednokrotnym SAML dla Bamboo za pomocą rozwiązania GmbH, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Logowanie jednokrotne SAML dla usługi Bamboo przy użyciu rozdzielczości "Logowanie jednokrotne z włączoną obsługą"

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne SAML dla Bamboo przez rozwiązanie GmbH obsługuje usługę **SP i dostawcy tożsamości** zainicjowano Logowanie jednokrotne
* Logowanie jednokrotne SAML dla usługi Bamboo przez rozwiązanie GmbH obsługuje funkcję aprowizacji użytkowników **just in Time**

## <a name="add-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>Dodaj Logowanie jednokrotne SAML dla Bamboo za pomocą rozdzielczości GmbH z galerii

Aby skonfigurować integrację logowania jednokrotnego protokołu SAML dla usługi Bamboo za pomocą rozdzielczości GmbH w usłudze Azure AD, musisz dodać Logowanie jednokrotne SAML dla Bamboo przez rozwiązanie GmbH z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **SSO logowania jednokrotnego w usłudze Bamboo przez rozwiązanie GmbH** w polu wyszukiwania.
1. Wybierz pozycję **SAML SSO dla Bamboo za pomocą rozdzielczości GmbH** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego SAML dla Bamboo za pomocą rozwiązania GmbH

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą logowania jednokrotnego SAML dla Bamboo za pomocą rozdzielczości GmbH przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze SAML SSO dla Bamboo za pomocą rozwiązania GmbH.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z logowaniem jednokrotnym SAML dla Bamboo za pomocą rozwiązania GmbH, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
     1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
     1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne SAML dla Bamboo za pomocą rozdzielczości GmbH Logowanie jednokrotne](#configure-saml-sso-for-bamboo-by-resolution-gmbh-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz Logowanie jednokrotne SAML dla Bamboo za pomocą rozdzielczości GmbH test użytkownika](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** -aby dysponować odpowiednikiem Britta Simon w protokole SSO protokołu SAML dla Bamboo przez rozwiązanie GmbHby Solution GmbH, które jest połączone z reprezentacją usługi Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.
 
1. W Azure Portal na stronie " **Logowanie jednokrotne w usłudze SAML dla Bamboo za pomocą rozwiązania GmbH** Application Integration" Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)
4. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

     W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z użytkownikiem [SSO protokołu SAML dla usługi Bamboo](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. W sekcji **Konfigurowanie protokołu SAML SSO dla Bamboo według rozdzielczości GmbH** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** , a następnie wpisz hasło.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do logowania jednokrotnego w protokole SAML przez rozwiązanie GmbH.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAML SSO dla Bamboo za pomocą rozdzielczości GmbH**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-sso"></a>Konfigurowanie logowania jednokrotnego SAML dla Bamboo za pomocą rozdzielczości GmbH Logowanie jednokrotne

1. Zaloguj się do rejestracji jednokrotnej protokołu SAML dla Bamboo za pomocą rozdzielczości GmbH witryna firmy jako administrator.

1. Po prawej stronie głównego paska narzędzi kliknij pozycję **Ustawienia**  >  **Dodatki**.

    ![Ustawienia](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Przejdź do sekcji Zabezpieczenia, kliknij pozycję **SAML SingleSignon** na pasku menu.

    ![Samlsingle](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**.

    ![Dodawanie dostawcy tożsamości](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    c. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **Dalej**.

1. Na stronie **Konfiguracja dostawcy tożsamości** kliknij przycisk **dalej**.

    ![Konfiguracja tożsamości](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Na stronie **importowania metadanych dostawcy tożsamości języka SAML** kliknij pozycję **Załaduj plik**, aby przekazać plik **METADATA XML** pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Kliknij przycisk **Dalej**.

1. Kliknij pozycję **Zapisz ustawienia**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Utwórz Logowanie jednokrotne SAML dla Bamboo za pomocą rozdzielczości GmbH test User

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w protokole SSO protokołu SAML dla Bamboo za pomocą rozdzielczości GmbH. Logowanie jednokrotne SAML dla usługi Bamboo przez rozwiązanie GmbH obsługuje Inicjowanie obsługi just-in-Time, a także możliwość ręcznego tworzenia użytkowników, kontaktowanie się z [logowaniem jednokrotnym SAML dla usługi Bamboo przez rozwiązanie GmbH Client Support Team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) zgodnie z wymaganiami.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do logowania jednokrotnego protokołu SAML dla Bamboo za pomocą rozdzielczości GmbH adres URL, gdzie można zainicjować przepływ logowania.  

* Przejdź do logowania jednokrotnego SAML dla usługi Bamboo, podając w adresie URL Logowanie do usługi GmbH, i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i zaloguj się do logowania jednokrotnego SAML dla Bamboo za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne.

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SSO protokołu SAML dla usługi Bamboo za pomocą rozwiązania GmbH w obszarze Moje aplikacje, jeśli jest skonfigurowana w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do rejestracji jednokrotnej protokołu SAML dla Bamboo za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego SAML dla Bamboo za pomocą rozdzielczości GmbH można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).