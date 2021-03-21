---
title: 'Samouczek: Konfigurowanie inicjowania obsługi przychodzącej z programu Workday w Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Inicjowanie obsługi ruchu przychodzącego z produktu Workday w usłudze Azure AD
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954093"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Samouczek: Konfigurowanie obsługi administracyjnej użytkowników w usłudze Azure AD
Celem tego samouczka jest przedstawienie czynności, które należy wykonać, aby udostępnić dane procesu roboczego z produktu Workday do Azure Active Directory. 

>[!NOTE]
>Skorzystaj z tego samouczka, jeśli użytkownicy, którym chcesz zainicjować obsługę administracyjną, są użytkownikami tylko w chmurze, którzy nie potrzebują lokalnego konta usługi AD. Jeśli użytkownicy wymagają tylko lokalnego konta usługi AD lub konta usług AD i Azure AD, zapoznaj się z samouczkiem dotyczącym [konfigurowania programu Workday, aby Active Directory](workday-inbound-tutorial.md) Inicjowanie obsługi użytkowników. 

## <a name="overview"></a>Omówienie

[Usługa aprowizacji użytkowników Azure Active Directory](../app-provisioning/user-provisioning.md) integruje się z [interfejsem API zasobów ludzkich firmy Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) , aby udostępnić konta użytkowników. Przepływy pracy aprowizacji użytkowników platformy Workday obsługiwane przez usługę aprowizacji użytkowników w usłudze Azure AD umożliwiają automatyzację następujących scenariuszy dotyczących zasobów ludzkich i zarządzania cyklem życia tożsamości:

* **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do produktu Workday konto użytkownika jest automatycznie tworzone w Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md)z możliwością zapisu zwrotnego adresu e-mail.

* **Aktualizacje atrybutu i profilu pracownika** — gdy rekord pracownika zostanie zaktualizowany w dniach roboczych (takich jak nazwa, tytuł lub Menedżer), jego konto użytkownika zostanie automatycznie zaktualizowane Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* **Zakończenie zatrudnienia pracownika** — gdy pracownik zostanie zakończony w dniach roboczych, jego konto użytkownika zostanie automatycznie wyłączone w Azure Active Directory i opcjonalnie Microsoft 365 i [inne aplikacje SaaS obsługiwane przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* Przedziały **pracownika** — gdy pracownik jest ponownie zatrudniany w dniu Workday, jego stare konto może zostać automatycznie ponownie uaktywnione lub zainicjowane (zależnie od preferencji) do Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

Ten roboczy Azure Active Directory rozwiązanie do aprowizacji użytkowników jest idealnym rozwiązaniem dla:

* Organizacje, które chcą uzyskać wbudowane, oparte na chmurze rozwiązanie do aprowizacji użytkowników w ramach platformy Workday

* Organizacje, które wymagają bezpośredniej aprowizacji użytkowników z produktu Workday do Azure Active Directory

* Organizacje, które wymagają aprowizacji użytkowników przy użyciu danych uzyskanych z produktu Workday

* Organizacje korzystające z Microsoft 365 do poczty e-mail

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano kompleksową architekturę rozwiązania obsługi użytkowników dla użytkowników korzystających tylko z chmury. Istnieją dwa powiązane przepływy:

* **Autorytatywny przepływ danych HR — od produktu Workday do Azure Active Directory:** W tym przepływie zdarzenia procesu roboczego (takie jak nowe zatrudnienie, transfery, zakończenia) są najpierw wykonywane w programie Workday, a następnie dane zdarzenia są przenoszone do Azure Active Directory. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizowania/włączania/wyłączania operacji w usłudze Azure AD.
* **Przepływ zapisywania zwrotnego — od Active Directory lokalnego do produktu Workday:** Po zakończeniu tworzenia konta w Active Directory zostanie ono zsynchronizowane z usługą Azure AD za pośrednictwem Azure AD Connect, a informacje takie jak adres e-mail, nazwa użytkownika i numer telefonu można napisać z powrotem do produktu Workday.

  ![Omówienie](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych kompleksowego użytkownika

1. Zespół kadr wykonuje transakcje procesu roboczego (przydziały/Firmy przeprowadzkowe lub nowe zatrudnienie/zwolnienia/zakończenia) w programie Workday Employee Central
2. Usługa Azure AD Provisioning uruchamia zaplanowane synchronizacje tożsamości z programu Workday i identyfikuje zmiany, które należy przetworzyć w celu synchronizacji z Active Directory lokalnymi.
3. Usługa Azure AD Provisioning określa zmianę i wywołuje operację tworzenia/aktualizowania/włączania/wyłączania dla użytkownika w usłudze Azure AD.
4. W przypadku skonfigurowania aplikacji [zapisywania zwrotnego dla produktu Workday](workday-writeback-tutorial.md) pobierane są atrybuty, takie jak adres e-mail, nazwa użytkownika i numer telefonu z usługi Azure AD. 
5. Usługa Azure AD Provisioning ustawia adres e-mail, nazwę użytkownika i numer telefonu w dniach roboczych.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Konfigurowanie obsługi administracyjnej użytkowników w chmurze opartej na usłudze Azure AD z poziomu produktu Workday w usłudze Active Directory wymaga znaczącego planowania obejmującego różne aspekty, takie jak:

* Określanie identyfikatora dopasowania 
* Mapowanie atrybutów
* Przekształcenie atrybutu 
* Filtry określania zakresu

Zapoznaj się z [planem wdrożenia usługi Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) , aby uzyskać wyczerpujące wytyczne dotyczące tych tematów. 

## <a name="configure-integration-system-user-in-workday"></a>Konfiguruj użytkownika systemu integracji w programie Workday

Zapoznaj się z sekcją [Konfigurowanie użytkownika systemu integracyjnego](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) na potrzeby tworzenia konta użytkownika integracji z systemem Workday z uprawnieniami do pobierania danych procesu roboczego. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>Konfigurowanie aprowizacji użytkowników z produktu Workday do usługi Azure AD

W poniższych sekcjach opisano kroki konfigurowania aprowizacji użytkowników z produktu Workday do usługi Azure AD na potrzeby wdrożeń opartych tylko na chmurze.

* [Dodawanie aplikacji łącznika aprowizacji usługi Azure AD i Tworzenie połączenia z produktem Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowań atrybutów produktu Workday i usługi Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika aprowizacji usługi Azure AD i Tworzenie połączenia z produktem Workday

**Aby skonfigurować dzień roboczy do Azure Active Directory aprowizacji dla użytkowników tylko w chmurze:**

1. Przejdź do witryny <https://portal.azure.com>.

2. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz **aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację**, a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj pozycję **Workday do aprowizacji użytkowników usługi Azure AD** i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **Inicjowanie obsługi**.

7. Zmień tryb **aprowizacji**  na **automatyczny**.

8. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika produktu Workday** — wprowadź nazwę użytkownika konta programu integracji z systemem Workday z dołączoną nazwą domeny dzierżawy. Powinien wyglądać następująco: username@contoso4

   * **Hasło do produktu Workday —** Wprowadź hasło konta systemu integracji produktu Workday

   * **Adres URL interfejsu API usług sieci Web dla produktu Workday —** Wprowadź adres URL punktu końcowego usług sieci Web Workday dla dzierżawy. Adres URL określa wersję interfejsu API usług sieci Web programu Workday używanego przez łącznik. 
   
     | Format adresu URL | Użyta wersja interfejsu API WWS | Wymagane zmiany XPATH |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | 21.1 v | Nie |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | 21.1 v | Nie |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Tak |

      > [!NOTE]
     > Jeśli w adresie URL nie określono informacji o wersji, aplikacja używa usług sieci Web WWS (Workday) 21.1 i nie są wymagane żadne zmiany do domyślnych wyrażeń interfejsu API XPATH dostarczonych z aplikacją. Aby użyć określonej wersji interfejsu API WWS, określ numer wersji w adresie URL <br>
     > Przykład: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Jeśli używasz WWS API v 30.0 +, przed włączeniem zadania aprowizacji, zaktualizuj **wyrażenia interfejsu API XPath** w obszarze **mapowanie atrybutu — > opcje zaawansowane — > Edytuj listę atrybutów dla produktu Workday** , odwołując się do sekcji Zarządzanie [odwołaniem do atrybutów](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [konfiguracji](workday-inbound-tutorial.md#managing-your-configuration) i wersji roboczej.  

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

   * Kliknij przycisk **Testuj połączenie** .

   * Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy adres URL i poświadczenia produktu Workday są prawidłowe w programie Workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów dni roboczych i usługi Azure AD

W tej sekcji opisano sposób przepływu danych przez użytkowników z produktu Workday do Azure Active Directory dla użytkowników korzystających tylko z chmury.

1. Na karcie Inicjowanie obsługi w obszarze **mapowania** kliknij pozycję **Synchronizuj procesy robocze do usługi Azure AD**.

2. W polu **Zakres obiektu źródłowego** możesz wybrać, które zestawy użytkowników w dniach roboczych powinny znajdować się w zakresie dla aprowizacji do usługi Azure AD, definiując zestaw filtrów opartych na atrybutach. Zakresem domyślnym jest "Wszyscy użytkownicy w usłudze Workday". Przykładowe filtry:

   * Przykład: zakres dla użytkowników z identyfikatorami procesów roboczych z zakresu od 1000000 do 2000000

      * Atrybut: WorkerID

      * Operator: dopasowanie wyrażenia regularnego

      * Wartość: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Przykład: tylko pracownicy warunkowi i nieregularni pracownicy

      * Atrybut: ContingentID

      * Operator: nie ma wartości NULL

3. W polu **Akcje obiektu docelowego** można globalnie filtrować akcje wykonywane w usłudze Azure AD. **Tworzenie**  i **Aktualizowanie** są najczęściej używane.

4. W sekcji **mapowania atrybutów** można określić, w jaki sposób mapowanie poszczególnych atrybutów produktu Workday ma Active Directory atrybuty.

5. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje te właściwości:

   * **Typ mapowania**

      * **Direct** — zapisuje wartość atrybutu Workday w atrybucie usługi AD bez zmian

      * **Stała** — Zapisz statyczną, stałą wartość ciągu w atrybucie usługi AD.

      * **Expression** — umożliwia zapisanie niestandardowej wartości atrybutu AD na podstawie jednego lub większej liczby atrybutów produktu Workday. [Aby uzyskać więcej informacji, zobacz ten artykuł w wyrażeniach](../app-provisioning/functions-for-customizing-application-data.md).

   * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli atrybutu, którego szukasz, nie ma, zobacz [Dostosowywanie listy atrybutów użytkownika produktu Workday](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes).

   * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma pustą wartość, mapowanie spowoduje zapisanie tej wartości.
            Najbardziej typową konfiguracją jest pozostawienie tej pustej.

   * **Attribute** — atrybut użytkownika w usłudze Azure AD.

   * **Dopasowywanie obiektów przy użyciu tego atrybutu** — czy ten atrybut ma być używany do jednoznacznego identyfikowania użytkowników między dniem Workday a usługą Azure AD. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator procesu roboczego dla produktu Workday, który zwykle jest mapowany na atrybut ID pracownika (nowy) lub atrybut rozszerzenia w usłudze Azure AD.

   * **Priorytet dopasowania** — można ustawić wiele pasujących atrybutów. Jeśli istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.

   * **Zastosuj to mapowanie**

     * **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika

     * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko dla akcji tworzenia użytkownika

6. Aby zapisać mapowania, kliknij pozycję **Zapisz** w górnej części sekcji Attribute-Mapping.


## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji inicjowania obsługi dla programu Workday można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemach z danymi w dniach roboczych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Na karcie **aprowizacji** Ustaw **stan aprowizacji** na **włączone**.

2. Kliknij pozycję **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie produktu Workday. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

4. W dowolnym momencie sprawdź kartę **dzienniki inspekcji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. W dziennikach inspekcji są wyświetlane wszystkie zdarzenia synchronizacji wykonywane przez usługę aprowizacji, takie jak informacje o użytkownikach, które są odczytywane z dnia roboczego, a następnie dodane lub zaktualizowane do Azure Active Directory. 

5. Po zakończeniu synchronizacji początkowej zostanie zapisany raport z podsumowaniem inspekcji na karcie **aprowizacji** , jak pokazano poniżej.

   > [!div class="mx-imgBorder"]
   > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat scenariuszy integracji usługi Azure AD i produktu Workday oraz wywołań usług sieci Web](../app-provisioning/workday-integration-reference.md)
* [Dowiedz się więcej na temat obsługiwanych atrybutów produktu Workday na potrzeby inicjowania obsługi przychodzącej](../app-provisioning/workday-attribute-reference.md)
* [Dowiedz się, jak skonfigurować zapisywanie zwrotne dla produktu Workday](workday-writeback-tutorial.md)
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak eksportować i importować konfiguracje aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)


