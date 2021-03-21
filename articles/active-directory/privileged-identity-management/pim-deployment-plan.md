---
title: Wdrażanie Privileged Identity Management (PIM) — Azure AD | Microsoft Docs
description: Opisuje sposób planowania wdrożenia programu Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036830"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Wdróż Azure AD Privileged Identity Management (PIM)

Ten artykuł zawiera przewodnik krok po kroku opisujący sposób planowania wdrożenia programu Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD). Użytkownik będzie ponownie przypisywać użytkowników w rolach z wysokim poziomem uprawnień do mniej zaawansowanych ról wbudowanych lub niestandardowych, a jeśli to możliwe, i planować przypisań ról just-in-Time dla najbardziej uprzywilejowanych ról. W tym artykule opisano zalecenia dotyczące planowania i wdrażania wdrożenia.

> [!TIP]
> W tym artykule zobaczysz elementy oznaczone jako:
>
> : heavy_check_mark: **firma Microsoft zaleca**
>
> Są to ogólne zalecenia i należy je zaimplementować tylko wtedy, gdy mają zastosowanie do konkretnych potrzeb przedsiębiorstwa.

## <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Aby można było korzystać z Privileged Identity Management, katalog musi mieć jedną z następujących licencji płatnych lub wersji próbnej. Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do używania Privileged Identity Management](subscription-requirements.md).

- Usługa Azure AD — wersja Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Jak działa PIM

Ta sekcja zawiera przegląd dotyczący planowania odpowiednich części procesu Privileged Identity Management. Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](pim-configure.md)

1. Zacznij korzystać z Privileged Identity Management, aby użytkownicy mieli uprawnienia do ról uprzywilejowanych.
1. Gdy uprawniony użytkownik musi korzystać z roli uprzywilejowanej, uaktywnia rolę przy użyciu Privileged Identity Management.
1. Użytkownik może być wymagany w ustawieniach, aby:

    - Korzystanie z uwierzytelniania wieloskładnikowego
    - Żądaj zatwierdzenia dla aktywacji
    - Podaj przyczynę biznesową dla aktywacji

1. Gdy użytkownik pomyślnie aktywuje swoją rolę, będzie mieć uprawnienia roli do określonego czasu trwania.
1. Administratorzy mogą wyświetlić historię wszystkich działań Privileged Identity Management w dzienniku inspekcji. Mogą również dodatkowo zabezpieczyć swoje organizacje usługi Azure AD i spełnić wymagania przy użyciu funkcji Privileged Identity Management, takich jak przeglądy i alerty dostępu.

## <a name="roles-that-can-be-managed-by-pim"></a>Role, które mogą być zarządzane przez usługę PIM

Wszystkie **role usługi Azure AD** znajdują się w Azure Active Directory (na przykład administrator globalny, administrator programu Exchange i administrator zabezpieczeń). Więcej informacji na temat ról i ich funkcji można znaleźć w temacie [uprawnienia roli administrator w Azure Active Directory](../roles/permissions-reference.md). Aby uzyskać pomoc dotyczącą określania ról do przypisania administratorów, zobacz [najmniej uprzywilejowanych ról według zadania](../roles/delegate-by-task.md).

**Role platformy Azure** to role połączone z zasobem platformy Azure, grupą zasobów, subskrypcją lub grupą zarządzania. Możesz użyć PIM, aby zapewnić dostęp just in Time do wbudowanych ról platformy Azure, takich jak właściciel, administrator dostępu użytkowników i współautor, a także do [ról niestandardowych](../../role-based-access-control/custom-roles.md). Aby uzyskać więcej informacji na temat ról platformy Azure, zobacz [Kontrola dostępu oparta na rolach na platformie Azure](../../role-based-access-control/overview.md).

Aby uzyskać więcej informacji, zobacz [role, którymi nie można zarządzać w Privileged Identity Management](pim-roles.md).

## <a name="deployment-plan"></a>Plan wdrożenia

Przed wdrożeniem Privileged Identity Management w organizacji postępuj zgodnie z instrukcjami i zapoznaj się z pojęciami w tej sekcji, aby ułatwić utworzenie planu dopasowanego do wymagań dotyczących tożsamości uprzywilejowanych w organizacji.

### <a name="identify-your-stakeholders"></a>Zidentyfikuj swoje osoby zainteresowane

W poniższej sekcji znajdują się informacje ułatwiające zidentyfikowanie wszystkich zainteresowanych uczestników projektu i konieczność wylogowania się, przejrzenia lub poinformowanie. Zawiera osobne tabele umożliwiające wdrażanie usług PIM dla ról usługi Azure AD i PIM dla ról platformy Azure. Dodaj uczestników projektu do poniższej tabeli, zgodnie z potrzebami organizacji.

- SO = Wyloguj się w tym projekcie
- R = przejrzyj ten projekt i podaj dane wejściowe
- I = poinformowany o tym projekcie

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Uczestnicy projektu: Privileged Identity Management dla ról usługi Azure AD

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Nazwa i adres e-mail | **Architekt tożsamości lub Administrator globalny platformy Azure**<br/>Przedstawiciel zespołu ds. zarządzania tożsamościami odpowiedzialny za definiowanie sposobu dostosowania tej zmiany do podstawowej infrastruktury zarządzania tożsamościami w organizacji. | SO/R/I |
| Nazwa i adres e-mail | **Menedżer linii/właściciela usługi**<br/>Przedstawiciel od właścicieli IT usługi lub grupy usług. Są one kluczowym sposobem podejmowania decyzji i ułatwiania Privileged Identity Management dla zespołu. | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się zalogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może przekazać opinię na temat możliwości tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli uprzywilejowanej**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Muszą wiedzieć, jak aktywować swoje role po zaimplementowaniu Privileged Identity Management. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Uczestnicy projektu: Privileged Identity Management ról platformy Azure

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Nazwa i adres e-mail | **Właściciel subskrypcji/zasobu**<br/>Przedstawiciel z właścicieli IT dla każdej subskrypcji lub zasobu, który ma zostać wdrożony Privileged Identity Management | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może przekazać opinię na temat możliwości tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli platformy Azure**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Muszą wiedzieć, jak aktywować swoje role po zaimplementowaniu Privileged Identity Management. | I |

### <a name="start-using-privileged-identity-management"></a>Rozpoczęcie korzystania z usługi Privileged Identity Management

W ramach procesu planowania należy przygotować Privileged Identity Management, wykonując kroki opisane w artykule [Privileged Identity Management](pim-getting-started.md) . Privileged Identity Management zapewnia dostęp do niektórych funkcji, które są zaprojektowane w celu ułatwienia wdrożenia.

Jeśli chcesz wdrożyć Privileged Identity Management dla zasobów platformy Azure, Skorzystaj z naszego [odnajdywania zasobów platformy Azure, aby zarządzać w Privileged Identity Management](pim-resource-roles-discover-resources.md) artykule. Tylko właściciele subskrypcji i grupy zarządzania mogą przenosić te zasoby w ramach zarządzania przez Privileged Identity Management. Po zakończeniu zarządzania funkcje usługi PIM są dostępne dla właścicieli na wszystkich poziomach, w tym grupy zarządzania, subskrypcji, grupy zasobów i zasobu. Jeśli jesteś administratorem globalnym próbującym wdrożyć Privileged Identity Management zasobów platformy Azure, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby zapewnić sobie dostęp do wszystkich zasobów platformy Azure w katalogu do odnajdowania. Jednak przed zarządzaniem swoimi zasobami przy użyciu Privileged Identity Management zalecamy zatwierdzenie od poszczególnych właścicieli subskrypcji.

### <a name="enforce-principle-of-least-privilege"></a>Wymuś zasadę najniższych uprawnień

Ważne jest, aby upewnić się, że zasady najniższych uprawnień w Twojej organizacji zostały wymuszone dla usługi Azure AD i ról platformy Azure.

#### <a name="plan-least-privilege-delegation"></a>Planowanie najniższych uprawnień

W przypadku ról usługi Azure AD często organizacja ma przypisać rolę administratora globalnego do kilku administratorów, gdy większość administratorów potrzebuje tylko jednej lub dwóch określonych i mniej zaawansowanych ról administratora. Dzięki dużej liczbie administratorów globalnych lub innych ról o wysokim poziomie uprawnień trudno jest śledzić przydziały ról uprzywilejowanych wystarczająco blisko.

Wykonaj następujące kroki, aby zaimplementować zasadę najniższych uprawnień dla ról usługi Azure AD.

1. Zapoznaj się z szczegółowością ról, odczytując i opisując dostępne [wbudowane role usługi Azure AD](../roles/permissions-reference.md). Ty i Twój zespół powinien również odwoływać się do [ról administratorów według tożsamości w usłudze Azure AD](../roles/delegate-by-task.md), która objaśnia najmniejszą rolę uprzywilejowaną dla konkretnych zadań.

1. Lista użytkowników, którzy mają role uprzywilejowane w organizacji. Możesz użyć funkcji [odnajdywania Privileged Identity Management i szczegółowych informacji (wersja zapoznawcza)](pim-security-wizard.md) , aby zmniejszyć ryzyko.

    ![Strona odnajdywanie i szczegółowe informacje (wersja zapoznawcza) w celu ograniczenia ekspozycji przez role uprzywilejowane](./media/pim-deployment-plan/new-preview-page.png)

1. Dla wszystkich administratorów globalnych w organizacji należy dowiedzieć się, dlaczego potrzebują roli. Następnie należy je usunąć z roli administratora globalnego i przypisać wbudowane role lub role niestandardowe z niższym uprawnieniem wewnątrz Azure Active Directory. Firma Microsoft obecnie ma tylko 10 administratorów z rolą administratora globalnego. Dowiedz się więcej o tym, [jak firma Microsoft używa Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. W przypadku wszystkich innych ról usługi Azure AD Przejrzyj listę przypisań, zidentyfikuj administratorów, którzy nie potrzebują już roli, i Usuń ich z przypisań.

Aby zautomatyzować ostatnie dwa kroki, możesz użyć przeglądów dostępu w Privileged Identity Management. Postępując zgodnie z instrukcjami w temacie [Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-start-security-review.md), można skonfigurować przegląd dostępu dla każdej roli usługi Azure AD, która ma co najmniej jednego członka.

![Tworzenie okienka przeglądu dostępu dla ról usługi Azure AD](./media/pim-deployment-plan/create-access-review.png)

Ustaw recenzentów na **elementy członkowskie (własne)**. Wszyscy użytkownicy w roli otrzymają wiadomość e-mail z prośbą o potwierdzenie, że potrzebują dostępu. Ponadto należy włączyć opcję **Wymagaj przyczyny przy zatwierdzeniu** w ustawieniach zaawansowanych, aby użytkownicy musieli określić, dlaczego potrzebują roli. Na podstawie tych informacji można usunąć użytkowników z niepotrzebnych ról lub delegować je do bardziej szczegółowych ról administratorów.

Przeglądy dostępu polegają na wiadomościach e-mail powiadamiających użytkowników o konieczności sprawdzenia dostępu do ról. Jeśli masz uprzywilejowane konta, które nie mają połączonych wiadomości e-mail, pamiętaj o wypełnieniu pola pomocniczy adres e-mail na tych kontach. Aby uzyskać więcej informacji, zobacz [proxyAddresses Attribute in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Planowanie delegowania roli zasobów platformy Azure

W przypadku subskrypcji i zasobów platformy Azure można skonfigurować podobny proces przeglądu dostępu, aby przejrzeć role w każdej subskrypcji lub zasobie. Celem tego procesu jest minimalizowanie przypisań administratorów i dostępu użytkowników do poszczególnych subskrypcji lub zasobów oraz usuwanie niepotrzebnych przypisań. Jednak organizacje często delegują te zadania do właściciela każdej subskrypcji lub zasobu, ponieważ mają lepsze zrozumienie konkretnych ról (zwłaszcza ról niestandardowych).

Jeśli jesteś w roli administratora globalnego próbującej wdrożyć usługę PIM dla ról platformy Azure w organizacji, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby uzyskać dostęp do każdej subskrypcji. Następnie można znaleźć wszystkich właścicieli subskrypcji i z nich korzystać, aby usunąć niepotrzebne przypisania i zminimalizować przypisanie roli właściciela.

Użytkownicy z rolą właściciela dla subskrypcji platformy Azure mogą również korzystać z [przeglądów dostępu dla zasobów platformy Azure](pim-resource-roles-start-access-review.md) w celu przeprowadzania inspekcji i usuwania niepotrzebnych przypisań ról podobnych do procesu opisanego wcześniej dla ról usługi Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Zdecyduj, które przypisania ról mają być chronione przez Privileged Identity Management

Po oczyszczeniu przypisań ról uprzywilejowanych w organizacji należy zdecydować, które role mają być chronione za pomocą Privileged Identity Management.

Jeśli rola jest chroniona za pomocą Privileged Identity Management, uprawnieni użytkownicy przypisani do niej muszą podnieść poziom uprawnień, aby korzystać z przywilejów przyznanych przez rolę. Proces podniesienia uprawnień może również obejmować uzyskanie zatwierdzenia przy użyciu uwierzytelniania wieloskładnikowego i zapewnienie przyczyny aktywacji. Privileged Identity Management może również śledzić podniesienia uprawnień za pomocą powiadomień i dzienników zdarzeń Privileged Identity Management i inspekcji usługi Azure AD.

Wybór ról do ochrony za pomocą Privileged Identity Management może być trudny i różny dla każdej organizacji. Ta sekcja zawiera nasze porady dotyczące najlepszych rozwiązań dotyczących usługi Azure AD i ról platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure Active Directory

Ważne jest, aby określić priorytety ochrony ról usługi Azure AD, które mają największe uprawnienia. W oparciu o wzorce użycia między wszystkimi klientami Privileged Identity Management, 10 głównych ról usługi Azure AD zarządzanych przez Privileged Identity Management są następujące:

1. Administrator globalny
1. Administrator zabezpieczeń
1. Administrator użytkowników
1. Administrator programu Exchange
1. Administrator programu SharePoint
1. Administrator usługi Intune
1. Czytelnik zabezpieczeń
1. Administrator usługi
1. Administrator rozliczeń
1. Administrator programu Skype dla firm

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby zarządzać wszystkimi administratorami globalnymi i administratorami zabezpieczeń przy użyciu Privileged Identity Management jako pierwszy krok, ponieważ są to użytkownicy, którzy mogą w przypadku naruszenia zabezpieczeń korzystać z najbardziej szkodliwego oprogramowania.

Ważne jest, aby wziąć pod uwagę, jakie dane i uprawnienia są najbardziej poufne dla Twojej organizacji. Przykładowo niektóre organizacje mogą chcieć chronić swoją rolę administratora Power BI lub rolę administratora zespołów przy użyciu Privileged Identity Management, ponieważ mogą uzyskiwać dostęp do danych i zmieniać podstawowe przepływy pracy.

Jeśli istnieją jakieś role z przypisanymi użytkownikami gościa, są one podatne na ataki.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** zarządzanie wszystkimi rolami z użytkownikami-gośćmi przy użyciu Privileged Identity Management, aby zmniejszyć ryzyko związane z kontami użytkowników-Gości z naruszeniem zabezpieczeń.

Role czytelnika, takie jak czytnik katalogów, czytnik centrum komunikatów i czytelnik zabezpieczeń, są czasami uważane za mniej ważne niż inne role, ponieważ nie mają uprawnień do zapisu. Jednak niektórzy klienci mają również ochronę tych ról, ponieważ osoby atakujące mające dostęp do tych kont mogą odczytywać dane poufne, takie jak dane osobowe. Weź pod uwagę to ryzyko podczas decydowania, czy chcesz, aby role czytnika w organizacji były zarządzane przy użyciu Privileged Identity Management.

#### <a name="azure-roles"></a>Role platformy Azure

Podczas wybierania przypisań ról, które mają być zarządzane za pomocą Privileged Identity Management dla zasobu platformy Azure, należy najpierw zidentyfikować subskrypcje/zasoby, które są najważniejsze dla organizacji. Przykładami takich subskrypcji/zasobów są:

- Zasoby, które obsługują najbardziej poufne dane
- Zasoby, od których zależą podstawowe aplikacje dla klientów

Jeśli jesteś administratorem globalnym, który ma problemy z wybraniem najważniejszych subskrypcji i zasobów, należy skontaktować się z właścicielami subskrypcji w organizacji w celu zebrania listy zasobów zarządzanych przez poszczególne subskrypcje. Następnie należy skontaktować się z właścicielami subskrypcji w celu pogrupowania zasobów na podstawie poziomu ważności w przypadku, gdy są one naruszone (niski, średni, wysoki). Określ priorytety zarządzania zasobami przy użyciu Privileged Identity Management na podstawie tego poziomu ważności.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** współpracę z subskrypcjami/właścicielami zasobów krytycznych usług w celu skonfigurowania Privileged Identity Management przepływu pracy dla wszystkich ról w poufnych subskrypcjach/zasobach.

Privileged Identity Management dla zasobów platformy Azure obsługuje konta usług powiązane z czasem. Konta usług należy traktować dokładnie tak samo, jak w przypadku traktowania zwykłego konta użytkownika.

W przypadku subskrypcji/zasobów, które nie są uznawane za krytyczne, nie trzeba konfigurować Privileged Identity Management dla wszystkich ról. Należy jednak nadal chronić rolę administratora dostępu właściciela i użytkownika przy użyciu Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** Zarządzanie rolami właściciela i rolami administratora dostępu użytkowników wszystkich subskrypcji/zasobów przy użyciu Privileged Identity Management.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Zdecyduj, czy do przypisywania ról ma być używana grupa

Określa, czy przypisać rolę do grupy, a nie do poszczególnych użytkowników, jest strategiczna decyzja. Podczas planowania należy rozważyć przypisanie roli do grupy w celu zarządzania przypisaniami ról, gdy:

- Wielu użytkowników jest przypisanych do roli
- Chcesz delegować przypisanie roli

#### <a name="many-users-are-assigned-to-a-role"></a>Wielu użytkowników jest przypisanych do roli

Śledzenie osoby, która jest przypisana do roli i zarządzanie swoimi przypisaniami, w zależności od tego, kiedy ich potrzebują, może zająć trochę czasu. Aby przypisać grupę do roli, należy najpierw [utworzyć grupę, do której można przypisać rolę](../roles/groups-create-eligible.md) , a następnie przypisać grupę jako kwalifikującą się do roli. Ta akcja dotyczy wszystkich użytkowników w grupie w tym samym procesie aktywacji co indywidualni użytkownicy, którzy mają prawo do podniesienia uprawnień do roli. Członkowie grupy aktywują przypisania do grupy indywidualnie przy użyciu żądania aktywacji Privileged Identity Management i procesu zatwierdzania. Grupa nie jest aktywowana, tylko członkostwo w grupie użytkownika.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Chcesz delegować przypisanie roli

Właściciel grupy może zarządzać członkostwem w grupie. W przypadku grup, do których można przypisać role usługi Azure AD, tylko administrator ról uprzywilejowanych, Administrator globalny i właściciele grupy mogą zarządzać członkostwem w grupie. Dodając nowych członków do grupy, członek uzyskuje dostęp do ról, do których grupa jest przypisana, niezależnie od tego, czy przypisanie jest uprawnione czy aktywne. Za pomocą właścicieli grup można delegować zarządzanie członkostwem w grupie dla przypisanej roli, aby zmniejszyć zakres wymaganych uprawnień. Aby uzyskać więcej informacji na temat przypisywania właściciela do grupy podczas tworzenia grupy, zobacz [Tworzenie grupy przypisanej do roli w usłudze Azure AD](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** przenoszenie grup usługi Azure AD, które można przypisać do ról w obszarze zarządzanie przez Privileged Identity Management. Po przypisaniu grupy do zarządzania przez program PIM jest ona nazywana grupą dostępu uprzywilejowanego. Użyj programu PIM, aby wymagać od właścicieli grupy aktywacji przypisania roli właściciela, zanim będzie można zarządzać członkostwem w grupie. Aby uzyskać więcej informacji na temat przełączania grup w programie PIM Management, zobacz temat [przenoszenie uprzywilejowanych grup dostępu (wersja zapoznawcza) do Privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Zdecyduj, które przypisania ról powinny być trwałe lub kwalifikujące się

Po określeniu listy ról, które mają być zarządzane przez Privileged Identity Management, należy zdecydować, którzy użytkownicy powinni uzyskać kwalifikującą się rolę i trwale aktywną rolę. Stałe aktywne role są normalnymi rolami przypisanymi za pomocą Azure Active Directory i zasobami platformy Azure, podczas gdy kwalifikujące się role można przypisywać tylko w Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby dla ról usługi Azure AD i ról platformy Azure nie było żadnych stałych, które są stale [aktywne, a](../roles/security-emergency-access.md)które mają stałą rolę administratora globalnego.

Mimo że firma Microsoft zaleca zero administratorów, czasami trudno jest uzyskać od tych organizacji. Poniżej przedstawiono kwestie, które należy wziąć pod uwagę podczas podejmowania tej decyzji:

- Częstotliwość podniesienia uprawnień — Jeśli użytkownik potrzebuje tylko jednego przypisania uprzywilejowanego, nie powinien mieć trwałego przypisania. Z drugiej strony, jeśli użytkownik potrzebuje roli do codziennego zadania, a korzystanie z Privileged Identity Management znacznie zmniejszy swoją produktywność, można je rozważyć dla stałej roli.
- Przypadki specyficzne dla Twojej organizacji — jeśli osoba mająca kwalifikującą się rolę pochodzi z odległego zespołu lub dyrektora o wysokiej klasyfikacji do punktu, który komunikuje się i wymusza podwyższenie poziomu, może być traktowany jako rola trwała.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie cyklicznych przeglądów dostępu dla użytkowników z trwałymi przypisaniami ról (jeśli istnieją). Dowiedz się więcej o cyklicznym przeglądzie dostępu w ostatniej sekcji tego planu wdrożenia

### <a name="draft-your-privileged-identity-management-settings"></a>Wersja robocza ustawień Privileged Identity Management

Przed zaimplementowaniem rozwiązania Privileged Identity Management warto podwyższyć poziom Privileged Identity Management ustawień dla każdej roli uprzywilejowanej używanej przez organizację. W tej sekcji przedstawiono kilka przykładów ustawień Privileged Identity Management dla konkretnych ról (są one przeznaczone tylko dla celów informacyjnych i mogą być różne dla organizacji). Każde z tych ustawień zostało szczegółowo omówione w zaleceniach firmy Microsoft po tabelach.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Ustawienia Privileged Identity Management dla ról usługi Azure AD

| Rola | Wymaganie uwierzytelniania wieloskładnikowego | Powiadomienie | Bilet zdarzenia | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administrator trwały |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrator globalny | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni administratorzy globalni | 1 godzina | Konta dostępu awaryjnego |
| Administrator programu Exchange | :heavy_check_mark: | :heavy_check_mark: | y | y | Brak | 2 godziny | Brak |
| Administrator pomocy technicznej | y | y | :heavy_check_mark: | y | Brak | 8 godzin | Brak |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Ustawienia Privileged Identity Management dla ról platformy Azure

| Rola | Wymaganie uwierzytelniania wieloskładnikowego | Powiadomienie | Wymagaj zatwierdzenia | Osoba zatwierdzająca | Czas trwania aktywacji | Administrator aktywny | Aktywne wygaśnięcie | Kwalifikujące się wygaśnięcie |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Właściciel krytycznych subskrypcji | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni właściciele subskrypcji | 1 godzina | Brak | n/d | 3 miesiące |
| Administrator dostępu użytkowników z mniej krytycznymi subskrypcjami | :heavy_check_mark: | :heavy_check_mark: | y | Brak | 1 godzina | Brak | n/d | 3 miesiące |
| Współautor maszyny wirtualnej | y | :heavy_check_mark: | y | Brak | 3 godziny | Brak | n/d | 6 miesięcy |

W poniższej tabeli opisano poszczególne ustawienia.

| Ustawienie | Opis |
| --- | --- |
| Rola | Nazwa roli, dla której są definiowane ustawienia. |
| Wymaganie uwierzytelniania wieloskładnikowego | Czy uprawniony użytkownik musi wykonać uwierzytelnianie wieloskładnikowe przed aktywowaniem roli.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** Wymuszanie uwierzytelniania wieloskładnikowego dla wszystkich ról administratora, szczególnie jeśli role mają użytkowników-Gości. |
| Powiadomienie | Jeśli wartość jest równa true, Administrator globalny, administrator ról uprzywilejowanych i administrator zabezpieczeń w organizacji otrzyma powiadomienie e-mail, gdy uprawniony użytkownik aktywuje rolę.<br/><br/>**Uwaga:** Niektóre organizacje nie mają adresu e-mail powiązanego z kontami administratorów, aby otrzymywać powiadomienia e-mail, należy ustawić alternatywny adres e-mail, aby administratorzy otrzymywali te wiadomości e-mail. |
| Bilet zdarzenia | Czy uprawniony użytkownik musi zarejestrować numer biletu zdarzenia podczas aktywowania ich roli. To ustawienie pomaga organizacji identyfikować każdą aktywację z wewnętrznym numerem zdarzenia w celu ograniczenia niepożądanej aktywacji.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** korzystanie z numerów biletów zdarzeń, aby powiązać Privileged Identity Management z systemem wewnętrznym. Ta metoda może być przydatna w przypadku osób zatwierdzających, które wymagają kontekstu aktywacji. |
| Wymagaj zatwierdzenia | Czy uprawniony użytkownik musi uzyskać zatwierdzenie, aby aktywować rolę.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie zatwierdzenia dla ról z największymi uprawnieniami. W oparciu o wzorce użycia wszystkich Privileged Identity Management klientów, administratorów globalnych, administratorów użytkowników, administratorów programu Exchange, administratora zabezpieczeń i administratora hasła są najczęściej używane role z konfiguracją zatwierdzania. |
| Osoba zatwierdzająca | Jeśli wymagane jest zatwierdzenie w celu aktywowania kwalifikującej się roli, należy podać osoby, które powinny zatwierdzić żądanie. Domyślnie Privileged Identity Management ustawia osoby zatwierdzającej na wszystkich użytkowników, którzy są administratorami ról uprzywilejowanych, niezależnie od tego, czy są one trwałe, czy kwalifikujące się.<br/><br/>**Uwaga:** Jeśli użytkownik jest uprawniony do roli usługi Azure AD i osoby zatwierdzającej rolę, nie będzie w stanie zatwierdzić siebie.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** , aby wybierać osoby zatwierdzające, które są najbardziej znające rolę i jej częste użytkowników, a nie administratora globalnego. |
| Czas trwania aktywacji | Długość czasu aktywacji użytkownika w roli przed jego wygaśnięciem. |
| Administrator trwały | Lista użytkowników, którzy będą administratorami stałymi dla roli (nigdy nie trzeba aktywować).<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** , aby nie mieć stałego administratora dla wszystkich ról, z wyjątkiem administratorów globalnych. Przeczytaj więcej na temat tego, kto powinien zostać uprawniony i kto powinien być trwale aktywny w tym planie. |
| Administrator aktywny | W przypadku zasobów platformy Azure Active administrator jest listą użytkowników, którzy nigdy nie będą musieli aktywować, aby korzystać z roli. Ta lista nie jest określana jako stały administrator, podobnie jak w przypadku ról usługi Azure AD, ponieważ można ustawić czas wygaśnięcia dla momentu utraty tej roli przez użytkownika. |
| Aktywne wygaśnięcie | Aktywne przypisania ról dla ról platformy Azure wygasają po upływie skonfigurowanego czasu trwania. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6-miesięczny, 1 rok lub trwale aktywny. |
| Kwalifikujące się wygaśnięcie | Uprawnione przypisania ról dla ról platformy Azure wygasają po tym czasie trwania. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6 miesięcy, 1 rok lub trwale kwalifikujące się. |

## <a name="implementation-plan"></a>Plan implementacji

Podstawą prawidłowego planowania jest podstawa, na której można pomyślnie wdrożyć aplikację z Azure Active Directory.  Zapewnia ona inteligentne zabezpieczenia i integrację, która upraszcza proces dołączania, skracając czas na pomyślne wdrożenia.  Ta kombinacja gwarantuje, że aplikacja jest zintegrowana z łatwością, jednocześnie ograniczając czas działania użytkowników końcowych.

### <a name="identify-test-users"></a>Identyfikowanie użytkowników testowych

Ta sekcja służy do identyfikowania zestawu użytkowników i grup użytkowników w celu sprawdzenia poprawności implementacji. Na podstawie ustawień wybranych w sekcji Planowanie Zidentyfikuj użytkowników, którzy mają zostać przetestować dla każdej roli.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby właściciele usług każdej roli usługi Azure AD byli użytkownikami testowymi, dzięki czemu mogą zapoznać się z procesem i stać się wewnętrznym Rzecznikem.

W tej tabeli Zidentyfikuj użytkowników testowych, którzy weryfikują, że ustawienia dla ról działają.

| Nazwa roli | Użytkownicy testowi |
| --- | --- |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |

### <a name="test-implementation"></a>Implementacja testowa

Teraz, po zidentyfikowaniu użytkowników testowych, użyj tego kroku, aby skonfigurować Privileged Identity Management dla użytkowników testowych. Jeśli organizacja chce dołączyć przepływ pracy Privileged Identity Management do własnej aplikacji wewnętrznej zamiast korzystać z Privileged Identity Management w Azure Portal, wszystkie operacje w Privileged Identity Management są również obsługiwane za pośrednictwem [interfejsu API programu Graph](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurowanie Privileged Identity Management dla ról usługi Azure AD

1. [Skonfiguruj ustawienia roli usługi Azure AD](pim-how-to-change-default-settings.md) na podstawie planowanych czynności.

1. Przejdź do **ról usługi Azure AD**, wybierz pozycję **role**, a następnie wybierz skonfigurowaną rolę.

1. W przypadku grupy użytkowników testowych, jeśli są już administratorami stałymi, możesz je kwalifikować, wyszukując je i konwertując z trwałego na kwalifikujące się, wybierając trzy kropki w ich wierszu. Jeśli nie mają jeszcze przypisań ról, można [utworzyć nowe kwalifikujące się przypisanie](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Powtórz kroki od 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link pozwalający [aktywować rolę usługi Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Konfigurowanie Privileged Identity Management ról platformy Azure

1. [Skonfiguruj ustawienia roli zasobów platformy Azure](pim-resource-roles-configure-role-settings.md) dla roli w ramach subskrypcji lub zasobu, który chcesz przetestować.

1. Przejdź do **zasobów platformy Azure** dla tej subskrypcji i wybierz **role**, wybierz skonfigurowaną rolę.

1. W przypadku grupy użytkowników testowych, jeśli są one już aktywnym administratorem, możesz je kwalifikować, wyszukując je i [aktualizując przypisane role](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Jeśli nie mają jeszcze roli, możesz [przypisać nową rolę](pim-resource-roles-assign-roles.md#assign-a-role).

1. Powtórz kroki od 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link pozwalający [aktywować swoją rolę zasobów platformy Azure](pim-resource-roles-activate-your-roles.md).

Należy użyć tego etapu, aby sprawdzić, czy cała konfiguracja skonfigurowana dla ról działa prawidłowo. Skorzystaj z poniższej tabeli, aby udokumentować testy. Ten etap powinien również służyć do optymalizowania komunikacji z użytkownikami, których to dotyczy.

| Rola | Oczekiwane zachowanie podczas aktywacji | Rzeczywiste wyniki |
| --- | --- | --- |
| Administrator globalny | (1) Wymagaj uwierzytelniania wieloskładnikowego<br/>(2) Wymagaj zatwierdzenia<br/>(3) osoba zatwierdzająca otrzymuje powiadomienie i może zatwierdzić<br/>(4) rola wygasa po upływie czasu wstępnego |  |
| Właściciel subskrypcji *X* | (1) Wymagaj uwierzytelniania wieloskładnikowego<br/>(2) kwalifikujące się przypisanie wygasa po upływie skonfigurowanego okresu |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Komunikowanie się Privileged Identity Management z zainteresowanymi stronami

Wdrożenie Privileged Identity Management spowoduje wprowadzenie dodatkowych kroków dla użytkowników ról uprzywilejowanych. Chociaż Privileged Identity Management znacznie zmniejszają problemy z zabezpieczeniami związane z tożsamościami uprzywilejowanymi, zmiana musi być efektywnie przekazywana przed wdrożeniem w całej organizacji. W zależności od liczby administratorów, którzy mają wpływ, organizacje często wybierają do tworzenia dokument wewnętrzny, wideo lub wiadomość e-mail o zmianie. Często zawarte w tych komunikacji obejmują:

- Co to jest PIM
- Co to jest korzyść dla organizacji
- Którego będzie dotyczyć
- Kiedy będzie można wdrożyć usługę PIM
- Jakie dodatkowe czynności będą wymagane, aby użytkownicy mogli aktywować swoją rolę
    - Należy wysłać linki do naszej dokumentacji:
    - [Aktywuj role usługi Azure AD](pim-how-to-activate-role.md)
    - [Aktywuj role platformy Azure](pim-resource-roles-activate-your-roles.md)
- Informacje kontaktowe lub pomoc techniczna dla wszystkich problemów związanych z usługą PIM

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby skonfigurować czas pracy z zespołem pomocy technicznej/wsparcia technicznego w celu przechodzenia przez proces Privileged Identity Managementy (Jeśli organizacja ma wewnętrzny zespół pomocy technicznej IT). Podaj im odpowiednie dokumenty oraz informacje kontaktowe.

### <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Po zakończeniu testowania i pomyślnym przeniesieniu Privileged Identity Management do środowiska produkcyjnego, powtórz wszystkie kroki w fazach testowania dla wszystkich użytkowników każdej roli zdefiniowanej w konfiguracji Privileged Identity Management. W przypadku Privileged Identity Management dla ról usługi Azure AD organizacje często testują i wdrażają Privileged Identity Management dla administratorów globalnych przed testowaniem i wdrażaniem Privileged Identity Management dla innych ról. W tym czasie w przypadku zasobów platformy Azure zwykle testowanie i wdrażanie Privileged Identity Management jedną subskrypcję platformy Azure.

### <a name="if-a-rollback-is-needed"></a>Jeśli jest wymagana wycofanie

Jeśli Privileged Identity Management nie zadziałała prawidłowo w środowisku produkcyjnym, następujące kroki wycofywania mogą pomóc w powrocie do znanego dobrego stanu przed rozpoczęciem konfigurowania Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Role usługi Azure Active Directory

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Wybierz pozycję **role usługi Azure AD** , a następnie wybierz pozycję **role**.
1. Dla każdej skonfigurowanej roli wybierz przycisk wielokropka (**...**) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Wybierz opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

#### <a name="azure-roles"></a>Role platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Wybierz pozycję **zasoby platformy Azure** , a następnie wybierz subskrypcję lub zasób do wycofania.
1. Wybierz pozycję **role**.
1. Dla każdej skonfigurowanej roli wybierz przycisk wielokropka (**...**) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Wybierz opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

## <a name="next-steps-after-deploying"></a>Następne kroki po wdrożeniu

Pomyślnie wdrożono Privileged Identity Management w środowisku produkcyjnym to znaczący krok w zakresie zabezpieczania tożsamości uprzywilejowanych w organizacji. Wdrożenie Privileged Identity Management obejmuje dodatkowe funkcje Privileged Identity Management, które powinny być używane na potrzeby zabezpieczeń i zgodności.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Korzystanie z alertów Privileged Identity Management w celu zabezpieczenia uprzywilejowanego dostępu

Aby uzyskać więcej informacji na temat korzystania z wbudowanych funkcji alertów Privileged Identity Management w celu zabezpieczenia organizacji, zobacz [alerty zabezpieczeń](pim-how-to-configure-security-alerts.md#security-alerts). Te alerty obejmują: Administratorzy nie używają uprzywilejowanych ról, są przypisywane role poza Privileged Identity Management, role są aktywowane zbyt często i nie tylko. Aby w pełni chronić organizację, należy regularnie przechodzić przez listę alertów i rozwiązać problemy. Alerty można wyświetlać i usuwać w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Wybierz pozycję **role usługi Azure AD** , a następnie wybierz pozycję **alerty**.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby od razu zająć się wszystkimi alertami oznaczonymi o wysokiej ważności. W przypadku alertów o średniej i niskiej ważności użytkownik powinien otrzymywać informacje i wprowadzić zmiany, jeśli sądzisz, że istnieje zagrożenie bezpieczeństwa.

Jeśli którekolwiek z określonych alertów nie są przydatne lub nie mają zastosowania w organizacji, zawsze możesz odrzucić ten alert na stronie alertów. Można zawsze przywrócić ten odrzucanie później na stronie ustawień usługi Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Skonfiguruj cykliczne przeglądy dostępu do regularnego przeprowadzania inspekcji tożsamości uprzywilejowanych w organizacji

Przeglądy dostępu są najlepszym sposobem na zaproszenie użytkowników przypisanych do ról uprzywilejowanych lub określonych recenzentów, czy każdy użytkownik musi mieć uprzywilejowaną tożsamość. Przeglądy dostępu są doskonałe, jeśli chcesz zmniejszyć obszar ataków i zachować zgodność. Aby uzyskać więcej informacji na temat rozpoczynania przeglądu dostępu, zobacz przeglądy [dostępu do ról usługi Azure AD](pim-how-to-start-security-review.md) i [przeglądy dostępu do ról platformy Azure](pim-resource-roles-start-access-review.md). W przypadku niektórych organizacji okresowe przeglądy dostępu są wymagane, aby zachować zgodność z przepisami i przepisami, a w przypadku innych, przegląd dostępu jest najlepszym sposobem wymuszania najniższych uprawnień w całej organizacji.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie kwartalnych przeglądów dostępu dla wszystkich ról usługi Azure AD i platformy Azure.

W większości przypadków, osoba przeglądający dla ról usługi Azure AD jest właścicielem subskrypcji, w której znajduje się rola dla ról platformy Azure. Jednak często zdarza się, że firmy mają uprzywilejowane konta, które nie są połączone z żadnym z adresów e-mail poszczególnych osób. W takich przypadkach nikt nie odczytuje ani nie przegląda dostępu.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** dodanie pomocniczego adresu e-mail dla wszystkich kont z przypisaniami ról uprzywilejowanych, które nie są połączone z regularnie sprawdzonym adresem e-mail.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Maksymalnie zapoznaj się z dziennikiem inspekcji, aby zwiększyć bezpieczeństwo i zgodność

Dziennik inspekcji jest miejscem, w którym można zachować aktualność i być zgodne z przepisami. Privileged Identity Management obecnie przechowuje 30-dniową historię wszystkich historii organizacji w swoim dzienniku inspekcji, w tym:

- Aktywacja/dezaktywacja kwalifikujących się ról
- Działania przypisania roli wewnątrz i na zewnątrz Privileged Identity Management
- Zmiany w ustawieniach roli
- Działania żądania/zatwierdzania/odmowy dla aktywacji roli z konfiguracją zatwierdzania
- Aktualizowanie alertów

Możesz uzyskać dostęp do dzienników inspekcji, jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej. Aby uzyskać więcej informacji, zobacz [Historia inspekcji dla](pim-how-to-use-audit-log.md) ról i historii inspekcji usługi Azure AD [dla ról platformy Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby co najmniej jeden administrator odczytał przez wszystkie zdarzenia inspekcji co tydzień i regularnie eksportować zdarzenia inspekcji.

Jeśli chcesz automatycznie przechowywać zdarzenia inspekcji przez dłuższy czas, Privileged Identity Management dziennik inspekcji zostanie automatycznie zsynchronizowany z dziennikami [inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie [monitorowania dzienników platformy Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) , aby archiwizować zdarzenia inspekcji na koncie usługi Azure Storage w celu zapewnienia większego bezpieczeństwa i zgodności.
