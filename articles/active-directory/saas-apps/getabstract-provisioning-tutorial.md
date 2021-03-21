---
title: 'Samouczek: Konfigurowanie getAbstract dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z Azure Active Directory do getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616149"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie getAbstract dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach metod getAbstract i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i zastrzega użytkownikom i grupom możliwość [getAbstract](https://www.getabstract.com) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje na temat tego, jak działa ta usługa, jak to się dzieje, i często zadawanych pytań, zobacz [Automatyzacja aprowizacji użytkowników i cofanie obsługi w aplikacjach typu oprogramowanie jako usługa (SaaS) za pomocą usługi Azure AD](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
> * Utwórz użytkowników w getAbstract.
> * Usuń użytkowników z metody getAbstract, gdy nie wymagają już dostępu.
> * Zachowaj synchronizację atrybutów użytkownika między usługą Azure AD i getAbstract.
> * Inicjowanie obsługi administracyjnej grup i członkostw w grupach w programie getAbstract.
> * Włącz logowanie jednokrotne [(SSO)](getabstract-tutorial.md) do getAbstract (zalecane).

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji. Przykładami są administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny.
* Dzierżawa getAbstract (licencja getAbstract Corporate).
* Logowanie jednokrotne włączone w dzierżawie usługi Azure AD i w dzierżawie getAbstract.
* Zatwierdzenie i system do zarządzania tożsamościami między domenami (standard scim) na potrzeby funkcji getAbstract. (Wyślij wiadomość e-mail do b2b.itsupport@getabstract.com .)

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
1. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Określ dane, które mają być [mapowane między usługą Azure AD i getAbstract](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie funkcji getAbstract w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do getAbstract.
1. Wybierz ikonę ustawień znajdującą się w prawym górnym rogu, a następnie wybierz opcję **My Administracja centralna** .

    ![Zrzut ekranu przedstawiający getAbstract my The Central administrator.](media/getabstract-provisioning-tutorial/my-account.png)

1. Znajdź i wybierz opcję **administrator Standard scim** .

    ![Zrzut ekranu pokazujący Standard scim administratora getAbstract.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Wybierz pozycję **Przejdź**.

    ![Zrzut ekranu pokazujący identyfikator Standard scim klienta.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Wybierz pozycję **Generuj nowy token**.

    ![Zrzut ekranu pokazujący Standard scim $ abstract token 1.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Jeśli masz pewność, wybierz pozycję **Generuj nowy token**. W przeciwnym razie wybierz pozycję **Anuluj**.

    ![Zrzut ekranu pokazujący Standard scim $ abstract token 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Wybierz ikonę Kopiuj do Schowka lub wybierz cały token i skopiuj go. Należy również pamiętać, że adres URL dzierżawy/podstawowy to `https://www.getabstract.com/api/scim/v2` . Te wartości zostaną wprowadzone w polach **token tajny** i **adres URL dzierżawy** na karcie **aprowizacji** aplikacji getAbstract w Azure Portal.

    ![Zrzut ekranu pokazujący Standard scim $ abstract token 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>Krok 3. Dodaj getAbstract z galerii aplikacji usługi Azure AD

Dodaj getAbstract z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do elementu getAbstract. Jeśli wcześniej skonfigurowano getAbstract dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecamy utworzenie oddzielnej aplikacji podczas wstępnego testowania integracji. Aby dowiedzieć się więcej na temat dodawania aplikacji z galerii, zapoznaj się z [tym przewodnikiem Szybki Start](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Możesz użyć usługi Azure AD Provisioning, aby określić zakres, który zostanie zainicjowany na podstawie przydziału do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w temacie [Inicjowanie obsługi aplikacji z filtrami określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Podczas przypisywania użytkowników i grup do metody getAbstract należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról.

* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników i grup, Zanim przeprowadzisz wszystko do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować tę opcję, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w celu getAbstract

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie TestApp na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla elementu getAbstract w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **getAbstract**.

    ![Zrzut ekranu przedstawiający link getAbstract na liście aplikacji.](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający kartę aprowizacji.](common/provisioning.png)

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu pokazujący tryb aprowizacji ustawiony na automatyczny.](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wprowadź wartość w polu getAbstract **URL dzierżawy** i informacje o **tokenie tajnym** . Wybierz pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z serwerem getAbstract. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto getAbstract ma uprawnienia administratora i spróbuj ponownie.

    ![Zrzut ekranu pokazujący pola adresu URL dzierżawy i tokenu tajnego.](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu przedstawiający pole powiadomień E-mail.](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby usunąć abstrakcyjne**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do getAbstract w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w getAbstract dla operacji Update. Jeśli zmienisz [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że interfejs API getAbstract obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |externalId|Ciąg|
   |preferredLanguage|Ciąg|

1. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do getAbstract**.

1. Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do getAbstract w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania grup w getAbstract dla operacji Update. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

    |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
    |---|---|---|
    |displayName|Ciąg|&check;|
    |externalId|Ciąg|
    |elementy członkowskie|Tematy pomocy|

1. Aby skonfigurować filtry zakresu, zobacz instrukcje zawarte w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning dla elementu getAbstract, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, dla których chcesz zainicjować obsługę administracyjną, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zrzut ekranu pokazujący zakres aprowizacji.](common/provisioning-scope.png)

1. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący przycisk Zapisz.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md) , aby określić, którzy użytkownicy byli pomyślnie lub niepowodzeniem.
* Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Aby dowiedzieć się więcej na temat stanów kwarantanny, zobacz temat [stan aprowizacji aplikacji dla kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
