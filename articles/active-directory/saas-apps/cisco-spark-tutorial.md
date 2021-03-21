---
title: 'Samouczek: Azure Active Directory Integracja z logowaniem jednokrotnym (SSO) przy użyciu rozwiązania Cisco WebEx | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Cisco Webex.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: ad6d5308638b112afe2b51c4e149f876651e429d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592527"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Cisco WebEx

W tym samouczku dowiesz się, jak zintegrować Cisco WebEx z usługą Azure Active Directory (Azure AD). W przypadku integrowania programu Cisco WebEx z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do firmy Cisco WebEx.
* Zezwól użytkownikom na automatyczne logowanie do firmy Cisco WebEx przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) Cisco WebEx.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Cisco WebEx obsługuje logowanie jednokrotne zainicjowane przez usługę **SP** .
* Cisco WebEx obsługuje [**Automatyczne Inicjowanie obsługi użytkowników**](./cisco-webex-provisioning-tutorial.md).

## <a name="adding-cisco-webex-from-the-gallery"></a>Dodawanie aplikacji Cisco Webex z galerii

Aby skonfigurować integrację aplikacji Cisco Webex z usługą Azure AD, musisz dodać aplikację Cisco Webex z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wpisz ciąg **Cisco WebEx** .
1. Wybierz pozycję **Cisco WebEx** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Cisco WebEx

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco WebEx przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Cisco WebEx.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Cisco WebEx, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj Cisco WebEx](#configure-cisco-webex)** , aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Cisco WebEx](#create-cisco-webex-test-user)** , aby miał odpowiednik B. Simon w Cisco WebEx połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **Cisco WebEx** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** kliknij ikonę Edytuj/pióra, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** przekaż pobrany plik **metadanych dostawcy usług** i skonfiguruj aplikację, wykonując następujące kroki:

    >[!Note]
    >Plik metadanych dostawcy usług zostanie pobrany z sekcji **Konfigurowanie Cisco WebEx** , która została omówiona w dalszej części tego samouczka. 

    a. Kliknij pozycję **Przekaż plik metadanych**.

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    c. Po pomyślnym ukończeniu przekazywania pliku metadanych dostawcy usług wartości **Identyfikator** i **Adres URL odpowiedzi** w sekcji **Podstawowa konfiguracja protokołu SAML** zostaną wypełnione automatycznie:

    W polu tekstowym **adres URL logowania** wklej wartość **adresu URL odpowiedzi**, który zostanie wypełniony autowypełniany przez przekazywanie plików metadanych Sp.

1. Aplikacja Cisco Webex oczekuje asercji SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenów języka SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![image (obraz)](common/default-attributes.png)

1. Oprócz powyższych, aplikacja Cisco WebEx oczekuje kilku atrybutów do przekazania z powrotem w odpowiedzi SAML, które przedstawiono poniżej. Te atrybuty są również wstępnie wypełnione, ale można je sprawdzić zgodnie z wymaganiami.
  
    | Nazwa |  Atrybut źródłowy|
    | ---------------|--------- |
    | Identyfikator UID | user.userprincipalname |

    > [!NOTE]
    >  Wartość atrybutu źródłowego jest domyślnie mapowana na userpricipalname. Tę zmianę można zmienić na wartość User. mail lub User. onpremiseuserprincipalname lub dowolne inne wartości zgodnie z ustawieniem w WebEx.


1. Na stronie **Konfiguruj pojedyncze Sign-On za pomocą elementu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do programu Cisco WebEx.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Cisco Webex**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-cisco-webex"></a>Konfigurowanie rozwiązania Cisco WebEx

1. Zaloguj się do programu Cisco WebEx przy użyciu poświadczeń administratora.

1. Wybierz pozycję **Ustawienia organizacji** i w sekcji **uwierzytelnianie** kliknij przycisk **Modyfikuj**.

    ![Zrzut ekranu przedstawia ustawienia uwierzytelniania, które umożliwiają wybranie opcji Modyfikuj.](./media/cisco-spark-tutorial/organization-settings.png)
  
1. Wybierz pozycję **Zintegruj dostawcę tożsamości innej firmy. (Zaawansowane)** i kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia integrację dostawcy tożsamości innej firmy.](./media/cisco-spark-tutorial/enterprise-settings.png)

1. Kliknij pozycję **Pobierz plik metadanych** , aby pobrać **plik metadanych dostawcy usług** i zapisać go na komputerze, a następnie kliknij przycisk **dalej**.

    ![Zrzut ekranu przedstawia plik metadanych dostawcy usług.](./media/cisco-spark-tutorial/sp-metadata.png)

1. Kliknij opcję **przeglądarka plików** , aby zlokalizować i przekazać plik metadanych usługi Azure AD. Następnie zaznacz opcję **Require certificate signed by a certificate authority in Metadata (more secure)** (Wymagaj certyfikatu podpisanego przez urząd certyfikacji w metadanych (bezpieczniejsze)) i kliknij przycisk **Next** (Dalej).

    ![Zrzut ekranu przedstawia stronę metadanych importu I d P.](./media/cisco-spark-tutorial/idp-metadata.png)

1. Wybierz pozycję **Test SSO Connection** (Testuj połączenie logowania jednokrotnego) i gdy zostanie otwarta nowa karta przeglądarki, uwierzytelnij się przy użyciu usługi Azure AD, logując się.

1. Wróć do karty programu **Cisco Cloud Collaboration Management** Browser. Jeśli test zakończył się pomyślnie, zaznacz **ten test zakończył się pomyślnie. Włącz opcję Single Sign-On** i kliknij przycisk **dalej**.

1. Kliknij pozycję **Zapisz**.

> [!NOTE]
> Aby dowiedzieć się więcej o konfigurowaniu programu Cisco WebEx, zapoznaj się z [tą](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog) stroną.

### <a name="create-cisco-webex-test-user"></a>Tworzenie użytkownika testowego aplikacji Cisco Webex

W tej sekcji użytkownik o nazwie B. Simon został utworzony w programie Cisco WebEx. Ta aplikacja obsługuje automatyczne Inicjowanie obsługi użytkowników, która umożliwia automatyczne Inicjowanie obsługi i cofanie aprowizacji na podstawie reguł firmy.  Firma Microsoft zaleca używanie automatycznej aprowizacji, gdy jest to możliwe. Zobacz, jak włączyć funkcję autoaprowizacji dla programu [Cisco WebEx](./cisco-webex-provisioning-tutorial.md).

Jeśli musisz ręcznie utworzyć użytkownika, wykonaj następujące czynności:

1. Zaloguj się do programu Cisco WebEx przy użyciu poświadczeń administratora.

2. Kliknij pozycję **Users** (Użytkownicy), a następnie pozycję **Manage Users** (Zarządzaj użytkownikami).
   
    ![Zrzut ekranu przedstawia stronę użytkownicy, na której można zarządzać użytkownikami.](./media/cisco-spark-tutorial/user-1.png) 

3. W oknie **Zarządzanie użytkownikami** wybierz pozycję **ręcznie Dodaj lub Modyfikuj użytkowników**.

    ![Zrzut ekranu przedstawia stronę użytkownicy, na której można zarządzać użytkownikami i wybrać opcję Ręcznie Dodaj lub Modyfikuj użytkowników.](./media/cisco-spark-tutorial/user-2.png)

4. Wybierz pozycję **Names and Email address** (Nazwy i adres e-mail). Wypełnij pola tekstowe w następujący sposób:

    ![Zrzut ekranu przedstawia okno dialogowe Zarządzanie użytkownikami, w którym można ręcznie dodawać lub modyfikować użytkowników.](./media/cisco-spark-tutorial/user-3.png) 

    a. W polu tekstowym **imię i nazwisko** wpisz imię użytkownika, na przykład **B**.

    b. W polu tekstowym **Last Name** (Nazwisko) wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W polu tekstowym **Email address** (Adres e-mail) wpisz adres e-mail użytkownika, na przykład b.simon@contoso.com.

5. Kliknij znak plus, aby dodać B. Simon. Następnie kliknij przycisk **Dalej**.

6. W oknie **Dodawanie usług dla użytkowników** kliknij przycisk **Dodaj użytkowników** , a następnie **Zakończ**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Cisco WebEx, w którym można zainicjować przepływ logowania. 

* Przejdź bezpośrednio do adresu URL logowania Cisco WebEx i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Kliknięcie kafelka Cisco WebEx w obszarze Moje aplikacje spowoduje przekierowanie do adresu URL logowania Cisco WebEx. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Cisco WebEx można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)