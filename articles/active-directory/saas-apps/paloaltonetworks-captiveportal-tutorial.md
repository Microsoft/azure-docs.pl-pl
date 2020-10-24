---
title: 'Samouczek: integracja Azure Active Directory z portalem programu Palo Alto Networks Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a aplikacją Palo Alto Networks Captive Portal.
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
ms.openlocfilehash: 66e363b43bf9028e3075efa0f5a1f54e6bda190d
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92512698"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Samouczek: integracja Azure Active Directory z portalem programu Palo Alto Networks

Z tego samouczka dowiesz się, jak zintegrować aplikację Palo Alto Networks Captive Portal z usługą Azure Active Directory (Azure AD).
Integracja portalu Palo Alto Networks z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do portalu Palo Alto Networks.
* Możesz pozwolić użytkownikom na automatyczne logowanie do portalu Palo Alto sieci (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Do zintegrowania usługi Azure AD z aplikacją Palo Alto Networks Captive Portal potrzebne są następujące elementy:

* Subskrypcja usługi Azure Active Directory. Jeśli nie masz usługi Azure AD, możesz skorzystać z [miesięcznej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja aplikacji Palo Alto Networks Captive Portal z obsługą logowania jednokrotnego.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Portal Palo Alto Networks obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne
* Portal Palo Alto Networks jest obsługiwany przez funkcję aprowizacji użytkowników **just in Time**

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Dodawanie portalu Palo Alto Networks z galerii

Aby skonfigurować integrację portalu Palo Alto Networks z usługą Azure AD, musisz dodać portal Microsoft Palo Alto Networks z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Palo Alto Networks Portal** w polu wyszukiwania.
1. Wybierz kolejno opcje **Palo Alto Networks Portal** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Ta sekcja umożliwia skonfigurowanie i przetestowanie logowania jednokrotnego usługi Azure AD za pomocą portalu Palo Alto Networks w oparciu o użytkownika testowego o nazwie **B. Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w portalu niestandardowym Palo Alto Networks.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą portalu Palo Alto Networks, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-sso)** — umożliwia użytkownikowi korzystanie z tej funkcji.
    * **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — Przetestuj Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika B. Simon.
    * **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — Skonfiguruj usługę B. Simon, aby korzystać z logowania jednokrotnego usługi Azure AD.
2. **[Konfiguruj Palo Alto Networks — logowanie JEDNOkrotne](#configure-palo-alto-networks-captive-portal-sso)** — Skonfiguruj ustawienia logowania jednokrotnego w aplikacji.
    * **[Utwórz użytkownika testowego portalu Palo Alto Networks](#create-a-palo-alto-networks-captive-portal-test-user)** , aby dysponować odpowiednikiem B. Simon w portalu internetowym Palo Alto sieci, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
3. **[Przetestuj Logowanie jednokrotne](#test-sso)** — Sprawdź, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **portalu Palo Alto sieci** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W okienku **podstawowe konfiguracje języka SAML** wykonaj następujące czynności:

   1. W polu **Identyfikator** wprowadź adres URL ze wzorcem `https://<customer_firewall_host_name>/SAML20/SP`.

   2. W polu **Adres URL odpowiedzi** wprowadź adres URL ze wzorcem `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > W tym kroku zaktualizuj wartości symboli zastępczych przy użyciu rzeczywistego identyfikatora i adresów URL odpowiedzi. Aby uzyskać te rzeczywiste wartości, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. W sekcji **Certyfikat podpisywania SAML** obok pola **Kod XML metadanych federacji** wybierz pozycję **Pobierz**. Zapisz pobrany plik na komputerze.

    ![Link pobierania w obszarze kodu XML metadanych federacji](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do portalu w sieci Palo Alto.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Palo Alto Networks Portal**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Konfigurowanie logowania jednokrotnego w portalu Palo Alto

Następnie skonfiguruj logowanie jednokrotne w aplikacji Palo Alto Networks Captive Portal:

1. W innym oknie przeglądarki zaloguj się w witrynie internetowej aplikacji Palo Alto Networks jako administrator.

2. Wybierz kartę **Device** (Urządzenie).

    ![Karta Device (Urządzenie) w aplikacji Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. W menu wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML), a następnie wybierz przycisk **Import** (Importuj).

    ![Przycisk Import (Importuj)](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. W oknie dialogowym **SAML Identity Provider Server Profile Import** (Importowanie profilu serwera dostawcy tożsamości SAML) wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego w aplikacji Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. W polu **Profile Name** (Nazwa profilu) wprowadź nazwę, taką jak **AzureAD-CaptivePortal**.
    
    2. Obok pola **Identity Provider Metadata** (Metadane dostawcy tożsamości) wybierz przycisk **Browse** (Przeglądaj). Wybierz plik metadata.xml pobrany w witrynie Azure Portal.
    
    3. Wybierz przycisk **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Tworzenie użytkownika testowego aplikacji Palo Alto Networks Captive Portal

Następnie utwórz użytkownika o nazwie *Britta Simon* w aplikacji Palo Alto Networks Captive Portal. Aplikacja Palo Alto Networks Captive Portal obsługuje aprowizację użytkowników typu just in time, która jest domyślnie włączona. W tej sekcji nie trzeba wykonywać żadnych zadań. Jeśli użytkownik jeszcze nie istnieje w aplikacji Palo Alto Networks Captive Portal, zostanie utworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do portalu Palo Alto Networks, dla którego skonfigurowano Logowanie jednokrotne

Możesz użyć panelu programu Microsoft Access. Po kliknięciu kafelka portal usługi Microsoft Palo Alto Networks w panelu dostępu należy automatycznie zalogować się do portalu usługi w sieci Palo Alto, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu portalu Palo Alto Networks można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).