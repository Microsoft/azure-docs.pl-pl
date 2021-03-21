---
title: 'Samouczek Azure Active Directory: integracja logowania jednokrotnego (SSO) z usługą Resource Central | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między usługami Azure Active Directory i Resource Central.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: dbc148fcbcd9c3be86a29df1e08755611a347b07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586630"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-resource-central"></a>Samouczek: integracja z logowaniem jednokrotnym (SSO) Azure Active Directory przy użyciu Centrum zasobów

W tym samouczku dowiesz się, jak zintegrować usługę Resource Central z Azure Active Directory (Azure AD). Podczas integrowania zasobów centralnych z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do centrum zasobów.
* Zezwól użytkownikom na automatyczne logowanie do zasobów centralnych przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) zasobów.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Resource Central obsługuje logowanie jednokrotne w usłudze **SP**

* Centrum zasobów jest obsługiwane przez funkcję aprowizacji użytkowników **just in Time**

## <a name="add-resource-central-from-the-gallery"></a>Dodawanie zasobów centralnych z galerii

Aby skonfigurować integrację zasobów centralnych z usługą Azure AD, należy dodać centrum zasobów z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** w polu wyszukiwania wprowadź wartość **środkowe zasoby**.
1. Wybierz pozycję **zasób centralnie** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-resource-central"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD dla zasobów centralnych

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Centrum zasobów przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w centrum zasobów.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu Centrum zasobów, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
    1. **[Utwórz zasób test centralny zasobów](#create-resource-central-test-user)** , aby uzyskać odpowiednika B. Simon w usłudze Resource Central, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Skonfiguruj centralne Logowanie jednokrotne](#configure-resource-central-sso)** w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja z **centralną aplikacją zasobów** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W **podstawowej konfiguracji SAML**, wprowadź wartości dla następujących pól:

   1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<DOMAIN_NAME>/ResourceCentral`

   1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca:  `https://<DOMAIN_NAME>/ResourceCentral`

   1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/Acs`

    > [!NOTE]
    > Te wartości nie są wartościami literalnymi. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora i wartości adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej dla klientów centralnych zasobów](mailto:st@aod.vn) .  Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w **certyfikacie podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W obszarze **Konfigurowanie zasobów centralnych** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość `username@companydomain.extension` . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do centrum zasobów.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **zasób centralny**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku **Dodaj przypisanie** .
1. W okienku **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją w obszarze **Wybierz rolę**. Jeśli żadna rola nie została skonfigurowana dla tej aplikacji, zostanie wyświetlona **Domyślna rola dostępu** .
1. W okienku **Dodaj przypisanie** kliknij przycisk **Przypisz** .

### <a name="create-resource-central-test-user"></a>Tworzenie użytkownika testowego zasobów centralnych

W tej sekcji użytkownik o nazwie **B. Simon** został utworzony w **Centrum zasobów**.

1. W centrum zasobów wybierz pozycję **zabezpieczenia**  >    >  **nowe**.
  
    :::image type="content" source="./media/resource-central/new-person.png" alt-text="Zrzut ekranu przedstawiający okienko osoby w centrum zasobów, z wyróżnionym przyciskiem nowy.":::

1. W polu **szczegóły osoby** w polu **Nazwa wyświetlana** wprowadź użytkownik **B. Simon**. W polu **adres SMTP** wprowadź nazwę użytkownika usługi Azure AD. Przykład: `B.Simon@contoso.com`.

    :::image type="content" source="./media/resource-central/person.png" alt-text="Zrzut ekranu przedstawiający okienko Szczegóły osoby w centrum zasobów.":::

## <a name="configure-resource-central-sso"></a>Skonfiguruj centralne Logowanie jednokrotne zasobów

W tej sekcji skonfigurujesz Logowanie jednokrotne w **Administratorze systemu Resource Central**.

1. W obszarze Centrum zasobów — administrator systemu wybierz opcję **uwierzytelnianie zewnętrzne**.
1.  W obszarze **Konfiguracja włączania** wybierz pozycję **tak**.

    ![Zrzut ekranu pokazujący opcję Włącz konfigurację wybraną w okienku uwierzytelnianie zewnętrzne w centrum zasobów.](./media/resource-central/enable.png)

1. W polu **protokół uwierzytelniania** wybierz pozycję **SAML2**. 

   :::image type="content" source="./media/resource-central/protocol.png" alt-text="Zrzut ekranu pokazujący SAML2 wybrany dla protokołu uwierzytelniania w centrum zasobów.":::

1. W obszarze **Konfiguracja SAML2** wprowadź wartości dla następujących pól:

    1. W polu Identyfikator **(identyfikator jednostki)**, **adres URL logowania**, **adres URL wylogowania** i **Identyfikator usługi Azure AD** wprowadź odpowiednie adresy URL:

       :::image type="content" source="./media/resource-central/auth.png" alt-text="Zrzut ekranu przedstawiający okienko konfiguracji SAML2 w centrum zasobów.":::

        Skopiuj adresy URL z okienka **Konfigurowanie zasobów centralnych** :

        :::image type="content" source="./media/resource-central/setup.png" alt-text="Zrzut ekranu przedstawiający okienko Konfigurowanie zasobów centralnych w centrum zasobów.":::

   1. W przypadku **zwrotnego adresu URL** wprowadź `https://<DOMAIN_NAME>/ResourceCentral/ExAuth/Saml2Authentication/CallbackHandler` .
  
1. W obszarze **certyfikat** Przekaż certyfikat, a następnie wprowadź hasło.

   ![Zrzut ekranu przedstawiający sekcję certyfikatu w centrum zasobów.](./media/resource-central/cert.png)
   
1. Wybierz pozycję **Zapisz**.

1. Wróć do witryny **Azure Portal**. W polu **certyfikat podpisywania SAML** Przekaż certyfikat i wprowadź hasło.

   ![Zrzut ekranu przedstawiający okienko Importuj certyfikat w Azure Portal.](./media/resource-central/cert2.png).

1. Wybierz pozycję **Dodaj**.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

Ta sekcja umożliwia testowanie konfiguracji logowania jednokrotnego usługi Azure AD. Aby przetestować Logowanie jednokrotne, masz trzy opcje:

* W Azure Portal wybierz pozycję **Testuj tę aplikację**. Link przekierowuje do adresu URL logowania centralnego zasobu, w którym można zainicjować logowanie.

* Bezpośrednio przejdź do adresu URL logowania centralnego zasobu i zainicjuj logowanie.

   :::image type="content" source="./media/resource-central/test.png" alt-text="Zrzut ekranu przedstawiający stronę sieci Web centralnej testowego logowania jednokrotnego dla zasobu.":::

* Użyj portalu My Apps firmy Microsoft. W portalu Moje aplikacje wybierz kafelek **Centrum zasobów** , aby przekierować do adresu URL logowania centralnego zasobu. Aby uzyskać więcej informacji, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu centrum zasobów do logowania jednokrotnego za pomocą usługi Azure AD można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
