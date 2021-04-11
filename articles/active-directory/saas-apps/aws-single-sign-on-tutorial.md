---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z logowaniem jednokrotnym w usłudze AWS | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i logowanie jednokrotne w usłudze AWS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z logowaniem jednokrotnym w usłudze AWS

W tym samouczku dowiesz się, jak zintegrować Logowanie jednokrotne w usłudze AWS przy użyciu usługi Azure Active Directory (Azure AD). Gdy integrujesz Logowanie jednokrotne w usłudze AWS z usługą Azure AD, możesz:

* Kontrolka w usłudze Azure AD, która ma dostęp do rejestracji jednokrotnej AWS.
* Zezwól użytkownikom na automatyczne logowanie do AWS logowania jednokrotnego przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) AWS.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Logowanie jednokrotne w usłudze AWS obsługuje zainicjowanie logowania jednokrotnego przez usługę **SP i dostawcy tożsamości** .

* Logowanie jednokrotne w usłudze AWS obsługuje [**Automatyczne Inicjowanie obsługi użytkowników**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Dodawanie logowania jednokrotnego AWS z galerii

Aby skonfigurować integrację logowania jednokrotnego AWS w usłudze Azure AD, musisz dodać Logowanie jednokrotne AWS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AWS Logowanie jednokrotne** w polu wyszukiwania.
1. Wybierz pozycję **AWS Logowanie jednokrotne** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD na potrzeby logowania jednokrotnego w usłudze AWS

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze AWS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w usłudze AWS Logowanie jednokrotne.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą logowania jednokrotnego w usłudze AWS, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj logowanie](#configure-aws-single-sign-on-sso)** jednokrotne w usłudze AWS, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego logowania](#create-aws-single-sign-on-test-user)** jednokrotnego (AWS), aby uzyskać odpowiednika B. Simon w usłudze AWS logowania jednokrotnego, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **logowania** jednokrotnego AWS Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli masz **plik metadanych dostawcy usług**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij pozycję **logo folderu** , aby wybrać plik metadanych, który został pobrany z sekcji Konfigurowanie logowania jednokrotnego **rejestracji AWS** (punkt 8), a następnie kliknij przycisk **Dodaj**.

    ![image2](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych wartości **identyfikatorów** i **adresów URL odpowiedzi** są automatycznie wypełniane w sekcji Podstawowa konfiguracja SAML:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Jeśli wartości **Identyfikator** i **Adres URL odpowiedzi** nie zostaną automatycznie wypełnione, wpisz te wartości ręcznie zgodnie z wymaganiami.

1. Jeśli nie masz **pliku metadanych dostawcy usług**, wykonaj następujące kroki w sekcji **Podstawowa konfiguracja języka SAML** , jeśli chcesz skonfigurować aplikację w trybie inicjowania **dostawcy tożsamości** , wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta rejestracji](mailto:aws-sso-partners@amazon.com) jednokrotnej AWS, aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Aplikacja logowania jednokrotnego AWS oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/edit-attribute.png)


    > [!NOTE]
    > Jeśli ABAC jest włączona w usłudze AWS SSO, dodatkowe atrybuty mogą być przesyłane jako Tagi sesji bezpośrednio do kont AWS.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie logowania** jednokrotnego AWS skopiuj odpowiednie adresy URL na podstawie wymagania.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do logowania jednokrotnego AWS.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **AWS Logowanie jednokrotne**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-aws-single-sign-on-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze AWS

1. Aby zautomatyzować konfigurację w ramach logowania jednokrotnego AWS, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Skonfiguruj logowanie** jednokrotne, aby przekierować użytkownika do aplikacji logowania jednokrotnego (AWS). Z tego miejsca podaj poświadczenia administratora, aby zalogować się do AWS logowania jednokrotnego. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-10.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować AWS Logowanie jednokrotne, w innym oknie przeglądarki sieci Web Zaloguj się do swojej witryny firmowej logowania jednokrotnego (AWS) jako administrator.

1. Przejdź do **usług — > zabezpieczenia, tożsamość & zgodność — > rejestracji jednokrotnej**.
2. W okienku nawigacji po lewej stronie wybierz pozycję **Ustawienia**.
3. Na stronie **Ustawienia** Znajdź **Źródło tożsamości** i kliknij pozycję **Zmień**.

    ![Zrzut ekranu przedstawiający usługę zmiany źródła tożsamości](./media/aws-single-sign-on-tutorial/settings.png)

4. W polu Źródło zmiany tożsamości wybierz pozycję **zewnętrzny dostawca tożsamości**.

    
    ![Zrzut ekranu przedstawiający sekcję Wybieranie zewnętrznego dostawcy tożsamości](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Wykonaj poniższe czynności w sekcji **Konfigurowanie dostawcy tożsamości zewnętrznej** :

    ![Zrzut ekranu przedstawiający sekcję Pobieranie i przekazywanie metadanych](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. W sekcji **metadane dostawcy usług** Znajdź **metadane SAML AWS logowania jednokrotnego** i wybierz pozycję **Pobierz plik metadanych** , aby pobrać plik metadanych, i Zapisz go na komputerze i Użyj tego pliku metadanych do przekazywania danych na Azure Portal.

    b. Skopiuj wartość **adresu URL logowania JEDNOkrotnego AWS** , wklej tę wartość do pola tekstowego **adres URL logowania** w **sekcji Podstawowa konfiguracja SAML** w Azure Portal.

    c. W sekcji **metadane dostawcy tożsamości** wybierz pozycję **Przeglądaj** , aby przekazać plik metadanych pobrany z Azure Portal.

    d. Wybierz kolejno pozycje **Dalej: przegląd**.

8. W polu tekstowym wpisz **Zaakceptuj** , aby zmienić źródło tożsamości.

    ![Zrzut ekranu przedstawiający potwierdzenie konfiguracji](./media/aws-single-sign-on-tutorial/accept.png)

9. Kliknij pozycję **Zmień źródło tożsamości**.

### <a name="create-aws-single-sign-on-test-user"></a>Utwórz użytkownika testowego logowania jednokrotnego AWS

1. Otwórz **konsolę rejestracji jednokrotnej AWS**.

2. W okienku nawigacji po lewej stronie wybierz pozycję **Użytkownicy**.

3. Na stronie użytkownicy wybierz pozycję **Dodaj użytkownika**.

4. Na stronie Dodawanie użytkownika wykonaj następujące kroki:

    a. W polu **Nazwa użytkownika** wpisz B. Simon.

    b. W polu **adres e-mail** wprowadź wartość `username@companydomain.extension` . Na przykład `B.Simon@contoso.com`.

    c. W polu **Potwierdź adres e-mail** wprowadź ponownie adres e-mail z poprzedniego kroku.

    d. W polu imię wprowadź wartość `Jane` .

    e. W polu nazwisko wprowadź wartość `Doe` .

    f. W polu Nazwa wyświetlana wprowadź `Jane Doe` .

    przykład Wybierz kolejno pozycje **Dalej: grupy**.

    > [!NOTE]
    > Upewnij się, że nazwa użytkownika wprowadzona w AWS rejestracji jednokrotnej jest zgodna z nazwą logowania użytkownika w usłudze Azure AD. Pozwoli to uniknąć problemów z uwierzytelnianiem.

5. Wybierz pozycję **Dodaj użytkownika**.
6. Następnie użytkownik zostanie przypisany do konta AWS. Aby to zrobić, w okienku nawigacji po lewej stronie AWS rejestracji jednokrotnej wybierz pozycję **konta AWS**.
7. Na stronie konta AWS wybierz kartę Organizacja AWS, zaznacz pole wyboru obok konta AWS, które chcesz przypisać do użytkownika. Następnie wybierz pozycję **Przypisz użytkowników**.
8. Na stronie przypisywanie użytkowników Znajdź i zaznacz pole wyboru obok użytkownika B. Simon. Następnie wybierz kolejno pozycje **Dalej: zestawy uprawnień**.
9. W sekcji Wybierz zestawy uprawnień zaznacz pole wyboru obok zestawu uprawnień, który ma zostać przypisany do użytkownika B. Simon. Jeśli nie masz istniejącego zestawu uprawnień, wybierz opcję **Utwórz nowy zestaw uprawnień**.

    > [!NOTE]
    > Zestawy uprawnień definiują poziom dostępu użytkowników i grup do konta AWS. Aby dowiedzieć się więcej na temat zestawów uprawnień, zobacz stronę **zestawy uprawnień** SSO AWS.
10. Wybierz pozycję **Zakończ**.

> [!NOTE]
> Logowanie jednokrotne w usłudze AWS obsługuje również automatyczne Inicjowanie obsługi użytkowników. więcej informacji można znaleźć w [tym miejscu](./aws-single-sign-on-provisioning-tutorial.md) w artykule Jak skonfigurować automatyczne Inicjowanie obsługi użytkowników.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do AWS adresu URL logowania jednokrotnego, w którym można zainicjować przepływ logowania.  

* Przejdź do adresu URL logowania jednokrotnego w usłudze AWS bezpośrednio i zainicjuj tam przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do logowania jednokrotnego AWS, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Logowanie jednokrotne w usłudze AWS w obszarze Moje aplikacje, jeśli zostało skonfigurowane w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do logowania jednokrotnego AWS, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu logowania jednokrotnego AWS można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).