---
title: 'Samouczek: Konfigurowanie platformy Fort Zmień chmurę w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników z usługi Azure AD, aby obsłużyć zmiany w chmurze.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ef9a8f5e-0bf0-46d6-8e17-3bcf1a5b0a6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2021
ms.author: Zhchia
ms.openlocfilehash: 52c8fcb20c3ee068a7ae995cd8a81845d21fae81
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98574360"
---
# <a name="tutorial-configure-fortes-change-cloud-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie platformy Fort Zmień chmurę na potrzeby automatycznej aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w obu warstwach: Zmień chmurę i Azure Active Directory (Azure AD), aby skonfigurować automatyczne Inicjowanie obsługi użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i cofa obsługę administracyjną użytkowników i grup do [czterdziestu zmian w chmurze](https://fortesglobal.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w usłudze Forts Change Cloud
> * Usuń użytkowników w czterdziestych zmianach w chmurze, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i czterdziestu zmian w chmurze
> * [Logowanie](fortes-change-cloud-tutorial.md) jednokrotne do usługi Fort w chmurze Zmień (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Usługa Fort zmienia dzierżawę w chmurze.
* Konto użytkownika w systemie Fort zmienia chmurę z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ dane, które mają być [mapowane między usługą Azure AD, a następnie zmień chmurę](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-fortes-change-cloud-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie platformy Fort Zmień chmurę w celu obsługi aprowizacji za pomocą usługi Azure AD

1. Zaloguj się przy użyciu konta administratora, aby obfort zmiany w chmurze. Kliknij **ikonę Ustawienia** , a następnie przejdź do **ustawień Standard scim**.

    [![Na stronie Czterdziesta zmiana ustawienia ](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png) usługi Cloud Standard scim](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png#lightbox)

2. W nowym oknie skopiuj i Zapisz **token podstawowy**. Ta wartość zostanie wprowadzona w polu token tajny na karcie Inicjowanie obsługi administracyjnej zmian w aplikacji w chmurze w Azure Portal.
     
      [" ![ Fort" Zmień token podstawowy w chmurze](media/fortes-change-cloud-provisioning-tutorial/primary-token.png)](media/fortes-change-cloud-provisioning-tutorial/primary-token.png#lightbox)

## <a name="step-3-add-fortes-change-cloud-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie czterdziestych zmian w chmurze z galerii aplikacji usługi Azure AD

Dodanie czterdziestych zmian w chmurze z galerii aplikacji usługi Azure AD w celu rozpoczęcia zarządzania aprowizacji do czterdziestych zmian w chmurze. Jeśli wcześniej skonfigurowano program Fort w chmurze Zmień chmurę na logowanie jednokrotne, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do usługi Forts Change Cloud należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-fortes-change-cloud"></a>Krok 5. Konfigurowanie automatycznej aprowizacji użytkowników w usłudze Forts Change Cloud 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w programie TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-fortes-change-cloud-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi administracyjnej użytkowników w przypadku platformy Fort Zmień chmurę w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Forts Zmień chmurę**.

    ![W przypadku usługi Fort Zmień link w chmurze na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizacja](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Automatyczne Inicjowanie obsługi kart](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź dane, które zostały zmienione przez adres URL dzierżawy w chmurze i token tajny. Kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z usługą Fort w chmurze. Jeśli połączenie nie powiedzie się, upewnij się, że na koncie w chmurze zmieniono uprawnienia administratora i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkownicy, aby obfort zmiany w chmurze**.

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD, aby obsłużą zmiany w chmurze w sekcji **Mapowanie atrybutów** . Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w czterdziestych zmianach w chmurze dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), należy się upewnić, że usługa Forts Change API chmury obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|String|
   |externalId|Ciąg|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: FCC: 2.0: User: administrator|Boolean|
   |urn: IETF: params: Standard scim: schematy: rozszerzenie: FCC: 2.0: User: loginDisabled|Boolean|

  

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning dla systemu Fort Zmień chmurę, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić, aby obsłużyć zmiany w chmurze, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

* Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
* Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
* Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
