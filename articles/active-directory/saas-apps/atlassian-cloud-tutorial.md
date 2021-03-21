---
title: 'Samouczek: integracja Azure Active Directory z chmurą Atlassian | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Atlassian Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: fe83a012cac68f705bc9fabc7748f5a7c7c61bbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94330569"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Samouczek: integracja chmury Atlassian z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować chmurę Atlassian z usługą Azure Active Directory (Azure AD). W przypadku integracji chmury Atlassian z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do chmury Atlassian.
* Zezwól użytkownikom na automatyczne logowanie do usługi Atlassian Cloud przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz skorzystać z miesięcznej [bezpłatnej wersji](https://azure.microsoft.com/pricing/free-trial/)próbnej.
* Subskrypcja z włączonym logowaniem jednokrotnym w chmurze Atlassian.
* Aby włączyć logowanie jednokrotne SAML (Security Assertion Markup Language) dla produktów Atlassian Cloud, należy skonfigurować usługę Atlassian Access. Dowiedz się więcej o usłudze [Atlassian Access]( https://www.atlassian.com/enterprise/cloud/identity-manager).

> [!NOTE]
> Ta integracja jest również dostępna do użycia w środowisku chmury dla instytucji rządowych USA usługi Azure AD. Tę aplikację można znaleźć w galerii aplikacji w chmurze dla instytucji rządowych USA usługi Azure AD i skonfigurować ją w taki sam sposób, jak w przypadku chmury publicznej.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym. 

* Usługa Atlassian Cloud obsługuje logowanie jednokrotne inicjowane za pomocą **SP oraz IDP**
* Chmura Atlassian obsługuje [Automatyczne Inicjowanie obsługi i cofanie aprowizacji użytkowników](atlassian-cloud-provisioning-tutorial.md)

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Dodawanie usługi Atlassian Cloud z galerii

Aby skonfigurować integrację usługi Atlassian Cloud z usługą Azure AD, należy dodać usługę Atlassian Cloud z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Atlassian Cloud** w polu wyszukiwania.
1. Wybierz pozycję **Atlassian Cloud** w panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą chmury Atlassian przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w chmurze Atlassian.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą chmury Atlassian, wykonaj następujące czynności:

1. **[Skonfiguruj usługę Azure AD przy użyciu logowania jednokrotnego w chmurze Atlassian](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z logowania jednokrotnego w usłudze Azure AD za pomocą usługi Atlassian Cloud.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Utwórz użytkownika testowego w chmurze Atlassian](#create-atlassian-cloud-test-user)** , aby dysponować odpowiednikiem B. Simon w chmurze Atlassian, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Aby zautomatyzować konfigurację w chmurze Atlassian, musisz zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Konfiguruj Atlassian Cloud** kieruje Cię do aplikacji w chmurze Atlassian. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do chmury Atlassian. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować chmurę Atlassian, zaloguj się do witryny firmy Atlassian w chmurze jako administrator i wykonaj następujące czynności.

1. Przed rozpoczęciem przejdź do wystąpienia produktu Atlassian i skopiuj/Zapisz adres URL wystąpienia
   > [!NOTE]
   > adres URL powinien pasować do `https://<instancename>.atlassian.net` wzorca

   ![Nazwa wystąpienia](./media/atlassian-cloud-tutorial/get-atlassian-instance-name.png)

1. Otwórz [Portal administracyjny Atlassian](https://admin.atlassian.com/) i kliknij nazwę organizacji

   ![firmy](./media/atlassian-cloud-tutorial/click-on-organization-in-atlassian-access.png)

1. Musisz zweryfikować swoją domenę przed przejściem do konfigurowania logowania jednokrotnego. Aby uzyskać więcej informacji, zobacz dokument [Weryfikacja domeny Atlassian](https://confluence.atlassian.com/cloud/domain-verification-873871234.html).
1. Na ekranie portalu administracyjnego Atlassian wybierz pozycję **zabezpieczenia** z lewej szuflady

   ![security](./media/atlassian-cloud-tutorial/click-on-security-in-atlassian-access.png)

1. Na ekranie zabezpieczeń portalu administracyjnego Atlassian wybierz pozycję **SAML logowanie** jednokrotne z lewej szuflady

   ![Logowanie jednokrotne SAML](./media/atlassian-cloud-tutorial/click-on-saml-sso-in-atlassian-access-security.png)

1. Kliknij pozycję **Dodaj konfigurację SAML** i pozostaw otwartą stronę

   ![Dodaj konfigurację SAML](./media/atlassian-cloud-tutorial/saml-configuration-in-atlassian-access-security-saml-sso.png)

   ![Dodaj konfigurację SAML 2](./media/atlassian-cloud-tutorial/add-saml-configuration.png)

1. W Azure Portal na stronie integracja aplikacji w **chmurze Atlassian** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Skonfiguruj Logowanie jednokrotne**.

   ![Konfigurowanie logowania jednokrotnego](./media/atlassian-cloud-tutorial/set-up-sso.png)

1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.

   ![Protokół SAML na platformie Azure](./media/atlassian-cloud-tutorial/saml-in-azure.png)

1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** przewiń w dół, aby **skonfigurować chmurę Atlassian**
   
   a. Kliknij pozycję **adresy URL konfiguracji**

   ![adresy](./media/atlassian-cloud-tutorial/configuration-urls.png)
   
   b. Skopiuj wartość **identyfikatora usługi Azure AD** z Azure Portal, wklej ją w polu tekstowym **Identyfikator jednostki dostawcy tożsamości** w Atlassian
   
   c. Skopiuj wartość **adresu URL logowania** z Azure Portal, wklej ją w polu tekstowym **adres URL logowania jednokrotnego dostawcy tożsamości** w Atlassian

   ![Adres URL logowania jednokrotnego dostawcy tożsamości](./media/atlassian-cloud-tutorial/configuration-urls-azure.png)

   ![Identyfikator jednostki i SS](./media/atlassian-cloud-tutorial/entity-id-and-ss.png)

1. Na stronie **Konfigurowanie pojedynczego Sign-On za pomocą języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Certyfikat podpisywania](./media/atlassian-cloud-tutorial/certificate.png)

   ![Certyfikat 1](./media/atlassian-cloud-tutorial/certificate-1.png)

1. **Dodaj/Zapisz** konfigurację SAML w Atlassian

1. Aby skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , należy edytować sekcję **Podstawowa konfiguracja protokołu SAML** w obszarze **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** na platformie Azure i otworzyć **stronę logowania** jednokrotnego SAML w portalu administratora Atlassian

   a. Skopiuj wartość **identyfikatora jednostki Sp** z Atlassian, wklej ją w polu **Identyfikator (identyfikator jednostki)** na platformie Azure i ustaw ją jako domyślną
   
   b. Skopiuj wartość **adresu URL usługi konsumenta potwierdzenia Sp** z Atlassian, wklej ją w polu **adresu URL odpowiedzi (adres URL usługi konsumenckej potwierdzenia)** na platformie Azure i ustaw ją jako domyślną
   
   c. Skopiuj wartość **adresu URL wystąpienia** skopiowaną w kroku 1 i wklej ją w polu **Stan przekazywania** na platformie Azure

   ![Kopiuj adresy URL](./media/atlassian-cloud-tutorial/copy-urls.png)

   ![przycisk Edytuj](./media/atlassian-cloud-tutorial/edit-button.png)

   ![obraz adresów URL](./media/atlassian-cloud-tutorial/urls.png)
   
1. Jeśli chcesz skonfigurować aplikację w trybie zainicjowania programu **SP** , Edytuj podstawową sekcję **Konfiguracja protokołu SAML** w obszarze **Konfigurowanie pojedynczego Sign-On ze stroną SAML** na platformie Azure. Skopiuj **adres URL wystąpienia** (z kroku 1) i wklej go w polu **Zaloguj adres URL** na platformie Azure

   ![Edytuj przycisk w adresach URL](./media/atlassian-cloud-tutorial/edit-button.png)

   ![adres URL logowania](./media/atlassian-cloud-tutorial/sign-on-URL.png)
   
1. Aplikacja Atlassian Cloud oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu języka SAML. Mapowanie atrybutu można edytować, klikając ikonę **Edytuj** . 

   ![atrybuty](./media/atlassian-cloud-tutorial/edit-attribute.png)
   
   1. Mapowanie atrybutu dla dzierżawy usługi Azure AD z licencją Microsoft 365
      
      a. Kliknij pozycję **unikatowy identyfikator użytkownika (identyfikator nazwy)**

      ![atrybuty i oświadczenia](./media/atlassian-cloud-tutorial/user-attributes-and-claims.png)
      
      b. Chmura Atlassian oczekuje, że **NameIdentifier** (**unikatowy identyfikator użytkownika**) będzie mapowany na adres e-mail użytkownika (**User.email**). Edytuj **atrybut Source** i zmień go na **User. mail**. Zapisz zmiany w poroście.

      ![unikatowy identyfikator użytkownika](./media/atlassian-cloud-tutorial/unique-user-identifier.png)
      
      c. Ostateczne mapowania atrybutów powinny wyglądać następująco.

      ![obraz 2](./media/atlassian-cloud-tutorial/default-attributes-1.png)
      
   1. Mapowanie atrybutu dla dzierżawy usługi Azure AD bez licencji Microsoft 365 

      a. Kliknij tę pozycję `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` .

      ![obraz 3](./media/atlassian-cloud-tutorial/email-address.png)
         
      b. Podczas gdy platforma Azure nie wypełnia atrybutu **User. mail** dla użytkowników utworzonych w dzierżawach usługi Azure AD bez licencji Microsoft 365 i przechowuje wiadomość e-mail dla takich użytkowników w atrybucie **userPrincipalName** . Chmura Atlassian oczekuje, że **NameIdentifier** (**unikatowy identyfikator użytkownika**) będzie mapowany do wiadomości e-mail użytkownika (**User. userPrincipalName**).  Edytuj **atrybut Source**  i zmień go na **User. userPrincipalName**. Zapisz zmiany w poroście.

      ![Ustaw adres e-mail](./media/atlassian-cloud-tutorial/set-email.png)
         
      c. Ostateczne mapowania atrybutów powinny wyglądać następująco.

      ![obraz 4](./media/atlassian-cloud-tutorial/default-attributes-2.png)
     
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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do chmury Atlassian.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Atlassian Cloud**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="create-atlassian-cloud-test-user"></a>Tworzenie użytkownika testowego usługi Atlassian Cloud

Aby umożliwić użytkownikom usługi Azure AD logowanie do usługi Atlassian Cloud, aprowizuj ręcznie konta użytkowników w usłudze Atlassian Cloud, wykonując następujące czynności:

1. W okienku **Administracja** okienku wybierz pozycję **Użytkownicy**.

    ![Link Użytkownicy usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

1. Aby utworzyć użytkownika w usłudze Atlassian Cloud, wybierz pozycję **Zaproś użytkownika**.

    ![Tworzenie użytkownika usługi Atlassian Cloud](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

1. W polu **Adres e-mail** wprowadź adres e-mail użytkownika, a następnie przypisz dostęp do aplikacji.

    ![Użytkownik w chmurze Atlassian](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

1. Aby wysłać wiadomość e-mail z zaproszeniem do użytkownika, wybierz pozycję **Zaproś użytkowników**. Wiadomość e-mail z zaproszeniem zostanie wysłana do użytkownika, a po zaakceptowaniu zaproszenia użytkownik będzie aktywny w systemie.

> [!NOTE]
> Możesz również zbiorczo utworzyć użytkowników, wybierając przycisk **Tworzenie zbiorcze** w sekcji **Użytkownicy**.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania w chmurze Atlassian, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania w chmurze Atlassian i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do chmury Atlassian, dla której skonfigurowano Logowanie jednokrotne 

Możesz również użyć panelu dostępu programu Microsoft, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka Atlassian Cloud w panelu dostępu, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do chmury Atlassian, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu chmury Atlassian można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).