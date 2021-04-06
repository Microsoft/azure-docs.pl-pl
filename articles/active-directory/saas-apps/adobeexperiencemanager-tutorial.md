---
title: 'Samouczek: integracja Azure Active Directory z programem Adobe Experience Manager | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i programem Adobe Experience Manager.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653358"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Samouczek: integracja Azure Active Directory z programem Adobe Experience Manager

W tym samouczku dowiesz się, jak zintegrować program Adobe Experience Manager z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Adobe Experience Manager z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Adobe Experience Manager.
* Zezwól użytkownikom na automatyczne logowanie do programu Adobe Experience Manager przy użyciu swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w programie Adobe Experience Manager.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Adobe Experience Manager obsługuje jednokrotne logowanie inicjowane przez **dostawcę usług i dostawcę tożsamości**

* Adobe Experience Manager obsługuje aprowizację użytkowników typu **Just In Time**

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Dodawanie programu Adobe Experience Manager z galerii

Aby skonfigurować integrację programu Adobe Experience Manager z usługą Azure AD, musisz dodać program Adobe Experience Manager z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Adobe Experience Manager** .
1. Wybierz pozycję **Adobe Experience Manager** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Adobe Experience Manager

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Adobe Experience Manager przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Adobe Experience Manager.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Adobe Experience Manager, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
2. **[Skonfiguruj Logowanie jednokrotne programu Adobe Experience Manager](#configure-adobe-experience-manager-sso)** , aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego programu Adobe Experience Manager](#create-adobe-experience-manager-test-user)** — aby udostępnić w programie Adobe Experience Manager odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
6. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji programu **Adobe Experience Manager** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Identyfikator** wpisz unikatową wartość, która została również zdefiniowana na Twoim serwerze AEM.

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj wartość adresu URL odpowiedzi przy użyciu rzeczywistego adresu URL odpowiedzi. Aby uzyskać tę wartość, skontaktuj się z [zespołem pomocy technicznej dla klienta programu Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w trybie inicjowania programu **SP** :

    W polu tekstowym **Adres URL logowania** wpisz adres URL swojego serwera programu Adobe Experience Manager.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfiguracja programu Adobe Experience Manager** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Adobe Experience Manager.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Adobe Experience Manager**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-adobe-experience-manager-sso"></a>Konfigurowanie logowania jednokrotnego programu Adobe Experience Manager

1. W innym oknie przeglądarki otwórz portal administracyjny programu **Adobe Experience Manager**.

2. Wybierz pozycję **Ustawienia**  >    >  **Użytkownicy** zabezpieczeń.

    ![Zrzut ekranu przedstawiający kafelek użytkownicy w programie Adobe Experience Manager.](./media/adobe-experience-manager-tutorial/user-1.png)

3. Wybierz **administratora** lub dowolnego innego odpowiedniego użytkownika.

    ![Zrzut ekranu, który wyróżnia użytkownika Adminisrator.](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. Wybierz pozycję **Ustawienia konta**  >  **Zarządzaj TrustStore**.

    ![Zrzut ekranu przedstawiający zarządzanie TrustStore w obszarze Ustawienia konta.](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. W obszarze **Dodaj certyfikat z pliku CER** kliknij pozycję **Wybierz plik certyfikatu**. Wyszukaj i wybierz plik certyfikatu, który został już pobrany z witryny Azure Portal.

    ![Zrzut ekranu, który podświetla przycisk Wybierz plik certyfikatu.](./media/adobe-experience-manager-tutorial/user-2.png)

6. Certyfikat jest dodawany do magazynu TrustStore. Zapisz alias certyfikatu.

    ![Zrzut ekranu pokazujący, że certyfikat został dodany do TrustStore.](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. Na stronie **Użytkownicy** wybierz pozycję **authentication-service**.

    ![Sreenshot, który wyróżnia usługi uwierzytelniania na ekranie.](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. Wybierz pozycję **Ustawienia konta**  >  **Utwórz magazyn kluczy i Zarządzaj nim**. Utwórz magazyn kluczy, podając hasło.

    ![Zrzut ekranu przedstawiający Zarządzanie magazynem kluczy.](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. Wróć do ekranu administratora. Następnie wybierz kolejno pozycje **Ustawienia**  >    >  **Konsola sieci Web**.

    ![Zrzut ekranu, który wyróżnia konsolę sieci Web w obszarze operacje w sekcji Ustawienia.](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    Spowoduje to otwarcie strony konfiguracji.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. Znajdź pozycję **Program obsługi uwierzytelniania Adobe Granite SAML 2.0**. Następnie wybierz ikonę **Dodaj**.

    ![Zrzut ekranu przedstawiający procedurę obsługi uwierzytelniania w programie Adobe Granite SAML 2,0.](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. Na tej stronie wykonaj następujące czynności.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. W polu **Ścieżka** wprowadź **/**.

    b. W polu **Adres URL dostawcy tożsamości** wprowadź wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. W polu **Alias certyfikatu dostawcy tożsamości** wprowadź wartość **aliasu certyfikatu**, która została dodana w magazynie TrustStore.

    d. W polu **Identyfikator jednostki podanej w zabezpieczeniach** wprowadź wartość unikatowego **identyfikatora usługi Azure Ad**, która została skonfigurowana w witrynie Azure Portal.

    e. W polu **Adres URL usługi Assertion Consumer Service** wprowadź wartość **adresu URL odpowiedzi**, która została skonfigurowana w witrynie Azure Portal.

    f. W polu **Hasło magazynu kluczy** wprowadź **hasło** ustawione w magazynie kluczy.

    przykład W polu **Identyfikator atrybutu użytkownika** wprowadź **identyfikator nazwy** lub inny identyfikator użytkownika, który jest odpowiedni w Twoim przypadku.

    h. Wybierz pozycję **Automatycznie utwórz użytkowników CRX**.

    i. W polu **Adres URL wylogowywania** wprowadź wartość unikatowego **adresu URL wylogowywania**, która została uzyskana z witryny Azure Portal.

    j. Wybierz pozycję **Zapisz**.

### <a name="create-adobe-experience-manager-test-user"></a>Tworzenie użytkownika testowego programu Adobe Experience Manager

W tej sekcji utworzysz w programie Adobe Experience Manager użytkownika o nazwie Britta Simon. W przypadku wybrania opcji **Automatycznie utwórz użytkowników CRX** użytkownicy są tworzeni automatycznie po pomyślnym uwierzytelnieniu.

Aby ręcznie utworzyć użytkowników, należy skontaktować się z [zespołem pomocy technicznej programu Adobe Experience Manager](https://helpx.adobe.com/support/experience-manager.html) w celu dodania użytkowników na platformie programu Adobe Experience Manager.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

#### <a name="sp-initiated"></a>Zainicjowano SP:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania programu Adobe Experience Manager, w którym można zainicjować przepływ logowania.  

* Przejdź bezpośrednio do adresu URL logowania programu Adobe Experience Manager i zainicjuj w nim przepływ logowania.

#### <a name="idp-initiated"></a>DOSTAWCY tożsamości zainicjowane:

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal i należy automatycznie zalogować się do programu Adobe Experience Manager, dla którego skonfigurowano Logowanie jednokrotne 

Możesz również użyć aplikacji Microsoft my Apps, aby przetestować aplikację w dowolnym trybie. Po kliknięciu kafelka programu Adobe Experience Manager w obszarze Moje aplikacje, jeśli zostanie on skonfigurowany w trybie SP, nastąpi przekierowanie do strony logowania do aplikacji w celu zainicjowania przepływu logowania, a jeśli zostanie on skonfigurowany w trybie dostawcy tożsamości, należy automatycznie zalogować się do programu Adobe Experience Manager, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu programu Adobe Experience Manager można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).