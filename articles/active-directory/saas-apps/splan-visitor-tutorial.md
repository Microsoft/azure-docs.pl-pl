---
title: 'Samouczek: integracja Azure Active Directory logowania jednokrotnego (SSO) z sPlan odwiedzających | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i sPlan.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644701"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Samouczek: integracja Azure Active Directory logowania jednokrotnego (SSO) z sPlan odwiedzających

W tym samouczku dowiesz się, jak zintegrować sPlan odwiedzających z usługą Azure Active Directory (Azure AD). Po zintegrowaniu sPlan odwiedzających z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do sPlan odwiedzających.
* Zezwól użytkownikom na automatyczne logowanie do sPlan odwiedzających przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji, Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne będą następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym dla Gości sPlan.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

SPlan odwiedzających obsługuje logowanie jednokrotne inicjowane przez usługę dostawcy tożsamości.

## <a name="add-splan-visitor-from-the-gallery"></a>Dodawanie sPlan odwiedzających z galerii

Aby skonfigurować integrację sPlan odwiedzających w usłudze Azure AD, Dodaj odwiedzający sPlan z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wprowadź **sPlan odwiedzających** w polu wyszukiwania.
1. Wybierz pozycję **sPlan odwiedzających** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla gościa sPlan

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą sPlan odwiedzających przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w sPlan.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą sPlan odwiedzających, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** , aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą użytkownika testowego B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne dla Gości sPlan](#configure-splan-visitor-sso)** , aby skonfigurować ustawienia logowania jednokrotnego za pomocą programu sPlan.
    1. **[Utwórz użytkownika testowego odwiedzającego sPlan](#create-a-splan-visitor-test-user)** , aby uzyskać odpowiednika B. Simon w odwiedzaniu sPlan, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** , aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne w usłudze Azure AD w Azure Portal:

1. W Azure Portal na stronie integracja aplikacji **sPlan odwiedzających** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie pojedynczego Sign-On przy użyciu języka SAML** wybierz ikonę **Edytuj/pióra** , aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Zrzut ekranu z wyróżnioną ikoną Edytuj/pióro dla podstawowej konfiguracji protokołu SAML.](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** aplikacja została wstępnie skonfigurowana, a wymagane adresy URL są wstępnie wypełniane na platformie Azure. Wybierz przycisk **Zapisz** , aby zapisać konfigurację.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu protokołu SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **plik XML metadanych Federacji**. Wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Zrzut ekranu z wyróżnionym linkiem pobierania XML metadanych Federacji.](common/metadataxml.png)

1. W sekcji **Konfigurowanie odwiedzania sPlan** skopiuj odpowiedni adres URL lub adresy URL na podstawie wymagania.

    ![Zrzut ekranu z wyróżnioną sekcją adresy URL konfiguracji.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w Azure Portal.

1. W okienku po lewej stronie Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz pozycję **nowy użytkownik** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wpisz **B. Simon**.  
   1. W polu **Nazwa użytkownika** wprowadź nazwę użytkownika w _username@companydomain.extension_ formacie. Na przykład wprowadź **B.Simon@contoso.com** .
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Wybierz przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do osoby odwiedzającej sPlan.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz **sPlan odwiedzających** , aby otworzyć przegląd aplikacji.
1. Znajdź sekcję **Zarządzanie** , a następnie wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy **Użytkownicy** , a następnie kliknij pozycję **Wybierz** w dolnej części ekranu.
1. Jeśli użytkownikowi zostanie przypisana rola, wybierz ją z menu rozwijanego **Wybierz rolę** . Jeśli nie skonfigurowano żadnej roli dla tej aplikacji, pozostaw wybraną **domyślną rolę dostępu** .
1. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

## <a name="configure-splan-visitor-sso"></a>Konfigurowanie logowania jednokrotnego dla Gości sPlan

Aby skonfigurować Logowanie jednokrotne za pomocą sPlan odwiedzających, Wyślij pobrany **plik XML metadanych Federacji** oraz odpowiednie skopiowane adresy url z Azure Portal do [zespołu pomocy technicznej sPlan](mailto:support@splan.com). Gwarantuje to, że połączenie SSO protokołu SAML jest prawidłowo ustawione na obu stronach.

### <a name="create-a-splan-visitor-test-user"></a>Tworzenie użytkownika testowego odwiedzającego sPlan

Utwórz użytkownika testowego o nazwie **Britta Simon** w programie odwiedzającym sPlan. Współpracuj z [zespołem pomocy technicznej sPlan](mailto:support@splan.com) , aby dodać użytkownika do sPlan odwiedzających. Przed użyciem logowania jednokrotnego należy utworzyć i aktywować użytkownika.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

Przetestuj konfigurację logowania jednokrotnego usługi Azure AD, korzystając z jednej z następujących opcji:

* **Azure Portal**: wybierz pozycję **Testuj tę aplikację** , aby automatycznie zalogować się do osoby odwiedzającej sPlan, dla której skonfigurowano Logowanie jednokrotne.
* **Portal Microsoft my Apps**: wybierz kafelek **sPlan odwiedzających** , aby automatycznie zalogować się do osoby odwiedzającej sPlan, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat portalu Moje aplikacje, zobacz artykuł [Logowanie i uruchamianie aplikacji z poziomu portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu sPlan odwiedzających można [dowiedzieć się, jak wymuszać kontrolki sesji w Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app). Kontrolki sesji pomagają w ochronie eksfiltracji i niefiltrowaniu poufnych danych organizacji w czasie rzeczywistym. Kontrolki sesji wykraczają poza dostęp warunkowy.