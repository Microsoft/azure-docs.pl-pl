---
title: 'Samouczek: Konfigurowanie zawartości dla automatycznej aprowizacji użytkowników przy użyciu Azure Active Directory'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z Azure Active Directory (Azure AD) do zawartości.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516342"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zawartości dla automatycznej aprowizacji użytkowników

W tym artykule opisano kroki, które należy wykonać w temacie zawartość i w Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu usługa Azure AD automatycznie inicjuje i zastrzega użytkownikom i grupom [zawartość](https://www.contentful.com/) przy użyciu usługi Azure AD Provisioning. Aby uzyskać ważne informacje dotyczące działania tej usługi i sposobu jej działania, a także często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu Azure Active Directory](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Obsługiwane funkcje

> [!div class="checklist"]
> * Tworzenie użytkowników w zawartości
> * Usuń użytkowników w zawartości, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i zawartością
> * Udostępnianie grup i członkostwa w grupach w zawartości
> * [Logowanie](contentful-tutorial.md) jednokrotne do zawartości (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące wymagania wstępne:

* [Dzierżawę usługi Azure AD](../develop/quickstart-create-new-tenant.md). 
* Konto użytkownika w usłudze Azure AD, które ma [uprawnienia](../roles/permissions-reference.md) do konfigurowania aprowizacji (na przykład administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny). 
* Konto organizacji z zawartością, które ma subskrypcję obsługującą system do aprowizacji międzydomenowego zarządzania tożsamościami (standard scim). Jeśli masz pytania dotyczące subskrypcji organizacji, skontaktuj się z [pomocą techniczną](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Planowanie wdrożenia aprowizacji

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](../app-provisioning/user-provisioning.md).
2. Określ, kto znajdzie się [w zakresie aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Określ dane, które mają być [mapowane między usługą Azure AD i zawartością](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Konfigurowanie zawartości w celu obsługi aprowizacji za pomocą usługi Azure AD

1. W obszarze zawartość Utwórz konto **użytkownika usługi** . Wszystkie uprawnienia do aprowizacji na platformie Azure są udostępniane za pomocą tego konta. Zalecamy wybranie **właściciela** jako roli organizacji dla tego konta.

2. Zaloguj się do zawartości jako **użytkownik usługi**.

3. W menu po lewej stronie wybierz pozycję **Ustawienia organizacji**  >  **Narzędzia dostępu**  >  **Inicjowanie obsługi użytkowników**.

   ![Zrzut ekranu przedstawiający menu Ustawienia organizacji w obszarze zawartość z wyróżnioną obsługą administracyjną w obszarze narzędzia dostępu.](media/contentful-provisioning-tutorial/access.png)

4. Skopiuj i Zapisz **adres URL Standard scim**. Ta wartość zostanie wprowadzona w Azure Portal na karcie **aprowizacji** aplikacji z zawartością.

5. Wybierz pozycję **Generuj osobisty token dostępu**.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. W oknie modalnym wprowadź nazwę osobistego tokenu dostępu, a następnie wybierz pozycję **Generuj**.

7. Zostanie wygenerowany adres URL Standard scim i token tajny. Skopiuj i Zapisz te wartości. Te wartości zostaną wprowadzone na karcie **aprowizacji** aplikacji zawartości w Azure Portal.

    ![Zrzut ekranu przedstawiający okienko osobistego tokenu dostępu z wyróżnioną pozycją języka C F P a i symbolem zastępczym tokenu.](media/contentful-provisioning-tutorial/token.png)


Jeśli masz pytania podczas konfigurowania aprowizacji w konsoli administracyjnej z zawartością, skontaktuj się z [pomocą techniczną](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Dodawanie zawartości z galerii aplikacji usługi Azure AD

Aby zarządzać obsługą administracyjną zawartości, Dodaj zawartość z galerii aplikacji usługi Azure AD. Jeśli wcześniej skonfigurowano zawartość do logowania jednokrotnego, możesz użyć tej samej aplikacji. Zaleca się jednak utworzenie oddzielnej aplikacji w celu wstępnego przetestowania integracji. Dowiedz się [, jak dodać aplikację w galerii](../manage-apps/add-application-portal.md). 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Określenie, kto znajdzie się w zakresie aprowizacji 

Możesz użyć usługi Azure AD Provisioning, aby określić zakres, który zostanie zainicjowany na podstawie przydziału do aplikacji lub na podstawie atrybutów użytkownika lub grupy. 

Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, wykonaj kroki, aby [przypisać użytkowników i grupy do aplikacji](../manage-apps/assign-user-or-group-access-portal.md).

W przypadku wybrania zakresu, który zostanie zainicjowany w oparciu wyłącznie o atrybuty użytkownika lub grupy, Użyj filtru określania zakresu, aby [zdefiniować reguły warunkowe dla kont użytkowników aprowizacji](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* W przypadku przypisywania użytkowników i grup do zawartości należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy, którzy mają domyślną rolę dostępu, są wykluczeni z aprowizacji i znajdują się w dziennikach aprowizacji jako nieefektywnie uprawnionych. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) , aby dodać więcej ról. 
* Zacznij od mniejszej skali. Przetestuj przy użyciu małego zestawu użytkowników i grup, Zanim przeprowadzisz wszystko do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować zakres, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Konfigurowanie automatycznej aprowizacji użytkowników do zawartości 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup w aplikacji testowej na podstawie przypisań użytkowników lub grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników dla zawartości w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

   ![Zrzut ekranu pokazujący menu aplikacje przedsiębiorstwa w Azure Portal z wyróżnionymi wszystkimi aplikacjami.](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **zawartość**.

   ![Zrzut ekranu pokazujący pierwszych 20 wyników zwróconych na liście aplikacji.](common/all-applications.png)

3. Wybierz kartę **Aprowizacja**.

   ![Zrzut ekranu przedstawiający kartę aprowizacji wyróżnioną w sekcji Zarządzanie w menu po lewej stronie.](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

   ![Zrzut ekranu pokazujący opcje trybu aprowizacji z automatycznym podświetlanym.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź swój zawartość adres URL dzierżawy i token tajny. Aby upewnić się, że usługa Azure AD może nawiązać połączenie z zawartością, wybierz pozycję **Testuj połączenie**. Jeśli połączenie nie powiedzie się, upewnij się, że Twoje konto zawartości ma uprawnienia administratora, a następnie spróbuj ponownie.

   ![Zrzut ekranu przedstawiający pola tekstowe dzierżawy U R L i Secret z wyróżnionym przyciskiem Testuj połączenie.](common/provisioning-testconnection-tenanturltoken.png)

6. W **wiadomości e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail, gdy wystąpi błąd** .

   ![Zrzut ekranu przedstawiający pole tekstowe powiadomienia E-mail.](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz pozycję **Synchronizuj Azure Active Directory użytkowników do zawartości**.

9. W sekcji **Mapowanie atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do zawartości. Atrybuty wybrane jako **pasujące** właściwości są używane w celu dopasowania do kont użytkowników w zawartości dla operacji aktualizacji. Jeśli zdecydujesz się zmienić [pasujący atrybut docelowy](../app-provisioning/customize-application-attributes.md), należy się upewnić, że interfejs API zawartości obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|

10. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do zawartości**.

11. W sekcji **Mapowanie atrybutów** Przejrzyj atrybuty grupy, które są synchronizowane z usługi Azure AD do zawartości. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania do grup w zawartości dla operacji aktualizacji. Wybierz przycisk **Zapisz** , aby zatwierdzić zmiany.

    |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
    |---|---|---|
    |displayName|Ciąg|&check;|
    |elementy członkowskie|Tematy pomocy|

12. Aby skonfigurować filtry zakresu, wykonaj kroki opisane w [samouczku dotyczącym filtru zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Aby włączyć usługę Azure AD Provisioning dla zawartości, w sekcji **Ustawienia** dla **stanu aprowizacji** wybierz pozycję **włączone**.

    ![Zrzut ekranu przedstawiający przełącznik i wyłączanie obsługi stanu aprowizacji.](common/provisioning-toggle-on.png)

14. Aby zdefiniować użytkowników lub grupy, dla których chcesz udostępnić zawartość, w sekcji **Ustawienia** dla **zakresu** wybierz odpowiednią opcję.

    ![Zrzut ekranu przedstawiający opcje, które można wybrać w okienku zakres.](common/provisioning-scope.png)

15. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący przycisk Zapisz i przycisk Anuluj.](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji dla wszystkich użytkowników i grup zdefiniowanych w **zakresie** w obszarze **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="monitor-your-deployment"></a>Monitorowanie wdrożenia

Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

* Aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie, zobacz [dzienniki aprowizacji](../reports-monitoring/concept-provisioning-logs.md).
* Aby sprawdzić stan cyklu inicjowania obsługi administracyjnej i jak zamknąć go, sprawdź [pasek postępu](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przekierowany do kwarantanny. Dowiedz się więcej na temat [Stanów kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
