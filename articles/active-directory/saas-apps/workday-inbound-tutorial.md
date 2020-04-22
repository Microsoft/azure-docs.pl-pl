---
title: 'Samouczek: Konfigurowanie workday do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować usługę Azure Active Directory do automatycznego inicjowania obsługi administracyjnej i usuwania z obsługi administracyjnej kont użytkowników w dniu roboczym.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdf0cbfb91332d60516432a7a67fb10404d89113
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683847"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie workday do automatycznego inicjowania obsługi administracyjnej przez użytkowników

Celem tego samouczka jest pokazanie kroków, które należy wykonać w celu zaimportowania profilów procesowych z programu Workday zarówno do usługi Active Directory, jak i usługi Azure Active Directory, z opcjonalnym zapisem adresu e-mail i nazwy użytkownika do workday.

## <a name="overview"></a>Omówienie

[Usługa inicjowania obsługi administracyjnej użytkowników usługi Azure Active Directory](../app-provisioning/user-provisioning.md) integruje się z [interfejsem API zasobów ludzkich workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) w celu aprowizowania kont użytkowników. Usługa Azure AD używa tego połączenia, aby włączyć następujące przepływy pracy inicjowania obsługi administracyjnej użytkownika:

* **Inicjowanie obsługi administracyjnej użytkowników w usłudze Active Directory** — aprowizuj wybrane zestawy użytkowników z dnia roboczego do co najmniej jednej domeny usługi Active Directory.

* **Inicjowanie obsługi administracyjnej użytkowników tylko w chmurze do usługi Azure Active Directory** — w scenariuszach, w których lokalna usługa Active Directory nie jest używana, użytkownicy mogą być aprowicyjnie bezpośrednio z workday do usługi Azure Active Directory przy użyciu usługi inicjowania obsługi administracyjnej użytkowników usługi Azure AD.

* **Zapisz adres e-mail i nazwę użytkownika do Workday** — usługa inicjowania obsługi administracyjnej użytkowników usługi Azure AD może zapisywać adresy e-mail i nazwę użytkownika z usługi Azure AD z powrotem do Workday.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Jakie scenariusze dotyczące zasobów ludzkich obejmują?

Przepływy pracy inicjowania obsługi administracyjnej użytkowników workday obsługiwane przez usługę inicjowania obsługi administracyjnej użytkowników usługi Azure AD umożliwiają automatyzację następujących scenariuszy zarządzania zasobami ludzkimi i cyklem życia tożsamości:

* **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do workday konto użytkownika jest automatycznie tworzone w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md)— z odpisem adresu e-mail do workday.

* **Aktualizacja atrybutów i profilów pracownika** — gdy rekord pracownika jest aktualizowany w worku (na przykład jego nazwa, tytuł lub menedżer), ich konto użytkownika zostanie automatycznie zaktualizowane w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

* **Wypaśnie pracowników** — po zakończeniu pracy pracownik jest automatycznie wyłączany w usłudze Active Directory, usłudze Azure Active Directory i opcjonalnie w usłudze Office 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

* **Pracownik ponownie zatrudnia** — po ponownym zatrudnieniu pracownika w programie Workday jego stare konto może zostać automatycznie ponownie aktywowane lub ponownie aprowizowane (w zależności od preferencji) do usługi Active Directory, usługi Azure Active Directory i opcjonalnie usługi Office 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD.](../app-provisioning/user-provisioning.md)

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Do kogo najlepiej nadaje się to rozwiązanie do inicjowania obsługi administracyjnej użytkownika?

To rozwiązanie do inicjowania obsługi administracyjnej użytkowników w dniu roboczym idealnie nadaje się do:

* Organizacje, które pragną wstępnie utworzonego rozwiązania opartego na chmurze do inicjowania obsługi administracyjnej użytkowników workday

* Organizacje wymagające bezpośredniego inicjowania obsługi administracyjnej z usługi Workday do usługi Active Directory lub usługi Azure Active Directory

* Organizacje, które wymagają inicjowania obsługi administracyjnej użytkowników przy użyciu danych uzyskanych z modułu HCM dnia roboczego (patrz [Get_Workers)](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)

* Organizacje, które wymagają dołączania, przenoszenia i pozostawiania użytkowników do synchronizacji z co najmniej jednym lasem, domenami i procesorami operacyjnymi usługi Active Directory na podstawie informacji o zmianach wykrytych w module HCM dnia roboczego (patrz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje korzystające z usługi Office 365 do poczcie e-mail

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano architekturę rozwiązania inicjowania obsługi administracyjnej dla użytkowników końcowych dla typowych środowisk hybrydowych. Istnieją dwa powiązane przepływy:

* **Autorytatywny przepływ danych hr — od dnia roboczego do lokalnego usługi Active Directory:** W tym przepływie zdarzenia procesu roboczego (takie jak Nowi zatrudnienia, transfery, zakończenia) najpierw występują w dzierżawie zasobów ludzkich w chmurze Workday, a następnie dane zdarzeń przepływa do lokalnej usługi Active Directory za pośrednictwem usługi Azure AD i agenta inicjowania obsługi administracyjnej. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizacji/włączania/wyłączania operacji w usłudze AD.
* **Przepływ storn po wysłaniu wiadomości e-mail i nazwy użytkownika — z lokalnej usługi Active Directory do dnia roboczego:** Po zakończeniu tworzenia konta w usłudze Active Directory jest on synchronizowany z usługą Azure AD za pośrednictwem usługi Azure AD Connect, a atrybut poczty e-mail i nazwy użytkownika można zapisać z powrotem do workday.

![Omówienie](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych użytkownika końcowego

1. Zespół HR wykonuje transakcje pracowników (Stowarzyszania/Przeprowadzki/Urlopowicze lub Nowi pracownicy/Transfery/Rozwiązania) w Workday HCM
2. Usługa inicjowania obsługi administracyjnej usługi Azure AD uruchamia zaplanowane synchronizacje tożsamości z działu kadr workday i identyfikuje zmiany, które muszą zostać przetworzone w celu synchronizacji z lokalną usługą Active Directory.
3. Usługa inicjowania obsługi administracyjnej usługi Azure AD wywołuje lokalnego agenta inicjowania obsługi administracyjnej usługi Azure AD Connect z ładunkiem żądań zawierającym operacje tworzenia/aktualizacji/włączania/wyłączania konta usługi AD.
4. Agent inicjowania obsługi administracyjnej usługi Azure AD Connect używa konta usługi do dodawania/aktualizowania danych konta usługi AD.
5. Aparat usługi Azure AD Connect / AD Sync uruchamia synchronizację delta w celu ściągania aktualizacji w usłudze AD.
6. Aktualizacje usługi Active Directory są synchronizowane z usługą Azure Active Directory.
7. Jeśli łącznik stortowany w dniu roboczym jest skonfigurowany, zapisuje atrybut e-mail i nazwę użytkownika do workday, na podstawie pasującego atrybutu używane.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Przed rozpoczęciem integracji workday sprawdź wymagania wstępne poniżej i przeczytaj następujące wskazówki dotyczące dopasowania bieżącej architektury usługi Active Directory i wymagań dotyczących inicjowania obsługi administracyjnej użytkowników do rozwiązań dostarczonych przez usługę Azure Active Directory. Dostępny jest również [kompleksowy plan wdrażania](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) z arkuszami planowania, który pomoże Ci we współpracy z partnerem integracyjnym workday i interesariuszami działu hr.

Ta sekcja obejmuje następujące aspekty planowania:

* [Wymagania wstępne](#prerequisites)
* [Wybieranie aplikacji łącznika inicjowania obsługi administracyjnej do wdrożenia](#selecting-provisioning-connector-apps-to-deploy)
* [Planowanie wdrażania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integracja z wieloma domenami usługi Active Directory](#integrating-with-multiple-active-directory-domains)
* [Planowanie dnia roboczego do mapowania i przekształceń atrybutów użytkowników usługi Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Prawidłowa licencja subskrypcji usługi Azure AD Premium P1 lub wyższa dla każdego użytkownika, która będzie pochodzić z programu Workday i aprowizowana do lokalnej usługi Active Directory lub usługi Azure Active Directory.
* Dostęp administratora globalnego usługi Azure AD do konfigurowania agenta inicjowania obsługi administracyjnej
* Dzierżawa implementacji workday do celów testowania i integracji
* Uprawnienia administratora w workday do tworzenia użytkownika integracji systemu i wprowadzania zmian w celu testowania danych pracowników do celów testowych
* W przypadku inicjowania obsługi administracyjnej usługi Active Directory serwer z systemem Windows Server 2012 lub nowszym ze środowiskiem uruchomieniowym .NET 4.7.1+ jest wymagany do obsługi [lokalnego agenta inicjowania obsługi administracyjnej](https://go.microsoft.com/fwlink/?linkid=847801)
* [Usługa Azure AD Connect](../hybrid/whatis-hybrid-identity.md) do synchronizowania użytkowników między usługą Active Directory a usługą Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Wybieranie aplikacji łącznika inicjowania obsługi administracyjnej do wdrożenia

Aby ułatwić inicjowanie obsługi administracyjnej przepływów pracy między workday i usługi Active Directory, usługa Azure AD udostępnia wiele aplikacji łącznika inicjowania obsługi administracyjnej, które można dodać z galerii aplikacji usługi Azure AD:

![Galeria aplikacji usługi Azure AD](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory User Provisioning** — Ta aplikacja ułatwia inicjowanie obsługi administracyjnej konta użytkownika z dnia roboczego do jednej domeny usługi Active Directory. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji usługi Azure AD dla każdej domeny usługi Active Directory, którą musisz udostępnić.

* **Workday to Azure AD User Provisioning** — chociaż usługa Azure AD Connect jest narzędziem, które powinno być używane do synchronizowania użytkowników usługi Active Directory z usługą Azure Active Directory, ta aplikacja może służyć do ułatwiania inicjowania obsługi administracyjnej użytkowników tylko w chmurze z dnia roboczego do pojedynczej dzierżawy usługi Azure Active Directory.

* **Stornia pracy** — ta aplikacja ułatwia odpis adresów e-mail użytkownika z usługi Azure Active Directory do Workday.

> [!TIP]
> Zwykła aplikacja "Dzień roboczy" służy do konfigurowania logowania jednokrotnego między workday i usługi Azure Active Directory.

Użyj schematu blokowego decyzji poniżej, aby określić, które aplikacje inicjowania obsługi administracyjnej workday są odpowiednie dla danego scenariusza.
    ![Schemat blokowy decyzji](./media/workday-inbound-tutorial/wday_app_flowchart.png "Schemat blokowy decyzji")

Użyj spisu treści, aby przejść do odpowiedniej sekcji tego samouczka.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planowanie wdrażania agenta inicjowania obsługi administracyjnej usługi Azure AD Connect

> [!NOTE]
> Ta sekcja jest odpowiednia tylko wtedy, gdy planujesz wdrożyć aplikację do inicjowania obsługi administracyjnej użytkowników usługi Active Directory. Można pominąć to, jeśli wdrażasz workday writeback lub Workday do aplikacji inicjowania obsługi administracyjnej użytkowników usługi Azure AD.

Rozwiązanie do inicjowania obsługi administracyjnej użytkowników usługi AD wymaga wdrożenia co najmniej jednego agenta inicjowania obsługi administracyjnej na serwerach z systemem Windows 2012 R2 lub większym z minimalną pamięcią RAM 4 GB i środowiskiem uruchomieniym .NET 4.7.1+. Przed zainstalowaniem agenta inicjującego aprowizowanie należy wziąć pod uwagę następujące kwestie:

* Upewnij się, że serwer hosta z uruchomionym agentem inicjowania obsługi administracyjnej ma dostęp sieciowy do domeny docelowej usługi AD
* Kreator konfiguracji agenta inicjowania obsługi administracyjnej rejestruje agenta w dzierżawie usługi Azure AD, a proces rejestracji wymaga dostępu do *.msappproxy.net za pomocą portu TLS 443. Upewnij się, że reguły zapory wychodzącej są w miejscu, które włączają tę komunikację. Agent obsługuje [wychodzącą konfigurację serwera proxy HTTPS](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Agent inicjowania obsługi administracyjnej używa konta usługi do komunikowania się z lokalnymi domenami usługi AD. Przed zainstalowaniem agenta zaleca się utworzenie konta usługi z uprawnieniami administratora domeny i hasłem, które nie wygasa.  
* Podczas konfiguracji agenta inicjowania obsługi administracyjnej można wybrać kontrolery domeny, które powinny obsługiwać żądania inicjowania obsługi administracyjnej. Jeśli masz kilka geograficznie rozproszonych kontrolerów domeny, zainstaluj agenta inicjowania obsługi administracyjnej w tej samej lokacji co preferowane kontrolery domeny, aby poprawić niezawodność i wydajność kompleksowego rozwiązania
* Aby uzyskać wysoką dostępność, można wdrożyć więcej niż jednego agenta inicjowania obsługi administracyjnej i zarejestrować go do obsługi tego samego zestawu lokalnych domen usługi AD.

> [!IMPORTANT]
> W środowiskach produkcyjnych firma Microsoft zaleca, aby mieć co najmniej 3 agentów inicjowania obsługi administracyjnej skonfigurowanych z dzierżawą usługi Azure AD w celu zapewnienia wysokiej dostępności.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integracja z wieloma domenami usługi Active Directory

> [!NOTE]
> Ta sekcja jest odpowiednia tylko wtedy, gdy planujesz wdrożyć aplikację do inicjowania obsługi administracyjnej użytkowników usługi Active Directory. Można pominąć to, jeśli wdrażasz workday writeback lub Workday do aplikacji inicjowania obsługi administracyjnej użytkowników usługi Azure AD.

W zależności od topologii usługi Active Directory należy określić liczbę aplikacji łącznika inicjowania obsługi administracyjnej użytkownika i liczbę agentów inicjowania obsługi administracyjnej do skonfigurowania. Poniżej wymieniono niektóre z typowych wzorców wdrażania, które można odwoływać się podczas planowania wdrożenia.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Scenariusz wdrażania #1: Pojedyncza dzierżawa dnia roboczego - > pojedynczej domeny usługi AD

W tym scenariuszu masz jedną dzierżawę Workday i chcesz aprowizować użytkowników do jednej domeny docelowej usługi AD. Oto zalecana konfiguracja produkcji dla tego wdrożenia.

|   |   |
| - | - |
| Nie. agentów inicjujących do wdrażania w środowisku lokalnym | 3 (dla wysokiej dostępności i pracy awaryjnej) |
| Nie. aplikacji aprowizacji użytkowników usługi AD w witrynie Workday do aplikacji do inicjowania obsługi administracyjnej w witrynie Azure portal | 1 |

  ![Scenariusz 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Scenariusz wdrażania #2: Dzierżawa pojedynczego dnia roboczego > wiele podrzędnych domen usługi AD

W tym scenariuszu obejmuje inicjowanie obsługi administracyjnej użytkowników z Workday do wielu domen podrzędnych docelowej usługi AD w lesie. Oto zalecana konfiguracja produkcji dla tego wdrożenia.

|   |   |
| - | - |
| Nie. agentów inicjujących do wdrażania w środowisku lokalnym | 3 (dla wysokiej dostępności i pracy awaryjnej) |
| Nie. aplikacji aprowizacji użytkowników usługi AD w witrynie Workday do aplikacji do inicjowania obsługi administracyjnej w witrynie Azure portal | jedna aplikacja na domenę podrzędną |

  ![Scenariusz 2](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Scenariusz wdrożenia #3: Pojedyncza dzierżawa dnia roboczego - > rozłączne lasy usługi AD

W tym scenariuszu obejmuje inicjowanie obsługi administracyjnej użytkowników z Workday do domen w rozłącznych lasach usługi AD. Oto zalecana konfiguracja produkcji dla tego wdrożenia.

|   |   |
| - | - |
| Nie. agentów inicjujących do wdrażania w środowisku lokalnym | 3 na rozłączny las AD |
| Nie. aplikacji aprowizacji użytkowników usługi AD w witrynie Workday do aplikacji do inicjowania obsługi administracyjnej w witrynie Azure portal | jedna aplikacja na domenę podrzędną |

  ![Scenariusz 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planowanie dnia roboczego do mapowania i przekształceń atrybutów użytkowników usługi Active Directory

> [!NOTE]
> Ta sekcja jest odpowiednia tylko wtedy, gdy planujesz wdrożyć aplikację do inicjowania obsługi administracyjnej użytkowników usługi Active Directory. Można pominąć to, jeśli wdrażasz workday writeback lub Workday do aplikacji inicjowania obsługi administracyjnej użytkowników usługi Azure AD.

Przed skonfigurowaniem inicjowania obsługi administracyjnej użytkownika do domeny usługi Active Directory należy wziąć pod uwagę następujące pytania. Odpowiedzi na te pytania określi sposób określania zakresu filtrów i mapowania atrybutów.

* **Jakich użytkowników w workday należy aprowizować w tym lesie usługi Active Directory?**

  * *Przykład: Użytkownicy, w których atrybut Workday "Firma" zawiera wartość "Contoso", a atrybut "Worker_Type" zawiera "Regular"*

* **W jaki sposób użytkownicy są kierowani do różnych jednostek organizacyjnych?**

  * *Przykład: Użytkownicy są kierowani do uli, które odpowiadają lokalizacji biura, zgodnie z definicją w workday "Gmina" i "Country_Region_Reference" atrybuty*

* **Jak należy wypełniać następujące atrybuty w usłudze Active Directory?**

  * Nazwa zwyczajowa (cn)
    * *Przykład: Użyj wartości User_ID dnia roboczego, ustawionej przez zasoby ludzkie*

  * Identyfikator pracownika (identyfikator pracownika)
    * *Przykład: Użyj wartości Worker_ID dnia roboczego*

  * Nazwa konta SAM (sAMAccountName)
    * *Przykład: Użyj wartości User_ID workday, filtrowane przez wyrażenie inicjowania obsługi administracyjnej usługi Azure AD, aby usunąć nielegalne znaki*

  * Nazwa główna użytkownika (userPrincipalName)
    * *Przykład: Użyj wartości User_ID workday z wyrażeniem inicjowania obsługi administracyjnej usługi Azure AD w celu dołkowania nazwy domeny*

* **Jak użytkownicy powinni być dopasowywki między Workday i Active Directory?**

  * *Przykład: Użytkownicy o określonej wartości "Worker_ID" w dniu roboczym są dopasowyjane do użytkowników usługi Active Directory, gdzie "employeeID" ma tę samą wartość. Jeśli wartość Worker_ID nie zostanie znaleziona w usłudze Active Directory, utwórz nowego użytkownika.*
  
* **Czy las usługi Active Directory zawiera już identyfikatory użytkowników wymagane do działania pasującej logiki?**

  * *Przykład: Jeśli ta konfiguracja jest nowym wdrożeniem Workday, zaleca się, aby usługa Active Directory była wstępnie wypełniona prawidłowymi wartościami Worker_ID dnia roboczego (lub unikatową wartością identyfikatora z wyboru), aby logika dopasowania była tak prosta, jak to możliwe.*

Jak skonfigurować i skonfigurować te specjalne aplikacje łącznika inicjowania obsługi administracyjnej jest przedmiotem pozostałych sekcji tego samouczka. Aplikacje, które chcesz skonfigurować, będą zależeć od systemów, które należy udostępnić i ile domen usługi Active Directory i dzierżaw usługi Azure AD znajduje się w Twoim środowisku.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurowanie użytkownika systemu integracji w workday

Typowym wymaganiem wszystkich łączników inicjowania obsługi administracyjnej workday jest to, że wymagają one poświadczeń użytkownika systemu integracji workday, aby połączyć się z interfejsem API zasobów ludzkich workday. W tej sekcji opisano sposób tworzenia użytkownika systemu integracji w workday i następujące sekcje:

* [Tworzenie użytkownika systemu integracji](#creating-an-integration-system-user)
* [Tworzenie integracyjnej grupy zabezpieczeń](#creating-an-integration-security-group)
* [Konfigurowanie uprawnień zasad zabezpieczeń domeny](#configuring-domain-security-policy-permissions)
* [Konfigurowanie uprawnień zasad zabezpieczeń procesów biznesowych](#configuring-business-process-security-policy-permissions)
* [Aktywowanie zmian zasad zabezpieczeń](#activating-security-policy-changes)

> [!NOTE]
> Istnieje możliwość obejścia tej procedury i zamiast tego użyj konta administratora globalnego Workday jako konta integracji systemu. Może to działać poprawnie w przypadku wersji demonstracyjnych, ale nie jest zalecane w przypadku wdrożeń produkcyjnych.

### <a name="creating-an-integration-system-user"></a>Tworzenie użytkownika systemu integracji

**Aby utworzyć użytkownika systemu integracji:**

1. Zaloguj się do dzierżawy workday przy użyciu konta administratora. W **aplikacji Workday**wprowadź pozycję Utwórz użytkownika w polu wyszukiwania, a następnie kliknij pozycję **Utwórz użytkownika systemu integracji**.

   ![Tworzenie użytkownika](./media/workday-inbound-tutorial/wd_isu_01.png "Tworzenie użytkownika")
2. Wykonaj zadanie **Utwórz użytkownika systemu integracji,** podając nazwę użytkownika i hasło dla nowego użytkownika systemu integracji.  
  
   * Pozostaw opcję **Wymagaj nowego hasła przy następnym logowaniu** niezaznaczone, ponieważ ten użytkownik będzie logował się programowo.
   * Pozostaw **minut limitu czasu sesji** z domyślną wartością 0, co uniemożliwi przedwcześnie przedwcześnie przedterminowe przedwcześnie przedwcześnie przedwcześnie określone czasy sesji użytkownika.
   * Wybierz opcję **Nie zezwalaj na sesje interfejsu użytkownika,** ponieważ zapewnia ona dodatkową warstwę zabezpieczeń, która uniemożliwia użytkownikowi z hasłem systemu integracji zalogowanie się do workday.

   ![Tworzenie użytkownika systemu integracji](./media/workday-inbound-tutorial/wd_isu_02.png "Tworzenie użytkownika systemu integracji")

### <a name="creating-an-integration-security-group"></a>Tworzenie integracyjnej grupy zabezpieczeń

W tym kroku utworzysz nieograniczoną lub ograniczoną grupę zabezpieczeń systemu integracji w workday i przypiszesz do tej grupy użytkownika systemu integracji utworzonego w poprzednim kroku.

**Aby utworzyć grupę zabezpieczeń:**

1. Wprowadź pozycję Utwórz grupę zabezpieczeń w polu wyszukiwania, a następnie kliknij pozycję **Utwórz grupę zabezpieczeń**.

    ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_03.png "Grupa CreateSecurity")
2. Wykonaj zadanie **Utwórz grupę zabezpieczeń.** 

   * Istnieją dwa typy grup zabezpieczeń w workday:
     * **Nieskrępowany:** Wszyscy członkowie grupy zabezpieczeń mogą uzyskiwać dostęp do wszystkich wystąpień danych zabezpieczonych przez grupę zabezpieczeń.
     * **Ograniczone:** Wszyscy członkowie grupy zabezpieczeń mają kontekstowy dostęp do podzbioru wystąpień danych (wierszy), do których grupa zabezpieczeń może uzyskać dostęp.
   * Skontaktuj się z partnerem integracji Workday, aby wybrać odpowiedni typ grupy zabezpieczeń dla integracji.
   * Po poznaniu typu grupy wybierz pozycję **Integration System Security Group (Unconstrained)** lub Integration System Security Group **(Constrained)** z listy rozwijanej **Typ grupy zabezpieczeń z dzierżawą.**

     ![Grupa CreateSecurity](./media/workday-inbound-tutorial/wd_isu_04.png "Grupa CreateSecurity")

3. Po pomyślnym utworzeniu grupy zabezpieczeń zostanie wyświetlona strona, na której można przypisać członków do grupy zabezpieczeń. Dodaj nowego użytkownika systemu integracji utworzonego w poprzednim kroku do tej grupy zabezpieczeń. Jeśli używasz grupy zabezpieczeń *z ograniczeniami,* należy również wybrać odpowiedni zakres organizacji.

    ![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_05.png "Edytuj grupę zabezpieczeń")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurowanie uprawnień zasad zabezpieczeń domeny

W tym kroku przyznasz grupie zabezpieczeń uprawnienia zasad "zabezpieczenia domeny" dla danych procesu roboczego.

**Aby skonfigurować uprawnienia zasad zabezpieczeń domeny:**

1. Wprowadź **konfigurację zabezpieczeń domeny** w polu wyszukiwania, a następnie kliknij łącze Raport konfiguracji zabezpieczeń **domeny**.  

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_06.png "Zasady zabezpieczeń domeny")  
2. W polu tekstowym **Domena** wyszukaj następujące domeny i dodaj je do filtru jeden po drugim.  
   * *Inicjowanie obsługi administracyjnej konta zewnętrznego*
   * *Dane pracownika: Pracownicy*
   * *Dane pracownika: Raporty pracowników publicznych*
   * *Dane osoby: Dane kontaktowe pracy*
   * *Dane pracownika: wszystkie stanowiska*
   * *Dane pracowników: Aktualne informacje o personelu*
   * *Dane pracownika: tytuł firmy w profilu pracownika*
   * *Konta dni roboczych*
   
     ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_07.png "Zasady zabezpieczeń domeny")  

     ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_08.png "Zasady zabezpieczeń domeny") 

     Kliknij przycisk **OK**.

3. W raporcie, który się pojawi, wybierz wielokropek (...) który pojawia się obok **opcji Inicjowanie obsługi administracyjnej konta zewnętrznego** i kliknij opcję menu Domain **-> Edit Security Policy Permissions**

    ![Zasady zabezpieczeń domeny](./media/workday-inbound-tutorial/wd_isu_09.png "Zasady zabezpieczeń domeny")  

4. Na stronie **Edytowanie uprawnień zasad zabezpieczeń domeny** przewiń w dół do sekcji **Uprawnienia integracji**. Kliknij znak "+", aby dodać grupę systemu integracji do listy grup zabezpieczeń z uprawnieniami integracji **Pobierz** i **Umieść.**

    ![Edytuj uprawnienie](./media/workday-inbound-tutorial/wd_isu_10.png "Edytuj uprawnienie")  

5. Kliknij znak "+", aby dodać grupę systemu integracji do listy grup zabezpieczeń z uprawnieniami integracji **Pobierz** i **Umieść.**

    ![Edytuj uprawnienie](./media/workday-inbound-tutorial/wd_isu_11.png "Edytuj uprawnienie")  

6. Powtórz kroki 3-5 powyżej dla każdej z tych pozostałych zasad zabezpieczeń:

   | Operacja | Zasady zabezpieczeń domeny |
   | ---------- | ---------- |
   | Pobierz i umieść | Dane pracownika: Raporty pracowników publicznych |
   | Pobierz i umieść | Dane osoby: Dane kontaktowe pracy |
   | Get | Dane pracownika: Pracownicy |
   | Get | Dane pracownika: wszystkie stanowiska |
   | Get | Dane pracowników: Aktualne informacje o personelu |
   | Get | Dane pracownika: tytuł firmy w profilu pracownika |
   | Pobierz i umieść | Konta dni roboczych |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurowanie uprawnień zasad zabezpieczeń procesów biznesowych

W tym kroku przyznasz uprawnienia zasad "bezpieczeństwo procesów biznesowych" dla danych pracownika do grupy zabezpieczeń. Ten krok jest wymagany do konfigurowania łącznika aplikacji Writeback workday.

**Aby skonfigurować uprawnienia zasad zabezpieczeń procesów biznesowych:**

1. Wprowadź **zasady przetwarzania biznesowego** w polu wyszukiwania, a następnie kliknij łącze Edytuj zasady **zabezpieczeń procesów biznesowych.**  

    ![Zasady bezpieczeństwa procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_12.png "Zasady bezpieczeństwa procesów biznesowych")  

2. W polu **tekstowym Typ procesu biznesowego** wyszukaj *pozycję Kontakt* i wybierz pozycję **Skontaktuj się z** procesem biznesowym Zmień i kliknij przycisk **OK**.

    ![Zasady bezpieczeństwa procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_13.png "Zasady bezpieczeństwa procesów biznesowych")  

3. Na stronie **Edytowanie zasad zabezpieczeń procesów biznesowych** przewiń do sekcji **Zachowaj informacje kontaktowe (usługa sieci Web).**

    ![Zasady bezpieczeństwa procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_14.png "Zasady bezpieczeństwa procesów biznesowych")  

4. Wybierz i dodaj nową grupę zabezpieczeń systemu integracji do listy grup zabezpieczeń, które mogą inicjować żądanie usług sieci web. Kliknij **gotowe**. 

    ![Zasady bezpieczeństwa procesów biznesowych](./media/workday-inbound-tutorial/wd_isu_15.png "Zasady bezpieczeństwa procesów biznesowych")  

### <a name="activating-security-policy-changes"></a>Aktywowanie zmian zasad zabezpieczeń

**Aby aktywować zmiany zasad zabezpieczeń:**

1. Wprowadź aktywuj w polu wyszukiwania, a następnie kliknij link **Aktywuj oczekujące zmiany zasad zabezpieczeń**.

    ![Aktywować](./media/workday-inbound-tutorial/wd_isu_16.png "Activate")

1. Rozpocznij zadanie Aktywuj oczekujące zmiany zasad zabezpieczeń, wprowadzając komentarz do celów inspekcji, a następnie kliknij przycisk **OK**.
1. Wykonaj zadanie na następnym ekranie, zaznaczając pole wyboru **Potwierdź,** a następnie kliknij przycisk **OK**.

    ![Aktywowanie oczekujących zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_18.png "Aktywowanie oczekujących zabezpieczeń")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurowanie inicjowania obsługi administracyjnej użytkowników z usługi Workday do usługi Active Directory

W tej sekcji przedstawiono kroki inicjowania obsługi administracyjnej konta użytkownika z programu Workday do każdej domeny usługi Active Directory w zakresie integracji.

* [Dodawanie aplikacji łącznika inicjowania obsługi administracyjnej i pobieranie agenta inicjowania obsługi administracyjnej](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalowanie i konfigurowanie lokalnych agentów inicjowania obsługi administracyjnej](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurowanie łączności z usługą Workday i Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurowanie mapowań atrybutów](#part-4-configure-attribute-mappings)
* [Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Część 1: Dodaj aplikację łącznika inicjowania obsługi administracyjnej i pobierz agenta inicjowania obsługi administracyjnej

**Aby skonfigurować inicjowanie obsługi administracyjnej usługi Workday to Active Directory:**

1. Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz **pozycję Dodaj aplikację**i wybierz kategorię **Wszystkie.**

5. Wyszukaj **program Inicjowanie obsługi administracyjnej usługi Workday w usłudze Active Directory**i dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu szczegółów aplikacji wybierz pozycję **Inicjowanie obsługi administracyjnej**.

7. Zmień **tryb** **inicjowania obsługi administracyjnej** na **Automatyczny**.

8. Kliknij baner informacyjny wyświetlony, aby pobrać agenta inicjowania obsługi administracyjnej. 

   ![Pobierz agenta](./media/workday-inbound-tutorial/pa-download-agent.png "Ekran programu Pobierania Agent")


### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Część 2: Instalowanie i konfigurowanie lokalnego agenta(-ów) inicjowania obsługi administracyjnej

Aby udostępnić usługę Active Directory lokalnie, agent inicjowania obsługi administracyjnej musi być zainstalowany na serwerze z platformą .NET 4.7.1+ Framework i dostępem do sieci żądanej domeny usługi Active Directory.

> [!TIP]
> Wersję programu .NET framework można sprawdzić na serwerze, korzystając z instrukcji podanych [w tym miejscu.](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)
> Jeśli serwer nie ma zainstalowanej platformy .NET 4.7.1 lub nowszej, można go pobrać [stąd](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows).  

Przenieś pobrany instalator agenta na hosta serwera i wykonaj poniższe czynności, aby ukończyć konfigurację agenta.

1. Zaloguj się do systemu Windows Server, w którym chcesz zainstalować nowego agenta.

1. Uruchom instalator agenta inicjującego, zaakceptuj warunki i kliknij przycisk **Zainstaluj.**

   ![Ekran instalacji](./media/workday-inbound-tutorial/pa_install_screen_1.png "Ekran instalacji")
   
1. Po zakończeniu instalacji zostanie uruchomiony kreator, a ekran **Connect Azure AD** zostanie wyświetlony. Kliknij przycisk **Uwierzytelnij,** aby połączyć się z wystąpieniem usługi Azure AD.

   ![Łączenie z usługą Azure AD](./media/workday-inbound-tutorial/pa_install_screen_2.png "Łączenie z usługą Azure AD")
   
1. Uwierzytelnij się w wystąpieniu usługi Azure AD przy użyciu poświadczeń administratora globalnego.

   ![Administrator Auth](./media/workday-inbound-tutorial/pa_install_screen_3.png "Administrator Auth")

   > [!NOTE]
   > Poświadczenia administratora usługi Azure AD są używane tylko do łączenia się z dzierżawą usługi Azure AD. Agent nie przechowuje poświadczeń lokalnie na serwerze.

1. Po pomyślnym uwierzytelnieniu za pomocą usługi Azure AD zostanie wyświetlony ekran **Połącz usługę Active Directory.** W tym kroku wprowadź nazwę domeny usługi AD i kliknij przycisk **Dodaj katalog.**

   ![Dodawanie katalogu](./media/workday-inbound-tutorial/pa_install_screen_4.png "Dodawanie katalogu")
  
1. Zostanie wyświetlony monit o wprowadzenie poświadczeń wymaganych do nawiązania połączenia z domeną usługi AD. Na tym samym ekranie można użyć **priorytetu Wybierz kontroler domeny,** aby określić kontrolery domeny, których agent powinien używać do wysyłania żądań inicjowania obsługi administracyjnej.

   ![Poświadczenia domeny](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Po skonfigurowaniu domeny instalator wyświetla listę skonfigurowanych domen. Na tym ekranie możesz powtórzyć krok #5 i #6, aby dodać więcej domen lub kliknąć **Przycisk Dalej,** aby przejść do rejestracji agenta.

   ![Skonfigurowane domeny](./media/workday-inbound-tutorial/pa_install_screen_6.png "Skonfigurowane domeny")

   > [!NOTE]
   > Jeśli masz wiele domen AD (np. na.contoso.com, emea.contoso.com), dodaj każdą domenę indywidualnie do listy.
   > Tylko dodanie domeny nadrzędnej (np. contoso.com) nie jest wystarczające. Należy zarejestrować każdą domenę podrzędną u agenta.
   
1. Przejrzyj szczegóły konfiguracji i kliknij **potwierdź,** aby zarejestrować agenta.
  
   ![Potwierdź ekran](./media/workday-inbound-tutorial/pa_install_screen_7.png "Potwierdź ekran")
   
1. Kreator konfiguracji wyświetla postęp rejestracji agenta.
  
   ![Rejestracja agenta](./media/workday-inbound-tutorial/pa_install_screen_8.png "Rejestracja agenta")
   
1. Po pomyślnym zakończeniu rejestracji agenta możesz kliknąć **przycisk Zakończ,** aby zakończyć pracę kreatora.
  
   ![Ekran wyjścia](./media/workday-inbound-tutorial/pa_install_screen_9.png "Ekran wyjścia")
   
1. Sprawdź instalację agenta i upewnij się, że jest on uruchomiony, otwierając przystawkę "Usługi" i poszukaj usługi o nazwie "Agent aprowizacji usługi Microsoft Azure AD Connect"
  
   ![Usługi](./media/workday-inbound-tutorial/services.png)

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Część 3: W aplikacji do inicjowania obsługi administracyjnej skonfiguruj łączność z usługą Workday i Active Directory
W tym kroku ustanawiamy łączność z Workday i usługi Active Directory w witrynie Azure portal. 

1. W witrynie Azure portal wróć do aplikacji do inicjowania obsługi administracyjnej usługi Active Directory utworzonej w [części 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Wypełnij sekcję **Poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika dnia roboczego** — wprowadź nazwę użytkownika konta systemu integracji workday z dołączenie nazwy domeny dzierżawy. Powinien wyglądać mniej więcej tak: **nazwa użytkownika\@tenant_name**

   * **Hasło dnia roboczego –** Wprowadź hasło konta systemu integracji Workday

   * **Adres URL interfejsu API usług sieci Web workday —** Wprowadź adres URL punktu końcowego usług sieci web workday dla dzierżawy. Ta wartość powinna https://wd3-impl-services1.workday.com/ccx/service/contoso4wyglądać następująco: , gdzie *contoso4* jest zastępowany poprawną nazwą dzierżawy i *wd3-impl* jest zastępowany ciągiem poprawnego środowiska.

     > [!NOTE]
     > Domyślnie aplikacja używa usług Sieci Web Workday w wersji 21.1, jeśli w adresie URL nie podano żadnych informacji o wersji. Aby użyć określonej wersji interfejsu API usług Sieci Web Workday, użyj formatu adresu URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Przykład: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0

   * **Las usługi Active Directory -** "Nazwa" domeny usługi Active Directory zarejestrowana u agenta. Użyj listy rozwijanej, aby wybrać domenę docelową do inicjowania obsługi administracyjnej. Ta wartość jest zazwyczaj ciąg jak: *contoso.com*

   * **Kontener usługi Active Directory -** Wprowadź numer DN kontenera, w którym agent powinien domyślnie tworzyć konta użytkowników.
        Przykład: *OU=Użytkownicy standardowi,OU=Użytkownicy,DC=contoso,DC=test*
        
     > [!NOTE]
     > To ustawienie wchodzi w grę tylko dla tworzenia konta użytkownika, jeśli *atrybut parentDistinguishedName* nie jest skonfigurowany w mapowania atrybutów. To ustawienie nie jest używane do operacji wyszukiwania lub aktualizacji użytkownika. Całe drzewo podrzędne domeny wchodzi w zakres operacji wyszukiwania.

   * **Wiadomość e-mail z powiadomieniem –** Wprowadź swój adres e-mail i zaznacz pole wyboru "Wyślij wiadomość e-mail w przypadku wystąpienia błędu".

     > [!NOTE]
     > Usługa inicjowania obsługi administracyjnej usługi Azure AD wysyła powiadomienie e-mail, jeśli zadanie inicjowania obsługi administracyjnej przechodzi do stanu [kwarantanny.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status)

   * Kliknij przycisk **Testuj połączenie.** Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia Workday i poświadczenia usługi AD skonfigurowane w konfiguracji agenta są prawidłowe.

     ![Azure Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **Mapowania** zostanie wyświetlone domyślne mapowanie **Synchronizuj pracowników dnia roboczego z lokalną usługą Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Część 4: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz przepływ danych użytkownika z worka do usługi Active Directory.

1. Na karcie Inicjowanie obsługi administracyjnej w obszarze **Mapowania**kliknij pozycję **Synchronizuj pracowników dni roboczych z lokalną usługą Active Directory**.

1. W polu **Zakres obiektu źródłowego** można wybrać zestawy użytkowników w workday powinny być w zakresie inicjowania obsługi administracyjnej do usługi AD, definiując zestaw filtrów opartych na atrybutach. Domyślnym zakresem jest "wszyscy użytkownicy w Workday". Przykładowe filtry:

   * Przykład: Zakres do użytkowników z identyfikatorami pracowników między 1000000 a 2000000 (z wyłączeniem 2000000)

      * Atrybut: WorkerID

      * Operator: REGEX Match

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracownicy, a nie pracownicy warunkowi

      * Atrybut: EmployeeID

      * Operator: NIE MA WARTOŚCI NULL

   > [!TIP]
   > Podczas konfigurowania aplikacji inicjowania obsługi administracyjnej po raz pierwszy, należy przetestować i zweryfikować mapowania atrybutów i wyrażeń, aby upewnić się, że daje pożądany wynik. Firma Microsoft zaleca użycie filtrów zakresu w obszarze **Zakres obiektu źródłowego** do testowania mapowań z kilkoma użytkownikami testowymi z Workday. Po sprawdzeniu, że mapowania działają, można usunąć filtr lub stopniowo rozszerzać go, aby uwzględnić więcej użytkowników.

   > [!CAUTION] 
   > Domyślnym zachowaniem aparatu inicjowania obsługi administracyjnej jest wyłączenie/usunięcie użytkowników, którzy wychodzą poza zakres. Może to nie być pożądane w dniu roboczym do integracji ad. Aby zastąpić to domyślne zachowanie, zapoznaj się z [artykułem Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](../app-provisioning/skip-out-of-scope-deletions.md)
  
1. W polu **Akcje obiektów docelowych** można globalnie filtrować, jakie akcje są wykonywane w usłudze Active Directory. **Tworzenie** i **aktualizowanie** są najczęściej.

1. W sekcji **Mapowania atrybutów** można zdefiniować sposób mapowania poszczególnych atrybutów workday na atrybuty usługi Active Directory.

1. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje następujące właściwości:

      * **Typ mapowania**

         * **Bezpośredni** — zapisuje wartość atrybutu Workday do atrybutu AD, bez żadnych zmian

         * **Stała** — zapis statycznej, stałej wartości ciągu do atrybutu AD

         * **Wyrażenie** — umożliwia zapisanie wartości niestandardowej do atrybutu AD na podstawie co najmniej jednego atrybutu Workday. [Aby uzyskać więcej informacji, zobacz ten artykuł na temat wyrażeń](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atrybut source** — atrybut użytkownika z workday. Jeśli atrybut, którego szukasz, nie jest obecny, zobacz [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes).

      * **Wartość domyślna** — opcjonalnie. Jeśli atrybut źródłowy ma pustą wartość, mapowanie zapisze tę wartość.
            Najczęstszą konfiguracją jest pozostawienie tego pustego miejsca.

      * **Atrybut docelowy** — atrybut użytkownika w usłudze Active Directory.

      * **Dopasuj obiekty przy użyciu tego atrybutu** — określa, czy to mapowanie powinno być używane do jednoznacznej identyfikacji użytkowników między usługą Workday i Active Directory. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator pracownika dla dnia roboczego, które jest zwykle mapowane na jeden z atrybutów identyfikatora pracownika w usłudze Active Directory.

      * **Pierwszeństwo dopasowania** — można ustawić wiele pasujących atrybutów. Gdy istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Jak tylko zostanie znaleziony dopasowania, nie dalsze pasujące atrybuty są oceniane.

      * **Zastosuj to mapowanie**

         * **Zawsze** — stosowanie tego mapowania zarówno w przypadku akcji tworzenia, jak i aktualizacji użytkownika

         * **Tylko podczas tworzenia** — zastosuj to mapowanie tylko do akcji tworzenia użytkownika

1. Aby zapisać mapowania, kliknij przycisk **Zapisz** u góry sekcji Mapowanie atrybutów.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Poniżej znajduje się kilka przykładowych mapowań atrybutów między workday i usługą Active Directory, z niektórymi typowymi wyrażeniami

* Wyrażenie, które mapuje atrybut *parentDistinguishedName* jest używane do inicjowania obsługi administracyjnej użytkownika do różnych procesorów operacyjnych na podstawie jednego lub więcej atrybutów źródła workday. W tym przykładzie umieszcza użytkowników w różnych systemach organizacyjnych na podstawie tego, w jakim mieście się znajdują.

* Atrybut *userPrincipalName* w usłudze Active Directory jest generowany przy użyciu funkcji usuwania duplikacji [SelectUniqueValue,](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) która sprawdza istnienie wygenerowanej wartości w docelowej domenie usługi AD i ustawia ją tylko wtedy, gdy jest unikatowa.  

* [Jest dokumentacja dotycząca pisania wyrażeń tutaj](../app-provisioning/functions-for-customizing-application-data.md). Ta sekcja zawiera przykłady usuwania znaków specjalnych.

| ATRYBUT WORKDAY | ATRYBUT USŁUGI ACTIVE DIRECTORY |  IDENTYFIKATOR DOPASOWANIA? | TWORZENIE / AKTUALIZACJA |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Tak** | Napisane tylko na create |
| **PreferowananameData**    |  Cn    |   |   Napisane tylko na create |
| **SelectUniqueValue(\@Join(" , Join(".", \[FirstName\], \[\]LastName ),\@"contoso.com"), Join("\[, Join(.", Mid( FirstName\], \[1, 1), LastName\]), "contoso.com"), Join("\@Join(".", Mid(\[\]FirstName , 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Napisane tylko na create 
| **\[Zamień(Mid(Replace(\]UserID\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\,\\\\\\ , "( : ; \\\|\\\\=\\\\,\\\\+\\\\\*\\\\? \\\\\\)", , ",", ), 1, 20), "(.) \\ &lt; \\ \\ &gt; \] \* \$\\ *$)", , "", , )**      |    sAMAccountName            |     |         Napisane tylko na create |
| **Przełącznik(\[\]Aktywny , , "0", "True", "1", "False")** |  accountDisabled (rozliczanie się)      |     | Tworzenie + aktualizacja |
| **Imię**   | givenName       |     |    Tworzenie + aktualizacja |
| **Nazwisko**   |   sn   |     |  Tworzenie + aktualizacja |
| **PreferowananameData**  |  displayName |     |   Tworzenie + aktualizacja |
| **Firmy**         | company   |     |  Tworzenie + aktualizacja |
| **Organizacja nadzorcza**  | department  |     |  Tworzenie + aktualizacja |
| **Wniosek menedżera**   | manager  |     |  Tworzenie + aktualizacja |
| **Tytuł biznesowy**   |  title     |     |  Tworzenie + aktualizacja | 
| **AddressLineData (Dane linii adresowej)**    |  Streetaddress  |     |   Tworzenie + aktualizacja |
| **Gmina**   |   l   |     | Tworzenie + aktualizacja |
| **KrajReferenceTwoLetter**      |   Co |     |   Tworzenie + aktualizacja |
| **KrajReferenceTwoLetter**    |  c  |     |         Tworzenie + aktualizacja |
| **KrajRegionReferencja** |  st     |     | Tworzenie + aktualizacja |
| **Wniosek o obszar roboczy** | physicalDeliveryOfficeName    |     |  Tworzenie + aktualizacja |
| **Postalcode**  |   Postalcode  |     | Tworzenie + aktualizacja |
| **Telefon PrimaryWorkTelephone**  |  Telephonenumber   |     | Tworzenie + aktualizacja |
| **Faks**      | numer telefonu faksymiiltelephoneNumber     |     |    Tworzenie + aktualizacja |
| **Urządzenia przenośne**  |    telefon komórkowy       |     |       Tworzenie + aktualizacja |
| **Wniosek lokalny** |  preferowany Język  |     |  Tworzenie + aktualizacja |                                               
| **Przełącznik(\[gmina , "OU=Użytkownicy standardowi,OU=Użytkownicy,OU=Domyślna,OU=Lokalizacje,DC=contoso,DC=com",\]"Dallas", "OU=Użytkownicy standardowi,Użytkownicy,OU=Dallas,OU=Lokacja=Lokalizacje,DC=contoso,DC=com", "Austin", "OU=Użytkownicy standardowi,OU=Użytkownicy,OU=OU=OU=OU=OU=Lokalizacje,DC=contoso,DC=com", "Austin", "OU=Użytkownicy standardowi,OU=Użytkownicy,OU=OU=OU=OU=Lokacje,DC=contoso,DC=com), "Austin", "OU=Użytkownicy standardowi,OU=Użytkownicy,OU=OU=OU=Dallas,OU=OU=Lokacje,DC=contoso,DC=com", "Austin", "OU=Użytkownicy standardowi,Użytkownicy,OU=Użytkownicy,OU=OU=OU=OU=Lokacje/Lokacje,DC=contoso,DC=com", "Austin", "O =Austin,OU=Lokalizacje,DC=contoso,DC=com", "Seattle", "OU=Użytkownicy standardowi,OU=Użytkownicy,OU=Seattle,OU=Lokalizacje,DC=contoso,DC=com", "Londyn", "OU=Użytkownicy standardowi",OU=Użytkownicy,OU=Londyn,OU=Lokalizacje,DC=contoso,DC=com")**  | nazwa nadrzędnaDistinguishedName     |     |  Tworzenie + aktualizacja |

Po zakończeniu konfiguracji mapowania atrybutów można teraz [włączyć i uruchomić usługę inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurowanie inicjowania obsługi administracyjnej przez użytkowników usługi Azure AD

W poniższych sekcjach opisano kroki konfigurowania inicjowania obsługi administracyjnej użytkowników z usługi Workday do usługi Azure AD dla wdrożeń tylko w chmurze.

* [Dodawanie aplikacji łącznika inicjowania obsługi administracyjnej usługi Azure AD i tworzenie połączenia z workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowań atrybutów Workday i Azure AD](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Postępuj zgodnie z poniższą procedurą tylko wtedy, gdy masz użytkowników tylko w chmurze, które muszą być aprowizowane do usługi Azure AD, a nie w lokalnej usłudze Active Directory.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika inicjowania obsługi administracyjnej usługi Azure AD i tworzenie połączenia z workday

**Aby skonfigurować usługę Workday na usługę Azure Active Directory dla użytkowników korzystających z usługi tylko w chmurze:**

1. Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz **pozycję Dodaj aplikację**, a następnie wybierz kategorię **Wszystkie.**

5. Wyszukaj **usługę Workday do inicjowania obsługi administracyjnej usługi Azure AD**i dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu szczegółów aplikacji wybierz pozycję **Inicjowanie obsługi administracyjnej**.

7. Zmień **tryb** **inicjowania obsługi administracyjnej** na **Automatyczny**.

8. Wypełnij sekcję **Poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika dnia roboczego** — wprowadź nazwę użytkownika konta systemu integracji workday z dołączenie nazwy domeny dzierżawy. Powinien wyglądać coś takiego:username@contoso4

   * **Hasło dnia roboczego –** Wprowadź hasło konta systemu integracji Workday

   * **Adres URL interfejsu API usług sieci Web workday —** Wprowadź adres URL punktu końcowego usług sieci web workday dla dzierżawy. Ta wartość powinna https://wd3-impl-services1.workday.com/ccx/service/contoso4wyglądać następująco: , gdzie *contoso4* jest zastępowany poprawną nazwą dzierżawy i *wd3-impl* jest zastępowany ciągiem poprawnego środowiska. Jeśli ten adres URL nie jest znany, skontaktuj się z partnerem integracyjnym lub przedstawicielem pomocy technicznej workday, aby ustalić poprawny adres URL do użycia.

     > [!NOTE]
     > Domyślnie aplikacja używa usług Sieci Web Workday w wersji 21.1, jeśli w adresie URL nie określono żadnych informacji o wersji. Aby użyć określonej wersji interfejsu API usług Sieci Web Workday, użyj formatu adresu URL:https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# <br>
     > Przykład: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v31.0


   * **Wiadomość e-mail z powiadomieniem –** Wprowadź swój adres e-mail i zaznacz pole wyboru "Wyślij wiadomość e-mail w przypadku wystąpienia błędu".

   * Kliknij przycisk **Testuj połączenie.**

   * Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy adres URL i poświadczenia workday są prawidłowe w workday.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Część 2: Konfigurowanie mapowań atrybutów Workday i Azure AD

W tej sekcji skonfigurujesz sposób przepływu danych użytkownika z workday do usługi Azure Active Directory dla użytkowników tylko w chmurze.

1. Na karcie Inicjowanie obsługi administracyjnej w obszarze **Mapowania**kliknij pozycję **Synchronizuj pracowników z usługą Azure AD**.

2. W polu **Zakres obiektu źródłowego** można wybrać zestawy użytkowników w workday powinny być w zakresie inicjowania obsługi administracyjnej usługi Azure AD, definiując zestaw filtrów opartych na atrybutach. Domyślnym zakresem jest "wszyscy użytkownicy w Workday". Przykładowe filtry:

   * Przykład: Zakres do użytkowników z identyfikatorami pracowników między 1000000 a 2000000

      * Atrybut: WorkerID

      * Operator: REGEX Match

      * Wartość: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Przykład: Tylko pracownicy warunkowi, a nie stali

      * Atrybut: ContingentID

      * Operator: NIE MA WARTOŚCI NULL

3. W polu **Akcje obiektów docelowych** można globalnie filtrować, jakie akcje są wykonywane w usłudze Azure AD. **Tworzenie** i **aktualizowanie** są najczęściej.

4. W sekcji **Mapowania atrybutów** można zdefiniować sposób mapowania poszczególnych atrybutów workday na atrybuty usługi Active Directory.

5. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje następujące właściwości:

   * **Typ mapowania**

      * **Bezpośredni** — zapisuje wartość atrybutu Workday do atrybutu AD, bez żadnych zmian

      * **Stała** — zapis statycznej, stałej wartości ciągu do atrybutu AD

      * **Wyrażenie** — umożliwia zapisanie wartości niestandardowej do atrybutu AD na podstawie co najmniej jednego atrybutu Workday. [Aby uzyskać więcej informacji, zobacz ten artykuł na temat wyrażeń](../app-provisioning/functions-for-customizing-application-data.md).

   * **Atrybut source** — atrybut użytkownika z workday. Jeśli atrybut, którego szukasz, nie jest obecny, zobacz [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes).

   * **Wartość domyślna** — opcjonalnie. Jeśli atrybut źródłowy ma pustą wartość, mapowanie zapisze tę wartość.
            Najczęstszą konfiguracją jest pozostawienie tego pustego miejsca.

   * **Atrybut docelowy** — atrybut użytkownika w usłudze Azure AD.

   * **Dopasuj obiekty przy użyciu tego atrybutu** — czy ten atrybut powinien służyć do jednoznacznej identyfikacji użytkowników między workday i usługi Azure AD. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator procesu roboczego dla workday, który jest zazwyczaj mapowany do atrybutu identyfikator pracownika (nowy) lub atrybutu rozszerzenia w usłudze Azure AD.

   * **Pierwszeństwo dopasowania** — można ustawić wiele pasujących atrybutów. Gdy istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Jak tylko zostanie znaleziony dopasowania, nie dalsze pasujące atrybuty są oceniane.

   * **Zastosuj to mapowanie**

     * **Zawsze** — stosowanie tego mapowania zarówno w przypadku akcji tworzenia, jak i aktualizacji użytkownika

     * **Tylko podczas tworzenia** — zastosuj to mapowanie tylko do akcji tworzenia użytkownika

6. Aby zapisać mapowania, kliknij przycisk **Zapisz** u góry sekcji Mapowanie atrybutów.

Po zakończeniu konfiguracji mapowania atrybutów można teraz [włączyć i uruchomić usługę inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurowanie zapisywania zwrotnego atrybutu usługi Azure AD w dniu roboczym

Postępuj zgodnie z tymi instrukcjami, aby skonfigurować storadzkę adresów e-mail użytkowników i nazwy użytkownika z usługi Azure Active Directory do Workday.

* [Dodawanie aplikacji łącznika stortowania i tworzenie połączenia z workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurowanie mapowania atrybutów stornowania](#part-2-configure-writeback-attribute-mappings)
* [Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Część 1: Dodawanie aplikacji łącznika stortowania i tworzenie połączenia z Workday

**Aby skonfigurować łącznik stornia zwrotnego dnia roboczego:**

1. Przejdź do pozycji <https://portal.azure.com> (Plik > Nowy > Inny).

2. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz pozycję **Aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz **pozycję Dodaj aplikację**, a następnie wybierz kategorię **Wszystkie.**

5. Wyszukaj **pozycję Writeback**w worku i dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu szczegółów aplikacji wybierz pozycję **Inicjowanie obsługi administracyjnej**.

7. Zmień **tryb** **inicjowania obsługi administracyjnej** na **Automatyczny**.

8. Wypełnij sekcję **Poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika administratora** — wprowadź nazwę użytkownika konta systemu integracji workday z dołączenie nazwy domeny dzierżawy. Powinien wyglądać mniej więcej tak: *nazwa użytkownika\@contoso4*

   * **Hasło administratora –** Wprowadź hasło konta systemu integracji Workday

   * **Adres URL dzierżawy —** Wprowadź adres URL punktu końcowego usług sieci web workday dla dzierżawy. Ta wartość powinna https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resourceswyglądać następująco: , gdzie *contoso4* jest zastępowany poprawną nazwą dzierżawy i *wd3-impl* jest zastępowany ciągiem poprawnego środowiska (jeśli to konieczne).

   * **Wiadomość e-mail z powiadomieniem –** Wprowadź swój adres e-mail i zaznacz pole wyboru "Wyślij wiadomość e-mail w przypadku wystąpienia błędu".

   * Kliknij przycisk **Testuj połączenie.** Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy adres URL i poświadczenia workday są prawidłowe w workday.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Część 2: Konfigurowanie mapowania atrybutów stornowania

W tej sekcji skonfigurujesz sposób przepływu atrybutów stornia zwrotnego z usługi Azure AD do Workday. Obecnie łącznik obsługuje tylko storadzkę adresu e-mail i nazwy użytkownika do Workday.

1. Na karcie Inicjowanie obsługi administracyjnej w obszarze **Mapowania**kliknij pozycję **Synchronizuj użytkowników usługi Azure Active Directory z workiem**.

2. W polu **Zakres obiektu źródłowego** można opcjonalnie filtrować, które zestawy użytkowników w usłudze Azure Active Directory powinny mieć swoje adresy e-mail zapisane z powrotem do workday. Domyślnym zakresem jest "wszyscy użytkownicy w usłudze Azure AD".

3. W sekcji **Mapowania atrybutów** zaktualizuj pasujący identyfikator, aby wskazać atrybut w usłudze Azure Active Directory, w którym przechowywany jest identyfikator pracownika workday lub identyfikator pracownika. Popularną metodą dopasowywania jest zsynchronizowanie identyfikatora pracownika workday lub identyfikator pracownika do rozszerzeniaAttribute1-15 w usłudze Azure AD, a następnie użyć tego atrybutu w usłudze Azure AD, aby dopasować użytkowników z powrotem w Workday.

4. Zazwyczaj mapujesz atrybut *UserPrincipalName* usługi Azure AD do atrybutu Workday *UserID* i mapujesz atrybut *poczty* usługi Azure AD na atrybut Workday *EmailAddress.* Aby zapisać mapowania, kliknij przycisk **Zapisz** u góry sekcji Mapowanie atrybutów.

Po zakończeniu konfiguracji mapowania atrybutów można teraz [włączyć i uruchomić usługę inicjowania obsługi administracyjnej użytkowników](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie inicjowania obsługi administracyjnej użytkowników

Po zakończeniu konfiguracji aplikacji inicjowania obsługi administracyjnej workday można włączyć usługę inicjowania obsługi administracyjnej w witrynie Azure portal.

> [!TIP]
> Domyślnie po włączeniu usługi inicjowania obsługi administracyjnej, rozpocznie operacje inicjowania obsługi administracyjnej dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemy z danymi workday, zadanie inicjowania obsługi administracyjnej może zakończyć się niepowodzeniem i przejść do stanu kwarantanny. Aby tego uniknąć, jako najlepszą praktykę, zaleca się skonfigurowanie **filtru zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów z kilkoma użytkownikami testowymi przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, że mapowania działają i dają pożądane wyniki, można usunąć filtr lub stopniowo rozszerzać go, aby uwzględnić więcej użytkowników.

1. Na karcie **Inicjowanie obsługi administracyjnej** ustaw **stan inicjowania obsługi administracyjnej** **na Włączone**.

2. Kliknij pozycję **Zapisz**.

3. Ta operacja rozpocznie synchronizację początkową, która może zająć zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie Workday. 

4. W dowolnym momencie sprawdź **inspekcję dzienników** kartę w witrynie Azure portal, aby zobaczyć, jakie akcje usługa inicjowania obsługi administracyjnej wykonała. Dzienniki inspekcji zawierają listę wszystkich zdarzeń synchronizacji wykonywanych przez usługę inicjowania obsługi administracyjnej, takich jak użytkownicy są odczytywane z programu Workday, a następnie dodawane lub aktualizowane do usługi Active Directory. Zapoznaj się z sekcją Rozwiązywanie problemów, aby uzyskać instrukcje dotyczące przeglądania dzienników inspekcji i naprawiania błędów inicjowania obsługi administracyjnej.

5. Po zakończeniu synchronizacji początkowej zostanie ono napisać raport podsumowujący inspekcję na karcie **Inicjowanie obsługi administracyjnej,** jak pokazano poniżej.

   ![Azure Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

* **Pytania dotyczące możliwości rozwiązania**
  * [W jaki sposób podczas przetwarzania nowego pracownika z programu Workday rozwiązanie ustawia hasło dla nowego konta użytkownika w usłudze Active Directory?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Czy rozwiązanie obsługuje wysyłanie powiadomień e-mail po zakończeniu operacji inicjowania obsługi administracyjnej?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Jak zarządzać dostarczaniem haseł dla nowych pracowników i bezpiecznie zapewnić mechanizm resetowania hasła?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Czy buforowanie rozwiązania Workday profilów użytkowników w chmurze usługi Azure AD lub w warstwie agenta inicjowania obsługi administracyjnej?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Czy rozwiązanie obsługuje przypisywanie lokalnych grup usługi AD do użytkownika?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Które interfejsy API workday używa rozwiązania do wykonywania zapytań i aktualizowania profilów roboczych workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Czy mogę skonfigurować dzierżawę HCM workday z dwoma dzierżawami usługi Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Dlaczego aplikacja inicjowania obsługi administracyjnej użytkowników "Workday to Azure AD" nie jest obsługiwana, jeśli wdrożyliśmy usługę Azure AD Connect?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Jak zaproponować ulepszenia lub zażądać nowych funkcji związanych z integracją Workday i Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Pytania dotyczące agenta inicjowania obsługi administracyjnej**
  * [Co to jest wersja GA agenta inicjowania obsługi administracyjnej?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Skąd mam wiedzieć, jak jest dostępna wersja mojego agenta inicjującego?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Czy firma Microsoft automatycznie wypycha aktualizacje agenta inicjowania obsługi administracyjnej?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Czy mogę zainstalować agenta inicjowania obsługi administracyjnej na tym samym serwerze z uruchomieniem usługi Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Jak skonfigurować agenta inicjowania obsługi administracyjnej do używania serwera proxy do wychodzącej komunikacji HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Jak upewnić się, że agent inicjujący inicjowanie obsługi administracyjnej jest w stanie komunikować się z dzierżawą usługi Azure AD i żadne zapory nie blokują portów wymaganych przez agenta?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Jak wyrejestrować domenę skojarzoną z moim agentem aprowizacji?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Jak odinstalować agenta inicjującego?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Pytania dotyczące mapowania i konfiguracji atrybutów usługi AD w ciągu dnia roboczego**
  * [Jak wykonać kopię zapasową lub wyeksportować roboczą kopię mapowania i schematu aprowizowania w ciągu dnia pracy?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Mam atrybuty niestandardowe w workday i usługi Active Directory. Jak skonfigurować rozwiązanie do pracy z atrybutami niestandardowymi?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Czy mogę aprowizować zdjęcie użytkownika z workday do usługi Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Jak synchronizować numery telefonów komórkowych z workday na podstawie zgody użytkownika na użycie publiczne?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Jak sformatować nazwy wyświetlane w urzędzie obsługi ad na podstawie atrybutów departamentu/kraju/miasta użytkownika i obsługiwać wariancje regionalne?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak użyć SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Jak usunąć znaki ze znakami diakrytycznymi i przekonwertować je na zwykłe alfabety angielskie?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Pytania dotyczące możliwości rozwiązania

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>W jaki sposób podczas przetwarzania nowego pracownika z programu Workday rozwiązanie ustawia hasło dla nowego konta użytkownika w usłudze Active Directory?

Gdy lokalny agent inicjowania obsługi administracyjnej otrzymuje żądanie utworzenia nowego konta usługi AD, automatycznie generuje złożone losowe hasło zaprojektowane w celu spełnienia wymagań dotyczących złożoności hasła zdefiniowanych przez serwer usługi AD i ustawia je w obiekcie użytkownika. To hasło nie jest nigdzie rejestrowane.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Czy rozwiązanie obsługuje wysyłanie powiadomień e-mail po zakończeniu operacji inicjowania obsługi administracyjnej?

Nie, wysyłanie powiadomień e-mail po zakończeniu operacji inicjowania obsługi administracyjnej nie jest obsługiwane w bieżącej wersji.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Jak zarządzać dostarczaniem haseł dla nowych pracowników i bezpiecznie zapewnić mechanizm resetowania hasła?

Jednym z ostatnich kroków związanych z inicjowania obsługi administracyjnej nowego konta usługi AD jest dostarczenie tymczasowego hasła przypisanego do konta usługi AD użytkownika. Wiele przedsiębiorstw nadal korzysta z tradycyjnego podejścia do dostarczania tymczasowego hasła do menedżera użytkownika, który następnie przekazuje hasło nowemu pracownikowi zatrudnionemu/warunkowemu. Ten proces ma nieodłączną wadę zabezpieczeń i istnieje opcja dostępna do zaimplementowania lepszego podejścia przy użyciu funkcji usługi Azure AD.

W ramach procesu rekrutacji zespoły HR zwykle sprawdzają przeszłość i sprawdzają numer telefonu komórkowego nowego pracownika. Dzięki integracji inicjowania obsługi administracyjnej użytkowników usługi AD można budować na podstawie tego faktu i wdrożyć funkcję samoobsługowego resetowania hasła dla użytkownika w dniu 1. Jest to realizowane przez propagowanie atrybutu "Numer mobilny" nowego pracownika z workday do usługi AD, a następnie z usługi AD do usługi Azure AD przy użyciu usługi Azure AD Connect. Po pojawieniu się "Numeru telefonu komórkowego" w usłudze Azure AD można włączyć [samoobsługowe resetowanie haseł (SSPR)](../authentication/howto-sspr-authenticationdata.md) dla konta użytkownika, aby w dniu 1 nowy pracownik mógł użyć zarejestrowanego i zweryfikowanego numeru telefonu komórkowego do uwierzytelniania.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Czy buforowanie rozwiązania Workday profilów użytkowników w chmurze usługi Azure AD lub w warstwie agenta inicjowania obsługi administracyjnej?

Nie, rozwiązanie nie utrzymuje pamięci podręcznej profili użytkowników. Usługa inicjowania obsługi administracyjnej usługi Azure AD po prostu działa jako procesor danych, odczytując dane z workday i zapisując do docelowej usługi Active Directory lub usługi Azure AD. Szczegółowe informacje dotyczące prywatności i przechowywania danych można znaleźć w sekcji [Zarządzanie danymi osobowymi.](#managing-personal-data)

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Czy rozwiązanie obsługuje przypisywanie lokalnych grup usługi AD do użytkownika?

Ta funkcja nie jest obecnie obsługiwana. Zalecane obejście jest wdrożenie skryptu programu PowerShell, który odpytowuje punkt końcowy interfejsu API programu Microsoft Graph dla [danych dziennika inspekcji](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-beta) i używa go do wyzwalania scenariuszy, takich jak przypisanie grupy. Ten skrypt programu PowerShell można dołączyć do harmonogramu zadań i wdrożyć w tym samym polu z uruchomionym agentem inicjującym aprowizacji.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Które interfejsy API workday używa rozwiązania do wykonywania zapytań i aktualizowania profilów roboczych workday?

Rozwiązanie korzysta obecnie z następujących interfejsów API workday:

* Format **adresu URL interfejsu API usług sieci Web usługi Workday** używany w sekcji Poświadczenia **administratora** określa wersję interfejsu API używaną do Get_Workers
  * Jeśli format adresu URL to:\#\#\#\#\.https://\.com/ccx/service/tenantName , a następnie jest używany interfejs API w wersji 21.1. 
  * Jeśli format adresu URL to:\#\#\#\#\.https://\.com/ccx/service/tenantName/Human\_Resources , a następnie jest używany interfejs API w wersji 21.1 
  * Jeśli format adresu URL to:\#\#\#\#\.\.https:// workday com/ccx/service/tenantName/Human\_Resources/v\# \# \. \# , używana jest określona wersja interfejsu API. (Przykład: jeśli określono wersja 34.0, to jest używany.)  
   
* Funkcja writeback wiadomości e-mail w dniu roboczym używa Maintain_Contact_Information (wersja 26.1) 
* Funkcja writeback nazwy użytkownika w dniu roboczym używa Update_Workday_Account (wersja 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Czy mogę skonfigurować dzierżawę HCM workday z dwoma dzierżawami usługi Azure AD?

Tak, ta konfiguracja jest obsługiwana. Oto kroki wysokiego poziomu, aby skonfigurować ten scenariusz:

* Wdrażanie agenta inicjowania obsługi administracyjnej #1 i rejestrowanie go w #1 dzierżawy usługi Azure AD.
* Wdrażanie agenta inicjowania obsługi administracyjnej #2 i rejestrowanie go w #2 dzierżawy usługi Azure AD.
* Na podstawie "domen podrzędnych", którymi będzie zarządzać każdy agent inicjowania obsługi administracyjnej, skonfiguruj każdego agenta z domenami. Jeden agent może obsługiwać wiele domen.
* W witrynie Azure portal skonfiguruj aplikację do inicjowania obsługi administracyjnej użytkowników usługi AD w każdej dzierżawie i skonfiguruj ją z odpowiednimi domenami.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Dlaczego aplikacja inicjowania obsługi administracyjnej użytkowników "Workday to Azure AD" nie jest obsługiwana, jeśli wdrożyliśmy usługę Azure AD Connect?

Gdy usługa Azure AD jest używana w trybie hybrydowym (gdzie zawiera kombinację użytkowników w chmurze + lokalnie), ważne jest, aby mieć jasną definicję "źródła uprawnień". Zazwyczaj scenariusze hybrydowe wymagają wdrożenia usługi Azure AD Connect. Po wdrożeniu usługi Azure AD Connect lokalna usługa AD jest źródłem uprawnień. Wprowadzenie łącznika Workday do usługi Azure AD do koszyka może prowadzić do sytuacji, w której wartości atrybutów Workday mogą potencjalnie zastąpić wartości ustawione przez usługę Azure AD Connect. W związku z tym użycie aplikacji inicjowania obsługi administracyjnej usługi "Workday to Azure AD" nie jest obsługiwane, gdy jest włączona usługa Azure AD Connect. W takich sytuacjach zaleca się używanie aplikacji inicjującej inicjowanie obsługi administracyjnej "Workday to AD", aby uzyskać użytkownikom dostęp do lokalnej usługi AD, a następnie zsynchronizować ich z usługą Azure AD Przy użyciu usługi Azure AD Connect.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Jak zaproponować ulepszenia lub zażądać nowych funkcji związanych z integracją Workday i Azure AD?

Twoja opinia jest wysoko ceniona, ponieważ pomaga nam wyznaczyć kierunek przyszłych wydań i ulepszeń. Z zadowoleniem przyjmujemy wszystkie opinie i zachęcamy do przesłania swojego pomysłu lub sugestii ulepszeń na [forum opinii usługi Azure AD.](https://feedback.azure.com/forums/169401-azure-active-directory) Aby uzyskać konkretne opinie związane z integracją Workday, wybierz kategorię *Aplikacje SaaS* i wyszukaj za pomocą słów kluczowych *Workday,* aby znaleźć istniejące opinie związane z Workday.

![Aplikacje UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![Dzień roboczy uservoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Sugerując nowy pomysł, sprawdź, czy ktoś inny już zasugerował podobną funkcję. W takim przypadku możesz głosować na funkcję lub żądanie ulepszenia. Możesz również zostawić komentarz dotyczący konkretnego przypadku użycia, aby pokazać swoje poparcie dla tego pomysłu i zademonstrować, w jaki sposób funkcja będzie dla Ciebie również cenna.

### <a name="provisioning-agent-questions"></a>Pytania dotyczące agenta inicjowania obsługi administracyjnej

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Co to jest wersja GA agenta inicjowania obsługi administracyjnej?

* Wersja GA agenta inicjowania obsługi administracyjnej jest 1.1.30 i powyżej.
* Jeśli wersja agenta jest mniejsza niż 1.1.30, zostanie uruchomiona publiczna wersja w wersji zapoznawczej i zostanie ona automatycznie zaktualizowana do wersji GA, jeśli serwer obsługujący agent ma środowisko uruchomieniowe .NET 4.7.1.
  * Wersję [platformy .NET zainstalowaną](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) na serwerze można sprawdzić. Jeśli na serwerze nie jest uruchomiony .NET 4.7.1, można [pobrać i zainstalować plik .NET 4.7.1](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Agent inicjowania obsługi administracyjnej zostanie automatycznie zaktualizowany do wersji GA po zainstalowaniu platformy .NET 4.7.1.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Skąd mam wiedzieć, jak jest dostępna wersja mojego agenta inicjującego?

* Zaloguj się na serwerze Windows, na którym jest zainstalowany agent inicjowania obsługi administracyjnej.
* Przejdź do menu**Odinstalowywanie lub zmienianie** menu Program **w Panelu sterowania** -> 
* Poszukaj wersji odpowiadającej wpisowi **Agenta aprowizującego usługi Microsoft Azure AD Connect**

  ![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Czy firma Microsoft automatycznie wypycha aktualizacje agenta inicjowania obsługi administracyjnej?

Tak, firma Microsoft automatycznie aktualizuje agenta inicjującego aprowizacji. Aktualizacje automatyczne można wyłączyć, zatrzymując usługę systemu **Windows Microsoft Azure AD Connect Agent Updater**.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Czy mogę zainstalować agenta inicjowania obsługi administracyjnej na tym samym serwerze z uruchomieniem usługi Azure AD Connect?

Tak, agenta inicjowania obsługi administracyjnej można zainstalować na tym samym serwerze, na który działa usługa Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>W momencie konfiguracji agent inicjowania obsługi administracyjnej monituje o poświadczenia administratora usługi Azure AD. Czy agent przechowuje poświadczenia lokalnie na serwerze?

Podczas konfiguracji agent inicjowania obsługi administracyjnej monituje o poświadczenia administratora usługi Azure AD tylko do łączenia się z dzierżawą usługi Azure AD. Nie przechowuje poświadczeń lokalnie na serwerze. Zachowuje jednak poświadczenia używane do łączenia się z *lokalną domeną usługi Active Directory* w lokalnym magazynie haseł systemu Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Jak skonfigurować agenta inicjowania obsługi administracyjnej do używania serwera proxy do wychodzącej komunikacji HTTP?

Agent inicjowania obsługi administracyjnej obsługuje korzystanie z wychodzącego serwera proxy. Można go skonfigurować, edytując plik konfiguracji agenta **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Dodaj do niego następujące wiersze, na końcu pliku `</configuration>` tuż przed tagiem zamknięcia.
Zastąp zmienne [proxy-server] i [proxy-port] nazwą serwera proxy i wartościami portów.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Jak upewnić się, że agent inicjujący inicjowanie obsługi administracyjnej jest w stanie komunikować się z dzierżawą usługi Azure AD i żadne zapory nie blokują portów wymaganych przez agenta?

Można również sprawdzić, czy wszystkie wymagane porty są otwarte, otwierając [narzędzie testowe portów łączników](https://aadap-portcheck.connectorporttest.msappproxy.net/) z sieci lokalnej. Więcej zielonych znaczników wyboru oznacza większą odporność.

Aby upewnić się, że narzędzie daje właściwe wyniki, należy:

* Otwórz narzędzie w przeglądarce z serwera, na którym zainstalowano agenta inicjowania obsługi administracyjnej.
* Upewnij się, że wszystkie serwery proxy lub zapory mające zastosowanie do agenta inicjowania obsługi administracyjnej są również stosowane do tej strony. Można to zrobić w programie Internet Explorer, przechodząc do **ustawienia -> Opcje internetowe -> Połączenia -> ustawienia sieci LAN**. Na tej stronie zobaczysz pole "Użyj serwera proxy dla swojej sieci LAN". Zaznacz to pole i umieść adres serwera proxy w polu "Adres".

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Czy można skonfigurować jeden agent inicjowania obsługi administracyjnej w celu aprowizowania wielu domen usługi AD?

Tak, jeden agent inicjowania obsługi administracyjnej można skonfigurować do obsługi wielu domen usługi AD, o ile agent ma linię wzroku do odpowiednich kontrolerów domeny. Firma Microsoft zaleca skonfigurowanie grupy 3 agentów inicjowania obsługi administracyjnej obsługujących ten sam zestaw domen usługi AD, aby zapewnić wysoką dostępność i zapewnić obsługę awaryjną.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Jak wyrejestrować domenę skojarzoną z moim agentem aprowizacji?

* W witrynie Azure portal pobierz *identyfikator dzierżawy* dzierżawy usługi Azure AD.
* Zaloguj się do serwera Windows z uruchomionym agentem inicjującym aprowizacji.
* Otwórz program PowerShell jako administrator systemu Windows.
* Zmień katalog zawierający skrypty rejestracji i uruchom następujące polecenia \[\] zastępujące parametr identyfikatora dzierżawy wartością identyfikatora dzierżawy.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Z listy agentów, które się pojawią `id` — skopiuj wartość pola z tego *zasobu,* którego nazwa zasobu jest równa nazwie domeny usługi AD.
* Wklej wartość identyfikatora do tego polecenia i wykonaj polecenie w programie PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Uruchom ponownie kreatora konfiguracji agenta.
* Wszystkie inne agentów, które zostały wcześniej przypisane do tej domeny, będą musiały zostać ponownie skonfigurowane.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Jak odinstalować agenta inicjującego?

* Zaloguj się na serwerze Windows, na którym jest zainstalowany agent inicjowania obsługi administracyjnej.
* Przejdź do menu**Odinstalowywanie lub zmienianie** menu Program **w Panelu sterowania** -> 
* Odinstaluj następujące programy:
  * Agent inicjowania obsługi administracyjnej usługi Microsoft Azure AD Connect
  * Aktualizacja agenta usługi Microsoft Azure AD Connect
  * Pakiet agenta aprowizacji usługi Microsoft Azure AD Connect

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Pytania dotyczące mapowania i konfiguracji atrybutów usługi AD w ciągu dnia roboczego

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Jak wykonać kopię zapasową lub wyeksportować roboczą kopię mapowania i schematu aprowizowania w ciągu dnia pracy?

Za pomocą interfejsu API programu Microsoft Graph można wyeksportować konfigurację inicjowania obsługi administracyjnej użytkownika w dniu roboczym. Szczegółowe informacje można znaleźć w sekcji [Eksportowanie i importowanie konfiguracji mapowania atrybutów użytkownika workday.](#exporting-and-importing-your-configuration)

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Mam atrybuty niestandardowe w workday i usługi Active Directory. Jak skonfigurować rozwiązanie do pracy z atrybutami niestandardowymi?

Rozwiązanie obsługuje niestandardowe atrybuty Workday i Active Directory. Aby dodać atrybuty niestandardowe do schematu mapowania, otwórz blok **Mapowanie atrybutów** i przewiń w dół, aby rozwinąć sekcję **Pokaż opcje zaawansowane**. 

![Edytuj listę atrybutów](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Aby dodać niestandardowe atrybuty Workday, wybierz opcję *Edytuj listę atrybutów dla workday* i dodaj niestandardowe atrybuty usługi AD, wybierz opcję *Edytuj listę atrybutów dla lokalnej usługi Active Directory*.

Zobacz też:

* [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Jak skonfigurować rozwiązanie do aktualizowania tylko atrybutów w usłudze AD na podstawie zmian w dniu roboczym i nie tworzenia nowych kont usługi AD?

Tę konfigurację można osiągnąć, ustawiając **akcje obiektów docelowych** w bloku **Mapowania atrybutów,** jak pokazano poniżej:

![Akcja aktualizacji](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaznacz pole wyboru "Aktualizuj", aby tylko operacje aktualizacji były przepływowe z dnia roboczego do usługi AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Czy mogę aprowizować zdjęcie użytkownika z workday do usługi Active Directory?

Rozwiązanie obecnie nie obsługuje ustawiania atrybutów binarnych, takich jak *thumbnailPhoto* i *jpegPhoto* w usłudze Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Jak synchronizować numery telefonów komórkowych z workday na podstawie zgody użytkownika na użycie publiczne?

* Przejdź do bloku "Inicjowanie obsługi administracyjnej" aplikacji inicjowania obsługi administracyjnej workday.
* Kliknij mapowania atrybutów 
* W obszarze **Mapowania**wybierz pozycję **Synchronizuj pracowników dni roboczych z lokalną usługą Active Directory** (lub **synchronizuj pracowników dni roboczych z usługą Azure AD).**
* Na stronie Mapowania atrybutów przewiń w dół i zaznacz pole "Pokaż opcje zaawansowane".  Kliknij listę **atrybutów Edytuj dla Workday**
* W bloku, który otwiera się, znajdź atrybut "Mobile" i kliknij wiersz, aby edytować **wyrażenie API** ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Zastąp **wyrażenie interfejsu API** następującym nowym wyrażeniem, które pobiera służbowy numer telefonu komórkowego tylko wtedy, gdy "Flaga użycia publicznego" jest ustawiona na "True" w workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Zapisz listę atrybutów.
* Zapisz mapowanie atrybutów.
* Wyczyść bieżący stan i uruchom ponownie pełną synchronizację.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Jak sformatować nazwy wyświetlane w urzędzie obsługi ad na podstawie atrybutów departamentu/kraju/miasta użytkownika i obsługiwać wariancje regionalne?

Jest to typowe wymaganie, aby skonfigurować atrybut *displayName* w umiań AD, tak aby również dostarcza informacji o dziale użytkownika i kraju/regionu. Na przykład, jeśli John Smith pracuje w Dziale Marketingu w USA, możesz chcieć, aby jego *nazwa wyświetlacza* była wyświetlana jako *Smith, John (Marketing-US).*

Oto jak można obsługiwać takie wymagania dotyczące konstruowania *CN* lub *displayName,* aby uwzględnić atrybuty, takie jak firma, jednostka biznesowa, miasto lub kraj/region.

* Każdy atrybut Workday jest pobierany przy użyciu podstawowego wyrażenia interfejsu API XPATH, które można konfigurować w **wykazie mapowania atrybutów -> sekcji zaawansowanej - > Edytuj listę atrybutów dla workday**. Oto domyślne wyrażenie API XPATH dla atrybutów Workday *PreferredFirstName*, *PreferredLastName*, *Company* i *SupervisoryOrganization.*

     | Atrybut dzień roboczy | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | Preferowana nazwa powiersz | wd:Pracownik/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | Preferowana nazwa ostatnia | wd:Pracownik/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Firma | wd:Pracownik/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | Organizacja nadzorcza | wd:Pracownik/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Upewnij się w zespole Workday, że powyższe wyrażenie interfejsu API jest prawidłowe dla konfiguracji dzierżawy workday. W razie potrzeby można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes).

* Podobnie informacje o kraju obecne w workday są pobierane przy użyciu następującego XPATH: *wd:worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     W sekcji lista atrybutów Dzień roboczy dostępnych jest 5 atrybutów związanych z krajem.

     | Atrybut dzień roboczy | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | KrajReferencja | wd:Pracownik/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferencePrzyjaźliwość | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | KrajReferycjaNumeryczny | wd:Pracownik/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | KrajReferenceTwoLetter | wd:Pracownik/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | KrajRegionReferencja | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Upewnij się w zespole Workday, że powyższe wyrażenia interfejsu API są prawidłowe dla konfiguracji dzierżawy workday. W razie potrzeby można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes).

* Aby utworzyć odpowiednie wyrażenie mapowania atrybutów, należy zidentyfikować atrybut Workday "autorytatywnie" reprezentuje imię użytkownika, nazwisko, kraj/region i dział. Załóżmy, że atrybuty są *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* i *SupervisoryOrganization* odpowiednio. Można użyć tego do utworzenia wyrażenia dla atrybutu AD *displayName* w następujący sposób, aby uzyskać nazwę wyświetlaną, taką jak *Smith, John (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Po uzyskaniu odpowiedniego wyrażenia edytuj tabelę Mapowania atrybutów i modyfikuj mapowanie atrybutów *displayName,* jak pokazano poniżej: ![DisplayName Mapping](./media/workday-inbound-tutorial/wd_displayname_map.png)

* Rozszerzając powyższy przykład, załóżmy, że chcesz przekonwertować nazwy miast pochodzące z Workday na wartości skrócone, a następnie użyć go do tworzenia nazw wyświetlanych, takich jak *Smith, John (CHI)* lub *Doe, Jane (NYC),* wtedy ten wynik można osiągnąć za pomocą wyrażenia Przełącz z atrybutem *Gmina* Workday jako zmienna determinanta.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Zobacz też:
  * [Składnia funkcji przełączania](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Łączenie składni funkcji](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Dołącz składnię funkcji](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak użyć SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?

Załóżmy, że chcesz wygenerować unikatowe wartości dla *atrybutu samAccountName* przy użyciu kombinacji atrybutów *FirstName* i *LastName* z Workday. Podane poniżej jest wyrażenie, które można rozpocząć od:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Jak działa powyższe wyrażenie: Jeśli użytkownik jest John Smith, najpierw próbuje wygenerować JSmith, jeśli JSmith już istnieje, a następnie generuje JoSmith, jeśli istnieje, generuje JohSmith. Wyrażenie zapewnia również, że wygenerowana wartość spełnia ograniczenie długości i ograniczenie znaków specjalnych skojarzone z *samAccountName*.

Zobacz też:

* [Składnia funkcji środkowej](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Zamień składnię funkcji](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Składnia funkcji SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Jak usunąć znaki ze znakami diakrytycznymi i przekonwertować je na zwykłe alfabety angielskie?

Użyj funkcji [NormalizeDiacritics,](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) aby usunąć znaki specjalne w imię i nazwisko użytkownika, podczas konstruowania adresu e-mail lub wartości CN dla użytkownika.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Ta sekcja zawiera szczegółowe wskazówki dotyczące rozwiązywania problemów z inicjowaniem obsługi administracyjnej z integracją workday przy użyciu dzienników inspekcji usługi Azure AD i dzienników Podglądu zdarzeń systemu Windows Server. Opiera się na ogólnych krokach rozwiązywania problemów i pojęciach przechwyconych w [samouczku: Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika](../app-provisioning/check-status-user-account-provisioning.md)

W tej sekcji omówiono następujące aspekty rozwiązywania problemów:

* [Konfigurowanie Podglądu zdarzeń systemu Windows do rozwiązywania problemów z agentami](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurowanie dzienników inspekcji portalu Azure do rozwiązywania problemów z usługami](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Opis dzienników dla operacji tworzenia konta użytkownika usługi AD](#understanding-logs-for-ad-user-account-create-operations)
* [Opis dzienników operacji aktualizacji programu Manager](#understanding-logs-for-manager-update-operations)
* [Rozwiązywanie często napotkanych błędów](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurowanie Podglądu zdarzeń systemu Windows do rozwiązywania problemów z agentami

* Zaloguj się na komputerze z systemem Windows Server, na którym wdrożono agenta inicjowania obsługi administracyjnej
* Otwórz aplikację klasyczną **Podgląd zdarzeń systemu Windows Server.**
* Wybierz **pozycję Dzienniki systemu Windows > aplikacji**.
* Użyj **bieżącego dziennika filtru...** aby wyświetlić wszystkie zdarzenia zarejestrowane w źródle **AAD. Connect.ProvisioningAgent** i wykluczyć zdarzenia o identyfikatorze zdarzenia "5", określając filtr "-5", jak pokazano poniżej.

  ![Podgląd zdarzeń systemu Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Kliknij **przycisk OK** i posortuj widok wyników według kolumny Data i **Godzina.**

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurowanie dzienników inspekcji portalu Azure do rozwiązywania problemów z usługami

* Uruchom [portal Azure](https://portal.azure.com)i przejdź do sekcji **Dzienniki inspekcji** aplikacji inicjowania obsługi administracyjnej workday.
* Użyj przycisku **Kolumny** na stronie Dzienniki inspekcji, aby wyświetlić tylko następujące kolumny w widoku (Data, Działanie, Stan, Przyczyna stanu). Ta konfiguracja gwarantuje, że można skupić się tylko na danych, które są istotne dla rozwiązywania problemów.

  ![Kolumny dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Aby filtrować widok, użyj parametrów zapytania **Cel** i **Zakres dat.** 
  * Ustaw parametr zapytania **docelowego** na "Identyfikator pracownika" lub "Identyfikator pracownika" obiektu roboczego workday.
  * Ustaw **zakres dat** na odpowiedni okres, w którym chcesz zbadać błędy lub problemy z inicjowaniem obsługi administracyjnej.

  ![Filtry dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Opis dzienników dla operacji tworzenia konta użytkownika usługi AD

Po wykryciu nowego zatrudnienia w workday (powiedzmy z identyfikatorem pracownika *21023),* usługa inicjowania obsługi administracyjnej usługi Azure AD próbuje utworzyć nowe konto użytkownika usługi AD dla pracownika i w procesie tworzy 4 rekordy dziennika inspekcji, jak opisano poniżej:

  [![Operacje tworzenia dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Po kliknięciu dowolnego z rekordów dziennika inspekcji zostanie otwarta strona **Szczegóły działania.** Oto, co wyświetla strona **Szczegóły działania** dla każdego typu rekordu dziennika.

* **Rekord importu dnia roboczego:** Ten rekord dziennika wyświetla informacje o pracowniku pobrane z dnia roboczego. Użyj informacji w sekcji *Dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z pobieraniem danych z dnia roboczego. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami interpretacji każdego pola.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* **Rekord importu usługi AD:** Ten rekord dziennika wyświetla informacje o koncie pobranym z usługi AD. Ponieważ podczas początkowego tworzenia użytkownika nie ma konta USŁUGI AD, *przyczyna stanu działania* wskazuje, że w usłudze Active Directory nie znaleziono konta z wartością atrybutu Pasujący identyfikator. Użyj informacji w sekcji *Dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z pobieraniem danych z dnia roboczego. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami interpretacji każdego pola.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Aby znaleźć rekordy dziennika agenta inicjowania obsługi administracyjnej odpowiadające tej operacji importowania usługi AD, otwórz dzienniki Podglądu zdarzeń systemu Windows i użyj **pola Znajdź...** opcja menu, aby znaleźć wpisy dziennika zawierające wartość atrybutu Pasujący identyfikator/właściwość łączenia (w tym przypadku *21023*).

  ![Znajdowanie](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Poszukaj wpisu o *identyfikatorze zdarzenia = 9*, który zapewni ci filtr wyszukiwania LDAP używany przez agenta do pobierania konta USŁUGI AD. Można sprawdzić, czy jest to odpowiedni filtr wyszukiwania do pobierania unikatowych wpisów użytkowników.

  ![Wyszukiwanie LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Rekord, który bezpośrednio następuje po nim z *identyfikatorem zdarzenia = 2* przechwytuje wynik operacji wyszukiwania i jeśli zwrócił żadnych wyników.

  ![Wyniki LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Rekord **akcji reguły synchronizacji:** Ten rekord dziennika wyświetla wyniki reguł mapowania atrybutów i skonfigurowane filtry zakresu wraz z akcją inicjowania obsługi administracyjnej, która zostanie podjęta w celu przetworzenia przychodzącego zdarzenia Workday. Użyj informacji w sekcji *Szczegóły dodatkowe* rekordu dziennika, aby rozwiązać problemy z akcją synchronizacji. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami interpretacji każdego pola.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Jeśli występują problemy z wyrażeń mapowania atrybutów lub przychodzące dane Workday ma problemy (na przykład: pusta lub null wartość dla wymaganych atrybutów), a następnie można zaobserwować błąd na tym etapie z ErrorCode podając szczegóły błędu.

* Rekord **eksportu usługi AD:** Ten rekord dziennika wyświetla wynik operacji tworzenia konta usługi AD wraz z wartościami atrybutów ustawionymi w procesie. Użyj informacji w sekcji *Szczegóły dodatkowe* rekordu dziennika, aby rozwiązać problemy z operacją tworzenia konta. Poniżej przedstawiono przykładowy rekord wraz ze wskaźnikami interpretacji każdego pola. W sekcji "Dodatkowe szczegóły" "Nazwa zdarzenia" jest ustawiona na "EntryExportAdd", "JoiningProperty" jest ustawiona na wartość atrybutu Pasujący identyfikator, "SourceAnchor" jest ustawiona na WorkdayID (WID) skojarzone z rekordem, a "TargetAnchor" jest ustawiona na wartość atrybutu AD "ObjectGuid" nowego użytkownika. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Aby znaleźć rekordy dziennika agenta inicjowania obsługi administracyjnej odpowiadające tej operacji eksportowania usługi AD, otwórz dzienniki Podglądu zdarzeń systemu Windows i użyj **pola Znajdź...** opcja menu, aby znaleźć wpisy dziennika zawierające wartość atrybutu Pasujący identyfikator/właściwość łączenia (w tym przypadku *21023*).  

  Poszukaj rekordu HTTP POST odpowiadającego sygnatury czasowej operacji eksportowania o *identyfikatorze zdarzenia = 2*. Ten rekord będzie zawierać wartości atrybutów wysyłane przez usługę inicjowania obsługi administracyjnej do agenta inicjowania obsługi administracyjnej.

  [![Scim Dodaj](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Natychmiast po powyższym zdarzeniu powinno być inne zdarzenie, które przechwytuje odpowiedź operacji tworzenia konta usługi AD. To zdarzenie zwraca nowy objectGuid utworzony w usłudze AD i jest ustawiony jako atrybut TargetAnchor w usłudze inicjowania obsługi administracyjnej.

  [![Scim Dodaj](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Opis dzienników operacji aktualizacji menedżera

Atrybut menedżera jest atrybutem odwołania w usłudze AD. Usługa inicjowania obsługi administracyjnej nie ustawia atrybutu menedżera w ramach operacji tworzenia użytkownika. Raczej atrybut menedżera jest ustawiany jako część operacji *aktualizacji* po utworzeniu konta USŁUGI AD dla użytkownika. Rozszerzając powyższy przykład, załóżmy, że nowy pracownik z identyfikatorem pracownika "21451" jest aktywowany w Workday, a nowy menedżer zatrudnienia (*21023*) ma już konto AD. W tym scenariuszu wyszukiwanie dzienników inspekcji dla użytkownika 21451 pokazuje się 5 wpisów.

  [![Aktualizacja menedżera](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Pierwsze 4 rekordy są podobne do tych, które zbadaliśmy w ramach operacji tworzenia użytkownika. 5 rekord jest eksport skojarzony z aktualizacją atrybutu menedżera. Rekord dziennika wyświetla wynik operacji aktualizacji menedżera konta usługi AD, która jest wykonywana przy użyciu atrybutu *ObjectGuid* menedżera.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Rozwiązywanie często napotkanych błędów

W tej sekcji opisano często widoczne błędy w inicjowaniu obsługi administracyjnej użytkowników programu Workday i sposób jego rozwiązania. Błędy są pogrupowane w następujący sposób:

* [Błędy agenta inicjowania obsługi administracyjnej](#provisioning-agent-errors)
* [Błędy łączności](#connectivity-errors)
* [Błędy tworzenia konta użytkownika usługi AD](#ad-user-account-creation-errors)
* [Błędy aktualizacji konta użytkownika usługi AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Błędy agenta inicjowania obsługi administracyjnej

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecana rozdzielczość|
|--|---|---|---|
|1.| Nie można uruchomić programu AADConnectProvisioningAgent podczas instalowania agenta inicjowania obsługi administracyjnej z komunikatem o błędzie: *nie można uruchomić usługi "Agent inicjowania usługi Microsoft Azure AD Connect" (AADConnectProvisioningAgent). Sprawdź, czy masz wystarczające uprawnienia do uruchomienia systemu.* | Ten błąd zwykle pojawia się, jeśli próbujesz zainstalować agenta inicjowania obsługi administracyjnej na kontrolerze domeny, a zasady grupy uniemożliwiają uruchomienie usługi.  Jest również widoczne, jeśli masz uruchomiona poprzednią wersję agenta i nie odinstalowałeś jej przed rozpoczęciem nowej instalacji.| Zainstaluj agenta inicjowania obsługi administracyjnej na serwerze innych niż kontroler domeny. Upewnij się, że poprzednie wersje agenta są odinstalowane przed zainstalowaniem nowego agenta.|
|2.| Usługa systemu Windows "Microsoft Azure AD Connect Agent inicjowania obsługi administracyjnej" jest w stanie *uruchamiania* i nie przełącza się na stan *uruchamiania.* | W ramach instalacji kreator agenta tworzy na serwerze konto lokalne **(NT Service\\AADConnectProvisioningAgent)** i jest to konto logowania używane do uruchamiania usługi. Jeśli zasady zabezpieczeń na serwerze Windows uniemożliwiają uruchamianie usług kont lokalnych, wystąpi ten błąd. | Otwórz *konsolę Usługi*. Kliknij prawym przyciskiem myszy usługę systemu Windows "Agent aprowizowania usługi Microsoft Azure AD Connect" i na karcie logowania określ konto administratora domeny, aby uruchomić usługę. Należy ponownie uruchomić usługę. |
|3.| Podczas konfigurowania agenta inicjowania obsługi administracyjnej z domeną usługi AD w kroku *Połącz usługa Active Directory,* kreator zajmuje dużo czasu, próbując załadować schemat usługi AD i ostatecznie upończenie limit czasu. | Ten błąd jest zazwyczaj wyświetlany, jeśli kreator nie może skontaktować się z serwerem kontrolera domeny usługi AD ze względu na problemy z zaporą. | Na ekranie *Kreatora połącz usługa Active Directory,* podając poświadczenia dla domeny usługi AD, istnieje opcja o nazwie *Wybierz priorytet kontrolera domeny*. Ta opcja służy do wybierania kontrolera domeny, który znajduje się w tej samej lokacji co serwer agenta i upewnij się, że nie ma reguł zapory blokujących komunikację. |

#### <a name="connectivity-errors"></a>Błędy łączności

Jeśli usługa inicjowania obsługi administracyjnej nie może połączyć się z usługą Workday lub Active Directory, może to spowodować przejście inicjowania obsługi administracyjnej do stanu poddanego kwarantannie. Skorzystaj z poniższej tabeli, aby rozwiązać problemy z łącznością.

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecana rozdzielczość|
|--|---|---|---|
|1.| Po **kliknięciu przycisku Testuj połączenie**pojawia się komunikat o błędzie: *Wystąpił błąd podczas łączenia się z usługą Active Directory. Upewnij się, że lokalny agent inicjowania obsługi administracyjnej jest uruchomiony i jest skonfigurowany z poprawną domeną usługi Active Directory.* | Ten błąd zwykle pojawia się, jeśli agent inicjowania obsługi administracyjnej nie jest uruchomiony lub istnieje komunikacja blokowania zapory między usługą Azure AD a agentem inicjującym aprowizacji. Ten błąd może również zostać wyświetlony, jeśli domena nie jest skonfigurowana w Kreatorze agenta. | Otwórz konsolę *Usługi* na serwerze Windows, aby potwierdzić, że agent jest uruchomiony. Otwórz kreatora agenta inicjowania obsługi administracyjnej i upewnij się, że właściwa domena jest zarejestrowana u agenta.  |
|2.| Zadanie inicjowania obsługi administracyjnej przechodzi do stanu kwarantanny w weekendy (pt-sob) i otrzymujemy powiadomienie e-mail, że występuje błąd z synchronizacją. | Częstą przyczyną występowania tego błędu są planowane przestoje produktu Workday. Jeśli korzystasz z dzierżawy implementacji produktu Workday, pamiętaj, że ten produkt ma w weekendy zaplanowane przestoje dzierżaw implementacji (zwykle od wieczora w piątek do rana w sobotę). W tym czasie aplikacje aprowizacji produktu Workday mogą przechodzić w stan kwarantanny, ponieważ nie mogą nawiązać połączenia z produktem Workday. Normalny stan zostaje przywrócony po ponownym przejściu w tryb online dzierżawy implementacji produktu Workday. W rzadkich przypadkach ten błąd może być także wyświetlany, jeśli zmieniło się hasło użytkownika systemu integracji z powodu odświeżenia dzierżawy lub jeśli konto zostało zablokowane bądź wygasło. | Zapytaj administratora produktu Workday lub partnera integracji o czas planowanych przestojów produktu Workday, aby wiedzieć, które komunikaty o alertach można zignorować, i móc potwierdzić dostępność wystąpienia produktu Workday po jego ponownym przejściu w tryb online.  |


#### <a name="ad-user-account-creation-errors"></a>Błędy tworzenia konta użytkownika usługi AD

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecana rozdzielczość|
|--|---|---|---|
|1.| Błędy operacji eksportu w dzienniku inspekcji z *komunikatem Błąd: OperationsError-SvcErr: Wystąpił błąd operacji. Nie skonfigurowano odwołania nadrzędnego dla usługi katalogowej. W związku z tym usługa katalogowa nie może wystawiać odwołań do obiektów poza tym lasem.* | Ten błąd jest zwykle wyświetlany, jeśli jednostka *organizacyjna kontenera usługi Active Directory* nie jest poprawnie ustawiona lub występują problemy z mapowaniem wyrażeń używanym dla *pliku parentDistinguishedName*. | Sprawdź parametr OU *kontenera usługi Active Directory* pod kątem literówek. Jeśli korzystasz z elementu *parentDistinguishedName* podczas mapowania atrybutów, upewnij się, że zawsze wynikiem jego oceny jest znany kontener w ramach domeny usługi AD. Sprawdź *Export* zdarzenia w dziennikach inspekcji, aby zobaczyć wygenerowaną wartość. |
|2.| Błędy operacji eksportu w dzienniku inspekcji z kodem błędu: *SystemForCrossDomainIdentityManagementBadResponse* i komunikat *Błąd: ConstraintViolation-AtrErr: Wartość w żądaniu jest nieprawidłowa. Wartość atrybutu nie znajdowała się w dopuszczalnym zakresie wartości. \nOłówne dane: CONSTRAINT_ATT_TYPE - firma*. | Chociaż ten błąd jest specyficzny dla atrybutu *firmy,* może zostać wyświetlony ten błąd dla innych atrybutów, takich jak *CN,* jak również. Ten błąd pojawia się z powodu ograniczenia schematu wymuszonego przez ad. Domyślnie atrybuty, takie jak *firma* i *CN* w układzie AD, mają górny limit 64 znaków. Jeśli wartość pochodząca z Workday jest więcej niż 64 znaków, wtedy zostanie wyświetlony ten komunikat o błędzie. | Sprawdź *Export* zdarzenia w dziennikach inspekcji, aby zobaczyć wartość atrybutu zgłoszonego w komunikacie o błędzie. Należy rozważyć obcięcie wartości pochodzącej z workday przy użyciu [funkcji Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) lub zmiana mapowania na atrybut AD, który nie ma podobnych ograniczeń długości.  |

#### <a name="ad-user-account-update-errors"></a>Błędy aktualizacji konta użytkownika usługi AD

Podczas procesu aktualizacji konta użytkownika usługi AD usługa inicjowania obsługi administracyjnej odczytuje informacje zarówno z workday, jak i usługi AD, uruchamia reguły mapowania atrybutów i określa, czy wszelkie zmiany muszą obowiązywać. W związku z tym zostanie wyzwolone zdarzenie aktualizacji. Jeśli którykolwiek z tych kroków napotka błąd, jest rejestrowany w dziennikach inspekcji. Skorzystaj z poniższej tabeli, aby rozwiązać problem typowych błędów aktualizacji.

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecana rozdzielczość|
|--|---|---|---|
|1.| Błędy akcji reguły synchronizacji w dzienniku inspekcji z komunikatem *EventName = EntrySynchronizationError i ErrorCode = EndpointNiedostępne*. | Ten błąd jest wyświetlany, jeśli usługa inicjowania obsługi administracyjnej nie może pobrać danych profilu użytkownika z usługi Active Directory z powodu błędu przetwarzania napotkanego przez lokalnego agenta inicjowania obsługi administracyjnej. | Sprawdź dzienniki Podglądu zdarzeń agenta inicjowania obsługi administracyjnej pod kątem zdarzeń powodujących błędy, które wskazują na problemy z operacją odczytu (filtruj według #2 o identyfikatorze zdarzenia). |
|2.| Atrybut menedżera w usłudze AD nie jest aktualizowany dla niektórych użytkowników w usłudze AD. | Najbardziej prawdopodobną przyczyną tego błędu jest użycie reguł zakresu, a menedżer użytkownika nie jest częścią zakresu. Możesz również napotkać ten problem, jeśli pasujący atrybut identyfikatora menedżera (np. identyfikator pracownika) nie zostanie znaleziony w docelowej domenie usługi AD lub nie zostanie ustawiony na poprawną wartość. | Przejrzyj filtr zakresu i dodaj użytkownika menedżera w zakresie. Sprawdź profil menedżera w usłudze AD, aby upewnić się, że istnieje wartość pasującego atrybutu Identyfikator. |

## <a name="managing-your-configuration"></a>Zarządzanie konfiguracją

W tej sekcji opisano, jak można dalej rozszerzać, dostosowywać i zarządzać konfiguracją inicjowania obsługi administracyjnej użytkownika opartą na worku. Obejmuje ona następujące tematy:

* [Dostosowywanie listy atrybutów użytkownika dnia roboczego](#customizing-the-list-of-workday-user-attributes)  
* [Eksportowanie i importowanie konfiguracji](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Dostosowywanie listy atrybutów użytkownika dnia roboczego

Aplikacje inicjowania obsługi administracyjnej workday dla usługi Active Directory i usługi Azure AD zawierają domyślną listę atrybutów użytkownika workday, które można wybrać. Listy te nie są jednak wyczerpujące. Workday obsługuje wiele setek możliwych atrybutów użytkownika, które mogą być standardowe lub unikatowe dla dzierżawy workday.

Usługa inicjowania obsługi administracyjnej usługi Azure AD obsługuje możliwość dostosowania listy lub atrybutu Workday, aby uwzględnić wszystkie atrybuty udostępniane w [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operacji interfejsu API zasobów ludzkich.

Aby to zrobić, należy użyć [Workday Studio](https://community.workday.com/studio-download) wyodrębnić xpath wyrażeń, które reprezentują atrybuty, które mają być używane, a następnie dodać je do konfiguracji inicjowania obsługi administracyjnej przy użyciu edytora atrybutów zaawansowanych w witrynie Azure portal.

**Aby pobrać wyrażenie XPath dla atrybutu użytkownika Workday:**

1. Pobierz i zainstaluj [Workday Studio](https://community.workday.com/studio-download). Aby uzyskać dostęp do instalatora, musisz mieć konto społeczności Workday.

2. Pobierz plik Workday Human_Resources WSDL z tego adresu URL:https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Uruchom Studio Workday.

4. Na pasku poleceń wybierz opcję **Workday > Test Web Service w testerze.**

5. Wybierz **opcję Zewnętrzna**i wybierz Human_Resources plik WSDL pobrany w kroku 2.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Ustaw **pole** Lokalizacja `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources`na , ale zastępując "IMPL-CC" rzeczywistym typem wystąpienia i "NAJEMCA" z rzeczywistą nazwą dzierżawy.

7. Ustaw **operację** na **Get_Workers**

8.  Kliknij małe **łącze konfiguracji** poniżej okienka Żądanie/Odpowiedź, aby ustawić poświadczenia workday. Zaznacz **pozycję Uwierzytelnianie**, a następnie wprowadź nazwę użytkownika i hasło dla konta systemu integracji workday. Pamiętaj, aby sformatować\@nazwę użytkownika jako dzierżawę nazw i pozostawić zaznaczoną opcję **Nazwa użytkownika WS-Security.**

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Kliknij przycisk **OK**.

10. W okienku **Żądanie** wklej w kodzie XML poniżej i ustaw **Employee_ID** identyfikatora pracownika rzeczywistego użytkownika w dzierżawie workday. Wybierz użytkownika, który ma atrybut wypełniony, który chcesz wyodrębnić.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Kliknij wyślij **żądanie** (zielona strzałka), aby wykonać polecenie. Jeśli to się powiedzie, odpowiedź powinna pojawić się w okienku **Odpowiedź.** Sprawdź odpowiedź, aby upewnić się, że ma dane wprowadzonego identyfikatora użytkownika, a nie błąd.

12. Jeśli to się powiedzie, skopiuj kod XML z **okienka Odpowiedź** i zapisz go jako plik XML.

13. Na pasku poleceń workday studio wybierz pozycję **Plik > otwórz plik...** i otwórz zapisany plik XML. Ta akcja otworzy plik w edytorze XML Workday Studio.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. W drzewie plików przejdź przez **/env: Koperta > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker,** aby znaleźć dane użytkownika.

15. W obszarze **wd: Pracownik**znajdź atrybut, który chcesz dodać, i wybierz go.

16. Skopiuj wyrażenie XPath dla wybranego atrybutu z pola **Ścieżka dokumentu.**

17. Usuń **prefiks /env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** z skopiowanego wyrażenia.

18. Jeśli ostatnim elementem w skopiowanym wyrażeniu jest węzeł (przykład: "/wd: Birth_Date"), następnie dołącz **/text()** na końcu wyrażenia. Nie jest to konieczne, jeśli ostatni element jest/@wd: atrybutem (przykład: " type").

19. Wynik powinien być `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`coś w stylu . Ta wartość jest to, co można skopiować do witryny Azure portal.

**Aby dodać niestandardowy atrybut użytkownika workday do konfiguracji inicjowania obsługi administracyjnej:**

1. Uruchom [witrynę Azure portal](https://portal.azure.com)i przejdź do sekcji Inicjowanie obsługi administracyjnej aplikacji workday, zgodnie z opisem we wcześniejszej części tego samouczka.

2. Ustaw **stan inicjowania obsługi administracyjnej** na **Wyłączone**i wybierz pozycję **Zapisz**. Ten krok pomoże upewnić się, że zmiany zostaną wprowadzone tylko wtedy, gdy będziesz gotowy.

3. W obszarze **Mapowania**wybierz pozycję **Synchronizuj pracowników dni roboczych z lokalną usługą Active Directory** (lub **synchronizuj pracowników dni roboczych z usługą Azure AD).**

4. Przewiń do dołu następnego ekranu i wybierz pozycję **Pokaż opcje zaawansowane**.

5. Wybierz **pozycję Edytuj listę atrybutów dla workday**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Przewiń do dołu listy atrybutów do miejsca, w którym znajdują się pola wejściowe.

7. W przeglądarce **Nazwa**wprowadź nazwę wyświetlaną atrybutu.

8. W przypadku **opcji Typ**wybierz typ, który odpowiednio odpowiada atrybutowi **(Ciąg** jest najczęściej spotykany).

9. W przypadku **wyrażenia interfejsu API**wprowadź wyrażenie XPath skopiowane z workday studio. Przykład: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wybierz **pozycję Dodaj atrybut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wybierz **pozycję Zapisz** powyżej, a następnie pozycję **Tak** w oknie dialogowym. Zamknij ekran mapowania atrybutów, jeśli jest on nadal otwarty.

12. Ponownie na głównej karcie **inicjowania obsługi administracyjnej** wybierz ponownie pozycję **Synchronizuj pracowników dni roboczych z lokalną usługą Active Directory** (lub **synchronizuj pracowników z usługą Azure AD).**

13. Wybierz **pozycję Dodaj nowe mapowanie**.

14. Nowy atrybut powinien być teraz wyświetlany na liście **atrybutów Źródło.**

15. Dodaj mapowanie nowego atrybutu zgodnie z potrzebami.

16. Po zakończeniu należy pamiętać, aby ustawić **stan inicjowania obsługi administracyjnej** z powrotem **na Włączone** i zapisać.

### <a name="exporting-and-importing-your-configuration"></a>Eksportowanie i importowanie konfiguracji

Zapoznaj się z artykułem [Eksportowanie i importowanie konfiguracji inicjowania obsługi administracyjnej](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

Rozwiązanie do inicjowania obsługi administracyjnej dnia roboczego dla usługi Active Directory wymaga zainstalowania agenta inicjowania obsługi administracyjnej na lokalnym serwerze systemu Windows, a ten agent tworzy dzienniki w dzienniku zdarzeń systemu Windows, które mogą zawierać dane osobowe w zależności od mapowania atrybutów usługi AD. Aby spełnić zobowiązania dotyczące prywatności użytkowników, można upewnić się, że żadne dane nie są przechowywane w dziennikach zdarzeń poza 48 godzin, konfiguruje zaplanowane zadanie systemu Windows, aby wyczyścić dziennik zdarzeń.

Usługa inicjowania obsługi administracyjnej usługi Azure AD należy do kategorii **procesora danych** klasyfikacji RODO. Jako potok podmiotu przetwarzającego dane usługa świadczy usługi przetwarzania danych dla kluczowych partnerów i konsumentów końcowych. Usługa inicjowania obsługi administracyjnej usługi Azure AD nie generuje danych użytkownika i nie ma niezależnej kontroli nad tym, jakie dane osobowe są zbierane i jak są używane. Pobieranie danych, agregacja, analiza i raportowanie w usłudze inicjowania obsługi administracyjnej usługi Azure AD są oparte na istniejących danych przedsiębiorstwa.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

W odniesieniu do przechowywania danych usługa inicjowania obsługi administracyjnej usługi Azure AD nie generuje raportów, nie przeprowadza analizy ani nie dostarcza szczegółowych informacji po upływie 30 dni. W związku z tym usługa inicjowania obsługi administracyjnej usługi Azure AD nie przechowuje, przetwarza ani nie przechowuje żadnych danych po upływie 30 dni. Ten projekt jest zgodny z przepisami RODO, przepisami dotyczącymi zgodności z przepisami firmy Microsoft dotyczącymi prywatności i zasadami przechowywania danych usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak przeglądać dzienniki i otrzymywać raporty dotyczące aktywności inicjowania obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Workday a usługą Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak zintegrować inne aplikacje SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Dowiedz się, jak zarządzać konfiguracjami inicjowania obsługi administracyjnej za pomocą interfejsów API programu Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
