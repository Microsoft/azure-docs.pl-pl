---
title: Traffic Manager platformy Azure z Azure Site Recovery | Microsoft Docs
description: Opisuje sposób korzystania z usługi Azure Traffic Manager z Azure Site Recovery na potrzeby odzyskiwania po awarii i migracji
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: bb112e0b2d1c64e65ecaf6749a25707d8632c0cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86134961"
---
# <a name="azure-traffic-manager-with-azure-site-recovery"></a>Usługa Azure Traffic Manager z usługą Azure Site Recovery

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Traffic Manager używa systemu nazw domen (DNS) do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego na podstawie metody routingu ruchu i kondycji punktów końcowych. Usługa Traffic Manager udostępnia szereg [metod routingu ruchu](../traffic-manager/traffic-manager-routing-methods.md) oraz [opcji monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md), które zaspokoją potrzeby różnych aplikacji i modeli automatycznej pracy w trybie failover. Klienci bezpośrednio łączą się z wybranym punktem końcowym. Traffic Manager nie jest serwerem proxy ani bramą i nie widzi ruchu przechodzącego między klientem a usługą.

W tym artykule opisano sposób łączenia inteligentnego routingu usługi Azure traffic monitor z zaawansowanymi możliwościami odzyskiwania po awarii i migracji Azure Site Recovery.

## <a name="on-premises-to-azure-failover"></a>Tryb failover na platformie Azure

W pierwszym scenariuszu należy wziąć pod uwagę **firmę A** , która ma całą infrastrukturę aplikacji działającą w środowisku lokalnym. Aby zapewnić ciągłość działania i zgodność z przepisami, **firma a** decyduje o użyciu Azure Site Recovery do ochrony swoich aplikacji.

**Firma a** ma uruchomione aplikacje z publicznymi punktami końcowymi i chce bezproblemowo przekierować ruch do platformy Azure w ramach zdarzenia awarii. [Priorytetowa](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Metoda routingu ruchu w usłudze Azure Traffic Manager umożliwia firmie A łatwe wdrażanie tego wzorca trybu failover.

Konfiguracja jest następująca:
- **Firma A** tworzy [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Korzystając z metody routingu **priorytetowego** , **firma A** tworzy dwa punkty końcowe — **podstawowe** dla lokalnego i **trybu failover** dla platformy Azure. **Podstawowym** przypisano priorytet 1, a **tryb failover** ma przypisany priorytet 2.
- Ponieważ **podstawowy** punkt końcowy jest hostowany poza platformą Azure, punkt końcowy jest tworzony jako [zewnętrzny](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punkt końcowy.
- W przypadku Azure Site Recovery witryna Azure nie ma żadnych maszyn wirtualnych ani aplikacji działających przed przełączeniem w tryb failover. W związku z tym punkt końcowy **trybu failover** jest również tworzony jako **zewnętrzny** punkt końcowy.
- Domyślnie ruch użytkowników jest kierowany do aplikacji lokalnej, ponieważ z tym punktem końcowym jest skojarzony najwyższy priorytet. Żaden ruch nie jest kierowany do platformy Azure, jeśli **podstawowy** punkt końcowy jest w dobrej kondycji.

![Lokalne na platformę Azure przed przejściem w tryb failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-before.png)

W przypadku awarii firma A może wyzwolić przejście w [tryb failover](site-recovery-failover.md) na platformę Azure i odzyskać swoje aplikacje na platformie Azure. Gdy usługa Azure Traffic Manager wykryje, że **podstawowy** punkt końcowy nie jest już w dobrej kondycji, automatycznie korzysta z punktu końcowego **trybu failover** w odpowiedzi DNS i użytkownicy łączą się z aplikacją odzyskaną na platformie Azure.

![Lokalne na platformę Azure po przejściu w tryb failover](./media/concepts-traffic-manager-with-site-recovery/on-premises-failover-after.png)

W zależności od wymagań firmy **firma A** może wybrać wyższą lub niższą [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) , aby przełączać się między środowiskiem lokalnym i platformą Azure w ramach zdarzenia awarii i zapewnić minimalną przestoje dla użytkowników.

Po włączeniu awarii **firma A** może przeprowadzić powrót po awarii z platformy Azure do środowiska lokalnego ([VMware](vmware-azure-failback.md) lub [Hyper-V](hyper-v-azure-failback.md)) przy użyciu Azure Site Recovery. Teraz, gdy Traffic Manager wykryje, że **podstawowy** punkt końcowy jest w dobrej kondycji, automatycznie korzysta z **podstawowego** punktu końcowego w odpowiedzi DNS.

## <a name="on-premises-to-azure-migration"></a>Migracja lokalna do platformy Azure

Oprócz odzyskiwania po awarii, Azure Site Recovery umożliwia także [migrację na platformę Azure](migrate-overview.md). Dzięki zaawansowanym możliwościom testowania pracy w trybie failover Azure Site Recovery klienci mogą ocenić wydajność aplikacji na platformie Azure bez wpływu na środowisko lokalne. Gdy klienci są gotowi do migracji, mogą oni zdecydować się na migrację całych obciążeń razem lub w celu stopniowego migrowania i skalowania.

Za pomocą [ważonej](../traffic-manager/traffic-manager-configure-weighted-routing-method.md) metody routingu Traffic Manager platformy Azure można przekierować część ruchu przychodzącego na platformę Azure, kierując większość do środowiska lokalnego. Takie podejście może pomóc w ocenie wydajności skalowania, ponieważ pozwala to na dalsze zwiększenie wagi przypisanej do platformy Azure w miarę migrowania więcej i większej liczby obciążeń do platformy Azure.

Na przykład **firma B** zdecyduje się na migrację w fazach, przenosząc część środowiska aplikacji przy zachowaniu instalacji lokalnej. W trakcie początkowych etapów, gdy większość środowiska jest lokalna, do środowiska lokalnego jest przypisywany większy nacisk. Menedżer ruchu zwraca punkt końcowy na podstawie wag przypisanych do dostępnych punktów końcowych.

![Migracja lokalna na platformie Azure](./media/concepts-traffic-manager-with-site-recovery/on-premises-migration.png)

Podczas migracji oba punkty końcowe są aktywne, a większość ruchu jest skierowana do środowiska lokalnego. Po zakończeniu migracji można przypisać większą wagę do punktu końcowego na platformie Azure, a wreszcie można dezaktywować lokalny punkt końcowy.

## <a name="azure-to-azure-failover"></a>Przejście do trybu failover z platformy Azure do platformy Azure

Na potrzeby tego przykładu należy wziąć pod uwagę **firmę C** , która ma całą infrastrukturę aplikacji działającą na platformie Azure. Ze względu na ciągłość działania i zgodność **firma C** decyduje się użyć Azure Site Recovery do ochrony swoich aplikacji.

**Firma C** uruchamia aplikacje z publicznymi punktami końcowymi i chce bezproblemowo przekierować ruch do innego regionu platformy Azure w przypadku awarii. [Priorytetowa](../traffic-manager/traffic-manager-configure-priority-routing-method.md) Metoda routingu ruchu umożliwia **firmie C** łatwe wdrażanie tego wzorca trybu failover.

Konfiguracja jest następująca:
- **Firma C** tworzy [profil Traffic Manager](../traffic-manager/quickstart-create-traffic-manager-profile.md).
- Korzystając z metody routingu **priorytetowego** , **firma C** tworzy dwa punkty końcowe — **podstawowe** dla regionu źródłowego (Azure Azja Wschodnia) i **trybu failover** dla regionu odzyskiwania (Azja Południowo-Wschodnia dla platformy Azure). **Podstawowym** przypisano priorytet 1, a **tryb failover** ma przypisany priorytet 2.
- Ponieważ **podstawowy** punkt końcowy jest hostowany na platformie Azure, punkt końcowy może być punktem końcowym [platformy Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) .
- W przypadku Azure Site Recovery witryna odzyskiwania systemu Azure nie ma żadnych maszyn wirtualnych ani aplikacji działających przed przełączeniem w tryb failover. W związku z tym punkt końcowy **trybu failover** można utworzyć jako [zewnętrzny](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) punkt końcowy.
- Domyślnie ruch użytkowników jest kierowany do aplikacji region źródłowy (Azja Wschodnia), ponieważ z tym punktem końcowym jest skojarzony najwyższy priorytet. Żaden ruch nie jest kierowany do regionu odzyskiwania, jeśli **podstawowy** punkt końcowy jest w dobrej kondycji.

![Azure na platformę Azure przed przejściem w tryb failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-before.png)

W przypadku awarii **firma C** może wyzwolić [tryb failover](azure-to-azure-tutorial-failover-failback.md) i odzyskać swoje aplikacje w regionie odzyskiwania systemu Azure. Gdy usługa Azure Traffic Manager wykryje, że podstawowy punkt końcowy nie jest już w dobrej kondycji, automatycznie używa punktu końcowego **trybu failover** w odpowiedzi DNS i użytkownicy łączą się z aplikacją odzyskaną w regionie odzyskiwania systemu Azure (Azja Południowo-Wschodnia).

![Azure na platformę Azure po przejściu w tryb failover](./media/concepts-traffic-manager-with-site-recovery/azure-failover-after.png)

W zależności od wymagań firmy, **firma C** może wybrać wyższą lub niższą [częstotliwość sondowania](../traffic-manager/traffic-manager-monitoring.md) , aby przełączać się między regionami źródłowymi i odzyskiwania oraz zapewnić minimalną przestoje dla użytkowników.

Po wystąpieniu awarii **firma C** może przeprowadzić powrót po awarii z regionu odzyskiwania platformy Azure do źródłowego regionu platformy Azure przy użyciu Azure Site Recovery. Teraz, gdy Traffic Manager wykryje, że **podstawowy** punkt końcowy jest w dobrej kondycji, automatycznie korzysta z **podstawowego** punktu końcowego w odpowiedzi DNS.

## <a name="protecting-multi-region-enterprise-applications"></a>Ochrona wieloregionowych aplikacji korporacyjnych

Przedsiębiorstwa globalne często ulepszają środowisko klienta, dostosowując swoje aplikacje do potrzeb regionalnych. Zmniejszenie lokalizacji i opóźnienia może prowadzić do podziału infrastruktury aplikacji między regionami. Przedsiębiorstwa są również powiązane z przepisami regionalnymi dotyczącymi danych w niektórych obszarach i umożliwiają izolowanie części infrastruktury aplikacji w granicach regionalnych.  

Rozważmy przykład, w którym **Firma D** podzieliła punkty końcowe swojej aplikacji na osobne przeprowadzenie Niemiec i reszty świata. **Firma D** korzysta z metody routingu [geograficznego](../traffic-manager/traffic-manager-configure-geographic-routing-method.md) platformy Azure Traffic Manager, aby ją skonfigurować. Każdy ruch pochodzący z Niemiec jest kierowany do **punktu końcowego 1** , a każdy ruch pochodzący spoza Niemiec jest kierowany do **punktu końcowego 2**.

Problem z tym instalatorem polega na tym, że jeśli **punkt końcowy 1** przestanie działać z dowolnego powodu, nie ma przekierowania ruchu do **punktu końcowego 2**. Ruch pochodzący z Niemiec nadal jest kierowany do **punktu końcowego 1** niezależnie od kondycji punktu końcowego, pozostawiając niemieckim użytkownikom bez dostępu do aplikacji **firmy D**. Podobnie, jeśli **punkt końcowy 2** przejdzie w tryb offline, nie jest przekierowywanie ruchu do **punktu końcowego 1**.

![Aplikacja wieloregionowa przed](./media/concepts-traffic-manager-with-site-recovery/geographic-application-before.png)

Aby uniknąć uruchomienia tego problemu i zapewnienia odporności aplikacji, **Firma D** używa [zagnieżdżonych profilów Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md) z Azure Site Recovery. W konfiguracji profilu zagnieżdżonego ruch nie jest kierowany do poszczególnych punktów końcowych, ale zamiast do innych profilów Traffic Manager. Oto jak działa ta konfiguracja:
- Zamiast korzystać z routingu geograficznego z poszczególnymi punktami końcowymi, **Firma D** używa routingu geograficznego z profilami Traffic Manager.
- Każdy podrzędny profil Traffic Manager korzysta z routingu **priorytetowego** przy użyciu podstawowego i punktu końcowego odzyskiwania, a tym samym zagnieżdżania **priorytetowego** routingu w ramach routingu **geograficznego** .
- Aby włączyć odporność aplikacji, Każda dystrybucja obciążeń wykorzystuje Azure Site Recovery przełączenia w tryb failover do regionu odzyskiwania opartego na wypadek awarii.
- Gdy Traffic Manager nadrzędny odbiera zapytanie DNS, zostaje skierowane do odpowiedniego Traffic Manager podrzędnego, który odpowiada na zapytanie z dostępny punkt końcowy.

![Aplikacja wieloregionowa po](./media/concepts-traffic-manager-with-site-recovery/geographic-application-after.png)

Jeśli na przykład punkt końcowy w Niemczech centralnych zakończy się niepowodzeniem, aplikacja może zostać szybko odzyskana do Niemiec północ. Nowy punkt końcowy obsługuje ruch pochodzący z Niemiec z minimalnym czasem przestoju dla użytkowników. Podobna awaria punktu końcowego w regionie Europa Zachodnia może być obsługiwana przez przywrócenie obciążenia aplikacji do Europy Północnej, a usługa Azure Traffic Manager obsługuje przekierowania DNS do dostępnego punktu końcowego.

Powyższa konfiguracja może być rozszerzona w celu uwzględnienia, ile jest wymaganych kombinacji regionów i punktów końcowych. Traffic Manager umożliwia używanie do 10 poziomów zagnieżdżonych profilów i nie zezwala na pętle w ramach konfiguracji zagnieżdżonej.

## <a name="recovery-time-objective-rto-considerations"></a>Uwagi dotyczące celu czasu odzyskiwania (RTO)

W większości organizacji Dodawanie lub modyfikowanie rekordów DNS jest obsługiwane przez osobny zespół lub kogoś spoza organizacji. Dzięki temu zadanie zmiany rekordów DNS jest bardzo trudne. Czas potrzebny do zaktualizowania rekordów DNS przez inne zespoły lub organizacje zarządzające infrastrukturą DNS różnią się od organizacji do organizacji i ma wpływ na RTO aplikacji.

Korzystając z Traffic Manager, można frontload pracy wymaganej do aktualizacji DNS. W chwili rzeczywistego przejścia w tryb failover nie jest wymagane wykonanie akcji ręcznej ani skryptu. Takie podejście umożliwia szybkie przełączanie (a tym samym obniżenie RTO), a także uniknięcie kosztownych czasochłonnych błędów zmiany nazw DNS w przypadku awarii. W przypadku Traffic Manager, nawet krok powrotu po awarii jest zautomatyzowany, co w przeciwnym razie trzeba będzie zarządzać osobno.

Ustawienie poprawnego [interwału sondowania](../traffic-manager/traffic-manager-monitoring.md) przy użyciu podstawowych lub szybkich okresowych kontroli kondycji może znacząco RTO podczas pracy w trybie failover i skrócić czas przestoju dla użytkowników.

Dodatkowo można zoptymalizować wartość czasu wygaśnięcia (TTL) usługi DNS dla profilu Traffic Manager. Czas wygaśnięcia jest wartością, dla której wpis DNS mógłby zostać zbuforowany przez klienta. W przypadku rekordu w systemie DNS nie można dwukrotnie wykonać zapytania w ramach zakresu czasu wygaśnięcia. Z każdym rekordem DNS jest skojarzony czas wygaśnięcia. Zmniejszenie tej wartości spowoduje zwiększenie liczby zapytań DNS do Traffic Manager ale może obniżyć RTO przez szybsze odnajdywanie.

Czas wygaśnięcia obsługiwany przez klienta również nie zwiększa się, jeśli liczba rozpoznawania nazw DNS między klientem a autorytatywnym serwerem DNS rośnie. Rozpoznawanie nazw DNS "liczy w dół" czas wygaśnięcia i tylko wartość czasu wygaśnięcia, która odzwierciedla czas, który upłynął od momentu zapisania rekordu. Dzięki temu rekord DNS jest odświeżany na kliencie po upływie czasu wygaśnięcia, niezależnie od liczby resolverów DNS w łańcuchu.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat Traffic Manager [metod routingu](../traffic-manager/traffic-manager-routing-methods.md).
- Dowiedz się więcej na temat [zagnieżdżonych profilów Traffic Manager](../traffic-manager/traffic-manager-nested-profiles.md).
- Dowiedz się więcej na temat [monitorowania punktów końcowych](../traffic-manager/traffic-manager-monitoring.md).
- Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md) w celu zautomatyzowania pracy awaryjnej aplikacji.
