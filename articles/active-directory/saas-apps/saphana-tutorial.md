---
title: 'Samouczek: integracja Azure Active Directory z SAP HANA | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i oprogramowaniem SAP HANA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: f6066997b5a63a9ffefc1371851c7200d7655c9c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97962603"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>Samouczek: integracja Azure Active Directory z SAP HANA

W tym samouczku dowiesz się, jak zintegrować SAP HANA z usługą Azure Active Directory (Azure AD). Podczas integrowania SAP HANA z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SAP HANA.
* Zezwól użytkownikom na automatyczne logowanie do SAP HANA przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem SAP HANA, potrzebujesz następujących elementów:

- Subskrypcji usługi Azure AD
- Subskrypcja oprogramowania SAP HANA z włączonym logowaniem jednokrotnym
- Wystąpienie oprogramowania HANA uruchomionego w ramach dowolnego dużego wystąpienia publicznego usługi IaaS, lokalnej, Azure VM lub SAP na platformie Azure
- Internetowy interfejs administracyjny XSA oraz program HANA Studio zainstalowane w wystąpieniu oprogramowania HANA

> [!NOTE]
> Nie zalecamy używania środowiska produkcyjnego oprogramowania SAP HANA do testowania kroków w tym samouczku. Integrację należy najpierw przetestować w środowisku programistycznym lub przejściowym aplikacji, a dopiero później użyć środowiska produkcyjnego.

Aby przetestować kroki w tym samouczku, musisz mieć dostęp do następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja oprogramowania SAP HANA z włączoną obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie SAP HANA obsługuje logowanie jednokrotne inicjowane przez **dostawcę tożsamości**
* Oprogramowanie SAP HANA obsługuje aprowizowanie użytkowników typu **just in time**

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.


## <a name="adding-sap-hana-from-the-gallery"></a>Dodawanie oprogramowania SAP HANA z galerii

Aby skonfigurować integrację oprogramowania SAP HANA z usługą Azure AD, należy dodać oprogramowanie SAP HANA z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP HANA** w polu wyszukiwania.
1. Wybierz **SAP HANA** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla SAP HANA

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą SAP HANA przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w SAP HANA.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą SAP HANA, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
2. **[Skonfiguruj SAP HANA Logowanie jednokrotne](#configure-sap-hana-sso)** — aby skonfigurować ustawienia pojedynczego Sign-On po stronie aplikacji.
    1. **[Tworzenie użytkownika testowego oprogramowania SAP HANA](#create-sap-hana-test-user)** — aby udostępnić w oprogramowaniu SAP HANA odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. Na Azure Portal na stronie integracja aplikacji **SAP HANA** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **adres URL odpowiedzi** wpisz adres URL, używając następującego wzorca:  `https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE]
    > Wartość adresu URL odpowiedzi nie jest prawdziwa. Zaktualizuj ją, stosując rzeczywisty adres URL odpowiedzi. Skontaktuj się z [zespołem pomocy technicznej SAP HANA klienta](https://cloudplatform.sap.com/contact.html) , aby uzyskać wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja SAP HANA oczekuje asercji SAML w konkretnym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Zrzut ekranu pokazujący sekcję "atrybuty użytkownika" z wybraną ikoną "Edytuj".](common/edit-attribute.png)

6. W sekcji **atrybuty użytkownika** w oknie dialogowym **atrybuty użytkownika & oświadczenia** wykonaj następujące czynności:
 
    a. Kliknij **ikonę Edytuj**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawiający okno dialogowe "atrybuty użytkownika & oświadczenia" z wybraną ikoną "Edytuj".](./media/saphana-tutorial/tutorial_usermail.png)

    ![image (obraz)](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. Z listy **Przekształcenie** wybierz pozycję **ExtractMailPrefix()**.

    c. Z listy **Parametr 1** wybierz pozycję **user.mail**.

    d. Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure, przyznając dostęp do SAP HANA.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAP HANA**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-hana-sso"></a>Skonfiguruj SAP HANA Logowanie jednokrotne

1. Aby skonfigurować logowanie jednokrotne po stronie oprogramowania SAP HANA, zaloguj się do **konsoli internetowej XSA oprogramowania HANA**, przechodząc do odpowiedniego punktu końcowego HTTPS.

    > [!NOTE]
    > W przypadku konfiguracji domyślnej adres URL przekierowuje żądanie do ekranu logowania, który wymaga poświadczeń uwierzytelnionego użytkownika bazy danych SAP HANA. Logujący się użytkownik musi mieć uprawnienia do wykonywania zadań administracyjnych dotyczących technologii SAML.

2. W interfejsie internetowym XSA wybierz pozycję **SAML Identity Provider** (Dostawca tożsamości SAML). W tym miejscu wybierz **+** przycisk w dolnej części ekranu, aby wyświetlić okienko **Dodawanie informacji o dostawcy tożsamości** . Następnie wykonaj następujące kroki:

    ![Dodawanie dostawcy tożsamości](./media/saphana-tutorial/sap1.png)

    a. W okienku **Add Identity Provider Info** (Dodawanie informacji o dostawcy tożsamości) wklej kod XML metadanych (pobrany z witryny Azure Portal) w polu **Metadata** (Metadane).

    ![Zrzut ekranu przedstawiający okienko "Dodawanie informacji o dostawcy tożsamości" z wyróżnionymi polami "Metadata" i "name".](./media/saphana-tutorial/sap2.png)

    b. Jeśli zawartość dokumentu XML jest prawidłowa, proces analizy wyodrębni informacje wymagane dla pól **Subject (Podmiot), Entity ID (Identyfikator jednostki) i Issuer (Wystawca)** w obszarze ekranu **General data** (Dane ogólne). Wyodrębni także informacje niezbędne dla pól adresu URL w obszarze ekranu **Destination** (Cel), na przykład **Base URL (Podstawowy adres URL) i SingleSignOn URL (Adres URL logowania jednokrotnego) (*)**.

    ![Dodawanie ustawień dostawcy tożsamości](./media/saphana-tutorial/sap3.png)

    c. W polu **Name** (Nazwa) obszaru ekranu **General Data** (Dane ogólne) podaj nazwę dla nowego dostawcy tożsamości SAML logowania jednokrotnego.

    > [!NOTE]
    > Nazwa dostawcy tożsamości SAML jest wymagana i musi być unikatowa. Jest ona pokazywana na liście dostępnych dostawców tożsamości SAML wyświetlanej po wybraniu standardu SAML jako metody uwierzytelniania używanej przez aplikacje XS oprogramowania SAP HANA. Na przykład możesz to zrobić w obszarze ekranu **Authentication** (Uwierzytelnianie) narzędzia XS Artifact Administration.

3. Wybierz polecenie **Save** (Zapisz), aby zapisać szczegóły dostawcy tożsamości SAML i dodać nowego dostawcę tożsamości SAML do listy znanych dostawców tożsamości SAML.

    ![Przycisk Zapisz](./media/saphana-tutorial/sap4.png)

4. W programie Studio HANA na karcie **Configuration** (Konfiguracja) przefiltruj ustawienia za pomocą ciągu **saml**. Następnie dostosuj parametr **assertion_timeout**, zmieniając wartość **10 sec** na **120 sec**.

    ![Ustawienie assertion_timeout](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>Tworzenie użytkownika testowego oprogramowania SAP HANA

Aby umożliwić użytkownikom usługi Azure AD logowanie do oprogramowania SAP HANA, należy aprowizować ich w oprogramowaniu SAP HANA.
Oprogramowanie SAP HANA obsługuje **aprowizację just in time**, która jest domyślnie włączona.

Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące kroki:

>[!NOTE]
>Opcjonalnie zmień uwierzytelnianie zewnętrzne używane przez użytkownika. Uwierzytelnianie można przeprowadzać za pomocą systemu zewnętrznego, takiego jak Kerberos. Aby uzyskać szczegółowe informacje o tożsamościach zewnętrznych, skontaktuj się z [administratorem domeny](https://cloudplatform.sap.com/contact.html).

1. Otwórz program [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) jako administrator, a następnie włącz logowanie jednokrotne SAML dla użytkownika DB-User.

    ![Tworzenie użytkownika](./media/saphana-tutorial/sap5.png)

2. Zaznacz niewidoczne pole wyboru po lewej stronie pozycji **SAML**, a następnie wybierz link **Configure** (Konfiguruj).

3. Wybierz pozycję **Add** (Dodaj), aby dodać dostawcę tożsamości SAML.  Wybierz odpowiedniego dostawcę tożsamości SAML, a następnie wybierz pozycję **OK**.

4. Dodaj **tożsamość zewnętrzną** (w tym przypadku BrittaSimon) lub wybierz pozycję **Any** (Dowolna). Następnie wybierz przycisk **OK**.

   > [!Note]
   > Jeśli pole wyboru **Any** (Dowolna) nie jest zaznaczone, nazwa użytkownika w oprogramowaniu HANA musi dokładnie pasować do głównej nazwy użytkownika przed sufiksem domeny. (Na przykład BrittaSimon@contoso.com to BrittaSimon w oprogramowaniu HANA).

5. Dla celów testowych przypisz wszystkie role **XS** do użytkownika.

    ![Przypisywanie ról](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > Należy udzielić tylko uprawnień odpowiednich dla danego przypadku użycia.

6. Zapisz użytkownika.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i należy automatycznie zalogować się do SAP HANA, dla którego skonfigurowano Logowanie jednokrotne

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka SAP HANA w obszarze Moje aplikacje należy automatycznie zalogować się do SAP HANA, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu SAP HANA można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).