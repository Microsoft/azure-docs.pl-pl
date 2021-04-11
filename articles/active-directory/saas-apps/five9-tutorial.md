---
title: 'Samouczek: Azure Active Directory integrację z kartą Five9 plus (CTI, agenci centrum kontaktów) | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i Five9 Plus adapter (CTI, agenci centrum kontaktów).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 85e953951d5368dc97312e7810f3c356bda7c6b6
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218723"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Samouczek: integracja Azure Active Directory z kartą Five9 plus (CTI, agenci centrum kontaktów)

W tym samouczku dowiesz się, jak zintegrować usługę Five9 Plus adapter (CTI, agenci centrum kontaktu) z Azure Active Directory (Azure AD). Po zintegrowaniu karty Five9 plus (CTI, agenci centrum kontaktu) z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do karty Five9 plus (CTI, agenci centrum styku).
* Umożliwienie użytkownikom automatycznego logowania się do usługi Five9 Plus adapter (CTI, agenci centrum kontaktu) z kontami w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z kartą Five9 plus (CTI, agenci centrum kontaktów), potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Five9 Plus — karta (CTI, agenci centrum styku) subskrypcja obsługująca Logowanie jednokrotne.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Five9 Plus adapter (CTI, agenci centrum kontaktów) obsługuje **dostawcy tożsamości** zainicjowane przez logowanie jednokrotne.

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Dodaj kartę Five9 plus (CTI, agenci centrum kontaktów) z galerii

Aby skonfigurować integrację karty Five9 plus (CTI, agenci centrum kontaktów) z usługą Azure AD, musisz dodać kartę Five9 plus (CTI, agenci centrum kontaktu) z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Five9 Plus adapter (CTI, agenci centrum kontaktów)** w polu wyszukiwania.
1. Wybierz pozycję **Five9 Plus adapter (CTI, agenci centrum kontaktów)** z panelu wyniki, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Five9 Plus adapter (CTI, agenci centrum kontaktów)

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Five9 Plus adapter (CTI, agenci centrum kontaktów) przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w Five9 Plus adapter (CTI, agenci centrum kontaktów).

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD przy użyciu karty Five9 plus (CTI, agenci centrum kontaktów), wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Five9 Plus adapter (CTI, agenci centrum kontaktów) logowanie JEDNOkrotne](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz Five9 Plus adapter (CTI, agenci centrum styku) test użytkownika](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** — Aby uzyskać odpowiedni odpowiednik B. Simon w Five9 Plus adapter (CTI, agenci centrum kontaktu), który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie integracji aplikacji na **karcie Five9 plus (CTI)** , Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** wykonaj następujące kroki:

    a. W polu tekstowym **Identyfikator** wpisz jeden z następujących adresów URL:
    
    |    Środowisko      |       Adres URL      |
    | :-- | :-- |
    | Dla "Five9 Plus adapter dla programu Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Dla "Five9 Plus adapter for systemu Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9 Plus adapter for Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. W polu tekstowym **adres URL odpowiedzi** wpisz jeden z następujących adresów URL:

    |      Środowisko     |      Adres URL      |
    | :--                  | :--           |
    | Dla "Five9 Plus adapter dla programu Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Dla "Five9 Plus adapter for systemu Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9 Plus adapter for Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. Na stronie **Konfiguracja Five9 plus (CTI, agenci centrum kontaktów)** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do karty Five9 plus (CTI, agenci centrum kontaktów).

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Five9 Plus adapter (CTI, agenci centrum styku)**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Konfigurowanie logowania jednokrotnego dla karty Five9 plus (CTI, agenci centrum kontaktów)

1. Aby skonfigurować Logowanie jednokrotne na **karcie Five9 plus (CTI, agenci centrum kontaktów)** , należy wysłać pobrany **certyfikat (base64)** i odpowiednie skopiowane adresy URL do [karty Five9 plus (CTI, agenci centrum styku)](https://www.five9.com/about/contact). Ponadto, aby dodatkowo skonfigurować Logowanie jednokrotne, wykonaj poniższe czynności zgodnie z kartą:

    a. Przewodnik administratora "Five9 Plus adapter for Agent Desktop Toolkit": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Przewodnik administratora "Five9 Plus adapter for Microsoft Dynamics CRM": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Przewodnik administratora "Five9 Plus adapter for systemu Zendesk": [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Tworzenie karty Five9 plus (CTI, agenci centrum kontaktów) — Użytkownik testowy

W tej sekcji utworzysz użytkownika o nazwie Britta Simon in Five9 Plus adapter (CTI, agenci centrum kontaktów). Skontaktuj się z [zespołem pomocy technicznej Five9 Plus adapter (CTI, agenci centrum styku)](https://www.five9.com/about/contact) , aby dodać użytkowników do platformy Five9 plus (CTI, agenci centrum styku). Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego. 

## <a name="test-sso"></a>Testuj Logowanie jednokrotne

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji.

* Kliknij pozycję Testuj tę aplikację w Azure Portal i zaloguj się automatycznie do karty Five9 plus (CTI, agenci centrum kontaktu), dla której skonfigurowano Logowanie jednokrotne.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Five9 plus (CTI, agenci centrum kontaktów) w obszarze Moje aplikacje należy automatycznie zalogować się do karty Five9 plus (CTI, agenci centrum kontaktu), dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu karty Five9 plus (CTI, agenci centrum kontaktów) można wymusić kontrolę sesji, która chroni eksfiltracji i niefiltrowanie danych poufnych organizacji w czasie rzeczywistym. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
