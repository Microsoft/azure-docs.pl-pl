---
title: Często zadawane pytania
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące rozwiązań VMware platformy Azure.
ms.topic: conceptual
ms.date: 1/14/2021
ms.openlocfilehash: 8245cd8da983ce48ba88d7faef76ab9b7ceb8c26
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218590"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution"></a>Często zadawane pytania dotyczące rozwiązań VMware platformy Azure

W tym artykule przedstawiono odpowiedzi na często zadawane pytania dotyczące rozwiązań VMware platformy Azure.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-vmware-solution"></a>Co to jest usługa Azure VMware Solution?

W miarę jak przedsiębiorstwa wykonują strategie modernizacji, aby poprawić elastyczność biznesową, obniżyć koszty i przyspieszyć innowacje, hybrydowe platformy w chmurze zostały wyznaczone jako klucze do obsługi transformacji cyfrowej klientów. Rozwiązanie VMware platformy Azure łączy oprogramowanie VMware Software-Defined Data Center (SDDC) z ekosystemem globalnej usługi w chmurze platformy Microsoft Azure. Rozwiązaniem VMware platformy Azure zarządza się w celu spełnienia wymagań dotyczących wydajności, dostępności, zabezpieczeń i zgodności.

## <a name="azure-vmware-solution-service"></a>Usługa rozwiązań VMware platformy Azure

### <a name="where-is-azure-vmware-solution-available-today"></a>Gdzie jest dostępne rozwiązanie Azure VMware już dzisiaj?

Usługa jest ciągle dodawana do nowych regionów. Aby uzyskać więcej informacji, zobacz [najnowsze informacje o dostępności usługi](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Czy obciążenia działające w ramach wystąpienia rozwiązania VMware platformy Azure zużywają lub integrują się z usługami platformy Azure?

Wszystkie usługi platformy Azure będą dostępne dla klientów rozwiązań VMware platformy Azure. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozdzielone na zasadzie wielkości liter.

### <a name="what-guest-operating-systems-are-compatible-with-azure-vmware-solution"></a>Jakie systemy operacyjne gościa są zgodne z rozwiązaniem VMware platformy Azure?

Informacje o zgodności systemu operacyjnego gościa z programem vSphere można znaleźć w [przewodniku zgodności programu VMware](https://www.vmware.com/resources/compatibility/search.php?deviceCategory=software&details=1&releases=485&page=1&display_interval=10&sortColumn=Partner&sortOrder=Asc&testConfig=16).  Aby zidentyfikować wersję usługi vSphere działającą w ramach rozwiązania Azure VMware, zobacz [wersje oprogramowania VMware](concepts-private-clouds-clusters.md#vmware-software-versions).

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Czy mogę użyć tych samych narzędzi, których teraz używam do zarządzania zasobami w chmurze prywatnej?

Tak. Azure Portal jest używany do wdrażania i kilku operacji zarządzania. Program vCenter i NSX Manager są używane do zarządzania zasobami vSphere i NSX-T.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Czy mogę zarządzać chmurą prywatną przy użyciu lokalnego programu vCenter?

Po uruchomieniu rozwiązanie VMware dla platformy Azure nie będzie obsługiwało jednego środowiska zarządzania w środowiskach lokalnych i prywatnych. Klastry chmury prywatnej będą zarządzane za pomocą programu vCenter i NSX Manager lokalnie w chmurze prywatnej.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Czy mogę używać pakietu vRealize Suite uruchomionego lokalnie? 

Konkretne integracje i przypadki użycia mogą być oceniane w zależności od wielkości liter.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Czy można migrować maszyny wirtualne vSphere z środowisk lokalnych do chmur prywatnych rozwiązań platformy Azure VMware?

Tak. Migracja maszyn wirtualnych i vMotion mogą służyć do przenoszenia maszyn wirtualnych do chmury prywatnej, jeśli są spełnione standardowe wymagania programu vCenter [vMotion](https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=2106952) .

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Czy określona wersja vSphere jest wymagana w środowiskach lokalnych?

Wszystkie środowiska chmury są dostarczane z oprogramowaniem VMware HCX, vSphere 5,5 lub nowszym w środowiskach lokalnych dla vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Jak wygląda proces kontroli zmian?

Aktualizacje wprowadzane do usługi są następujące po standardowym procesie zarządzania zmianami Microsoft Azure. Klienci są odpowiedzialni za wszelkie zadania administracyjne dotyczące obciążeń i powiązane procesy zarządzania zmianami.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Jak różni się to od rozwiązania Azure VMware CloudSimple?

Korzystając z nowego rozwiązania VMware platformy Azure, firma Microsoft i oprogramowanie VMware mają bezpośrednie powiązanie z dostawcą usług w chmurze. Nowe rozwiązanie jest w pełni zaprojektowane, stworzone i obsługiwane przez firmę Microsoft i potwierdzone przez oprogramowanie VMware. Architektura rozwiązań jest spójna, dzięki stosowi technologii VMware działającego w dedykowanej infrastrukturze platformy Azure.

### <a name="can-azure-vmware-solution-vms-be-managed-by-vmrc"></a>Czy maszyny wirtualne rozwiązań VMware platformy Azure mogą być zarządzane przez funkcję autosterowania?
Tak. Pod warunkiem, że system, w którym jest zainstalowany, może uzyskać dostęp do programu vCenter chmury prywatnej i używa publicznego systemu DNS do rozpoznawania nazw hostów ESXi.

### <a name="are-there-special-instructions-for-installing-and-using-vmrc-with-azure-vmware-solution-vms"></a>Czy istnieją specjalne instrukcje dotyczące instalowania i używania funkcji niedostępności z maszynami wirtualnymi rozwiązań VMware platformy Azure?
Nie. Aby spełnić wymagania wstępne dotyczące maszyn wirtualnych, postępuj zgodnie z [instrukcjami dostarczonymi przez program VMware](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-89E7E8F0-DB2B-437F-8F70-BA34C505053F.html). 

### <a name="is-vmware-hcx-supported-on-vpns"></a>Czy oprogramowanie VMware HCX jest obsługiwane w sieciach VPN?
Nie, ze względu na wymagania dotyczące przepustowości i opóźnienia.

### <a name="can-azure-bastion-be-used-for-connecting-to-azure-vmware-solution-vms"></a>Czy usługa Azure bastionu może być używana do nawiązywania połączenia z maszynami wirtualnymi rozwiązań VMware platformy Azure?
Azure bastionu to usługa, która jest zalecana do łączenia się z polem skoku, aby uniemożliwić udostępnianie rozwiązania Azure VMware do Internetu. Nie można używać usługi Azure bastionu do nawiązywania połączeń z maszynami wirtualnymi rozwiązań VMware platformy Azure, ponieważ nie są one obiektami usługi Azure IaaS.

### <a name="can-azure-load-balancer-internal-be-used-for-azure-vmware-solution-vms"></a>Czy Azure Load Balancer można używać wewnętrznych dla maszyn wirtualnych rozwiązania Azure VMware?
Nie. Azure Load Balancer Internal — obsługuje tylko maszyny wirtualne IaaS platformy Azure. Azure Load Balancer nie obsługuje pul zaplecza opartych na protokole IP; tylko maszyny wirtualne platformy Azure lub obiekty zestawu skalowania maszyn wirtualnych, w których maszyny wirtualne rozwiązań platformy Azure VMware nie są obiektami platformy Azure.

### <a name="can-an-existing-expressroute-gateway-be-used-to-connect-to-azure-vmware-solution"></a>Czy do nawiązania połączenia z rozwiązaniem VMware platformy Azure można używać istniejącej bramy ExpressRoute?
Tak. Użyj istniejącej bramy ExpressRoute, aby nawiązać połączenie z rozwiązaniem VMware platformy Azure, o ile nie przekracza limit czterech obwodów ExpressRoute na sieć wirtualną. Aby uzyskać dostęp do rozwiązania VMware platformy Azure ze środowisk lokalnych za pośrednictwem usługi ExpressRoute, musisz mieć ExpressRoute Global Reach, ponieważ Brama ExpressRoute nie udostępnia przechodniej routingu między podłączonymi do niego obwodami.

## <a name="compute-network-storage-and-backup"></a>Obliczenia, Sieć, magazyn i kopia zapasowa

### <a name="is-there-more-than-one-type-of-host-available"></a>Czy jest dostępny więcej niż jeden typ hosta?

Dostępny jest tylko jeden typ hosta.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Jakie są specyfikacje procesora dla każdego typu hosta?

Serwery mają dwa procesory Intel procesorów 2,3 GHz.

### <a name="how-much-memory-is-in-each-host"></a>Ile pamięci znajduje się na każdym hoście?

Serwery mają 576 GB pamięci RAM.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Co to jest pojemność magazynu każdego hosta?

Każdy host ESXi ma dwie sieci vSAN diskgroups z warstwą wydajności 15,2 TB i warstwą pamięci podręcznej interfejsu NVMe 3,2 TB (1,6 TB w każdej z nich).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Jaka przepustowość sieci jest dostępna na każdym hoście ESXi?

Każdy host ESXi w rozwiązaniu VMware platformy Azure jest skonfigurowany z kartami sieciowymi 4 25 GB/s, z dwiema kartami sieciowymi obsługującymi ruch systemowy ESXi i dwie karty sieciowe obsługiwane dla ruchu obciążeń. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Czy dane są przechowywane w magazynach danych sieci vSAN szyfrowanych w spoczynku?

Tak, wszystkie dane sieci vSAN są szyfrowane domyślnie przy użyciu kluczy przechowywanych w Azure Key Vault.

###  <a name="what-independent-software-vendors-isvs-backup-solutions-work-with-azure-vmware-solution"></a>Jakie rozwiązania do tworzenia kopii zapasowych niezależnych dostawców oprogramowania (ISV) współpracują z rozwiązaniem VMware platformy Azure?

Rozwiązania firmy CommVault, Veritas i Veeam zostały rozszerzone do pracy z rozwiązaniem VMware platformy Azure.  Jednak każde rozwiązanie do tworzenia kopii zapasowych, które używa programu VMware VADP z trybem transportu HotAdd, będzie działało od razu na platformie Azure VMware.

### <a name="what-about-support-for-isv-backup-solutions"></a>Co o obsłudze rozwiązań do tworzenia kopii zapasowych niezależnych dostawców oprogramowania?

Ponieważ te rozwiązania do tworzenia kopii zapasowych są instalowane i zarządzane przez klientów, mogą skontaktować się z odpowiednim dostawcą oprogramowania, aby uzyskać pomoc techniczną. 

### <a name="what-is-the-correct-storage-policy-for-the-dedupe-setup"></a>Jakie są poprawne zasady magazynu dla konfiguracji deduplikacji?

Użyj zasad magazynu *thin_provision* dla szablonu maszyny wirtualnej.  Wartość domyślna to *thick_provision*.

### <a name="are-the-snmp-infrastructure-logs-shared"></a>Czy dzienniki infrastruktury SNMP są udostępnione?

Nie.

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

### <a name="is-the-underlying-infrastructure-shared"></a>Czy podstawowa infrastruktura jest udostępniona?

Nie, hosty i klastry w chmurze prywatnej są dedykowane i bezpiecznie usuwane przed i po użyciu.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Co to jest minimalna i Maksymalna liczba hostów na klaster?

Klastry mogą skalować od 3 do 16 hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Czy mogę skalować swoje klastry chmur prywatnych?

Tak, rozmiar klastrów między minimalną i maksymalną liczbą hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

### <a name="what-are-trial-clusters"></a>Co to są klastry próbne?

Klastry próbne to trzy klastry hostów używane do comiesięcznych ocen chmur prywatnych rozwiązań VMware platformy Azure.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Czy można używać hostów wysokiej klasy na potrzeby klastrów próbnych?

Nie. Hosty wysokiej klasy ESXi są zarezerwowane do użytku w klastrach produkcyjnych.

## <a name="azure-vmware-solution-and-vmware-software"></a>Rozwiązanie VMware i oprogramowanie VMware platformy Azure

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Jakie wersje oprogramowania VMware są używane w chmurach prywatnych?

[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


### <a name="do-private-clouds-use-vmware-nsx"></a>Czy Chmura prywatna korzysta z programu VMware NSX?

Tak, NSX-T 2,5 jest używany w przypadku sieci zdefiniowanej przez oprogramowanie w chmurach prywatnych rozwiązań VMware platformy Azure.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Czy mogę używać oprogramowania VMware NSX-V w chmurze prywatnej?

Nie. NSX-T jest jedyną obsługiwaną wersją programu NSX.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Czy NSX jest wymagany w środowiskach lokalnych lub sieciach, które łączą się z chmurą prywatną?

Nie, nie musisz używać NSX lokalnie.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Co to jest harmonogram uaktualniania i aktualizacji oprogramowania VMware w chmurze prywatnej?

Uaktualnienie pakietu oprogramowania w chmurze prywatnej zachowuje oprogramowanie w ramach jednej wersji najnowszego pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Jak często będzie aktualizowany stos oprogramowania chmury prywatnej?

Oprogramowanie chmury prywatnej jest uaktualniane zgodnie z harmonogramem, który śledzi wydanie pakietu oprogramowania z programu VMware. Chmura prywatna nie wymaga przestojów w przypadku uaktualnień.

## <a name="connectivity"></a>Łączność

### <a name="what-network-ip-address-planning-is-required-to-incorporate-private-clouds-with-on-premises-environments"></a>Jakie Planowanie adresów IP sieci jest wymagane w celu uwzględnienia chmur prywatnych w środowiskach lokalnych?

Do wdrożenia chmury prywatnej rozwiązania Azure VMware jest wymagane miejsce w sieci prywatnej/22. Ta prywatna przestrzeń adresowa nie powinna nakładać się na inne sieci wirtualne w ramach subskrypcji lub sieci lokalnych.
 
### <a name="how-do-i-connect-from-on-premises-environments-to-an-azure-vmware-solution-private-cloud"></a>Jak mogę połączyć się ze środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware?

Można nawiązać połączenie z usługą przy użyciu jednej z dwóch metod: 

- Z MASZYNą wirtualną lub bramą aplikacji wdrożoną w sieci wirtualnej platformy Azure, która jest połączona za pomocą usługi ExpressRoute z chmurą prywatną.
- Za pośrednictwem ExpressRoute Global Reach z lokalnego centrum danych do obwodu usługi Azure ExpressRoute.

### <a name="how-do-i-connect-a-workload-vm-to-the-internet-or-an-azure-service-endpoint"></a>Jak mogę połączyć maszynę wirtualną obciążenia z Internetem lub punktem końcowym usługi platformy Azure?

W Azure Portal Włącz łączność z Internetem w chmurze prywatnej. Za pomocą Menedżera NSX-T Utwórz router T1 NSX-T i przełącznik logiczny. Następnie można użyć programu vCenter do wdrożenia maszyny wirtualnej w segmencie sieci zdefiniowanym przez przełącznik logiczny. Ta maszyna wirtualna będzie miała dostęp sieciowy do Internetu i usług platformy Azure.

### <a name="do-i-need-to-restrict-access-from-the-internet-to-vms-on-logical-networks-in-a-private-cloud"></a>Czy muszę ograniczyć dostęp z Internetu do maszyn wirtualnych w sieciach logicznych w chmurze prywatnej?

Nie. Ruch sieciowy przychodzący z Internetu bezpośrednio do chmur prywatnych jest domyślnie niedozwolony.  Można jednak uwidocznić maszyny wirtualne rozwiązań VMware platformy Azure w Internecie za pośrednictwem opcji [publiczny adres IP](public-ip-usage.md) w Azure Portal dla chmury prywatnej rozwiązania Azure VMware.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Czy muszę ograniczyć dostęp do Internetu z maszyn wirtualnych w sieciach logicznych do Internetu?

Tak. Należy użyć Menedżera NSX-T, aby utworzyć zaporę w celu ograniczenia dostępu maszyny wirtualnej do Internetu.


### <a name="can-azure-vmware-solution-use-azure-virtual-wan-hosted-expressroute-gateways"></a>Czy rozwiązanie VMware firmy Azure korzysta z hostowanych bram ExpressRoute Azure Virtual WAN?
Tak.

### <a name="can-transit-connectivity-be-established-between-on-premises-and-azure-vmware-solution-through-azure-virtual-wan-over-expressroute-global-reach"></a>Czy można nawiązać połączenie między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure za pośrednictwem wirtualnej sieci WAN platformy Azure przez ExpressRoute Global Reach?
Wirtualna sieć WAN platformy Azure nie zapewnia routingu przechodniego między dwoma podłączonymi obwodami usługi ExpressRoute a bramą ExpressRoute sieci WAN innej niż wirtualna. Korzystanie z programu ExpressRoute Global Reach umożliwia łączność między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure, ale za pośrednictwem sieci globalnej firmy Microsoft zamiast wirtualnego koncentratora sieci WAN.

### <a name="could-i-use-hcx-through-public-internet-communications-as-a-workaround-for-the-non-supportability-of-hcx-when-using-vpn-s2s-with-vwan-for-on-premises-communications"></a>Czy mogę używać HCX za pośrednictwem publicznej komunikacji z Internetem w celu obejścia niewsparcia dla HCX w przypadku korzystania z sieci VPN S2S z vWAN dla komunikacji lokalnej?

Obecnie jedyną obsługiwaną metodą dla oprogramowania VMware HCX jest ExpressRoute.

## <a name="accounts-and-privileges"></a>Konta i uprawnienia

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Jakie konta i uprawnienia uzyskasz przy użyciu nowej chmury prywatnej rozwiązania Azure VMware?

Masz poświadczenia dla użytkownika cloudadmin w programie vCenter i dostęp administracyjny do Menedżera NSX-T. Istnieje również Grupa CloudAdmin, która może służyć do uwzględnienia Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Czy może mieć dostęp administratora do hostów ESXi?

Nie, dostęp administratora do ESXi jest ograniczony do spełnienia wymagań dotyczących zabezpieczeń rozwiązania.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Jakie uprawnienia i uprawnienia są dostępne w programie vCenter?

Będziesz mieć uprawnienia do grupy CloudAdmin. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Jakie uprawnienia i uprawnienia są dostępne w Menedżerze NSX-T?

Masz pełne uprawnienia administratora na NSX-T i mogą zarządzać kontrolą dostępu opartą na rolach vSphere, tak jak w przypadku lokalnego centrum danych NSX-T. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

> [!NOTE]
> Router T0 jest tworzony i konfigurowany w ramach wdrożenia chmury prywatnej. Wszelkie modyfikacje tego routera logicznego lub maszyn wirtualnych węzła brzegowego NSX-T mogą mieć wpływ na łączność z chmurą prywatną.

## <a name="billing-and-support"></a>Rozliczenia i pomoc techniczna

### <a name="how-will-pricing-be-structured-for-azure-vmware-solution"></a>W jaki sposób Cennik będzie miał strukturę dla rozwiązań VMware platformy Azure?

Ogólne pytania dotyczące cen można znaleźć na stronie z [cennikiem](https://azure.microsoft.com/pricing/details/azure-vmware) rozwiązań VMware platformy Azure. 

### <a name="can-azure-vmware-solution-be-purchased-through-a-microsoft-csp"></a>Czy można zakupić rozwiązanie VMware na platformie Azure za pomocą dostawcy Microsoft CSP?

Tak, klienci mogą wdrażać rozwiązanie VMware dla platformy Azure w ramach subskrypcji platformy Azure zarządzanej przez dostawcę usług kryptograficznych.

### <a name="who-supports-azure-vmware-solution"></a>Kto obsługuje rozwiązanie Azure VMware?

Firma Microsoft zapewnia pomoc techniczną dla rozwiązań VMware platformy Azure. Możesz przesłać [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). 

W przypadku subskrypcji zarządzanych przez dostawcę usług kryptograficznych pierwszy poziom wsparcia zapewnia dostawcy rozwiązań w taki sam sposób, jak dostawca CSP dla innych usług platformy Azure.

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Jakie konta muszę utworzyć chmurę prywatną rozwiązania Azure VMware?

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure.

### <a name="are-red-hat-solutions-supported-on-azure-vmware-solution"></a>Czy rozwiązania firmy Red Hat są obsługiwane na platformie Azure VMware?

Firma Microsoft i Red Hat współdzielą zintegrowany, współpracujący zespół pomocy technicznej, który zapewnia ujednolicony punkt kontaktowy dla ekosystemów Red Hat działających na platformie Azure.  Podobnie jak w przypadku innych usług platformy Azure, które współpracują z Red Hat Enterprise Linux, rozwiązanie Azure VMware jest objęte dostępem w chmurze i zintegrowanym parasolem wsparcia. Red Hat Enterprise Linux jest obsługiwany na potrzeby uruchamiania na platformie Azure VMware rozwiązanie platformy Azure.

### <a name="is-vmware-hcx-enterprise-available-and-if-so-how-much-does-it-cost"></a>Czy jest dostępny program VMware HCX Enterprise, a jeśli tak, ile kosztuje?

Program VMware HCX Enterprise jest dostępny w ramach rozwiązania Azure VMware jako funkcji/usługi w *wersji zapoznawczej* . Chociaż rozwiązanie VMware HCX Enterprise for Azure VMware jest w wersji zapoznawczej, jest to bezpłatna funkcja/usługa i podlega podglądowi warunków i postanowień usługi. Po przejściu do usługi VMware HCX Enterprise zostanie wyświetlone 30-dniowe powiadomienie o przełączeniu na rozliczenia. Możesz ją wyłączyć lub zrezygnować z usługi.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Jak mogę zażądać zwiększenia limitu przydziału hosta dla rozwiązania Azure VMware?

W przypadku subskrypcji zarządzanych przez dostawcę CSP klient musi przesłać żądanie do partnera. Zespół partnerski, a następnie nawiąże się z firmą Microsoft w celu zwiększenia limitu przydziału dla subskrypcji. Aby uzyskać szczegółowe informacje, zobacz [jak włączyć artykuł dotyczący zasobów rozwiązań VMware platformy Azure](enable-azure-vmware-solution.md) . 

W przypadku subskrypcji EA należy wykonać poniższą procedurę. Najpierw będą potrzebne:

* [Umowa Enterprise platformy Azure (EA)](../cost-management-billing/manage/ea-portal-agreements.md) z firmą Microsoft.
* Konto platformy Azure w ramach subskrypcji platformy Azure.

Aby można było utworzyć zasób rozwiązania Azure VMware, należy przesłać bilet pomocy technicznej w celu przydzielenia hostów. Potwierdzenie i spełnienie Twojego żądania zajmuje maksymalnie pięć dni roboczych. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i potrzebujesz większej liczby hostów, przejdziesz do tego samego procesu.

1. W Azure Portal w obszarze **Pomoc i obsługa techniczna** Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
   - **Typ problemu:** Naukow
   - **Subskrypcja:** Wybierz swoją subskrypcję
   - **Usługa:** Wszystkie usługi > rozwiązanie VMware dla platformy Azure
   - **Zasób:** Pytanie ogólne 
   - **Podsumowanie:** Potrzebna pojemność
   - **Typ problemu:** Problemy z zarządzaniem pojemnością
   - **Podtyp problemu:** Żądanie klienta dotyczące dodatkowego przydziału/pojemności hosta

1. W **opisie** biletu pomocy technicznej na karcie Szczegóły podaj następujące **informacje** :

   - ZK lub produkcja 
   - Nazwa regionu
   - Liczba hostów
   - Wszelkie inne szczegóły

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy hosty, które umożliwiają zapełnienie chmury prywatnej oraz dla hostów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i poproś o dodatkowe hosty, pamiętaj, że potrzebujemy pięciu dni roboczych, aby przydzielić hosty. 

1. Przed udostępnieniem hostów upewnij się, że rejestrujesz dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać więcej informacji na temat rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md). 

### <a name="are-reserved-instances-available-for-purchasing-through-the-cloud-solution-provider-csp-program"></a>Czy zarezerwowane wystąpienia są dostępne do zakupu przez program Cloud Solution Provider (CSP)?

Tak. Dostawca CSP może zakupić zarezerwowane wystąpienia dla swoich klientów. Aby uzyskać więcej informacji, zobacz temat [Zapisywanie kosztów z zarezerwowanym wystąpieniem](reserved-instance.md). 

### <a name="does-azure-vmware-solution-offer-multi-tenancy-for-hosting-csp-partners"></a>Czy rozwiązanie Azure VMware oferuje wiele dzierżawców na potrzeby hostowania partnerów CSP?

Nie. Obecnie rozwiązanie VMware platformy Azure nie oferuje wielu dzierżawców.

### <a name="will-traffic-between-on-premises-and-azure-vmware-solution-over-expressroute-incur-any-outbound-data-transfer-charge-in-the-metered-data-plan"></a>Czy ruch między środowiskiem lokalnym i rozwiązaniem VMware platformy Azure przez program ExpressRoute wiąże się z opłatami za transfer danych wychodzących w planie taryfowych danych?

Ruch w ramach obwodu usługi Azure VMware ExpressRoute nie jest naliczany w żaden sposób. Ruch z obwodu usługi ExpressRoute łączącego się z siecią lokalną z platformą Azure jest naliczany zgodnie z planami cenowymi ExpressRoute.


## <a name="customer-communication"></a>Komunikacja z klientem

### <a name="how-can-i-receive-an-alert-when-azure-sends-service-health-notifications-to-my-azure-subscription"></a>Jak mogę odebrać alert, gdy platforma Azure wysyła powiadomienia o kondycji usługi do mojej subskrypcji platformy Azure?

Problemy z usługą, planowana konserwacja, klasyfikatory kondycji, powiadomienia doradców zabezpieczeń są publikowane za pomocą **Service Health** w Azure Portal.  Podczas konfigurowania alertów dziennika aktywności dla tych powiadomień można podejmować odpowiednie działania. Aby uzyskać więcej informacji, zobacz [tworzenie alertów dotyczących kondycji usługi przy użyciu Azure Portal](../service-health/alerts-activity-log-service-notifications-portal.md#create-service-health-alert-using-azure-portal).

:::image type="content" source="media/service-health.png" alt-text="Zrzut ekranu przedstawiający powiadomienia Service Health":::



<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952?lang=en_US&queryTerm=21069522

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
