---
title: 'Samouczek: Inicjowanie obsługi administracyjnej użytkowników dla zapasu czasu — Azure AD'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie inicjować udostępnianie kont użytkowników i cofać ich obsługę zapasu czasu.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2020
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ca536ddacb0f81459625b733eb79282e145afba
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87016338"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie zapasu czasu dla automatycznej aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać w ramach zapasu czasu i usługi Azure AD w celu automatycznego aprowizacji i cofania aprowizacji kont użytkowników z usługi Azure AD w celu zapewnienia zapasu. Aby uzyskać ważne informacje o tym, jak działa ta usługa, jak ona dotyczy, i często zadawanych pytań, zobacz [Automatyzowanie aprowizacji użytkowników i Anulowanie udostępniania aplikacji SaaS przy użyciu programu Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane możliwości
> [!div class="checklist"]
> * Tworzenie użytkowników w zapasach czasu
> * Usuń użytkowników w zapasach czasowych, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD a zapasem czasu
> * Udostępnianie grup i członkostwa w grupach w zapasach czasu
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial) jednokrotne do zapasu czasu (zalecane)


## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* [Dzierżawa usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant).
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji (np. Administrator aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny).
* Dzierżawa zapasowa z [planem Plus](https://aadsyncfabric.slack.com/pricing) lub lepsza.
* Konto użytkownika w zapasie czasu z uprawnieniami administratora zespołu.

## <a name="step-1-plan-your-provisioning-deployment"></a>Krok 1. Planowanie wdrożenia aprowizacji
1. Dowiedz się [, jak działa usługa aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ, które dane mają być [mapowane między usługą Azure AD a zapasem czasu](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>Krok 2. Dodawanie czasu zapasowego z galerii aplikacji usługi Azure AD

Dodaj zapas czasu z galerii aplikacji usługi Azure AD, aby rozpocząć zarządzanie aprowizacjim do zapasu czasu. W przypadku wcześniejszego skonfigurowania zapasowego czasu na potrzeby logowania jednokrotnego możesz użyć tej samej aplikacji. Jednak zaleca się utworzenie osobnej aplikacji podczas wstępnego testowania integracji. Dowiedz się więcej o dodawaniu aplikacji z galerii [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>Krok 3. Zdefiniuj, kto będzie w zakresie aprowizacji 

Usługa Azure AD Provisioning umożliwia określenie zakresu użytkowników, którzy będą obsługiwani w oparciu o przypisanie do aplikacji i lub na podstawie atrybutów użytkownika/grupy. Jeśli wybierzesz zakres, który zostanie zainicjowany do aplikacji na podstawie przypisania, możesz wykonać następujące [kroki](../manage-apps/assign-user-or-group-access-portal.md) , aby przypisać użytkowników i grupy do aplikacji. Jeśli zdecydujesz się na określenie zakresu, który zostanie zainicjowany na podstawie atrybutów użytkownika lub grupy, możesz użyć filtru określania zakresu, zgodnie z opisem w [tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* Podczas przypisywania użytkowników i grup do zapasu czasu należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i zostaną oznaczeni jako nieskutecznie uprawnieni do dzienników aprowizacji. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać dodatkowe role. 

* Zacznij od małych. Przetestuj przy użyciu małego zestawu użytkowników i grup przed przekazaniem ich do wszystkich osób. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu na podstawie atrybutu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>Krok 4. Konfigurowanie automatycznego aprowizacji użytkowników do zapasu czasu 

Ta sekcja przeprowadzi Cię przez proces łączenia się z interfejsem API inicjowania obsługi konta użytkownika usługi Azure AD i konfigurowania usługi aprowizacji w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w zapasach czasu na podstawie przypisywania użytkowników i grup w usłudze Azure AD.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Aby skonfigurować automatyczne Inicjowanie obsługi konta użytkownika w usłudze Azure AD:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Slack**.

    ![Link do usługi Slack na liście aplikacji](common/all-applications.png)

3. Wybierz kartę **aprowizacji** .

    ![Karta aprowizacji](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Karta aprowizacji](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** kliknij przycisk **Autoryzuj**. Spowoduje to otwarcie okna dialogowego autoryzacji zapasowych w nowym oknie przeglądarki.

    ![Autoryzacja](media/slack-provisioning-tutorial/authorization.png)


6. W nowym oknie, zaloguj się do zapasu czasu przy użyciu konta administratora Twojego zespołu. w oknie dialogowym uzyskana autoryzacja wybierz zespół zapasowy, dla którego chcesz włączyć obsługę administracyjną, a następnie wybierz pozycję **Autoryzuj**. Po zakończeniu Wróć do Azure Portal, aby zakończyć konfigurację aprowizacji.

    ![Okno dialogowe autoryzacji](./media/slack-provisioning-tutorial/slackauthorize.png)

7. W Azure Portal kliknij pozycję **Testuj połączenie** , aby upewnić się, że usługa Azure AD może nawiązać połączenie z aplikacją czasu zapasu. Jeśli połączenie nie powiedzie się, upewnij się, że konto zapasowe ma uprawnienia administratora zespołu, a następnie spróbuj ponownie wykonać krok "Autoryzuj".

8. W polu **adres E-mail powiadomienia** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji, a następnie zaznacz pole wyboru **Wyślij powiadomienie e-mail po wystąpieniu błędu** .

    ![Wiadomość E-mail z powiadomieniem](common/provisioning-notification-email.png)

9. Wybierz pozycję **Zapisz**.

10. W sekcji mapowania wybierz pozycję **synchronizuj Azure Active Directory użytkownicy do zapasu czasu**.

11. W sekcji **mapowania atrybutów** Przejrzyj atrybuty użytkownika, które zostaną zsynchronizowane z usługi Azure AD do zapasu czasu. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane w celu dopasowania do kont użytkowników w zapasach czasu dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

   |Atrybut|Typ|
   |---|---|
   |aktywne|Boolean (wartość logiczna)|
   |externalId|String (ciąg)|
   |displayName|String (ciąg)|
   |Nazwa. rodzina|String (ciąg)|
   |Nazwa. imię|String (ciąg)|
   |title|String (ciąg)|
   |wiadomości e-mail [Type EQ "Work"]. Value|String (ciąg)|
   |userName|String (ciąg)|
   |Pseudonim|String (ciąg)|
   |adresy [typ EQ "untyped"]. streetAddress|String (ciąg)|
   |adresy [Type EQ "untyped"]. locale|String (ciąg)|
   |adresy [Type EQ "untyped"]. region|String (ciąg)|
   |adresy [Type EQ "untyped"]. KodPocztowy|String (ciąg)|
   |adresy [Type EQ "untyped"]. Country|String (ciąg)|
   |numer telefonu [typ EQ "Mobile"]. Value|String (ciąg)|
   |numer telefonu [typ EQ "Work"]. wartość|String (ciąg)|
   |role [podstawowa EQ "true"]. Value|String (ciąg)|
   |locale|String (ciąg)|
   |Name. honorificPrefix|String (ciąg)|
   |fotografie [typ EQ "Fotografia"]. wartość|String (ciąg)|
   |profileUrl|String (ciąg)|
   |TimeZone|String (ciąg)|
   |userType|String (ciąg)|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. Department|String (ciąg)|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. Manager|Dokumentacja|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. employeeNumber|String (ciąg)|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. costCenter|String (ciąg)|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. Organization|String (ciąg)|
   |urn: Standard scim: schematy: rozszerzenie: Enterprise: 1.0. dzielenie|String (ciąg)|

12. W sekcji **mapowania** wybierz pozycję **Synchronizuj grupy Azure Active Directory do zapasu czasu**.

13. W sekcji **mapowania atrybutów** Przejrzyj atrybuty grupy, które będą synchronizowane z usługi Azure AD do zapasu czasu. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości będą używane do dopasowania do grup w zapasach czasu dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić zmiany.

      |Atrybut|Typ|
      |---|---|
      |displayName|String (ciąg)|
      |elementy członkowskie|Dokumentacja|

14. Aby skonfigurować filtry określania zakresu, zapoznaj się z poniższymi instrukcjami w [samouczku dotyczącym filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

15. Aby włączyć usługę Azure AD Provisioning dla zapasu czasu, Zmień **stan aprowizacji** na **włączone** w sekcji **Ustawienia** .

    ![Stan aprowizacji jest przełączany](common/provisioning-toggle-on.png)

16. Zdefiniuj użytkowników i/lub grupy, które chcesz udostępnić do zapasu czasu, wybierając odpowiednie wartości w **zakresie** w sekcji **Ustawienia** .

    ![Zakres aprowizacji](common/provisioning-scope.png)

17. Gdy wszystko będzie gotowe do udostępnienia, kliknij przycisk **Zapisz**.

    ![Zapisywanie konfiguracji aprowizacji](common/provisioning-configuration-save.png)

Ta operacja uruchamia początkowy cykl synchronizacji wszystkich użytkowników i grup zdefiniowanych w **zakresie** w sekcji **Ustawienia** . Cykl początkowy trwa dłużej niż kolejne cykle, które wystąpiły co około 40 minut, o ile usługa Azure AD Provisioning jest uruchomiona. 

## <a name="step-5-monitor-your-deployment"></a>Krok 5. Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

1. Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie
2. Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
3. Jeśli konfiguracja aprowizacji wydaje się być w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Więcej informacji o Stanach kwarantanny znajduje się [tutaj](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

* Podczas konfigurowania atrybutu **DisplayName** zapasu należy pamiętać o następujących zachowaniach:

  * Wartości nie są całkowicie unikatowe (np. 2 Użytkownicy mogą mieć taką samą nazwę wyświetlaną)

  * Obsługuje znaki inne niż angielskie, spacje i wielkie litery. 
  
  * Dozwolone znaki interpunkcyjne obejmują kropki, podkreślenia, łączniki, apostrofy, nawiasy (np. **[{}])**) i separatory (np. **,/;**).
  
  * Właściwość displayName nie może zawierać znaku "@". W przypadku uwzględnienia znaku "@" można znaleźć pominięte zdarzenie w dziennikach aprowizacji z opisem "AttributeValidationFailed".

  * Tylko aktualizacje, jeśli są skonfigurowane te dwa ustawienia w obszarze roboczym zapasu/ **Synchronizacja profilu organizacji jest włączona** , a **Użytkownicy nie mogą zmieniać ich nazwy wyświetlanej**.

* Atrybut **nazwy użytkownika** zapasu musi znajdować się w zakresie 21 znaków i mieć unikatową wartość.

* Zapasy czasu umożliwiają dopasowanie z użyciem atrybutów **username** i **email**.  
  
* Typowe kody erorr są udokumentowane w oficjalnej dokumentacji zapasowej —https://api.slack.com/scim#errors

## <a name="change-log"></a>Dziennik zmian

* 06/16/2020 — modyfikowany atrybut DisplayName do zaktualizowania tylko podczas tworzenia nowego użytkownika.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../manage-apps/check-status-user-account-provisioning.md)