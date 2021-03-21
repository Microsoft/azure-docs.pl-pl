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
ms.date: 11/13/2020
ms.author: jeedes
ms.openlocfilehash: b70c50e7c2900f884dd4d91c6650205bc626326e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96178045"
---
# <a name="tutorial-azure-active-directory-integration-with-keeper-password-manager--digital-vault"></a>Samouczek: integracja Azure Active Directory z menedżerem haseł programu opiekuna & magazynu cyfrowego

Z tego samouczka dowiesz się, jak zintegrować aplikację Keeper Password Manager & Digital Vault z usługą Azure Active Directory (Azure AD).
Ta integracja zapewnia następujące korzyści:

* Możesz kontrolować w usłudze Azure AD, kto ma dostęp do usługi Keeper Password Manager & Digital Vault.
* Możesz umożliwić użytkownikom automatyczne logowanie się do Menedżera haseł programu opiekuna & magazynem cyfrowym (Logowanie jednokrotne) przy użyciu kont usługi Azure AD.
* Kontami można zarządzać w jednej centralnej lokalizacji: Azure Portal.


## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z menedżerem haseł programu opiekuna & magazynie cyfrowym, potrzebne są:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [miesięczną wersję próbną](https://azure.microsoft.com/pricing/free-trial/).
* Menedżer haseł opiekuna & subskrypcję magazynu cyfrowego — włączono obsługę logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Menedżer haseł opiekuna & magazyn cyfrowy obsługuje logowanie jednokrotne zainicjowane przez usługę SP.

* Menedżer haseł opiekuna & magazyn cyfrowy obsługuje Inicjowanie obsługi użytkowników just in Time.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Dodawanie Menedżera haseł programu opiekuna & magazynu cyfrowego z galerii

Aby skonfigurować integrację Menedżera haseł programu opiekuna & magazynem cyfrowym w usłudze Azure AD, Dodaj aplikację z galerii do listy aplikacji zarządzanych oprogramowania jako usługi (SaaS).

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W obszarze **Dodaj z galerii** wpisz w polu wyszukiwania pozycję **Menedżer haseł programu opiekuna &** .
1. Wybierz pozycję **Menedżer haseł opiekuna & magazyn cyfrowy** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager--digital-vault"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla programu opiekuna Menedżer haseł & magazyn cyfrowy

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Menedżera haseł opiekuna & cyfrowy magazyn przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Menedżerze haseł opiekuna & magazynie cyfrowym.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Menedżera haseł opiekuna & magazynu cyfrowego:

1. [Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso) , aby umożliwić użytkownikom korzystanie z tej funkcji.

    * [Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user) — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą użytkownika Britta Simon.
    * [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.

1. [Skonfiguruj Menedżera haseł programu opiekuna & Logowanie jednokrotne w magazynie cyfrowym](#configure-keeper-password-manager--digital-vault-sso) w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
    * [Tworzenie Menedżera haseł programu opiekuna & użytkownika testowego magazynu cyfrowego](#create-a-keeper-password-manager--digital-vault-test-user) w celu posiadania odpowiedników Britta Simon w Menedżerze haseł programu opiekuna & magazynu cyfrowego połączonego z reprezentacją usługi Azure AD.
1. [Przetestuj Logowanie jednokrotne](#test-sso) , aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie Zarządzanie **hasłem programu opiekuna &** w witrynie integracji aplikacji magazynu cyfrowego Znajdź sekcję **Zarządzaj** . Wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** wybierz ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Zrzut ekranu przedstawiający Konfigurowanie jednego Sign-On za pomocą języka SAML z wyróżnioną ikoną ołówka.](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja języka SAML** wykonaj następujące czynności:

    a. W polu **adres URL logowania** wpisz adres URL, który używa następującego wzorca:
    * W przypadku logowania jednokrotnego w chmurze: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Lokalne Logowanie jednokrotne: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    b. Dla **identyfikatora (identyfikator jednostki)** wpisz adres URL, który używa następującego wzorca:
    * W przypadku logowania jednokrotnego w chmurze: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Lokalne Logowanie jednokrotne: `https://<KEEPER_FQDN>/sso-connect`

    c. W polu **adres URL odpowiedzi** wpisz adres URL, który używa następującego wzorca:
    * W przypadku logowania jednokrotnego w chmurze: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Lokalne Logowanie jednokrotne: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [kierownikiem ds. ochrony magazynu &](https://keepersecurity.com/contact.html). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Menedżer haseł programu opiekuna & aplikacji magazynu cyfrowego oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych.

    ![Zrzut ekranu atrybutów użytkownika & oświadczenia.](common/default-attributes.png)

1. Ponadto Menedżer haseł opiekuna & aplikacji magazynu cyfrowego oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. Są one przedstawione w poniższej tabeli. Te atrybuty są również wstępnie wypełnione, ale można je przejrzeć zgodnie z wymaganiami.

    | Nazwa | Atrybut źródłowy|
    | ------------| --------- |
    | Pierwsze | user.givenname |
    | Ostatnie | user.surname |
    | E-mail | user.mail |

5. Na **skonfigurować pojedyncze Sign-On przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** wybierz pozycję **Pobierz**. Spowoduje to pobranie **XML metadanych Federacji** z opcji zgodnie z wymaganiami i zapisanie go na komputerze.

    ![Zrzut ekranu przedstawiający certyfikat podpisywania SAML z wyróżnionym pobieraniem.](common/metadataxml.png)

6. Na stronie **Konfigurowanie Menedżera haseł programu opiekuna & magazynu cyfrowego** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Zrzut ekranu przedstawiający Konfigurowanie Menedżera haseł programu opiekuna & magazynie cyfrowym z wyróżnionymi adresami URL.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie `B.Simon` .

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**  >  **Użytkownicy**  >  **Wszyscy użytkownicy**.
1. W górnej części ekranu wybierz pozycję **nowy użytkownik**.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W obszarze **Nazwa** wprowadź `B.Simon` .  
   1. W polu **Nazwa użytkownika** wprowadź `username@companydomain.extension` . Na przykład `B.Simon@contoso.com`.
   1. Wybierz pozycję **Pokaż hasło**, a następnie Zapisz wartość wyświetlaną.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Menedżera haseł opiekuna & magazynie cyfrowym.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Keeper Password Manager & Digital Vault**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**. W okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.
1. W obszarze **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy użytkowników. Następnie wybierz **pozycję zaznacz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola zostanie przypisana do użytkowników, możesz wybrać ją z listy **Wybierz rolę** . Jeśli żadna rola nie została skonfigurowana dla tej aplikacji, zostanie wybrana **Domyślna rola dostępu** .
1. W obszarze **Dodaj przypisanie** wybierz pozycję **Przypisz**.


## <a name="configure-keeper-password-manager--digital-vault-sso"></a>Konfigurowanie Menedżera haseł programu opiekuna & rejestracji jednokrotnej w magazynie cyfrowym

Aby skonfigurować Logowanie jednokrotne dla aplikacji, zapoznaj się z instrukcjami w [przewodniku pomocy technicznej](https://docs.keeper.io/sso-connect-guide/)dotyczącej wsparcia.

### <a name="create-a-keeper-password-manager--digital-vault-test-user"></a>Tworzenie Menedżera haseł programu opiekuna & użytkownika testowego magazynu cyfrowego

Aby umożliwić użytkownikom usługi Azure AD logowanie się do Menedżera haseł programu opiekuna & magazynie cyfrowym, należy je udostępnić. Aplikacja obsługuje funkcję aprowizacji użytkowników just in Time, a po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Jeśli chcesz ręcznie skonfigurować użytkowników, skontaktuj się z [pomocą techniczną](https://keepersecurity.com/contact.html).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* W Azure Portal wybierz pozycję **Testuj tę aplikację**. Spowoduje to przekierowanie do adresu URL logowania opiekuna Menedżera haseł & magazynu cyfrowego, w którym można zainicjować logowanie. 

* Możesz przejść bezpośrednio do adresu URL logowania dla aplikacji i zainicjować w tym miejscu logowanie.

* Możesz użyć panelu programu Microsoft Access. Po wybraniu kafelka **Magazyn cyfrowy &** w panelu dostępu zostanie on przekierowany do adresu URL logowania dla aplikacji. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu & magazynu cyfrowego przez Menedżera haseł programu opiekuna można wymusić kontrolę sesji. Chroni to eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Informacje o sposobie wymuszania kontroli sesji przy użyciu Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad).