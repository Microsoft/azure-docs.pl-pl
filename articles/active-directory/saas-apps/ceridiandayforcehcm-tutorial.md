---
title: 'Samouczek: integracja Azure Active Directory z Ceridian Dayforce HCM | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 4468340dbeeeb67b736d9fd2d227d9c7b2ecbeb6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427721"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Samouczek: integracja Azure Active Directory z usługą Ceridian Dayforce HCM

W tym samouczku dowiesz się, jak zintegrować usługę Ceridian Dayforce HCM z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Ceridian Dayforce HCM z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do Ceridian Dayforce HCM.
* Zezwól użytkownikom na automatyczne logowanie do Ceridian Dayforce HCM przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO, Ceridian Dayforce HCM).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Aplikacja Ceridian Dayforce HCM obsługuje logowanie jednokrotne inicjowane przez **dostawcę usług**

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Dodaj Ceridian Dayforce HCM z galerii

Aby skonfigurować integrację aplikacji Ceridian Dayforce HCM z usługą Azure AD, musisz dodać aplikację Ceridian Dayforce HCM z galerii do swojej listy zarządzanych aplikacji SaaS.

1. Zaloguj się do Azure Portal przy użyciu konta służbowego lub konto Microsoft prywatnego.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Ceridian Dayforce HCM** w polu wyszukiwania.
1. Wybierz pozycję **Ceridian DAYFORCE HCM** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla Ceridian Dayforce HCM

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą Ceridian Dayforce HCM przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w Ceridian Dayforce HCM.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą Ceridian Dayforce HCM, wykonaj następujące czynności:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Ceridian DAYFORCE HCM Logowanie jednokrotne](#configure-ceridian-dayforce-hcm-sso)** — aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Ceridian DAYFORCE HCM](#create-ceridian-dayforce-hcm-test-user)** , aby dysponować odpowiednikiem B. Simon w Ceridian Dayforce HCM, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD 

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W Azure Portal na stronie **Ceridian DAYFORCE HCM** Application Integration Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę ołówka dla **podstawowej konfiguracji SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![Domena i adresy URL aplikacji Ceridian Dayforce HCM — informacje dotyczące logowania jednokrotnego](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL używany przez użytkowników do logowania się do aplikacji Ceridian Dayforce HCM.

    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | W przypadku środowiska testowego | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. W polu tekstowym **Identyfikator** wpisz adres URL, używając następującego wzorca:

    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp` |
    | W przypadku środowiska testowego | `https://fs-test.dayforcehcm.com/sp` |

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL używany przez usługę Azure AD do publikowania odpowiedzi.

    | Środowisko | Adres URL |
    | :-- | :-- |
    | W przypadku środowiska produkcyjnego | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | W przypadku środowiska testowego | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. W celu uzyskania tych wartości skontaktuj się z [zespołem pomocy technicznej klienta aplikacji Ceridian Dayforce HCM](https://www.ceridian.com/support). Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Aplikacja Ceridian Dayforce HCM oczekuje potwierdzeń SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Wartościami tych atrybutów możesz zarządzać w sekcji **Atrybuty użytkownika** na stronie integracji aplikacji. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij przycisk **Edytuj**, aby otworzyć okno dialogowe **Atrybuty użytkownika**.

    ![Zrzut ekranu przedstawia atrybuty użytkownika z wybraną ikoną Edytuj.](common/edit-attribute.png)

6. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej, i wykonaj następujące czynności:

    | Nazwa | Atrybut źródłowy|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![Zrzut ekranu przedstawia oświadczenia użytkownika z opcją dodania nowego oświadczenia.](common/new-save-attribute.png)

    ![Zrzut ekranu przedstawia okno dialogowe Zarządzanie oświadczeniami użytkowników, w którym można wprowadzić podane wartości.](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    c. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Z listy **Atrybut źródłowy** wybierz atrybut użytkownika, którego chcesz użyć na potrzeby implementacji. Na przykład jeśli chcesz użyć identyfikatora EmployeeID jako unikatowego identyfikatora użytkownika, a wartość atrybutu jest przechowywana w elemencie ExtensionAttribute2, wybierz pozycję user.extensionattribute2.

    f. Kliknij przycisk **OK** .

    przykład Kliknij pozycję **Zapisz**.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

8. W sekcji **Konfigurowanie aplikacji Ceridian Dayforce HCM** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

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

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do Ceridian Dayforce HCM.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Ceridian Dayforce HCM**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.
1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .
1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz, że rola ma być przypisana do użytkowników, możesz wybrać ją z listy rozwijanej **Wybierz rolę** . Jeśli nie skonfigurowano roli dla tej aplikacji, zostanie wyświetlona wybrana rola "domyślny dostęp".
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz** .

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Konfigurowanie Ceridian Dayforce HCM Logowanie jednokrotne

Aby skonfigurować logowanie jednokrotne po stronie aplikacji **Ceridian Dayforce HCM**, musisz wysłać pobrany **plik XML metadanych** i odpowiednie adresy URL skopiowane z witryny Azure Portal [zespołowi pomocy technicznej aplikacji Ceridian Dayforce HCM](https://www.ceridian.com/support). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Tworzenie użytkownika testowego aplikacji Ceridian Dayforce HCM

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w aplikacji Ceridian Dayforce HCM. Współpracuj z [zespołem pomocy technicznej Ceridian DAYFORCE HCM](https://www.ceridian.com/support) , aby dodać użytkowników na platformie Ceridian Dayforce HCM. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu następujących opcji. 

* Kliknij pozycję **Testuj tę aplikację** w Azure Portal. Spowoduje to przekierowanie do adresu URL logowania Ceridian Dayforce HCM, gdzie można zainicjować przepływ logowania. 

* Przejdź do adresu URL logowania Ceridian Dayforce HCM bezpośrednio i zainicjuj w nim przepływ logowania.

* Możesz korzystać z aplikacji Microsoft my Apps. Po kliknięciu kafelka Ceridian Dayforce HCM w obszarze Moje aplikacje zostanie ono przekierowany do adresu URL logowania do Ceridian Dayforce. Aby uzyskać więcej informacji o moich aplikacjach, zobacz [wprowadzenie do aplikacji Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu Ceridian Dayforce HCM można wymusić kontrolę sesji, która chroni w czasie rzeczywistym eksfiltracji i niefiltrowanie danych poufnych w organizacji. Kontrolka sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymuszać kontrolę sesji za pomocą Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
