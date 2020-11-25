---
title: 'Samouczek: Konfigurowanie inicjowania obsługi przychodzącej SuccessFactors w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Inicjowanie obsługi ruchu przychodzącego z usługi SuccessFactors w usłudze Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 08/05/2020
ms.author: chmutali
ms.openlocfilehash: a62943c1a808424ded1a5e46ed115cda332bf7d5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020759"
---
# <a name="tutorial-configure-sap-successfactors-to-azure-ad-user-provisioning"></a>Samouczek: Konfigurowanie aprowizacji oprogramowania SAP SuccessFactors w usłudze Azure AD
Celem tego samouczka jest przedstawienie czynności, które należy wykonać w celu udostępnienia danych procesu roboczego od SuccessFactors pracowników centralnych do Azure Active Directory z opcjonalnym zapisem adresu e-mail do SuccessFactors. 

>[!NOTE]
>Skorzystaj z tego samouczka, jeśli użytkownicy, którym chcesz zainicjować obsługę administracyjną SuccessFactors, są użytkownikami tylko w chmurze, którzy nie potrzebują lokalnego konta usługi AD. Jeśli użytkownicy wymagają tylko lokalnego konta usługi AD lub konta usług AD i Azure AD, zapoznaj się z samouczkiem dotyczącym [konfigurowania oprogramowania SAP SuccessFactors, aby Active Directory](sap-successfactors-inbound-provisioning-tutorial.md#overview) Inicjowanie obsługi użytkowników. 

## <a name="overview"></a>Omówienie

[Usługa aprowizacji użytkowników Azure Active Directory](../app-provisioning/user-provisioning.md) integruje się z [centralnym pracownikiem SuccessFactors](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) w celu zarządzania cyklem życia tożsamości użytkowników. 

Przepływy pracy aprowizacji użytkowników SuccessFactors obsługiwane przez usługę aprowizacji użytkowników w usłudze Azure AD umożliwiają automatyzację następujących scenariuszy dotyczących zasobów ludzkich i zarządzania cyklem życia tożsamości:

* **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do usługi SuccessFactors konto użytkownika jest automatycznie tworzone w Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md)z zwrotem adresu e-mail na SuccessFactors.

* **Aktualizacje atrybutu i profilu pracownika** — gdy rekord pracownika zostanie zaktualizowany w SuccessFactors (takie jak nazwa, tytuł lub Menedżer), jego konto użytkownika zostanie automatycznie zaktualizowane Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* **Zakończenie zatrudnienia pracownika** — gdy pracownik zostanie zakończony w SuccessFactors, jego konto użytkownika zostanie automatycznie wyłączone w Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* Przedziały **pracownika** — gdy pracownik jest ponownie zatrudniany w usłudze SuccessFactors, jego stare konto może zostać automatycznie ponownie uaktywnione lub zainicjowane (zależnie od preferencji), aby Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

Ta SuccessFactors do Azure Active Directory rozwiązanie do aprowizacji użytkowników jest idealnym rozwiązaniem dla:

* Organizacje, które chcą uzyskać wbudowane, oparte na chmurze rozwiązanie do aprowizacji użytkowników SuccessFactors

* Organizacje, które wymagają bezpośredniej aprowizacji użytkowników z SuccessFactors do Azure Active Directory

* Organizacje, które wymagają, aby użytkownicy korzystali z danych uzyskanych od [SuccessFactors Employee Central (WE)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Organizacje korzystające z Microsoft 365 do poczty e-mail

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano kompleksową architekturę rozwiązania obsługi użytkowników dla użytkowników korzystających tylko z chmury. Istnieją dwa powiązane przepływy:

* **Autorytatywny przepływ danych HR — od SuccessFactors do Azure Active Directory:** W tym przepływie zdarzenia procesu roboczego (takie jak nowe zatrudnienie, transfery, zakończenia) są najpierw wykonywane w chmurze SuccessFactors Employee Central, a następnie dane zdarzenia są przenoszone do Azure Active Directory. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizowania/włączania/wyłączania operacji w usłudze Azure AD.
* **Przepływ zapisywania zwrotnego wiadomości e-mail — od lokalnego Active Directory do SuccessFactors:** Po zakończeniu tworzenia konta w Azure Active Directory wartość atrybutu adresu e-mail lub nazwa UPN wygenerowanego w usłudze Azure AD może zostać zapisana z powrotem do SuccessFactors.

  ![Omówienie](./media/sap-successfactors-inbound-provisioning/sf2aad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych kompleksowego użytkownika

1. Zespół kadr wykonuje transakcje procesu roboczego (przydziały/Firmy przeprowadzkowe lub nowe zatrudnienie/zwolnienia/zakończenia) w witrynie SuccessFactors Employee Central
2. Usługa Azure AD Provisioning uruchamia zaplanowane synchronizacje tożsamości z SuccessFactors EC i identyfikuje zmiany, które muszą zostać przetworzone w celu synchronizacji z lokalnym Active Directory.
3. Usługa Azure AD Provisioning określa zmianę i wywołuje operację tworzenia/aktualizowania/włączania/wyłączania dla użytkownika w usłudze Azure AD.
4. Jeśli skonfigurowano [aplikację zapisywania zwrotnego SuccessFactors](sap-successfactors-writeback-tutorial.md) , adres e-mail użytkownika zostanie pobrany z usługi Azure AD. 
5. Usługa Azure AD Provisioning zapisuje z powrotem atrybut poczty e-mail w SuccessFactors, na podstawie użytego atrybutu.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Konfigurowanie obsługi administracyjnej użytkowników w chmurze opartej na usłudze SuccessFactors w usłudze Azure AD wymaga znaczących czynności związanych z planowaniem obejmujących różne aspekty, takie jak:

* Określanie identyfikatora dopasowania 
* Mapowanie atrybutów
* Przekształcenie atrybutu 
* Filtry określania zakresu

Zapoznaj się z [planem wdrożenia usługi Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) , aby uzyskać wyczerpujące wytyczne dotyczące tych tematów. Zapoznaj się z dokumentacją dotyczącą [integracji oprogramowania SAP SuccessFactors](../app-provisioning/sap-successfactors-integration-reference.md) , aby poznać obsługiwane jednostki, szczegóły przetwarzania oraz sposób dostosowywania integracji dla różnych scenariuszy kadr. 

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
* Z **listy rola uprawnień** wybierz rolę utworzoną na potrzeby uprawnień użycia interfejsu API.
* W obszarze **Przypisz tę rolę do...** kliknij przycisk **Dodaj.** ...
* Z menu rozwijanego wybierz pozycję **Grupa uprawnień** , a następnie kliknij pozycję **Wybierz...** , aby otworzyć okno grupy, aby wyszukać i wybrać utworzoną powyżej grupę. 
  > [!div class="mx-imgBorder"]
  > ![Dodaj grupę uprawnień](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)
* Przejrzyj uprawnienia przyznane grupie uprawnień. 
  > [!div class="mx-imgBorder"]
  > ![Szczegóły roli i grupy uprawnień](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)
* Kliknij przycisk **Zapisz zmiany**.

## <a name="configuring-user-provisioning-from-successfactors-to-azure-ad"></a>Konfigurowanie aprowizacji użytkowników z SuccessFactors do usługi Azure AD

Ta sekcja zawiera kroki dla aprowizacji konta użytkownika z SuccessFactors do usługi Azure AD.

* [Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Konfigurowanie mapowań atrybutów](#part-2-configure-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>Część 1. Dodawanie aplikacji łącznika aprowizacji i Konfigurowanie łączności z usługą SuccessFactors

**Aby skonfigurować SuccessFactors do aprowizacji usługi Azure AD:**

1. Przejdź do strony <https://portal.azure.com>

2. Na lewym pasku nawigacyjnym wybierz pozycję **Azure Active Directory**

3. Wybierz **aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację**, a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj **SuccessFactors, aby Azure Active Directory aprowizacji użytkowników** i dodać tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **aprowizacji**

7. Zmień tryb **aprowizacji** **Mode** na **automatyczny**

8. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta użytkownika interfejsu API SuccessFactors z DOŁĄCZONYm identyfikatorem firmy. Ma format: **username \@ companyID**

   * **Hasło administratora —** Wprowadź hasło dla konta użytkownika interfejsu API SuccessFactors. 

   * **Adres URL dzierżawy —** Wprowadź nazwę punktu końcowego usług interfejsu API OData SuccessFactors. Wprowadź tylko nazwę hosta serwera bez protokołu HTTP lub https. Ta wartość powinna wyglądać następująco: **API-Server-Name.SuccessFactors.com**.

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".
    > [!NOTE]
    > Usługa Azure AD Provisioning wysyła powiadomienie e-mail, jeśli zadanie aprowizacji przejdzie do stanu [kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Kliknij przycisk **Testuj połączenie** . Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia i adres URL SuccessFactors są prawidłowe.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2aad-provisioning-creds.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **mapowania** zostanie wyświetlone mapowanie domyślne **Synchronizacja SuccessFactors użytkowników do Azure Active Directory**

### <a name="part-2-configure-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych przez użytkownika z SuccessFactors do Active Directory.

1. Na karcie Inicjowanie obsługi w obszarze **mapowania** kliknij pozycję **Synchronizuj SuccessFactors użytkowników, aby Azure Active Directory**.

1. W polu **Zakres obiektu źródłowego** możesz wybrać, które zestawy użytkowników w SuccessFactors powinny znajdować się w zakresie dla aprowizacji do usługi Azure AD przez zdefiniowanie zestawu filtrów opartych na atrybutach. Zakresem domyślnym jest "Wszyscy użytkownicy w SuccessFactors". Przykładowe filtry:

   * Przykład: zakres dla użytkowników z personIdExternalem między 1000000 i 2000000 (z wyłączeniem 2000000)

      * Atrybut: personIdExternal

      * Operator: dopasowanie wyrażenia regularnego

      * Wartość: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Przykład: tylko pracownicy i niezależni pracownicy

      * Atrybut: IDPracownika

      * Operator: nie ma wartości NULL

   > [!TIP]
   > Podczas konfigurowania aplikacji do aprowizacji po raz pierwszy należy przetestować i zweryfikować mapowania atrybutów oraz wyrażenia, aby upewnić się, że daje żądany wynik. Firma Microsoft zaleca używanie filtrów określania zakresu w obszarze **zakres obiektów źródłowych** do testowania mapowań za pomocą kilku użytkowników testowych z SuccessFactors. Po sprawdzeniu, czy mapowania działają, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

   > [!CAUTION] 
   > Domyślne zachowanie aparatu aprowizacji polega na wyłączeniu/usunięciu użytkowników, którzy wykraczają poza zakres. Może to nie być pożądane w SuccessFactors do integracji z usługą Azure AD. Aby zastąpić to zachowanie domyślne, odnoszące się do artykułu [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. W polu **Akcje obiektu docelowego** można globalnie filtrować akcje wykonywane na Active Directory. **Tworzenie** i **Aktualizowanie** są najczęściej używane.

1. W sekcji **mapowania atrybutów** można zdefiniować, w jaki sposób poszczególne atrybuty SuccessFactors są mapowane na atrybuty Active Directory.

  >[!NOTE]
  >Aby uzyskać pełną listę atrybutów SuccessFactors obsługiwanych przez aplikację, zapoznaj się z [odwołaniem do atrybutu SuccessFactors](../app-provisioning/sap-successfactors-attribute-reference.md)


1. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje te właściwości:

      * **Typ mapowania**

         * **Direct** — zapisuje wartość atrybutu SuccessFactors w atrybucie usługi AD bez zmian

         * **Stała** — Zapisz statyczną, stałą wartość ciągu w atrybucie usługi AD.

         * **Expression** — umożliwia zapisanie niestandardowej wartości atrybutu AD na podstawie jednego lub większej liczby atrybutów SuccessFactors. [Aby uzyskać więcej informacji, zobacz ten artykuł w wyrażeniach](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atrybut źródłowy** — atrybut użytkownika z SuccessFactors

      * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma pustą wartość, mapowanie spowoduje zapisanie tej wartości.
            Najbardziej typową konfiguracją jest pozostawienie tej pustej.

      * **Atrybut target** — atrybut user w Active Directory.

      * **Dopasowywanie obiektów przy użyciu tego atrybutu** — niezależnie od tego, czy mapowanie ma być używane do unikatowego identyfikowania użytkowników między SuccessFactors i Active Directory. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator procesu roboczego dla SuccessFactors, który jest zwykle mapowany na jeden z atrybutów identyfikatora pracownika w Active Directory.

      * **Priorytet dopasowania** — można ustawić wiele pasujących atrybutów. Jeśli istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.

      * **Zastosuj to mapowanie**

         * **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika

         * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko dla akcji tworzenia użytkownika

1. Aby zapisać mapowania, kliknij pozycję **Zapisz** w górnej części sekcji Attribute-Mapping.

Po zakończeniu konfiguracji mapowania atrybutów możesz teraz [włączyć i uruchomić usługę aprowizacji użytkowników](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji SuccessFactorsing (Inicjowanie obsługi administracyjnej) można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemach z danymi w dniach roboczych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Na karcie **aprowizacji** Ustaw **stan aprowizacji** na **włączone**.

2. Kliknij pozycję **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie SuccessFactors. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

4. W dowolnym momencie sprawdź kartę **dzienniki inspekcji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. W dziennikach inspekcji są wyświetlane wszystkie zdarzenia synchronizacji wykonywane przez usługę aprowizacji, takie jak informacje o użytkownikach, które są odczytywane z dnia roboczego, a następnie dodane lub zaktualizowane do Active Directory. 

5. Po zakończeniu synchronizacji początkowej zostanie zapisany raport z podsumowaniem inspekcji na karcie **aprowizacji** , jak pokazano poniżej.

   > [!div class="mx-imgBorder"]
   > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o obsługiwanych atrybutach SuccessFactors na potrzeby inicjowania obsługi przychodzącej](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Dowiedz się, jak skonfigurować funkcję zapisywania zwrotnego wiadomości e-mail w usłudze SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między SuccessFactors i Azure Active Directory](successfactors-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak eksportować i importować konfiguracje aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)