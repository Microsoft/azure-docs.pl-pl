---
title: 'Samouczek: Konfigurowanie zapisywania zwrotnego SuccessFactors w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować zapisywanie zwrotne atrybutów SuccessFactors z usługi Azure AD
services: active-directory
author: cmmdesai
documentationcenter: na
manager: jodadzie
ms.assetid: ad255bd4-9e50-43a1-a92b-359215868b6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2019
ms.author: chmutali
ms.openlocfilehash: 2de0cdd32428884170f549afacdbd52c3a10c93f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77060052"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-sap-successfactors-preview"></a>Samouczek: Konfigurowanie zapisywania zwrotnego atrybutów z usługi Azure AD do oprogramowania SAP SuccessFactors (wersja zapoznawcza)
Celem tego samouczka jest wyświetlenie czynności, które należy wykonać w celu uzyskania atrybutów zapisywania zwrotnego z usługi Azure AD do SuccessFactors Employee Central. Jedynym atrybutem, który jest obecnie obsługiwany dla zapisywania zwrotnego jest atrybut poczty e-mail. 

## <a name="overview"></a>Omówienie

Po skonfigurowaniu integracji przychodzącej aprowizacji przy użyciu funkcji [SuccessFactors do lokalnej aplikacji do aprowizacji usługi AD](sap-successfactors-inbound-provisioning-tutorial.md) lub [SuccessFactors w aplikacji Azure AD](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) Provisioning można opcjonalnie skonfigurować aplikację zapisywania zwrotnego SuccessFactors, aby zapisać adres E-mail z powrotem do SuccessFactors. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

To rozwiązanie SuccessFactors zapisywania zwrotnego użytkownika jest idealnym rozwiązaniem dla:

* Organizacje korzystające z pakietu Office 365, które chcą zapisać zwrotnie autorytatywnych atrybutów zarządzanych przez dział IT (na przykład adres e-mail) z powrotem do SuccessFactors

## <a name="configuring-successfactors-for-the-integration"></a>Konfigurowanie SuccessFactors na potrzeby integracji

Typowym wymaganiem wszystkich łączników aprowizacji SuccessFactors jest to, że wymagają poświadczeń konta SuccessFactors z uprawnieniami do wywoływania interfejsów API usługi SuccessFactors OData. W tej sekcji opisano kroki tworzenia konta usługi w programie SuccessFactors i udzielania odpowiednich uprawnień. 

* [Utwórz/Zidentyfikuj konto użytkownika interfejsu API w SuccessFactors](#createidentify-api-user-account-in-successfactors)
* [Utwórz rolę uprawnień interfejsu API](#create-an-api-permissions-role)
* [Tworzenie grupy uprawnień dla użytkownika interfejsu API](#create-a-permission-group-for-the-api-user)
* [Udziel uprawnienia roli do grupy uprawnień](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>Utwórz/Zidentyfikuj konto użytkownika interfejsu API w SuccessFactors
Skontaktuj się z zespołem administracyjnym SuccessFactors lub partnerem implementacji, aby utworzyć lub zidentyfikować konto użytkownika w usłudze SuccessFactors, które będzie używane do wywoływania interfejsów API usługi OData. Poświadczenia nazwy użytkownika i hasła tego konta będą wymagane podczas konfigurowania aplikacji aprowizacji w usłudze Azure AD. 

### <a name="create-an-api-permissions-role"></a>Utwórz rolę uprawnień interfejsu API

* Zaloguj się do platformy SAP SuccessFactors przy użyciu konta użytkownika, które ma dostęp do centrum administracyjnego.
* Wyszukaj pozycje *Zarządzaj rolami uprawnień*, a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.
  ![Zarządzanie rolami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)
* Na liście rola uprawnień kliknij pozycję **Utwórz nową**.
  > [!div class="mx-imgBorder"]
  > ![Utwórz nową rolę uprawnienia](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)
* Dodaj nazwę i **Opis** **roli** dla nowej roli uprawnienia. Nazwa i opis powinny wskazywać, że rola ma uprawnienia do użycia interfejsu API.
  > [!div class="mx-imgBorder"]
  > ![Szczegóły roli uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)
* W obszarze Ustawienia uprawnień kliknij pozycję **uprawnienie...**, a następnie przewiń w dół listę uprawnień i kliknij pozycję **Zarządzaj narzędzia integracji**. Zaznacz pole wyboru **Zezwalaj administratorowi na dostęp do interfejsu API OData za pomocą uwierzytelniania podstawowego**.
  > [!div class="mx-imgBorder"]
  > ![Zarządzanie narzędziami integracji](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)
* Przewiń w dół do tego samego pola i wybierz pozycję **Employee Central API**. Dodaj uprawnienia, jak pokazano poniżej, aby czytać przy użyciu interfejsu API ODATA i edytować za pomocą interfejsu API ODATA. Wybierz opcję Edytuj, jeśli planujesz używać tego samego konta do scenariusza zapisywania zwrotnego SuccessFactors. 
  > [!div class="mx-imgBorder"]
  > ![Odczyt uprawnień do zapisu](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)
* Kliknij przycisk **gotowe**. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-a-permission-group-for-the-api-user"></a>Tworzenie grupy uprawnień dla użytkownika interfejsu API

* W centrum administracyjnym SuccessFactors Wyszukaj pozycję *Zarządzaj grupami uprawnień*, a następnie wybierz pozycję **Zarządzaj grupami uprawnień** z wyników wyszukiwania.
  > [!div class="mx-imgBorder"]
  > ![Zarządzaj grupami uprawnień](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)
* W oknie Zarządzanie grupami uprawnień kliknij pozycję **Utwórz nowe**.
  > [!div class="mx-imgBorder"]
  > ![Dodaj nową grupę](./media/sap-successfactors-inbound-provisioning/create-new-group.png)
* Dodaj nazwę grupy dla nowej grupy. Nazwa grupy powinna wskazywać, że grupa jest przeznaczony dla użytkowników interfejsu API.
  > [!div class="mx-imgBorder"]
  > ![Nazwa grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)
* Dodaj członków do grupy. Można na przykład wybrać pozycję **Nazwa użytkownika** z menu rozwijanego Pula osób, a następnie wprowadzić nazwę użytkownika konta interfejsu API, która będzie używana do integracji. 
  > [!div class="mx-imgBorder"]
  > ![Dodawanie członków grupy](./media/sap-successfactors-inbound-provisioning/add-group-members.png)
* Kliknij pozycję **gotowe** , aby zakończyć tworzenie grupy uprawnień.

### <a name="grant-permission-role-to-the-permission-group"></a>Udziel uprawnienia roli do grupy uprawnień

* W centrum administracyjnym SuccessFactors Wyszukaj pozycję *Zarządzaj rolami uprawnień*, a następnie wybierz pozycję **Zarządzaj rolami uprawnień** z wyników wyszukiwania.
* Z **listy rola uprawnień**wybierz rolę utworzoną na potrzeby uprawnień użycia interfejsu API.
* W obszarze **Przypisz tę rolę do...** kliknij przycisk **Dodaj.** ...
* Z menu rozwijanego wybierz pozycję **Grupa uprawnień** , a następnie kliknij pozycję **Wybierz...** , aby otworzyć okno grupy, aby wyszukać i wybrać utworzoną powyżej grupę. 
  > [!div class="mx-imgBorder"]
  > ![Dodaj grupę uprawnień](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Przejrzyj uprawnienia przyznane grupie uprawnień. 
  > [!div class="mx-imgBorder"]
  > ![Szczegóły roli i grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kliknij przycisk **Zapisz zmiany**.

## <a name="configuring-successfactors-writeback"></a>Konfigurowanie zapisywania zwrotnego SuccessFactors

Ta sekcja zawiera kroki dla programu 

* [Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurowanie mapowań atrybutów](#part-2-configure-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Część 1. Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors

**Aby skonfigurować zapisywanie zwrotne SuccessFactors:**

1. Przejdź do strony <https://portal.azure.com>

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**

3. Wybierz **aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację**, a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj funkcję **zapisywania zwrotnego SuccessFactors**i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **aprowizacji**

7. Zmień tryb **aprowizacji** **Mode** na **automatyczny**

8. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta użytkownika interfejsu API SuccessFactors z DOŁĄCZONYm identyfikatorem firmy. Ma format: **username\@companyID**

   * **Hasło administratora —** Wprowadź hasło dla konta użytkownika interfejsu API SuccessFactors. 

   * **Adres URL dzierżawy —** Wprowadź nazwę punktu końcowego usług interfejsu API OData SuccessFactors. Wprowadź tylko nazwę hosta serwera bez protokołu HTTP lub https. Ta wartość powinna wyglądać następująco: **API-Server-Name.SuccessFactors.com**.

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".
    > [!NOTE]
    > Usługa Azure AD Provisioning wysyła powiadomienie e-mail, jeśli zadanie aprowizacji przejdzie do stanu [kwarantanny](/azure/active-directory/manage-apps/application-provisioning-quarantine-status) .

   * Kliknij przycisk **Testuj połączenie** . Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia i adres URL SuccessFactors są prawidłowe.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **mapowania** zostanie wyświetlone mapowanie domyślne **Azure Active Directory użytkowników do SuccessFactors**

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych przez użytkownika z SuccessFactors do Active Directory.

1. Na karcie Inicjowanie obsługi w obszarze **mapowania**kliknij pozycję **Synchronizuj Azure Active Directory użytkowników, aby SuccessFactors**.

1. W polu **Zakres obiektu źródłowego** możesz wybrać, które zestawy użytkowników w usłudze Azure AD mają być uwzględniane w przypadku zapisywania zwrotnego przez zdefiniowanie zestawu filtrów opartych na atrybutach. Zakresem domyślnym jest "Wszyscy użytkownicy w usłudze Azure AD". 
   > [!TIP]
   > Podczas konfigurowania aplikacji do aprowizacji po raz pierwszy należy przetestować i zweryfikować mapowania atrybutów oraz wyrażenia, aby upewnić się, że daje żądany wynik. Firma Microsoft zaleca używanie filtrów określania zakresu w obszarze **zakres obiektów źródłowych** do testowania mapowań za pomocą kilku użytkowników testowych z usługi Azure AD. Po sprawdzeniu, czy mapowania działają, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Pole **akcji obiektu docelowego** obsługuje tylko operację **aktualizacji** .

1. W sekcji **mapowania atrybutów** można zmienić tylko pasujący identyfikator, który jest używany do łączenia profilu użytkownika SuccessFactors z użytkownikiem usługi Azure AD i który atrybut w usłudze Azure AD służy jako źródło wiadomości e-mail. 
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-attribute-mapping.png)

   >[!NOTE]
   >Zapisywanie zwrotne SuccessFactors obsługuje tylko atrybut poczty e-mail. Nie należy używać **Dodaj nowego mapowania** do dodawania nowych atrybutów. 

1. Aby zapisać mapowania, kliknij pozycję **Zapisz** w górnej części sekcji Mapowanie atrybutów.

Po zakończeniu konfiguracji mapowania atrybutów możesz teraz [włączyć i uruchomić usługę aprowizacji użytkowników](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji SuccessFactorsing (Inicjowanie obsługi administracyjnej) można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemach z danymi w dniach roboczych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Na karcie **aprowizacji** Ustaw **stan aprowizacji** na **włączone**.

2. Kliknij przycisk **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie SuccessFactors. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

4. W dowolnym momencie sprawdź kartę **dzienniki inspekcji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. W dziennikach inspekcji są wyświetlane wszystkie zdarzenia synchronizacji wykonywane przez usługę aprowizacji, takie jak informacje o użytkownikach, które są odczytywane z dnia roboczego, a następnie dodane lub zaktualizowane do Active Directory. 

5. Po zakończeniu synchronizacji początkowej zostanie zapisany raport z podsumowaniem inspekcji na karcie **aprowizacji** , jak pokazano poniżej.

   > [!div class="mx-imgBorder"]
   > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące aktywności aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między SuccessFactors i Azure Active Directory](successfactors-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)

