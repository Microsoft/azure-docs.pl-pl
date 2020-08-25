---
title: Często zadawane pytania
description: Zawiera odpowiedzi na niektóre często zadawane pytania dotyczące rozwiązań VMware platformy Azure.
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: dikamath
ms.openlocfilehash: cffa31bb66adfde2af24ab2542322479639ed9dd
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752184"
---
# <a name="frequently-asked-questions-about-azure-vmware-solution-preview"></a>Często zadawane pytania dotyczące wersji zapoznawczej rozwiązań VMware platformy Azure

Odpowiedzi na często zadawane pytania dotyczące rozwiązań VMware platformy Azure.

## <a name="general"></a>Ogólne

**Co to jest rozwiązanie Azure VMware?**

W miarę jak przedsiębiorstwa wykonują strategie modernizacji, aby poprawić elastyczność biznesową, obniżyć koszty i przyspieszyć innowacje, hybrydowe platformy w chmurze zostały wyznaczone jako klucze do obsługi transformacji cyfrowej klientów. Rozwiązanie VMware platformy Azure łączy oprogramowanie firmy VMware z centrum danych (SDDC), które ma Microsoft Azure globalny ekosystem usług w chmurze. Rozwiązaniem VMware platformy Azure jest zarządzanie w celu spełnienia wymagań dotyczących wydajności, dostępności, zabezpieczeń i zgodności.

## <a name="azure-vmware-solution-service"></a>Usługa rozwiązań VMware platformy Azure

**Gdzie jest dostępne rozwiązanie Azure VMware już dzisiaj?**

Usługa jest ciągle dodawana do nowych regionów. Aby uzyskać więcej informacji, zobacz [najnowsze informacje o dostępności usługi](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) . 

**Czy obciążenia działające w ramach wystąpienia rozwiązania VMware platformy Azure zużywają lub integrują się z usługami platformy Azure?**

Wszystkie usługi platformy Azure będą dostępne dla klientów rozwiązań VMware platformy Azure. Ograniczenia dotyczące wydajności i dostępności dla określonych usług muszą być rozdzielone na zasadzie wielkości liter.

**Czy mogę użyć tych samych narzędzi, których teraz używam do zarządzania zasobami w chmurze prywatnej?**

Tak. Azure Portal jest używany do wdrażania i wielu operacji zarządzania. Program vCenter i NSX Manager są używane do zarządzania zasobami vSphere i NSX-T.

**Czy mogę zarządzać chmurą prywatną przy użyciu lokalnego programu vCenter?**

Po uruchomieniu rozwiązanie VMware dla platformy Azure nie będzie obsługiwało jednego środowiska zarządzania w środowiskach lokalnych i prywatnych. Klastry chmury prywatnej będą zarządzane za pomocą programu vCenter i NSX Manager lokalnie w chmurze prywatnej.

**Czy mogę używać pakietu vRealize Suite uruchomionego lokalnie?** 

Konkretne integracje i przypadki użycia mogą być oceniane w zależności od wielkości liter.

**Czy można migrować maszyny wirtualne vSphere z środowisk lokalnych do chmur prywatnych rozwiązań platformy Azure VMware?**

Tak. Migracja maszyn wirtualnych i vMotion mogą służyć do przenoszenia maszyn wirtualnych do chmury prywatnej, jeśli są spełnione standardowe wymagania programu vCenter [vMotion](https://kb.vmware.com/s/article/210695) .

**Czy określona wersja vSphere jest wymagana w środowiskach lokalnych?**

Ze względu na to, że wszystkie środowiska chmury są dostarczane z HCX, vSphere 5,5 lub nowszym w środowiskach lokalnych dla vMotion.

**Jak wygląda proces kontroli zmian?**

Aktualizacje wprowadzone do usługi będą zgodne z procesem standardowego zarządzania zmianami Microsoft Azure. Klienci są odpowiedzialni za wszelkie zadania administracyjne dotyczące obciążeń i powiązane procesy zarządzania zmianami.

**Jak różni się to od rozwiązania Azure VMware CloudSimple?**

Korzystając z nowego rozwiązania VMware platformy Azure, firma Microsoft i oprogramowanie VMware mają bezpośrednie powiązanie z dostawcą usług w chmurze. Nowe rozwiązanie jest w pełni zaprojektowane, stworzone i obsługiwane przez firmę Microsoft i potwierdzone przez oprogramowanie VMware. Architektura rozwiązań jest spójna, dzięki stosowi technologii VMware działającego w dedykowanej infrastrukturze platformy Azure.

**Jeśli jestem istniejącym klientem rozwiązań VMware platformy Azure, co oznacza w tej wersji zapoznawczej?**

Nie wprowadzono zmian w istniejącym rozwiązaniu VMware platformy Azure przez CloudSimple. Nadal obsługujemy rozwiązanie na platformie Azure. Rozwiązanie VMware platformy Azure jest objęte umową dotyczącą poziomu usług [(SLA)](https://aka.ms/CSVMwareSLA). Klienci powinni nadal korzystać z usługi w przypadku obciążeń produkcyjnych. jest to dostępne rozwiązanie, które podlega postanowieniom [dotyczącym usług firmy Microsoft](https://azure.microsoft.com/support/legal/).

**Czy mogę przeprowadzić migrację z rozwiązania Azure VMware, CloudSimple do tego nowego rozwiązania?**

Tak, rozwiązanie VMware platformy Azure obsługuje migrację przy użyciu znanych narzędzi VMware, takich jak HCX. W przypadku klientów, którzy chcą przeprowadzić migrację do nowego rozwiązania, skontaktuj się z zespołem konto Microsoft, aby poznać opcje i dostępną pomoc techniczną.



## <a name="compute-network-and-storage"></a>Obliczenia, Sieć i magazyn

**Czy jest dostępny więcej niż jeden typ hosta?**

Dostępny jest tylko jeden typ hosta.

**Jakie są specyfikacje procesora dla każdego typu hosta?**

Serwery mają dwa procesory Intel procesorów 2,3 GHz.

**Ile pamięci znajduje się na każdym hoście?**

Serwery mają 576 GB pamięci RAM.

**Co to jest pojemność magazynu każdego hosta?**

Każdy host ESXi ma dwie sieci vSAN diskgroups z warstwą wydajności wynoszącą 15,2 TB, a 3,2 TB warstwy pamięci podręcznej interfejsu NVMe (1,6 TB w każdej z nich).

**Jaka przepustowość sieci jest dostępna na każdym hoście ESXi?**

Każdy host ESXi ma rozwiązanie Azure VMware, które jest skonfigurowane z kartami sieciowymi 4 25 GB/s, z dwiema kartami sieciowymi obsługującymi ruch systemu ESXi i dwie karty sieciowe obsługiwane dla ruchu obciążeń. 

**Czy dane są przechowywane w magazynach danych sieci vSAN szyfrowanych w spoczynku?**

Tak, wszystkie dane sieci vSAN są szyfrowane domyślnie przy użyciu kluczy przechowywanych w Azure Key Vault.

## <a name="hosts-clusters-and-private-clouds"></a>Hosty, klastry i chmury prywatne

**Czy podstawowa infrastruktura jest udostępniona?**

Nie, hosty i klastry w chmurze prywatnej są dedykowane i bezpiecznie usuwane przed i po użyciu.

**Co to jest minimalna i Maksymalna liczba hostów na klaster?**

Klastry mogą skalować od 3 do 16 hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

**Czy mogę skalować swoje klastry chmur prywatnych?**

Tak, liczba klastrów jest skalowana między minimalną i maksymalną liczbą hostów ESXi. Klastry próbne są ograniczone do trzech hostów.

**Co to są klastry próbne?**

Klastry próbne to trzy klastry hostów używane do oceny w jednym miesiącu chmur prywatnych rozwiązań Azure VMware.

**Czy można używać hostów wysokiej klasy na potrzeby klastrów próbnych?**

Nie. Hosty wysokiej klasy ESXi są zarezerwowane do użytku w klastrach produkcyjnych.

## <a name="azure-vmware-solution-and-vmware-software"></a>Rozwiązanie VMware i oprogramowanie VMware platformy Azure

**Jakie wersje oprogramowania VMware są używane w chmurach prywatnych?**

Chmury prywatne używają vSphere 6,7, sieci vSAN 6,7, HCX i wersji 2,5 NSX-T.  

**Czy Chmura prywatna korzysta z programu VMware NSX?**

Tak, NSX-T 2,5 jest używany w przypadku sieci zdefiniowanej przez oprogramowanie w chmurach prywatnych rozwiązań VMware platformy Azure.

**Czy mogę używać oprogramowania VMware NSX-V w chmurze prywatnej?**

Nie. NSX-T jest jedyną obsługiwaną wersją programu NSX.

**NSX jest wymagany w środowiskach lokalnych lub sieciach, które łączą się z chmurą prywatną.**

Nie, nie musisz używać NSX lokalnie.

**Co to jest harmonogram uaktualniania i aktualizacji oprogramowania VMware w chmurze prywatnej?**

Uaktualnienia pakietu oprogramowania w chmurze prywatnej są wykonywane w celu zachowania oprogramowania w ramach jednej wersji najnowszej wersji pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN).

**Jak często będzie aktualizowany stos oprogramowania chmury prywatnej?**

Oprogramowanie chmury prywatnej jest uaktualniane zgodnie z harmonogramem, który śledzi wersję pakietu oprogramowania z programu VMware. Chmura prywatna nie wymaga przestojów w przypadku uaktualnień.

## <a name="connectivity"></a>Łączność

**Jakie Planowanie adresów IP sieci jest wymagane w celu uwzględnienia chmur prywatnych w środowiskach lokalnych?**

Do wdrożenia chmury prywatnej rozwiązania Azure VMware jest wymagane miejsce w sieci prywatnej/22. Ta prywatna przestrzeń adresowa nie powinna nakładać się na inne sieci wirtualne w ramach subskrypcji lub sieci lokalnych.
 
**Jak mogę połączyć się ze środowiska lokalnego z chmurą prywatną rozwiązania Azure VMware?**

Można nawiązać połączenie z usługą przy użyciu jednej z dwóch metod: 

- Z MASZYNą wirtualną lub bramą aplikacji wdrożoną w sieci wirtualnej platformy Azure, która jest połączona za pomocą usługi ExpressRoute z chmurą prywatną.
- Za pośrednictwem ExpressRoute Global Reach z lokalnego centrum danych do obwodu usługi Azure ExpressRoute.

**Jak mogę połączyć maszynę wirtualną obciążenia z Internetem lub punktem końcowym usługi platformy Azure?**

W Azure Portal Włącz łączność z Internetem w chmurze prywatnej. Za pomocą Menedżera NSX-T Utwórz router T1 NSX-T i przełącznik logiczny. Następnie można użyć programu vCenter do wdrożenia maszyny wirtualnej w segmencie sieci zdefiniowanym przez przełącznik logiczny. Ta maszyna wirtualna będzie miała dostęp sieciowy do Internetu i usług platformy Azure.

**Czy muszę ograniczyć dostęp z Internetu do maszyn wirtualnych w sieciach logicznych w chmurze prywatnej?**

Nie. Ruch sieciowy przychodzący z Internetu bezpośrednio do chmur prywatnych nie jest dozwolony.

**Czy muszę ograniczyć dostęp do Internetu z maszyn wirtualnych w sieciach logicznych do Internetu?**

Tak. Aby utworzyć zaporę ograniczającą dostęp do sieci wirtualnej, należy użyć Menedżera NSX-T.

## <a name="accounts-and-privileges"></a>Konta i uprawnienia

**Jakie konta i uprawnienia uzyskasz przy użyciu nowej chmury prywatnej rozwiązania Azure VMware?**

Masz poświadczenia dla użytkownika cloudadmin w programie vCenter i dostęp administracyjny do Menedżera NSX-T. Istnieje również Grupa CloudAdmin, która może służyć do uwzględnienia Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

**Czy może mieć dostęp administratora do hostów ESXi?**

Nie, dostęp administratora do ESXi jest ograniczony do spełnienia wymagań dotyczących zabezpieczeń rozwiązania.

**Jakie uprawnienia i uprawnienia są dostępne w programie vCenter?**

Będziesz mieć uprawnienia do grupy CloudAdmin. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

**Jakie uprawnienia i uprawnienia są dostępne w Menedżerze NSX-T?**

Masz pełne uprawnienia administratora na NSX-T i mogą zarządzać kontrolą dostępu opartą na rolach, tak jak w przypadku lokalnego centrum danych NSX-T. Aby uzyskać więcej informacji, zobacz [pojęcia dotyczące dostępu i tożsamości](concepts-identity.md).

> [!NOTE]
> Router T0 jest tworzony i konfigurowany w ramach wdrożenia chmury prywatnej. Wszelkie modyfikacje tego routera logicznego lub maszyn wirtualnych węzła brzegowego NSX-T mogą mieć wpływ na łączność z chmurą prywatną.

## <a name="billing-and-support"></a>Rozliczenia i pomoc techniczna

**Jak będą naliczane opłaty w trakcie korzystania z wersji zapoznawczej rozwiązania Azure VMware**

Rozliczanie za rozwiązanie VMware dla platformy Azure w okresie zapoznawczym jest comiesięczne w oparciu o płatność zgodnie z rzeczywistym użyciem. Dodatkowe opcje będą dostępne w ogólnej dostępności.

**W jaki sposób Cennik będzie miał strukturę w trakcie korzystania z wersji zapoznawczej rozwiązania Azure VMware?**

Aby uzyskać ogólne pytania dotyczące cen, zobacz stronę z [cennikiem](https://azure.microsoft.com/pricing/details/azure-vmware) rozwiązań VMware platformy Azure. Cennik wersji zapoznawczej jest dostępny na żądanie, skontaktuj się z zespołem ds. kont lub Skorzystaj z linku na stronie cennika, aby skontaktować się z firmą

**Kto obsługuje rozwiązanie Azure VMware?**

Pomoc techniczna dla rozwiązań VMware platformy Azure jest dostarczana przez firmę Microsoft. Pamiętaj, że zgodnie z naszymi wskazówkami dotyczącymi wersji zapoznawczej firma Microsoft zapewnia pomoc techniczną w godzinach od 9 do 5 PM PST w piątek (poniedziałek). Możesz zgłosić bilet pomocy technicznej z [tego linku](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

**Jakie konta muszę utworzyć chmurę prywatną rozwiązania Azure VMware?**

Musisz mieć konto platformy Azure w ramach subskrypcji platformy Azure.

<a name="how-to-request-a-quota-increase-for-avs"></a>**Jak mogę zażądać zwiększenia limitu przydziału hosta dla rozwiązania Azure VMware?**

Można zażądać zwiększenia limitu przydziału przez [przesłanie żądania pomocy technicznej](..\azure-portal\supportability\how-to-create-azure-support-request.md). Zespół zarządzający przydziałami oblicza żądanie i zatwierdza go w ciągu trzech dni roboczych.  

> [!IMPORTANT]
> Przed zażądaniem zwiększenia limitu przydziału upewnij się, że zarejestrowano dostawcę zasobów **Microsoft. Automatyczna synchronizacja** w Azure Portal.  
> ```azurecli-interactive
> az provider register -n Microsoft.AVS --subscription <your subscription ID>
> ```
> Aby uzyskać dodatkowe sposoby rejestrowania dostawcy zasobów, zobacz [dostawcy zasobów platformy Azure i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

1. W Azure Portal w obszarze **Pomoc i obsługa techniczna**Utwórz **nowe żądanie pomocy technicznej** i podaj następujące informacje dotyczące biletu:
   - **Typ problemu:** Naukow
   - **Subskrypcja:** Identyfikator subskrypcji
   - **Usługa:**  Rozwiązanie VMware platformy Azure 
   - **Podsumowanie:** Zwiększenie limitu przydziału
   - **Typ problemu:** Problemy z zarządzaniem pojemnością
   - **Podtyp problemu:** Żądanie klienta dotyczące dodatkowego przydziału/pojemności hosta

1. W opisie biletu pomocy technicznej na karcie Szczegóły podaj następujące informacje:
   - Liczba dodatkowych węzłów   
   - Jednostka SKU węzła
   - Region (Region)

   > [!NOTE] 
   > Domyślnie zostanie udzielony co najmniej cztery węzły.

1. Kliknij przycisk **Przegląd + Utwórz** , aby przesłać żądanie.

<!-- LINKS - external -->
[kb2106952]: https://kb.vmware.com/s/article/2106952

<!-- LINKS - internal -->
[Access and Identity Concepts]: concepts-identity.md
