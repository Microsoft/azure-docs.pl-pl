---
title: Architektura referencyjna przedsiębiorstwa dla Azure DevTest Labs
description: Ten artykuł zawiera wskazówki dotyczące architektury referencyjnej dla Azure DevTest Labs w przedsiębiorstwie.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 7b9652009a4e3c7bfdea029f204429a86562a552
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92144558"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Architektura referencyjna Azure DevTest Labs dla przedsiębiorstw
Ten artykuł zawiera architekturę referencyjną, która pomaga wdrożyć rozwiązanie na podstawie Azure DevTest Labs w przedsiębiorstwie. Obejmuje następujące elementy:
- Łączność lokalna za pośrednictwem usługi Azure ExpressRoute
- Brama usług pulpitu zdalnego do zdalnego logowania się do maszyn wirtualnych
- Łączność z repozytorium artefaktów dla prywatnych artefaktów
- Inne usługi PaaS, które są używane w laboratoriach

![Diagram architektury referencyjnej](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Architektura
Są to kluczowe elementy architektury referencyjnej:

- **Azure Active Directory (Azure AD)**: DevTest Labs używa [usługi Azure AD do zarządzania tożsamościami](../active-directory/fundamentals/active-directory-whatis.md). Te dwa kluczowe aspekty należy wziąć pod uwagę w przypadku udzielenia użytkownikom dostępu do środowiska w oparciu o DevTest Labs:
    - **Zarządzanie**zasobami: umożliwia dostęp do Azure Portal w celu zarządzania zasobami (tworzenia maszyn wirtualnych, tworzenia środowisk, uruchamiania, zatrzymywania, ponownego uruchamiania, usuwania i stosowania artefaktów; itd.). Zarządzanie zasobami odbywa się na platformie Azure przy użyciu kontroli dostępu opartej na rolach (RBAC). Przypisujesz role do użytkowników i ustawisz uprawnienia na poziomie zasobów i dostępu.
    - **Maszyny wirtualne (na poziomie sieci)**: w konfiguracji domyślnej maszyny wirtualne używają konta administratora lokalnego. Jeśli istnieje dostępna domena ([Azure AD Domain Services](../active-directory-domain-services/overview.md), domena lokalna lub domena oparta na chmurze), komputery można przyłączyć do domeny. Użytkownicy mogą następnie łączyć się z maszynami wirtualnymi przy użyciu tożsamości opartych na domenie.
- **Łączność lokalna**: w naszym diagramie architektury jest używany [ExpressRoute](../expressroute/expressroute-introduction.md) . Można również użyć [sieci VPN typu lokacja-lokacja](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Chociaż ExpressRoute nie są wymagane w przypadku laboratoriów DevTest, często są używane w przedsiębiorstwach. ExpressRoute jest wymagany tylko wtedy, gdy potrzebny jest dostęp do zasobów firmy. Typowe scenariusze są następujące:
    - Masz dane lokalne, których nie można przenieść do chmury.
    - Wolisz dołączyć maszyny wirtualne laboratorium do domeny lokalnej.
    - Chcesz wymusić ruch sieciowy w środowisku chmury i poza nim za pomocą lokalnej zapory na potrzeby zabezpieczeń/zgodności.
- **Sieciowe grupy zabezpieczeń**: typowy sposób ograniczania ruchu do środowiska chmury (lub w środowisku chmury) na podstawie źródłowych i docelowych adresów IP polega na użyciu [sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md). Na przykład, chcesz zezwolić tylko na ruch pochodzący z sieci firmowej do sieci laboratorium.
- **Brama usług pulpitu zdalnego**: przedsiębiorstwa zwykle blokują wychodzące połączenia pulpitu zdalnego w zaporze firmowej. Istnieje kilka sposobów włączania łączności do środowiska opartego na chmurze w usłudze DevTest Labs, w tym:
  - Użyj [bramy usług pulpitu zdalnego](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)i zezwól na statyczny adres IP modułu równoważenia obciążenia bramy.
  - [Kierowanie całego ruchu przychodzącego RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) za pośrednictwem połączenia sieci VPN ExpressRoute/lokacja-lokacja. Ta funkcja jest powszechnym zagadnieniem, gdy przedsiębiorstwa planują wdrożenie DevTest Labs.
- **Usługi sieciowe (sieci wirtualne, podsieci)**: topologia [sieci platformy Azure](../networking/networking-overview.md) to kolejny element klucza w architekturze DevTest Labs. Określa, czy zasoby z laboratorium mogą komunikować się i mieć dostęp do zasobów lokalnych i internetowych. Nasz diagram architektury zawiera najpopularniejsze sposoby korzystania z usługi DevTest Labs: Wszystkie laboratoria nawiązują połączenie za pośrednictwem [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) przy użyciu [modelu gwiazdy](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) do połączenia sieci VPN typu lokacja-lokacja z siecią lokalną. Jednak DevTest Labs bezpośrednio korzysta z platformy Azure Virtual Network, więc nie ma żadnych ograniczeń dotyczących konfigurowania infrastruktury sieciowej.
- **DevTest Labs**: DevTest Labs jest kluczową częścią ogólnej architektury. Aby dowiedzieć się więcej na temat usługi, zobacz [Informacje o DevTest Labs](devtest-lab-overview.md).
- **Maszyny wirtualne i inne zasoby (SaaS, PaaS, IaaS)**: maszyny wirtualne są kluczowym obciążeniem, które DevTest Labs obsługuje wraz z innymi zasobami platformy Azure. DevTest laboratoria ułatwiają i przyspieszają, aby zapewnić dostęp do zasobów platformy Azure (w tym maszyn wirtualnych i innych zasobów platformy Azure). Dowiedz się więcej o dostępie do platformy Azure dla [deweloperów](devtest-lab-developer-lab.md) i [testerów](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Zagadnienia dotyczące skalowalności
Mimo że DevTest Labs nie ma wbudowanych przydziałów ani limitów, inne zasoby platformy Azure, które są używane w typowej operacji laboratorium, mają [przydziały na poziomie subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md). Dlatego w typowym wdrożeniu przedsiębiorstwa potrzebna jest wiele subskrypcji platformy Azure, które obejmują duże wdrożenie DevTest Labs. Poniżej przedstawiono limity przydziałów:

- **Grupy zasobów**: w konfiguracji domyślnej DevTest Labs tworzy grupę zasobów dla każdej nowej maszyny wirtualnej lub użytkownik tworzy środowisko przy użyciu usługi. Subskrypcje mogą zawierać [do 980 grup zasobów](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Oznacza to, że jest to limit maszyn wirtualnych i środowisk w ramach subskrypcji. Istnieją dwie inne konfiguracje, które należy wziąć pod uwagę:
    - **[Wszystkie maszyny wirtualne przechodzą do tej samej grupy zasobów](resource-group-control.md)**: Mimo że ta konfiguracja ułatwia spełnienie limitu grupy zasobów, ma wpływ na limit typu zasobu na zasób.
    - **Przy użyciu udostępnionych publicznych adresów IP**: wszystkie maszyny wirtualne o tym samym rozmiarze i regionie należy umieścić w tej samej grupie zasobów. Ta konfiguracja to "Ziemia Środkowa" między przydziałami grup zasobów i przydziałami zasobów typu zasób, jeśli maszyny wirtualne mogą mieć publiczne adresy IP.
- **Zasoby na grupę zasobów według typu zasobu**: domyślny limit zasobów dla [grupy zasobów dla każdego typu zasobu to 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Jeśli używasz *wszystkich maszyn wirtualnych, przejdź do tej samej konfiguracji grupy zasobów* , użytkownicy trafią na tę subskrypcję, szczególnie jeśli maszyny wirtualne mają wiele dodatkowych dysków.
- **Konta magazynu**: laboratorium w DevTest Labs jest dostarczane z kontem magazynu. Limit przydziału platformy Azure dla [liczby kont magazynu na region na subskrypcję to 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Maksymalna liczba laboratoriów DevTest w tym samym regionie to również 250.
- **Przypisania ról**: przypisanie roli polega na tym, jak nadawać użytkownikowi lub głównemu dostęp do zasobu (właściciel, zasób, poziom uprawnień). Na platformie Azure obowiązuje [Limit przypisań ról 2 000 na subskrypcję](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits). Domyślnie usługa DevTest Labs tworzy grupę zasobów dla każdej maszyny wirtualnej. Właściciel otrzymuje uprawnienie *właściciela* dla maszyny wirtualnej i *czytnika* DevTest Labs do grupy zasobów. W ten sposób każda nowa utworzona maszyna wirtualna używa dwóch przypisań ról poza przypisaniami, które są używane w przypadku udzielenia użytkownikom uprawnień do laboratorium.
- **Operacje odczytu/zapisu interfejsu API**: istnieją różne sposoby automatyzowania platform Azure i DevTest Labs, w tym interfejsów API REST, programu PowerShell, interfejsu wiersza polecenia platformy Azure i zestawu Azure SDK. Za poorednictwem automatyzacji można napotkać inny limit dla żądań interfejsu API: Każda subskrypcja zezwala na [12 000 żądań odczytu i 1 200 żądań zapisu na godzinę](../azure-resource-manager/management/request-limits-and-throttling.md). Należy pamiętać o tym limicie podczas automatyzowania DevTest Labs.

## <a name="manageability-considerations"></a>Zagadnienia dotyczące możliwości zarządzania
DevTest Labs ma doskonały administracyjny interfejs użytkownika do pracy z pojedynczym laboratorium. Jednak w przedsiębiorstwie jest możliwe posiadanie wielu subskrypcji platformy Azure i wielu laboratoriów. Wprowadzanie zmian w sposób ciągły we wszystkich laboratoriach wymaga obsługi skryptów/automatyzacji. Poniżej przedstawiono kilka przykładów i najlepszych praktyk związanych z zarządzaniem wdrożeniem usługi DevTest Labs:

- **Zmiany w ustawieniach laboratorium**: typowym scenariuszem jest zaktualizowanie określonego ustawienia laboratorium dla wszystkich laboratoriów we wdrożeniu. Na przykład dostępny jest nowy rozmiar wystąpienia maszyny wirtualnej, a wszystkie laboratoria muszą zostać zaktualizowane, aby zezwolić na to. Najlepiej zautomatyzować te zmiany za pomocą skryptów programu PowerShell, interfejsu wiersza polecenia lub interfejsów API REST.  
- **Osobisty token dostępu repozytorium artefaktów**: zazwyczaj tokeny dostępu osobistego dla repozytorium git wygasają przez 90 dni, rok lub dwa lata. Aby zapewnić ciągłość, ważne jest, aby zwiększyć osobisty token dostępu lub utworzyć nowy. Następnie użyj usługi Automation, aby zastosować nowy osobisty token dostępu do wszystkich laboratoriów.
- **Ogranicz zmiany do ustawienia laboratorium**: często określone ustawienie musi być ograniczone (takie jak umożliwienie korzystania z obrazów z witryny Marketplace). Możesz użyć Azure Policy, aby uniemożliwić zmiany typu zasobu. Możesz też utworzyć rolę niestandardową i przyznać użytkownikom tę rolę zamiast roli *właściciela* dla laboratorium. Można to zrobić w przypadku większości ustawień w laboratorium (obsługa wewnętrzna, anons Lab, dozwolone rozmiary maszyn wirtualnych itd.).
- **Wymagaj, aby maszyny wirtualne były zgodne z konwencją nazewnictwa**: menedżerowie zwykle chcą łatwo identyfikować maszyny wirtualne będące częścią środowiska projektowania i testowania opartego na chmurze. Można to zrobić za pomocą [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Należy pamiętać, że DevTest Labs używa podstawowych zasobów platformy Azure, które są zarządzane w taki sam sposób: sieci, dysków, obliczeń itd. Na przykład Azure Policy ma zastosowanie do maszyn wirtualnych, które zostały utworzone w laboratorium. Azure Security Center może zgłosić zgodność z maszyną wirtualną. Usługa Azure Backup może zapewnić regularne kopie zapasowe maszyn wirtualnych w laboratorium.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa
Azure DevTest Labs używa istniejących zasobów na platformie Azure (obliczeń, sieci itp.). Dzięki temu program automatycznie korzysta z funkcji zabezpieczeń wbudowanych w platformę. Na przykład aby wymagać przychodzących połączeń pulpitu zdalnego pochodzące tylko z sieci firmowej, po prostu Dodaj sieciową grupę zabezpieczeń do sieci wirtualnej w bramie usług pulpitu zdalnego. Jedynym dodatkowym zagadnieniem dotyczącym zabezpieczeń jest poziom uprawnień przyznawanych członkom zespołu, którzy korzystają z laboratoriów w codziennym okresie. Najczęściej występujące uprawnienia są [ *właścicielami* i *użytkownikami*](devtest-lab-add-devtest-user.md). Aby uzyskać więcej informacji na temat tych ról, zobacz [Dodawanie właścicieli i użytkowników w Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następnym artykułem w tej serii: [skalowanie infrastruktury Azure DevTest Labs](devtest-lab-guidance-scale.md).