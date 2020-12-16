---
title: Azure DevTest Labs często zadawane pytania | Microsoft Docs
description: Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące Azure DevTest Labs.
ms.topic: article
ms.date: 07/17/2020
ms.openlocfilehash: 9fcdc160754822d5c6f22b7349d0e72f0cf22633
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590277"
---
# <a name="azure-devtest-labs-faq"></a>Azure DevTest Labs — często zadawane pytania
Uzyskaj odpowiedzi na niektóre często zadawane pytania dotyczące Azure DevTest Labs.

## <a name="blog-post"></a>Wpis w blogu
Nasz blog zespołu DevTest Labs został wycofany z 20 marca 2019. 

### <a name="where-can-i-track-feature-updates-from-now-on"></a>Gdzie mogę teraz śledzić aktualizacje funkcji?
Od teraz będziemy ogłaszać aktualizacje funkcji i wpisy w blogach w blogu platformy Azure i aktualizacjach platformy Azure. Te wpisy w blogu również łączą się z naszą dokumentacją wszędzie tam, gdzie jest to wymagane.

Zasubskrybuj [blog DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) i [DevTest Labs Azure](https://azure.microsoft.com/updates/?product=devtest-lab) , aby uzyskać informacje o nowych funkcjach w DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>Co się dzieje z istniejącymi wpisami w blogu?
Obecnie pracujemy nad migrowaniem istniejących wpisów w blogu (z wyjątkiem aktualizacji przestoju) do naszej [dokumentacji DevTest Labs](devtest-lab-overview.md). Gdy blog MSDN jest przestarzały, zostanie on przekierowany do omówienia dokumentacji dotyczącej usługi DevTest Labs. Po przekierowaniu można wyszukać szukany artykuł w tytule "Filtruj według". Nie przeprowadzono jeszcze wszystkich wpisów, ale powinny one zostać wykonane z końcem tego miesiąca. 


### <a name="where-do-i-see-outage-updates"></a>Gdzie mogę zobaczyć aktualizacje przestoju?
Będziemy ogłaszać aktualizacje przestojów przy użyciu naszego uchwytu Twitter od razu. Zapoznaj się z nami w serwisie Twitter, aby uzyskać najnowsze aktualizacje dotyczące awarii i znanych usterek.

### <a name="twitter"></a>Twitter
Nasz uchwyt usługi Twitter: [@azlabservices](https://twitter.com/azlabservices)

## <a name="general"></a>Ogólne
### <a name="what-if-my-question-isnt-answered-here"></a>Co zrobić, jeśli w tym miejscu nie udzielono odpowiedzi na moje pytanie?
Jeśli pytania nie ma na liście, daj nam znać, aby pomóc Ci znaleźć odpowiedź.

- Opublikuj pytanie na końcu tego często zadawanych pytań.
- Aby dotrzeć do szerszego grona użytkowników, Opublikuj pytanie na [stronie pytania&](/answers/topics/azure-devtestlabs.html)pytań i odpowiedzi dla Azure DevTest Labs. Zaangażuj się z zespołem Azure DevTest Labs i innymi członkami społeczności.
- W przypadku żądań funkcji Prześlij swoje żądania i pomysły dotyczące [Azure DevTest Labs głosu użytkownika](https://feedback.azure.com/forums/320373-azure-devtest-labs).

### <a name="what-is-a-microsoft-account"></a>Co to jest konto Microsoft?
Konto Microsoft to konto, którego używasz do niemal wszystkiego, czego potrzebujesz od urządzeń i usług firmy Microsoft. Jest to adres e-mail i hasło używane do logowania się do aplikacji Skype, Outlook.com, OneDrive, Windows Phone, Azure i Xbox Live. Pojedyncze konto oznacza, że pliki, zdjęcia, kontakty i ustawienia mogą się pojawić na dowolnym urządzeniu.

> [!NOTE]
> Konto Microsoft używany do nazywania identyfikatora Windows Live ID.

### <a name="why-should-i-use-azure-devtest-labs"></a>Dlaczego warto używać Azure DevTest Labs?
Azure DevTest Labs może zaoszczędzić czas i pieniądze zespołu. Deweloperzy mogą tworzyć własne środowiska przy użyciu kilku różnych baz. Mogą również używać artefaktów do szybkiego wdrażania i konfigurowania aplikacji. Używając niestandardowych obrazów i formuł, można zapisywać maszyny wirtualne jako szablony i łatwo je odtworzyć w zespole. Program DevTest Labs oferuje także kilka konfigurowalnych zasad, które mogą być używane przez administratorów laboratorium do zmniejszania ilości odpadów i zarządzania środowiskami zespołu. Te zasady obejmują automatyczne zamykanie, próg kosztów, maksymalną liczbę maszyn wirtualnych na użytkownika i maksymalny rozmiar maszyny wirtualnej. Dokładniejsze wyjaśnienie usługi DevTest Labs można znaleźć w temacie [Omówienie](devtest-lab-overview.md) lub wprowadzenie [filmu wideo](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

### <a name="what-does-worry-free-self-service-mean"></a>Co oznacza "nieodpłatne samoobsługowe"?
Samoobsługowe bez obaw oznacza, że deweloperzy i testerzy tworzą własne środowiska w miarę potrzeb. Administratorzy mają świadomość, że DevTest Labs mogą pomóc w określeniu odpowiedniego dostępu, zminimalizowaniu strat i kontroli kosztów. Administratorzy mogą określić, które rozmiary maszyn wirtualnych są dozwolone, maksymalną liczbę maszyn wirtualnych oraz czas uruchamiania i wyłączania maszyn wirtualnych. DevTest Labs ułatwiają również monitorowanie kosztów i Ustawianie alertów, aby pomóc w zaistnieniu sposobu używania zasobów laboratorium.

### <a name="how-can-i-use-devtest-labs"></a>Jak mogę korzystać z DevTest Labs?
DevTest Labs jest przydatne w każdym przypadku, gdy wymagane są środowiska deweloperskie lub testowe, i chcesz je szybko odtworzyć lub zarządzać nimi przy użyciu zasad oszczędności kosztów.
Poniżej przedstawiono scenariusze, do których nasi klienci używają DevTest Labs:

- Zarządzaj środowiskami deweloperskimi i testowymi w jednym miejscu. Użyj zasad, aby obniżyć koszty i utworzyć niestandardowe obrazy w celu udostępniania kompilacji w zespole.
- Opracowywanie aplikacji przy użyciu obrazów niestandardowych w celu zapisania stanu dysku w ramach etapów tworzenia.
- Śledź koszty w stosunku do postępu.
- Twórz środowiska testów masy do testowania zapewniania jakości.
- Użyj artefaktów i formuł, aby łatwo skonfigurować i odtworzyć aplikację w różnych środowiskach.
- Dystrybuuj maszyny wirtualne pod kątem imprezy rozpoczynają (do współpracy deweloperskiej lub testowej), a następnie łatwo Wycofaj ich obsługę po zakończeniu zdarzenia.

### <a name="how-am-i-billed-for-devtest-labs"></a>Jak naliczane są opłaty za DevTest Labs?
DevTest Labs to bezpłatna usługa. Tworzenie laboratoriów oraz Konfigurowanie zasad, szablonów i artefaktów w usłudze DevTest Labs jest bezpłatne. Płacisz tylko za zasoby platformy Azure używane w laboratoriach, takie jak maszyny wirtualne, konta magazynu i sieci wirtualne. Aby uzyskać więcej informacji na temat kosztów zasobów laboratorium, zobacz [Azure DevTest Labs Cennik](https://azure.microsoft.com/pricing/details/devtest-lab/).

## <a name="security"></a>Zabezpieczenia

### <a name="what-are-the-different-security-levels-in-devtest-labs"></a>Jakie są różne poziomy zabezpieczeń w DevTest Labs?
Dostęp zabezpieczeń jest określany przez kontrolę dostępu opartą na rolach (Azure RBAC). Aby dowiedzieć się, jak działa program Access, można poznać różnice między uprawnieniami, rolą i zakresem zdefiniowanym przez funkcję RBAC platformy Azure.

- **Uprawnienie**: uprawnienie jest zdefiniowany dostęp do konkretnej akcji. Na przykład uprawnienie może mieć dostęp do odczytu do wszystkich maszyn wirtualnych.
- **Rola**: rola to zestaw uprawnień, które mogą być pogrupowane i przypisywane do użytkownika. Na przykład użytkownik z rolą właściciela subskrypcji ma dostęp do wszystkich zasobów w ramach subskrypcji.
- **Zakres**: zakres jest poziomem w hierarchii zasobu platformy Azure. Na przykład zakres może być grupą zasobów, jednym laboratorium lub całą subskrypcją.

W zakresie DevTest Labs istnieją dwa typy ról, które definiują uprawnienia użytkownika:

- **Właściciel laboratorium**: właściciel laboratorium ma dostęp do wszystkich zasobów w laboratorium. Właściciel laboratorium może modyfikować zasady, odczytywać i zapisywać na wszystkich maszynach wirtualnych, zmieniać sieć wirtualną i tak dalej.
- **Użytkownik laboratorium**: Użytkownik laboratorium może wyświetlać wszystkie zasoby laboratorium, takie jak maszyny wirtualne, zasady i sieci wirtualne. Jednak użytkownik laboratorium nie może modyfikować zasad ani żadnych maszyn wirtualnych, które zostały utworzone przez innych użytkowników.

Role niestandardowe można także tworzyć w DevTest Labs. Aby dowiedzieć się, jak utworzyć role niestandardowe w DevTest Labs, zobacz [udzielanie uprawnień użytkownika do określonych zasad laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Ponieważ zakresy są hierarchiczne, gdy użytkownik ma uprawnienia w określonym zakresie, użytkownik ma automatycznie przyznane te uprawnienia dla każdego zakresu niższego poziomu w zakresie. Na przykład jeśli użytkownik ma przypisaną rolę właściciela subskrypcji, użytkownik ma dostęp do wszystkich zasobów w ramach subskrypcji. Te zasoby obejmują maszyny wirtualne, sieci wirtualne i laboratoria. Właściciel subskrypcji automatycznie dziedziczy rolę właściciela laboratorium. Jednak przeciwieństwo nie jest prawdziwe. Właściciel laboratorium ma dostęp do laboratorium, który jest niższym zakresem niż poziom subskrypcji. Dlatego właściciel laboratorium nie widzi maszyn wirtualnych, sieci wirtualnych ani innych zasobów poza laboratorium.

### <a name="how-do-i-define-azure-role-based-access-control-for-my-devtest-labs-environments-to-ensure-that-it-can-govern-while-developerstest-can-do-their-work"></a>Jak mogę zdefiniować kontrolę dostępu opartą na rolach na platformie Azure dla środowisk DevTest Labs, aby mieć pewność, że może ona być zarządzana, gdy deweloperzy/testy mogą wykonywać swoje prace?
Istnieje szeroki wzorzec, jednak szczegóły są zależne od organizacji.

Centralna powinna być tylko to, co jest niezbędne, i umożliwia zespołom projektu i aplikacji posiadanie wymaganego poziomu kontroli. Zazwyczaj oznacza to, że Central IT jest właścicielem subskrypcji i obsługuje podstawowe funkcje IT, takie jak konfiguracje sieci. Zestaw **właścicieli** dla subskrypcji powinien być niewielki. Tacy właściciele mogą mianować dodatkowych właścicieli w razie potrzeby lub zastosować zasady na poziomie subskrypcji, na przykład "Brak publicznego adresu IP".

Może istnieć podzbiór użytkowników, którzy wymagają dostępu między subskrypcjami, takimi jak pomoc lub obsługa warstwy 2. W tym przypadku zalecamy nadanie tym użytkownikom dostępu **współautora** , aby mogli zarządzać zasobami, ale nie zapewniają dostępu użytkowników ani dostosowywać zasad.

Zasób DevTest Labs powinien należeć do właścicieli, którzy znajdują się blisko zespołu projektu/aplikacji. Jest to spowodowane tym, że rozumieją one wymagania dotyczące maszyn i wymaganego oprogramowania. W większości organizacji właściciel tego zasobu DevTest Labs jest często liderem projektu/projektowania. Ten właściciel może zarządzać użytkownikami i zasadami w środowisku laboratoryjnym i może zarządzać wszystkimi maszynami wirtualnymi w środowisku DevTest Labs.

Członków zespołu projektu/aplikacji należy dodać do roli **użytkownika DevTest Labs** . Ci użytkownicy mogą tworzyć maszyny wirtualne (w trybie online z zasadami dotyczącymi poziomu laboratorium i subskrypcji). Mogą również zarządzać własnymi maszynami wirtualnymi. Nie mogą oni zarządzać maszynami wirtualnymi należącymi do innych użytkowników.

Aby uzyskać więcej informacji, zobacz artykuł [Azure Enterprise szkielet — dokumentacja dotycząca nadzoru subskrypcji](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


### <a name="how-do-i-create-a-role-to-allow-users-to-do-a-specific-task"></a>Jak mogę utworzyć rolę, aby umożliwić użytkownikom wykonywanie określonych zadań?
Aby uzyskać kompleksowy artykuł dotyczący tworzenia ról niestandardowych i przypisywania uprawnień do roli, zobacz [udzielanie uprawnień użytkownika do określonych zasad laboratorium](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Oto przykład skryptu tworzącego **użytkownika zaawansowanego roli DevTest Labs**, który ma uprawnienia do uruchamiania i zatrzymywania wszystkich maszyn wirtualnych w laboratorium:


```powershell
$policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
$policyRoleDef.Id = $null
$policyRoleDef.Name = "DevTest Labs Advanced User"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
$policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  
```

### <a name="how-can-an-organization-ensure-corporate-security-policies-are-in-place"></a>Jak organizacja może zapewnić, że zasady zabezpieczeń firmy są stosowane?
Organizacja może ją osiągnąć, wykonując następujące czynności:

- Opracowywanie i publikowanie kompleksowych zasad zabezpieczeń. Zasady te są połączone z regułami akceptowalnego zastosowania skojarzonymi z oprogramowaniem, zasobami w chmurze. Definiuje również, co jasno narusza zasady.
- Opracowywanie obrazu niestandardowego, artefaktów niestandardowych i procesu wdrażania, który umożliwia aranżację w obszarze zabezpieczeń zdefiniowanym za pomocą usługi Active Directory. To podejście wymusza granicę firmy i ustawia wspólny zestaw kontrolek środowiska. Te kontrolki względem środowiska, które deweloperzy mogą rozważyć podczas opracowywania i postępują zgodnie z bezpiecznym cyklem rozwoju w ramach całego procesu. Celem jest również zapewnienie środowiska, które nie jest nadmiernie restrykcyjne, co może utrudnić rozwój, ale rozsądny zestaw kontrolek. Zasady grupy w jednostce organizacyjnej (OU) zawierające maszyny wirtualne laboratorium mogą być podzbiorem łącznej liczby zasad grupy, które znajdują się w środowisku produkcyjnym. Alternatywnie mogą być dodatkowym zestawem, aby prawidłowo ograniczyć określone zagrożenia.

### <a name="how-can-an-organization-ensure-data-integrity-to-ensure-that-remoting-developers-cant-remove-code-or-introduce-malware-or-unapproved-software"></a>W jaki sposób organizacja zapewnia integralność danych, aby zapewnić, że deweloperzy zdalni nie mogą usunąć kodu ani wprowadzić złośliwego oprogramowania czy niezatwierdzonym oprogramowaniem?
Istnieje kilka warstw kontroli, które ograniczają zagrożenia od zewnętrznych konsultantów, wykonawców lub pracowników, którzy są usługami zdalnymi w programie w celu współpracy w DevTest Labs.

Jak wspomniano wcześniej, pierwszy krok musi mieć zaprojektowaną i zdefiniowaną zasadę akceptowalnego zastosowania, która jasno podkreśla konsekwencje, gdy ktoś narusza zasady.

Pierwszą warstwą kontroli dostępu zdalnego jest zastosowanie zasad dostępu zdalnego za pośrednictwem połączenia sieci VPN, które nie jest bezpośrednio połączone z laboratorium.

Druga warstwa formantów polega na zastosowaniu zestawu obiektów zasad grupy, które uniemożliwiają kopiowanie i wklejanie za pomocą pulpitu zdalnego. Można zaimplementować zasady sieciowe, aby nie zezwalać na usługi wychodzące ze środowiska, takiego jak usługi FTP i RDP poza środowiskiem. Routing zdefiniowany przez użytkownika może wymusić powrót całego ruchu sieciowego platformy Azure z powrotem do lokalnego, ale Routing nie mógł uwzględnić wszystkich adresów URL, które mogą zezwalać na przekazywanie danych, chyba że są one kontrolowane za pomocą serwera proxy w celu skanowania zawartości i sesji. Publiczne adresy IP mogą być ograniczone w sieci wirtualnej wspierającej DevTest Labs, aby nie zezwalały na mostkowanie zewnętrznego zasobu sieciowego.

W rezultacie tego samego typu ograniczenia należy zastosować w całej organizacji, która będzie uwzględniać wszystkie możliwe metody nośników wymiennych lub zewnętrznych adresów URL, które mogą akceptować ogłoszenie zawartości. Zapoznaj się z specjalistą ds. zabezpieczeń, aby przejrzeć i wdrożyć zasady zabezpieczeń. Aby uzyskać więcej zaleceń, zobacz [Microsoft cybernetycznymi Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).

## <a name="lab-configuration"></a>Konfiguracja laboratorium

### <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>Jak mogę utworzyć laboratorium na podstawie szablonu Menedżer zasobów?
Oferujemy [repozytorium GitHub Azure Resource Manager szablonów](https://azure.microsoft.com/resources/templates/101-dtl-create-lab) , które można wdrożyć jako-lub zmodyfikować, aby utworzyć szablony niestandardowe dla laboratoriów. Każdy szablon zawiera link umożliwiający wdrożenie laboratorium w ramach własnej subskrypcji platformy Azure. Można też dostosować szablon i [wdrożyć go przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-powershell.md).


### <a name="can-i-have-all-virtual-machines-to-be-created-in-a-common-resource-group-instead-having-each-machine-in-its-own-resource-group"></a>Czy można utworzyć wszystkie maszyny wirtualne w ramach wspólnej grupy zasobów, a nie każdy komputer w jego własnej grupie zasobów?
Tak, jako właściciel laboratorium, możesz zezwolić na alokację grupy zasobów dla laboratorium lub mieć wszystkie maszyny wirtualne utworzone w ramach określonej grupy zasobów.

Scenariusz oddzielnej grupy zasobów:
-   DevTest Labs tworzy nową grupę zasobów dla wszystkich publicznych/prywatnych maszyn wirtualnych IP, które można utworzyć
-   DevTest Labs tworzy grupę zasobów dla udostępnionych komputerów IP, które należą do tego samego rozmiaru.

Scenariusz typowej grupy zasobów:
-   Wszystkie maszyny wirtualne są w tej samej grupie zasobów, którą określisz. Dowiedz się więcej o [alokacji grupy zasobów dla laboratorium](./resource-group-control.md).

### <a name="how-do-i-maintain-a-naming-convention-across-my-devtest-labs-environment"></a>Jak mogę zachować konwencję nazewnictwa w środowisku DevTest Labs?
Możesz chcieć przyciągnąć bieżące konwencje nazewnictwa przedsiębiorstwa do operacji platformy Azure i zapewnić ich spójność w środowisku DevTest Labs. Zalecamy, aby podczas wdrażania DevTest Labs były określone zasady uruchamiania. Te zasady są wdrażane za pomocą centralnego skryptu i szablonów JSON w celu wymuszenia spójności. Zasady nazewnictwa można zaimplementować za pomocą zasad platformy Azure, które są stosowane na poziomie subskrypcji. Aby uzyskać przykłady JSON dla Azure Policy, zobacz [Azure Policy Samples](../governance/policy/samples/index.md).

### <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>Ile laboratoriów można utworzyć w ramach tej samej subskrypcji?
Nie ma określonego limitu liczby laboratoriów, które można utworzyć na subskrypcję. Ilość używanych zasobów na subskrypcję jest jednak ograniczona. Możesz zapoznać się z [limitami i przydziałami dla subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) oraz [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).


### <a name="how-many-vms-can-i-create-per-lab"></a>Ile maszyn wirtualnych można utworzyć na laboratorium?
Nie ma określonego limitu liczby maszyn wirtualnych, które można utworzyć dla laboratorium. Jednak zasoby (rdzenie maszyn wirtualnych, publiczne adresy IP itp.), które są używane, są ograniczone na subskrypcję. Możesz zapoznać się z [limitami i przydziałami dla subskrypcji platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md) oraz [jak zwiększyć te limity](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

### <a name="how-do-i-determine-the-ratio-of-users-per-lab-and-the-overall-number-of-labs-that-are-needed-across-an-organization"></a>Jak mogę określić stosunek użytkowników na laboratorium i ogólną liczbę laboratoriów, które są w danej organizacji?
Zalecamy, aby jednostki biznesowe i grupy programistyczne skojarzone z tym samym projektem programistycznym były skojarzone z tym samym laboratorium. Umożliwia zastosowanie tych samych typów zasad, obrazów i zasad zamykania do obu grup.

Może być również konieczne rozważenie granic geograficznych. Na przykład deweloperzy w regionie Północno-środkowe Stany Zjednoczone (USA) mogą używać laboratorium zainicjowanego w regionie Wschodnie stany USA 2. Deweloperzy w Dallas, Texas i Denver mogą być kierowani do używania zasobów w Południowo-środkowe stany USA. W przypadku współpracy z zewnętrznymi osobami trzecimi mogą zostać przypisane do laboratorium, które nie jest używane przez wewnętrznych deweloperów.

Możesz również użyć laboratorium dla określonego projektu w Azure DevOps Projects. Następnie należy zastosować zabezpieczenia za pomocą określonej grupy Azure Active Directory, co umożliwia dostęp do obu zestawów zasobów. Sieć wirtualna przypisana do laboratorium może być kolejną granicą, aby skonsolidować użytkowników.

### <a name="how-can-we-prevent-the-deletion-of-resources-within-a-lab"></a>Jak zapobiegamy usunięciu zasobów w ramach laboratorium?
Zaleca się, aby ustawić odpowiednie uprawnienia na poziomie laboratorium, tak aby tylko autoryzowani użytkownicy mogli usuwać zasoby lub zmieniać zasady laboratorium. Deweloperzy powinni być umieszczani w grupie **użytkowników DevTest Labs** . Deweloperem potencjalnego klienta lub liderem infrastruktury powinien być **właściciel DevTest Labs**. Zalecamy posiadanie tylko dwóch właścicieli laboratorium. Te zasady rozszerzają się do repozytorium kodu w celu uniknięcia uszkodzenia. Użytkownicy laboratorium mają prawa do używania zasobów, ale nie mogą aktualizować zasad laboratorium. Zapoznaj się z poniższym artykułem zawierającym listę ról i praw, które każda wbudowana grupa ma w laboratorium: [Dodawanie właścicieli i użytkowników w Azure DevTest Labs](devtest-lab-add-devtest-user.md).

### <a name="how-do-i-share-a-direct-link-to-my-lab"></a>Jak mogę udostępniać bezpośredni link do mojego laboratorium?

1. W [Azure Portal](https://portal.azure.com)przejdź do laboratorium.
2. Skopiuj **adres URL laboratorium** z przeglądarki, a następnie Udostępnij go użytkownikom laboratorium.

> [!NOTE]
> Jeśli użytkownik laboratorium jest użytkownikiem zewnętrznym, który ma konto Microsoft, ale nie jest członkiem wystąpienia Active Directory organizacji, podczas próby uzyskania dostępu do linku udostępnionego użytkownik może wyświetlić komunikat o błędzie. Jeśli użytkownik zewnętrzny zobaczy komunikat o błędzie, należy polecić użytkownikowi wybranie jego nazwy w prawym górnym rogu Azure Portal. Następnie w sekcji katalog menu użytkownik może wybrać katalog, w którym istnieje laboratorium.

## <a name="virtual-machines"></a>Maszyny wirtualne

### <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>Dlaczego nie widzę maszyn wirtualnych na stronie Virtual Machines, która jest widoczna w DevTest Labs?
Podczas tworzenia maszyny wirtualnej w DevTest Labs masz uprawnienia dostępu do tej maszyny wirtualnej. Możesz wyświetlić maszynę wirtualną na stronie laboratoria i na stronie **Virtual Machines** . Użytkownicy przypisani do roli **właściciela DevTest Labs** mogą zobaczyć wszystkie maszyny wirtualne, które zostały utworzone w laboratorium, na stronie **wszystkie Virtual Machines** . Jednak użytkownicy z rolą **użytkownika DevTest Labs** nie otrzymują automatycznie dostępu do odczytu do zasobów maszyny wirtualnej utworzonych przez innych użytkowników. W związku z tym te maszyny wirtualne nie są wyświetlane na stronie **Virtual Machines** .


### <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>Jak mogę utworzyć wiele maszyn wirtualnych na podstawie tego samego szablonu jednocześnie?
Dostępne są dwie opcje umożliwiające jednoczesne utworzenie wielu maszyn wirtualnych na podstawie tego samego szablonu:

- Możesz użyć [rozszerzenia zadań DevOps platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
- [Szablon Menedżer zasobów można wygenerować](devtest-lab-add-vm.md#save-azure-resource-manager-template) podczas tworzenia maszyny wirtualnej i [wdrożyć szablon Menedżer zasobów z programu Windows PowerShell](../azure-resource-manager/templates/deploy-powershell.md).
- Można również określić więcej niż jedno wystąpienie maszyny do utworzenia podczas tworzenia maszyny wirtualnej. Aby dowiedzieć się więcej o tworzeniu wielu wystąpień maszyn wirtualnych, zobacz dokument dotyczący [tworzenia maszyny wirtualnej laboratorium](devtest-lab-add-vm.md).

### <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>Jak mogę przenieść moje istniejące maszyny wirtualne platformy Azure do mojego laboratorium DevTest Labs?
Aby skopiować istniejące maszyny wirtualne do DevTest Labs:

1.  Skopiuj plik VHD istniejącej maszyny wirtualnej za pomocą [skryptu programu Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/samples/DevTestLabs/Scripts/CopyVirtualMachines/CopyAzVHDFromVMToLab.ps1).
2.  Utwórz [niestandardowy obraz](devtest-lab-create-template.md) w laboratorium DevTest Labs.
3.  Utwórz maszynę wirtualną w laboratorium z obrazu niestandardowego.

### <a name="can-i-attach-multiple-disks-to-my-vms"></a>Czy mogę dołączyć wiele dysków do maszyn wirtualnych?

Tak, możesz dołączyć wiele dysków do maszyn wirtualnych.

### <a name="are-gen-2-images-supported-by-devtest-labs"></a>Czy są to obrazy generacji 2 obsługiwane przez DevTest Labs?
Tak. Usługa DevTest Labs obsługuje [obrazy generacji 2](../virtual-machines/generation-2.md). Jeśli jednak dla obrazu są dostępne wersje Gen 1 i Gen 2, DevTest Labs wyświetla tylko wersję Gen 1 obrazu podczas tworzenia maszyny wirtualnej. Obraz zostanie wyświetlony, jeśli dostępna jest tylko wersja 2 generacji. 

### <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Jeśli chcę używać obrazu systemu operacyjnego Windows do testowania, czy muszę kupić subskrypcję MSDN?
Aby użyć obrazów systemu operacyjnego klienta systemu Windows (systemu Windows 7 lub nowszej wersji) do programowania lub testowania na platformie Azure, wykonaj jedną z następujących czynności:

- [Kup subskrypcję MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Jeśli masz Umowa Enterprise, Utwórz subskrypcję platformy Azure z [ofertą Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p).

Aby uzyskać więcej informacji na temat środków na korzystanie z platformy Azure dla każdej oferty MSDN, zobacz [comiesięczne kredyty na korzystanie z platformy Azure dla subskrybentów Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).


### <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>Jak mogę zautomatyzować proces usuwania wszystkich maszyn wirtualnych w moim laboratorium?
Jako właściciel laboratorium możesz usunąć maszyny wirtualne z laboratorium w Azure Portal. Możesz również usunąć wszystkie maszyny wirtualne w laboratorium przy użyciu skryptu programu PowerShell. W poniższym przykładzie w obszarze wartości, **Aby zmienić** komentarz Zmodyfikuj wartości parametrów. Możesz pobrać `subscriptionId` `labResourceGroup` wartości, i `labName` z okienka laboratorium w Azure Portal.

```powershell
# Delete all the VMs in a lab.

# Values to change:
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource group here>"
$labName = "<Enter lab name here>"

# Sign in to your Azure account.
Connect-AzAccount

# Select the Azure subscription that has the lab. This step is optional
# if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab that has the VMs that you want to delete.
$lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Get the VMs from that lab.
$labVMs = Get-AzResource | Where-Object {
          $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
          $_.Name -like "$($lab.Name)/*"}

# Delete the VMs.
foreach($labVM in $labVMs)
{
    Remove-AzResource -ResourceId $labVM.ResourceId -Force
}
```

## <a name="environments"></a>Środowiska

### <a name="how-can-i-use-resource-manager-templates-in-my-devtest-labs-environment"></a>Jak mogę używać szablonów Menedżer zasobów w środowisku DevTest Labs?
Szablony Menedżer zasobów można wdrożyć w środowisku DevTest Labs, wykonując kroki opisane w [funkcji środowiska w artykule DevTest Labs](devtest-lab-test-env.md) . Zasadniczo możesz sprawdzić szablony Menedżer zasobów w repozytorium git (Azure Repos lub GitHub) i dodać [prywatne repozytorium dla szablonów](devtest-lab-test-env.md) do laboratorium. Ten scenariusz może nie być użyteczny, jeśli używasz laboratoriów DevTest Labs do hostowania maszyn deweloperskich, ale mogą być przydatne, jeśli tworzysz środowisko przejściowe, które jest reprezentatywne dla produkcji.

Warto również zauważyć, że liczba maszyn wirtualnych na laboratorium lub dla poszczególnych użytkowników ogranicza liczbę maszyn utworzonych w sposób macierzysty w laboratorium, a nie w środowiskach (Menedżer zasobów szablonów).

## <a name="custom-images"></a>Obrazy niestandardowe

### <a name="how-can-i-set-up-an-easily-repeatable-process-to-bring-my-custom-organizational-images-into-a-devtest-labs-environment"></a>Jak mogę skonfigurować łatwy do powtórzenia proces, aby przenieść niestandardowe obrazy organizacyjne do środowiska DevTest Labs?
Obejrzyj ten [film wideo w wzorcu fabryki obrazu](https://sec.ch9.ms/ch9/8e8a/9ea0b8d4-b803-4f23-bca4-4808d9368e8a/dtlimagefactory_mid.mp4). Ten scenariusz jest zaawansowanym scenariuszem, a udostępniane skrypty są tylko przykładowymi skryptami. Jeśli wymagane są jakiekolwiek zmiany, należy zarządzać i obsługiwać skrypty używane w danym środowisku.

Aby uzyskać szczegółowe informacje na temat tworzenia fabryki obrazu, zobacz [Tworzenie niestandardowej fabryki obrazów w Azure DevTest Labs](image-factory-create.md).

### <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>Jaka jest różnica między obrazem niestandardowym a formułą?
Obraz niestandardowy jest obrazem zarządzanym. Formuła jest obrazem, który można skonfigurować przy użyciu dodatkowych ustawień, a następnie zapisać i odtworzyć. Obraz niestandardowy może być preferowany, jeśli chcesz szybko utworzyć kilka środowisk przy użyciu tego samego podstawowego, niezmiennego obrazu. Formuła może być lepszym rozwiązaniem, jeśli chcesz odtworzyć konfigurację maszyny wirtualnej z najnowszymi bitami w ramach sieci wirtualnej lub podsieci lub jako maszynę wirtualną o określonym rozmiarze. Dokładniejsze wyjaśnienie można znaleźć w temacie [porównanie niestandardowych obrazów i formuł w DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

### <a name="when-should-i-use-a-formula-vs-custom-image"></a>Kiedy należy używać formuły a obrazu niestandardowego?
Typowym czynnikiem decydującym w tym scenariuszu jest koszt i ponowne użycie. Jeśli masz scenariusz, w którym wielu użytkowników/laboratoriów wymagają obrazu z dużą ilością oprogramowania na obrazie podstawowym, możesz obniżyć koszty, tworząc obraz niestandardowy. Oznacza to, że obraz jest tworzony jeden raz. Skraca on czas instalacji maszyny wirtualnej i koszt ponoszony z powodu maszyny wirtualnej uruchomionej, gdy wystąpi konfiguracja.

Jednak dodatkowym czynnikiem, który należy zwrócić uwagę, jest częstotliwość wprowadzania zmian w pakiecie oprogramowania. Jeśli uruchamiasz codzienne kompilacje i chcesz, aby oprogramowanie było wymagane na maszynach wirtualnych użytkowników, rozważ użycie formuły zamiast obrazu niestandardowego.

Dokładniejsze wyjaśnienie można znaleźć w temacie [porównanie niestandardowych obrazów i formuł](devtest-lab-comparing-vm-base-image-types.md) w DevTest Labs.

### <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>Jak mogę zautomatyzować proces przekazywania plików VHD w celu utworzenia obrazów niestandardowych?

Aby zautomatyzować przekazywanie plików VHD do tworzenia obrazów niestandardowych, dostępne są dwie opcje:

- Użyj [AzCopy](../storage/common/storage-use-azcopy-v10.md) do kopiowania lub przekazywania plików VHD do konta magazynu skojarzonego z laboratorium.
- Użyj [Eksplorator usługi Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Eksplorator usługi Storage to autonomiczna aplikacja działająca w systemach Windows, OS X i Linux.

Aby znaleźć docelowe konto magazynu skojarzone z Twoim laboratorium:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2.  Z menu po lewej stronie wybierz pozycję **grupy zasobów**.
3.  Znajdź i wybierz grupę zasobów, która jest skojarzona z laboratorium.
4.  W obszarze **Przegląd** wybierz jedno z kont magazynu.
5.  Wybierz **obiekty blob**.
6.  Wyszukaj operacje przekazywania na liście. Jeśli nie istnieje, Wróć do kroku 4 i spróbuj użyć innego konta magazynu.
7.  Użyj **adresu URL** jako miejsca docelowego w poleceniu AzCopy.

Kiedy należy używać obrazu portalu Azure Marketplace a własnego niestandardowego obrazu organizacyjnego?

### <a name="when-should-i-use-an-azure-marketplace-image-vs-my-own-custom-organizational-image"></a>Kiedy należy używać obrazu portalu Azure Marketplace a własnego niestandardowego obrazu organizacyjnego?
Portal Azure Marketplace powinien być używany domyślnie, chyba że istnieją konkretne problemy lub wymagania organizacyjne. Typowe przykłady to:

- Złożona konfiguracja oprogramowania, która wymaga dołączenia aplikacji jako części obrazu podstawowego.
- Instalacja i konfiguracja aplikacji może zająć wiele godzin, co nie pozwala na dodanie czasu obliczeń do obrazu portalu Azure Marketplace.
- Deweloperzy i testerzy wymagają szybkiego dostępu do maszyny wirtualnej i chcą zminimalizować czas instalacji nowej maszyny wirtualnej.
- Warunki zgodności lub przepisy prawne (na przykład zasady zabezpieczeń), które muszą być stosowane dla wszystkich maszyn.
- Używanie obrazów niestandardowych nie powinno być uznawane za jasne. Wprowadzają one dodatkową złożoność, ponieważ teraz trzeba zarządzać plikami VHD dla tych podstawowych obrazów bazowych. Należy również regularnie uaktualniać te obrazy podstawowe przy użyciu aktualizacji oprogramowania. Aktualizacje te obejmują nowe aktualizacje systemu operacyjnego, a także wszelkie aktualizacje lub zmiany konfiguracji, które są potrzebne dla samego pakietu oprogramowania.

## <a name="artifacts"></a>Artifacts

### <a name="what-are-artifacts"></a>Co to są artefakty?
Artefakty to dostosowywalne elementy, których można użyć do wdrożenia najnowszych bitów lub wdrożenia narzędzi deweloperskich na maszynie wirtualnej. Dołącz artefakty do maszyny wirtualnej podczas tworzenia maszyny wirtualnej. Po aprowizacji maszyny wirtualnej artefakty wdrażają i konfigurują maszynę wirtualną. Różne istniejące już artefakty są dostępne w naszym [publicznym repozytorium GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). Możesz również [tworzyć własne artefakty](devtest-lab-artifact-author.md).

### <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Wystąpił błąd mojego artefaktu podczas tworzenia maszyny wirtualnej. Jak mogę rozwiązać ten problem?
Aby dowiedzieć się, jak uzyskać dzienniki artefaktu zakończonego niepowodzeniem, zobacz [Jak zdiagnozować błędy artefaktów w DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md).

### <a name="when-should-an-organization-use-a-public-artifact-repository-vs-private-artifact-repository-in-devtest-labs"></a>Kiedy organizacja ma używać publicznego repozytorium artefaktów a repozytorium artefaktów prywatnych w usłudze DevTest Labs?
[Publiczne repozytorium artefaktów](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) zawiera początkowy zestaw pakietów oprogramowania, które są najczęściej używane. Pomaga w szybkim wdrożeniu bez konieczności inwestowania w odtworzenie popularnych narzędzi programistycznych i dodatków. Możesz wybrać wdrożenie własnego repozytorium prywatnego. Można równolegle używać publicznego i prywatnego repozytorium. Możesz również wyłączyć repozytorium publiczne. Kryteria służące do wdrażania repozytorium prywatnego powinny mieć następujące pytania i zagadnienia:

- Czy organizacja ma wymóg posiadania licencjonowanego oprogramowania firmowego jako części swojej oferty DevTest Labs? Jeśli odpowiedź ma wartość tak, należy utworzyć prywatne repozytorium.
- Czy organizacja opracowuje niestandardowe oprogramowanie, które zapewnia konkretną operację, która jest wymagana w ramach ogólnego procesu aprowizacji? Jeśli odpowiedź ma wartość tak, należy wdrożyć prywatne repozytorium.
- Jeśli zasady ładu organizacji wymagają izolacji, a zewnętrzne repozytoria nie podlegają bezpośrednio zarządzaniu konfiguracją przez organizację, należy wdrożyć prywatne repozytorium artefaktów. W ramach tego procesu można skopiować początkową kopię repozytorium publicznego i zintegrować ją z repozytorium prywatnym. Następnie repozytorium publiczne można wyłączyć, aby nikt nie mógł uzyskać do niego dostępu. Takie podejście wymusza, aby wszyscy użytkownicy w organizacji mieli tylko jedno repozytorium zatwierdzone przez organizację i zminimalizować dryf konfiguracji.


### <a name="should-an-organization-plan-for-a-single-repository-or-allow-multiple-repositories"></a>Czy należy zaplanować organizację dla jednego repozytorium czy zezwolić na wiele repozytoriów?
W ramach ogólnej strategii zarządzania zarządzaniem i konfiguracją organizacji zalecamy używanie scentralizowanego repozytorium. W przypadku korzystania z wielu repozytoriów mogą one stać się silosami niezarządzanego oprogramowania w danym czasie. W przypadku repozytorium centralnego wiele zespołów może używać artefaktów z tego repozytorium dla swoich projektów. Wymusza ona standaryzację, bezpieczeństwo, łatwość zarządzania i eliminuje duplikowanie wysiłków. W ramach centralizacji następujące działania są zalecanymi rozwiązaniami w zakresie długoterminowego zarządzania i trwałości:

- Skojarz Azure Repos z tą samą dzierżawą Azure Active Directory, z której korzysta subskrypcja platformy Azure na potrzeby uwierzytelniania i autoryzacji.
- Utwórz grupę o nazwie `All DevTest Labs Developers` w Azure Active Directory, która jest centralnie zarządzana. Wszyscy deweloperzy, którzy przyczyniają się do rozwoju artefaktów, powinni być umieszczani w tej grupie.
- Ta sama Grupa Azure Active Directory może służyć do zapewnienia dostępu do repozytorium Azure Repos i do laboratorium.
- W Azure Repos, rozgałęzianie lub rozwidlenie powinno być używane do oddzielenia repozytorium w środowisku deweloperskim z głównego repozytorium produkcyjnego. Zawartość jest dodawana tylko do głównej gałęzi z żądaniem ściągnięcia po prawidłowym przeglądzie kodu. Gdy recenzent kodu zatwierdza zmiany, programista lidera, który jest odpowiedzialny za konserwację głównej gałęzi, Scala zaktualizowany kod.

## <a name="cicd-integration"></a>Integracja ciągłej integracji/ciągłego wdrażania

### <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>Czy DevTest Labs integruje się z moją pozostałą/łańcucha narzędzi CD?
Jeśli korzystasz z usługi Azure DevOps, możesz użyć [rozszerzenia zadań programu DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) w celu zautomatyzowania potoku wydania w laboratoriach DevTest Labs. Niektóre z zadań, które można wykonać za pomocą tego rozszerzenia, obejmują:

- Utwórz maszynę wirtualną i Wdróż ją automatycznie. Możesz również skonfigurować maszynę wirtualną z najnowszą kompilacją przy użyciu funkcji kopiowania plików platformy Azure lub programu PowerShell Azure DevOps Services.
- Automatycznie Przechwytuj stan maszyny wirtualnej po przetestowaniu, aby odtworzyć usterkę na tej samej maszynie wirtualnej w celu przeprowadzenia dalszej analizy.
- Usuń maszynę wirtualną na końcu potoku wydania, gdy nie jest już potrzebne.

Poniższe wpisy w blogu oferują wskazówki i informacje dotyczące korzystania z rozszerzenia Azure DevOps Services:

- [DevTest Labs i rozszerzenie Azure DevOps](integrate-environments-devops-pipeline.md)
- [Wdróż nową maszynę wirtualną w istniejącym środowisku DevTest Labs Lab z Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Korzystanie z usługi Azure DevOps Services Release Management na potrzeby ciągłego wdrażania w usłudze DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)

W przypadku innych/Continuous dostarczanych (ciągłej integracji) łańcuchy narzędzi można osiągnąć te same scenariusze, wdrażając [szablony Azure Resource Manager](https://azure.microsoft.com/resources/templates/) przy użyciu [poleceń cmdlet Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) i [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). Możesz również używać [interfejsów API REST do DevTest Labs](https://aka.ms/dtlrestapis) , aby zintegrować je z łańcucha narzędzi.

## <a name="networking"></a>Networking

### <a name="when-should-i-create-a-new-virtual-network-for-my-devtest-labs-environment-vs-using-an-existing-virtual-network"></a>Kiedy należy utworzyć nową sieć wirtualną dla środowiska DevTest Labs a przy użyciu istniejącej sieci wirtualnej?
Jeśli maszyny wirtualne muszą współdziałać z istniejącą infrastrukturą, rozważ użycie istniejącej sieci wirtualnej w środowisku usługi DevTest Labs. Jeśli używasz ExpressRoute, możesz zminimalizować liczbę sieci wirtualnych/podsieci, aby nie było fragmentacji przestrzeni adresów IP, która jest przypisana do użycia w subskrypcjach.

Rozważ użycie wzorca komunikacji równorzędnej sieci wirtualnej tutaj ([model](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)gwiazdy). Takie podejście umożliwia komunikację sieci wirtualnej/podsieci między subskrypcjami. W przeciwnym razie każde środowisko DevTest Labs może mieć własną sieć wirtualną.

Istnieją [limity](../azure-resource-manager/management/azure-subscription-service-limits.md) liczby sieci wirtualnych na subskrypcję. Wartość domyślna to 50, chociaż ten limit można podwyższyć do 100.

### <a name="when-should-i-use-a-shared-ip-vs-public-ip-vs-private-ip"></a>Kiedy należy używać udostępnionego adresu IP a publicznego adresu IP a prywatny adres IP?

W przypadku korzystania z sieci VPN typu lokacja-lokacja lub usługi Express Route należy rozważyć użycie prywatnych adresów IP, aby umożliwić dostęp do tych maszyn za pośrednictwem sieci wewnętrznej i uzyskać dostęp za pośrednictwem publicznego Internetu.

> [!NOTE]
> Właściciele laboratorium mogą zmienić te zasady podsieci, aby upewnić się, że nikt nie tworzy publicznych adresów IP dla swoich maszyn wirtualnych. Właściciel subskrypcji powinien utworzyć zasady subskrypcji uniemożliwiające tworzenie publicznych adresów IP.

W przypadku korzystania ze współużytkowanych publicznych adresów IP maszyny wirtualne w laboratorium współużytkują publiczny adres. Takie podejście może być przydatne, gdy konieczne jest uniknięcie naruszenia limitów publicznych adresów IP dla danej subskrypcji.

### <a name="how-do-i-ensure-that-development-and-test-virtual-machines-are-unable-to-reach-the-public-internet-are-there-any-recommended-patterns-to-set-up-network-configuration"></a>Jak mogę upewnić się, że maszyny wirtualne deweloperskie i testowe nie mogą uzyskać dostępu do publicznej sieci Internet? Czy istnieją zalecane wzorce konfigurowania konfiguracji sieci?

Tak. Istnieją dwa aspekty, które należy wziąć pod uwagę — ruch przychodzący i wychodzący.

- **Ruch przychodzący** — Jeśli maszyna wirtualna nie ma publicznego adresu IP, nie można jej skontaktować z Internetem. Typowym podejściem jest upewnienie się, że ustawiono zasady na poziomie subskrypcji, w tym, że żaden użytkownik nie może utworzyć publicznego adresu IP.
- **Ruch wychodzący** — Jeśli chcesz uniemożliwić maszynom wirtualnym bezpośredni dostęp do publicznej sieci Internet i wymusić ruch przez zaporę firmową, możesz kierować ruchem lokalnym za pośrednictwem usługi Express Route lub VPN przy użyciu protokołu wymuszonego routingu.

> [!NOTE]
> Jeśli masz serwer proxy, który blokuje ruch bez ustawień serwera proxy, nie zapomnij dodać wyjątków do konta magazynu artefaktów laboratorium.

W przypadku maszyn wirtualnych lub podsieci można także użyć grup zabezpieczeń sieci. Ten krok powoduje dodanie dodatkowej warstwy ochrony w celu zezwalania na ruch i blokowania go.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>Dlaczego moja istniejąca sieć wirtualna nie jest poprawnie zapisywana?
Jedną z możliwości jest to, że nazwa sieci wirtualnej zawiera kropki. Jeśli tak, spróbuj usunąć okresy lub zamienić je na łączniki. Następnie spróbuj ponownie zapisać sieć wirtualną.

### <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>Dlaczego otrzymuję błąd "nie znaleziono zasobu nadrzędnego", gdy zastrzegasz maszynę wirtualną za pomocą programu PowerShell?
Jeśli jeden zasób jest elementem nadrzędnym dla innego zasobu, musi istnieć zasób nadrzędny przed utworzeniem zasobu podrzędnego. Jeśli zasób nadrzędny nie istnieje, zobaczysz komunikat **ParentResourceNotFound** . Jeśli nie określisz zależności w zasobie nadrzędnym, zasób podrzędny można wdrożyć przed elementem nadrzędnym.

Maszyny wirtualne są zasobami podrzędnymi w ramach laboratorium w grupie zasobów. W przypadku używania szablonów Menedżer zasobów do wdrażania maszyn wirtualnych przy użyciu programu PowerShell nazwa grupy zasobów podana w skrypcie programu PowerShell powinna być nazwą grupy zasobów laboratorium. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie typowych błędów wdrażania platformy Azure](../azure-resource-manager/templates/common-deployment-errors.md).

### <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>Gdzie można znaleźć więcej informacji o błędzie w przypadku niepowodzenia wdrożenia maszyny wirtualnej?
Błędy wdrożenia maszyny wirtualnej są przechwytywane w dziennikach aktywności. Dzienniki aktywności maszyn wirtualnych laboratorium można znaleźć w obszarze **dzienniki inspekcji** lub **diagnostyki maszyn wirtualnych** w menu zasób na stronie maszyny wirtualnej laboratorium (strona zostanie wyświetlona po wybraniu maszyny wirtualnej z listy moje maszyny wirtualne).

Czasami błąd wdrażania występuje przed rozpoczęciem wdrożenia maszyny wirtualnej. Przykładem jest przekroczenie limitu subskrypcji dla zasobu, który został utworzony z maszyną wirtualną. W takim przypadku szczegóły błędu są przechwytywane w dziennikach aktywności poziomu laboratorium. Dzienniki aktywności znajdują się w dolnej części ustawień **Konfiguracja i zasady** . Aby uzyskać więcej informacji o korzystaniu z dzienników aktywności na platformie Azure, zobacz [Wyświetlanie dzienników aktywności w celu inspekcji akcji na zasobach](../azure-resource-manager/management/view-activity-logs.md).

### <a name="why-do-i-get-location-is-not-available-for-resource-type-error-when-trying-to-create-a-lab"></a>Dlaczego otrzymuję komunikat o błędzie "Lokalizacja jest niedostępna dla typu zasobu" podczas próby utworzenia laboratorium?
Podczas próby utworzenia laboratorium może zostać wyświetlony komunikat o błędzie podobny do następującego:

```
The provided location 'australiacentral' is not available for resource type 'Microsoft.KeyVault/vaults'. List of available regions for the resource type is 'northcentralus,eastus,northeurope,westeurope,eastasia,southeastasia,eastus2,centralus,southcentralus,westus,japaneast,japanwest,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia,canadacentral,canadaeast,uksouth,ukwest,westcentralus,westus2,koreacentral,koreasouth,francecentral,southafricanorth
```

Ten błąd można rozwiązać, wykonując jedną z następujących czynności:

#### <a name="option-1"></a>Opcja 1
Sprawdź dostępność typu zasobu w regionach świadczenia usługi Azure na stronie [dostępne produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/) . Jeśli typ zasobu nie jest dostępny w określonym regionie, DevTest Labs nie obsługuje tworzenia laboratorium w tym regionie. Wybierz inny region podczas tworzenia laboratorium.

#### <a name="option-2"></a>Opcja 2
Jeśli typ zasobu jest dostępny w Twoim regionie, sprawdź, czy jest on zarejestrowany w ramach subskrypcji. Można to zrobić na poziomie właściciela subskrypcji, jak pokazano w [tym artykule](../azure-resource-manager/management/resource-providers-and-types.md).