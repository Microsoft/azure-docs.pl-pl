---
title: Planowanie aplikacji w chmurze w celu Azure Active Directory aprowizacji użytkowników
description: W tym artykule opisano proces wdrażania integracji systemów kadr w chmurze, takich jak Workday i SuccessFactors, z Azure Active Directory. Integracja usługi Azure AD z systemem zarządzania KADRą w chmurze skutkuje ukończeniem całego cyklu życia tożsamości.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: kenwith
ms.reviewer: arvindha, celested
ms.openlocfilehash: 9c896d4cccf898b8818b4c363c5bc891a8734ca5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256715"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>Planowanie aplikacji w chmurze w celu Azure Active Directory aprowizacji użytkowników

W przeszłości pracownicy IT korzystali z ręcznych metod tworzenia, aktualizowania i usuwania pracowników. Używają metod, takich jak przekazywanie plików CSV lub skryptów niestandardowych do synchronizowania danych pracownika. Te procesy aprowizacji są podatne na błędy, niezabezpieczone i trudne do zarządzania.

Aby zarządzać cyklem życia tożsamości pracowników, dostawców lub procesów roboczych, [Azure Active Directory (Azure AD) usługa aprowizacji użytkowników](../app-provisioning/user-provisioning.md) oferuje integrację z aplikacjami ludzkimi opartymi na chmurze (HR). Przykładami aplikacji są: Workday lub SuccessFactors.

Usługa Azure AD używa tej integracji w celu włączenia następujących przepływów pracy aplikacji w chmurze (aplikacji):

- **Udostępnianie użytkownikom Active Directory:** Zainicjuj obsługę wybranych zestawów użytkowników z poziomu aplikacji w chmurze w jednej lub kilku domenach Active Directory.
- **Udostępnianie użytkownikom tylko w chmurze usługi Azure AD:** W scenariuszach, w których Active Directory nie są używane, należy udostępnić użytkownikom bezpośrednio z aplikacji KADRowej w chmurze do usługi Azure AD.
- **Zapisuj z powrotem do aplikacji kadrowej w chmurze:** Napisz adresy e-mail i atrybuty nazwy użytkownika z usługi Azure AD z powrotem do aplikacji w chmurze.

> [!NOTE]
> Ten plan wdrożenia pokazuje, jak wdrożyć przepływy pracy aplikacji kadr w chmurze przy użyciu aprowizacji użytkowników usługi Azure AD. Aby uzyskać informacje na temat sposobu wdrażania automatycznego aprowizacji użytkowników w aplikacjach typu oprogramowanie jako usługa (SaaS), zobacz [Planowanie automatycznego wdrażania aprowizacji użytkowników](./plan-auto-user-provisioning.md).

## <a name="enabled-hr-scenarios"></a>Włączone scenariusze kadr

Usługa aprowizacji użytkowników w usłudze Azure AD umożliwia automatyzację następujących scenariuszy zarządzania cyklem życia tożsamości opartych na usłudze HR:

- **Nowe zatrudnienie pracownika:** Po dodaniu nowego pracownika do aplikacji w chmurze, konto użytkownika zostanie automatycznie utworzone w Active Directory i w usłudze Azure AD z opcją zapisania z powrotem adresu e-mail i atrybutów username do aplikacji w chmurze.
- **Aktualizacje atrybutu pracownika i profilu:** Gdy rekord pracownika, taki jak nazwa, tytuł lub Menedżer, zostanie zaktualizowany w aplikacji w chmurze, konto użytkownika zostanie automatycznie zaktualizowane w Active Directory i Azure AD.
- **Zakończenia pracowników:** Gdy pracownik zostanie zakończony w aplikacji KADRowej w chmurze, jego konto użytkownika zostanie automatycznie wyłączone w Active Directory i w usłudze Azure AD.
- **Pracownicy przezatrudnieni:** Gdy pracownik jest ponownie zatrudniany w aplikacji KADRowej w chmurze, jego stare konto może zostać automatycznie ponownie uaktywnione lub zainicjowane w celu Active Directory i usługi Azure AD.

## <a name="who-is-this-integration-best-suited-for"></a>Dla kogo jest to integracja najlepiej dopasowanej?

Integracja aplikacji w chmurze z obsługą administracyjną użytkowników w usłudze Azure AD doskonale nadaje się dla organizacji, które:

- Potrzebujesz wstępnie skompilowanego rozwiązania opartego na chmurze do aprowizacji użytkowników w chmurze.
- Wymagaj bezpośredniej aprowizacji użytkowników z aplikacji w chmurze w celu Active Directory lub Azure AD.
- Wymaganie, aby użytkownicy mieli możliwość aprowizacji przy użyciu danych uzyskanych z aplikacji KADRowej w chmurze.
- Wymagaj synchronizacji, przeniesienia i opuszczenia użytkowników do co najmniej jednego Active Directory lasów, domen i jednostek organizacyjnych na podstawie informacji o zmianach wykrytych w aplikacji w chmurze.
- Użyj Microsoft 365 do poczty e-mail.

## <a name="learn"></a>Learn

Inicjowanie obsługi użytkowników tworzy podstawę do ciągłego zarządzania tożsamościami. Zwiększa ono jakość procesów biznesowych, które opierają się na autorytatywnych danych tożsamości.

### <a name="terms"></a>Terminologia

W tym artykule są stosowane następujące warunki:

- **System źródłowy**: repozytorium użytkowników, z których pochodzą postanowienia usługi Azure AD. Przykładem jest aplikacja usługi Cloud kadr, taka jak Workday lub SuccessFactors.
- **System docelowy**: repozytorium użytkowników, do których mają prawa usługi Azure AD. Przykładami są Active Directory, Azure AD, Microsoft 365 lub inne aplikacje SaaS.
- **Przyłączanie — proces opuszczania firmy przeprowadzki**: termin używany do nowych zatrudniania, transferów i kończenia pracy przy użyciu aplikacji kadrowej w chmurze jako systemu rekordów. Proces kończy się po pomyślnym zainicjowaniu przez usługę niezbędnych atrybutów do systemu docelowego.

### <a name="key-benefits"></a>Najważniejsze korzyści

Ta funkcja aprowizacji IT oparta na usłudze kadr oferuje następujące korzyści biznesowe:

- **Zwiększ produktywność:** Teraz można zautomatyzować przypisanie kont użytkowników i Microsoft 365 licencji oraz zapewnić dostęp do grup kluczy. Automatyzacja przypisań pozwala nowym zatrudniać natychmiastowy dostęp do swoich narzędzi do zadań i zwiększa produktywność.
- **Zarządzanie ryzykiem:** Aby zwiększyć bezpieczeństwo, można zautomatyzować zmiany w zależności od stanu pracownika lub członkostwa w grupach przy użyciu danych przepływających z aplikacji w chmurze. Automatyzacja zmian gwarantuje, że tożsamości użytkowników i dostęp do najważniejszych aplikacji są aktualizowane automatycznie, gdy użytkownicy przechodzą lub opuściją organizację.
- **Zgodność i zarządzanie adresami:** Usługa Azure AD obsługuje natywne dzienniki inspekcji dla żądań aprowizacji użytkowników wykonywanych przez aplikacje obu systemów źródłowych i docelowych. Za pomocą inspekcji można śledzić, kto ma dostęp do aplikacji z jednego ekranu.
- **Zarządzaj kosztami:** Automatyczne Inicjowanie obsługi zmniejsza koszty, unikając nieefektywności i błędu ludzkiego związanego z obsługą ręczną. Pozwala to ograniczyć potrzebę tworzenia niestandardowo rozbudowanych rozwiązań użytkowników w czasie z użyciem starszych i nieaktualnych platform.

### <a name="licensing"></a>Licencjonowanie

Aby skonfigurować aplikację kadr w chmurze do integracji aprowizacji użytkowników usługi Azure AD, musisz mieć ważną [licencję Azure AD — wersja Premium](https://azure.microsoft.com/pricing/details/active-directory/) i licencję dla aplikacji kadrowej w chmurze, taką jak Workday lub SuccessFactors.

Potrzebna jest również ważna licencja subskrypcyjna na Azure AD — wersja Premium P1 lub nowsza dla każdego użytkownika, który zostanie pożądany przez aplikację kadr w chmurze i zostanie zainicjowany do Active Directory lub Azure AD. Niewłaściwa liczba licencji należących do aplikacji w chmurze w usłudze Cloud kadr może prowadzić do błędów podczas aprowizacji użytkownika.

### <a name="prerequisites"></a>Wymagania wstępne

- [Administrator tożsamości hybrydowej](../roles/permissions-reference.md#hybrid-identity-administrator) usługi Azure AD w celu skonfigurowania agenta aprowizacji Azure AD Connect.
- Rola [administratora aplikacji](../roles/permissions-reference.md#application-administrator) usługi Azure AD w celu skonfigurowania aplikacji do aprowizacji w Azure Portal
- Wystąpienie testowe i produkcyjne aplikacji w chmurze.
- Uprawnienia administratora w aplikacji KADRowej w chmurze umożliwiającej tworzenie użytkownika integracji systemu i wprowadzanie zmian w celu przetestowania danych pracownika na potrzeby testowania.
- W celu aprowizacji użytkowników do Active Directory, na serwerze z systemem Windows Server 2012 lub nowszym z programem .NET 4.7.1 + Runtime wymagany jest hostowanie agenta aprowizacji Azure AD Connect
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) synchronizowania użytkowników między Active Directory i Azure AD.

### <a name="training-resources"></a>Zasoby szkoleniowe

| **Zasoby** | **Link i opis** |
|:-|:-|
| Filmy wideo | [Co to jest inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure?](https://youtu.be/_ZjARPpI6NI) |
| | [Jak wdrożyć Inicjowanie obsługi użytkowników w usłudze Active Directory systemu Azure](https://youtu.be/pKzyts6kfrw) |
| Samouczki | [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure AD](../saas-apps/tutorial-list.md) |
| | [Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| Często zadawane pytania | [Automatyczne Inicjowanie obsługi użytkowników](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [Inicjowanie obsługi z poziomu produktu Workday do usługi Azure AD](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>Architektura rozwiązania

Poniższy przykład opisuje kompleksową architekturę rozwiązania obsługi użytkowników dla typowych środowisk hybrydowych i obejmuje:

- **Autorytatywny przepływ danych HR z aplikacji usługi Cloud kadr do Active Directory.** W tym przepływie jest inicjowane zdarzenie HR (dla sprzężeń — proces opuszczania i opuszczanie) w dzierżawie aplikacji w chmurze. Usługa Azure AD Provisioning i Agent aprowizacji Azure AD Connect udostępniają dane użytkownika z dzierżawy aplikacji kadr w chmurze do usługi Active Directory. W zależności od zdarzenia może to prowadzić do tworzenia, aktualizowania, włączania i wyłączania operacji w Active Directory.
- **Synchronizuj z usługą Azure AD i zapisuj pocztę e-mail i nazwę użytkownika z lokalnego Active Directory do aplikacji w chmurze.** Po zaktualizowaniu kont w Active Directory są one synchronizowane z usługą Azure AD za pomocą usługi Azure AD Connect. Adresy e-mail i atrybuty nazwy użytkownika można zapisać z powrotem do dzierżawy aplikacji KADRowej w chmurze.

![Diagram przepływu pracy](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>Opis przepływu pracy

Na diagramie przedstawiono następujące podstawowe kroki:  

1. **Zespół kadr** wykonuje transakcje w dzierżawie aplikacji kadrowej w chmurze.
2. **Usługa Azure AD Provisioning** uruchamia zaplanowane cykle z dzierżawy aplikacji kadr w chmurze i identyfikuje zmiany, które muszą zostać przetworzone w celu synchronizacji z Active Directory.
3. **Usługa Azure AD Provisioning** wywołuje Azure AD Connect agenta aprowizacji z ładunkiem żądania, który zawiera Active Directory konta tworzenia, aktualizowania, włączania i wyłączania.
4. **Azure AD Connect Agent aprowizacji** używa konta usługi do zarządzania danymi konta Active Directory.
5. **Azure AD Connect** uruchamia [synchronizację](../hybrid/how-to-connect-sync-whatis.md) Delta w celu ściągnięcia aktualizacji w Active Directory.
6. Aktualizacje **Active Directory** są synchronizowane z usługą Azure AD.
7. **Usługa Azure AD Provisioning** zapisuje z powrotem atrybut poczty e-mail i nazwę użytkownika z usługi Azure AD do dzierżawy aplikacji kadrowej w chmurze.

## <a name="plan-the-deployment-project"></a>Planowanie projektu wdrożenia

Podczas określania strategii tego wdrożenia w danym środowisku należy wziąć pod uwagę potrzeby organizacyjne.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

W przypadku niepowodzenia projektów technologicznych zazwyczaj są one spowodowane niezgodnymi oczekiwaniami na wpływ, wyniki i obowiązki. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md)Cię odpowiednie osoby zainteresowane. Upewnij się również, że role udziałowców w projekcie są dobrze zrozumiałe. Udokumentowanie uczestników projektu i ich danych wejściowych i accountabilities.

Dołącz przedstawiciela z organizacji KADRy, który może zapewnić dane wejściowe dla istniejących procesów biznesowych usługi kadr i tożsamości procesu roboczego oraz wymagania dotyczące przetwarzania danych zadania.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Aktywnie Komunikuj się z użytkownikami, aby dowiedzieć się, kiedy i w jaki sposób ich środowisko zostanie zmienione. Daj im znać, jak uzyskać pomoc techniczną, jeśli wystąpią problemy.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Integrowanie procesów biznesowych i przepływów pracy tożsamości z aplikacji w chmurze w usłudze Cloud prod z systemami docelowymi wymaga dużej ilości walidacji danych, transformacji danych, czyszczenia danych i kompleksowego testowania przed wdrożeniem rozwiązania w środowisku produkcyjnym.

Uruchom konfigurację początkową w [środowisku pilotażowym](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) przed przeskalowaniem jej do wszystkich użytkowników w produkcji.

## <a name="select-cloud-hr-provisioning-connector-apps"></a>Wybierz aplikacje łącznika usługi Cloud Personal

Aby ułatwić przepływy pracy aprowizacji usługi Azure AD między aplikacją i Active Directory w chmurze, można dodać wiele aplikacji łącznika aprowizacji z galerii aplikacji usługi Azure AD:

- **Aplikacja w chmurze do Active Directory aprowizacji użytkowników**: Ta aplikacja łącznika aprowizacji ułatwia Inicjowanie obsługi konta użytkownika z poziomu aplikacji kadr w chmurze w jednej domenie Active Directory. Jeśli masz wiele domen, możesz dodać jedno wystąpienie tej aplikacji z galerii aplikacji usługi Azure AD dla każdej domeny Active Directory, do której należy przeprowadzić obsługę administracyjną.
- **Obsługa administracyjna aplikacji w chmurze w usłudze Azure AD**: podczas Azure AD Connect jest to narzędzie, które ma być używane do synchronizowania Active Directory użytkowników z usługą Azure AD. Ta aplikacja łącznika aprowizacji może służyć do ułatwienia aprowizacji użytkowników korzystających tylko z chmury z aplikacji w chmurze w ramach jednej dzierżawy usługi Azure AD.
- **Zapisywanie zwrotne aplikacji w chmurze**: Ta aplikacja łącznika aprowizacji ułatwia zapisywanie adresów e-mail użytkowników z usługi Azure AD do aplikacji w chmurze.

Na przykład na poniższej ilustracji przedstawiono aplikacje łącznika produktu Workday dostępne w galerii aplikacji usługi Azure AD.

![Galeria aplikacji portalu Azure Active Directory](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>Wykres przepływu decyzji

Użyj poniższego wykresu przepływu decyzji, aby określić, które aplikacje do obsługi kadr w chmurze są odpowiednie dla Twojego scenariusza.

![Wykres przepływu decyzji](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>Zaprojektowanie topologii wdrażania agenta aprowizacji Azure AD Connect

Integracja aprowizacji między aplikacją i Active Directory w chmurze wymaga czterech składników:

- Dzierżawa aplikacji KADRowej w chmurze
- Aplikacja łącznika aprowizacji
- Azure AD Connect agenta aprowizacji
- Domena Active Directory

Azure AD Connect topologia wdrażania agenta aprowizacji jest zależna od liczby dzierżawców aplikacji w chmurze i Active Directory domen podrzędnych, które mają zostać zintegrowane. Jeśli masz wiele domen Active Directory, zależy od tego, czy domeny Active Directory są ciągłe, czy [rozłączane](/windows-server/identity/ad-ds/plan/disjoint-namespace).

Na podstawie decyzji wybierz jeden z scenariuszy wdrażania:

- Dzierżawa pojedynczej aplikacji w chmurze — > docelowej jednej lub wielu Active Directory domen podrzędnych w lesie zaufanym
- Dzierżawa pojedynczej aplikacji w chmurze — > celu wielu domen podrzędnych w oddzielnym lesie Active Directory

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>Dzierżawa pojedynczej aplikacji w chmurze — > docelowej jednej lub wielu Active Directory domen podrzędnych w lesie zaufanym

Zalecamy zastosowanie następującej konfiguracji produkcyjnej:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba Azure AD Connect agentów aprowizacji do wdrożenia|Dwa (w przypadku wysokiej dostępności i trybu failover)
|Liczba aplikacji łącznika aprowizacji do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla Azure AD Connect agenta aprowizacji|Windows 2012 R2 + z wierszem wglądu do geozlokalizowane Active Directory kontrolery domeny</br>Może współistnieć z usługą Azure AD Connect|

![Przepływ do agentów lokalnych](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>Dzierżawa pojedynczej aplikacji w chmurze — > celu wielu domen podrzędnych w oddzielnym lesie Active Directory

Ten scenariusz obejmuje Inicjowanie obsługi użytkowników z aplikacji w chmurze w przypadku domen rozłączonych Active Directory lasów.

Zalecamy zastosowanie następującej konfiguracji produkcyjnej:

|Wymaganie|Zalecenie|
|:-|:-|
|Liczba Azure AD Connect agentów aprowizacji do wdrożenia w środowisku lokalnym|Dwa na odłączony Las Active Directory|
|Liczba aplikacji łącznika aprowizacji do skonfigurowania|Jedna aplikacja na domenę podrzędną|
|Host serwera dla Azure AD Connect agenta aprowizacji|Windows 2012 R2 + z wierszem wglądu do geozlokalizowane Active Directory kontrolery domeny</br>Może współistnieć z usługą Azure AD Connect|

![Dzierżawa usługi Single App Active Directory w chmurze](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect wymagania dotyczące agenta aprowizacji

Aplikacja Cloud kadr dla Active Directory rozwiązanie do aprowizacji użytkowników wymaga wdrożenia co najmniej jednego Azure AD Connect agentów aprowizacji na serwerach z systemem Windows 2012 R2 lub nowszym. Serwery muszą mieć co najmniej 4 GB pamięci RAM i .NET 4.7.1 + Runtime. Upewnij się, że serwer hosta ma dostęp sieciowy do domeny docelowej Active Directory.

Aby przygotować środowisko lokalne, Kreator konfiguracji agenta aprowizacji Azure AD Connect zarejestruje agenta za pomocą dzierżawy usługi Azure AD, [otwiera porty](../manage-apps/application-proxy-add-on-premises-application.md#open-ports), [zezwala na dostęp do adresów URL](../manage-apps/application-proxy-add-on-premises-application.md#allow-access-to-urls)i obsługuje [konfigurację wychodzącego serwera proxy HTTPS](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).

Agent aprowizacji używa konta usługi do komunikacji z domenami Active Directory. Przed zainstalowaniem agenta należy utworzyć konto usługi w Active Directory Użytkownicy i komputery, które spełniają następujące wymagania:

- Hasło, które nie wygasa
- Delegowane uprawnienia kontroli do odczytu, tworzenia, usuwania i zarządzania kontami użytkowników

Można wybrać kontrolery domeny, które powinny obsługiwać żądania aprowizacji. Jeśli masz kilka rozproszonych geograficznie kontrolerów domeny, Zainstaluj agenta aprowizacji w tej samej lokacji, w której znajdują się preferowane kontrolery domeny. To pozycjonowanie zwiększa niezawodność i wydajność kompleksowego rozwiązania.

Aby zapewnić wysoką dostępność, można wdrożyć więcej niż jednego Azure AD Connect agenta aprowizacji. Zarejestruj agenta, aby obsłużyć ten sam zestaw lokalnych domen Active Directory.

## <a name="plan-scoping-filters-and-attribute-mapping"></a>Planowanie filtrów zakresu i mapowania atrybutów

Po włączeniu obsługi administracyjnej aplikacji w chmurze w celu Active Directory lub usługi Azure AD Azure Portal kontroluje wartości atrybutów za pośrednictwem mapowania atrybutu.

### <a name="define-scoping-filters"></a>Definiowanie filtrów określania zakresu

[Filtry zakresu](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) umożliwiają definiowanie reguł opartych na atrybutach, które określają, którzy użytkownicy powinni być obsługiwani z aplikacji kadrowej w chmurze do Active Directory lub Azure AD.

Po zainicjowaniu procesu przyłączania należy zebrać następujące wymagania:

- Czy aplikacja usługi Cloud kadr służy do przeprowadzenia na pokładzie pracowników i pracowników warunkowych?
- Czy planujesz używać aplikacji w chmurze do aprowizacji użytkowników usługi Azure AD do zarządzania pracownikami i pracownikami warunkowymi?
- Czy planujesz wdrożyć aplikację kadr w chmurze na potrzeby aprowizacji użytkowników usługi Azure AD tylko dla podzbioru użytkowników aplikacji w chmurze? Przykładem mogą być tylko pracownicy.

W zależności od wymagań podczas konfigurowania mapowań atrybutów można ustawić pole **Zakres obiektu źródłowego** , aby wybrać, które zestawy użytkowników w aplikacji kadrowej w chmurze powinny znajdować się w zakresie dla aprowizacji Active Directory. Aby uzyskać więcej informacji, zobacz Samouczek dotyczący aplikacji Cloud kadr w przypadku najczęściej używanych filtrów określania zakresu.

### <a name="determine-matching-attributes"></a>Określanie pasujących atrybutów

Dzięki aprowizacji można dopasować istniejące konta między systemem źródłowym a docelowym. Po zintegrowaniu aplikacji usługi Cloud kadr z usługą Azure AD Provisioning można [skonfigurować Mapowanie atrybutów](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings) , aby określić, jakie dane użytkownika powinny być przepływać z aplikacji w chmurze do Active Directory lub Azure AD.

Po zainicjowaniu procesu przyłączania należy zebrać następujące wymagania:

- Jaki jest unikatowy identyfikator w tej aplikacji usługi Cloud kadr, który służy do identyfikowania poszczególnych użytkowników?
- Jak można obsłużyć się w perspektywie cyklu życia tożsamości? Czy pracownicy przezatrudniają stare identyfikatory pracowników?
- Czy możesz przetwarzać zatrudnienie w przyszłości i tworzyć konta Active Directory z nich z góry?
- W ramach perspektywy cyklu życia tożsamości, w jaki sposób można obsłużyć proces konwersji pracownika na warunkowe lub w inny sposób?
- Czy przekonwertowani użytkownicy zachowują swoje stare konta Active Directory lub otrzymują nowe?

W zależności od wymagań usługa Azure AD obsługuje bezpośrednie Mapowanie atrybutów między atrybutami, dostarczając wartości stałe lub [pisząc wyrażenia dla mapowań atrybutów](../app-provisioning/functions-for-customizing-application-data.md). Ta elastyczność zapewnia ostateczną kontrolę nad tym, co jest zapełnione w atrybucie aplikacji Target. Korzystając z [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) i Eksploratora grafów, można wyeksportować mapowania atrybutów aprowizacji użytkowników i schemat do pliku JSON, a następnie zaimportować je z powrotem do usługi Azure AD.

Domyślnie atrybut w aplikacji KADRowej w chmurze reprezentujący unikatowy identyfikator pracownika jest używany jako pasujący atrybut *mapowany na unikatowy atrybut w Active Directory.* Na przykład w scenariuszu aplikacji Workday atrybut **Workday** **WorkerID** jest mapowany na atrybut Active Directory **IDPracownika** .

Można ustawić wiele pasujących atrybutów i przypisać priorytet pasujący. Są one oceniane w oparciu o pasujące pierwszeństwo. Po znalezieniu dopasowania nie są oceniane żadne dalsze pasujące atrybuty.

Można również [dostosować domyślne mapowania atrybutów](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types), takie jak zmiana lub usuwanie istniejących mapowań atrybutów. Można również tworzyć nowe mapowania atrybutów zgodnie z potrzebami biznesowymi. Aby uzyskać więcej informacji, zobacz Samouczek dotyczący aplikacji Cloud Kadr (na przykład [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)), aby zapoznać się z listą atrybutów niestandardowych, które mają być mapowane.

### <a name="determine-user-account-status"></a>Określanie stanu konta użytkownika

Domyślnie aplikacja łącznika aprowizacji mapuje stan profilu użytkownika usługi HR na stan konta użytkownika w Active Directory lub Azure AD w celu ustalenia, czy należy włączyć lub wyłączyć konto użytkownika.

Po zainicjowaniu procesu Joiners-Leavers należy zebrać poniższe wymagania.

| Proces | Wymagania |
| - | - |
| **Sprzężenia** | Jak można obsłużyć się w perspektywie cyklu życia tożsamości? Czy pracownicy przezatrudniają stare identyfikatory pracowników? |
| | Czy możesz przetwarzać zatrudnienie w przyszłości i tworzyć konta Active Directory z nich z góry? Czy te konta są tworzone w stanie włączony lub wyłączony? |
| | W ramach perspektywy cyklu życia tożsamości, w jaki sposób można obsłużyć proces konwersji pracownika na warunkowe lub w inny sposób? |
| | Czy przekonwertowani użytkownicy zachowują swoje stare konta Active Directory lub otrzymują nowe? |
| **Wyjściu** | Czy zakończenia są obsługiwane inaczej w przypadku pracowników i pracowników warunkowych w Active Directory? |
| | Jakie daty efektywne są brane pod uwagę w przypadku przetwarzania zakończenia użytkownika? |
| | W jaki sposób pracownicy i warunkowe konwersje procesów roboczych wpływają na istniejące Active Directory konta? |
| | Jak przetworzyć operację odwołaj w Active Directory? Operacje odwołaj należy obsługiwać, jeśli w ramach procesu łącznika zostaną utworzone przyszłe zatrudnienie w Active Directory. |

W zależności od wymagań można dostosować logikę mapowania przy użyciu [wyrażeń usługi Azure AD](../app-provisioning/functions-for-customizing-application-data.md) , aby konto Active Directory zostało włączone lub wyłączone na podstawie kombinacji punktów danych.

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>Mapowanie aplikacji KADRowej w chmurze na Active Directory atrybuty użytkownika

Każda aplikacja KADRa w chmurze jest dostarczana z domyślną aplikacją usługi Cloud kadr do Active Directory mapowań.

Po zainicjowaniu procesu joins-Interleavers należy zebrać poniższe wymagania.

| Proces | Wymagania |
| - | - |
| **Sprzężenia** | Czy Active Directory proces tworzenia konta jest ręczny, zautomatyzowany lub częściowo zautomatyzowany? |
| | Czy planujesz propagowanie atrybutów niestandardowych z aplikacji w chmurze do Active Directory? |
| **DM** | Jakie atrybuty chcesz przetworzyć za każdym razem, gdy w aplikacji w chmurze jest wykonywana operacja firmy przeprowadzki? |
| | Czy wykonujesz wszystkie określone walidacje atrybutów w momencie aktualizacji użytkownika? Jeśli tak, podaj szczegóły. |
| **Wyjściu** | Czy zakończenia są obsługiwane inaczej w przypadku pracowników i pracowników warunkowych w Active Directory? |
| | Jakie daty efektywne są brane pod uwagę w przypadku przetwarzania zakończenia użytkownika? |
| | Jak pracownicy i warunkowe konwersje procesów roboczych wpływają na istniejące Active Directory konta? |

W zależności od wymagań można zmodyfikować mapowania, aby spełniały cele integracji. Aby uzyskać więcej informacji, zobacz Samouczek dotyczący konkretnej aplikacji w chmurze (na przykład [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)), aby zapoznać się z listą atrybutów niestandardowych, które mają być mapowane.

### <a name="generate-a-unique-attribute-value"></a>Generuj unikatową wartość atrybutu

Po zainicjowaniu procesu przyłączania może być konieczne wygenerowanie unikatowych wartości atrybutów podczas ustawiania atrybutów, takich jak CN, samAccountName i UPN, z unikatowymi ograniczeniami.

Funkcja Azure AD [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) oblicza każdą regułę, a następnie sprawdza wartość wygenerowaną w celu zapewnienia unikatowości w systemie docelowym. Aby zapoznać się z przykładem, zobacz [generowanie unikatowej wartości atrybutu userPrincipalName (UPN)](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute).

> [!NOTE]
> Ta funkcja jest obecnie obsługiwana tylko w celu Active Directory aprowizacji użytkowników w programie Workday. Nie można jej używać z innymi aplikacjami aprowizacji.

### <a name="configure-active-directory-ou-container-assignment"></a>Konfigurowanie przypisania kontenera jednostki organizacyjnej Active Directory

Jest to typowy wymóg umieszczania Active Directory kont użytkowników w kontenerach w oparciu o jednostki biznesowe, lokalizacje i działy. Po zainicjowaniu procesu firmy przeprowadzkowej, jeśli istnieje zmiana organizacji nadzorczej, może być konieczne przeniesienie użytkownika z jednej jednostki organizacyjnej do innej w Active Directory.

Użyj funkcji [Switch ()](../app-provisioning/functions-for-customizing-application-data.md#switch) , aby skonfigurować logikę biznesową dla przypisania jednostki organizacyjnej i zamapować ją na Active Directory atrybutu **parentDistinguishedName**.

Na przykład jeśli chcesz utworzyć użytkowników w jednostce organizacyjnej na **podstawie atrybutu HR**, można użyć następującego wyrażenia:

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

W tym wyrażeniu, jeśli wartość gminy to Dallas, Austin, Seattle lub Londyn, konto użytkownika zostanie utworzone w odpowiedniej jednostce organizacyjnej. Jeśli nie ma dopasowania, konto zostanie utworzone w domyślnej jednostce organizacyjnej.

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>Planowanie dostarczania haseł dla nowych kont użytkowników

Po zainicjowaniu procesu przyłączania należy ustawić i dostarczyć tymczasowe hasło nowych kont użytkowników. Za pomocą usługi Cloud HR dla użytkowników usługi Azure AD można wdrożyć funkcję samoobsługowego [resetowania hasła](../authentication/tutorial-enable-sspr.md) (SSPR) usługi Azure AD dla użytkownika na dzień.

SSPR to prosty środek dla administratorów IT umożliwiający użytkownikom Resetowanie swoich haseł lub odblokowywanie ich kont. Możesz udostępnić atrybut **numeru telefonu komórkowego** z poziomu aplikacji usługi Cloud kadr w celu Active Directory i zsynchronizowania go z usługą Azure AD. Gdy atrybut **numeru komórkowego** jest w usłudze Azure AD, możesz włączyć SSPR dla konta użytkownika. Następnie nowy użytkownik może użyć zarejestrowanego i zweryfikowanego numeru komórkowego na potrzeby uwierzytelniania.

## <a name="plan-for-initial-cycle"></a>Planowanie cyklu początkowego

Gdy usługa Azure AD Provisioning jest uruchamiana po raz pierwszy, wykonuje [początkowy cykl](../app-provisioning/how-provisioning-works.md#initial-cycle) względem aplikacji kadrowej w chmurze, aby utworzyć migawkę wszystkich obiektów użytkownika w aplikacji w chmurze. Czas trwania cykli początkowej zależy od liczby użytkowników w systemie źródłowym. Cykl początkowy dla niektórych dzierżawców aplikacji usługi Cloud kadr z ponad 100 000 użytkownikami może zająć dużo czasu.

W **przypadku dużych dzierżawców aplikacji kadrowych w chmurze (>30 000 użytkowników)** Uruchom cykl początkowy w etapach progresywnych. Aktualizacje przyrostowe należy uruchamiać dopiero po sprawdzeniu, czy poprawne atrybuty są ustawione w Active Directory dla różnych scenariuszy aprowizacji użytkowników. Postępuj zgodnie z poniższą kolejnością.

1. Uruchom cykl początkowy tylko dla ograniczonego zestawu użytkowników, ustawiając [Filtr określania zakresu](#plan-scoping-filters-and-attribute-mapping).
2. Sprawdź, Active Directory aprowizacji konta i wartości atrybutów ustawione dla użytkowników wybranych do pierwszego uruchomienia. Jeśli wynik spełnia Twoje oczekiwania, rozwiń filtr określania zakresu, aby stopniowo obejmować więcej użytkowników i zweryfikować wyniki drugiego przebiegu.

Po spełnieniu wyników cyklu początkowego dla użytkowników testowych Uruchom [Aktualizacje przyrostowe](../app-provisioning/how-provisioning-works.md#incremental-cycles).

## <a name="plan-testing-and-security"></a>Planowanie testowania i zabezpieczeń

Na każdym etapie wdrożenia od początkowej wersji pilotażowej przez włączenie aprowizacji użytkowników upewnij się, że testy są odpowiednie, a następnie Przeprowadź inspekcję cykli aprowizacji.

### <a name="plan-testing"></a>Testowanie planu

Po skonfigurowaniu aplikacji do obsługi administracyjnej w chmurze w usłudze Azure AD należy uruchomić przypadki testowe, aby sprawdzić, czy to rozwiązanie spełnia wymagania organizacji.

|Scenariusze|Oczekiwane wyniki|
|:-|:-|
|Nowy pracownik jest zatrudniany w aplikacji KADRowej w chmurze.| — Konto użytkownika jest obsługiwane w Active Directory.</br>— Użytkownik może zalogować się do aplikacji Active Directory-Domain i wykonać odpowiednie działania.</br>— Jeśli Azure AD Connect synchronizacji jest skonfigurowany, konto użytkownika zostanie również utworzone w usłudze Azure AD.
|Użytkownik jest zakończony w aplikacji w chmurze.|— Konto użytkownika jest wyłączone w Active Directory.</br>— Użytkownik nie może zalogować się do żadnych aplikacji dla przedsiębiorstw chronionych przez Active Directory.
|Organizacja nadzorcza użytkownika jest aktualizowana w aplikacji w chmurze.|Na podstawie mapowania atrybutów konto użytkownika jest przenoszone z jednej jednostki organizacyjnej do innej w Active Directory.|
|HR aktualizuje Menedżera użytkownika w aplikacji KADRowej w chmurze.|Pole Menedżera w Active Directory zostało zaktualizowane w celu odzwierciedlenia nazwy nowego menedżera.|
|HR zatrudnia pracownika w nowej roli.|Zachowanie zależy od konfiguracji aplikacji KADRowej w chmurze w celu generowania identyfikatorów pracowników:</br>— Jeśli identyfikator starego pracownika jest ponownie używany w przypadku ponownego wynajmu, łącznik włącza istniejące konto Active Directory dla użytkownika.</br>— Jeśli ponowne zatrudnienie spowoduje pobranie nowego identyfikatora pracownika, łącznik tworzy nowe konto Active Directory dla użytkownika.|
|HR konwertuje pracownika na pracownika kontraktu lub na odwrót.|Nowe konto Active Directory zostanie utworzone dla nowej osoby, a stare konto zostanie wyłączone przy dacie wejścia w życie konwersji.|

Skorzystaj z poprzednich wyników, aby określić sposób przejścia implementacji automatycznej aprowizacji użytkowników do środowiska produkcyjnego na podstawie ustalonych osi czasu.

> [!TIP]
> Używaj technik, takich jak zmniejszenie ilości danych i szybka Kontrola danych podczas odświeżania środowiska testowego z danymi produkcyjnymi, aby usunąć lub zamaskować poufne dane osobowe w celu zapewnienia zgodności ze standardami ochrony prywatności i zabezpieczeń. 

### <a name="plan-security"></a>Planowanie zabezpieczeń

W ramach wdrażania nowej usługi często wymagane jest sprawdzenie zabezpieczeń. Jeśli Przegląd zabezpieczeń jest wymagany lub nie został przeprowadzony, zapoznaj się z artykułem wiele [oficjalnych dokumentów](https://www.microsoft.com/download/details.aspx?id=36391) usługi Azure AD, które zawierają omówienie tożsamości jako usługi.

### <a name="plan-rollback"></a>Planowanie wycofywania

Implementacja inicjowania obsługi administracyjnej użytkowników w chmurze może nie działać zgodnie z oczekiwaniami w środowisku produkcyjnym. W takim przypadku następujące kroki wycofywania mogą pomóc w powracaniu do poprzedniego znanego dobrego stanu.

1. Przejrzyj [Raport z podsumowaniem aprowizacji](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal) i [Inicjowanie obsługi administracyjnej](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview) , aby określić, jakie nieprawidłowe operacje zostały wykonane na odpowiednich użytkownikach lub grupach. Aby uzyskać więcej informacji na temat podsumowania raportów i dzienników udostępniania, zobacz [Zarządzanie obsługą użytkowników aplikacji w chmurze](#manage-your-configuration).
2. Ostatni znany dobry stan użytkowników lub grup można określić za pomocą dzienników inspekcji aprowizacji lub przeglądając systemy docelowe (Azure AD lub Active Directory).
3. Współpracuj z właścicielem aplikacji, aby zaktualizować użytkowników lub grupy objęte bezpośrednio w aplikacji przy użyciu ostatnich znanych wartości stanu.

## <a name="deploy-the-cloud-hr-app"></a>Wdróż aplikację usługi Cloud kadr

Wybierz aplikację kadr w chmurze, która jest wyrównana do wymagań dotyczących rozwiązania.

**Workday**: Aby zaimportować profile procesów roboczych z produktu Workday do Active Directory i usługi Azure AD, zobacz [Samouczek: Konfigurowanie produktu Workday do automatycznego aprowizacji użytkowników](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment). Opcjonalnie możesz napisać adres e-mail, nazwę użytkownika i numer telefonu do dnia roboczego.

**SAP SuccessFactors**: Aby zaimportować profile procesów roboczych z usługi SuccessFactors do Active Directory i Azure AD, zobacz [Samouczek: Konfigurowanie SAP SuccessFactors dla automatycznej aprowizacji użytkowników](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md). Opcjonalnie możesz napisać adres e-mail i nazwę użytkownika do SuccessFactors.

## <a name="manage-your-configuration"></a>Zarządzanie konfiguracją

Usługa Azure AD może zapewnić dodatkowe informacje dotyczące użycia i kondycji operacyjnej w organizacji za pomocą dzienników inspekcji i raportów.

### <a name="gain-insights-from-reports-and-logs"></a>Uzyskiwanie szczegółowych informacji z raportów i dzienników

Po pomyślnym [cyklu początkowym](../app-provisioning/how-provisioning-works.md#initial-cycle)usługa Azure AD Provisioning będzie nadal uruchamiać aktualizacje przyrostowe z powrotem do tyłu, w odstępach czasu zdefiniowanych w samouczkach dotyczących poszczególnych aplikacji, dopóki nie wystąpi jedno z następujących zdarzeń:

- Usługa została zatrzymana ręcznie. Nowy cykl początkowy jest wyzwalany przy użyciu [Azure Portal](https://portal.azure.com/) lub odpowiedniego polecenia [API Microsoft Graph](/graph/api/resources/synchronization-overview) .
- Nowy cykl początkowy jest wyzwalany ze względu na zmianę mapowań atrybutów lub filtrów określania zakresu.
- Proces aprowizacji jest kierowany do kwarantanny z powodu wysokiego współczynnika błędów. Pozostaje w kwarantannie przez ponad cztery tygodnie, w których czas jest automatycznie wyłączany.

Aby przejrzeć te zdarzenia i wszystkie inne działania wykonywane przez usługę aprowizacji, [Dowiedz się, jak przejrzeć dzienniki i uzyskać raporty dotyczące działań aprowizacji](../app-provisioning/check-status-user-account-provisioning.md).

#### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w dziennikach inspekcji usługi Azure AD. Dzienniki inspekcji usługi Azure AD można kierować do Azure Monitor dzienników w celu dalszej analizy. Za pomocą dzienników Azure Monitor (znanych także jako Log Analytics obszaru roboczego) można wykonywać zapytania dotyczące danych, aby znaleźć zdarzenia, analizować trendy i wykonywać korelacje w różnych źródłach danych. Obejrzyj ten [film wideo](https://youtu.be/MP5IaCTwkQg) , aby poznać zalety korzystania z dzienników Azure monitor na potrzeby dzienników usługi Azure AD w praktycznych scenariuszach użytkownika.

Zainstaluj [widoki usługi log Analytics dla dzienników aktywności w usłudze Azure AD](../reports-monitoring/howto-install-use-log-analytics-views.md) , aby uzyskać dostęp do [wstępnie skompilowanych raportów](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views) dotyczących zdarzeń aprowizacji w Twoim środowisku.

Aby uzyskać więcej informacji, zobacz jak [analizować dzienniki aktywności usługi Azure AD za pomocą dzienników Azure monitor](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md).

### <a name="manage-personal-data"></a>Zarządzanie danymi osobowymi

Agent aprowizacji Azure AD Connect zainstalowany w systemie Windows Server tworzy dzienniki w dzienniku zdarzeń systemu Windows, które mogą zawierać dane osobowe w zależności od aplikacji w chmurze do Active Directory mapowań atrybutów. Aby zachować zgodność z wymaganiami dotyczącymi prywatności użytkowników, należy skonfigurować zaplanowane zadanie systemu Windows, aby wyczyścić dziennik zdarzeń i upewnić się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Usługa Azure AD Provisioning nie generuje raportów, nie przeprowadza analizy ani nie udostępnia szczegółowych informacji poza 30 dni, ponieważ usługa nie przechowuje, nie przetwarza ani nie przechowuje żadnych danych dłużej niż 30 dni.

### <a name="troubleshoot"></a>Rozwiązywanie problemów

Aby rozwiązać problemy, które mogą zostać przełączane podczas aprowizacji, zobacz następujące artykuły:

- [Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD](application-provisioning-config-problem.md)
- [Synchronizowanie atrybutu z Active Directory lokalnego z usługą Azure AD w celu aprowizacji aplikacji](user-provisioning-sync-attributes-for-mapping.md)
- [Wystąpił problem podczas zapisywania poświadczeń administratora podczas konfigurowania aprowizacji użytkowników w aplikacji Galerii Azure Active Directory](./user-provisioning.md)
- [Nie zainicjowano obsługi użytkowników w aplikacji z galerii usługi Azure AD](application-provisioning-config-problem-no-users-provisioned.md)
- [W aplikacji z galerii usługi Azure AD jest inicjowany niewłaściwy zestaw użytkowników](../manage-apps/add-application-portal-assign-users.md)
- [Konfigurowanie Podgląd zdarzeń systemu Windows na potrzeby rozwiązywania problemów z agentem](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [Konfigurowanie dzienników inspekcji Azure Portal na potrzeby rozwiązywania problemów z usługą](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [Omówienie dzienników dla operacji tworzenia konta użytkownika usługi AD](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [Omówienie dzienników dla operacji aktualizacji Menedżera](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [Rozwiązywanie najczęściej występujących błędów](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>Następne kroki

- [Pisanie wyrażeń do mapowania atrybutów](functions-for-customizing-application-data.md)
- [Omówienie interfejsu API synchronizacji usługi Azure AD](/graph/api/resources/synchronization-overview)
- [Pomiń usuwanie kont użytkowników, które wykraczają poza zakres](skip-out-of-scope-deletions.md)
- [Azure AD Connect agenta aprowizacji: historia wersji](provisioning-agent-release-version-history.md)