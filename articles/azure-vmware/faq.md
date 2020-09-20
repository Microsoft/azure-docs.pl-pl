---
title: Często zadawane pytania
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące rozwiązań VMware platformy Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: 7b4abc2d711a3da6a6df125854759e083d7e04a7
ms.sourcegitcommit: 0fd1f3fe7817ad44d878d580ec167e1508051795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2020
ms.locfileid: "90817840"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Często zadawane pytania dotyczące wersji zapoznawczej rozwiązań VMware platformy Azure

Odpowiedzi na często zadawane pytania dotyczące rozwiązań VMware platformy Azure.

## <a name="general"></a>Ogólne

### <a name="what-is-azure-vmware-solution"></a>Co to jest usługa Azure VMware Solution?

W miarę jak przedsiębiorstwa wykonują strategie modernizacji, aby poprawić elastyczność biznesową, obniżyć koszty i przyspieszyć innowacje, hybrydowe platformy w chmurze zostały wyznaczone jako klucze do obsługi transformacji cyfrowej klientów. Rozwiązanie VMware platformy Azure łączy oprogramowanie firmy VMware z centrum danych (SDDC), które ma Microsoft Azure globalny ekosystem usług w chmurze. Rozwiązaniem VMware platformy Azure jest zarządzanie w celu spełnienia wymagań dotyczących wydajności, dostępności, zabezpieczeń i zgodności.

## <a name="azure-vmware-solution-service"></a>Usługa rozwiązań VMware platformy Azure

### <a name="where-is-azure-vmware-solution-available-today"></a>Gdzie jest dostępne rozwiązanie Azure VMware już dzisiaj?

Usługa jest ciągle dodawana do nowych regionów. Aby uzyskać więcej informacji, zobacz [najnowsze informacje o dostępności usługi](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

### <a name="can-workloads-running-in-an-azure-vmware-solution-instance-consume-or-integrate-with-azure-services"></a>Czy obciążenia działające w ramach wystąpienia rozwiązania VMware platformy Azure zużywają lub integrują się z usługami platformy Azure?

Wszystkie usługi platformy Azure będą dostępne dla klientów rozwiązań VMware platformy Azure. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozdzielone na zasadzie wielkości liter.

### <a name="do-i-use-the-same-tools-that-i-use-now-to-manage-private-cloud-resources"></a>Czy mogę użyć tych samych narzędzi, których teraz używam do zarządzania zasobami w chmurze prywatnej?

Tak. Azure Portal jest używany do wdrażania i wielu operacji zarządzania. Program vCenter i NSX Manager są używane do zarządzania zasobami vSphere i NSX-T.

### <a name="can-i-manage-a-private-cloud-with-my-on-premises-vcenter"></a>Czy mogę zarządzać chmurą prywatną przy użyciu lokalnego programu vCenter?

Po uruchomieniu rozwiązanie VMware dla platformy Azure nie będzie obsługiwało jednego środowiska zarządzania w środowiskach lokalnych i prywatnych. Klastry chmury prywatnej będą zarządzane za pomocą programu vCenter i NSX Manager lokalnie w chmurze prywatnej.

### <a name="can-i-use-vrealize-suite-running-on-premises"></a>Czy mogę używać pakietu vRealize Suite uruchomionego lokalnie? 

Konkretne integracje i przypadki użycia mogą być oceniane w zależności od wielkości liter.

### <a name="can-i-migrate-vsphere-vms-from-on-premises-environments-to-azure-vmware-solution-private-clouds"></a>Czy można migrować maszyny wirtualne vSphere z środowisk lokalnych do chmur prywatnych rozwiązań platformy Azure VMware?

Tak. Migracja maszyn wirtualnych i vMotion mogą służyć do przenoszenia maszyn wirtualnych do chmury prywatnej, jeśli są spełnione standardowe wymagania programu vCenter [vMotion](https://kb.vmware.com/s/article/210695) .

### <a name="is-a-specific-version-of-vsphere-required-in-on-premises-environments"></a>Czy określona wersja vSphere jest wymagana w środowiskach lokalnych?

Ze względu na to, że wszystkie środowiska chmury są dostarczane z HCX, vSphere 5,5 lub nowszym w środowiskach lokalnych dla vMotion.

### <a name="what-does-the-change-control-process-look-like"></a>Jak wygląda proces kontroli zmian?

Aktualizacje wprowadzone do usługi będą zgodne z procesem standardowego zarządzania zmianami Microsoft Azure. Klienci są odpowiedzialni za wszelkie zadania administracyjne dotyczące obciążeń i powiązane procesy zarządzania zmianami.

### <a name="how-is-this-different-from-azure-vmware-solution-by-cloudsimple"></a>Jak różni się to od rozwiązania Azure VMware CloudSimple?

Korzystając z nowego rozwiązania VMware platformy Azure, firma Microsoft i oprogramowanie VMware mają bezpośrednie powiązanie z dostawcą usług w chmurze. Nowe rozwiązanie jest w pełni zaprojektowane, stworzone i obsługiwane przez firmę Microsoft i potwierdzone przez oprogramowanie VMware. Architektura rozwiązań jest spójna, dzięki stosowi technologii VMware działającego w dedykowanej infrastrukturze platformy Azure.

### <a name="if-im-an-existing-azure-vmware-solution-customer-what-does-this-preview-mean-for-me"></a>Jeśli jestem istniejącym klientem rozwiązań VMware platformy Azure, co oznacza w tej wersji zapoznawczej?

Nie wprowadzono zmian w istniejącym rozwiązaniu VMware platformy Azure przez CloudSimple. Nadal obsługujemy rozwiązanie na platformie Azure. Rozwiązanie VMware platformy Azure jest objęte umową dotyczącą poziomu usług [(SLA)](https://aka.ms/CSVMwareSLA). Klienci powinni nadal korzystać z usługi w przypadku obciążeń produkcyjnych. jest to dostępne rozwiązanie, które podlega postanowieniom [dotyczącym usług firmy Microsoft](https://azure.microsoft.com/support/legal/).

### <a name="can-i-migrate-from-azure-vmware-solution-by-cloudsimple-to-this-new-solution"></a>Czy mogę przeprowadzić migrację z rozwiązania Azure VMware, CloudSimple do tego nowego rozwiązania?

Tak, rozwiązanie VMware platformy Azure obsługuje migrację przy użyciu znanych narzędzi VMware, takich jak HCX. W przypadku klientów, którzy chcą przeprowadzić migrację do nowego rozwiązania, skontaktuj się z zespołem konto Microsoft, aby poznać opcje i dostępną pomoc techniczną.



## <a name="compute-network-and-storage"></a>Obliczenia, Sieć i magazyn

### <a name="is-there-more-than-one-type-of-host-available"></a>Czy jest dostępny więcej niż jeden typ hosta?

Dostępny jest tylko jeden typ hosta.

### <a name="what-are-the-cpu-specifications-in-each-type-of-host"></a>Jakie są specyfikacje procesora dla każdego typu hosta?

Serwery mają dwa procesory Intel procesorów 2,3 GHz.

### <a name="how-much-memory-is-in-each-host"></a>Ile pamięci znajduje się na każdym hoście?

Serwery mają 576 GB pamięci RAM.

### <a name="what-is-the-storage-capacity-of-each-host"></a>Co to jest pojemność magazynu każdego hosta?

Każdy host ESXi ma dwie sieci vSAN diskgroups z warstwą wydajności wynoszącą 15,2 TB, a 3,2 TB warstwy pamięci podręcznej interfejsu NVMe (1,6 TB w każdej z nich).

### <a name="how-much-network-bandwidth-is-available-in-each-esxi-host"></a>Jaka przepustowość sieci jest dostępna na każdym hoście ESXi?

Każdy host ESXi ma rozwiązanie Azure VMware, które jest skonfigurowane z kartami sieciowymi 4 25 GB/s, z dwiema kartami sieciowymi obsługującymi ruch systemu ESXi i dwie karty sieciowe obsługiwane dla ruchu obciążeń. 

### <a name="is-data-stored-on-the-vsan-datastores-encrypted-at-rest"></a>Czy dane są przechowywane w magazynach danych sieci vSAN szyfrowanych w spoczynku?

Tak, wszystkie dane sieci vSAN są szyfrowane domyślnie przy użyciu kluczy przechowywanych w Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

### <a name="is-the-underlying-infrastructure-shared"></a>Czy podstawowa infrastruktura jest udostępniona?

Nie, hosty i klastry w chmurze prywatnej są dedykowane i bezpiecznie usuwane przed i po użyciu.

### <a name="what-are-the-minimum-and-maximum-number-of-hosts-per-cluster"></a>Co to jest minimalna i Maksymalna liczba hostów na klaster?

Klastry mogą skalować od 3 do 16 hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

### <a name="can-i-scale-my-private-cloud-clusters"></a>Czy mogę skalować swoje klastry chmur prywatnych?

Tak, liczba klastrów jest skalowana między minimalną i maksymalną liczbą hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

### <a name="what-are-trial-clusters"></a>Co to są klastry próbne?

Klastry próbne to trzy klastry hostów używane do oceny w jednym miesiącu chmur prywatnych rozwiązań Azure VMware.

### <a name="can-i-use-high-end-hosts-for-trial-clusters"></a>Czy można używać hostów wysokiej klasy na potrzeby klastrów próbnych?

Nie. Hosty wysokiej klasy ESXi są zarezerwowane do użytku w klastrach produkcyjnych.

## <a name="azure-vmware-solution-and-vmware-software"></a>Rozwiązanie VMware i oprogramowanie VMware platformy Azure

### <a name="what-versions-of-vmware-software-is-used-in-private-clouds"></a>Jakie wersje oprogramowania VMware są używane w chmurach prywatnych?

Chmury prywatne używają vSphere 6,7, sieci vSAN 6,7, HCX i wersji 2,5 NSX-T.  

### <a name="do-private-clouds-use-vmware-nsx"></a>Czy Chmura prywatna korzysta z programu VMware NSX?

Tak, NSX-T 2,5 jest używany w przypadku sieci zdefiniowanej przez oprogramowanie w chmurach prywatnych rozwiązań VMware platformy Azure.

### <a name="can-i-use-vmware-nsx-v-in-a-private-cloud"></a>Czy mogę używać oprogramowania VMware NSX-V w chmurze prywatnej?

Nie. NSX-T jest jedyną obsługiwaną wersją programu NSX.

### <a name="is-nsx-required-in-on-premises-environments-or-networks-that-connect-to-a-private-cloud"></a>Czy NSX jest wymagany w środowiskach lokalnych lub sieciach, które łączą się z chmurą prywatną?

Nie, nie musisz używać NSX lokalnie.

### <a name="what-is-the-upgrade-and-update-schedule-for-vmware-software-in-a-private-cloud"></a>Co to jest harmonogram uaktualniania i aktualizacji oprogramowania VMware w chmurze prywatnej?

Uaktualnienia pakietu oprogramowania w chmurze prywatnej są wykonywane w celu zachowania oprogramowania w ramach jednej wersji najnowszej wersji pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN).

### <a name="how-often-will-the-private-cloud-software-stack-be-updated"></a>Jak często będzie aktualizowany stos oprogramowania chmury prywatnej?

Oprogramowanie chmury prywatnej jest uaktualniane zgodnie z harmonogramem, który śledzi wersję pakietu oprogramowania z programu VMware. Chmura prywatna nie wymaga przestojów w przypadku uaktualnień.

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

Nie. Ruch sieciowy przychodzący z Internetu bezpośrednio do chmur prywatnych nie jest dozwolony.

### <a name="do-i-need-to-restrict-internet-access-from-vms-on-logical-networks-to-the-internet"></a>Czy muszę ograniczyć dostęp do Internetu z maszyn wirtualnych w sieciach logicznych do Internetu?

Tak. Aby utworzyć zaporę ograniczającą dostęp do sieci wirtualnej, należy użyć Menedżera NSX-T.

## <a name="accounts-and-privileges"></a>Konta i uprawnienia

### <a name="what-accounts-and-privileges-will-i-get-with-my-new-azure-vmware-solution-private-cloud"></a>Jakie konta i uprawnienia uzyskasz przy użyciu nowej chmury prywatnej rozwiązania Azure VMware?

Masz poświadczenia dla użytkownika cloudadmin w programie vCenter i dostęp administracyjny do Menedżera NSX-T. Istnieje również Grupa CloudAdmin, która może służyć do uwzględnienia Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

### <a name="can-have-administrator-access-to-esxi-hosts"></a>Czy może mieć dostęp administratora do hostów ESXi?

Nie, dostęp administratora do ESXi jest ograniczony do spełnienia wymagań dotyczących zabezpieczeń rozwiązania.

### <a name="what-privileges-and-permissions-will-i-have-in-vcenter"></a>Jakie uprawnienia i uprawnienia są dostępne w programie vCenter?

Będziesz mieć uprawnienia do grupy CloudAdmin. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

### <a name="what-privileges-and-permissions-will-i-have-on-the-nsx-t-manager"></a>Jakie uprawnienia i uprawnienia są dostępne w Menedżerze NSX-T?

Masz pełne uprawnienia administratora na NSX-T i mogą zarządzać kontrolą dostępu opartą na rolach, tak jak w przypadku lokalnego centrum danych NSX-T. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

> [!NOTE]
> Router T0 jest tworzony i konfigurowany w ramach wdrożenia chmury prywatnej. Wszelkie modyfikacje tego routera logicznego lub maszyn wirtualnych węzła brzegowego NSX-T mogą mieć wpływ na łączność z chmurą prywatną.

## <a name="billing-and-support"></a>Rozliczenia i pomoc techniczna

### <a name="how-will-i-be-billed-during-the-preview-of-azure-vmware-solution"></a>Jak będą naliczane opłaty w trakcie korzystania z wersji zapoznawczej rozwiązania Azure VMware?

Rozliczanie za rozwiązanie VMware dla platformy Azure w okresie zapoznawczym jest comiesięczne w oparciu o płatność zgodnie z rzeczywistym użyciem. Dodatkowe opcje będą dostępne w ogólnej dostępności.

### <a name="how-will-pricing-be-structured-during-the-preview-of-azure-vmware-solution"></a>W jaki sposób Cennik będzie miał strukturę w trakcie korzystania z wersji zapoznawczej rozwiązania Azure VMware?

Aby uzyskać ogólne pytania dotyczące cen, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/azure-vmware) rozwiązań VMware platformy Azure. Cennik wersji zapoznawczej jest dostępny na żądanie, skontaktuj się z zespołem ds. kont lub Skorzystaj z linku na stronie cennika, aby skontaktować się z firmą

### <a name="who-supports-azure-vmware-solution"></a>Kto obsługuje rozwiązanie Azure VMware?

Pomoc techniczna dla rozwiązań VMware platformy Azure jest dostarczana przez firmę Microsoft. Pamiętaj, że zgodnie z naszymi wskazówkami dotyczącymi wersji zapoznawczej firma Microsoft zapewnia pomoc techniczną w godzinach od 9 do 5 PM PST w piątek (poniedziałek). Możesz zgłosić bilet pomocy technicznej z [tego linku](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

### <a name="what-accounts-do-i-need-to-create-an-azure-vmware-solution-private-cloud"></a>Jakie konta muszę utworzyć chmurę prywatną rozwiązania Azure VMware?

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure.

### <a name="how-do-i-request-a-host-quota-increase-for-azure-vmware-solution"></a>Jak mogę zażądać zwiększenia limitu przydziału hosta dla rozwiązania Azure VMware?

* Będziesz potrzebować [platformy Azure Umowa Enterprise (EA)](https://docs.microsoft.com/azure/cost-management-billing/manage/ea-portal-agreements) z firmą Microsoft.
* Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure.

Przed utworzeniem zasobu rozwiązania Azure VMware należy przesłać bilet pomocy technicznej w celu przydzielenia węzłów. Gdy zespół pomocy technicznej otrzyma Twoje żądanie, zajmie do pięciu dni roboczych, aby potwierdzić żądanie i przydzielić węzły. Jeśli masz istniejącą chmurę prywatną rozwiązania VMware platformy Azure i chcesz uzyskać więcej węzłów, przejdziesz do tego samego procesu.


1. W Azure Portal w obszarze **Pomoc i obsługa techniczna**Utwórz **[nowe żądanie pomocy technicznej](https://rc.portal.azure.com/#create/Microsoft.Support)** i podaj następujące informacje dotyczące biletu:
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
   - Liczba węzłów
   - Wszelkie inne szczegóły

   >[!NOTE]
   >Rozwiązanie VMware firmy Azure zaleca co najmniej trzy węzły do roztworzenia chmury prywatnej oraz węzłów nadmiarowości N + 1. 

1. Wybierz pozycję **Przegląd + Utwórz** , aby przesłać żądanie.

   Potwierdzenie Twojego wniosku zostanie potrwać do pięciu dni roboczych.

   >[!IMPORTANT] 
   >Jeśli masz już istniejące rozwiązanie VMware platformy Azure i żądasz dodatkowych węzłów, pamiętaj, że potrzebujemy pięciu dni roboczych na przydzielenie węzłów. 

1. Przed udostępnieniem węzłów upewnij się, że rejestrujesz dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
