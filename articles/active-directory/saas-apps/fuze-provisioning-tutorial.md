---
title: 'Samouczek: Konfigurowanie Fuze dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników w usłudze Fuze.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd
ms.assetid: 34718201-4f0e-4260-9af0-b3b70a1e8265
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: aacaa8ca7e0cd15b34f29479d38d7bc8d95001de
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253719"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie Fuze na potrzeby automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w Fuze i Azure Active Directory (Azure AD) w celu skonfigurowania usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji użytkowników i/lub grup do [Fuze](https://www.fuze.com/). Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../app-provisioning/user-provisioning.md).

> [!NOTE]
> Ten łącznik jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji na temat ogólnych Microsoft Azure warunki użytkowania funkcji w wersji zapoznawczej, zobacz [dodatkowe warunki użytkowania dla Microsoft Azure podglądów](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w Fuze
> * Usuń użytkowników w Fuze, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i Fuze
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/fuze-tutorial) jednokrotne do Fuze (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
* [Dzierżawa Fuze](https://www.fuze.com/).
* Konto użytkownika w Fuze z uprawnieniami administratora.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD i Fuze](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie Fuze w celu obsługi aprowizacji za pomocą usługi Azure AD

Przed skonfigurowaniem usługi Fuze na potrzeby automatycznego inicjowania obsługi administracyjnej użytkowników w usłudze Azure AD należy włączyć obsługę administracyjną Standard scim na Fuze. 

1. Zacznij od skontaktowania się z przedstawicielem usługi Fuze, aby uzyskać następujące wymagane informacje:

    * Lista jednostek SKU produktu Fuze, które są obecnie używane w Twojej firmie.
    * Lista kodów lokalizacji dla lokalizacji firmy.
    * Lista kodów działu dla firmy.

2. Te jednostki SKU i kody można znaleźć w ramach kontraktu Fuze i dokumentów konfiguracyjnych lub kontaktując się z przedstawicielem Fuze.

3. Po otrzymaniu wymagań przedstawiciel Fuze dostarczy token uwierzytelniania Fuze, który jest wymagany do włączenia integracji. Ta wartość zostanie wprowadzona w polu token tajny na karcie aprowizacji aplikacji Fuze w Azure Portal.

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie Fuze z galerii aplikacji usługi Azure AD

Dodaj Fuze z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do Fuze. Jeśli wcześniej skonfigurowano Fuze do logowania jednokrotnego, możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników do Fuze należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj się z małym zestawem użytkowników przed wprowadzeniem ich do wszystkich osób. Jeśli zakres dla aprowizacji jest ustawiony na przypisanych użytkowników, można kontrolować ten sposób, przypisując jeden lub dwóch użytkowników do aplikacji. Gdy zakres jest ustawiony dla wszystkich użytkowników, można określić [Filtr określania zakresu atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników do Fuze 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie Fuze na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi użytkowników dla Fuze w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Fuze**.

    ![Link aplikacji Fuze na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź wartość w polu **podstawowy adres URL Standard scim 2,0 i token uwierzytelniania Standard scim** pobrane wcześniej od przedstawiciela Fuze w **adresie URL dzierżawy** i w **tokenie klucza tajnego**. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może się połączyć z usługą Fuze. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Fuze ma uprawnienia administratora, a następnie spróbuj ponownie.

    ![Token adresu URL dzierżawy](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, i zaznacz pole wyboru — **Wyślij powiadomienie e-mail, gdy wystąpi awaria**.

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

7. Kliknij pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do Fuze**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, do Fuze w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Fuze for Updates. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |userName|Ciąg|
   |Nazwa. imię|Ciąg|
   |Nazwa. rodzina|Ciąg|
   |wiadomości e-mail [Type EQ "Work"]. Value|Ciąg|
   |aktywne|Wartość logiczna|

10. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla Fuze, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić Fuze, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkową synchronizację wszystkich użytkowników i/lub grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Ograniczenia łącznika

* Fuze obsługuje niestandardowe atrybuty Standard scim o nazwie **uprawnienia**. Te atrybuty można tworzyć i nie aktualizować. 

## <a name="change-log"></a>Dziennik zmian

* 06/15/2020 — Limit szybkości integracji dostosowany do 10 żądań na sekundę.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).