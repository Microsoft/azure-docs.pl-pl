---
title: Ogólne pytania dotyczące usługi Azure Site Recovery
description: W tym artykule omówiono popularne pytania dotyczące Azure Site Recovery.
ms.topic: conceptual
ms.date: 7/14/2020
ms.openlocfilehash: 974a119efaa69440aed6e6f9c3ac26ba26d7da16
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581165"
---
# <a name="general-questions-about-azure-site-recovery"></a>Ogólne pytania dotyczące usługi Azure Site Recovery

Ten artykuł zawiera podsumowanie często zadawanych pytań dotyczących Azure Site Recovery. W przypadku określonych scenariuszy przejrzyj te artykuły

- [Pytania dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure na platformie Azure](azure-to-azure-common-questions.md)
- [Pytania dotyczące odzyskiwania po awarii maszyny wirtualnej VMware na platformie Azure](vmware-azure-common-questions.md)
- [Pytania dotyczące odzyskiwania po awarii maszyny wirtualnej funkcji Hyper-V na platformie Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Ogólne

### <a name="what-does-site-recovery-do"></a>Do czego służy usługa Site Recovery?

Site Recovery współdziałanie z strategią zachowania ciągłości działania i odzyskiwania po awarii (BCDR), przez organizowanie i automatyzowanie replikacji maszyn wirtualnych platformy Azure między regionami, lokalnymi maszynami wirtualnymi i serwerami fizycznymi do platformy Azure oraz na maszynach lokalnych do dodatkowego centrum danych. [Dowiedz się więcej](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Czy mogę chronić maszynę wirtualną z dyskiem Docker?

Nie, ten scenariusz jest nieobsługiwany.

### <a name="what-does-site-recovery-do-to-ensure-data-integrity"></a>Co Site Recovery zrobić, aby zapewnić integralność danych?

Istnieją różne miary podejmowane przez Site Recovery w celu zapewnienia integralności danych. Nawiązywane jest bezpieczne połączenie między wszystkimi usługami przy użyciu protokołu HTTPS. Gwarantuje to, że żadne złośliwe oprogramowanie lub jednostki zewnętrzne nie mogą naruszać danych. Inna miara jest używana przy użyciu sum kontrolnych. Transfer danych między źródłem a celem jest wykonywany przez Obliczanie sum kontrolnych danych między nimi. Zapewnia to spójność transferowanych danych.

## <a name="service-providers"></a>Dostawcy usług

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Jestem dostawcą usług. Czy Site Recovery działa dla modeli infrastruktury dedykowanej i udostępnionej?
Tak. Usługa Site Recovery obsługuje modele oparte na infrastrukturze dedykowanej i współdzielonej.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Czy dla dostawcy usług jest tożsamość mojej dzierżawy współdzielona z usługą Site Recovery?
Nie. Tożsamość dzierżawy pozostaje anonimowa. Twoje dzierżawy nie wymagają dostępu do portalu usługi Site Recovery. Tylko administrator dostawcy usług wykonuje czynności w portalu.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Czy dane aplikacji dzierżawców będą kiedykolwiek znajdować się na platformie Azure?
Podczas replikacji między lokacjami należącymi do dostawcy usług dane aplikacji nigdy nie trafiają do platformy Azure. Dane są szyfrowane podczas przesyłania i replikowane bezpośrednio między lokacjami dostawcy usług.

Jeśli przeprowadzasz replikację do platformy Azure, dane aplikacji są wysyłane do usługi Azure Storage, ale nie do usługi Site Recovery. Dane są szyfrowane podczas przesyłania i pozostają zaszyfrowane na platformie Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Czy moje dzierżawy otrzymają rachunek za jakiekolwiek usługi platformy Azure?
Nie. Relacja rozliczeniowa platformy Azure jest nawiązana bezpośrednio z dostawcą usług. Dostawcy usług są odpowiedzialni za generowanie konkretnych rachunków dla swoich dzierżaw.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Jeśli przeprowadzam replikację do platformy Azure, czy moje maszyny wirtualne muszą być uruchomione na platformie Azure przez cały czas?
Nie. dane są replikowane do usługi Azure Storage w ramach subskrypcji. Podczas testowego (test odzyskiwania po awarii) lub rzeczywistego uruchamiania trybu failover usługa Site Recovery automatycznie tworzy maszyny wirtualne w ramach subskrypcji.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Czy podczas replikacji do platformy Azure jest zapewniana izolacja na poziomie dzierżawy?
Tak.

### <a name="what-platforms-do-you-currently-support"></a>Jakie platformy są obecnie obsługiwane?
Obsługiwane są wdrożenia pakietu Azure Pack, systemu platformy Cloud Platform i programu System Center (2012 i wyższych). [Dowiedz się więcej](/previous-versions/azure/windows-server-azure-pack/dn850370(v=technet.10)) o usłudze Azure Pack i integracji Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Czy są obsługiwane wdrożenia oparte na jednym pakiecie Azure Pack i jednym serwerze VMM?
Tak. maszyny wirtualne funkcji Hyper-V można replikować na platformę Azure lub między lokacjami dostawcy usług.  Należy pamiętać, że w przypadku replikowania między lokacjami dostawcy usług integracja elementu Runbook platformy Azure jest niedostępna.

## <a name="pricing"></a>Ceny

### <a name="where-can-i-find-pricing-information"></a>Gdzie mogę znaleźć informacje o cenach?
Przejrzyj szczegóły [cennika Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/) .


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Jak można obliczyć przybliżone opłaty podczas korzystania z Site Recovery?

Za pomocą [kalkulatora cen](https://aka.ms/asr_pricing_calculator) można oszacować koszty przy użyciu Site Recovery.

Aby uzyskać szczegółowe oszacowanie kosztów, uruchom narzędzie planista wdrażania dla oprogramowania [VMware](./site-recovery-deployment-planner.md) lub [funkcji Hyper-V](https://aka.ms/asr-deployment-planner), a następnie użyj [raportu szacowania kosztów](./site-recovery-vmware-deployment-planner-cost-estimation.md).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Dyski zarządzane są teraz używane do replikowania maszyn wirtualnych VMware i serwerów fizycznych. Czy są naliczane dodatkowe opłaty za konto magazynu pamięci podręcznej z dyskami zarządzanymi?

Nie. nie ma dodatkowych opłat za pamięć podręczną. W przypadku replikowania do konta magazynu w warstwie Standardowa ten magazyn pamięci podręcznej jest częścią tego samego docelowego konta magazynu.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Jestem użytkownikiem Azure Site Recovery przez ponad miesiąc. Czy nadal otrzymuję pierwsze 31 bezpłatnych dni dla każdego chronionego wystąpienia?

Tak. Każde chronione wystąpienie nie ma Azure Site Recovery opłat za pierwsze 31 dni. Na przykład jeśli w ciągu ostatnich 6 miesięcy włączono ochronę 10 wystąpień, a 11 wystąpienie zostało połączone z Azure Site Recovery, nie ma opłat za 11 wystąpienie przez pierwsze 31 dni. W pierwszych 10 wystąpieniach nadal są naliczane opłaty za Azure Site Recovery, ponieważ były one chronione przez więcej niż 31 dni.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Czy w ciągu pierwszych 31 dni będą naliczane inne opłaty za korzystanie z platformy Azure?

Tak, nawet jeśli Site Recovery jest bezpłatny w ciągu pierwszych 31 dni chronionego wystąpienia, mogą zostać naliczone opłaty za usługę Azure Storage, transakcje magazynowe i transfer danych. Odzyskana maszyna wirtualna może również naliczać opłaty za usługę Azure COMPUTE.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Czy istnieją koszty związane z przechodzeniem do odzyskiwania po awarii/testowaniem pracy w trybie failover?

Nie ma oddzielnego kosztu do testowania odzyskiwania po awarii. Po utworzeniu maszyny wirtualnej po przejściu do trybu failover zostaną naliczone opłaty za obliczenia.



## <a name="security"></a>Zabezpieczenia

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Czy dane replikacji są wysyłane do usługi Site Recovery?
Nie, Site Recovery nie przechwytuje replikowanych danych i nie zawiera żadnych informacji o działaniach na maszynach wirtualnych lub serwerach fizycznych.
Dane replikacji są wymieniane między lokalnymi hostami funkcji Hyper-V, funkcjami hypervisor VMware lub serwerami fizycznymi i usługą Azure Storage lub lokacją dodatkową. Usługa Site Recovery nie ma możliwości przechwycenia tych danych. Do usługi Site Recovery są wysyłane jedynie metadane wymagane do organizowania replikacji i trybu failover.  

Site Recovery to ISO 27001:2013, 27018, HIPAA, DPA certyfikowany i jest w trakcie oceny SOC2 i FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Ze względów związanych ze zgodnością nawet te metadane lokalne muszą pozostać w tym samym regionie geograficznym. Może Site Recovery pomóc nam?
Tak. Po utworzeniu magazynu Site Recovery w regionie firma Microsoft gwarantuje, że wszystkie metadane wymagane do włączenia i organizowania replikacji i trybu failover pozostają w granicach geograficznych tego regionu.

### <a name="does-site-recovery-encrypt-replication"></a>Czy usługa Site Recovery szyfruje replikację?
W przypadku maszyn wirtualnych i serwerów fizycznych replikacja między lokacjami lokalnymi jest obsługiwana. W przypadku maszyn wirtualnych i serwerów fizycznych replikowanych do platformy Azure obsługiwane są zarówno szyfrowanie podczas przesyłania, jak i [szyfrowanie (na platformie Azure)](../storage/common/storage-service-encryption.md) .

### <a name="does-azure-to-azure-site-recovery-use-tls-12-for-all-communications-across-microservices-of-azure"></a>Czy platforma Azure-Azure Site Recovery korzysta z protokołu TLS 1,2 dla całej komunikacji na mikrousługi na platformie Azure?
Tak, protokół TLS 1,2 jest wymuszany domyślnie dla scenariusza platformy Azure-to-Azure Site Recovery. 

### <a name="how-can-i-enforce-tls-12-on-vmware-to-azure-and-physical-server-to-azure-site-recovery-scenarios"></a>Jak wymusić protokół TLS 1,2 w scenariuszach z oprogramowaniem VMware-to-Azure i serwerem fizycznym do Azure Site Recovery?
Agenci mobilności instalowani na replikowanych elementach komunikują się tylko z serwerem przetwarzania tylko w protokole TLS 1,2. Jednak komunikacja z serwera konfiguracji do platformy Azure i z serwera przetwarzania na platformę Azure może należeć do protokołu TLS 1,1 lub 1,0. Postępuj zgodnie ze [wskazówkami](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) , aby wymusić szyfrowanie TLS 1,2 na wszystkich serwerach konfiguracji i serwerach przetwarzania skonfigurowanych przez użytkownika.

### <a name="how-can-i-enforce-tls-12-on-hyperv-to-azure-site-recovery-scenarios"></a>Jak wymusić protokół TLS 1,2 w scenariuszach HyperV-to-Azure Site Recovery?
Cała komunikacja między mikrousługami Azure Site Recovery odbywa się na protokole TLS 1,2. Site Recovery używa dostawców zabezpieczeń skonfigurowanych w systemie (OS) i używa najnowszego dostępnego protokołu TLS. Należy jawnie włączyć protokół TLS 1,2 w rejestrze, a następnie Site Recovery rozpocząć korzystanie z protokołu TLS 1,2 w celu komunikacji z usługami. 

### <a name="how-can-i-enforce-restricted-access-on-my-storage-accounts-which-are-accessed-by-site-recovery-service-for-readingwriting-replication-data"></a>Jak można wymusić ograniczony dostęp na kontach magazynu, do których uzyskuje się dostęp za pomocą usługi Site Recovery na potrzeby odczytywania/zapisywania danych replikacji?
Możesz przełączyć się na zarządzaną tożsamość magazynu usługi Recovery Services, przechodząc do ustawienia *tożsamości* . Po zarejestrowaniu magazynu za pomocą Azure Active Directory można przejść do kont magazynu i przypisać następujące przypisania ról do magazynu:

- Konta magazynu oparte na Menedżer zasobów (typ standardowy):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Współautor danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Konta magazynu oparte na Menedżer zasobów (typ warstwy Premium):
  - [Współautor](../role-based-access-control/built-in-roles.md#contributor)
  - [Właściciel danych obiektu blob usługi Storage](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasyczne konta magazynu:
  - [Współautor klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Rola usługi operatora kluczy klasycznego konta magazynu](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

## <a name="disaster-recovery"></a>Odzyskiwanie po awarii

### <a name="what-can-site-recovery-protect"></a>Co może Site Recovery chronić?
* **Maszyny wirtualne platformy Azure**: Site Recovery mogą replikować dowolne obciążenia uruchomione na OBSŁUGIWANEJ maszynie wirtualnej platformy Azure
* **Maszyny wirtualne funkcji Hyper-v**: Site Recovery może chronić każde obciążenie działające na maszynie wirtualnej funkcji Hyper-v.
* **Serwery fizyczne**: Site Recovery może chronić serwery fizyczne z systemem Windows lub Linux.
* **Maszyny wirtualne VMware**: Site Recovery może chronić każde obciążenie działające na maszynie wirtualnej VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Jakie obciążenia mogę chronić za pomocą usługi Site Recovery?
Za pomocą Site Recovery można chronić większość obciążeń uruchomionych na obsługiwanej maszynie wirtualnej lub serwerze fizycznym. Site Recovery zapewnia obsługę replikacji obsługującej aplikacje, dzięki czemu aplikacje mogą być odzyskiwane do stanu inteligentnego. Integruje się z aplikacjami firmy Microsoft, takimi jak SharePoint, Exchange, Dynamics, SQL Server i Active Directory, i ściśle współpracuje z wiodącymi dostawcami, takimi jak Oracle, SAP, IBM i Red Hat. [Dowiedz się więcej](site-recovery-workload.md) o ochronie obciążeń.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Czy mogę zarządzać odzyskiwaniem po awarii dla oddziałów firmy przy użyciu usługi Site Recovery?
Tak. W przypadku korzystania z Site Recovery do organizowania replikacji i trybu failover w biurach oddziałów będziesz mieć ujednoliconą aranżację i wgląd we wszystkie obciążenia biur oddziałów w centralnej lokalizacji. Możesz z łatwością uruchomić tryb failover i zarządzać odzyskiwaniem po awarii wszystkich oddziałów z siedziby, bez konieczności odwiedzania oddziałów.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych platformy Azure?

Tak, Site Recovery obsługuje awarie maszyn wirtualnych platformy Azure między regionami platformy Azure. [Przejrzyj typowe pytania](azure-to-azure-common-questions.md) dotyczące odzyskiwania po awarii maszyny wirtualnej platformy Azure. Jeśli chcesz przeprowadzić replikację między dwoma regionami świadczenia usługi Azure w tym samym kontynentie, Skorzystaj z naszej oferty Azure DR. Nie trzeba konfigurować serwera konfiguracji/serwera przetwarzania ani połączeń ExpressRoute.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych VMware?

Tak, Site Recovery obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware. Zapoznaj się z [typowymi pytaniami](vmware-azure-common-questions.md) dotyczącymi odzyskiwania po awarii maszyn wirtualnych VMware.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>Czy odzyskiwanie po awarii jest obsługiwane dla maszyn wirtualnych funkcji Hyper-V?
Tak, Site Recovery obsługuje odzyskiwanie po awarii lokalnych maszyn wirtualnych funkcji Hyper-V. [Przejrzyj typowe pytania](hyper-v-azure-common-questions.md) dotyczące odzyskiwania po awarii maszyn wirtualnych funkcji Hyper-V.

### <a name="is-disaster-recovery-supported-for-physical-servers"></a>Czy w przypadku serwerów fizycznych jest obsługiwane odzyskiwanie awaryjne?
Tak, Site Recovery obsługuje odzyskiwanie po awarii lokalnych serwerów fizycznych z systemami Windows i Linux na platformę Azure lub do lokacji dodatkowej. Dowiedz się więcej o wymaganiach dotyczących odzyskiwania po awarii na [platformie Azure](vmware-physical-azure-support-matrix.md#replicated-machines)oraz[lokacji dodatkowej](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
Należy pamiętać, że serwery fizyczne będą uruchamiane jako maszyny wirtualne na platformie Azure po przejściu w tryb failover. Powrót po awarii z platformy Azure do lokalnego serwera fizycznego nie jest obecnie obsługiwany. Można wrócić po awarii na maszynę wirtualną VMware.





## <a name="replication"></a>Replikacja

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Czy można replikować za pośrednictwem sieci VPN typu lokacja-lokacja do platformy Azure?
Azure Site Recovery replikuje dane na konto usługi Azure Storage lub na dyskach zarządzanych za pośrednictwem publicznego punktu końcowego. Replikacja nie jest oparta na sieci VPN typu lokacja-lokacja. 

### <a name="why-cant-i-replicate-over-vpn"></a>Dlaczego nie można replikować za pośrednictwem sieci VPN?

W przypadku replikacji na platformę Azure ruch związany z replikacją dociera do publicznych punktów końcowych usługi Azure Storage. W ten sposób można replikować tylko za pośrednictwem publicznej sieci Internet lub za pośrednictwem ExpressRoute (Komunikacja równorzędna firmy Microsoft lub istniejąca publiczna Komunikacja równorzędna).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Czy można używać Riverbed SteelHeads do replikacji?

Nasz partner, Riverbed, zawiera szczegółowe wskazówki dotyczące pracy z Azure Site Recoveryami. Zapoznaj się z [przewodnikiem po rozwiązaniu](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Czy można używać ExpressRoute do replikowania maszyn wirtualnych na platformę Azure?
Tak, [ExpressRoute może służyć](concepts-expressroute-with-site-recovery.md) do replikowania lokalnych maszyn wirtualnych na platformę Azure.

- Azure Site Recovery replikuje dane do usługi Azure Storage za pośrednictwem publicznego punktu końcowego. Należy skonfigurować [komunikację równorzędną firmy Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) lub używać istniejącej [publicznej komunikacji równorzędnej](../expressroute/about-public-peering.md) (przestarzałe dla nowych obwodów) do używania ExpressRoute do replikacji Site Recovery.
- Komunikacja równorzędna firmy Microsoft jest zalecaną domeną routingu na potrzeby replikacji.
- Replikacja nie jest obsługiwana w przypadku prywatnej komunikacji równorzędnej.
- Jeśli chronisz maszyny wirtualne lub maszyny fizyczne, upewnij się, że zostały spełnione także [wymagania dotyczące sieci](vmware-azure-configuration-server-requirements.md#network-requirements) dla serwera konfiguracji. Serwer konfiguracji wymaga połączenia z określonymi adresami URL w celu aranżacji replikacji Site Recovery. Nie można użyć ExpressRoute dla tej łączności.
- Po przełączeniu maszyny wirtualnej w tryb failover do sieci wirtualnej platformy Azure możesz uzyskać do nich dostęp za pomocą konfiguracji [prywatnej komunikacji równorzędnej](../expressroute/expressroute-circuit-peerings.md#privatepeering) w sieci wirtualnej platformy Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Jeśli replika na platformę Azure, jakiego typu konto magazynu lub dysk zarządzany jest potrzebny?

Korzystanie z kont magazynu jako magazynu docelowego nie jest obsługiwane przez Azure Site Recovery. Zalecane jest raczej używanie dysków zarządzanych jako magazynu docelowego dla maszyn. Dyski zarządzane obsługują tylko typ LRS dla odporności danych.

### <a name="how-often-can-i-replicate-data"></a>Jak często mogę replikować dane?
* **Funkcja Hyper-V:** Maszyny wirtualne funkcji Hyper-V mogą być replikowane co 30 sekund (z wyjątkiem magazynu Premium Storage), pięć minut lub 15 minut.
* **Maszyny wirtualne platformy Azure, maszyny wirtualne VMware, serwery fizyczne:** Częstotliwość replikacji nie jest odpowiednia w tym miejscu. Replikacja jest ciągła.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Czy mogę zwiększyć replikację z istniejącej lokacji odzyskiwania do innej lokacji o większej funkcjonalności?
Replikacja rozszerzona lub łańcuchowa nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Czy mogę przeprowadzić replikację w trybie offline podczas pierwszej replikacji do platformy Azure?
Ta funkcja nie jest obsługiwana. Zażądaj tej funkcji na [forum opinii](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Czy z replikacji można wykluczyć określone dyski?
Jest to obsługiwane w przypadku replikowania maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V na platformę Azure przy użyciu Azure Portal.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Czy można replikować maszyny wirtualne z dyskami dynamicznymi?
Dyski dynamiczne są obsługiwane podczas replikowania maszyn wirtualnych funkcji Hyper-V oraz replikacji maszyn wirtualnych VMware i maszyn fizycznych na platformę Azure. Dysk systemu operacyjnego musi być dyskiem podstawowym.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Czy można ograniczyć przepustowość przydzieloną dla ruchu związanego z replikacją?
Tak. Więcej informacji na temat ograniczania przepustowości można znaleźć w następujących artykułach:

* [Planowanie pojemności do replikowania maszyn wirtualnych VMware i serwerów fizycznych](site-recovery-plan-capacity-vmware.md)
* [Planowanie pojemności replikowania maszyn wirtualnych funkcji Hyper-V na platformę Azure](./hyper-v-deployment-planner-overview.md)

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Czy mogę włączyć replikację z spójnością aplikacji na serwerach z systemem Linux? 
Tak. Azure Site Recovery dla systemu operacyjnego Linux obsługuje niestandardowe skrypty aplikacji na potrzeby spójności aplikacji. Skrypt niestandardowy z opcjami pre i post będzie używany przez agenta mobilności Azure Site Recovery podczas spójności aplikacji. Poniżej znajdują się kroki umożliwiające włączenie tego programu.

1. Zaloguj się jako katalog główny na komputerze.
2. Zmień katalog na Azure Site Recovery lokalizację instalacji agenta mobilności. Wartość domyślna to "/usr/local/ASR"<br>
    `# cd /usr/local/ASR`
3. Zmień katalog na "VX/Scripts" w lokalizacji instalacji<br>
    `# cd VX/scripts`
4. Utwórz skrypt powłoki bash o nazwie "customscript.sh" z uprawnieniami wykonywania dla użytkownika root.<br>
    a. Skrypt powinien obsługiwać "--pre" i "--post" (należy pamiętać o podwójnych kreskach) opcje wiersza polecenia<br>
    b. Gdy skrypt jest wywoływany przy użyciu opcji pre-Option, powinien zablokować dane wejściowe/wyjściowe aplikacji oraz, gdy zostanie wywołane za pomocą polecenia post, powinno odblokować dane wejściowe/wyjściowe aplikacji.<br>
    c. Przykładowy szablon —<br>

    `# cat customscript.sh`<br>

```
    #!/bin/bash

    if [ $# -ne 1 ]; then
        echo "Usage: $0 [--pre | --post]"
        exit 1
    elif [ "$1" == "--pre" ]; then
        echo "Freezing app IO"
        exit 0
    elif [ "$1" == "--post" ]; then
        echo "Thawed app IO"
        exit 0
    fi
```

5. Dodawanie poleceń Zablokuj i Odblokuj dane wejściowe/wyjściowe w przypadku aplikacji wymagających spójności aplikacji. Możesz dodać inny skrypt określający te i wywołać go z elementu "customscript.sh" za pomocą opcji pre i post.

>[!Note]
>Wersja agenta Site Recovery powinna mieć wartość 9,24 lub wyższą, aby można było obsługiwać skrypty niestandardowe.

## <a name="replication-policy"></a>Zasady replikacji

### <a name="what-is-a-replication-policy"></a>Co to są zasady replikacji?

Zasady replikacji określają ustawienia dla historii przechowywania punktów odzyskiwania. Zasady definiują również częstotliwość migawek spójnych z aplikacjami. Domyślnie Azure Site Recovery tworzy nowe zasady replikacji z ustawieniami domyślnymi:

- 24 godziny dla historii przechowywania punktów odzyskiwania.
- 4 godziny dla częstotliwości migawek spójnych z aplikacjami.

### <a name="what-is-a-crash-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie awarii?

Punkt odzyskiwania spójny na poziomie awarii zawiera dane na dysku, tak jak w przypadku ściągania przewodu z serwera podczas tworzenia migawki. Punkt odzyskiwania spójny na poziomie awarii nie obejmuje niczego, co było w pamięci podczas tworzenia migawki.

Obecnie większość aplikacji może odzyskać z migawek spójnych na poziomie awarii. Punkt odzyskiwania spójny na poziomie awarii jest zwykle wystarczający dla systemów operacyjnych i aplikacji, takich jak serwery plików, serwery DHCP i serwery wydruku.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Jaka jest częstotliwość generowania punktów odzyskiwania spójnych na poziomie awarii?

Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut.

### <a name="what-is-an-application-consistent-recovery-point"></a>Co to jest punkt odzyskiwania spójny na poziomie aplikacji?

Punkty odzyskiwania spójne z aplikacją są tworzone na podstawie migawek spójnych z aplikacjami. Punkty odzyskiwania spójne z aplikacją przechwytują te same dane co migawki spójne z awarią, a także przechwytują dane w pamięci i wszystkie transakcje w procesie.

Ze względu na dodatkową zawartość migawki spójne z aplikacjami są najbardziej wykorzystywane i są najdłuższe. Zalecamy używanie punktów odzyskiwania spójnych na poziomie aplikacji dla systemów operacyjnych i aplikacji baz danych, takich jak SQL Server.

>[!Note]
>Tworzenie punktów odzyskiwania spójnych na poziomie aplikacji kończy się niepowodzeniem na komputerze z systemem Windows, jeśli ma więcej niż 64 woluminów.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Jaki jest wpływ punktów odzyskiwania spójnych na poziomie aplikacji na wydajność aplikacji?

Punkty odzyskiwania spójne z aplikacją przechwytują wszystkie dane w pamięci i w procesie. Ponieważ punkty odzyskiwania przechwytują te dane, wymagają one struktury, takiej jak Usługa kopiowania woluminów w tle w systemie Windows w celu przełączenia aplikacji w stan spoczynku. Jeśli proces przechwytywania jest częsty, może mieć wpływ na wydajność, gdy obciążenie jest już zajęte. Nie zalecamy używania niskich częstotliwości dla punktów odzyskiwania spójnych z aplikacjami dla obciążeń niezwiązanych z bazą danych. Nawet w przypadku obciążenia bazy danych wystarczy 1 godzinę.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Jaka jest minimalna częstotliwość generowania punktów odzyskiwania spójnych z aplikacjami?

Site Recovery można utworzyć punkt odzyskiwania spójny dla aplikacji o minimalnej częstotliwości wynoszącej 1 godzinę.

### <a name="how-are-recovery-points-generated-and-saved"></a>Jak są generowane i zapisywane punkty odzyskiwania?

Aby zrozumieć, jak Site Recovery generuje punkty odzyskiwania, zobacz przykład zasad replikacji. Te zasady replikacji mają punkt odzyskiwania z 24-godzinnym okresem przechowywania i migawką częstotliwości zgodną z aplikacją (1 godzina).

Site Recovery tworzy punkt odzyskiwania spójny na poziomie awarii co 5 minut. Tej częstotliwości nie można zmienić. W ciągu ostatniej godziny można wybrać jeden z 12 punktów spójnych z awarią i 1 punkt spójności aplikacji. W miarę upływu czasu program Site Recovery czyści wszystkie punkty odzyskiwania poza ostatnią godziną i zapisuje tylko 1 punkt odzyskiwania na godzinę.

Poniższy zrzut ekranu ilustruje przykład. Zrzut ekranu:

- W ciągu ostatniej godziny istnieją punkty odzyskiwania z częstotliwością wynoszącą 5 minut.
- W ciągu ostatniej godziny Site Recovery zachowuje tylko 1 punkt odzyskiwania.

   ![Lista wygenerowanych punktów odzyskiwania](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>Jak daleko z powrotem mogę odzyskać?

Najstarszym punktem odzyskiwania, którego można użyć, jest 72 godzin.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Mam zasady replikacji przez 24 godziny. Co się stanie, jeśli problem uniemożliwia Site Recovery wygenerowanie punktów odzyskiwania przez ponad 24 godziny? Czy moje poprzednie punkty odzyskiwania zostaną utracone?

Nie, Site Recovery będzie przechowywać wszystkie poprzednie punkty odzyskiwania. W zależności od okna przechowywania punktów odzyskiwania Site Recovery zastępuje najstarszy punkt tylko wtedy, gdy generuje nowe punkty. Z powodu problemu Site Recovery nie może wygenerować żadnych nowych punktów odzyskiwania. Dopóki nie pojawią się nowe punkty odzyskiwania, wszystkie stare punkty pozostaną po przejściu do okna przechowywania.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Jak zmienić zasady replikacji po włączeniu replikacji na maszynie wirtualnej?

Przejdź do **magazynu Site Recovery**  >  **Site Recovery**  >  **zasady replikacji** infrastruktury. Wybierz zasady, które chcesz edytować, i Zapisz zmiany. Każda zmiana zostanie również zastosowana do wszystkich istniejących replikacji.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Czy wszystkie punkty odzyskiwania są kompletną kopią maszyny wirtualnej czy różnicą?

Pierwszy wygenerowany punkt odzyskiwania ma kompletną kopię. Wszystkie kolejne punkty odzyskiwania mają zmiany różnicowe.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Czy zwiększenie okresu przechowywania punktów odzyskiwania zwiększa koszt magazynu?

Tak, jeśli okres przechowywania zostanie zwiększony z 24 godzin do 72 godzin, Site Recovery będzie zapisywać punkty odzyskiwania dla dodatkowych 48 godzin. Dodatkowy czas będzie powodować naliczanie opłat za magazyn. Na przykład pojedynczy punkt odzyskiwania może mieć zmiany różnicowe o wartości 10 GB i koszt za GB wynoszący $0,16 miesięcznie. Dodatkowe opłaty byłyby $1,60 × 48 miesięcznie.


## <a name="failover"></a>Tryb failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>W jaki sposób można uzyskać dostęp do maszyn wirtualnych platformy Azure po przejściu w tryb failover?

Maszyny wirtualne Azure są dostępne za pośrednictwem bezpiecznego połączenia internetowego, sieci VPN między lokacjami lub za pośrednictwem usługi Azure ExpressRoute. Aby nawiązać połączenie, należy przygotować wiele rzeczy. [Dowiedz się więcej](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Jeśli na platformie Azure działa tryb failover na platformie Azure, upewnij się, że moje dane są odporne?
Platforma Azure została zaprojektowana z myślą o odporności danych. Site Recovery został już zaprojektowany w celu przełączenia w tryb failover do pomocniczego centrum danych platformy Azure zgodnie z umową SLA platformy Azure. W takim przypadku upewnij się, że Twoje metadane i magazyny pozostają w tym samym regionie geograficznym, który został wybrany dla Twojego magazynu.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Jeśli przeprowadzam replikację między dwoma centrami danych, co się stanie, jeśli wystąpiło nieoczekiwane przestoje w podstawowym centrum danych?
Możesz wyzwolić nieplanowany tryb failover z lokacji dodatkowej. Usługa Site Recovery nie wymaga łączności z lokacją główną do pracy w trybie failover.

### <a name="is-failover-automatic"></a>Czy tryb failover jest automatyczny?
Tryb failover nie jest automatyczny. Aby wyzwolić pracę w trybie failover, można zainicjować pracę w trybie failover za pomocą pojedynczego kliknięcia w portalu lub użyć [programu Site Recovery PowerShell](/powershell/module/az.recoveryservices) . Powrót po awarii jest prostą akcją w portalu Site Recovery.

Aby zautomatyzować, można użyć lokalnego programu Orchestrator lub Operations Manager, aby wykryć awarię maszyny wirtualnej, a następnie wyzwolić tryb failover przy użyciu zestawu SDK.

* [Przeczytaj więcej](site-recovery-create-recovery-plans.md) na temat planów odzyskiwania.
* [Przeczytaj więcej](site-recovery-failover.md) na temat trybu failover.
* [Przeczytaj więcej](./vmware-azure-failback.md) na temat awarii maszyn wirtualnych VMware i serwerów fizycznych z powrotem

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Czy w przypadku braku odpowiedzi lub awarii hosta lokalnego można wrócić do innego hosta?
Tak. odzyskiwanie lokalizacji alternatywnej można użyć do powrotu po awarii do innego hosta z platformy Azure.

* [Dla maszyn wirtualnych VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Dla maszyn wirtualnych funkcji Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

### <a name="what-is-the-difference-between-complete-migration-commit-and-disable-replication"></a>Jaka jest różnica między ukończeniem migracji, zatwierdzaniem i wyłączaniem replikacji?

Gdy maszyna z lokalizacji źródłowej została przełączona w tryb failover do lokalizacji docelowej, dostępne są trzy opcje do wyboru. Wszystkie trzy funkcje w różnych celach —

1.  **Pełna migracja** oznacza, że nie można już wrócić do lokalizacji źródłowej. Przeprowadzono migrację do regionu docelowego i teraz wszystko jest gotowe. Kliknięcie przycisku Ukończ wyzwalacze migracji spowoduje zatwierdzenie, a następnie wyłączenie replikacji wewnętrznie. 
2.  **Zatwierdzenie** oznacza, że nie jest to koniec procesu replikacji. Element replikacji wraz z całą konfiguracją pozostanie niezmieniony i można ponownie włączyć **ochronę** w późniejszym momencie, aby umożliwić replikację maszyn z powrotem do regionu źródłowego. 
3.  Wartość **Wyłącz replikację** spowoduje wyłączenie replikacji i usunięcie całej konfiguracji powiązanej. Nie wpłynie to na już istniejący komputer w regionie docelowym.

## <a name="automation"></a>Automation

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Czy można zautomatyzować scenariusze Site Recovery przy użyciu zestawu SDK?
Tak. Przepływy pracy usługi Site Recovery można zautomatyzować przy użyciu interfejsu API REST, programu PowerShell lub zestawu SDK platformy Azure. Obecnie obsługiwane scenariusze wdrażania Site Recovery przy użyciu programu PowerShell:

* [Replikowanie maszyn wirtualnych funkcji Hyper-V w chmurach usługi zarządzania w celu Azure PowerShell Menedżer zasobów](hyper-v-vmm-powershell-resource-manager.md)
* [Replikowanie maszyn wirtualnych funkcji Hyper-V bez programu VMM do Azure PowerShell Menedżer zasobów](hyper-v-azure-powershell-resource-manager.md)
* [Replikowanie oprogramowania VMware na platformę Azure za pomocą programu PowerShell Menedżer zasobów](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Uaktualnianie składnika/dostawcy

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Gdzie można znaleźć informacje o wersji/pakiet zbiorczy aktualizacji Site Recovery Upgrades

[Dowiedz się więcej](site-recovery-whats-new.md) o nowych aktualizacjach i [Pobierz informacje zbiorcze](service-updates-how-to.md).

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [omówieniem usługi Site Recovery](site-recovery-overview.md)