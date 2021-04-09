---
title: 'Samouczek: Azure Active Directory integrację z logowaniem jednokrotnym SAML dla BitBucket za pomocą rozwiązania GmbH | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH.
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
ms.openlocfilehash: aa5e3e88ceb957728799f27482de7477ba6b7b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98621250"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Samouczek: integracja Azure Active Directory z logowaniem jednokrotnym SAML dla BitBucket przez rozwiązanie GmbH

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne SAML dla BitBucket za pomocą rozwiązania GmbH z Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne SAML dla BitBucket za pomocą rozwiązania GmbH z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp toSAML SSO dla BitBucket przy użyciu rozwiązania GmbH.
* Zezwól użytkownikom na automatyczne logowanie się w usłudze toSAML SSO dla BitBucket przez rozwiązanie GmbH przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Do skonfigurowania integracji z usługą Azure AD w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa logowania jednokrotnego dla aplikacji Bitbucket firmy resolution GmbH obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług i dostawcę tożsamości**
* Usługa logowania jednokrotnego dla aplikacji Bitbucket firmy resolution GmbH obsługuje aprowizowanie użytkowników typu **Just In Time**


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Dodaj Logowanie jednokrotne SAML dla BitBucket za pomocą rozdzielczości GmbH z galerii

Aby skonfigurować integrację usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z usługą Azure AD, musisz dodać usługę logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz pozycję **Azure Active Directory**.
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz polecenie **SSO logowania jednokrotnego w usłudze BitBucket przez rozwiązanie GmbH** w polu wyszukiwania.
1. Wybierz pozycję **SAML SSO dla usługi BitBucket, podając** wyniki z wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla logowania jednokrotnego SAML dla BitBucket za pomocą rozwiązania GmbH

Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą logowania jednokrotnego SAML dla BitBucket za pomocą rozdzielczości GmbH przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze SAML SSO dla BitBucket za pomocą rozwiązania GmbH.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD z logowaniem jednokrotnym SAML dla BitBucket za pomocą rozwiązania GmbH, wykonaj następujące czynności:


1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne SAML dla BitBucket za pomocą rozdzielczości GmbH Logowanie jednokrotne](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** — aby mieć w usłudze logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączasz Logowanie jednokrotne usługi Azure AD w Azure Portal.
 
1. W Azure Portal na stronie " **Logowanie jednokrotne w usłudze SAML dla BitBucket za pomocą rozwiązania GmbH** Application Integration" Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz pojedynczą Sign-On metodę** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczej Sign-On przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:


    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :
    
    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej dla klientów usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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
1. Na liście Aplikacje wybierz pozycję **SAML SSO dla BitBucket za pomocą rozdzielczości GmbH**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. Następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Konfigurowanie logowania jednokrotnego SAML dla BitBucket za pomocą rozdzielczości GmbH Logowanie jednokrotne

1. Zaloguj się do firmowej witryny usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH jako administrator.

2. Z prawej strony głównego paska narzędzi kliknij pozycję **Ustawienia**.

3. Przejdź do sekcji KONTA i kliknij pozycję **SAML SingleSignOn**.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Na stronie **konfiguracji wtyczki SIngleSignOn języka SAML** kliknij pozycję **Dodaj dostawcę tożsamości**. 

    ![Dodawanie dostawcy tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Na stronie **wybierania dostawcy tożsamości języka SAML** wykonaj następujące kroki:

    ![Dostawca tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. W obszarze **Typ dostawcy tożsamości** ustaw wartość **AZURE AD**.

    b. W polu tekstowym **Nazwa** wpisz nazwę.

    c. W polu tekstowym **Opis** wpisz opis.

    d. Kliknij przycisk **Dalej**.

6. Na **stronie konfiguracji dostawcy tożsamości** kliknij przycisk **Dalej**.

    ![Konfiguracja tożsamości](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Na stronie **importowania metadanych dostawcy tożsamości języka SAML** kliknij pozycję **Załaduj plik**, aby przekazać plik **METADATA XML** pobrany z witryny Azure Portal.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Kliknij przycisk **Dalej**.

9. Kliknij pozycję **Zapisz ustawienia**.

    ![Zapisywanie](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Tworzenie użytkownika testowego usługi logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH

Celem tej sekcji jest utworzenie użytkownika o nazwie Britta Simon w obrębie funkcji logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH. Logowanie jednokrotne SAML dla aplikacji Bitbucket firmy resolution GmbH obsługuje aprowizację typu just in time, a użytkowników można tworzyć ręcznie. Skontaktuj się z [zespołem pomocy technicznej dotyczącej logowania jednokrotnego SAML dla aplikacji Bitbucket firmy resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) w sprawie swoich wymagań.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do logowania jednokrotnego protokołu SAML dla BitBucket za pomocą rozdzielczości GmbH adres URL, gdzie można zainicjować przepływ logowania.  

* Przejdź do logowania jednokrotnego SAML dla usługi BitBucket, podając w adresie URL Logowanie do usługi GmbH, i zainicjuj w tym miejscu przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i zaloguj się do logowania jednokrotnego SAML dla BitBucket za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne.

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka SSO protokołu SAML dla usługi BitBucket za pomocą rozwiązania GmbH w obszarze Moje aplikacje, jeśli jest skonfigurowana w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli jest skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do rejestracji jednokrotnej protokołu SAML dla BitBucket za pomocą rozwiązania GmbH, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego SAML dla BitBucket za pomocą rozdzielczości GmbH można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).