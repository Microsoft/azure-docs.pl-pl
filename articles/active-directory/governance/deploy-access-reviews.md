---
title: Planowanie wdrożenia przeglądów dostępu Azure Active Directory
description: Przewodnik planowania dla pomyślnego wdrożenia przeglądów dostępu
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4122e645b76751e8944704a6405cf5dee09129f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97932439"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Planowanie wdrożenia Azure Active Directory przeglądy dostępu

[Przeglądy dostępu w usłudze Azure Active Directory (Azure AD)](access-reviews-overview.md) ułatwiają organizacji utrzymywanie bezpieczeństwa sieci przez zarządzanie [cyklem życia dostępu do zasobów](identity-governance-overview.md). Za pomocą przeglądów dostępu można:

* Zaplanuj regularne przeglądy lub wykonaj przeglądy ad hoc, aby zobaczyć, kto ma dostęp do określonych zasobów, takich jak aplikacje i grupy

* Śledzenie przeglądów szczegółowych informacji, zgodności lub zasad

* Deleguj przeglądy do określonych administratorów, właścicieli firm lub użytkowników końcowych, którzy mogą zaświadczać o konieczności ciągłego dostępu

* Korzystaj z szczegółowych informacji, aby efektywnie określić, czy użytkownicy powinni nadal mieć dostęp

* Automatyzuj wyniki przeglądu, takie jak usuwanie dostępu użytkowników do zasobów

  ![Diagram przedstawiający przepływ przeglądów dostępu.](./media/deploy-access-review/1-planning-review.png)

Przeglądy dostępu to [Azure AD Identity Governance](identity-governance-overview.md) możliwości. Inne funkcje to [Zarządzanie uprawnieniami](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) i [warunki użytkowania](../conditional-access/terms-of-use.md). Wspólnie ułatwiają one organizacjom rozwiązywanie tych czterech pytań:

* Którzy użytkownicy powinni mieć dostęp do zasobów?

* Jakie są użytkownicy korzystający z tego dostępu?

* Czy istnieje skuteczna kontrola organizacyjna na potrzeby zarządzania dostępem?

* Czy inspekcje mogą sprawdzić, czy kontrolki działają?

Planowanie wdrożenia recenzji dostępu jest niezbędne w celu zapewnienia osiągnięcia odpowiedniej strategii zarządzania dla użytkowników w organizacji.

### <a name="key-benefits"></a>Najważniejsze korzyści

Najważniejsze zalety włączenia przeglądów dostępu to:

* **Kontrola współpracy**. Przeglądy dostępu umożliwiają organizacjom zarządzanie dostępem do wszystkich zasobów, których potrzebują użytkownicy. Gdy użytkownicy współużytkują i współpracują, organizacje mogą mieć pewność, że informacje są tylko dla autoryzowanych użytkowników.

* **Zarządzanie ryzykiem**. Przeglądy dostępu zapewniają organizacjom możliwość przeglądania dostępu do danych i aplikacji, co zmniejsza ryzyko wycieku danych i wycieku danych. Obejmuje to możliwości regularnego przeglądania dostępu partnera zewnętrznego do zasobów firmy. 

* **Adresowanie zgodności i zarządzania**. Za pomocą przeglądów dostępu można zarządzać cyklem życia dostępu do grup, aplikacji i witryn oraz je zatwierdzać. Możesz kontrolować przeglądy śledzenia pod kątem zgodności lub aplikacji zależnych od ryzyka, które są specyficzne dla Twojej organizacji. 

* **Obniżyć koszty**. Przeglądy dostępu są kompilowane w chmurze i natywnie współdziałają z zasobami w chmurze, takimi jak grupy, aplikacje i pakiety dostępu. Korzystanie z przeglądów dostępu jest tańsze niż tworzenie własnych narzędzi lub w inny sposób uaktualnianie lokalnego zestawu danych.

### <a name="training-resources"></a>Zasoby szkoleniowe

Następujące filmy wideo mogą być przydatne podczas nauki dotyczącej przeglądów dostępu:

* [Co to są przeglądy dostępu w usłudze Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Jak utworzyć przeglądy dostępu w usłudze Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Jak włączyć przeglądy dostępu w usłudze Azure AD](https://youtu.be/X1SL2uubx9M)

* [Jak przejrzeć dostęp przy użyciu dostępu](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licencje

Wymagana jest prawidłowa licencja na Azure AD — wersja Premium (P2) dla każdej osoby, innej niż administratorzy globalni lub Administratorzy użytkowników, którzy będą tworzyć lub wykonywać przeglądy dostępu. Aby uzyskać więcej informacji, zobacz [wymagania licencyjne dotyczące przeglądów dostępu](access-reviews-overview.md).

Mogą być również wymagane inne funkcje zarządzania tożsamościami, takie jak [Zarządzanie cyklem życia uprawnień](entitlement-management-overview.md) lub Zarządzanie tożsamościami uprzywilejowanymi. W takim przypadku może być również konieczne pokrewnych licencji. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Planowanie projektu wdrożenia przeglądy dostępu

Należy wziąć pod uwagę potrzebę organizacji, aby określić strategię wdrażania przeglądów dostępu w danym środowisku.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich uczestników projektu

Gdy projekty technologii kończą się niepowodzeniem, zazwyczaj są to spowodowane niezgodnością oczekiwań, rezultatów i obowiązków. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md) Cię odpowiednie osoby zainteresowane i że role projektu są jasne.

Aby zapoznać się z przeglądami dostępu, prawdopodobnie zostaną uwzględnione przedstawiciele następujących zespołów w organizacji:

* **Administracja IT** służy do zarządzania infrastrukturą IT i administrowania inwestycjami w chmurze oraz aplikacjami oprogramowania jako usługi (SaaS). Ten zespół będzie:

   * Przejrzyj dostęp uprzywilejowany do infrastruktury i aplikacji, w tym Microsoft 365 i usługi Azure AD.

   * Zaplanuj i uruchom przeglądy dostępu dla grup, które są używane do obsługi list wyjątków lub projektów pilotażowych IT, aby zachować aktualne listy dostępu.

   * Upewnij się, że programistyczny (skryptowy) dostęp do zasobów za pomocą jednostek usługi jest podlegał i przeglądany.

* **Zespoły deweloperów** kompilują i utrzymują aplikacje dla organizacji. Ten zespół będzie:

   * Kontroluj, kto może uzyskiwać dostęp do składników w SaaS, PaaS i IaaS i zarządzać nimi, które obejmują opracowane rozwiązania.

   * Zarządzaj grupami, które mogą uzyskiwać dostęp do aplikacji i narzędzi do wewnętrznego tworzenia aplikacji.

   * Wymagaj tożsamości uprzywilejowanych mających dostęp do oprogramowania lub rozwiązań produkcyjnych hostowanych dla klientów

* **Jednostki biznesowe** zarządzają projektami i własnymi aplikacjami. Ten zespół będzie: 

   * Przeglądanie i zatwierdzanie lub odrzucanie dostępu do grup i aplikacji dla użytkowników wewnętrznych i zewnętrznych.

   * Zaplanuj i wykonaj przeglądy, aby zaświadczać ciągły dostęp do pracowników i tożsamości zewnętrznych, takich jak partnerzy biznesowi.

* **Nadzór korporacyjny** gwarantuje, że organizacja korzysta z zasad wewnętrznych i jest zgodna z przepisami. Ten zespół będzie:

   * Żądaj lub Zaplanuj nowe przeglądy dostępu.

   * Oceniaj procesy i procedury umożliwiające przeglądanie dostępu, w tym dokumentację i rejestrowanie w celu zachowania zgodności.

   * Przejrzyj wyniki poprzednich przeglądów dla najbardziej krytycznych zasobów.

> [!NOTE]
> W przypadku przeglądów wymagających ręcznych ocen należy zaplanować odpowiednie recenzentów i przejrzeć cykle, które spełniają wymagania zasad i zgodności. Jeśli cykle przeglądu są zbyt częste lub jest zbyt mało recenzentów, jakość może zostać utracona, a zbyt wiele lub zbyt mało osób może uzyskać dostęp. 

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja jest niezwykle ważna dla sukcesu każdego nowego procesu biznesowego. Aktywnie Komunikuj się z użytkownikami, jak i kiedy ich środowisko zostanie zmienione i jak uzyskać pomoc techniczną, jeśli wystąpią problemy. 

#### <a name="communicate-changes-in-accountability"></a>Powiadamiaj o zmianach w odpowiedzialności

Przeglądy dostępu obsługują przenoszony zakres odpowiedzialności za przeglądanie i działanie w przypadku ciągłego dostępu do właścicieli firmy. Podejmowanie decyzji o oddzieleniu dostępu od stacji dysków IT bardziej precyzyjne decyzje dotyczące dostępu. Jest to kulturowe zmiany odpowiedzialności i odpowiedzialności właściciela zasobu. Proaktywnie Komunikuj się z tą zmianą i zapewnij, że właściciele zasobów są przeszkoleni i mogą korzystać z szczegółowych informacji w celu podejmowania właściwych decyzji.

Jasno, będzie ona mieć kontrolę nad wszystkimi decyzjami dotyczącymi dostępu do infrastruktury i przypisaniami ról uprzywilejowanych. 

#### <a name="customize-email-communication"></a>Dostosowywanie komunikacji e-mail

Po zaplanowaniu przeglądu należy określić użytkowników, którzy będą przeprowadzali ten przegląd. Ci recenzenci otrzymają wiadomość e-mail z powiadomieniem o nowych przypisanych do nich recenzjach, a także przypomnieniami przed wygaśnięciem przypisanej do nich recenzji.

Administratorzy mogą zdecydować się na wysłanie powiadomienia w połowie drogi przed wygaśnięciem lub upływem dnia przed jego wygaśnięciem. 

Wiadomość e-mail wysłana do recenzentów można dostosować w taki sposób, aby zawierała niestandardowy krótki komunikat, który zachęca je do działania na przegląd. Zalecamy użycie dodatkowego tekstu do:

* Dołącz osobistą wiadomość do recenzentów, aby zrozumieć, że są one wysyłane przez dział IT lub działu IT.

* Dołącz hiperłącze lub odwołanie do wewnętrznych informacji na temat oczekiwań przeglądu oraz dodatkowych materiałów referencyjnych lub szkoleniowych.

* Dołącz link do instrukcji dotyczących przeprowadzania samodzielnej [oceny dostępu.](review-your-access.md) 

  ![Adres e-mail recenzenta](./media/deploy-access-review/2-plan-reviewer-email.png)

Po wybraniu pozycji Rozpocznij przegląd Recenzenci będą kierowani do [portalu dostępu](https://myapplications.microsoft.com/) w celu uzyskiwania dostępu do grup i aplikacji. Portal udostępnia im przegląd wszystkich użytkowników, którzy mają dostęp do przeglądanego zasobu, oraz Zalecenia systemu na podstawie ostatniego logowania i informacji o dostępie.

### <a name="plan-a-pilot"></a>Planowanie pilotażu

Zachęcamy klientów do wstępnego przeglądu dostępu pilotażowego z małą grupą i ukierunkowanych zasobów niekrytycznych. Pilotażowe informacje mogą pomóc w dostosowaniu procesów i komunikacji zgodnie z potrzebami oraz zwiększyć możliwości użytkowników i recenzentów w celu spełnienia wymagań dotyczących zabezpieczeń i zgodności.

W programie pilotażowym zalecamy:

* Zacznij od przeglądów, w których wyniki nie są automatycznie stosowane, i możesz kontrolować implikacje.

* Upewnij się, że wszyscy użytkownicy mają prawidłowe adresy e-mail na liście w usłudze Azure AD i otrzymują do nich komunikację e-mail w celu podjęcia odpowiedniej akcji. 

* Udokumentowanie dowolnego dostępu usuniętego jako część pilotażu w przypadku konieczności szybkiego przywrócenia go.

* Monitoruj dzienniki inspekcji, aby upewnić się, że wszystkie zdarzenia są prawidłowo poddawane inspekcji.

Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dla pilotażu](../fundamentals/active-directory-deployment-plans.md).

## <a name="introduction-to-access-reviews"></a>Wprowadzenie do przeglądów dostępu

Ta sekcja zawiera informacje o pojęciach dotyczących przeglądu dostępu, które należy znać przed rozpoczęciem planowania recenzji.

### <a name="what-resource-types-can-be-reviewed"></a>Jakie typy zasobów można sprawdzić?

Po zintegrowaniu zasobów organizacji z usługą Azure AD (na przykład użytkownikami, aplikacjami i grupami) można je zarządzać i przeglądać. 

Typowe elementy docelowe do przeglądu obejmują:

* [Aplikacje zintegrowane z usługą Azure AD na potrzeby logowania jednokrotnego](../manage-apps/what-is-application-management.md) (na przykład SaaS).

* [Członkostwo](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) w grupach (synchronizowane z usługą Azure AD lub utworzone w usłudze Azure ad lub Microsoft 365, w tym Microsoft Teams).

* [Pakiet dostępu](./entitlement-management-overview.md) , który grupuje zasoby (grupy, aplikacje i lokacje) w jednym pakiecie w celu zarządzania dostępem.

* [Role usługi Azure AD i role zasobów platformy Azure](../privileged-identity-management/pim-resource-roles-assign-roles.md) zgodnie z definicją w Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Kto będzie tworzyć przeglądy dostępu i zarządzać nimi?

Rola administracyjna wymagana do tworzenia i odczytywania przeglądu dostępu oraz zarządzania nim zależy od typu przeglądanego zasobu. W poniższej tabeli przedstawiono role wymagane dla każdego typu zasobu:

| Typ zasobu| Tworzenie przeglądów dostępu i zarządzanie nimi (twórcy)| Wyniki przeglądu dostępu do odczytu |
| - | - | -|
| Grupa lub aplikacja| Administrator globalny <p>Administrator użytkowników| Twórcy i administrator zabezpieczeń |
| Role uprzywilejowane w usłudze Azure AD| Administrator globalny <p>Administrator ról uprzywilejowanych| twórcy <p>Czytelnik zabezpieczeń<p>Administrator zabezpieczeń |
| Role uprzywilejowane na platformie Azure (zasoby)| Administrator globalny<p>Administrator użytkowników<p>Właściciel zasobu| twórcy |
| Pakiet dostępu| Administrator globalny<p>Twórca pakietu dostępu| Tylko administrator globalny |


Aby uzyskać więcej informacji, zobacz [uprawnienia roli administrator w Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Kto będzie przeglądać dostęp do zasobu?

Twórca przeglądu dostępu decyduje się w momencie utworzenia, który przeprowadzi przegląd. Nie można zmienić tego ustawienia po rozpoczęciu przeglądu. Recenzenci są reprezentowani przez trzy osób:

* Właściciele zasobów, którzy są właścicielami tego zasobu.

* Zestaw indywidualnie wybranych delegatów wybrany przez administratora przeglądów dostępu.

* Użytkownicy końcowi, którzy każdy z nich zaświadczą w celu uzyskania ciągłego dostępu.

Podczas tworzenia przeglądu dostępu Administratorzy mogą wybrać co najmniej jednego recenzenta. Wszyscy recenzenci mogą rozpocząć i przeprowadzić przegląd, wybierając użytkowników w celu uzyskania dostępu do zasobu lub usunięcia ich. 

### <a name="components-of-an-access-review"></a>Składniki przeglądu dostępu

Przed wdrożeniem przeglądów dostępu należy zaplanować typy przeglądów odpowiednie dla Twojej organizacji. Aby to zrobić, należy podjąć decyzje biznesowe na temat tego, co chcesz przejrzeć, oraz działań, które należy podjąć w oparciu o te przeglądy.

Aby utworzyć zasady przeglądu dostępu, musisz dysponować następującymi informacjami.

* Co to są zasoby do przejrzenia?

* Którego dostępu jest sprawdzany.

* Jak często ma się odbywać przegląd?

* Kto będzie wykonywał przegląd?

   * Jak będą one powiadamiane o przejrzeniu?

   * Jakie są osie czasu, które mają być wymuszane na potrzeby recenzji?

* Jakie akcje automatyczne powinny być wymuszane na podstawie przeglądu?

   * Co się stanie, jeśli recenzent nie odpowie w czasie?

* Jakie działania ręczne zostaną podjęte w wyniku przeglądu?

* Jaka Komunikacja powinna być wysyłana na podstawie podjętych akcji?


**Przykładowy plan przeglądu dostępu**

| Składnik| Wartość |
| - | - |
| **Zasoby do przejrzenia**| Dostęp do systemu Microsoft Dynamics |
| **Częstotliwość przeglądu**| Miesięcznie |
| **Kto wykonuje przegląd**| Menedżerowie programów grupy Dynamics Business |
| **Powiadomienie**| Wyślij wiadomość e-mail na 24 godziny przed rozpoczęciem przeglądu aliasu Dynamics-Pms<p>Dołącz do recenzentów niestandardową wiadomość, aby zabezpieczyć swój zakup |
| **Oś czasu**| 48 godzin od powiadomienia |
|**Akcje automatyczne**| Usuń dostęp z dowolnego konta, które nie ma interakcyjnego logowania w ciągu 90 dni, usuwając użytkownika z grupy zabezpieczeń Dynamics. <p>*Wykonaj akcje, jeśli nie zostały zrecenzowane na osi czasu.* |
| **Akcje ręczne**| Recenzenci mogą wykonać zatwierdzenie usunięcia przed automatycznym akcją w razie potrzeby. |
| **Wiadomości**| Wyślij użytkowników wewnętrznych (członków), którzy zostali usunięci w wiadomości e-mail z informacją, że są usunięci i jak odzyskać dostęp. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatyzowanie akcji opartych na przeglądach dostępu

Można wybrać opcję automatycznego usuwania dostępu przez ustawienie opcji automatycznie Zastosuj do zasobu, aby włączyć.

  ![Planowanie przeglądów dostępu](./media/deploy-access-review/3-automate-actions-settings.png)

Po zakończeniu przeglądu i zakończeniu jego pracy użytkownicy, którzy nie zostali zatwierdzeni przez recenzenta, zostaną automatycznie usunięci z zasobu lub utrzymani z ciągłym dostępem. Może to oznaczać usunięcie członkostwa w grupie, ich przypisanie aplikacji lub odwołanie ich bezpośrednio w celu podniesienia uprawnień do roli uprzywilejowanej.

Zapoznaj się z zaleceniami

Zalecenia są wyświetlane recenzentom w ramach środowiska recenzenta i wskazują Ostatnie Logowanie osoby do dzierżawy lub ostatni dostęp do aplikacji. Te informacje ułatwiają recenzentom podejmowanie właściwych decyzji dotyczących dostępu. Wybranie pozycji Wypełnij zalecenia spowoduje przestrzeganie zaleceń dotyczących przeglądu dostępu. Po zakończeniu przeglądu dostępu system zastosuje te zalecenia automatycznie dla użytkowników, dla których recenzenci nie odpowiedzieli.

Zalecenia są zależne od kryteriów w przeglądzie dostępu. Na przykład jeśli skonfigurujesz przegląd w celu usunięcia dostępu bez interakcyjnego logowania przez 90 dni, zalecamy usunięcie wszystkich użytkowników spełniających te kryteria. Firma Microsoft stale pracuje nad ulepszaniem zaleceń. 

### <a name="review-guest-user-access"></a>Przeglądanie dostępu użytkowników-Gości

Za pomocą przeglądów dostępu można przeglądać i czyścić tożsamości partnerów współpracy z organizacji zewnętrznych. Konfiguracja przeglądu dla partnerów może spełniać wymagania dotyczące zgodności.

Tożsamościom zewnętrznym można udzielić dostępu do zasobów firmy za pomocą jednej z następujących akcji:

* Dodano do grupy 

* Zaproszeni do zespołów 

* Przypisane do aplikacji dla przedsiębiorstw lub pakietu dostępu

* Przypisano rolę uprzywilejowaną w usłudze Azure AD lub w ramach subskrypcji platformy Azure

Zobacz [przykładowy skrypt](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Skrypt będzie przedstawiał użycie tożsamości zewnętrznych zaproszonych do dzierżawy. W usłudze Azure AD można zobaczyć członkostwo w grupach użytkowników zewnętrznych, przypisania ról i przypisania aplikacji. Skrypt nie będzie zawierać żadnych przypisań poza usługą Azure AD, na przykład bezpośrednie przypisanie praw do zasobów programu SharePoint bez użycia grup.

Podczas tworzenia przeglądu dostępu dla grup lub aplikacji można wybrać opcję zezwalania Fokusowi recenzenta na wszystkich użytkowników z dostępem lub tylko Gości. Wybierając pozycję tylko użytkownicy-Goście, recenzenci otrzymują skoncentrowaną listę tożsamości zewnętrznych z usługi Azure AD B2B, która ma dostęp do zasobu.

 ![Przeglądanie użytkowników-Gości](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> NIE będzie to obejmować zewnętrznych elementów członkowskich, którzy mają element UserType elementu członkowskiego. Nie obejmuje to również użytkowników zaproszonych poza środowiskiem współpracy B2B usługi Azure AD, na przykład tych, którzy mają dostęp do zawartości udostępnionej bezpośrednio za pomocą programu SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Planowanie przeglądów dostępu dla pakietów dostępu

[Pakiety dostępu](entitlement-management-overview.md) mogą znacznie uprościć strategię przeglądu i kontroli dostępu. Pakiet dostępu to pakiet wszystkich zasobów z dostępem, które użytkownik musi pracować nad projektem lub wykonać ich zadanie. Na przykład możesz chcieć utworzyć pakiet dostępu zawierający wszystkie aplikacje, które są wymagane przez deweloperów w organizacji, lub wszystkie aplikacje, do których mają dostęp użytkownicy zewnętrzni. Administrator lub Menedżer pakietów dostępu delegowanego następnie grupuje zasoby (grupy lub aplikacje) i role, których użytkownicy potrzebują dla tych zasobów.

Podczas [tworzenia pakietu dostępu](entitlement-management-access-package-create.md)można utworzyć co najmniej jedną zasadę dostępu, która określa warunki, dla których użytkownicy mogą zażądać pakietu dostępu, jak wygląda proces zatwierdzania i jak często osoba musiałaby ponownie zażądać dostępu. Przeglądy dostępu są konfigurowane podczas tworzenia lub edytowania zasad pakietu dostępu.

Otwórz kartę cykl życia, aby przewinąć w dół do okna przeglądy dostępu.

 ![Zrzut ekranu, który pokazuje "Edytuj zasady" na karcie "cykl życia".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Planowanie przeglądów dostępu dla grup

Oprócz pakietów dostępu przeglądanie członkostwa w grupie jest najbardziej skutecznym sposobem na dostęp. Zaleca się, aby dostęp do zasobów był przypisany za pośrednictwem [grup zabezpieczeń lub grup Microsoft 365](../fundamentals/active-directory-manage-groups.md), a użytkownicy są dodawani do tych grup w celu uzyskania dostępu.

Pojedynczej grupie można udzielić dostępu do wszystkich odpowiednich zasobów. Dostęp do grupy można przypisać do poszczególnych zasobów lub do pakietu dostępu, który grupuje aplikacje i inne zasoby. Za pomocą tej metody można przeglądać dostęp do grupy, a nie dostęp do poszczególnych aplikacji. 

Członkostwo w grupie może być analizowane przez: 

* Administratorzy

* Właściciele grupy

* Wybrani użytkownicy, możliwość recenzji delegowanej podczas tworzenia przeglądu

* Członkowie grupy, zaświadczanie dla siebie

### <a name="group-ownership"></a>Własność grupy

Zaleca się, aby właściciele grup przeglądali członkostwo, ponieważ są one najlepszym rozwiązaniem, aby wiedzieć, kto potrzebuje dostępu. Własność grup różni się od typu grupy:

Grupy, które są tworzone w Microsoft 365 i usługa Azure AD, mają co najmniej jednego dobrze zdefiniowanego właściciela. W większości przypadków tacy właściciele tworzą idealnych recenzentów dla własnych grup, ponieważ wiedzą, kto powinien mieć dostęp. 

Na przykład firma Microsoft Teams używa grup Microsoft 365 jako podstawowego modelu autoryzacji, aby udzielić użytkownikom dostępu do zasobów w programie SharePoint, programie Exchange, programie OneNote lub w innych usługach Microsoft 365. Twórca zespołu automatycznie otrzymuje właściciela i powinien być odpowiedzialny za zaświadczenie członkostwa w tej grupie. 

Grupy utworzone ręcznie w portalu usługi Azure AD lub za pośrednictwem skryptów za pośrednictwem Microsoft Graph mogą nie mieć zdefiniowanych właścicieli. Zalecamy Definiowanie ich za pośrednictwem portalu usługi Azure AD w sekcji "właściciele" grupy lub za pośrednictwem grafu.

Grupy, które są synchronizowane z Active Directory lokalnych, nie mogą mieć właściciela w usłudze Azure AD. Podczas tworzenia dla nich przeglądu dostępu należy wybrać osoby, które najlepiej nadają się do podejmowania decyzji dotyczących członkostwa w nich.

> [!NOTE]
> Zalecamy zdefiniowanie zasad biznesowych, które definiują sposób tworzenia grup, aby zapewnić przejrzyste własności grup i odpowiedzialność za regularne przeglądy członkostwa. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Przeglądanie członkostwa grup wykluczeń w zasadach dostępu warunkowego 

Istnieją przypadki, w których zasady dostępu warunkowego mające na celu zapewnienie bezpieczeństwa sieci nie powinny mieć zastosowania do wszystkich użytkowników. Na przykład zasady dostępu warunkowego, które umożliwiają użytkownikom logowanie się tylko w sieci firmowej, mogą nie dotyczyć zespołu sprzedaży, który jest szeroko przenoszone. W takim przypadku członkowie zespołu sprzedaży zostaną przeniesieni do grupy, a grupa ta zostanie wykluczona z zasad dostępu warunkowego. 

Należy regularnie przeglądać takie członkostwo w grupach, ponieważ wykluczenie reprezentuje potencjalne ryzyko, jeśli złe elementy członkowskie zostały wykluczone z wymogu.

Za [pomocą przeglądów dostępu usługi Azure AD można zarządzać użytkownikami wykluczonymi z zasad dostępu warunkowego](conditional-access-exclusion.md).

### <a name="review-external-users-group-memberships"></a>Przejrzyj członkostwa w grupach użytkowników zewnętrznych

Aby zminimalizować liczbę czynności ręcznych i związanych z nimi potencjalnych błędów, należy rozważyć użycie [grup dynamicznych](../enterprise-users/groups-create-rule.md) do przypisywania członkostwa w grupach na podstawie atrybutów użytkownika. Można utworzyć co najmniej jedną grupę dynamiczną dla użytkowników zewnętrznych. Wewnętrzny sponsor może działać jako recenzent dla członkostwa w grupie. 

Uwaga: użytkownicy zewnętrzni usunięci z grupy w wyniku przeglądu dostępu nie zostaną usunięci z dzierżawy. 

Można je usunąć z dzierżawy usuniętej ręcznie lub za pomocą skryptu.

### <a name="review-access-to-on-premises-groups"></a>Przeglądanie dostępu do grup lokalnych

Przeglądy dostępu nie mogą zmieniać członkostwa w grupach grup synchronizowanych z lokalizacji lokalnej za pomocą [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Wynika to z faktu, że źródło danych jest w środowisku lokalnym.

Nadal można korzystać z przeglądów dostępu w celu planowania i obsługi regularnych przeglądów grup lokalnych. Następnie recenzenci będą podejmować działania w grupie lokalnej. Ta strategia zachowuje przeglądy dostępu jako narzędzie dla wszystkich przeglądów.

Możesz użyć wyników z przeglądu dostępu do grup lokalnych i przetworzyć je w dalszej postaci:

* Pobieranie raportu CSV z przeglądu dostępu i czynności podejmowanych ręcznie.

* Używanie Microsoft Graph do programistycznego uzyskiwania dostępu do wyników i decyzji w ramach ukończonych przeglądów dostępu.

Aby na przykład uzyskać dostęp do wyników dla grupy zarządzanej przez usługi Windows AD, użyj tego [skryptu przykładowego programu PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Skrypt zawiera opis wymaganych wywołań wykresów i eksportuje polecenia AD-PowerShell systemu Windows w celu przeprowadzenia zmian.

## <a name="plan-access-reviews-for-applications"></a>Planowanie przeglądów dostępu dla aplikacji 

Gdy przeglądasz dostęp do aplikacji, przeglądasz dostęp dla pracowników i tożsamości zewnętrznych do informacji i danych w aplikacji. Wybierz, aby przejrzeć aplikację, jeśli chcesz wiedzieć, kto ma dostęp do określonej aplikacji, a nie pakietu dostępu lub grupy. 

Zalecamy planowanie przeglądów aplikacji w następujących scenariuszach:

* Użytkownicy uzyskują bezpośredni dostęp do aplikacji (poza grupą lub pakietem dostępu).

* Aplikacja uwidacznia krytyczne lub poufne informacje.

* Aplikacja ma określone wymagania w zakresie zgodności, do których należy zaświadczać.

* Podejrzewasz niewłaściwy dostęp.

Aby utworzyć przeglądy dostępu dla aplikacji, należy ustawić wymagane przypisanie użytkownika? Właściwość na wartość tak. W przypadku wybrania opcji nie wszyscy użytkownicy w katalogu, w tym tożsamości zewnętrzne, mogą uzyskiwać dostęp do aplikacji i nie można przeglądać dostępu do aplikacji. 

 ![Planowanie przypisań aplikacji](./media/deploy-access-review/6-plan-applications-assignment-required.png)

Następnie należy [przypisać użytkowników i grupy](../manage-apps/assign-user-or-group-access-portal.md) , do których chcesz uzyskać dostęp. 

### <a name="reviewers-for-an-application"></a>Recenzenci dla aplikacji

Przeglądy dostępu mogą dotyczyć członków grupy lub użytkowników, którzy zostali przypisani do aplikacji. Aplikacje w usłudze Azure AD nie muszą mieć właściciela, co oznacza, że opcja wyboru właściciela aplikacji jako recenzenta nie jest możliwa. Można dodatkowo przekroczyć zakres przeglądu, aby przejrzeć tylko użytkowników-Gości przypisanych do aplikacji, a nie sprawdzać całego dostępu.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Planowanie przeglądu usługi Azure AD i ról zasobów platformy Azure

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) upraszcza zarządzanie dostępem uprzywilejowanym do zasobów w usłudze Azure AD. Dzięki temu można zachować listę ról uprzywilejowanych, zarówno w [usłudze Azure AD](../roles/permissions-reference.md) , jak i w przypadku [zasobów platformy Azure](../../role-based-access-control/built-in-roles.md) znacznie mniejszych i zwiększyć ogólne zabezpieczenia katalogu.

Przeglądy dostępu umożliwiają recenzentom potwierdzenie, czy użytkownicy nadal muszą znajdować się w roli. Podobnie jak przeglądy dostępu dla pakietów dostępu, przeglądy dla ról usługi Azure AD i zasobów platformy Azure są zintegrowane z użytkownikami administracyjnymi PIM. Zalecamy regularne przeglądanie następujących przypisań ról:

* Administrator globalny

* Administrator użytkowników

* Administrator uprzywilejowanego uwierzytelniania

* Administrator dostępu warunkowego

* Administrator zabezpieczeń

* Wszystkie role administracyjne usług Microsoft 365 i Dynamics

Role wybrane w tym miejscu obejmują stałą i kwalifikującą się rolę. 

W sekcji recenzenci wybierz co najmniej jedną osobę, aby przejrzeć wszystkich użytkowników. Możesz również wybrać, aby członkowie mogli przeglądać swój własny dostęp.

 ![Edycja zasad](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Wdróż przeglądy dostępu

Po przygotowaniu strategii i planowaniu dostępu do zasobów zintegrowanych z usługą Azure AD należy wdrożyć przeglądy i zarządzać nimi, korzystając z poniższych zasobów.

### <a name="review-access-packages"></a>Przejrzyj pakiety dostępu

Aby zmniejszyć ryzyko nieaktualnego dostępu, Administratorzy mogą włączyć okresowe przeglądy użytkowników, którzy mają aktywne przypisania do pakietu dostępu. Postępuj zgodnie z instrukcjami podanymi w poniższym linku:

| Instrukcje| Opis |
| - | - |
| [Tworzenie przeglądów dostępu](entitlement-management-access-reviews-create.md)| Włącz przeglądy pakietu dostępu. |
| [Wykonaj przeglądy dostępu](entitlement-management-access-reviews-review-access.md)| Wykonaj przeglądy dostępu dla innych użytkowników przypisanych do pakietu dostępu. |
| [Samodzielne przeglądanie przypisanych pakietów dostępu](entitlement-management-access-reviews-self-review.md)| Samodzielna weryfikacja przypisanych pakietów dostępu |


> [!NOTE]
> Użytkownicy końcowi, którzy dokonają samooceny i mówią, że nie potrzebują już dostępu, nie są natychmiast usuwani z pakietu dostępu. Są one usuwane z pakietu dostępu po zakończeniu przeglądu lub jeśli administrator zatrzyma przegląd.

### <a name="review-groups-and-apps"></a>Przeglądanie grup i aplikacji

Dostęp do grup i aplikacji musi być zmieniany w miarę upływu czasu. Aby zmniejszyć ryzyko związane ze starymi przypisaniami dostępu, Administratorzy mogą tworzyć przeglądy dostępu dla członków grupy lub dostępu do aplikacji. Postępuj zgodnie z instrukcjami podanymi w poniższym linku:

| Instrukcje| Opis |
| - | - |
| [Tworzenie przeglądów dostępu](create-access-review.md)| Utwórz co najmniej jedną weryfikację dostępu dla członków grupy lub dostępu do aplikacji. |
| [Wykonaj przeglądy dostępu](perform-access-review.md)| Przeprowadź przegląd dostępu dla członków grupy lub użytkowników mających dostęp do aplikacji. |
| [Samoobsługowe przeglądanie dostępu](review-your-access.md)| Członkowie przeglądają własny dostęp do grupy lub aplikacji |
| [Ukończ przegląd dostępu](complete-access-review.md)| Wyświetl przegląd dostępu i Zastosuj wyniki |
| [Podejmowanie akcji dla grup lokalnych](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Przykładowy skrypt programu PowerShell do działania na przeglądach dostępu dla grup lokalnych. |


### <a name="review-azure-ad-roles"></a>Przeglądanie ról usługi Azure AD

Aby zmniejszyć ryzyko związane ze starymi przypisaniami ról, należy regularnie przeglądać dostęp do ról uprzywilejowanych usługi Azure AD.

![Zrzut ekranu przedstawiający listę "przegląd członkostwa" ról Azure A D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Postępuj zgodnie z instrukcjami pod poniższymi linkami:

| Instrukcje | Opis |
| - | - |
 [Tworzenie przeglądów dostępu](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Tworzenie przeglądów dostępu dla uprzywilejowanych ról usługi Azure AD w usłudze PIM |
| [Samoobsługowe przeglądanie dostępu](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Jeśli masz przypisaną rolę administracyjną, Zatwierdź lub Odmów dostępu do roli |
| [Ukończ przegląd dostępu](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wyświetl przegląd dostępu i Zastosuj wyniki |


### <a name="review-azure-resource-roles"></a>Przeglądanie ról zasobów platformy Azure

Aby zmniejszyć ryzyko związane ze starymi przypisaniami ról, należy regularnie przeglądać dostęp do ról uprzywilejowanych zasobów platformy Azure. 

![przeglądanie ról usługi Azure AD](./media/deploy-access-review/9-review-azure-roles-picker.png)

Postępuj zgodnie z instrukcjami pod poniższymi linkami:

| Instrukcje| Opis |
| - | -|
| [Tworzenie przeglądów dostępu](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Tworzenie przeglądów dostępu dla uprzywilejowanych ról zasobów platformy Azure w usłudze PIM |
| [Samoobsługowe przeglądanie dostępu](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Jeśli masz przypisaną rolę administracyjną, Zatwierdź lub Odmów dostępu do roli |
| [Ukończ przegląd dostępu](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Wyświetl przegląd dostępu i Zastosuj wyniki |


## <a name="use-the-access-reviews-api"></a>Korzystanie z interfejsu API przeglądów dostępu

Zobacz [metody interfejsu API programu Graph](/graph/api/resources/accessreviews-root?view=graph-rest-beta) oraz [uprawnienia roli i aplikacji sprawdzanie autoryzacji](/graph/api/resources/accessreviews-root?view=graph-rest-beta) w celu współpracy z zasobami do przeglądania i zarządzania nimi. Metody przeglądy dostępu w interfejsie API Microsoft Graph są dostępne dla kontekstów aplikacji i użytkowników. Podczas uruchamiania skryptów w kontekście aplikacji, konto używane do uruchamiania interfejsu API (zasady usługi) musi mieć przyznane uprawnienie "AccessReview. Read. All", aby wykonywać zapytania dotyczące informacji o przeglądach dostępu.

Popularne zadania przeglądów dostępu do automatyzowania przy użyciu interfejs API programu Graph do przeglądów dostępu są następujące:

* Utwórz i uruchom przegląd dostępu

* Ręcznie Zakończ przegląd dostępu przed zaplanowanym końcem

* Wyświetl wszystkie uruchomione przeglądy dostępu i ich stan

* Zapoznaj się z historią serii przeglądu oraz podejmowanych decyzji i działań.

* Zbierz decyzje z przeglądu dostępu

* Zbieraj decyzje od zakończonych przeglądów, w których recenzent podjął inną decyzję niż zalecany system.

Podczas tworzenia nowych zapytań interfejs API programu Graph dla usługi Automation zalecamy korzystanie z [Eksploratora grafów](https://developer.microsoft.com/en-us/graph/graph-explorer). Możesz tworzyć i eksplorować zapytania grafu przed umieszczeniem ich w skryptach i kodzie. Może to pomóc szybko wykonać iterację zapytania, aby uzyskać dokładne wyniki wyszukiwania, bez zmiany kodu skryptu.

## <a name="monitor-access-reviews"></a>Monitoruj przeglądy dostępu

Działania przeglądów dostępu są rejestrowane i dostępne w [dziennikach inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md). Dane inspekcji można filtrować według kategorii, typu działania i zakresu dat. Oto przykładowe zapytanie:

| Kategoria| Zasady |
| - | - |
| Typ działania| Tworzenie przeglądu dostępu |
| | Aktualizuj przegląd dostępu |
| | Zakończono przegląd dostępu |
| | Usuwanie przeglądu dostępu |
| | Zatwierdzanie decyzji |
| | Odmów decyzji |
| | Resetowanie decyzji |
| | Zastosuj decyzję |
| Zakres dat| siedem dni |


Aby uzyskać bardziej zaawansowane zapytania i analizę kontroli dostępu, a także śledzić zmiany i dokonywać przeglądów, zalecamy wyeksportowanie dzienników inspekcji usługi Azure AD do [usługi azure log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) lub centrum zdarzeń platformy Azure. W przypadku przechowywania danych w usłudze Azure Log Analytics można użyć [zaawansowanego języka analitycznego](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) — i utworzyć własne pulpity nawigacyjne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat pokrewnych technologii.

* [Co to jest zarządzanie prawami w usłudze Azure AD?](entitlement-management-overview.md)

* [Co to jest usługa Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)