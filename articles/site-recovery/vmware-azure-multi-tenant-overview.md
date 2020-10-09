---
title: Odzyskiwanie awaryjne wielu dzierżawców maszyn wirtualnych VMware z Azure Site Recovery
description: Zawiera omówienie Azure Site Recovery obsługi odzyskiwania po awarii programu VMWare na platformie Azure w programie z wieloma dzierżawcami (CSP).
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 840049265d3b6e4d2fddd794646bfd5691aab9a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74083987"
---
# <a name="overview-of-multi-tenant-support-for-vmware-disaster-recovery-to-azure-with-csp"></a>Omówienie obsługi wielu dzierżawców na potrzeby odzyskiwania po awarii oprogramowania VMware na platformie Azure przy użyciu dostawcy usług kryptograficznych

[Azure Site Recovery](site-recovery-overview.md) obsługuje wiele dzierżawców dla subskrypcji dzierżawców. Obsługuje ona również obsługę wielu dzierżawców dla subskrypcji dzierżawców, które są tworzone i zarządzane za pomocą programu Microsoft Cloudgo dostawcy rozwiązań (CSP).

Ten artykuł zawiera Omówienie wdrażania wielu dzierżawców VMware i zarządzania nimi w usłudze Azure.

## <a name="multi-tenant-environments"></a>Środowiska z wieloma dzierżawcami

Istnieją trzy główne modele z wieloma dzierżawcami:

* **Udostępniony dostawca usług hostingowych (HSP)**: partner jest właścicielem infrastruktury fizycznej i używa udostępnionych zasobów (vCenter, centrów danych, magazynu fizycznego itd.) do hostowania wielu maszyn wirtualnych dzierżawcy w tej samej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną lub w ramach samoobsługowego rozwiązania.

* **Dedykowany dostawca usług hostingowych**: partner jest właścicielem infrastruktury fizycznej, ale używa dedykowanych zasobów (wielu vCenter, fizycznych magazynów danych itd.) w celu hostowania maszyn wirtualnych każdej dzierżawy w oddzielnej infrastrukturze. Partner może zapewnić zarządzanie odzyskiwaniem po awarii jako usługę zarządzaną, a Dzierżawca może być jego własnością jako samoobsługowe rozwiązanie.

* **Dostawca usług zarządzanych (msp)**: klient jest właścicielem infrastruktury fizycznej, która hostuje maszyny wirtualne, a partner zapewnia obsługę i zarządzanie odzyskiwaniem po awarii.

## <a name="shared-hosting-services-provider-hsp"></a>Udostępniony dostawca usług hostingowych (HSP)

Pozostałe dwa scenariusze są podzbiorami scenariusza hostingu udostępnionego i korzystają z tych samych zasad. Różnice są opisane na końcu wskazówek dotyczących hostingu udostępnionego.

Podstawowe wymaganie w scenariuszu z wieloma dzierżawcami polega na tym, że dzierżawy muszą być odizolowane. Jedna dzierżawa nie powinna mieć możliwości obserwowania zawartości obsługiwanej przez inną dzierżawę. W środowisku zarządzanym przez partnera to wymaganie nie jest tak ważne, jak w środowisku samoobsługowym, co może być krytyczne. W tym artykule przyjęto założenie, że izolacja dzierżawy jest wymagana.

Architektura jest pokazana na poniższym diagramie.

![Udostępnione HSP z jednym programem vCenter](./media/vmware-azure-multi-tenant-overview/shared-hosting-scenario.png)  

**Udostępnione hosting z jednym serwerem vCenter Server**

Na diagramie każdy klient ma oddzielny serwer zarządzania. Ta konfiguracja ogranicza dostęp dzierżawców do maszyn wirtualnych specyficznych dla dzierżawców i włącza izolację dzierżawy. Replikacja maszyn wirtualnych VMware używa serwera konfiguracji do odnajdywania maszyn wirtualnych i instalowania agentów. Te same zasady mają zastosowanie w środowiskach z wieloma dzierżawcami, a dodanie opcji ograniczania odnajdywania maszyn wirtualnych przy użyciu kontroli dostępu vCenter.

Wymaganie izolacji danych oznacza, że wszystkie poufne informacje o infrastrukturze (takie jak poświadczenia dostępu) pozostają nieujawnione dla dzierżawców. Z tego powodu zaleca się, aby wszystkie składniki serwera zarządzania pozostawały w wyłącznej kontroli partnera. Składniki serwera zarządzania są następujące:

* Serwer konfiguracji
* Serwer przetwarzania
* Główny serwer docelowy

Oddzielny serwer przetwarzania skalowany w poziomie jest również objęty kontrolą partnera.

## <a name="configuration-server-accounts"></a>Konta serwera konfiguracji

Każdy serwer konfiguracji w scenariuszu z wieloma dzierżawcami używa dwóch kont:

- **konto dostępu vCenter**: to konto służy do odnajdywania maszyn wirtualnych dzierżawcy. Ma przypisane uprawnienia dostępu vCenter. Aby zapobiec przeciekom dostępu, zalecamy, aby partnerzy wprowadzili te poświadczenia w narzędziu konfiguracji.

- **Konto dostępu do maszyny wirtualnej**: to konto służy do instalowania agenta usługi mobilności na maszynach wirtualnych dzierżawcy przy użyciu funkcji automatycznego wypychania. Zwykle jest to konto domeny, które dzierżawcy może zapewnić partnerowi lub konto, które może bezpośrednio zarządzać partnerem. Jeśli dzierżawca nie chce bezpośrednio udostępniać szczegółowych informacji partnerskim, może wprowadzić poświadczenia w ograniczonym czasie dostępu do serwera konfiguracji. W przypadku pomocy partnera można ręcznie zainstalować agenta usługi mobilności.

## <a name="vcenter-account-requirements"></a>wymagania dotyczące konta vCenter

Skonfiguruj serwer konfiguracji przy użyciu konta, do którego jest przypisana specjalna rola.

- Przypisanie roli musi być stosowane do konta dostępu vCenter dla każdego obiektu vCenter i nie są propagowane do obiektów podrzędnych. Ta konfiguracja zapewnia izolację dzierżawy, ponieważ Propagacja dostępu może spowodować przypadkowe uzyskanie dostępu do innych obiektów.

    ![Opcja Propaguj do obiektów podrzędnych](./media/vmware-azure-multi-tenant-overview/assign-permissions-without-propagation.png)

- Alternatywnym podejściem jest przypisanie konta użytkownika i roli w obiekcie centrum danych i propagowanie ich do obiektów podrzędnych. Następnie nadaj kontu rolę **Brak dostępu** dla każdego obiektu (takiego jak maszyny wirtualne należące do innych dzierżawców), które powinny być niedostępne dla określonej dzierżawy. Ta konfiguracja jest nieposkomplikowana. Udostępnia on przypadkowe kontrole dostępu, ponieważ każdy nowy obiekt podrzędny ma również automatycznie udzielony dostęp Dziedziczony z elementu nadrzędnego. W związku z tym zalecamy korzystanie z pierwszej metody.

### <a name="create-a-vcenter-account"></a>Utwórz konto vCenter

1. Utwórz nową rolę przez sklonowanie wstępnie zdefiniowanej roli *tylko do odczytu* , a następnie nadaj jej wygodną nazwę (taką jak Azure_Site_Recovery, jak pokazano w tym przykładzie).
2. Przypisz następujące uprawnienia do tej roli:

   * **Magazyn**danych: Przydziel miejsce, Przeglądaj magazyn danych, operacje na plikach niskiego poziomu, usuń plik, Aktualizuj pliki maszyny wirtualnej
   * **Sieć**: przypisywanie sieci
   * **Zasób**: Przypisz maszynę wirtualną do puli zasobów, Migruj ją z maszyny wirtualnej, Migruj ją na maszynie wirtualnej
   * **Zadania**: Tworzenie zadania, aktualizowanie zadania
   * **Maszyna wirtualna — konfiguracja**: wszystkie
   * > **interakcji z maszyną wirtualną** , połączenie z urządzeniem, Konfigurowanie nośnika CD, Konfigurowanie dyskietki, wyłączanie, Włączanie, Instalowanie narzędzi VMware
   * **Maszyna wirtualna — > tworzenia spisu** z istniejących, tworzenia nowych, rejestrowania, wyrejestrowywania
   * **Inicjowanie obsługi** maszyny wirtualnej > Zezwalanie na pobieranie maszyn wirtualnych, zezwalanie na przekazywanie plików maszyn wirtualnych
   * **Maszyna wirtualna — zarządzanie migawkami** > usuwanie migawek

       ![Okno dialogowe Edytowanie roli](./media/vmware-azure-multi-tenant-overview/edit-role-permissions.png)

3. Przypisywanie poziomów dostępu do konta vCenter (używanego na serwerze konfiguracji dzierżawy) dla różnych obiektów w następujący sposób:

>| Obiekt | Rola | Uwagi |
>| --- | --- | --- |
>| vCenter | Read-Only | Jest to konieczne tylko w celu umożliwienia dostępu vCenter do zarządzania różnymi obiektami. To uprawnienie można usunąć, jeśli konto nigdy nie zostanie przekazane dzierżawcy lub nie będzie używane do żadnych operacji zarządzania w programie vCenter. |
>| Centrum danych | Azure_Site_Recovery |  |
>| Host i klaster hostów | Azure_Site_Recovery | Umożliwia ponowne zagwarantowanie dostępu na poziomie obiektu, tak aby tylko dostępne hosty miały maszyny wirtualne dzierżawcy przed przełączeniem w tryb failover i po awarii. |
>| Magazyn danych i klaster magazynów danych | Azure_Site_Recovery | Analogicznie jak powyżej. |
>| Sieć | Azure_Site_Recovery |  |
>| Serwer zarządzania | Azure_Site_Recovery | Obejmuje dostęp do wszystkich składników (CS, PS i MT) poza maszyną CS. |
>| Maszyny wirtualne dzierżawcy | Azure_Site_Recovery | Zapewnia, że wszystkie nowe maszyny wirtualne dzierżawcy konkretnej dzierżawy również uzyskają dostęp lub nie będą wykrywalne za pośrednictwem Azure Portal. |

Dostęp do konta vCenter jest teraz zakończony. Ten krok spełnia minimalne wymagania dotyczące uprawnień do ukończenia operacji powrotu po awarii. Możesz również użyć tych uprawnień dostępu z istniejącymi zasadami. Po prostu zmodyfikuj istniejące uprawnienia w taki sposób, aby obejmowały uprawnienia roli z kroku 2.

### <a name="failover-only"></a>Tylko tryb failover
Aby ograniczyć liczbę operacji odzyskiwania po awarii do trybu failover tylko (to znaczy bez możliwości powrotu po awarii), należy wykonać poprzednią procedurę z następującymi wyjątkami:

- Zamiast przypisywać rolę *Azure_Site_Recovery* do konta dostępu vCenter, przypisz tylko rolę tylko do *odczytu* do tego konta. Ten zestaw uprawnień umożliwia replikację maszyny wirtualnej i tryb failover, a nie zezwala na powrót po awarii.
- Wszystkie inne w poprzednim procesie pozostają bez zmian. Aby zapewnić izolację dzierżawy i ograniczyć odnajdywanie maszyn wirtualnych, wszystkie uprawnienia są nadal przypisywane tylko na poziomie obiektu i nie są propagowane do obiektów podrzędnych.

### <a name="deploy-resources-to-the-tenant-subscription"></a>Wdrażanie zasobów w ramach subskrypcji dzierżawy

1. Na Azure Portal Utwórz grupę zasobów, a następnie wdróż magazyn Recovery Services na typowym procesie.
2. Pobierz klucz rejestracji magazynu.
3. Zarejestruj program CS dla dzierżawy przy użyciu klucza rejestracji magazynu.
4. Wprowadź poświadczenia dla dwóch kont dostępu, konta, aby uzyskać dostęp do serwera vCenter, oraz konto umożliwiające dostęp do maszyny wirtualnej.

    ![Konta serwera konfiguracji Menedżera](./media/vmware-azure-multi-tenant-overview/config-server-account-display.png)

### <a name="register-servers-in-the-vault"></a>Rejestrowanie serwerów w magazynie

1. W Azure Portal w utworzonym wcześniej magazynie zarejestruj serwer vCenter na serwerze konfiguracji przy użyciu utworzonego konta programu vCenter.
2. Zakończ proces "Przygotuj infrastrukturę" dla Site Recovery na zwykły proces.
3. Maszyny wirtualne są teraz gotowe do replikacji. Sprawdź, czy w obszarze **replikacja**są wyświetlane tylko maszyny wirtualne dzierżawy  >  **Select virtual machines**.

## <a name="dedicated-hosting-solution"></a>Dedykowane rozwiązanie hostingu

Jak pokazano na poniższym diagramie, różnica architektury w dedykowanym rozwiązaniu hostingu polega na tym, że infrastruktura każdej dzierżawy jest skonfigurowana tylko dla tej dzierżawy.

![Architektura — Shared-HSP](./media/vmware-azure-multi-tenant-overview/dedicated-hosting-scenario.png)  
**Dedykowany scenariusz hostingu z wieloma vCentermi**

## <a name="managed-service-solution"></a>Rozwiązanie usługi zarządzanej

Jak pokazano na poniższym diagramie, różnica architektury w rozwiązaniu usługi zarządzanej polega na tym, że infrastruktura każdej dzierżawy jest również fizycznie oddzielona od innej infrastruktury dzierżawców. Ten scenariusz zwykle istnieje, gdy dzierżawca jest właścicielem infrastruktury i chce, aby dostawca rozwiązań mógł zarządzać odzyskiwaniem po awarii.

![Architektura — Shared-HSP](./media/vmware-azure-multi-tenant-overview/managed-service-scenario.png)  
**Scenariusz usługi zarządzanej z wieloma vCentermi**

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej](site-recovery-role-based-linked-access-control.md) na temat kontroli dostępu opartej na rolach w Site Recovery.
- Dowiedz się, jak [skonfigurować odzyskiwanie po awarii maszyn wirtualnych VMware na platformie Azure](vmware-azure-tutorial.md).
- Dowiedz się więcej o [wielu dzierżawcach z programem CSP dla maszyn wirtualnych VMware](vmware-azure-multi-tenant-csp-disaster-recovery.md).
