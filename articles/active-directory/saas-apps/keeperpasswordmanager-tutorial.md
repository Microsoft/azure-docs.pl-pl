---
title: 'Samouczek: integracja Azure Active Directory z menedżerem haseł programu opiekuna & magazynu cyfrowego | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Keeper Password Manager & Digital Vault.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/07/2020
ms.author: jeedes
ms.openlocfilehash: 05b81457af8e74f95bdd3af940fa2b39ba93504a
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92459146"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Samouczek: integracja Azure Active Directory z menedżerem haseł programu opiekuna & magazynu cyfrowego

Z tego samouczka dowiesz się, jak zintegrować aplikację Keeper Password Manager & Digital Vault z usługą Azure Active Directory (Azure AD).
integracja usługi Keeper Password Manager & Digital Vault z usługą Azure AD oferuje następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi Keeper Password Manager & Digital Vault.
* Możesz zezwolić swoim użytkownikom na automatyczne logowanie do aplikacji Keeper Password Manager & Digital Vault (logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Keeper Password Manager & Digital Vault, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja usługi Keeper Password Manager & Digital Vault z włączonym logowaniem jednokrotnym

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Usługa Keeper Password Manager & Digital Vault obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

* Usługa Keeper Password Manager & Digital Vault obsługuje aprowizację użytkowników **Just In Time**

* Po skonfigurowaniu Menedżera haseł programu opiekuna & magazynem cyfrowym można wymusić kontrolę sesji, co chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodawanie usługi Keeper Password Manager & Digital Vault z galerii

Aby skonfigurować integrację usługi Keeper Password Manager & Digital Vault z usługą Azure AD, musisz dodać usługę Keeper Password Manager & Digital Vault z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Menedżer haseł programu opiekuna &** .
1. Wybierz pozycję **Menedżer haseł opiekuna & magazynie cyfrowym** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu opiekuna Menedżer haseł & magazyn cyfrowy

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Menedżera haseł opiekuna & cyfrowy magazyn przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Menedżerze haseł opiekuna & magazynie cyfrowym.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Menedżera haseł opiekuna & magazynie cyfrowym, wykonaj następujące bloki konstrukcyjne::

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.

    * **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
    * **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.

1. **[Konfigurowanie Menedżera haseł programu opiekuna & rejestracji jednokrotnej magazynu cyfrowego](#configure-keeper-password-manager--digital-vault-sso)** — w celu skonfigurowania ustawień pojedynczego Sign-On po stronie aplikacji.
    * **[Tworzenie użytkownika testowego usługi Keeper Password Manager & Digital Vault](#create-keeper-password-manager--digital-vault-test-user)** — aby mieć w aplikacji Keeper Password Manager & Digital Vault odpowiednik użytkownika Britta Simon połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/), na stronie **Menedżer haseł programu opiekuna &** Strona integracja aplikacji magazynu cyfrowego Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: 
    * W przypadku **logowania jednokrotnego w chmurze** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Na **Premium logowanie JEDNOkrotne** : `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: 
    * W przypadku **logowania jednokrotnego w chmurze** : `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Na **Premium logowanie JEDNOkrotne** : `https://<KEEPER_FQDN>/sso-connect`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: 
    * W przypadku **logowania jednokrotnego w chmurze** : `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Na **Premium logowanie JEDNOkrotne** : `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej usługi Keeper Password Manager & Digital Vault Client](https://keepersecurity.com/contact.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie usługi Keeper Password Manager & Digital Vault** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Menedżera haseł opiekuna & magazynie cyfrowym.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Keeper Password Manager & Digital Vault**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurowanie Menedżera haseł programu opiekuna & rejestracji jednokrotnej w magazynie cyfrowym

Aby skonfigurować logowanie jednokrotne po stronie usługi **Keeper Password Manager & Digital Vault**, postępuj zgodnie z wytycznymi podanymi na stronie [przewodnika pomocy technicznej firmy Keeper](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-keeper-password-manager--digital-vault-test-user"></a>Tworzenie użytkownika testowego usługi Keeper Password Manager & Digital Vault

Aby umożliwić użytkownikom usługi Azure AD logowanie się do usługi Keeper Password Manager & Digital Vault, należy aprowizować ich w tej usłudze. Aplikacja obsługuje aprowizowanie użytkowników typu Just In Time. Po uwierzytelnieniu użytkownicy zostaną automatycznie utworzeni w aplikacji. Jeśli chcesz skonfigurować użytkowników ręcznie, możesz skontaktować się z [pomocą techniczną firmy Keeper](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Keeper Password Manager & Digital Vault w panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Keeper Password Manager & Digital Vault, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](./tutorial-list.md)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md)

- [Wypróbuj Menedżera haseł programu opiekuna & magazynie cyfrowym w usłudze Azure AD](https://aad.portal.azure.com/)

- [Co to jest kontrola sesji w Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)