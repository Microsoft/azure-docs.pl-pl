---
title: 'Samouczek: Konfigurowanie zarządzania drukowaniem w chmurze PaperCut dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD w celu PaperCut zarządzania drukowaniem w chmurze.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7e65d727-2951-4aec-a7a3-7bde49ed09e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: c0738e7033f0aa39ca5f12a44c06d086175eb8fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731470"
---
# <a name="tutorial-configure-papercut-cloud-print-management-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zarządzania drukowaniem w chmurze PaperCut dla automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w ramach zarządzania drukowaniem w chmurze PaperCut i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup w celu [PaperCut zarządzania drukowaniem w chmurze](https://www.papercut.com/products/papercut-pocket/) za pomocą usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Obsługiwane możliwości

> [!div class="checklist"]
> * Tworzenie użytkowników w programie PaperCut Management drukowania w chmurze
> * Usuwanie użytkowników w programie PaperCut Management drukowania w chmurze, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i PaperCut Zarządzanie drukowaniem w chmurze

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md). 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto administratora zarządzania drukowaniem w chmurze PaperCut.


## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ dane, które mają być [mapowane między usługą Azure AD i PaperCut Zarządzanie drukowaniem w chmurze](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-papercut-cloud-print-management-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie zarządzania drukowaniem w chmurze PaperCut w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się do [konsoli administracyjnej PaperCut Pocket](https://pocket.papercut.com/) lub [konsoli administracyjnej programu PaperCut Hive](https://hive.papercut.com/).

2. Przejdź do **dodatków**  >  **wszystkie** Dodatki i Znajdź **Microsoft Azure AD User Sync**.

3. Kliknij pozycję **Dowiedz się więcej** , a następnie kliknij przycisk **Dodaj** , aby zainstalować.



4. Po zainstalowaniu zostanie wyświetlona strona szczegółów dodatku z **adresem URL dzierżawy** i **tokenem tajnym**. Te wartości zostaną wprowadzone w polu adres URL dzierżawy \* i pole tokenu tajnego \* na karcie aprowizacji w aplikacji do zarządzania drukowaniem w chmurze PaperCut w Azure Portal.



## <a name="step-3-add-papercut-cloud-print-management-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie zarządzania drukowaniem w chmurze PaperCut z galerii aplikacji usługi Azure AD

Dodaj Zarządzanie drukowaniem w chmurze PaperCut z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacji do PaperCut Zarządzanie drukowaniem w chmurze. Jeśli wcześniej skonfigurowano PaperCut Zarządzanie drukowaniem w chmurze dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](../manage-apps/add-application-portal.md).

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

* Podczas przypisywania użytkowników i grup do PaperCut zarządzania drukowaniem w chmurze należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md), aby dodać więcej ról.

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-papercut-cloud-print-management"></a>Krok 5. Skonfiguruj automatyczne Inicjowanie obsługi administracyjnej użytkowników w celu PaperCut zarządzania drukowaniem w chmurze

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w usłudze PaperCut w chmurze Management na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-papercut-cloud-print-management-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w celu zarządzania drukowaniem w chmurze PaperCut w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

   ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **PaperCut Zarządzanie drukowaniem w chmurze**.

   ![Link zarządzania drukowaniem w chmurze PaperCut na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

   ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

   ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy zarządzania drukowaniem w chmurze PaperCut i token tajny. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą PaperCut Management drukowania. Jeśli połączenie nie powiedzie się, upewnij się, że konto zarządzania drukowaniem w chmurze PaperCut ma uprawnienia administratora, a następnie spróbuj ponownie.

   ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

   ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Udostępnij Azure Active Directory użytkownikom**.
   ![Mapowanie usługi AAD](media/papercut-cloud-print-management-provisioning-tutorial/mapping.png)

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługą Azure AD, aby PaperCut Zarządzanie drukowaniem w chmurze w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowywania kont użytkowników w usłudze PaperCut Cloud Print Management for Updates. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API zarządzania drukowaniem w chmurze PaperCut obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |displayName|Ciąg|
   |emails[type eq "work"].value|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning w celu zarządzania drukowaniem w chmurze PaperCut, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby PaperCut Zarządzanie drukowaniem w chmurze, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD.

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](../reports-monitoring/concept-provisioning-logs.md), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)