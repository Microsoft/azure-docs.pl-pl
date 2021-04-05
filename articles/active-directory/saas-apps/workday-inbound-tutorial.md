---
title: 'Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników przy użyciu Azure Active Directory | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Azure Active Directory, aby automatycznie udostępniać i cofać obsługę administracyjną kont użytkowników w programie Workday.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: a34881901fd8642fff9ac37512cd2ef260ad9d1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954224"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników

Celem tego samouczka jest przedstawienie czynności, które należy wykonać, aby udostępnić profile procesów roboczych z produktu Workday do Active Directory lokalnego (AD).

>[!NOTE]
>Skorzystaj z tego samouczka, jeśli użytkownicy, którym chcesz zainicjować obsługę administracyjną z produktu Workday, wymagają lokalnego konta usługi AD i konta usługi Azure AD. 
>* Jeśli użytkownicy z produktu Workday potrzebują tylko konta usługi Azure AD (tylko użytkownicy w chmurze), zapoznaj się z samouczkiem dotyczącym konfigurowania inicjowania obsługi administracyjnej użytkowników w usłudze [Azure AD](workday-inbound-cloud-only-tutorial.md) . 
>* Aby skonfigurować Stornowanie atrybutów, takich jak adres e-mail, nazwa użytkownika i numer telefonu z usługi Azure AD do produktu Workday, zapoznaj się z samouczkiem dotyczącym [konfigurowania zapisywania zwrotnego](workday-writeback-tutorial.md)w dniu Workday.

## <a name="overview"></a>Omówienie

[Usługa aprowizacji użytkowników Azure Active Directory](../app-provisioning/user-provisioning.md) integruje się z [interfejsem API zasobów ludzkich firmy Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) , aby udostępnić konta użytkowników. Przepływy pracy aprowizacji użytkowników platformy Workday obsługiwane przez usługę aprowizacji użytkowników w usłudze Azure AD umożliwiają automatyzację następujących scenariuszy dotyczących zasobów ludzkich i zarządzania cyklem życia tożsamości:

* **Zatrudnianie nowych pracowników** — po dodaniu nowego pracownika do produktu Workday konto użytkownika jest automatycznie tworzone w Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md)z możliwością zapisu informacji kontaktowych zarządzanych przez dział IT do produktu Workday.

* **Aktualizacje atrybutu pracownika i profilu** — gdy rekord pracownika zostanie zaktualizowany w dniach roboczych (takich jak nazwa, tytuł lub Menedżer), jego konto użytkownika zostanie automatycznie zaktualizowane w Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* **Zakończenie zatrudnienia pracownika** — gdy pracownik zostanie przerwany w dniach roboczych, jego konto użytkownika jest automatycznie wyłączone w Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacjach SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md).

* Przedziały **pracownika** — gdy pracownik jest ponownie zatrudniany w dniu Workday, jego stare konto może zostać automatycznie ponownie uaktywnione lub zainicjowane (zależnie od preferencji) do Active Directory, Azure Active Directory i opcjonalnie Microsoft 365 i [innych aplikacji SaaS obsługiwanych przez usługę Azure AD](../app-provisioning/user-provisioning.md).

### <a name="whats-new"></a>Co nowego
W tej sekcji znajdują się najnowsze ulepszenia integracji z produktem Workday. Aby uzyskać listę kompleksowych aktualizacji, planowanych zmian i archiwów, odwiedź stronę [co nowego w Azure Active Directory?](../fundamentals/whats-new.md) 

* **Oct 2020 — włączono obsługę administracyjną na żądanie dla produktu Workday:** Przy użyciu [aprowizacji na żądanie](../app-provisioning/provision-on-demand.md) możesz teraz przetestować kompleksowe Inicjowanie obsługi dla określonego profilu użytkownika w dniu Workday, aby zweryfikować Mapowanie atrybutów i logikę wyrażenia.   

* **Może 2020 — możliwość stornowania numerów telefonów do produktu Workday:** Oprócz poczty e-mail i nazwy użytkownika można teraz Stornowaniem numeru telefonu służbowego i numeru telefonu komórkowego z usługi Azure AD do produktu Workday. Aby uzyskać więcej informacji, zapoznaj się z [samouczkiem dotyczącym aplikacji zapisywania zwrotnego](workday-writeback-tutorial.md).

* **Kwiecień 2020 — wsparcie dla najnowszej wersji interfejsu API usługi Web Services (WWS) platformy Workday:** Dwa razy rok w marcu i wrześniu firma Workday oferuje aktualizacje bogate w funkcje, które pomagają spełnić cele biznesowe i zmieniać wymagania pracowników. Aby zachować nowe funkcje dostarczone przez dzień Workday, możesz teraz bezpośrednio określić wersję interfejsu API WWS, która ma być używana w adresie URL połączenia. Aby uzyskać szczegółowe informacje na temat określania wersji interfejsu API produktu Workday, zapoznaj się z sekcją [Konfigurowanie łączności](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)z usługą Workday. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Dla kogo to rozwiązanie do aprowizacji użytkowników jest najlepiej dopasowane?

Rozwiązanie do aprowizacji użytkowników w ramach platformy Workday jest idealne dla:

* Organizacje, które chcą uzyskać wbudowane, oparte na chmurze rozwiązanie do aprowizacji użytkowników w ramach platformy Workday

* Organizacje, które wymagają bezpośredniej aprowizacji użytkowników z produktu Workday do Active Directory lub Azure Active Directory

* Organizacje, które wymagają aprowizacji użytkowników przy użyciu danych uzyskanych z modułu Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje, które wymagają przyłączenia, przeniesienia i opuszczenia użytkowników do co najmniej jednego Active Directory lasów, domen i jednostek organizacyjnych na podstawie informacji o zmianach wykrytych w module Workday HCM (zobacz [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organizacje korzystające z Microsoft 365 do poczty e-mail

## <a name="solution-architecture"></a>Architektura rozwiązania

W tej sekcji opisano kompleksową architekturę rozwiązania obsługi użytkowników dla typowych środowisk hybrydowych. Istnieją dwa powiązane przepływy:

* **Autorytatywny przepływ danych HR — od dnia roboczego do Active Directory lokalnego:** W tym przepływie zdarzenia procesu roboczego (takie jak nowe zatrudnienie, transfery, zakończenia) są najpierw wykonywane w dzierżawie usługi HR w usłudze w chmurze, a następnie dane zdarzenia są przesyłane do Active Directory lokalnych za pośrednictwem usług Azure AD i agenta aprowizacji. W zależności od zdarzenia może to prowadzić do tworzenia/aktualizowania/włączania/wyłączania operacji w usłudze AD.
* **Przepływ zapisywania zwrotnego — od Active Directory lokalnego do produktu Workday:** Po zakończeniu tworzenia konta w Active Directory zostanie ono zsynchronizowane z usługą Azure AD za pośrednictwem Azure AD Connect, a informacje takie jak adres e-mail, nazwa użytkownika i numer telefonu można napisać z powrotem do produktu Workday.

![Omówienie](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>Przepływ danych kompleksowego użytkownika

1. Zespół kadr wykonuje transakcje procesu roboczego (przydziały/Firmy przeprowadzkowe lub nowe zatrudnienie/zakończenia) w programie Workday HCM
2. Usługa Azure AD Provisioning uruchamia zaplanowane synchronizacje tożsamości z produktu Workday HR i identyfikuje zmiany, które należy przetworzyć w celu synchronizacji z Active Directory lokalnymi.
3. Usługa Azure AD Provisioning wywołuje lokalnego agenta inicjowania obsługi administracyjnej Azure AD Connect z ładunkiem żądania zawierającym operacje tworzenia/aktualizowania/włączania/wyłączania konta usługi AD.
4. Azure AD Connect Agent aprowizacji używa konta usługi do dodawania/aktualizowania danych konta usług AD.
5. Aparat Azure AD Connect/AD Sync uruchamia synchronizację Delta w celu ściągnięcia aktualizacji w usłudze AD.
6. Aktualizacje Active Directory są synchronizowane z Azure Active Directory.
7. Jeśli aplikacja [zapisywania zwrotnego dla produktu Workday](workday-writeback-tutorial.md) została skonfigurowana, zapisuje z powrotem atrybuty, takie jak adres e-mail, nazwa użytkownika i numer telefonu do dnia roboczego.

## <a name="planning-your-deployment"></a>Planowanie wdrożenia

Skonfigurowanie produktu Workday do Active Directory aprowizacji użytkowników wymaga znaczącego planowania obejmującego różne aspekty, takie jak:
* Konfiguracja agenta aprowizacji Azure AD Connect 
* Liczba aplikacji do wdrożenia inicjowanych przez użytkowników w usłudze AD
* Wybieranie odpowiedniego identyfikatora dopasowania, Mapowanie atrybutów, przekształcenie i określanie zakresu filtrów

Zapoznaj się z [planem wdrożenia usługi Cloud HR](../app-provisioning/plan-cloud-hr-provision.md) , aby uzyskać wyczerpujące wytyczne i zalecane najlepsze rozwiązania. 

## <a name="configure-integration-system-user-in-workday"></a>Konfiguruj użytkownika systemu integracji w programie Workday

Typowym wymaganiem wszystkich łączników aprowizacji w programie Workday jest to, że wymagają poświadczeń użytkownika systemu integracji produktu Workday do łączenia się z interfejsem API zasobów ludzkich usługi Workday. W tej sekcji opisano sposób tworzenia użytkownika systemu integracji w programie Workday i zawiera on następujące sekcje:

* [Tworzenie użytkownika systemu integracyjnego](#creating-an-integration-system-user)
* [Tworzenie grupy zabezpieczeń integracji](#creating-an-integration-security-group)
* [Konfigurowanie uprawnień zasad zabezpieczeń domeny](#configuring-domain-security-policy-permissions)
* [Konfigurowanie uprawnień zasad zabezpieczeń procesów firmy](#configuring-business-process-security-policy-permissions)
* [Aktywowanie zmian zasad zabezpieczeń](#activating-security-policy-changes)

> [!NOTE]
> Można pominąć tę procedurę i zamiast tego użyć konta administratora globalnego Workday jako konta integracji systemu. Może to być bardziej dokładne dla pokazów, ale nie jest to zalecane w przypadku wdrożeń produkcyjnych.

### <a name="creating-an-integration-system-user"></a>Tworzenie użytkownika systemu integracyjnego

**Aby utworzyć użytkownika systemu integracji:**

1. Zaloguj się do dzierżawy usługi Workday przy użyciu konta administratora. W **aplikacji Workday** wprowadź w polu wyszukiwania pozycję Utwórz użytkownika, a następnie kliknij pozycję **Utwórz użytkownika systemu integracji**.

   >[!div class="mx-imgBorder"] 
   >![Utwórz użytkownika](./media/workday-inbound-tutorial/wd_isu_01.png "Tworzenie użytkownika")
2. Wykonaj zadanie **użytkownika Tworzenie systemu integracji** , podając nazwę użytkownika i hasło dla nowego użytkownika systemu integracji.  

   * Pozostaw zaznaczone pole wyboru **Wymagaj nowego hasła przy następnym logowaniu** , ponieważ ten użytkownik będzie logować się programowo.
   * Pozostaw **limit czasu sesji w minutach** o wartości domyślnej 0, co uniemożliwi przekroczenie limitu czasu sesji użytkownika.
   * Wybierz opcję **nie Zezwalaj na sesje interfejsu użytkownika** , ponieważ zapewnia ona dodaną warstwę zabezpieczeń, która uniemożliwia użytkownikowi z hasłem systemu integracji rejestrowanie się w usłudze Workday.

   > [!div class="mx-imgBorder"]
   > ![Utwórz użytkownika systemu integracyjnego](./media/workday-inbound-tutorial/wd_isu_02.png "Utwórz użytkownika systemu integracyjnego")

### <a name="creating-an-integration-security-group"></a>Tworzenie grupy zabezpieczeń integracji

W tym kroku utworzysz nieograniczeń lub ograniczone grupy zabezpieczeń systemu integracji w programie Workday i przypiszesz użytkownika systemu integracji utworzonego w poprzednim kroku do tej grupy.

**Aby utworzyć grupę zabezpieczeń:**

1. W polu wyszukiwania wprowadź Utwórz grupę zabezpieczeń, a następnie kliknij pozycję **Utwórz grupę zabezpieczeń**.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu pokazujący, że w polu wyszukiwania została wprowadzona wartość "Utwórz grupę zabezpieczeń" i "Utwórz grupę zabezpieczeń — zadanie" wyświetlaną w wynikach wyszukiwania.](./media/workday-inbound-tutorial/wd_isu_03.png)
2. Ukończ zadanie **tworzenia grupy zabezpieczeń** . 

   * W programie Workday istnieją dwa typy grup zabezpieczeń:
     * **Nieograniczone:** Wszyscy członkowie grupy zabezpieczeń mogą uzyskać dostęp do wszystkich wystąpień danych zabezpieczonych przez grupę zabezpieczeń.
     * **Ograniczone:** Wszyscy członkowie grupy zabezpieczeń mają dostęp kontekstowy do podzbioru wystąpień danych (wierszy), do których grupa zabezpieczeń może uzyskać dostęp.
   * Skontaktuj się z partnerem integracji usługi Workday, aby wybrać odpowiedni typ grupy zabezpieczeń dla integracji.
   * Po poznaniu typu grupy wybierz pozycję **integracja systemowa grupa zabezpieczeń (nieograniczona)** lub **system integracji Grupa zabezpieczeń (ograniczone)** z listy rozwijanej **Typ grupy zabezpieczeń dzierżawca** .

     > [!div class="mx-imgBorder"]
     >![Grupa zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_04.png "Grupa zabezpieczeń")

3. Po pomyślnym utworzeniu grupy zabezpieczeń zostanie wyświetlona strona, na której można przypisać członków do grupy zabezpieczeń. Dodaj nowego użytkownika systemu integracji utworzonego w poprzednim kroku do tej grupy zabezpieczeń. Jeśli używasz grupy zabezpieczeń z *ograniczeniami* , musisz również wybrać odpowiedni zakres organizacji.

   >[!div class="mx-imgBorder"]
   >![Edytuj grupę zabezpieczeń](./media/workday-inbound-tutorial/wd_isu_05.png "Edytuj grupę zabezpieczeń")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurowanie uprawnień zasad zabezpieczeń domeny

W tym kroku zostanie udzielone uprawnienie "zabezpieczenia domeny" dotyczące danych procesu roboczego do grupy zabezpieczeń.

**Aby skonfigurować uprawnienia zasad zabezpieczeń domeny:**

1. W polu wyszukiwania wprowadź **przynależność do grupy zabezpieczeń i dostęp** , a następnie kliknij łącze raportu.
   >[!div class="mx-imgBorder"]
   >![Wyszukaj członkostwo w grupie zabezpieczeń](./media/workday-inbound-tutorial/security-group-membership-access.png)

1. Wyszukaj i wybierz grupę zabezpieczeń utworzoną w poprzednim kroku. 
   >[!div class="mx-imgBorder"]
   >![Wybierz grupę zabezpieczeń](./media/workday-inbound-tutorial/select-security-group-workday.png)

1. Kliknij przycisk wielokropka (...) obok nazwy grupy i z menu, wybierz opcję **Grupa zabezpieczeń > zachować uprawnienia domeny dla grupy zabezpieczeń**
   >[!div class="mx-imgBorder"]
   >![Wybierz pozycję Zachowaj uprawnienia domeny](./media/workday-inbound-tutorial/select-maintain-domain-permissions.png)

1. W obszarze **uprawnienia integracji** Dodaj następujące domeny do listy **zasad zabezpieczeń domeny z zezwoleniem na dostęp** .
   * *Inicjowanie obsługi konta zewnętrznego*
   * *Dane procesu roboczego: raporty dotyczące publicznych procesów roboczych* 
   * *Dane osoby: informacje kontaktowe służbowych* (wymagane w przypadku planowania zapisywania zwrotnego danych kontaktowych z usługi Azure AD do produktu Workday)
   * *Konta programu Workday* (wymagane w przypadku planowania zapisywania zwrotnego nazwy użytkownika/nazwy UPN z usługi Azure AD do produktu Workday)

1. W obszarze **uprawnienia integracji** Dodaj następujące domeny do listy **zasad zabezpieczeń domeny z zezwoleniem na dostęp** .
   * *Dane procesu roboczego: procesy robocze*
   * *Dane procesu roboczego: wszystkie pozycje*
   * *Dane procesu roboczego: bieżące informacje o kadrze*
   * *Dane procesu roboczego: tytuł działalności w profilu procesu roboczego*
   * *Dane procesu roboczego: zakwalifikowani pracownicy* (opcjonalnie Dodaj ten proces, aby pobrać dane dotyczące kwalifikacji procesów roboczych do aprowizacji)
   * *Dane procesu roboczego: umiejętności i doświadczenia* (opcjonalnie Dodaj ten proces, aby pobrać dane umiejętności procesu roboczego w celu aprowizacji)

1. Po wykonaniu powyższych kroków zostanie wyświetlony ekran uprawnienia, jak pokazano poniżej:
   >[!div class="mx-imgBorder"]
   >![Wszystkie uprawnienia zabezpieczeń domeny](./media/workday-inbound-tutorial/all-domain-security-permissions.png)

1. Kliknij  przycisk OK **i wykonaj** na następnym ekranie, aby zakończyć konfigurację. 

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurowanie uprawnień zasad zabezpieczeń procesów firmy

W tym kroku zostanie udzielone uprawnienie "zabezpieczenia procesu biznesowego" dotyczące danych procesu roboczego do grupy zabezpieczeń. 

> [!NOTE]
> Ten krok jest wymagany tylko w przypadku konfigurowania łącznika aplikacji zapisywania zwrotnego dla produktu Workday.

**Aby skonfigurować uprawnienia do zasad zabezpieczeń procesów firmy:**

1. W polu wyszukiwania wprowadź **zasady procesu biznesowego** , a następnie kliknij przycisk Edytuj zadanie **zasad zabezpieczeń procesu biznesowego** .  

   >[!div class="mx-imgBorder"]
   >![Zrzut ekranu pokazujący "zasady procesu biznesowego" w polu wyszukiwania i wybrane ustawienie "Edytuj zasady zabezpieczeń procesów firmy" — zadanie.](./media/workday-inbound-tutorial/wd_isu_12.png "Zasady zabezpieczeń procesów firmy")  

2. W polu tekstowym **Typ procesu biznesowego** Wyszukaj *kontakty* i wybierz pozycję **kontakt służbowy Zmień** proces biznesowy i kliknij przycisk **OK**.

   >[!div class="mx-imgBorder"]
   >![Zrzut ekranu przedstawiający stronę "Edytowanie zasad zabezpieczeń procesów firmy" oraz "zmiana kontaktu służbowego" wybraną w menu "typ procesu biznesowego".](./media/workday-inbound-tutorial/wd_isu_13.png "Zasady zabezpieczeń procesów firmy")  

3. Na stronie **Edytowanie zasad zabezpieczeń procesu biznesowego** przewiń do sekcji **zmiana informacji o kontakcie służbowym (usługa sieci Web)** .


4. Wybierz i Dodaj nową grupę zabezpieczeń systemu integracyjnego do listy grup zabezpieczeń, które mogą inicjować żądanie usług sieci Web. 

   >[!div class="mx-imgBorder"]
   >![Zasady zabezpieczeń procesów firmy](./media/workday-inbound-tutorial/wd_isu_15.png "Zasady zabezpieczeń procesów firmy")  

5. Kliknij przycisk **gotowe**. 

### <a name="activating-security-policy-changes"></a>Aktywowanie zmian zasad zabezpieczeń

**Aby aktywować zmiany zasad zabezpieczeń:**

1. Wprowadź aktywację w polu wyszukiwania, a następnie kliknij link **Aktywuj oczekujące zmiany zasad zabezpieczeń**.
   >[!div class="mx-imgBorder"]
   >![Zdezaktywować](./media/workday-inbound-tutorial/wd_isu_16.png "Activate")

1. Rozpocznij zadanie Aktywuj oczekujące zmiany zasad zabezpieczeń, wprowadzając komentarz do celów inspekcji, a następnie kliknij przycisk **OK**.
1. Ukończ zadanie na następnym ekranie, zaznaczając pole wyboru **Potwierdź**, a następnie kliknij przycisk **OK**.

   >[!div class="mx-imgBorder"]
   >![Aktywuj oczekujące zabezpieczenia](./media/workday-inbound-tutorial/wd_isu_18.png "Aktywuj oczekujące zabezpieczenia")  

## <a name="provisioning-agent-installation-prerequisites"></a>Wymagania wstępne instalacji agenta aprowizacji

Przed przejściem do następnej sekcji zapoznaj się z [wymaganiami wstępnymi instalacji agenta aprowizacji](../cloud-sync/how-to-prerequisites.md) . 

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfigurowanie aprowizacji użytkowników z produktu Workday do Active Directory

Ta sekcja zawiera kroki dla aprowizacji konta użytkownika z produktu Workday do każdej domeny Active Directory w ramach zakresu integracji.

* [Dodawanie aplikacji łącznika aprowizacji i pobieranie agenta aprowizacji](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Instalowanie i konfigurowanie lokalnych agentów aprowizacji](#part-2-install-and-configure-on-premises-provisioning-agents)
* [Konfigurowanie łączności z usługą Workday i Active Directory](#part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory)
* [Konfigurowanie mapowań atrybutów](#part-4-configure-attribute-mappings)
* [Włączanie i uruchamianie aprowizacji użytkowników](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>Część 1. Dodawanie aplikacji łącznika aprowizacji i pobieranie agenta aprowizacji

**Aby skonfigurować dzień roboczy do Active Directory aprowizacji:**

1. Przejdź do witryny <https://portal.azure.com>.

2. W Azure Portal Wyszukaj i wybierz pozycję **Azure Active Directory**.

3. Wybierz **aplikacje przedsiębiorstwa**, a następnie **wszystkie aplikacje**.

4. Wybierz pozycję **Dodaj aplikację**, a następnie wybierz kategorię **wszystkie** .

5. Wyszukaj pozycję **Workday, aby Active Directory aprowizacji użytkowników**, i Dodaj tę aplikację z galerii.

6. Po dodaniu aplikacji i wyświetleniu ekranu Szczegóły aplikacji wybierz opcję **Inicjowanie obsługi**.

7. Zmień tryb **aprowizacji**  na **automatyczny**.

8. Kliknij transparent informacji wyświetlany, aby pobrać agenta aprowizacji. 

   >[!div class="mx-imgBorder"]
   >![Pobierz agenta](./media/workday-inbound-tutorial/pa-download-agent.png "Pobierz ekran agenta")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>Część 2. Instalowanie i konfigurowanie lokalnych agentów aprowizacji

Aby zapewnić Active Directory lokalnego, Agent aprowizacji musi być zainstalowany na serwerze przyłączonym do domeny, który ma dostęp sieciowy do żądanych Active Directory domen.

Przenieś pobrany Instalator agenta na hosta serwera i wykonaj kroki opisane [w sekcji **Instalowanie agenta**](../cloud-sync/how-to-install.md) , aby ukończyć konfigurację agenta.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-workday-and-active-directory"></a>Część 3: w aplikacji aprowizacji Skonfiguruj łączność z produktem Workday i Active Directory
W tym kroku ustanawiamy łączność z produktem Workday i Active Directory w Azure Portal. 

1. W Azure Portal przejdź z powrotem do produktu Workday, aby Active Directory aplikację aprowizacji użytkowników utworzoną w [części 1](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)

1. Wypełnij sekcję **poświadczenia administratora** w następujący sposób:

   * **Nazwa użytkownika produktu Workday** — wprowadź nazwę użytkownika konta programu integracji z systemem Workday z dołączoną nazwą domeny dzierżawy. Powinien wyglądać następująco: **Nazwa użytkownika \@ tenant_name**

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
     > <br> Jeśli używasz WWS API v 30.0 +, przed włączeniem zadania aprowizacji, zaktualizuj **wyrażenia interfejsu API XPath** w obszarze **mapowanie atrybutu — > opcje zaawansowane — > Edytuj listę atrybutów dla produktu Workday** , odwołując się do sekcji Zarządzanie [odwołaniem do atrybutów](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [konfiguracji](#managing-your-configuration) i wersji roboczej.  

   * **Active Directory Las** "Nazwa" domeny Active Directory, zarejestrowana w agencie. Użyj listy rozwijanej, aby wybrać domenę docelową do aprowizacji. Ta wartość jest zazwyczaj ciągiem: *contoso.com*

   * **Kontener Active Directory —** Wprowadź nazwę wyróżniającą kontenera, w której Agent powinien domyślnie utworzyć konta użytkowników.
        Przykład: *OU = użytkownicy standardowi, OU = users, DC = contoso, DC = test*

     > [!NOTE]
     > To ustawienie jest dostępne tylko w przypadku tworzenia kont użytkowników, jeśli atrybut *parentDistinguishedName* nie jest skonfigurowany w mapowaniu atrybutów. To ustawienie nie jest używane na potrzeby operacji wyszukiwania ani aktualizowania użytkownika. Całe poddrzewo domeny znajduje się w zakresie operacji wyszukiwania.

   * **Wiadomość e-mail z powiadomieniem —** Wprowadź adres e-mail, a następnie zaznacz pole wyboru "Wyślij wiadomość e-mail, jeśli wystąpi błąd".

     > [!NOTE]
     > Usługa Azure AD Provisioning wysyła powiadomienie e-mail, jeśli zadanie aprowizacji przejdzie do stanu [kwarantanny](../app-provisioning/application-provisioning-quarantine-status.md) .

   * Kliknij przycisk **Testuj połączenie** . Jeśli test połączenia zakończy się pomyślnie, kliknij przycisk **Zapisz** u góry. Jeśli to się nie powiedzie, sprawdź, czy poświadczenia dla produktu Workday i poświadczenia usługi AD skonfigurowane w instalatorze agenta są prawidłowe.

     >[!div class="mx-imgBorder"]
     >![Zrzut ekranu przedstawiający stronę "Inicjowanie obsługi" z wprowadzonymi poświadczeniami.](./media/workday-inbound-tutorial/wd_1.png)

   * Po pomyślnym zapisaniu poświadczeń w sekcji **mapowania** zostanie wyświetlone mapowanie domyślne **Synchronizacja procesów roboczych programu Workday do lokalnego Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>Część 4: Konfigurowanie mapowań atrybutów

W tej sekcji skonfigurujesz sposób przepływu danych przez użytkownika z produktu Workday do Active Directory.

1. Na karcie Inicjowanie obsługi w obszarze **mapowania** kliknij pozycję **Synchronizuj procesy robocze programu Workday z lokalnymi Active Directory**.

1. W polu **Zakres obiektu źródłowego** możesz wybrać, które zestawy użytkowników w dniach roboczych powinny znajdować się w zakresie dla aprowizacji do usługi AD, definiując zestaw filtrów opartych na atrybutach. Zakresem domyślnym jest "Wszyscy użytkownicy w usłudze Workday". Przykładowe filtry:

   * Przykład: zakres dla użytkowników z identyfikatorami procesów roboczych z zakresu od 1000000 do 2000000 (z wyłączeniem 2000000)

      * Atrybut: WorkerID

      * Operator: dopasowanie wyrażenia regularnego

      * Wartość: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Przykład: tylko pracownicy i niezależni pracownicy

      * Atrybut: IDPracownika

      * Operator: nie ma wartości NULL

   > [!TIP]
   > Podczas konfigurowania aplikacji do aprowizacji po raz pierwszy należy przetestować i zweryfikować mapowania atrybutów oraz wyrażenia, aby upewnić się, że daje żądany wynik. Firma Microsoft zaleca używanie [filtrów zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) w ramach **zakresu obiektu źródłowego** i [aprowizacji na żądanie](../app-provisioning/provision-on-demand.md) w celu testowania mapowań za pomocą kilku użytkowników testowych z produktu Workday. Po sprawdzeniu, czy mapowania działają, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

   > [!CAUTION] 
   > Domyślne zachowanie aparatu aprowizacji polega na wyłączeniu/usunięciu użytkowników, którzy wykraczają poza zakres. Może to nie być pożądane w ramach integracji z usługą AD. Aby zastąpić to zachowanie domyślne, odnoszące się do artykułu [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](../app-provisioning/skip-out-of-scope-deletions.md)

1. W polu **Akcje obiektu docelowego** można globalnie filtrować akcje wykonywane na Active Directory. **Tworzenie** i **Aktualizowanie** są najczęściej używane.

1. W sekcji **mapowania atrybutów** można określić, w jaki sposób mapowanie poszczególnych atrybutów produktu Workday ma Active Directory atrybuty.

1. Kliknij istniejące mapowanie atrybutów, aby je zaktualizować, lub kliknij pozycję **Dodaj nowe mapowanie** u dołu ekranu, aby dodać nowe mapowania. Mapowanie poszczególnych atrybutów obsługuje te właściwości:

      * **Typ mapowania**

         * **Direct** — zapisuje wartość atrybutu Workday w atrybucie usługi AD bez zmian

         * **Stała** — Zapisz statyczną, stałą wartość ciągu w atrybucie usługi AD.

         * **Expression** — umożliwia zapisanie niestandardowej wartości atrybutu AD na podstawie jednego lub większej liczby atrybutów produktu Workday. [Aby uzyskać więcej informacji, zobacz ten artykuł w wyrażeniach](../app-provisioning/functions-for-customizing-application-data.md).

      * **Atrybut źródłowy** — atrybut użytkownika z produktu Workday. Jeśli atrybutu, którego szukasz, nie ma, zobacz [Dostosowywanie listy atrybutów użytkownika produktu Workday](#customizing-the-list-of-workday-user-attributes).

      * **Wartość domyślna** — opcjonalne. Jeśli atrybut źródłowy ma pustą wartość, mapowanie spowoduje zapisanie tej wartości.
            Najbardziej typową konfiguracją jest pozostawienie tej pustej.

      * **Atrybut target** — atrybut user w Active Directory.

      * **Dopasowywanie obiektów przy użyciu tego atrybutu** — niezależnie od tego, czy mapowanie ma być używane do unikatowego identyfikowania użytkowników między dniem roboczym i Active Directory. Ta wartość jest zazwyczaj ustawiana w polu Identyfikator procesu roboczego dla produktu Workday, który zwykle jest mapowany na jeden z atrybutów identyfikatora pracownika w Active Directory.

      * **Priorytet dopasowania** — można ustawić wiele pasujących atrybutów. Jeśli istnieje wiele, są one oceniane w kolejności zdefiniowanej przez to pole. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.

      * **Zastosuj to mapowanie**

         * **Zawsze** — Zastosuj to mapowanie zarówno dla akcji tworzenia i aktualizowania użytkownika

         * **Tylko podczas tworzenia** — Zastosuj to mapowanie tylko dla akcji tworzenia użytkownika

1. Aby zapisać mapowania, kliknij pozycję **Zapisz** w górnej części sekcji Attribute-Mapping.
   >[!div class="mx-imgBorder"]
   >![Zrzut ekranu przedstawiający stronę "Mapowanie atrybutów" z wybraną akcją "Zapisz".](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Poniżej przedstawiono przykładowe mapowania atrybutów między dniem Workday i Active Directory przy użyciu niektórych typowych wyrażeń

* Wyrażenie, które jest mapowane na atrybut *parentDistinguishedName* , jest używane do udostępniania użytkownikowi różnych jednostek organizacyjnych na podstawie jednego lub większej liczby atrybutów źródłowych produktu Workday. Ten przykład tu umieszcza użytkowników w różnych jednostkach organizacyjnych na podstawie miejscowości, w której znajdują się.

* Atrybut *userPrincipalName* w Active Directory jest generowany przy użyciu funkcji usuwania duplikatów [SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) , która sprawdza obecność wygenerowanej wartości w docelowej domenie usługi AD i ustawia ją tylko wtedy, gdy jest unikatowa.  

* [W tym miejscu znajduje się dokumentacja dotycząca pisania wyrażeń](../app-provisioning/functions-for-customizing-application-data.md). Ta sekcja zawiera przykłady dotyczące usuwania znaków specjalnych.

| ATRYBUT WORKDAY | ATRYBUT USŁUGI ACTIVE DIRECTORY |  PASUJE IDENTYFIKATOR? | UTWÓRZ/ZAKTUALIZUJ |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID (Identyfikator pracownika) | **Tak** | Zapisywane tylko w przypadku tworzenia |
| **PreferredNameData**    |  CN    |   |   Zapisywane tylko w przypadku tworzenia |
| **SelectUniqueValue (join (" \@ ", join (".",  \[ FirstName \] , \[ LastName \] ), "contoso.com"), join (" \@ ", join (".", Mid ( \[ FirstName \] , 1, 1), \[ LastName \] ), "contoso.com"), join (" \@ ", join (".", Mid ( \[ FirstName \] , 1, 2), \[ LastName \] ), "contoso.com")**   | userPrincipalName     |     | Zapisywane tylko w przypadku tworzenia 
| `Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )`      |    sAMAccountName            |     |         Zapisywane tylko w przypadku tworzenia |
| **Switch ( \[ aktywny \] ,, "0", "true", "1", "false")** |  accountDisabled      |     | Utwórz i zaktualizuj |
| **FirstName (Imię)**   | givenName       |     |    Utwórz i zaktualizuj |
| **LastName (Nazwisko)**   |   sn   |     |  Utwórz i zaktualizuj |
| **PreferredNameData**  |  displayName |     |   Utwórz i zaktualizuj |
| **Przedsiębiorstwo**         | company   |     |  Utwórz i zaktualizuj |
| **SupervisoryOrganization**  | działu,  |     |  Utwórz i zaktualizuj |
| **ManagerReference**   | manager  |     |  Utwórz i zaktualizuj |
| **BusinessTitle**   |  tytuł     |     |  Utwórz i zaktualizuj | 
| **AddressLineData**    |  streetAddress  |     |   Utwórz i zaktualizuj |
| **Gmina**   |   l   |     | Utwórz i zaktualizuj |
| **CountryReferenceTwoLetter**      |   co |     |   Utwórz i zaktualizuj |
| **CountryReferenceTwoLetter**    |  c  |     |         Utwórz i zaktualizuj |
| **CountryRegionReference** |  st     |     | Utwórz i zaktualizuj |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Utwórz i zaktualizuj |
| **Pocztowy**  |   postalCode  |     | Utwórz i zaktualizuj |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Utwórz i zaktualizuj |
| **Faks**      | facsimileTelephoneNumber     |     |    Utwórz i zaktualizuj |
| **Mobilny**  |    telefon komórkowy       |     |       Utwórz i zaktualizuj |
| **LocalReference** |  preferredLanguage  |     |  Utwórz i zaktualizuj |                                               
| **Switch ( \[ gmina \] : "OU = default users, DC = contoso, DC = com", "Dallas", "OU = Dallas, OU = users, DC = contoso, DC = com", "Austin", "OU = Austin, OU = users, DC = contoso, DC = com", "Seattle", "OU = Seattle, OU = users, DC = contoso, DC = com", "Londyn", "OU = Londyn, OU = users, DC = contoso, DC = com")**  | parentDistinguishedName     |     |  Utwórz i zaktualizuj |

Po zakończeniu konfiguracji mapowania atrybutów można testować Inicjowanie obsługi dla pojedynczego użytkownika przy użyciu [inicjowania obsługi na żądanie](../app-provisioning/provision-on-demand.md) , a następnie [włączyć i uruchomić usługę aprowizacji użytkowników](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Włączanie i uruchamianie aprowizacji użytkowników

Po zakończeniu konfiguracji aplikacji do aprowizacji w dniu Workday i sprawdzeniu aprowizacji dla pojedynczego użytkownika z obsługą [administracyjną na żądanie](../app-provisioning/provision-on-demand.md)można włączyć usługę aprowizacji w Azure Portal.

> [!TIP]
> Domyślnie po włączeniu usługi aprowizacji zostaną zainicjowane operacje aprowizacji dla wszystkich użytkowników w zakresie. Jeśli występują błędy w mapowaniu lub problemach z danymi w dniach roboczych, zadanie aprowizacji może się nie powieść i przejść do stanu kwarantanny. Aby tego uniknąć, najlepszym rozwiązaniem jest zalecamy skonfigurowanie filtru **zakresu obiektów źródłowych** i przetestowanie mapowań atrybutów za pomocą kilku użytkowników testowych korzystających z [aprowizacji na żądanie](../app-provisioning/provision-on-demand.md) przed uruchomieniem pełnej synchronizacji dla wszystkich użytkowników. Po sprawdzeniu, czy mapowania działają i dają odpowiednie wyniki, można usunąć filtr lub stopniowo rozwijać go, aby uwzględnić więcej użytkowników.

1. Przejdź do bloku **aprowizacji** i kliknij pozycję **Rozpocznij Inicjowanie obsługi**.

1. Ta operacja rozpocznie synchronizację początkową, która może potrwać zmienną liczbę godzin w zależności od liczby użytkowników w dzierżawie produktu Workday. Możesz sprawdzić, czy pasek postępu śledzi postęp cyklu synchronizacji. 

1. W dowolnym momencie sprawdź kartę **dzienniki inspekcji** w Azure Portal, aby zobaczyć, jakie akcje zostały wykonane przez usługę aprowizacji. W dziennikach inspekcji są wyświetlane wszystkie zdarzenia synchronizacji wykonywane przez usługę aprowizacji, takie jak informacje o użytkownikach, które są odczytywane z dnia roboczego, a następnie dodane lub zaktualizowane do Active Directory. Zapoznaj się z sekcją rozwiązywania problemów, aby uzyskać instrukcje dotyczące sposobu przeglądania dzienników inspekcji i naprawiania błędów aprowizacji.

1. Po zakończeniu synchronizacji początkowej zostanie zapisany raport z podsumowaniem inspekcji na karcie **aprowizacji** , jak pokazano poniżej.
   > [!div class="mx-imgBorder"]
   > ![Pasek postępu aprowizacji](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

* **Pytania dotyczące możliwości rozwiązania**
  * [W jaki sposób rozwiązanie ustawia hasło dla nowego konta użytkownika w Active Directory w przypadku przetwarzania nowego zatrudnienia z produktu Workday?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Czy rozwiązanie obsługuje wysyłanie powiadomień pocztą e-mail po ukończeniu operacji aprowizacji?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Czy w przypadku pamięci podręcznej rozwiązania profile użytkowników platformy Workday w chmurze usługi Azure AD lub w warstwie agenta aprowizacji?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Czy rozwiązanie obsługuje przypisywanie lokalnych grup usługi AD do użytkownika?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Które interfejsy API programu Workday wykorzystują rozwiązanie do wysyłania zapytań i aktualizowania profilów procesów roboczych dla programu Workday?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Czy mogę skonfigurować dzierżawę usługi Workday HCM z dwoma dzierżawami usługi Azure AD?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Jak mogę Sugeruj ulepszenia lub zażądać nowych funkcji związanych z usługą Workday i integracją usługi Azure AD?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Pytania dotyczące agenta aprowizacji**
  * [Co to jest wersja GA agenta aprowizacji?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Jak mogę znać wersję mojego agenta aprowizacji?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Czy firma Microsoft automatycznie wypychanie aktualizacji agenta aprowizacji?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Czy można zainstalować agenta aprowizacji na tym samym serwerze, na którym działa Azure AD Connect?](#can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect)
  * [Jak mogę skonfigurować agenta aprowizacji do używania serwera proxy do wychodzącej komunikacji HTTP?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Jak mogę upewnić się, że Agent aprowizacji jest w stanie komunikować się z dzierżawą usługi Azure AD, a zapory nie blokują portów wymaganych przez agenta?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Jak mogę Wyrejestrowanie domeny skojarzonej z agentem aprowizacji?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Jak mogę odinstalować agenta aprowizacji?](#how-do-i-uninstall-the-provisioning-agent)

* **Mapowanie i pytania dotyczące konfiguracji programu Workday do usługi AD**
  * [Jak mogę utworzyć kopię zapasową lub wyeksportować działającą kopię mapowania i schematu atrybutu aprowizacji produktu Workday?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Mam atrybuty niestandardowe w dniach roboczych i Active Directory. Jak mogę skonfigurować rozwiązanie do pracy z moimi atrybutami niestandardowymi?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Czy można udostępnić zdjęcie użytkownika z produktu Workday do Active Directory?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Jak mogę synchronizować numery telefonów komórkowych z produktu Workday w oparciu o zgodę użytkownika na użycie publiczne?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Jak mogę format nazw wyświetlanych w usłudze AD na podstawie atrybutów działu/kraju/miejscowości użytkownika i obsłużyć wariancje regionalne?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Jak używać SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Jak mogę usunąć znaki diakrytyczne i przekonwertować je na normalne alfabety angielskie?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Pytania dotyczące możliwości rozwiązania

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>W jaki sposób rozwiązanie ustawia hasło dla nowego konta użytkownika w Active Directory w przypadku przetwarzania nowego zatrudnienia z produktu Workday?

Gdy lokalny Agent aprowizacji otrzyma żądanie utworzenia nowego konta usługi AD, automatycznie generuje złożone hasło losowe, zaprojektowane do spełnienia wymagań dotyczących złożoności haseł zdefiniowanych przez serwer AD i ustawia je dla obiektu użytkownika. To hasło nie jest rejestrowane w dowolnym miejscu.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Czy rozwiązanie obsługuje wysyłanie powiadomień pocztą e-mail po ukończeniu operacji aprowizacji?

Nie, wysyłanie powiadomień pocztą e-mail po ukończeniu operacji aprowizacji nie jest obsługiwane w bieżącej wersji.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Czy w przypadku pamięci podręcznej rozwiązania profile użytkowników platformy Workday w chmurze usługi Azure AD lub w warstwie agenta aprowizacji?

Nie, rozwiązanie nie utrzymuje pamięci podręcznej profilów użytkowników. Usługa Azure AD Provisioning działa po prostu jako procesor danych, odczytywanie danych z produktu Workday i zapisywanie do Active Directory docelowej lub usługi Azure AD. Zapoznaj się z sekcją [Zarządzanie danymi osobowymi](#managing-personal-data) w celu uzyskania szczegółowych informacji dotyczących prywatności użytkowników i przechowywania danych.

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Czy rozwiązanie obsługuje przypisywanie lokalnych grup usługi AD do użytkownika?

Ta funkcja nie jest obecnie obsługiwana. Zalecane obejście polega na wdrożeniu skryptu programu PowerShell, który wysyła zapytanie do punktu końcowego interfejsu API Microsoft Graph na potrzeby [danych dziennika inspekcji](/graph/api/resources/azure-ad-auditlog-overview) i służy do wyzwalania scenariuszy, takich jak przypisywanie grup. Ten skrypt programu PowerShell może zostać dołączony do harmonogramu zadań i wdrożony w tym samym oknie, na którym jest uruchomiony agent aprowizacji.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Które interfejsy API programu Workday wykorzystują rozwiązanie do wysyłania zapytań i aktualizowania profilów procesów roboczych dla programu Workday?

Obecnie rozwiązanie używa następujących interfejsów API produktu Workday:

* Format **adresu URL interfejsu API usług sieci Web** w programie Workday użyty w sekcji **poświadczenia administratora** określa wersję interfejsu api używaną dla Get_Workers
  * W przypadku formatu adresu URL: https:// \# \# \# \# \. Workday \. com/CCX/Service/dzierżawcname, a następnie używany jest interfejs API v 21.1. 
  * W przypadku formatu adresu URL: https:// \# \# \# \# \. Workday \. com/CCX/Service/dzierżawca/kadry \_ , a następnie jest używany interfejs API v 21.1 
  * W przypadku formatu adresu URL: https:// \# \# \# \# \. Workday \. com/CCX/Service/dzierżawca/Human \_ resources/v \# \# \. \# , zostanie użyta określona wersja interfejsu API. (Przykład: Jeśli określono 34.0 v, jest on używany).  

* Funkcja zapisywania zwrotnego poczty E-mail dla produktu Workday używa Change_Work_Contact_Information (v 30.0) 
* Funkcja zapisywania zwrotnego użytkownika produktu Workday używa Update_Workday_Account (v 31.2) 

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Czy mogę skonfigurować dzierżawę usługi Workday HCM z dwoma dzierżawami usługi Azure AD?

Tak, ta konfiguracja jest obsługiwana. Poniżej przedstawiono procedurę wysokiego poziomu służącą do konfigurowania tego scenariusza:

* Wdróż agenta aprowizacji #1 i zarejestruj go przy użyciu dzierżawy usługi Azure AD #1.
* Wdróż agenta aprowizacji #2 i zarejestruj go przy użyciu dzierżawy usługi Azure AD #2.
* Na podstawie "domen podrzędnych", które będą zarządzane przez każdego agenta aprowizacji, należy skonfigurować każdego agenta za pomocą domen. Jeden Agent może obsługiwać wiele domen.
* W Azure Portal Skonfiguruj aplikację Workday do usługi AD User Provisioning w każdej dzierżawie i ustaw ją z odpowiednimi domenami.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Jak mogę Sugeruj ulepszenia lub zażądać nowych funkcji związanych z usługą Workday i integracją usługi Azure AD?

Twoje opinie są wysoce wyceniane, ponieważ ułatwiają nam Ustawianie kierunku dla przyszłych wersji i ulepszeń. Wszystkie opinie i zachęcamy do przesyłania sugestii dotyczących pomysłów i udoskonaleń na [forum opinii w usłudze Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory). Aby uzyskać informacje na temat informacji zwrotnych dotyczących integracji z programem Workday, wybierz kategorię *SaaS aplikacje* i Wyszukaj przy użyciu słów kluczowych *Workday* , aby znaleźć istniejące informacje zwrotne dotyczące dnia roboczego.

> [!div class="mx-imgBorder"]
> ![Aplikacje UserVoice SaaS](media/workday-inbound-tutorial/uservoice_saas_apps.png)

> [!div class="mx-imgBorder"]
> ![Dzień roboczy UserVoice](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Podczas sugerowania nowego pomysłu Sprawdź, czy ktoś inny już sugerował podobną funkcję. W takim przypadku można zagłosować na żądanie funkcji lub rozszerzenia. Możesz również pozostawić komentarz dotyczący konkretnego przypadku użycia, aby pokazać swoją pomoc techniczną dotyczącą pomysłu i zademonstrować, jak ta funkcja będzie zbyt cenna.

### <a name="provisioning-agent-questions"></a>Pytania dotyczące agenta aprowizacji

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Co to jest wersja GA agenta aprowizacji?

Zapoznaj się z artykułem [Azure AD Connect Agent aprowizacji: wersja historia](../app-provisioning/provisioning-agent-release-version-history.md) wersji dla najnowszej wersji programu releaseing Agent aprowizacji.  

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Jak mogę znać wersję mojego agenta aprowizacji?

* Zaloguj się do systemu Windows Server, na którym zainstalowano agenta aprowizacji.
* Przejdź do **Panelu sterowania**  ->  **Odinstaluj lub Zmień menu programu**
* Wyszukaj wersję odpowiadającą wpisowi **Microsoft Azure AD Połącz agenta aprowizacji**

  >[!div class="mx-imgBorder"]
  >![Azure Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Czy firma Microsoft automatycznie wypychanie aktualizacji agenta aprowizacji?

Tak, firma Microsoft automatycznie aktualizuje agenta aprowizacji, jeśli usługa Windows **Microsoft Azure AD Connect Agent Aktualizator** działa.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect"></a>Czy można zainstalować agenta aprowizacji na tym samym serwerze, na którym działa Azure AD Connect?

Tak, można zainstalować agenta aprowizacji na tym samym serwerze, na którym działa Azure AD Connect.

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>W czasie konfigurowania Agent aprowizacji wyświetli prośbę o poświadczenia administratora usługi Azure AD. Czy Agent przechowuje poświadczenia lokalnie na serwerze?

Podczas konfigurowania Agent aprowizacji jest monitowany o poświadczenia administratora usługi Azure AD tylko w celu nawiązania połączenia z dzierżawą usługi Azure AD. Poświadczenia nie są przechowywane lokalnie na serwerze. Jednak zachowuje poświadczenia używane do nawiązywania połączenia z lokalną *domeną Active Directory* w lokalnym magazynie haseł systemu Windows.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Jak mogę skonfigurować agenta aprowizacji do używania serwera proxy do wychodzącej komunikacji HTTP?

Agent aprowizacji obsługuje korzystanie z serwera proxy wychodzącego. Można ją skonfigurować, edytując plik konfiguracji agenta **C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config**. Dodaj do niego następujące wiersze w kierunku końca pliku tuż przed tagiem zamykającym `</configuration>` .
Zastąp zmienne [proxy-server] i [proxy-port] odpowiednio nazwą i numerem portu serwera proxy.

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

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Jak mogę upewnić się, że Agent aprowizacji jest w stanie komunikować się z dzierżawą usługi Azure AD, a zapory nie blokują portów wymaganych przez agenta?

Możesz również sprawdzić, czy wszystkie [wymagane porty](../manage-apps/application-proxy-add-on-premises-application.md#open-ports) są otwarte.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Czy można skonfigurować jednego agenta aprowizacji do udostępniania wielu domen usługi AD?

Tak, jeden Agent aprowizacji można skonfigurować do obsługi wielu domen usługi AD, o ile agent ma wgląd w odpowiednie kontrolery domeny. Firma Microsoft zaleca skonfigurowanie grupy 3 aprowizacji agentów obsługujący ten sam zestaw domen usługi AD w celu zapewnienia wysokiej dostępności i zapewnienia obsługi trybu failover.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Jak mogę Wyrejestrowanie domeny skojarzonej z agentem aprowizacji?

* W Azure Portal Uzyskaj *Identyfikator dzierżawy* dzierżawy usługi Azure AD.
* Zaloguj się do systemu Windows Server, na którym jest uruchomiony agent aprowizacji.
* Otwórz program PowerShell jako administrator systemu Windows.
* Przejdź do katalogu zawierającego skrypty rejestracji i uruchom następujące polecenia, zastępując \[ parametr identyfikatora dzierżawy \] wartością identyfikatora dzierżawy.

  ```powershell
  cd "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder"
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Z wyświetlonej listy agentów — skopiuj wartość `id` pola z tego zasobu, którego *resourceName* równa się nazwie domeny usługi AD.
* Wklej wartość identyfikatora do tego polecenia i wykonaj polecenie w programie PowerShell.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Uruchom ponownie Kreatora konfiguracji agenta.
* Wszyscy inni agenci, którzy zostali wcześniej przypisani do tej domeny, będą musieli ponownie skonfigurować konfigurację.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Jak mogę odinstalować agenta aprowizacji?

* Zaloguj się do systemu Windows Server, na którym zainstalowano agenta aprowizacji.
* Przejdź do **Panelu sterowania**  ->  **Odinstaluj lub Zmień menu programu**
* Odinstaluj następujące programy:
  * Microsoft Azure AD połączyć się z agentem aprowizacji
  * Microsoft Azure AD Aktualizator połączenia z agentem
  * Microsoft Azure AD Połącz pakiet agenta aprowizacji

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Mapowanie i pytania dotyczące konfiguracji programu Workday do usługi AD

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Jak mogę utworzyć kopię zapasową lub wyeksportować działającą kopię mapowania i schematu atrybutu aprowizacji produktu Workday?

Za pomocą interfejsu API Microsoft Graph można eksportować konfigurację aprowizacji użytkowników w ramach programu Workday. Aby uzyskać szczegółowe informacje, zapoznaj się z instrukcjami w sekcji [Eksportowanie i Importowanie konfiguracji mapowania atrybutu aprowizacji użytkowników](#exporting-and-importing-your-configuration) w ramach platformy Workday.

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Mam atrybuty niestandardowe w dniach roboczych i Active Directory. Jak mogę skonfigurować rozwiązanie do pracy z moimi atrybutami niestandardowymi?

Rozwiązanie obsługuje niestandardowe atrybuty dni roboczych i Active Directory. Aby dodać niestandardowe atrybuty do schematu mapowania, Otwórz blok **Mapowanie atrybutów** i przewiń w dół, aby rozwinąć sekcję **Pokaż opcje zaawansowane**. 

![Edytuj listę atrybutów](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Aby dodać niestandardowe atrybuty produktu Workday, wybierz opcję *Edytuj listę atrybutów dla produktu Workday* i Dodaj niestandardowe atrybuty usługi AD, wybierz opcję *Edytuj listę atrybutów dla lokalnego Active Directory*.

Zobacz też:

* [Dostosowywanie listy atrybutów użytkownika produktu Workday](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Jak mogę skonfigurować rozwiązanie tak, aby aktualizować tylko atrybuty w usłudze AD na podstawie zmian wprowadzonych w dniu roboczym i nie tworzyć nowych kont usługi AD?

Tę konfigurację można osiągnąć przez ustawienie **akcji obiektu docelowego** w bloku **mapowania atrybutów** , jak pokazano poniżej:

![Aktualizuj akcję](./media/workday-inbound-tutorial/wd_target_update_only.png)

Zaznacz pole wyboru "Aktualizuj", aby tylko operacje aktualizowania były przesyłane z produktu Workday do usługi AD. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Czy można udostępnić zdjęcie użytkownika z produktu Workday do Active Directory?

Obecnie rozwiązanie nie obsługuje ustawiania atrybutów binarnych, takich jak *thumbnailPhoto* i *jpegPhoto* w Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Jak mogę synchronizować numery telefonów komórkowych z produktu Workday w oparciu o zgodę użytkownika na użycie publiczne?

* Przejdź do bloku "Inicjowanie obsługi" aplikacji do aprowizacji w programie Workday.
* Kliknij mapowania atrybutów 
* W obszarze **mapowania** wybierz pozycję **Synchronizuj procesy robocze programu Workday z lokalnymi Active Directory** (lub **zsynchronizuj procesy robocze z systemem Workday z usługą Azure AD**).
* Na stronie mapowania atrybutów przewiń w dół i zaznacz pole wyboru "Pokaż opcje zaawansowane".  Kliknij, **Aby edytować listę atrybutów dla produktu Workday**
* W bloku, który zostanie otwarty, Znajdź atrybut "Mobile" i kliknij wiersz, aby można było edytować **wyrażenie interfejsu API** ![ Mobile Rodo](./media/workday-inbound-tutorial/mobile_gdpr.png)

* Zastąp **wyrażenie interfejsu API** następującym nowym wyrażeniem, które pobiera służbowy numer telefonu, tylko jeśli "Flaga użycia publicznego" jest ustawiona na wartość "true" w programie Workday.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Zapisz listę atrybutów.
* Zapisz mapowanie atrybutu.
* Wyczyść bieżący stan i ponownie uruchom pełną synchronizację.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Jak mogę format nazw wyświetlanych w usłudze AD na podstawie atrybutów działu/kraju/miejscowości użytkownika i obsłużyć wariancje regionalne?

Typowym wymaganiem jest skonfigurowanie atrybutu *DisplayName* w usłudze AD, tak aby podano również informacje na temat działu i kraju/regionu użytkownika. Na przykład jeśli Jan Kowalski działa w dziale marketingu w Stanach Zjednoczonych, można chcieć, aby jej *Nazwa wyświetlana* była wyświetlana jako *Kowalski, Jan (Marketing-US)*.

Oto jak można obsługiwać takie wymagania dotyczące konstruowania nazwy *pospolitej* lub nazwy *wyświetlanej* w celu uwzględnienia atrybutów, takich jak firma, jednostka biznesowa, miasto lub kraj/region.

* Każdy atrybut Workday jest pobierany przy użyciu źródłowego wyrażenia interfejsu API XPATH, które można skonfigurować w  **mapowaniu atrybutów-> sekcji Zaawansowane — > Edytuj listę atrybutów dla dnia roboczego**. Oto domyślne wyrażenie interfejsu API XPATH dla atrybutów Workday *PreferredFirstName*, *PreferredLastName*, *Company* i *SupervisoryOrganization* .

     | Atrybut Workday | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | PreferredFirstName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: First_Name/Text () |
     | PreferredLastName | WD: Worker/WD: Worker_Data/WD: Personal_Data/WD: Name_Data/WD: Preferred_Name_Data/WD: Name_Detail_Data/WD: Last_Name/Text () |
     | Firma | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data [WD: Organization_Data/WD: Organization_Type_Reference/WD: ID [ @wd:type = "Organization_Type_ID"] = "Company"]/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | WD: Worker/WD: Worker_Data/WD: Organization_Data/WD: Worker_Organization_Data/WD: Organization_Data [WD: Organization_Type_Reference/WD: ID [ @wd:type = "Organization_Type_ID"] = "nadzorcze"]/WD: Organization_Name/Text () |

   Skontaktuj się z zespołem w programie Workday, że wyrażenie interfejsu API powyżej jest prawidłowe dla konfiguracji dzierżawy w Twojej firmie. W razie potrzeby można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika produktu Workday](#customizing-the-list-of-workday-user-attributes).

* Podobnie informacje o kraju/regionie obecne w usłudze Workday są pobierane przy użyciu następującej składni XPATH: *WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference*

     Istnieją 5 atrybutów dotyczących kraju/regionu, które są dostępne w sekcji Lista atrybutów dnia roboczego.

     | Atrybut Workday | Wyrażenie XPATH interfejsu API |
     | ----------------- | -------------------- |
     | CountryReference | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = "ISO_3166-1_Alpha-3_Code"]/text () |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = "ISO_3166-1_Numeric-3_Code"]/text () |
     | CountryReferenceTwoLetter | WD: Worker/WD: Worker_Data/WD: Employment_Data/WD: Position_Data/WD: Business_Site_Summary_Data/WD: Address_Data/WD: Country_Reference/WD: ID [ @wd:type = "ISO_3166-1_Alpha-2_Code"]/text () |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Skontaktuj się z zespołem usługi Workday, że powyższe wyrażenia interfejsu API są prawidłowe dla konfiguracji dzierżawy w Twojej firmie. W razie potrzeby można je edytować zgodnie z opisem w sekcji [Dostosowywanie listy atrybutów użytkownika produktu Workday](#customizing-the-list-of-workday-user-attributes).

* Aby skompilować odpowiednie wyrażenie mapowania atrybutu, ustal, który atrybut Workday "" autorytatywnie "reprezentuje Imię użytkownika, nazwisko, kraj/region i dział. Załóżmy, że atrybuty są odpowiednio *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* i *SupervisoryOrganization* . Za pomocą tego programu można utworzyć wyrażenie dla atrybutu *DisplayName* usługi AD w następujący sposób, aby uzyskać nazwę wyświetlaną *, np. Kowalski, Jan (Marketing-US)*.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Gdy masz odpowiednie wyrażenie, edytuj tabelę mapowania atrybutów i zmodyfikuj Mapowanie atrybutów *DisplayName* , jak pokazano poniżej:   ![ Mapowanie DisplayName](./media/workday-inbound-tutorial/wd_displayname_map.png)

* W powyższym przykładzie Załóżmy, że chcesz przekonwertować nazwy miast pochodzące z produktu Workday na wartości skrócone, a następnie użyć ich do kompilowania nazw wyświetlanych *, takich jak Kowalski, Jan (Chi)* lub *Nowak, Janina (NYC)*, a następnie można osiągnąć ten wynik przy użyciu wyrażenia Switch z atrybutem *gminy* firmy Workday jako zmiennej wyznacznikowej.

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
  * [Switch — Składnia funkcji](../app-provisioning/functions-for-customizing-application-data.md#switch)
  * [Składnia funkcji join](../app-provisioning/functions-for-customizing-application-data.md#join)
  * [Dołącz składnię funkcji](../app-provisioning/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Jak używać SelectUniqueValue do generowania unikatowych wartości dla atrybutu samAccountName?

Załóżmy, że chcesz wygenerować unikatowe wartości dla atrybutu *sAMAccountName* przy użyciu kombinacji wartości *FirstName* i *LastName* od dnia roboczego. Podano poniżej wyrażenie, które można rozpocząć od:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

Jak działa powyższe wyrażenie: Jeśli użytkownik ma Jan Kowalski, najpierw próbuje wygenerować JSmith, jeśli JSmith już istnieje, a następnie generuje JoSmith, jeśli istnieje, generuje JohSmith. Wyrażenie gwarantuje również, że wygenerowana wartość spełnia ograniczenie długości i ograniczenia znaków specjalnych skojarzone z *sAMAccountName*.

Zobacz też:

* [Funkcja MID — składnia](../app-provisioning/functions-for-customizing-application-data.md#mid)
* [Zamień składnię funkcji](../app-provisioning/functions-for-customizing-application-data.md#replace)
* [Składnia funkcji SelectUniqueValue](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Jak mogę usunąć znaki diakrytyczne i przekonwertować je na normalne alfabety angielskie?

Użyj funkcji [NormalizeDiacritics](../app-provisioning/functions-for-customizing-application-data.md#normalizediacritics) , aby usunąć znaki specjalne w imię i nazwisko użytkownika, podczas konstruowania adresu e-mail lub wartości CN dla użytkownika.

## <a name="troubleshooting-tips"></a>Wskazówki dotyczące rozwiązywania problemów

Ta sekcja zawiera szczegółowe wskazówki dotyczące rozwiązywania problemów z inicjowaniem obsługi administracyjnej w ramach integracji z programem Workday przy użyciu dzienników inspekcji usługi Azure AD i dzienników Podgląd zdarzeń systemu Windows Server. Jest on kompilowany na podstawie ogólnych kroków rozwiązywania problemów i koncepcji przechwyconych w [samouczku: Raportowanie dotyczące automatycznego aprowizacji kont użytkowników](../app-provisioning/check-status-user-account-provisioning.md)

W tej części omówiono następujące aspekty rozwiązywania problemów:

* [Konfigurowanie agenta aprowizacji w celu emitowania dzienników Podgląd zdarzeń](#configure-provisioning-agent-to-emit-event-viewer-logs)
* [Konfigurowanie Podgląd zdarzeń systemu Windows na potrzeby rozwiązywania problemów z agentem](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Konfigurowanie dzienników inspekcji Azure Portal na potrzeby rozwiązywania problemów z usługą](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Omówienie dzienników dla operacji tworzenia konta użytkownika usługi AD](#understanding-logs-for-ad-user-account-create-operations)
* [Omówienie dzienników dla operacji aktualizacji Menedżera](#understanding-logs-for-manager-update-operations)
* [Rozwiązywanie najczęściej występujących błędów](#resolving-commonly-encountered-errors)

### <a name="configure-provisioning-agent-to-emit-event-viewer-logs"></a>Konfigurowanie agenta aprowizacji w celu emitowania dzienników Podgląd zdarzeń
1. Zaloguj się na komputerze z systemem Windows Server, na którym został wdrożony Agent aprowizacji
1. Zatrzymaj usługę **Microsoft Azure AD Połącz agenta aprowizacji**.
1. Utwórz kopię oryginalnego pliku konfiguracji: *C:\Program Files\Microsoft Azure AD Connect aprowizacji Agent\AADConnectProvisioningAgent.exe.config*.
1. Zastąp istniejącą `<system.diagnostics>` sekcję następującą sekcją. 
   * Ta konfiguracja odbiornika **ETW** emituje komunikaty do dzienników Podglądzie zdarzeń
   * **TextWriterListener** konfiguracji odbiornika wysyła komunikaty śledzenia do pliku *ProvAgentTrace. log*. Usuń znaczniki komentarza z wierszy związanych z textWriterListener tylko w celu zaawansowania rozwiązywania problemów. 

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <!-- <add name="textWriterListener"/> -->
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <!-- <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/> -->
         </sharedListeners>
     </system.diagnostics>

   ```
1. Uruchom usługę Service **Microsoft Azure AD Connect Agent aprowizacji**.

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Konfigurowanie Podgląd zdarzeń systemu Windows na potrzeby rozwiązywania problemów z agentem

1. Zaloguj się na komputerze z systemem Windows Server, na którym został wdrożony Agent aprowizacji
1. Otwórz aplikację klasyczną **systemu Windows Server Podgląd zdarzeń** .
1. Wybierz pozycję **Dzienniki systemu Windows > aplikacji**.
1. Użyj **bieżącego dziennika filtru...** Opcja wyświetlania wszystkich zdarzeń zarejestrowanych w źródle **Azure AD Connect agenta aprowizacji** i wykluczania zdarzeń z identyfikatorem zdarzenia "5", określając filtr "-5", jak pokazano poniżej.
   > [!NOTE]
   > Zdarzenie o IDENTYFIKATORze 5 przechwytuje komunikaty Bootstrap agenta do usługi Azure AD w chmurze i dlatego filtruje ją podczas analizowania plików dziennika. 

   ![Podgląd zdarzeń systemu Windows](media/workday-inbound-tutorial/wd_event_viewer_01.png)

1. Kliknij przycisk **OK** i posortuj widok wyników według kolumny **daty i godziny** .

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Konfigurowanie dzienników inspekcji Azure Portal na potrzeby rozwiązywania problemów z usługą

1. Uruchom [Azure Portal](https://portal.azure.com)i przejdź do sekcji **dzienniki inspekcji** aplikacji do aprowizacji w programie Workday.
1. Użyj przycisku **kolumny** na stronie dzienniki inspekcji, aby wyświetlić tylko następujące kolumny w widoku (Data, działanie, stan, Przyczyna stanu). Ta konfiguracja zapewnia skoncentrowanie się tylko na danych, które są istotne w przypadku rozwiązywania problemów.

   ![Kolumny dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_00.png)

1. Użyj parametrów zapytania **docelowego** i **zakresu dat** , aby przefiltrować widok. 
   * Ustaw **docelowy** parametr zapytania na "Identyfikator procesu roboczego" lub "identyfikator pracownika" obiektu roboczego Workday.
   * Ustaw **zakres dat** na odpowiedni przedział czasu, w którym chcesz zbadać błędy lub problemy z obsługą administracyjną.

   ![Filtry dziennika inspekcji](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Omówienie dzienników dla operacji tworzenia konta użytkownika usługi AD

Po wykryciu nowego zatrudnienia w usłudze Workday (Załóżmy o IDENTYFIKATORze pracownika *21023*) usługa Azure AD Provisioning próbuje utworzyć nowe konto użytkownika usługi AD dla procesu roboczego, a w procesie tworzy 4 rekordy dziennika inspekcji zgodnie z poniższym opisem:

  [![Dziennik inspekcji — operacje tworzenia](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Po kliknięciu dowolnego z rekordów dziennika inspekcji zostanie otwarta strona **szczegóły działania** . Poniżej znajduje się Strona **szczegóły działania** wyświetlana dla każdego typu rekordu dziennika.

* Rekord **importu produktu Workday** : ten rekord dziennika zawiera informacje o procesie roboczym pobrane z produktu Workday. Skorzystaj z informacji w sekcji *dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z pobieraniem danych z produktu Workday. Przykładowy rekord jest przedstawiony poniżej wraz ze wskaźnikami dotyczącymi sposobu interpretacji poszczególnych pól.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Rekord **importu usługi AD** : ten rekord dziennika zawiera informacje o koncie pobieranym z usługi AD. Jak podczas początkowego tworzenia użytkownika nie ma konta usługi AD, *Przyczyna stanu działania* będzie wskazywać, że w Active Directory nie znaleziono konta o pasującej wartości atrybutu ID. Skorzystaj z informacji w sekcji *dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z pobieraniem danych z produktu Workday. Przykładowy rekord jest przedstawiony poniżej wraz ze wskaźnikami dotyczącymi sposobu interpretacji poszczególnych pól.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Aby znaleźć rekordy dziennika agenta aprowizacji odpowiadające tej operacji importowania usługi AD, Otwórz Dzienniki systemu Windows Podgląd zdarzeń i Użyj **Znajdź...** opcja menu umożliwiająca znalezienie wpisów dziennika zawierających pasującą wartość atrybutu ID/przyłączenia (w tym przypadku *21023*).

  ![Znajdowanie](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Poszukaj wpisu o *identyfikatorze zdarzenia = 9*, który zapewni filtr wyszukiwania LDAP używany przez agenta do pobierania konta usługi AD. Można sprawdzić, czy jest to odpowiedni filtr wyszukiwania do pobrania unikatowych wpisów użytkownika.

  ![Wyszukiwanie LDAP](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Rekord, który od razu następuje z *identyfikatorem zdarzenia = 2* , przechwytuje wynik operacji wyszukiwania i, jeśli zwróci jakiekolwiek wyniki.

  ![Wyniki LDAP](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Rekord **akcji reguły synchronizacji** : ten rekord dziennika zawiera wyniki reguł mapowania atrybutów i skonfigurowanych filtrów określania zakresu wraz z akcją aprowizacji, która zostanie podjęta w celu przetworzenia przychodzącego zdarzenia Workday. Skorzystaj z informacji w sekcji *dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z akcją synchronizacji. Przykładowy rekord jest przedstawiony poniżej wraz ze wskaźnikami dotyczącymi sposobu interpretacji poszczególnych pól.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Jeśli występują problemy z wyrażeniami mapowania atrybutów lub przychodzące dane dotyczące produktu Workday zawierają problemy (na przykład: wartość pusta lub zerowa dla wymaganych atrybutów), zobaczysz błąd na tym etapie, gdy kod błędu zawiera szczegóły błędu.

* Rekord **eksportu AD** : ten rekord dziennika przedstawia wynik operacji tworzenia konta usługi AD wraz z wartościami atrybutów ustawionymi w procesie. Skorzystaj z informacji w sekcji *dodatkowe szczegóły* rekordu dziennika, aby rozwiązać problemy z operacją tworzenia konta. Przykładowy rekord jest przedstawiony poniżej wraz ze wskaźnikami dotyczącymi sposobu interpretacji poszczególnych pól. W sekcji "dodatkowe szczegóły" "EventName" jest ustawiona na "EntryExportAdd", "JoiningProperty" jest ustawiona na wartość pasującego atrybutu ID, wartość "SourceAnchor" jest ustawiona na WorkdayID (WID) skojarzoną z rekordem, a "TargetAnchor" jest ustawiona na wartość atrybutu "ObjectGuid" nowo utworzonego użytkownika. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Aby znaleźć rekordy dziennika agenta aprowizacji odpowiadające tej operacji eksportowania usługi AD, Otwórz Dzienniki systemu Windows Podgląd zdarzeń i Użyj **Znajdź...** opcja menu umożliwiająca znalezienie wpisów dziennika zawierających pasującą wartość atrybutu ID/przyłączenia (w tym przypadku *21023*).  

  Wyszukaj rekord HTTP POST odpowiadający sygnatury czasowej operacji eksportu z *identyfikatorem zdarzenia = 2*. Ten rekord będzie zawierać wartości atrybutów wysyłane przez usługę aprowizacji do agenta aprowizacji.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_05.png" alt-text="Zrzut ekranu pokazujący rekord &quot;HTTP POST&quot; w dzienniku &quot;Agent aprowizacji&quot;." lightbox="media/workday-inbound-tutorial/wd_event_viewer_05.png":::

  Bezpośrednio po powyższym zdarzeniu powinno być inne zdarzenie, które przechwytuje odpowiedź na operację tworzenia konta usługi AD. To zdarzenie zwraca nowy objectGuid utworzony w usłudze AD i jest ustawiony jako atrybut TargetAnchor w usłudze aprowizacji.

  :::image type="content" source="media/workday-inbound-tutorial/wd_event_viewer_06.png" alt-text="Zrzut ekranu pokazujący dziennik &quot;Agent aprowizacji&quot; z identyfikatorem objectGuid utworzonym w usłudze AD." lightbox="media/workday-inbound-tutorial/wd_event_viewer_06.png":::

### <a name="understanding-logs-for-manager-update-operations"></a>Omówienie dzienników dla operacji aktualizacji Menedżera

Atrybut Manager jest atrybutem Reference w usłudze AD. Usługa aprowizacji nie ustawia atrybutu Manager jako części operacji tworzenia użytkownika. Zamiast tego atrybut Manager jest ustawiany jako część operacji *aktualizacji* po utworzeniu konta usługi AD dla użytkownika. Powyższy przykład, Załóżmy, że nowe zatrudnienie o IDENTYFIKATORze pracownika "21451" zostało aktywowane w dniach roboczych, a nowy Menedżer zatrudniania (*21023*) ma już konto usługi AD. W tym scenariuszu wyszukiwanie w dziennikach inspekcji dla użytkownika 21451 pokazuje 5 wpisów.

  [![Aktualizacja Menedżera](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Pierwsze 4 rekordy są podobne do tych, które zostały omówione w ramach operacji tworzenia użytkownika. Piąty rekord jest eksportem skojarzonym z aktualizacją atrybutu Menedżera. Rekord dziennika przedstawia wynik operacji aktualizacji Menedżera kont usługi AD, która jest wykonywana przy użyciu atrybutu *objectGUID* Menedżera.

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

### <a name="resolving-commonly-encountered-errors"></a>Rozwiązywanie najczęściej występujących błędów

W tej sekcji opisano często występujące błędy podczas aprowizacji użytkowników z produktu Workday oraz sposób ich rozwiązywania. Błędy są pogrupowane w następujący sposób:

* [Błędy agenta aprowizacji](#provisioning-agent-errors)
* [Błędy łączności](#connectivity-errors)
* [Błędy tworzenia konta użytkownika usługi AD](#ad-user-account-creation-errors)
* [Błędy aktualizacji konta użytkownika usługi AD](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Błędy agenta aprowizacji

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Wystąpił błąd podczas instalowania agenta aprowizacji z komunikatem o błędzie:  *nie można uruchomić usługi "Microsoft Azure AD połączyć się z agentem aprowizacji" (AADConnectProvisioningAgent). Sprawdź, czy masz wystarczające uprawnienia do uruchamiania systemu.* | Ten błąd jest zwykle wyświetlany, jeśli próbujesz zainstalować agenta aprowizacji na kontrolerze domeny, a zasady grupy uniemożliwiają uruchomienie usługi.  Jest również widoczna, jeśli masz Poprzednia wersja agenta z uruchomioną usługą i nie został on odinstalowany przed rozpoczęciem nowej instalacji.| Zainstaluj agenta aprowizacji na serwerze niebędącym kontrolerem domeny. Przed zainstalowaniem nowego agenta upewnij się, że poprzednie wersje agenta zostały odinstalowane.|
|2.| Agent aprowizacji usługi systemu Windows "Microsoft Azure AD Connect" jest w stanie *początkowym* i nie przechodzi do stanu *uruchomienia* . | W ramach instalacji Kreator agenta tworzy konto lokalne (**NT Service \\ AADConnectProvisioningAgent**) na serwerze i jest to konto logowania używane do uruchamiania usługi. Jeśli zasady zabezpieczeń na serwerze z systemem Windows uniemożliwiają uruchamianie usług na kontach lokalnych, ten błąd zostanie wyświetlony. | Otwórz *konsolę usługi*. Kliknij prawym przyciskiem myszy usługę systemu Windows "Microsoft Azure AD Połącz agenta aprowizacji" i na karcie Logowanie Podaj konto administratora domeny, aby uruchomić usługę. Należy ponownie uruchomić usługę. |
|3.| Podczas konfigurowania agenta aprowizacji przy użyciu domeny usługi AD w kroku *łączenie Active Directory* Kreator zajmuje dużo czasu, próbując załadować schemat usługi AD i ostatecznie przekroczyć limit czasu. | Ten błąd jest zazwyczaj wyświetlany, jeśli kreator nie może skontaktować się z serwerem kontrolera domeny usługi AD ze względu na problemy z zaporą. | Na ekranie Kreator *łączenia Active Directory* , podczas podawania poświadczeń dla domeny usługi AD istnieje opcja o nazwie *Wybierz priorytet kontrolera domeny*. Użyj tej opcji, aby wybrać kontroler domeny znajdujący się w tej samej lokacji co serwer agenta i upewnić się, że nie ma żadnych reguł zapory blokujących komunikację. |

#### <a name="connectivity-errors"></a>Błędy łączności

Jeśli usługa aprowizacji nie może nawiązać połączenia z dniem Workday lub Active Directory, może to spowodować, że inicjowanie obsługi zostanie przeprowadzone w stanie kwarantanny. Skorzystaj z poniższej tabeli, aby rozwiązać problemy z łącznością.

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Po kliknięciu przycisku **Testuj połączenie** zostanie wyświetlony komunikat o błędzie: wystąpił *błąd podczas nawiązywania połączenia z Active Directory. Upewnij się, że lokalny Agent aprowizacji jest uruchomiony i jest skonfigurowany z poprawną domeną Active Directory.* | Ten błąd jest zwykle wyświetlany, jeśli Agent aprowizacji nie jest uruchomiony lub istnieje zapora blokująca komunikację między usługą Azure AD a agentem aprowizacji. Ten błąd może również pojawić się, jeśli domena nie jest skonfigurowana w Kreatorze agenta. | Otwórz konsolę *usługi* na serwerze z systemem Windows, aby upewnić się, że Agent jest uruchomiony. Otwórz Kreatora agenta aprowizacji i sprawdź, czy odpowiednia domena jest zarejestrowana w agencie.  |
|2.| Zadanie aprowizacji przejdzie do stanu kwarantanny w weekendy (pt-SAT) i otrzymamy wiadomość e-mail z powiadomieniem, że wystąpił błąd synchronizacji. | Częstą przyczyną występowania tego błędu są planowane przestoje produktu Workday. Jeśli korzystasz z dzierżawy implementacji produktu Workday, pamiętaj, że ten produkt ma w weekendy zaplanowane przestoje dzierżaw implementacji (zwykle od wieczora w piątek do rana w sobotę). W tym czasie aplikacje aprowizacji produktu Workday mogą przechodzić w stan kwarantanny, ponieważ nie mogą nawiązać połączenia z produktem Workday. Normalny stan zostaje przywrócony po ponownym przejściu w tryb online dzierżawy implementacji produktu Workday. W rzadkich przypadkach ten błąd może być także wyświetlany, jeśli zmieniło się hasło użytkownika systemu integracji z powodu odświeżenia dzierżawy lub jeśli konto zostało zablokowane bądź wygasło. | Zapytaj administratora produktu Workday lub partnera integracji o czas planowanych przestojów produktu Workday, aby wiedzieć, które komunikaty o alertach można zignorować, i móc potwierdzić dostępność wystąpienia produktu Workday po jego ponownym przejściu w tryb online.  |

#### <a name="ad-user-account-creation-errors"></a>Błędy tworzenia konta użytkownika usługi AD

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Niepowodzenia operacji eksportu w dzienniku inspekcji z *błędem komunikatu: OperationsError-SvcErr: Wystąpił błąd operacji. Nie skonfigurowano nadrzędnego odwołania dla usługi katalogowej. W związku z tym usługa katalogowa nie może wystawić odwołań do obiektów spoza tego lasu.* | Ten błąd jest zwykle wyświetlany, jeśli jednostka organizacyjna *kontenera Active Directory* nie jest ustawiona poprawnie lub jeśli występują problemy z mapowaniem wyrażenia użytego dla *parentDistinguishedName*. | Sprawdź parametr *Active Directory* jednostki organizacyjnej kontenera dla literówki. Jeśli korzystasz z elementu *parentDistinguishedName* podczas mapowania atrybutów, upewnij się, że zawsze wynikiem jego oceny jest znany kontener w ramach domeny usługi AD. Sprawdź zdarzenie *eksportu* w dziennikach inspekcji, aby wyświetlić wygenerowaną wartość. |
|2.| Niepowodzenia operacji eksportu w dzienniku inspekcji z kodem błędu: *SystemForCrossDomainIdentityManagementBadResponse* i komunikat *o błędzie: ConstraintViolation-AtrErr: wartość w żądaniu jest nieprawidłowa. Wartość atrybutu nie ma akceptowalnego zakresu wartości. Szczegóły \nError: CONSTRAINT_ATT_TYPE — firma*. | Chociaż ten błąd jest specyficzny dla atrybutu *firmy* , może zostać wyświetlony ten błąd dla innych atrybutów, takich jak *CN* . Ten błąd występuje ze względu na ograniczenie schematu wymuszane przez usługi AD. Domyślnie atrybuty, takie jak *firma* i *CN* w usłudze AD, mają górny limit wynoszący 64 znaków. Jeśli wartość pochodząca z produktu Workday przekracza 64 znaków, zobaczysz ten komunikat o błędzie. | Sprawdź zdarzenie *eksportu* w dziennikach inspekcji, aby wyświetlić wartość atrybutu zgłoszonego w komunikacie o błędzie. Rozważ Obcinanie wartości z produktu Workday przy użyciu funkcji [Mid](../app-provisioning/functions-for-customizing-application-data.md#mid) lub zmianę mapowań na atrybut AD, który nie ma ograniczeń o podobnej długości.  |

#### <a name="ad-user-account-update-errors"></a>Błędy aktualizacji konta użytkownika usługi AD

Podczas procesu aktualizacji konta użytkownika usługi AD usługa aprowizacji odczytuje informacje z programów Workday i AD, uruchamia reguły mapowania atrybutów i określa, czy zmiany muszą zostać zastosowane. W związku z tym wyzwalane jest zdarzenie aktualizacji. Jeśli którykolwiek z tych kroków napotka błąd, jest on rejestrowany w dziennikach inspekcji. Skorzystaj z poniższej tabeli, aby rozwiązać typowe problemy z aktualizacją.

|#|Scenariusz błędu |Prawdopodobne przyczyny|Zalecane rozwiązanie|
|--|---|---|---|
|1.| Błędy akcji reguły synchronizacji w dzienniku inspekcji z komunikatem *EventName = EntrySynchronizationError i ErrorCode = EndpointUnavailable*. | Ten błąd jest wyświetlany, jeśli usługa aprowizacji nie może pobrać danych profilu użytkownika z Active Directory z powodu błędu przetwarzania napotkanego przez lokalny Agent aprowizacji. | Sprawdź dzienniki aprowizacji Podgląd zdarzeń dla zdarzeń błędów wskazujących na problemy z operacją odczytu (Filtruj według identyfikatora zdarzenia #2). |
|2.| Atrybut Manager w usłudze AD nie zostanie zaktualizowany dla niektórych użytkowników w usłudze AD. | Najbardziej prawdopodobną przyczyną tego błędu jest użycie reguł określania zakresu, a Menedżer użytkownika nie należy do zakresu. Ten problem może również wystąpić, jeśli w docelowej domenie usługi AD nie znaleziono atrybutu identyfikatora zgodnego z menedżerem (np. IDPracownika) lub nie ustawiono poprawnej wartości. | Przejrzyj filtr określania zakresu i Dodaj użytkownika kierownika w zakresie. Sprawdź profil kierownika w usłudze AD, aby upewnić się, że istnieje wartość atrybutu pasującego identyfikatora. |

## <a name="managing-your-configuration"></a>Zarządzanie konfiguracją

W tej sekcji opisano, jak można dodatkowo rozszerzać i dostosowywać konfigurację aprowizacji użytkowników opartą na usłudze Workday oraz zarządzać nią. Obejmuje następujące tematy:

* [Dostosowywanie listy atrybutów użytkownika produktu Workday](#customizing-the-list-of-workday-user-attributes)  
* [Eksportowanie i importowanie konfiguracji](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Dostosowywanie listy atrybutów użytkownika produktu Workday

Aplikacje do obsługi administracyjnej w programie Workday dla Active Directory i usługi Azure AD zawierają domyślną listę atrybutów użytkownika produktu Workday, spośród których można wybierać. Jednak te listy nie są wyczerpujące. Program Workday obsługuje wiele setek atrybutów użytkowników, które mogą być standardowe lub unikatowe dla dzierżawy produktu Workday.

Usługa Azure AD Provisioning obsługuje możliwość dostosowywania listy lub atrybutu Workday w celu uwzględnienia wszelkich atrybutów, które są widoczne w [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) operacji interfejsu API zasobów ludzkich.

Aby to zmienić, należy użyć programu [Workday Studio](https://community.workday.com/studio-download) do wyodrębnienia wyrażeń XPath reprezentujących atrybuty, których chcesz użyć, a następnie dodać je do konfiguracji aprowizacji przy użyciu Zaawansowanego edytora atrybutów w Azure Portal.

**Aby pobrać wyrażenie XPath dla atrybutu użytkownika Workday:**

1. Pobierz i zainstaluj program [Workday Studio](https://community.workday.com/studio-download). Do uzyskania dostępu do Instalatora będzie potrzebne konto społecznościowe w usłudze Workday.

2. Pobierz plik dni roboczych **Human_Resources** WSDL specyficzny dla wersji interfejsu API WWS planowanej do użycia z [katalogu usług sieci Web Workday](https://community.workday.com/sites/default/files/file-hosting/productionapi/index.html)

3. Uruchom aplikację Workday Studio.

4. Na pasku poleceń wybierz pozycję  **Workday > testowa usługa sieci Web w testerze** .

5. Wybierz pozycję **zewnętrzny**, a następnie wybierz plik Human_Resources WSDL pobrany w kroku 2.

    ![Zrzut ekranu pokazujący plik "Human_Resources" otwarty w programie Workday Studio.](./media/workday-inbound-tutorial/wdstudio1.png)

6. W polu **Lokalizacja** Określ wartość `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` , ale Zastąp ciąg "Impl-CC" rzeczywistym typem wystąpienia i "dzierżawca" nazwą swojej rzeczywistej dzierżawy.

7. Ustaw **operację** na **Get_Workers**

8.    Kliknij łącze małe **Konfigurowanie** poniżej okienka żądanie/odpowiedź, aby ustawić poświadczenia dla produktu Workday. Sprawdź pozycję **uwierzytelnianie**, a następnie wprowadź nazwę użytkownika i hasło do konta systemu integracji z programem Workday. Upewnij się, że nazwa użytkownika jest sformatowana jako \@ dzierżawca, i pozostaw wybraną opcję **WS-Security UsernameToken** .
   ![Zrzut ekranu pokazujący kartę "zabezpieczenia" z wprowadzonym parametrem "username" i "Password" oraz "WS-Security Username token".](./media/workday-inbound-tutorial/wdstudio2.png)

9. Wybierz przycisk **OK**.

10. W okienku **żądanie** wklej poniższy kod XML. Ustaw **EMPLOYEE_ID** identyfikator pracownika rzeczywistego użytkownika w dzierżawie platformy Workday. Ustaw wartość **WD: Version** do wersji WWS, która ma zostać użyta. Wybierz użytkownika, który ma atrybut wypełniony, który ma zostać wyodrębniony.

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

11. Kliknij **żądanie wysłania** (zieloną strzałkę), aby wykonać polecenie. Jeśli to się powiedzie, odpowiedź powinna pojawić się w okienku **odpowiedzi** . Sprawdź odpowiedź, aby upewnić się, że ma ona dane wprowadzonego identyfikatora użytkownika, a nie błąd.

12. Jeśli to się powiedzie, skopiuj plik XML z okienka **odpowiedzi** i Zapisz go jako plik XML.

13. Na pasku poleceń programu Workday Studio wybierz pozycję **plik > Otwórz plik...** i otwórz zapisany plik XML. Ta akcja spowoduje otwarcie pliku w edytorze XML programu Workday Studio.

    ![Zrzut ekranu przedstawiający plik X M L otwarty w edytorze "Workday Studio X M L".](./media/workday-inbound-tutorial/wdstudio3.png)

14. W drzewie plików Przejdź przez **/env: koperta > env: Body > WD: Get_Workers_Response > WD: Response_Data > WD: proces roboczy** , aby znaleźć dane użytkownika.

15. W obszarze **WD: Worker** Znajdź atrybut, który chcesz dodać, a następnie wybierz go.

16. Skopiuj wyrażenie XPath dla wybranego atrybutu spoza pola **ścieżka dokumentu** .

17. Usuń **/env: Envelope/env: Body/WD: Get_Workers_Response/WD: Response_Data/** prefix z skopiowanego wyrażenia.

18. Jeśli ostatni element w skopiowanym wyrażeniu jest węzłem (przykład: "/WD: Birth_Date"), a następnie Dołącz **/Text ()** na końcu wyrażenia. Nie jest to konieczne, jeśli ostatni element jest atrybutem (przykład: " /@wd: Type").

19. Wynik powinien być podobny do tego `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()` . Ta wartość jest kopiowana do Azure Portal.

**Aby dodać niestandardowy atrybut użytkownika Workday do konfiguracji aprowizacji:**

1. Uruchom [Azure Portal](https://portal.azure.com)i przejdź do sekcji aprowizacji aplikacji do aprowizacji w programie Workday, zgodnie z opisem we wcześniejszej części tego samouczka.

2. Ustaw **stan aprowizacji** na **wyłączony**, a następnie wybierz pozycję **Zapisz**. Ten krok pomaga zapewnić, że zmiany zostaną zastosowane tylko wtedy, gdy wszystko będzie gotowe.

3. W obszarze **mapowania** wybierz pozycję **Synchronizuj procesy robocze programu Workday z lokalnymi Active Directory** (lub **zsynchronizuj procesy robocze z systemem Workday z usługą Azure AD**).

4. Przewiń w dół następnego ekranu, a następnie wybierz pozycję **Pokaż opcje zaawansowane**.

5. Wybierz pozycję **Edytuj listę atrybutów dla produktu Workday**.

    ![Zrzut ekranu przedstawiający stronę "Inicjowanie obsługi administracyjnej użytkowników w usłudze Azure A D" z wyróżnioną akcją "Edytuj listę atrybutów dla produktu Workday".](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Przewiń w dół listy atrybutów do miejsca, w którym znajdują się pola wejściowe.

7. W obszarze **Nazwa** wprowadź nazwę wyświetlaną dla atrybutu.

8. Dla opcji **Typ** wybierz typ, który odpowiednio odpowiada atrybutowi (**ciąg** jest najbardziej typowy).

9. Dla **wyrażenia interfejsu API** wprowadź wyrażenie XPath skopiowane z programu Workday Studio. Przykład: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wybierz pozycję **Dodaj atrybut**.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wybierz pozycję **Zapisz** powyżej, a następnie kliknij przycisk **tak** w oknie dialogowym. Zamknij ekran Attribute-Mapping, jeśli jest nadal otwarty.

12. Z powrotem na głównej karcie **aprowizacji** wybierz pozycję **Synchronizuj procesy robocze w dniach roboczych z lokalną Active Directory** (lub **zsynchronizuj pracowników ponownie z usługą Azure AD**).

13. Wybierz pozycję **Dodaj nowe mapowanie**.

14. Nowy atrybut powinien teraz pojawić się na liście **atrybutów źródłowych** .

15. W razie potrzeby dodaj mapowanie dla nowego atrybutu.

16. Po zakończeniu Pamiętaj, aby ustawić **stan aprowizacji** z powrotem **na Włącz** i Zapisz.

### <a name="exporting-and-importing-your-configuration"></a>Eksportowanie i importowanie konfiguracji

Zapoznaj się z artykułem [Eksportowanie i Importowanie konfiguracji aprowizacji](../app-provisioning/export-import-provisioning-configuration.md)

## <a name="managing-personal-data"></a>Zarządzanie danymi osobowymi

Rozwiązanie do aprowizacji w dniu Workday dla Active Directory wymaga zainstalowania agenta aprowizacji na lokalnym serwerze z systemem Windows, a ten Agent tworzy dzienniki w dzienniku zdarzeń systemu Windows, które mogą zawierać dane osobowe w zależności od Twojego programu Workday do mapowań atrybutów usługi AD. Aby zachować zgodność z zobowiązaniami w zakresie prywatności użytkowników, można upewnić się, że żadne dane nie zostaną zachowane w dziennikach zdarzeń dłużej niż 48 godzin przez skonfigurowanie zaplanowanego zadania systemu Windows w celu wyczyszczenia dziennika zdarzeń.

Usługa Azure AD Provisioning znajduje się w kategorii **procesor danych** klasyfikacji Rodo. Jako potok przetwarzający dane Usługa udostępnia usługi przetwarzania danych klientom najważniejszych partnerów i klientów końcowych. Usługa Azure AD Provisioning nie generuje danych użytkownika i nie ma niezależnej kontroli nad tym, jakie dane osobowe są zbierane i jak są wykorzystywane. Pobieranie i agregacja danych, analiza i raportowanie w usłudze Azure AD Provisioning są oparte na istniejących danych przedsiębiorstwa.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

W odniesieniu do przechowywania danych usługa Azure AD Provisioning nie generuje raportów, nie przeprowadza analizy ani nie udostępnia szczegółowych informacji poza 30 dni. W związku z tym usługa Azure AD Provisioning nie przechowuje, nie przetwarza ani nie zachowuje żadnych danych dłużej niż 30 dni. Ten projekt jest zgodny z przepisami Rodo, regulacjami zgodności ochrony prywatności firmy Microsoft i zasadami przechowywania danych usługi Azure AD.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej na temat scenariuszy integracji usługi Azure AD i produktu Workday oraz wywołań usług sieci Web](../app-provisioning/workday-integration-reference.md)
* [Dowiedz się, jak przeglądać dzienniki i uzyskiwać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md)
* [Dowiedz się, jak skonfigurować Logowanie jednokrotne między produktem Workday i Azure Active Directory](workday-tutorial.md)
* [Dowiedz się, jak skonfigurować zapisywanie zwrotne dla produktu Workday](workday-writeback-tutorial.md)
* [Dowiedz się, jak używać interfejsów API Microsoft Graph do zarządzania konfiguracjami aprowizacji](/graph/api/resources/synchronization-overview)