---
title: 'Samouczek: Konfigurowanie Bizagi Studio na potrzeby automatyzacji procesów cyfrowych w celu automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak automatycznie inicjować i cofać obsługę kont użytkowników z usługi Azure AD do Bizagi Studio na potrzeby automatyzacji procesów cyfrowych.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: b739cd50c4d4477d3622350a9a9c96b600794c7d
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632041"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie programu Bizagi Studio do automatyzacji procesów cyfrowych w celu automatycznego aprowizacji użytkowników

W tym samouczku opisano kroki, które należy wykonać w programie Bizagi Studio dla automatyzacji procesów cyfrowych i Azure Active Directory (Azure AD) w celu skonfigurowania automatycznego aprowizacji użytkowników. Po skonfigurowaniu tej opcji usługa Azure AD automatycznie inicjuje i zastrzega użytkownikom i grupom [Bizagi Studio na potrzeby automatyzacji procesów cyfrowych](https://www.bizagi.com/) przy użyciu usługi Azure AD Provisioning. Aby zapoznać się z ważnymi szczegółowymi informacjami na temat przeznaczenia i sposobu działania tej usługi oraz z często zadawanymi pytaniami, zobacz [Automatyzowanie aprowizacji i cofania aprowizacji użytkowników w aplikacjach SaaS przy użyciu usługi Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Obsługiwane funkcje
> [!div class="checklist"]
> * Tworzenie użytkowników w programie Bizagi Studio na potrzeby automatyzacji procesów cyfrowych
> * Usuwanie użytkowników w programie Bizagi Studio na potrzeby automatyzacji procesów cyfrowych, gdy nie wymagają już dostępu
> * Utrzymywanie synchronizacji atrybutów użytkowników między usługą Azure AD i programem Bizagi Studio na potrzeby automatyzacji procesów cyfrowych
> * [Logowanie](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial) jednokrotne do programu Bizagi Studio na potrzeby automatyzacji procesów cyfrowych (zalecane)

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku założono, że masz już następujące elementy:

* [Dzierżawę usługi Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). 
* Konto użytkownika w usłudze Azure AD z [uprawnieniami](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) do konfigurowania aprowizacji. Przykładami mogą być Administratorzy aplikacji, administrator aplikacji w chmurze, właściciel aplikacji lub Administrator globalny. 
* Bizagi Studio for Digital Process Automation w wersji 11.2.4.2 X lub nowszej.

## <a name="plan-your-provisioning-deployment"></a>Planowanie wdrożenia aprowizacji
Wykonaj następujące kroki w celu zaplanowania:

1. Dowiedz się więcej na temat [sposobu działania usługi aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Określ, kto będzie [objęty zakresem aprowizacji](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Określ dane, które mają być [mapowane między usługą Azure AD i Bizagi Studio na potrzeby automatyzacji procesów cyfrowych](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Konfigurowanie obsługi aprowizacji w usłudze Azure AD
Aby skonfigurować program Bizagi Studio do automatyzacji procesów cyfrowych w celu obsługi aprowizacji w usłudze Azure AD, wykonaj następujące kroki:

1. Zaloguj się do portalu służbowego jako użytkownik z **uprawnieniami administratora**.

2. Przejdź do aplikacji **admin**  >  **Security**  >  **OAuth 2**.

   ![Zrzut ekranu z Bizagi, z wyróżnionymi aplikacjami OAuth 2.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Wybierz pozycję **Dodaj**.
4. W obszarze **Typ dotacji** wybierz opcję **token elementu nośnego**. W obszarze **dozwolony zakres** wybierz pozycję **interfejs API** i **Synchronizacja użytkownika**. Następnie wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający rejestrowanie aplikacji z typem udzielenia i dozwolonym zakresem.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Skopiuj i Zapisz **klucz tajny klienta**. W Azure Portal, w przypadku aplikacji Bizagi Studio for Digital Process Automation, na karcie **aprowizacji** wartość klucza tajnego klienta jest wprowadzana w polu **token tajny** .

   ![Zrzut ekranu protokołu OAuth z kluczem tajnym klienta highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Dodawanie aplikacji z galerii usługi Azure AD

Aby rozpocząć zarządzanie obsługą administracyjną programu Bizagi Studio na potrzeby automatyzacji procesów cyfrowych, Dodaj aplikację z galerii aplikacji usługi Azure AD. Jeśli wcześniej skonfigurowano program Bizagi Studio do automatyzacji procesów cyfrowych na potrzeby logowania jednokrotnego, możesz użyć tej samej aplikacji. Po pierwszym przetestowaniu integracji należy jednak utworzyć oddzielną aplikację. Aby uzyskać więcej informacji, zobacz [Szybki Start: Dodawanie aplikacji do dzierżawy usługi Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="define-who-is-in-scope-for-provisioning"></a>Zdefiniuj, kto jest w zakresie aprowizacji 

Za pomocą usługi Azure AD Provisioning można określić zakres, który jest inicjowany na podstawie przypisywania do aplikacji na podstawie atrybutów użytkownika i grupy lub obu tych elementów. Jeśli zakres jest oparty na przypisaniu, zapoznaj się z instrukcjami w sekcji [przypisywanie lub cofanie przypisania użytkowników i grup w przypadku aplikacji przy użyciu interfejs API programu Graph](../manage-apps/assign-user-or-group-access-portal.md) do przypisywania użytkowników i grup do aplikacji. Jeśli zakres jest oparty wyłącznie na atrybutach użytkownika lub grupy, można użyć filtru określania zakresu. Aby uzyskać więcej informacji, zobacz Tworzenie [aplikacji opartych na atrybutach przy użyciu filtrów zakresu](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

Należy zwrócić uwagę na następujące kwestie dotyczące określania zakresu:

* Podczas przypisywania użytkowników i grup do programu Bizagi Studio na potrzeby automatyzacji procesów cyfrowych należy wybrać rolę inną niż **domyślny dostęp**. Użytkownicy z domyślną rolą dostępu są wykluczeni z aprowizacji i są oznaczani w dziennikach aprowizacji jako nieskutecznie uprawnieni. Jeśli jedyną rolą dostępną w aplikacji jest domyślna rola dostępu, można [zaktualizować manifest aplikacji](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) , aby dodać więcej ról. 

* Zacznij od mniejszej skali. Przeprowadź test z użyciem mniejszego zestawu użytkowników i grup, zanim wdrożysz to rozwiązanie dla wszystkich. W przypadku wybrania dla zakresu aprowizacji przypisanych użytkowników i grup można kontrolować ten sposób, przypisując do aplikacji jednego lub dwóch użytkowników lub grupy. Gdy zakres jest ustawiony dla wszystkich użytkowników i grup, można określić [Filtr określania zakresu oparty na atrybutach](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="configure-automatic-user-provisioning"></a>Konfigurowanie automatycznej aprowizacji użytkowników 

Ta sekcja przeprowadzi Cię przez kroki konfigurowania usługi Azure AD Provisioning w celu tworzenia, aktualizowania i wyłączania użytkowników i grup. Wykonujesz to w aplikacji testowej na podstawie przypisań użytkowników i grup w usłudze Azure AD.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Konfigurowanie automatycznej aprowizacji użytkowników w programie Bizagi Studio na potrzeby automatyzacji procesów cyfrowych w usłudze Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Wybierz pozycję **aplikacje dla przedsiębiorstw**  >  **wszystkie aplikacje**.

    ![Zrzut ekranu Azure Portal, z wyróżnionymi aplikacjami dla przedsiębiorstw i wszystkimi aplikacjami.](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Bizagi Studio na potrzeby automatyzacji procesów cyfrowych**.

3. Wybierz kartę **Aprowizacja**.

    ![Zrzut ekranu przedstawiający opcje zarządzania z wyróżnioną obsługą administracyjną.](common/provisioning.png)

4. Ustaw **tryb aprowizacji** na **automatyczny**.

    ![Kontrolka trybu aprowizacji Screenshotof z automatycznym podświetlaniem.](common/provisioning-automatic.png)

5. W sekcji **poświadczenia administratora** wprowadź adres URL dzierżawy i token tajny dla Bizagi Studio do automatyzacji procesów cyfrowych. 

      * **Adres URL dzierżawy:** Wprowadź punkt końcowy Bizagi Standard scim o następującej strukturze:  `<Your_Bizagi_Project>/scim/v2/` .
         Na przykład: `https://my-company.bizagi.com/scim/v2/`.

      * **Token tajny:** Ta wartość jest pobierana z kroku opisanego wcześniej w tym artykule.

      Aby upewnić się, że usługa Azure AD może nawiązać połączenie z programem Bizagi Studio w celu automatyzacji procesów cyfrowych, wybierz pozycję **Testuj połączenie**. Jeśli połączenie nie powiedzie się, upewnij się, że konto usługi Bizagi Studio for Digital Process Automation ma uprawnienia administratora, a następnie spróbuj ponownie.

   ![Zrzut ekranu z poświadczeniami administratora z wyróżnionym połączeniem testowym.](common/provisioning-testconnection-tenanturltoken.png)

6. W polu **wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, które powinny otrzymywać powiadomienia o błędach aprowizacji. Wybierz opcję **wysyłania powiadomienia e-mail w przypadku wystąpienia błędu**.

    ![Zrzut ekranu przedstawiający opcje powiadomień E-mail.](common/provisioning-notification-email.png)

7. Wybierz pozycję **Zapisz**.

8. W sekcji **mapowania** wybierz kolejno pozycje **Synchronizuj Azure Active Directory użytkownicy do Bizagi Studio na potrzeby automatyzacji procesów cyfrowych**.

9. W sekcji **Mapowanie atrybutów** Przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Bizagi Studio na potrzeby automatyzacji procesów cyfrowych. Atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w programie Bizagi Studio do automatyzacji procesów cyfrowych dla operacji aktualizacji. W przypadku zmiany [pasującego atrybutu Target](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)należy upewnić się, że interfejs API automatyzacji procesów Bizagi Studio for Digital Process obsługuje filtrowanie użytkowników na podstawie tego atrybutu. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.

   |Atrybut|Typ|Obsługiwane na potrzeby filtrowania|
   |---|---|---|
   |userName|Ciąg|&check;|
   |aktywne|Wartość logiczna|
   |emails[type eq "work"].value|Ciąg|
   |name.givenName|Ciąg|
   |name.familyName|Ciąg|
   |Nazwa. sformatowana|Ciąg|
   |phoneNumbers[type eq "mobile"].value|Ciąg|

   Niestandardowe atrybuty rozszerzeń można dodać, przechodząc do **opcji Pokaż opcje zaawansowane > Edytuj listę atrybutów dla Bizagi**. Atrybuty rozszerzenia niestandardowego muszą być poprzedzone prefiksem **urn: IETF: params: Standard scim: schematy: Extension: Bizagi: 2.0: UserProperties:**. Na przykład jeśli atrybut niestandardowego rozszerzenia to **IdentificationNumber**, atrybut musi być dodany jako **urn: IETF: params: Standard scim: schematy: Extension: Bizagi: 2.0: UserProperties: IdentificationNumber**. Wybierz pozycję **Zapisz** , aby zatwierdzić wszelkie zmiany.
   
    ![Edytuj listę atrybutów.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Więcej informacji na temat dodawania atrybutów niestandardowych można znaleźć w temacie [Dostosowywanie atrybutów aplikacji](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).


10. Aby skonfigurować filtry zakresu, zobacz [samouczek filtru określania zakresu](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Aby włączyć usługę Azure AD Provisioning for Bizagi Studio na potrzeby automatyzacji procesów cyfrowych, w sekcji **Ustawienia** Zmień **stan aprowizacji** na **włączone**.

    ![Zrzut ekranu przedstawiający przełącznik stanu aprowizacji.](common/provisioning-toggle-on.png)

12. Zdefiniuj użytkowników i grupy, które chcesz udostępnić Bizagi Studio na potrzeby automatyzacji procesów cyfrowych. W sekcji **Ustawienia** wybierz odpowiednie wartości w **zakresie**.

    ![Zrzut ekranu przedstawiający opcje zakresu.](common/provisioning-scope.png)

13. Gdy wszystko będzie gotowe do udostępnienia, wybierz pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający formant Save.](common/provisioning-configuration-save.png)

Ta operacja spowoduje rozpoczęcie cyklu synchronizacji początkowej wszystkich użytkowników i grup zdefiniowanych w obszarze **Zakres** w sekcji **Ustawienia**. Cykl początkowy trwa dłużej niż kolejne, które mają miejsce co około 40 minut w czasie działania usługi aprowizacji Azure AD. 

## <a name="monitor-your-deployment"></a>Monitorowanie wdrożenia
Po skonfigurowaniu aprowizacji Użyj następujących zasobów do monitorowania wdrożenia:

- Użyj [dzienników aprowizacji](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) , aby określić, którzy użytkownicy zostali zainicjowani pomyślnie lub niepomyślnie.
- Sprawdź [pasek postępu](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) , aby zobaczyć stan cyklu aprowizacji oraz sposób jego zakończenia.
- Jeśli konfiguracja aprowizacji jest w złej kondycji, aplikacja zostanie przestawiona na kwarantannę. Aby uzyskać więcej informacji, zobacz [Inicjowanie obsługi aplikacji w stanie kwarantanny](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie aprowizacją kont użytkowników w aplikacjach dla przedsiębiorstw](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../manage-apps/check-status-user-account-provisioning.md)
