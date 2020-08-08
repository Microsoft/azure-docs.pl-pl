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
ms.date: 08/06/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9e8250661fdbd6c67faade31caaed61ee8a399fe
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008098"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Wdróż Azure AD Privileged Identity Management (PIM)

W tym przewodniku krok po kroku opisano sposób planowania wdrożenia programu Privileged Identity Management (PIM) w organizacji Azure Active Directory (Azure AD).

> [!TIP]
> W tym artykule zobaczysz elementy oznaczone jako:
>
> : heavy_check_mark: **firma Microsoft zaleca**
>
> Są to ogólne zalecenia, które należy zaimplementować tylko w przypadku, gdy mają zastosowanie do konkretnych potrzeb przedsiębiorstwa.

## <a name="learn-about-privileged-identity-management"></a>Dowiedz się więcej o Privileged Identity Management

Azure AD Privileged Identity Management ułatwia zarządzanie uprzywilejowanymi rolami administracyjnymi w usłudze Azure AD, zasobach platformy Azure i innych usługach online firmy Microsoft. W świecie, w którym są przypisane i zapomniane tożsamości uprzywilejowane, Privileged Identity Management zapewnia rozwiązania, takie jak dostęp just in Time, żądania przepływów pracy zatwierdzania i w pełni zintegrowane przeglądy dostępu, dzięki czemu można identyfikować, odkrywać i zapobiegać złośliwym działaniom uprzywilejowanych ról w czasie rzeczywistym. Wdrożenie Privileged Identity Management w celu zarządzania rolami uprzywilejowanymi w całej organizacji znacznie zmniejsza ryzyko, a jednocześnie pozwala uzyskać cenne informacje o działaniach ról uprzywilejowanych.

### <a name="business-value-of-privileged-identity-management"></a>Wartość biznesowa Privileged Identity Management

**Zarządzanie ryzykiem** — Zabezpiecz swoją organizację, wymuszając zasadę [najniższych uprawnień dostępu](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) i dostęp just in Time. Minimalizując liczbę stałych przypisań użytkowników do ról uprzywilejowanych i wymuszanie zatwierdzeń i uwierzytelniania wieloskładnikowego dla podniesienia uprawnień, można znacznie zmniejszyć zagrożenia bezpieczeństwa związane z dostępem uprzywilejowanym w organizacji. Wymuszanie najniższych uprawnień i dostępu just in Time umożliwi również Wyświetlanie historii dostępu do ról uprzywilejowanych i Śledzenie problemów z zabezpieczeniami w miarę ich występowania.

**Zgodność adresów i zarządzanie** — wdrożenie Privileged Identity Management tworzy środowisko na potrzeby zarządzania tożsamościami. Podniesienie uprawnień dla tożsamości uprzywilejowanych umożliwia Privileged Identity Management śledzenie działań uprzywilejowanego dostępu w organizacji. Będziesz również mieć możliwość wyświetlania i otrzymywania powiadomień o wszystkich przypisaniach trwałych i kwalifikujących się ról w organizacji. Za pomocą przeglądu dostępu można regularnie kontrolować i usuwać niepotrzebne tożsamości uprzywilejowane oraz upewnić się, że organizacja jest zgodna z najbardziej rygorystycznymi standardami dotyczącymi tożsamości, dostępu i zabezpieczeń.

**Zmniejsz koszty** — Zmniejsz koszty, eliminując nieefektywność, błąd ludzki i problemy z zabezpieczeniami, wdrażając Privileged Identity Management poprawnie. Wynikiem jest spadek cybernetycznymi zbrodni związanych z tożsamościami uprzywilejowanymi, które są kosztowne i trudne do odzyskania. Privileged Identity Management pomoże również ograniczyć koszty związane z inspekcją dostępu do informacji, gdy chodzi o przestrzeganie przepisów i standardów.

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="licensing-requirements"></a>Wymagania dotyczące licencjonowania

Aby można było korzystać z Privileged Identity Management, katalog musi mieć jedną z następujących licencji płatnych lub wersji próbnej:

- Usługa Azure AD — wersja Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

Aby uzyskać więcej informacji, zobacz [wymagania licencyjne do używania Privileged Identity Management](subscription-requirements.md).

### <a name="key-terminology"></a>Terminologia klucza

| Termin lub pojęcie | Opis |
| --- | --- |
| kwalifikowanie się | Przypisanie roli, które wymaga od użytkownika wykonania jednej lub kilku akcji w celu użycia tej roli. Jeśli użytkownik został zakwalifikowany do roli, oznacza to, że może aktywować tę rolę, kiedy musi wykonać zadanie uprzywilejowane. Nie ma żadnej różnicy między dostępem udzielonym komuś za pomocą trwałego przypisania roli i przypisania kwalifikowania się do roli. Jedyna różnica polega na tym, że niektórzy użytkownicy nie potrzebują tego dostępu przez cały czas. |
| aktywuj | Proces wykonywania jednej lub kilku akcji w celu użycia roli, do której użytkownik został zakwalifikowany. Akcje te mogą obejmować przeprowadzenie uwierzytelniania wieloskładnikowego (MFA), podanie uzasadnienia biznesowego lub żądanie zatwierdzenia od wyznaczonych osób zatwierdzających. |
| dostęp just-in-time (JIT) | Model, w którym użytkownicy uzyskują tymczasowe uprawnienia do wykonywania uprzywilejowanych zadań, dzięki czemu złośliwi lub nieautoryzowani użytkownicy nie mogą uzyskać dostępu po wygaśnięciu uprawnienia. Dostęp jest udzielany tylko wtedy, gdy użytkownicy go potrzebują. |
| zasada dostępu z najniższymi uprawnieniami | Zalecane rozwiązanie dotyczące zabezpieczeń, w którym każdy użytkownik ma minimalne uprawnienia wymagane do wykonania zadania, które mu przydzielono. Takie rozwiązanie pozwala zminimalizować liczbę administratorów globalnych i zamiast tego korzystać ze specyficznych ról administratorów dla konkretnych scenariuszy. |

Aby uzyskać więcej informacji, zobacz [terminologia](pim-configure.md#terminology).

### <a name="high-level-overview-of-how-privileged-identity-management-works"></a>Ogólne omówienie sposobu działania Privileged Identity Management

1. Privileged Identity Management jest skonfigurowany tak, aby użytkownicy mieli uprawnienia do ról uprzywilejowanych.
1. Gdy uprawniony użytkownik musi korzystać z roli uprzywilejowanej, uaktywnia rolę w Privileged Identity Management.
1. W zależności od ustawień Privileged Identity Management skonfigurowanych dla roli użytkownik musi wykonać pewne czynności (takie jak uwierzytelnianie wieloskładnikowe, uzyskanie zatwierdzenia lub określenie przyczyny).
1. Po pomyślnym aktywowaniu roli przez użytkownika otrzymasz rolę dla wstępnie skonfigurowanego okresu.
1. Administratorzy mogą wyświetlić historię wszystkich działań Privileged Identity Management w dzienniku inspekcji. Mogą również dodatkowo zabezpieczyć swoje organizacje usługi Azure AD i spełnić wymagania przy użyciu funkcji Privileged Identity Management, takich jak przeglądy i alerty dostępu.

Aby uzyskać więcej informacji, zobacz [co to jest Azure AD Privileged Identity Management?](pim-configure.md).

### <a name="roles-that-can-be-managed-by-privileged-identity-management"></a>Role, którymi można zarządzać za pomocą Privileged Identity Management

**Role usługi Azure AD** — te role znajdują się w Azure Active Directory (na przykład administrator globalny, administrator programu Exchange i administrator zabezpieczeń). Więcej informacji na temat ról i ich funkcji można znaleźć w temacie [uprawnienia roli administrator w Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Aby uzyskać pomoc dotyczącą określania ról do przypisania administratorów, zobacz [najmniej uprzywilejowanych ról według zadania](../users-groups-roles/roles-delegate-by-task.md).

**Role zasobów platformy Azure** — te role są połączone z zasobem platformy Azure, grupą zasobów, subskrypcją lub grupą zarządzania. Privileged Identity Management zapewnia dostęp just in Time do obu wbudowanych ról, takich jak właściciel, administrator dostępu użytkowników i współautor, a także [role niestandardowe](../../role-based-access-control/custom-roles.md). Aby uzyskać więcej informacji na temat ról zasobów platformy Azure, zobacz [Kontrola dostępu oparta na rolach (Azure RBAC)](../../role-based-access-control/overview.md).

Aby uzyskać więcej informacji, zobacz [role, którymi nie można zarządzać w Privileged Identity Management](pim-roles.md).

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

Ta sekcja koncentruje się na tym, co należy zrobić przed wdrożeniem Privileged Identity Management w organizacji. Ważne jest, aby postępować zgodnie z instrukcjami i zrozumieć koncepcje przedstawione w tej sekcji, ponieważ przeprowadzą Cię przez proces tworzenia najlepszego planu dopasowanego do tożsamości uprzywilejowanych w organizacji.

### <a name="identify-your-stakeholders"></a>Zidentyfikuj swoje osoby zainteresowane

W poniższej sekcji znajdują się informacje ułatwiające zidentyfikowanie wszystkich zainteresowanych uczestników projektu i konieczność wylogowania się, przejrzenia lub poinformowanie użytkownika. Zawiera osobne tabele do wdrażania Privileged Identity Management dla ról usługi Azure AD i Privileged Identity Management dla ról zasobów platformy Azure. Dodaj uczestników projektu do poniższej tabeli, zgodnie z potrzebami organizacji.

- SO = Wyloguj się w tym projekcie
- R = przejrzyj ten projekt i podaj dane wejściowe
- I = poinformowany o tym projekcie

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Uczestnicy projektu: Privileged Identity Management dla ról usługi Azure AD

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Nazwa i adres e-mail | **Architekt tożsamości lub Administrator globalny platformy Azure**<br/>Przedstawiciel zespołu ds. zarządzania tożsamościami odpowiedzialny za definiowanie sposobu wyrównywania tej zmiany z podstawową infrastrukturą zarządzania tożsamościami w organizacji. | SO/R/I |
| Nazwa i adres e-mail | **Menedżer linii/właściciela usługi**<br/>Przedstawiciel od właścicieli IT usługi lub grupy usług. Są one kluczowym sposobem podejmowania decyzji i pomocy w rozwinięcia Privileged Identity Management dla zespołu. | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może dostarczyć dane wejściowe dotyczące możliwości obsługi tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli uprzywilejowanej**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Muszą wiedzieć, jak aktywować swoje role po zaimplementowaniu Privileged Identity Management. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-resource-roles"></a>Uczestnicy projektu: Privileged Identity Management dla ról zasobów platformy Azure

| Nazwa | Rola | Akcja |
| --- | --- | --- |
| Nazwa i adres e-mail | **Właściciel subskrypcji/zasobu**<br/>Przedstawiciel z właścicieli IT dla każdej subskrypcji lub zasobu, który ma zostać wdrożony Privileged Identity Management | SO/R/I |
| Nazwa i adres e-mail | **Właściciel zabezpieczeń**<br/>Przedstawiciel zespołu ds. zabezpieczeń, który może się wylogować, aby plan spełniał wymagania dotyczące zabezpieczeń Twojej organizacji. | SO/R |
| Nazwa i adres e-mail | **Menedżer pomocy technicznej IT/pomoc techniczna**<br/>Przedstawiciel firmy z działu pomocy technicznej IT, który może dostarczyć dane wejściowe dotyczące możliwości obsługi tej zmiany w perspektywie pomocy technicznej. | R/I |
| Nazwa i wiadomość e-mail dla użytkowników pilotażowych | **Użytkownicy roli platformy Azure**<br/>Grupa użytkowników, dla których zaimplementowano usługi Privileged Identity Management. Muszą wiedzieć, jak aktywować swoje role po zaimplementowaniu Privileged Identity Management. | I |

### <a name="enable-privileged-identity-management"></a>Włącz Privileged Identity Management

W ramach procesu planowania musisz najpierw wyrazić zgodę na i włączyć Privileged Identity Management, postępując zgodnie z naszymi artykułem [Privileged Identity Management](pim-getting-started.md) . Włączenie Privileged Identity Management zapewnia dostęp do niektórych funkcji, które są specjalnie zaprojektowane w celu ułatwienia wdrożenia.

Jeśli celem jest wdrożenie Privileged Identity Management dla zasobów platformy Azure, należy postępować zgodnie z naszymi [zasobami platformy Azure, aby zarządzać w Privileged Identity Management](pim-resource-roles-discover-resources.md) artykule. Tylko właściciele subskrypcji i grupy zarządzania mogą odnajdywać te zasoby i dołączać je do Privileged Identity Management. Po dołączeniu funkcja PIM będzie dostępna dla właścicieli na wszystkich poziomach, takich jak grupa zarządzania, subskrypcja, Grupa zasobów i zasób. Jeśli jesteś administratorem globalnym próbującym wdrożyć Privileged Identity Management zasobów platformy Azure, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby zapewnić sobie dostęp do wszystkich zasobów platformy Azure w katalogu do odnajdowania. Jednak przed zarządzaniem swoimi zasobami przy użyciu Privileged Identity Management zalecamy zatwierdzenie od poszczególnych właścicieli subskrypcji.

### <a name="enforce-principle-of-least-privilege"></a>Wymuś zasadę najniższych uprawnień

Ważne jest, aby upewnić się, że zasady najniższych uprawnień w Twojej organizacji zostały wymuszone dla usługi Azure AD i ról zasobów platformy Azure.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

W przypadku ról usługi Azure AD powszechne jest, aby organizacje przypisywały rolę administratora globalnego do znaczącej liczby administratorów, gdy większość administratorów potrzebuje tylko jednej lub dwóch określonych ról administratora. Przypisań ról uprzywilejowanych również można pozostawić niezarządzaną.

> [!NOTE]
> Wspólne pułapek w delegowaniu ról:
>
> - Administrator, który jest odpowiedzialny za wymianę, ma rolę administratora globalnego, mimo że potrzebuje tylko roli administratora programu Exchange do wykonywania codziennych zadań.
> - Rola administratora globalnego jest przypisywana do administratora pakietu Office, ponieważ administrator musi mieć role zabezpieczeń i administratorów programu SharePoint, a łatwiej jest tylko delegować jedną rolę.
> - Administratorowi przypisano rolę administratora zabezpieczeń, aby wykonać zadanie o długim czasie temu, ale nigdy nie zostało usunięte.

Wykonaj następujące kroki, aby wymusić zasadę najniższych uprawnień dla ról usługi Azure AD.

1. Zapoznaj się z szczegółowością ról, odczytując i opisując [dostępne role administratorów usługi Azure AD](../users-groups-roles/directory-assign-admin-roles.md#available-roles). Ty i Twój zespół powinien również odwoływać się do [ról administratorów według tożsamości w usłudze Azure AD](../users-groups-roles/roles-delegate-by-task.md), która objaśnia najmniejszą rolę uprzywilejowaną dla konkretnych zadań.

1. Lista użytkowników, którzy mają role uprzywilejowane w organizacji. Możesz użyć [odnajdywania Privileged Identity Management i szczegółowych informacji (wersja zapoznawcza)](pim-security-wizard.md) , aby uzyskać dostęp do strony podobnej do następującej.

    ![Strona odnajdywanie i szczegółowe informacje (wersja zapoznawcza) w celu ograniczenia ekspozycji przez role uprzywilejowane](./media/pim-deployment-plan/new-preview-page.png)

1. Dla wszystkich administratorów globalnych w organizacji należy dowiedzieć się, dlaczego potrzebują roli. W oparciu o odczytywanie poprzedniej dokumentacji, jeśli zadanie osoby może być wykonywane przez jedną lub kilka szczegółowych ról administratora, należy usunąć je z roli administratora globalnego i przystąpić odpowiednio do Azure Active Directory (jako odwołanie: obecnie Firma Microsoft ma tylko około 10 administratorów z rolą administratora globalnego. Dowiedz się więcej na [temat sposobu korzystania z Privileged Identity Management przez firmę Microsoft](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access)).

1. W przypadku wszystkich innych ról usługi Azure AD Przejrzyj listę przypisań, zidentyfikuj administratorów, którzy nie potrzebują już roli, i Usuń ich z przypisań.

Aby zautomatyzować ostatnie dwa kroki, możesz użyć przeglądów dostępu w Privileged Identity Management. Postępując zgodnie z instrukcjami w temacie [Rozpoczynanie przeglądu dostępu dla ról usługi Azure AD w Privileged Identity Management](pim-how-to-start-security-review.md), można skonfigurować przegląd dostępu dla każdej roli usługi Azure AD, która ma co najmniej jednego członka.

![Tworzenie okienka przeglądu dostępu dla ról usługi Azure AD](./media/pim-deployment-plan/create-access-review.png)

Należy ustawić recenzentów na **elementy członkowskie (własne)**. Spowoduje to wysłanie wiadomości e-mail do wszystkich członków w roli w celu potwierdzenia, że potrzebują dostępu. Należy również włączyć opcję **Wymagaj przyczyny przy zatwierdzeniu** w ustawieniach zaawansowanych, aby użytkownicy mogli określać, dlaczego potrzebują roli. Na podstawie tych informacji można usunąć użytkowników z niepotrzebnych ról i delegować bardziej szczegółowe role administratorów w przypadku administratorów globalnych.

Przeglądy dostępu polegają na wiadomościach e-mail powiadamiających użytkowników o konieczności sprawdzenia dostępu do ról. Jeśli masz uprzywilejowane konta, które nie mają połączonych wiadomości e-mail, pamiętaj o wypełnieniu pola pomocniczy adres e-mail na tych kontach. Aby uzyskać więcej informacji, zobacz [proxyAddresses Attribute in Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

W przypadku subskrypcji i zasobów platformy Azure można skonfigurować podobny proces przeglądu dostępu, aby przejrzeć role w każdej subskrypcji lub zasobie. Celem tego procesu jest minimalizowanie przypisań administratorów i dostępu użytkowników do poszczególnych subskrypcji lub zasobów oraz usuwanie niepotrzebnych przypisań. Jednak organizacje często delegują te zadania do właściciela każdej subskrypcji lub zasobu, ponieważ mają lepsze zrozumienie konkretnych ról (zwłaszcza ról niestandardowych).

Jeśli jesteś administratorem IT z rolą administratora globalnego próbującym wdrożyć Privileged Identity Management dla zasobów platformy Azure w organizacji, możesz podwyższyć [poziom dostępu do zarządzania wszystkimi subskrypcjami platformy Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) , aby uzyskać dostęp do każdej subskrypcji. Następnie można znaleźć wszystkich właścicieli subskrypcji i z nich korzystać, aby usunąć niepotrzebne przypisania i zminimalizować przypisanie roli właściciela.

Użytkownicy z rolą właściciela dla subskrypcji platformy Azure mogą również wykorzystywać [przeglądy dostępu dla zasobów platformy Azure](pim-resource-roles-start-access-review.md) , aby przeprowadzać inspekcję i usuwać niepotrzebne przypisania ról podobne do procesu opisanego wcześniej dla ról usługi Azure AD.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Zdecyduj, które przypisania ról mają być chronione przez Privileged Identity Management

Po oczyszczeniu przypisań ról uprzywilejowanych w organizacji należy zdecydować, które role mają być chronione za pomocą Privileged Identity Management.

Jeśli rola jest chroniona za pomocą Privileged Identity Management, uprawnieni użytkownicy przypisani do niej muszą podnieść poziom uprawnień, aby korzystać z przywilejów przyznanych przez rolę. Proces podniesienia uprawnień może również obejmować uzyskanie zatwierdzenia, przeprowadzenie uwierzytelniania wieloskładnikowego i/lub dostarczenie przyczyny aktywacji. Privileged Identity Management może również śledzić podniesienia uprawnień za pomocą powiadomień i dzienników zdarzeń Privileged Identity Management i inspekcji usługi Azure AD.

Wybór ról do ochrony za pomocą Privileged Identity Management może być trudny i różny dla każdej organizacji. Ta sekcja zawiera nasze najlepsze rozwiązanie dla usług Azure AD i Azure Resource role.

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

Ważne jest, aby określić priorytety ochrony ról usługi Azure AD, które mają największą liczbę uprawnień. W oparciu o wzorce użycia między wszystkimi klientami Privileged Identity Management, 10 głównych ról usługi Azure AD zarządzanych przez Privileged Identity Management są następujące:

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
> : heavy_check_mark: **firma Microsoft zaleca** zarządzanie wszystkimi administratorami globalnymi i administratorami zabezpieczeń przy użyciu Privileged Identity Management jako pierwszy krok, ponieważ są one tymi, które mogą spowodować, że w przypadku naruszenia zabezpieczeń zostały naruszone.

Ważne jest, aby wziąć pod uwagę, jakie dane i uprawnienia są najbardziej poufne dla Twojej organizacji. Przykładowo niektóre organizacje mogą chcieć chronić swoją rolę administratora Power BI lub rolę administratora zespołów przy użyciu Privileged Identity Management, ponieważ mają możliwość dostępu do danych i/lub zmiany podstawowych przepływów pracy.

Jeśli istnieją jakieś role z przypisanymi użytkownikami gościa, są one szczególnie narażone na ataki.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** zarządzanie wszystkimi rolami z użytkownikami-gośćmi przy użyciu Privileged Identity Management, aby zmniejszyć ryzyko związane z kontami użytkowników-Gości z naruszeniem zabezpieczeń.

Role czytnika, takie jak czytniki katalogów, czytnik centrum komunikatów i czytelnik zabezpieczeń, są czasami uważane za mniej ważne w porównaniu z innymi rolami, ponieważ nie mają uprawnień do zapisu. Jednak niektórzy klienci widzą również ochronę tych ról, ponieważ osoby atakujące, którzy uzyskali dostęp do tych kont, mogą odczytywać dane poufne, takie jak dane osobowe. Należy to wziąć pod uwagę podczas decydowania, czy role czytnika w organizacji muszą być zarządzane przy użyciu Privileged Identity Management.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

Podczas wybierania przypisań ról, które mają być zarządzane za pomocą Privileged Identity Management dla zasobu platformy Azure, należy najpierw zidentyfikować subskrypcje/zasoby, które są najważniejsze dla organizacji. Przykładami takich subskrypcji/zasobów są:

- Zasoby, które obsługują najbardziej poufne dane
- Zasoby, od których zależą podstawowe aplikacje dla klientów

Jeśli jesteś administratorem globalnym, który ma problemy z wybraniem najważniejszych subskrypcji/zasobów, należy skontaktować się z właścicielami subskrypcji w organizacji w celu zebrania listy zasobów zarządzanych przez każdą subskrypcję. Następnie należy skontaktować się z właścicielami subskrypcji w celu pogrupowania zasobów na podstawie poziomu ważności w przypadku naruszenia zabezpieczeń (niski, średni, wysoki). Należy określić priorytety zarządzania zasobami przy użyciu Privileged Identity Management na podstawie tego poziomu ważności.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** współpracę z subskrypcjami/właścicielami zasobów krytycznych usług w celu skonfigurowania Privileged Identity Management przepływu pracy dla wszystkich ról w poufnych subskrypcjach/zasobach.

Privileged Identity Management dla zasobów platformy Azure obsługuje konta usług powiązane z czasem. Konta usług należy traktować dokładnie tak samo, jak w przypadku traktowania zwykłego konta użytkownika.

W przypadku subskrypcji/zasobów, które nie są uznawane za krytyczne, nie trzeba konfigurować Privileged Identity Management dla wszystkich ról. Należy jednak nadal chronić rolę administratora dostępu właściciela i użytkownika przy użyciu Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** Zarządzanie rolami właściciela i rolami administratora dostępu użytkowników wszystkich subskrypcji/zasobów przy użyciu Privileged Identity Management.

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Zdecyduj, które przypisania ról powinny być trwałe lub kwalifikujące się

Po określeniu listy ról, które mają być zarządzane przez Privileged Identity Management, należy zdecydować, którzy użytkownicy powinni uzyskać kwalifikującą się rolę i trwale aktywną rolę. Stałe aktywne role są normalnymi rolami przypisanymi za pomocą Azure Active Directory i zasobami platformy Azure, podczas gdy kwalifikujące się role można przypisywać tylko w Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby dla ról usługi Azure AD i ról zasobów platformy Azure nie było żadnych stałych, które są trwale [aktywne, a](../users-groups-roles/directory-emergency-access.md)które mają stałą rolę administratora globalnego.

Mimo że firma Microsoft zaleca zero administratorów, czasami trudno jest uzyskać od tych organizacji. Poniżej przedstawiono kwestie, które należy wziąć pod uwagę podczas podejmowania tej decyzji:

- Częstotliwość podniesienia uprawnień — Jeśli użytkownik potrzebuje tylko jednego przypisania uprzywilejowanego, nie powinien mieć trwałego przypisania. Z drugiej strony, jeśli użytkownik potrzebuje roli do codziennego zadania, a korzystanie z Privileged Identity Management znacznie zmniejszy swoją produktywność, można je rozważyć dla stałej roli.
- Przypadki specyficzne dla Twojej organizacji — jeśli osoba mająca kwalifikującą się rolę pochodzi z bardzo odległego zespołu lub dyrektora o wysokiej klasyfikacji do punktu, który komunikuje się i wymusza proces podniesienia uprawnień, może być traktowany jako rola trwała.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie cyklicznych przeglądów dostępu dla użytkowników z trwałymi przypisaniami ról (jeśli istnieją). Dowiedz się więcej o cyklicznym przeglądzie dostępu w ostatniej sekcji tego planu wdrożenia

### <a name="draft-your-privileged-identity-management-settings"></a>Wersja robocza ustawień Privileged Identity Management

Przed zaimplementowaniem rozwiązania Privileged Identity Management warto podwyższyć poziom Privileged Identity Management ustawień dla każdej roli uprzywilejowanej używanej przez organizację. W tej sekcji przedstawiono kilka przykładów ustawień Privileged Identity Management dla konkretnych ról (są one przeznaczone tylko dla celów informacyjnych i mogą być różne dla organizacji). Każde z tych ustawień zostało szczegółowo omówione w zaleceniach firmy Microsoft po tabelach.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Ustawienia Privileged Identity Management dla ról usługi Azure AD

| Rola | Wymaganie uwierzytelniania wieloskładnikowego | Powiadomienie | Bilet zdarzenia | Wymagaj zatwierdzenia | Osoby zatwierdzającej | Czas trwania aktywacji | Administrator trwały |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Administrator globalny | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni administratorzy globalni | 1 godzina | Konta dostępu awaryjnego |
| Administrator programu Exchange | :heavy_check_mark: | :heavy_check_mark: | y | y | Brak | 2 godziny | Brak |
| Administrator pomocy technicznej | y | y | :heavy_check_mark: | y | Brak | 8 godzin | Brak |

#### <a name="privileged-identity-management-settings-for-azure-resource-roles"></a>Privileged Identity Management ustawienia dla ról zasobów platformy Azure

| Rola | Wymaganie uwierzytelniania wieloskładnikowego | Powiadomienie | Wymagaj zatwierdzenia | Osoby zatwierdzającej | Czas trwania aktywacji | Administrator aktywny | Aktywne wygaśnięcie | Kwalifikujące się wygaśnięcie |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Właściciel krytycznych subskrypcji | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Inni właściciele subskrypcji | 1 godzina | Brak | nie dotyczy | 3 miesiące |
| Administrator dostępu użytkowników z mniej krytycznymi subskrypcjami | :heavy_check_mark: | :heavy_check_mark: | y | Brak | 1 godzina | Brak | nie dotyczy | 3 miesiące |
| Współautor maszyny wirtualnej | y | :heavy_check_mark: | y | Brak | 3 godziny | Brak | nie dotyczy | 6 miesięcy |

W poniższej tabeli opisano poszczególne ustawienia.

| Ustawienie | Opis |
| --- | --- |
| Rola | Nazwa roli, dla której są definiowane ustawienia. |
| Wymaganie uwierzytelniania wieloskładnikowego | Czy uprawniony użytkownik musi wykonać uwierzytelnianie wieloskładnikowe przed aktywowaniem roli.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** Wymuszanie uwierzytelniania wieloskładnikowego dla wszystkich ról administratora, szczególnie jeśli role mają użytkowników-Gości. |
| Powiadomienie | Jeśli wartość jest równa true, Administrator globalny, administrator ról uprzywilejowanych i administrator zabezpieczeń w organizacji otrzyma powiadomienie e-mail, gdy uprawniony użytkownik aktywuje rolę.<br/><br/>**Uwaga:** Niektóre organizacje nie mają adresu e-mail powiązanego z kontami administratorów, aby otrzymywać powiadomienia e-mail, należy ustawić alternatywny adres e-mail, aby administratorzy otrzymywali te wiadomości e-mail. |
| Bilet zdarzenia | Czy uprawniony użytkownik musi zarejestrować numer biletu zdarzenia podczas aktywowania ich roli. To ustawienie pomaga organizacji identyfikować każdą aktywację z wewnętrznym numerem zdarzenia w celu ograniczenia niepożądanej aktywacji.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** korzystanie z numerów biletów zdarzeń, aby powiązać Privileged Identity Management z systemem wewnętrznym. Jest to szczególnie przydatne w przypadku osób zatwierdzających, które wymagają kontekstu aktywacji. |
| Wymagaj zatwierdzenia | Czy uprawniony użytkownik musi uzyskać zatwierdzenie, aby aktywować rolę.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie zatwierdzenia dla ról z największymi uprawnieniami. W oparciu o wzorce użycia wszystkich Privileged Identity Management klientów, administratorów globalnych, administratorów użytkowników, administratorów programu Exchange, administratora zabezpieczeń i administratora hasła są najczęściej używane role z konfiguracją zatwierdzania. |
| Osoby zatwierdzającej | Jeśli wymagane jest zatwierdzenie w celu aktywowania kwalifikującej się roli, należy podać osoby, które powinny zatwierdzić żądanie. Domyślnie Privileged Identity Management ustawia osoby zatwierdzającej na wszystkich użytkowników, którzy są administratorami ról uprzywilejowanych, niezależnie od tego, czy są one trwałe, czy kwalifikujące się.<br/><br/>**Uwaga:** Jeśli użytkownik jest uprawniony do roli usługi Azure AD i osoby zatwierdzającej rolę, nie będzie w stanie zatwierdzić siebie.<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** , aby wybrać osoby zatwierdzające, które są najbardziej znające konkretną rolę i jej częste użytkowników, a nie administratora globalnego. |
| Czas trwania aktywacji | Długość czasu aktywacji użytkownika w roli przed jego wygaśnięciem. |
| Administrator trwały | Lista użytkowników, którzy będą administratorami stałymi dla roli (nigdy nie trzeba aktywować).<br/><br/> : heavy_check_mark: **firma Microsoft zaleca** , aby nie mieć stałego administratora dla wszystkich ról, z wyjątkiem administratorów globalnych. Przeczytaj więcej na temat tego, kto powinien zostać uprawniony i kto powinien być trwale aktywny w tym planie. |
| Administrator aktywny | W przypadku zasobów platformy Azure Active administrator jest listą użytkowników, którzy nigdy nie będą musieli aktywować, aby korzystać z roli. Nie jest to nazywane stałymi administratorami, takimi jak role usługi Azure AD, ponieważ można ustawić czas wygaśnięcia dla momentu utraty tej roli przez użytkownika. |
| Aktywne wygaśnięcie | Przypisanie aktywnej roli dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6-miesięczny, 1 rok lub trwale aktywny. |
| Kwalifikujące się wygaśnięcie | Odpowiednie przypisanie roli dla ról zasobów platformy Azure wygasa po tym skonfigurowanym okresie. Możesz wybrać 15 dni, 1 miesiąc, 3 miesiące, 6 miesięcy, 1 rok lub trwale kwalifikujące się. |

## <a name="implement-your-solution"></a>Implementowanie rozwiązania

Podstawą prawidłowego planowania jest podstawa, na której można pomyślnie wdrożyć aplikację z Azure Active Directory.  Zapewnia ona inteligentne zabezpieczenia i integrację, która upraszcza proces dołączania, skracając czas na pomyślne wdrożenia.  Ta kombinacja gwarantuje, że aplikacja jest zintegrowana z łatwością, jednocześnie ograniczając czas działania użytkowników końcowych.

### <a name="identify-test-users"></a>Identyfikowanie użytkowników testowych

Ta sekcja służy do identyfikowania zestawu użytkowników i grup użytkowników w celu sprawdzenia poprawności implementacji. Na podstawie ustawień wybranych w sekcji Planowanie Zidentyfikuj użytkowników, którzy mają zostać przetestować dla każdej roli.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby właściciele usług każdej roli usługi Azure AD byli użytkownikami testowymi, dzięki czemu mogą zapoznać się z procesem i stać się wewnętrznym Rzecznikem.

W tej tabeli Zidentyfikuj użytkowników testowych, którzy weryfikują, że ustawienia dla każdej roli działają.

| Nazwa roli | Użytkownicy testowi |
| --- | --- |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |
| &lt;Nazwa roli&gt; | &lt;Użytkownicy do testowania roli&gt; |

### <a name="test-implementation"></a>Implementacja testowa

Teraz, po zidentyfikowaniu użytkowników testowych, użyj tego kroku, aby skonfigurować Privileged Identity Management dla użytkowników testowych. Jeśli organizacja chce dołączyć przepływ pracy Privileged Identity Management do własnej aplikacji wewnętrznej zamiast korzystać z Privileged Identity Management w Azure Portal, wszystkie operacje w Privileged Identity Management są również obsługiwane za pośrednictwem [interfejsu API programu Graph](https://docs.microsoft.com/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurowanie Privileged Identity Management dla ról usługi Azure AD

1. [Skonfiguruj ustawienia roli usługi Azure AD](pim-how-to-change-default-settings.md) na podstawie planowanych czynności.

1. Przejdź do **ról usługi Azure AD**, kliknij pozycję **role**, a następnie wybierz właśnie skonfigurowaną rolę.

1. W przypadku grupy użytkowników testowych, jeśli są już stałymi uprawnieniami administratora, możesz je przykwalifikować, wyszukując je i konwertując z trwałego na kwalifikujące się, klikając trzy kropki w ich wierszu. Jeśli nie mają jeszcze przypisań ról, można [utworzyć nowe kwalifikujące się przypisanie](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Powtórz kroki od 1-3 dla wszystkich ról, które chcesz przetestować.

1. Po skonfigurowaniu użytkowników testowych należy wysłać im link pozwalający [aktywować rolę usługi Azure AD](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-resource-roles"></a>Konfigurowanie Privileged Identity Management dla ról zasobów platformy Azure

1. [Skonfiguruj ustawienia roli zasobów platformy Azure](pim-resource-roles-configure-role-settings.md) dla roli w ramach subskrypcji lub zasobu, który chcesz przetestować.

1. Przejdź do **zasobów platformy Azure** dla tej subskrypcji, a następnie kliknij pozycję **role**, wybierz właśnie skonfigurowaną rolę.

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
    - [Aktywuj role zasobów platformy Azure](pim-resource-roles-activate-your-roles.md)
- Informacje kontaktowe lub pomoc techniczna dla wszystkich problemów związanych z usługą PIM

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby skonfigurować czas pracy z zespołem pomocy technicznej/wsparcia technicznego w celu przechodzenia przez proces Privileged Identity Managementy (Jeśli organizacja ma wewnętrzny zespół pomocy technicznej IT). Podaj im odpowiednie dokumenty oraz informacje kontaktowe.

### <a name="move-to-production"></a>Przenoszenie do środowiska produkcyjnego

Po zakończeniu testowania i pomyślnym przeniesieniu Privileged Identity Management do środowiska produkcyjnego, powtórz wszystkie kroki w fazach testowania dla wszystkich użytkowników każdej roli zdefiniowanej w konfiguracji Privileged Identity Management. W przypadku Privileged Identity Management dla ról usługi Azure AD organizacje często testują i wdrażają Privileged Identity Management dla administratorów globalnych przed testowaniem i wdrażaniem Privileged Identity Management dla innych ról. W tym czasie w przypadku zasobów platformy Azure zwykle testowanie i wdrażanie Privileged Identity Management jedną subskrypcję platformy Azure.

### <a name="in-the-case-a-rollback-is-needed"></a>W przypadku konieczności wycofania

Jeśli Privileged Identity Management nie zadziałała prawidłowo w środowisku produkcyjnym, następujące kroki wycofywania mogą pomóc w powrocie do znanego dobrego stanu przed rozpoczęciem konfigurowania Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Role usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **role**.
1. Dla każdej skonfigurowanej roli kliknij przycisk wielokropka (**...**) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Kliknij opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

#### <a name="azure-resource-roles"></a>Role zasobów platformy Azure

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **zasoby platformy Azure** , a następnie kliknij subskrypcję lub zasób, które chcesz przywrócić.
1. Kliknij pozycję **role**.
1. Dla każdej skonfigurowanej roli kliknij przycisk wielokropka (**...**) dla wszystkich użytkowników z uprawnionym przypisaniem.
1. Kliknij opcję **Utwórz trwale** , aby przypisać rolę jako trwałą.

## <a name="next-steps-after-deploying"></a>Następne kroki po wdrożeniu

Pomyślnie wdrożono Privileged Identity Management w środowisku produkcyjnym to znaczący krok w zakresie zabezpieczania tożsamości uprzywilejowanych w organizacji. Wdrożenie Privileged Identity Management obejmuje dodatkowe funkcje Privileged Identity Management, które powinny być używane na potrzeby zabezpieczeń i zgodności.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Korzystanie z alertów Privileged Identity Management w celu zabezpieczenia uprzywilejowanego dostępu

Aby lepiej chronić organizację, należy korzystać z wbudowanych funkcji alertów Privileged Identity Management. Aby uzyskać więcej informacji, zobacz [alerty zabezpieczeń](pim-how-to-configure-security-alerts.md#security-alerts). Te alerty obejmują: Administratorzy nie używają uprzywilejowanych ról, są przypisywane role poza Privileged Identity Management, role są aktywowane zbyt często i nie tylko. Aby w pełni chronić organizację, należy regularnie przechodzić przez listę alertów i rozwiązać problemy. Alerty można wyświetlać i usuwać w następujący sposób:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Otwórz **Azure AD Privileged Identity Management**.
1. Kliknij pozycję **role usługi Azure AD** , a następnie kliknij pozycję **alerty**.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby od razu zająć się wszystkimi alertami oznaczonymi o wysokiej ważności. W przypadku alertów o średniej i niskiej ważności użytkownik powinien otrzymywać informacje i wprowadzić zmiany, jeśli sądzisz, że istnieje zagrożenie bezpieczeństwa.

Jeśli którekolwiek z określonych alertów nie są przydatne lub nie mają zastosowania w organizacji, zawsze możesz odrzucić ten alert na stronie alertów. Można zawsze przywrócić ten odrzucanie później na stronie ustawień usługi Azure AD.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Skonfiguruj cykliczne przeglądy dostępu do regularnego przeprowadzania inspekcji tożsamości uprzywilejowanych w organizacji

Przeglądy dostępu są najlepszym sposobem na zaproszenie użytkowników przypisanych do ról uprzywilejowanych lub określonych recenzentów, czy każdy użytkownik musi mieć uprzywilejowaną tożsamość. Przeglądy dostępu są doskonałe, jeśli chcesz zmniejszyć obszar ataków i zachować zgodność. Aby uzyskać więcej informacji na temat rozpoczynania przeglądu dostępu, zobacz przeglądy [dostępu do ról usługi Azure AD](pim-how-to-start-security-review.md) oraz [przeglądy dostępu do ról zasobów platformy Azure](pim-resource-roles-start-access-review.md). W przypadku niektórych organizacji okresowe przeglądy dostępu są wymagane, aby zachować zgodność z przepisami i przepisami, a w przypadku innych, przegląd dostępu jest najlepszym sposobem wymuszania najniższych uprawnień w całej organizacji.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie kwartalnych przeglądów dostępu dla wszystkich ról usługi Azure AD i zasobów platformy Azure.

W większości przypadków, osoba dokonująca przeglądu dla ról usługi Azure AD jest właścicielem subskrypcji, w której znajduje się rola zasobów platformy Azure. Jednak często zdarza się, że firmy mają uprzywilejowane konta, które nie są połączone z żadnym z adresów e-mail poszczególnych osób. W takich przypadkach nikt nie odczytuje ani nie przegląda dostępu.

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** dodanie pomocniczego adresu e-mail dla wszystkich kont z przypisaniami ról uprzywilejowanych, które nie są połączone z regularnie sprawdzonym adresem e-mail.

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Maksymalnie zapoznaj się z dziennikiem inspekcji, aby zwiększyć bezpieczeństwo i zgodność

Dziennik inspekcji jest miejscem, w którym można zachować aktualność i być zgodne z przepisami. Privileged Identity Management obecnie przechowuje 30-dniową historię wszystkich historii organizacji w swoim dzienniku inspekcji, w tym:

- Aktywacja/dezaktywacja kwalifikujących się ról
- Działania przypisania roli wewnątrz i na zewnątrz Privileged Identity Management
- Zmiany w ustawieniach roli
- Działania żądania/zatwierdzania/odmowy dla aktywacji roli z konfiguracją zatwierdzania
- Aktualizowanie alertów

Możesz uzyskać dostęp do tych dzienników inspekcji, jeśli jesteś administratorem globalnym lub administratorem roli uprzywilejowanej. Aby uzyskać więcej informacji, zobacz [historię inspekcji dla ról usługi Azure AD](pim-how-to-use-audit-log.md) i [historii inspekcji dla ról zasobów platformy Azure](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** , aby co najmniej jeden administrator odczytał przez wszystkie zdarzenia inspekcji co tydzień i regularnie eksportować zdarzenia inspekcji.

Jeśli chcesz automatycznie przechowywać zdarzenia inspekcji przez dłuższy czas, Privileged Identity Management dziennik inspekcji zostanie automatycznie zsynchronizowany z dziennikami [inspekcji usługi Azure AD](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **firma Microsoft zaleca** skonfigurowanie [monitorowania dzienników platformy Azure](../reports-monitoring/concept-activity-logs-azure-monitor.md) do archiwizowania zdarzeń inspekcji na koncie usługi Azure Storage na potrzeby zabezpieczeń i zgodności.
