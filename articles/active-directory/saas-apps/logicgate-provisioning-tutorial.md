---
title: 'Samouczek: konfigurowanie usługi LogicGate pod celu automatycznego aprowizowania użytkowników za pomocą Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie aprowizować i deprowizować konta użytkowników z usługi Azure AD do usługi LogicGate.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: eea988ef-b0f1-4d22-b867-310f167540c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2021
ms.author: Zhchia
ms.openlocfilehash: c9c938ab344a7d861af713fa42e2e39afa1df1b3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589469"
---
# <a name="tutorial-configure-logicgate-for-automatic-user-provisioning"></a>Samouczek: konfigurowanie usługi LogicGate na celu automatyczną aprowizowanie użytkowników

W tym samouczku opisano kroki, które należy wykonać w obu usługach LogicGate i Azure Active Directory (Azure AD), aby skonfigurować automatyczną aprowizowanie użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie aprowizuje i usuwa aprowizowanie użytkowników i grup w usłudze [LogicGate](https://www.logicgate.com) przy użyciu usługi Azure AD Provisioning Service. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w bramie LogicGate
> * Usuwanie użytkowników w bramie LogicGate, gdy nie wymagają już dostępu
> * Synchronizowanie atrybutów użytkownika między usługami Azure AD i LogicGate

## <a name="prerequisites"></a>Wymagania wstępne

W scenariuszu opisanym w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. administratora aplikacji, administratora aplikacji w chmurze, właściciela aplikacji lub administratora globalnego). 
* Dzierżawa usługi LogicGate z włączonym planem Enterprise lub lepszym rozwiązaniem.
* Konto użytkownika w bramie LogicGate z uprawnieniami administratora.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto znajdzie się [w zakresie aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, jakie dane mają [być mapowe między usługą Azure AD i bramą LogicGate.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-logicgate-to-support-provisioning-with-azure-ad"></a>Krok 2. Konfigurowanie usługi LogicGate w celu obsługi aprowacji w usłudze Azure AD

1. Zaloguj się do **konsoli administracyjnej usługi LogicGate.** Przejdź do karty **Narzędzia główne** i kliknij **ikonę Profilu** w prawym górnym rogu.
2. Przejdź do **klucza** **>** **dostępu profilu.**

    ![Karta Profil](./media/logicgate-provisioning-tutorial/profile.png)

3. Kliknij pozycję **Generuj klucz dostępu.** 
    
    ![Karta Dostęp](./media/logicgate-provisioning-tutorial/key.png)

4. Skopiuj i zapisz **klucz dostępu**. Ta wartość zostanie wprowadzona w polu **Token** tajny * na karcie Inicjowanie obsługi aplikacji LogicGate w Azure Portal. 
    
    ![Karta Klawisz](./media/logicgate-provisioning-tutorial/access.png)

## <a name="step-3-add-logicgate-from-the-azure-ad-application-gallery"></a>Krok 3. Dodawanie aplikacji LogicGate z galerii aplikacji usługi Azure AD

Dodaj aplikację LogicGate z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizowaniem w usłudze LogicGate. Jeśli wcześniej s konfigurowano aplikację LogicGate dla logowania jednokrotnego, możesz użyć tej samej aplikacji. Zalecane jest jednak utworzenie osobnej aplikacji na potrzeby początkowych testów integracji. Więcej informacji o dodawaniu aplikacji z galerii znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Krok 4. Określenie, kto znajdzie się w zakresie aprowizacji 

Usługa aprowizacji Azure AD umożliwia określenie zakresu aprowizacji na podstawie przypisania do aplikacji lub na podstawie atrybutów użytkownika/grupy. Jeśli zdecydujesz się na określenie zakresu aprowizacji w aplikacji na podstawie przypisania, możesz skorzystać z następujących [instrukcji](../manage-apps/assign-user-or-group-access-portal.md) w celu przypisania użytkowników i grup do aplikacji. Jeśli zdecydujesz się na określenie zakresu aprowizacji wyłącznie na podstawie atrybutów użytkownika lub grupy, możesz użyć filtra zakresu zgodnie z opisem zamieszczonym [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do bramy LogicGate należy wybrać rolę inną niż **Domyślny dostęp.** Użytkownicy z rolą Dostęp domyślny są wykluczeni z aprowizacji, a w dziennikach aprowizacji zostaną oznaczeni jako niemający skutecznego uprawnienia. Jeśli jedyną rolą dostępną w aplikacji jest Dostęp domyślny, możesz [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps), aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku ustawienia zakresu aprowizacji na przypisanych użytkowników i grupy możesz w tym celu przypisać do aplikacji jednego czy dwóch użytkowników bądź jedną lub dwie grupy. W przypadku ustawienia zakresu na wszystkich użytkowników i wszystkie grupy, możesz określić [filtrowanie zakresu na podstawie atrybutów](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-logicgate"></a>Krok 5. Konfigurowanie automatycznego aprowizowania użytkowników w bramie LogicGate 

Ta sekcja zawiera instrukcje konfigurowania usługi aprowizowania usługi Azure AD w celu tworzenia, aktualizowania i wyłączania użytkowników i/lub grup w aplikacji TestApp na podstawie przypisań użytkowników i/lub grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-logicgate-in-azure-ad"></a>Aby skonfigurować automatyczną aprowizowanie użytkowników dla aplikacji LogicGate w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **LogicGate.**

    ![Link do aplikacji LogicGate na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

    ![Karta Aprowizuj](common/provisioning.png)

4. Ustaw **Tryb aprowizacji** na **Automatyczny**.

    ![Karta Automatyczna](common/provisioning-automatic.png)

5. W sekcji **Poświadczenia administratora wprowadź** adres URL dzierżawy LogicGate i token tajny. Kliknij **pozycję Testuj połączenie,** aby upewnić się, że usługa Azure AD może nawiązać połączenie z bramą LogicGate. Jeśli połączenie nie powiedzie się, upewnij się, że konto LogicGate ma uprawnienia administratora, i spróbuj ponownie.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **Adres e-mail do powiadomień** wpisz adres e-mail osoby lub grupy, która ma otrzymywać powiadomienia o błędach autoryzacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail w przypadku wystąpienia błędu**.

    ![Adres e-mail do powiadomień](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **Mapowania wybierz** pozycję Synchronizuj użytkowników Azure Active Directory **z bramą LogicGate.**

9. Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do bramy LogicGate w **sekcji Mapowanie** atrybutów. Atrybuty wybrane jako właściwości **dopasowania** są używane do dopasowywania kont użytkowników w bramie LogicGate w przypadku operacji aktualizacji. Jeśli zdecydujesz się zmienić pasujący [atrybut docelowy](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), upewnij się, że interfejs API LogicGate obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz,** aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane filtrowanie|
   |---|---|---|
   |userName|Ciąg|&check;|
   |emails[type eq "work"].value|Ciąg|
   |aktywne|Wartość logiczna|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|

10. Aby skonfigurować filtry zakresu, skorzystaj z instrukcji przedstawionych w [samouczku dotyczącym filtrów zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę aprowizowania Usługi  Azure AD dla bramy LogicGate, zmień stan aprowacji na **Wł.** w **sekcji** Ustawienia.

    ![Stan aprowizacji — przełącznik w pozycji włączonej](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i/lub grupy, które chcesz aprowizować  w bramie LogicGate, wybierając odpowiednie wartości w sekcji Zakres **w sekcji** Ustawienia.

    ![Zakres aprowizacji](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do rozpoczęcia aprowizacji, kliknij pozycję **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="step-6-monitor-your-deployment"></a>Krok 6. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji możesz skorzystać z następujących zasobów, aby monitorować wdrożenie:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs), aby określić, których użytkowników udało się lub nie udało aprowizować
2. Sprawdź [pasek postępu](/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user), aby zapoznać się ze stanem cyklu aprowizacji i czasem pozostałym do jego zakończenia
3. Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja przejdzie w stan kwarantanny. Więcej informacji o stanach kwarantanny znajdziesz [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
