---
title: 'Samouczek: Konfigurowanie gramatyki w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864809"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie gramatyki w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w odniesieniu do gramatyki i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje obsługę administracyjną użytkowników i grup w celu ich [gramatyki](https://www.grammarly.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Twórz użytkowników w sposób gramatyki
> * Usuń użytkowników w trybie gramatyki, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a gramatyką

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* Konto biznesowe w przypadku gramatyki z dostępem administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Określ dane, które mają być [mapowane między usługą Azure AD a gramatyką](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie gramatyki w celu obsługi aprowizacji za pomocą usługi Azure AD

Skontaktuj się ze swoim przedstawicielem gramatycznym lub napisz, aby <support@grammarly.com> zażądać tokenu aprowizacji.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie gramatyki z galerii aplikacji usługi Azure AD

Dodaj gramatykę z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie obsługą gramatyczną. Jeśli wcześniej skonfigurowano gramatykę na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecamy utworzenie oddzielnej aplikacji podczas wstępnego testowania integracji. Aby dowiedzieć się więcej na temat dodawania aplikacji z galerii, zapoznaj się z [tym przewodnikiem Szybki Start](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Możesz użyć usługi Azure AD Provisioning, aby określić zakres, który zostanie zainicjowany na podstawie przydziału do aplikacji lub na podstawie atrybutów użytkownika lub grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w temacie [Inicjowanie obsługi aplikacji z filtrami określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* W przypadku przypisywania użytkowników i grup do gramatyki należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról.

* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników i grup, Zanim przeprowadzisz wszystko do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować tę opcję, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w celu gramatyki

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników lub grup w programie TestApp na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników w przypadku gramatyki w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu przedstawiający okienko aplikacje dla przedsiębiorstw.](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **Gramatyka**.

    ![Zrzut ekranu przedstawiający link do gramatyki na liście aplikacji.](common/all-applications.png)

1. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający kartę aprowizacji.](common/provisioning.png)

1. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Zrzut ekranu pokazujący tryb aprowizacji ustawiony na automatyczny.](common/provisioning-automatic.png)

1. W sekcji **poświadczenia administratora** wpisz w polu wprowadź **adres URL dzierżawy** `https://sso.grammarly.com/scim/v2` , a w polu **token tajny** wprowadź token dostarczony przez gramatykę (patrz krok 2 powyżej). Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z gramatyką. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto gramatyczne ma uprawnienia administratora, i spróbuj ponownie.

    ![Zrzut ekranu pokazujący pola adresu URL dzierżawy i tokenu tajnego.](common/provisioning-testconnection-tenanturltoken.png)

1. W polu **E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Zrzut ekranu przedstawiający pole powiadomień E-mail.](common/provisioning-notification-email.png)

1. Wybierz pozycję **Zapisz**.

1. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy, aby Gramatyka**.

1. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do gramatyki w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu poprawnego dopasowania kont użytkowników w trybie gramatyki w przypadku operacji aktualizacji. Jeśli zmienisz [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), musisz się upewnić, że interfejs API gramatyki obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |externalId|Ciąg|
   |aktywne|Wartość logiczna|
   |displayName|Ciąg|
   |emails[type eq "work"].value|Ciąg|


1. Aby skonfigurować filtry zakresu, zobacz instrukcje zawarte w [samouczku filtr zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

1. Aby włączyć usługę Azure AD Provisioning w celu gramatyki, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Zrzut ekranu przedstawiający stan aprowizacji włączony.](common/provisioning-toggle-on.png)

1. Zdefiniuj użytkowników lub grupy, które chcesz udostępnić do gramatyki, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

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
