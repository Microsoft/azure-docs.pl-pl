---
title: 'Samouczek: integracja Azure Active Directory z oprogramowaniem SAP Business ByDesign | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i SAP Business ByDesign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654344"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Samouczek: integracja usługi Azure Active Directory z oprogramowaniem SAP Business ByDesign

W tym samouczku dowiesz się, jak zintegrować oprogramowanie SAP Business ByDesign z usługą Azure Active Directory (Azure AD). W przypadku integrowania oprogramowania SAP Business ByDesign z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do SAP Business ByDesign.
* Zezwól użytkownikom na automatyczne logowanie do oprogramowania SAP Business ByDesign przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) w oprogramowaniu SAP Business ByDesign.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Oprogramowanie SAP Business ByDesign obsługuje zainicjowanie rejestracji jednokrotnej **SP**

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Dodawanie SAP Business ByDesign z galerii

Aby skonfigurować integrację SAP Business ByDesign w usłudze Azure AD, musisz dodać SAP Business ByDesign z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **SAP Business ByDesign** w polu wyszukiwania.
1. Wybierz pozycję **SAP Business ByDesign** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie rejestracji jednokrotnej usługi Azure AD

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD przy użyciu oprogramowania SAP Business ByDesign przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w oprogramowaniu SAP Business ByDesign.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą oprogramowania SAP Business ByDesign, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj oprogramowanie SAP Business BYDESIGN SSO](#configure-sap-business-bydesign-sso)** — aby skonfigurować pojedyncze ustawienia Sign-On po stronie aplikacji.
    1. **[Utwórz użytkownika testowego SAP Business ByDesign](#create-sap-business-bydesign-test-user)** , aby dysponować odpowiednikiem Britta Simon w oprogramowaniu SAP Business ByDesign, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracja aplikacji **SAP Business ByDesign** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej SAP Business ByDesign Client](https://www.sap.com/products/cloud-analytics.support.html) . Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja SAP Business ByDesign oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![image1](common/edit-attribute.png)

6. Kliknij ikonę **Edytuj** , aby edytować **wartość identyfikatora nazwy**.

    ![image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. W sekcji **Zarządzanie oświadczeniami użytkowników** wykonaj następujące czynności:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wybierz **transformację** jako **Źródło**.

    b. Na liście rozwijanej **transformacja** wybierz pozycję **ExtractMailPrefix ()**.

    > [!NOTE]
    > Na domyślne ByD używa **nieokreślonego** formatu NameID dla mapowania użytkownika. ByD mapuje NameID SAML-Assertions dla aliasu użytkownika ByD. Dodatkowo ByD obsługuje format identyfikatora nazwy **EmailAddress**. W tym przypadku ByD mapuje NameIDa SAM na adres e-mail użytkownika ByD, ByD dane kontaktowe pracownika. Aby uzyskać więcej szczegółowych informacji, możesz odwołać się do [tego blogu SAP](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie programu SAP Business ByDesign** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon do korzystania z logowania jednokrotnego platformy Azure, udzielając dostępu do oprogramowania SAP Business ByDesign.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **SAP Business ByDesign**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".

1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-sap-business-bydesign-sso"></a>Skonfiguruj oprogramowanie SAP Business ByDesign SSO

1. Zaloguj się do portalu SAP Business ByDesign z uprawnieniami administratora.

2. Przejdź do **typowego zadania zarządzania aplikacjami i użytkownikami** , a następnie kliknij kartę **dostawca tożsamości** .

3. Kliknij pozycję **Nowy dostawca tożsamości** i wybierz plik XML metadanych pobrany z Azure Portal. Przez zaimportowanie tych metadanych system automatycznie przekaże wymagany certyfikat podpisywania i certyfikat szyfrowania.

    ![Konfigurowanie pojedynczego Sign-On1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Aby dołączyć **adres URL usługi konsumenckej potwierdzenia** do żądania SAML, wybierz pozycję **Dołącz adres URL usługi potwierdzenia odbiorcy**.

5. Kliknij przycisk **Activate Single Sign-On** (Aktywuj logowanie jednokrotne).

6. Zapisz zmiany.

7. Kliknij kartę **My System** (Mój system).

    ![Konfigurowanie pojedynczego Sign-On2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. W polu tekstowym **adres URL logowania do usługi Azure AD** wklej wartość **adresu URL logowania** , która została skopiowana z Azure Portal.

    ![Konfigurowanie pojedynczego Sign-On3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Określ, czy pracownik może ręcznie wybrać opcję logowania przy użyciu identyfikatora użytkownika i hasła lub logowania jednokrotnego, wybierając pozycję **Ręczne wybieranie dostawcy tożsamości**.

10. W sekcji **adres URL logowania jednokrotnego** Określ adres URL, który powinien być używany przez pracownika do jednokrotnego do systemu.
    Z listy rozwijanej adres URL wysyłany do pracownika możesz wybrać jedną z następujących opcji:

    **Non-SSO URL** (Adres URL w przypadku braku obsługi logowania jednokrotnego)

    System wysyła do pracownika tylko normalny adres URL systemu. Pracownik nie może jednokrotnego za pomocą logowania jednokrotnego i należy zamiast tego użyć hasła lub certyfikatu.

    **SSO URL** (Adres URL logowania jednokrotnego)

    System wysyła do pracownika tylko adres URL logowania jednokrotnego. Pracownik może jednokrotnego za pomocą logowania jednokrotnego. Żądanie uwierzytelnienia zostanie przekierowane za pośrednictwem dostawcy tożsamości.

    **Automatic Selection** (Wybór automatyczny)

    Jeśli logowanie jednokrotne nie jest aktywne, system wysyła do pracownika normalny adres URL systemu. Jeśli logowanie jednokrotne jest aktywne, system sprawdza, czy pracownik ma hasło. Jeśli hasło jest dostępne, do pracownika wysyłany jest zarówno adres URL logowania jednokrotnego, jak i adres URL w przypadku braku obsługi logowania jednokrotnego. Jeśli jednak pracownik nie ma hasła, do pracownika wysyłany jest tylko adres URL logowania jednokrotnego.

11. Zapisz zmiany.

### <a name="create-sap-business-bydesign-test-user"></a>Utwórz użytkownika testowego SAP Business ByDesign

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w oprogramowaniu SAP Business ByDesign. Aby dodać użytkowników z platformy SAP Business ByDesign, skontaktuj się z [zespołem obsługi klienta SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html) . 

> [!NOTE]
> Upewnij się, że wartość NameID powinna być zgodna z polem username na platformie SAP Business ByDesign platform.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

1. Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania SAP Business ByDesign, w którym można zainicjować przepływ logowania. 

2. Przejdź bezpośrednio do adresu URL logowania SAP Business ByDesign, a następnie zainicjuj w nim przepływ logowania.

3. Możesz korzystać z aplikacji Microsoft my Apps. Kliknięcie kafelka SAP Business ByDesign w obszarze Moje aplikacje spowoduje przekierowanie do adresu URL logowania SAP Business ByDesign. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

* Po skonfigurowaniu oprogramowania SAP Business ByDesign można wymusić kontrolki sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolki sesji rozciągają się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).