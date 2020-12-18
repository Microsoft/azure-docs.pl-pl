---
title: Przegląd obsługiwanej przez platformę migracji zasobów IaaS z wersji klasycznej do Azure Resource Manager
description: Zapoznaj się z zapoznaj się z migracją zasobów z klasycznej do Azure Resource Manager przez obsługiwaną platformę.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: e7d013775861f290d532e0d7c132896ebeff8ae8
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680208"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager-in-linux"></a>Migracja zasobów IaaS z wersji klasycznej do Azure Resource Manager w systemie Linux obsługiwana przez platformę

> [!IMPORTANT]
> Dzisiaj około 90% maszyn wirtualnych IaaS korzysta z [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28 lutego 2020, klasyczne maszyny wirtualne są przestarzałe i zostaną w pełni wycofane z 1 marca 2023. [Dowiedz się więcej]( https://aka.ms/classicvmretirement) o tym zaniechaniu i [sposobach jego działania](classic-vm-deprecation.md#how-does-this-affect-me).



Ten artykuł zawiera omówienie narzędzia do migracji obsługiwanego przez platformę, sposób migrowania zasobów z modeli wdrażania usługi Azure Service Manager (ASM) z wersji klasycznej do Menedżer zasobów (ARM) i szczegółowe informacje dotyczące sposobu łączenia zasobów z dwóch modeli wdrożenia, które współdziałają z subskrypcją za pomocą bram sieci VPN lokacja-lokacja. Więcej informacji o [Azure Resource Manager funkcjach i korzyściach](../azure-resource-manager/management/overview.md)można znaleźć w artykule. 

## <a name="goal-for-migration"></a>Cel migracji
Menedżer zasobów umożliwia wdrażanie złożonych aplikacji za pośrednictwem szablonów, Konfigurowanie maszyn wirtualnych przy użyciu rozszerzeń maszyny wirtualnej oraz zarządzanie dostępem i tagowanie. Azure Resource Manager obejmuje skalowalne, równoległe wdrożenie maszyn wirtualnych w zestawach dostępności. Nowy model wdrażania zapewnia także niezależne Zarządzanie cyklem życia zasobów obliczeniowych, sieci i magazynu. Na koniec należy włączyć zabezpieczenia domyślnie przy użyciu wymuszania maszyn wirtualnych w sieci wirtualnej.

Prawie wszystkie funkcje z klasycznego modelu wdrażania są obsługiwane w przypadku obliczeń, sieci i magazynu w obszarze Azure Resource Manager. Aby skorzystać z nowych możliwości w Azure Resource Manager, można migrować istniejące wdrożenia z klasycznego modelu wdrażania.

## <a name="supported-resources--configurations-for-migration"></a>Obsługiwane zasoby & konfiguracje do migracji

### <a name="supported-resources-for-migration"></a>Obsługiwane zasoby na potrzeby migracji
* Virtual Machines
* Zestawy dostępności
* Konta magazynu
* Sieci wirtualne
* Bramy sieci VPN
* [Bramy usługi Express Route](https://docs.microsoft.com/azure/expressroute/expressroute-howto-move-arm) _(w tej samej subskrypcji tylko w przypadku Virtual Network)_
* Grupy zabezpieczeń sieci
* Tabele tras
* Zastrzeżone adresy IP

## <a name="supported-configurations-for-migration"></a>Obsługiwane konfiguracje migracji
Te klasyczne zasoby IaaS są obsługiwane podczas migracji

| Usługa | Konfigurowanie |
| --- | --- |
| Azure AD Domain Services | [Sieci wirtualne, które zawierają usługi domenowe Azure AD](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Obsługiwane zakresy migracji
Istnieją cztery różne sposoby przeprowadzenia migracji zasobów obliczeniowych, sieciowych i magazynowych:

* [Migracja maszyn wirtualnych (nie w sieci wirtualnej)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migracja maszyn wirtualnych (w sieci wirtualnej)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migracja kont magazynu](#migration-of-storage-accounts)
* [Migracja niedołączonych zasobów](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migracja maszyn wirtualnych (nie w sieci wirtualnej)
W modelu wdrażania Menedżer zasobów zabezpieczenia są domyślnie wymuszane dla aplikacji. Wszystkie maszyny wirtualne muszą znajdować się w sieci wirtualnej w modelu Menedżer zasobów. Platforma Azure ponownie uruchamia `Stop` `Deallocate` maszyny wirtualne (, i) w `Start` ramach migracji. Dostępne są dwie opcje dla sieci wirtualnych, do których zostanie przeprowadzona migracja Virtual Machines:

* Możesz poprosić platformę o utworzenie nowej sieci wirtualnej i przeprowadzenie migracji maszyny wirtualnej do nowej sieci wirtualnej.
* Maszynę wirtualną można migrować do istniejącej sieci wirtualnej w Menedżer zasobów.

> [!NOTE]
> W tym zakresie migracji operacje płaszczyzny zarządzania i operacje płaszczyzny danych mogą nie być dozwolone przez okres czasu podczas migracji.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migracja maszyn wirtualnych (w sieci wirtualnej)
W przypadku większości konfiguracji maszyn wirtualnych tylko metadane są migrowane między klasycznym i Menedżer zasobów modelami wdrażania. Bazowe maszyny wirtualne są uruchomione na tym samym sprzęcie, w tej samej sieci i w tym samym magazynie. Operacje płaszczyzny zarządzania mogą nie być dozwolone przez pewien czas podczas migracji. Jednak płaszczyzna danych nadal działa. Oznacza to, że aplikacje działające na maszynach wirtualnych (klasycznych) nie generują przestojów podczas migracji.

Następujące konfiguracje nie są obecnie obsługiwane. Jeśli w przyszłości zostanie dodana obsługa, niektóre maszyny wirtualne w tej konfiguracji mogą spowodować przestoje (przechodzenie przez zatrzymanie, cofnięcie alokacji i ponowne uruchomienie operacji maszyny wirtualnej).

* W jednej usłudze w chmurze jest dostępny więcej niż jeden zestaw dostępności.
* Istnieje co najmniej jeden zestaw dostępności i maszyny wirtualne, które nie znajdują się w zestawie dostępności w jednej usłudze w chmurze.

> [!NOTE]
> W tym zakresie migracji płaszczyzna zarządzania może być niedozwolona przez pewien czas podczas migracji. W przypadku niektórych konfiguracji, zgodnie z wcześniejszym opisem, występują przestoje płaszczyzny danych.
>

### <a name="migration-of-storage-accounts"></a>Migracja kont magazynu
Aby umożliwić bezproblemową migrację, można wdrożyć Menedżer zasobów maszyny wirtualne na klasycznym koncie magazynu. Dzięki tej możliwości zasoby obliczeniowe i sieciowe mogą być migrowane niezależnie od kont magazynu. Po przeprowadzeniu migracji do Virtual Machines i Virtual Network należy przeprowadzić migrację przez konta magazynu, aby ukończyć proces migracji.

Jeśli konto magazynu nie ma żadnych skojarzonych dysków lub Virtual Machines danych i zawiera tylko obiekty blob, pliki, tabele i kolejki, migracja do Azure Resource Manager może być przeprowadzana jako migracja autonomiczna bez zależności.

> [!NOTE]
> Model wdrażania Menedżer zasobów nie ma koncepcji klasycznych obrazów i dysków. Po przeprowadzeniu migracji konta magazynu obrazy klasyczne i dyski nie są widoczne na stosie Menedżer zasobów, ale zapasowe dyski VHD pozostają na koncie magazynu.

Poniższe zrzuty ekranu pokazują, jak uaktualnić klasyczne konto magazynu do konta magazynu Azure Resource Manager przy użyciu Azure Portal:
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Przejdź do konta magazynu.
3. W sekcji **Ustawienia** kliknij pozycję **Migruj do ARM**.
4. Kliknij przycisk **Sprawdź poprawność** , aby określić wykonalność migracji.
5. Jeśli walidacja przebiega pomyślnie, kliknij przycisk **Przygotuj** , aby utworzyć zmigrowane konto magazynu.
6. Wpisz **wartość tak** , aby potwierdzić migrację, a następnie kliknij przycisk **Zatwierdź** , aby zakończyć migrację.

    ![Sprawdź poprawność konta magazynu](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Przygotuj konto magazynu](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Zakończ migrację konta magazynu](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migracja niedołączonych zasobów
Konta magazynu bez skojarzonych dysków ani danych Virtual Machines mogą być migrowane niezależnie.

Sieciowe grupy zabezpieczeń, tabele tras & zastrzeżone adresy IP, które nie są dołączone do żadnych Virtual Machines i sieci wirtualnych, również można migrować niezależnie.

<br>

## <a name="unsupported-features-and-configurations"></a>Nieobsługiwane funkcje i konfiguracje
Niektóre funkcje i konfiguracje nie są obecnie obsługiwane; w poniższych sekcjach opisano nasze zalecenia.

### <a name="unsupported-features"></a>Nieobsługiwane funkcje
Następujące funkcje nie są obecnie obsługiwane. Opcjonalnie można usunąć te ustawienia, przeprowadzić migrację maszyn wirtualnych, a następnie ponownie włączyć ustawienia w Menedżer zasobów model wdrażania.

| Dostawca zasobów | Cecha | Zalecenie |
| --- | --- | --- |
| Wystąpienia obliczeniowe | Nieskojarzone dyski maszyny wirtualnej. | Po przeprowadzeniu migracji konta magazynu zostaną zmigrowane obiekty blob VHD znajdujące się za tymi dyskami |
| Wystąpienia obliczeniowe | Obrazy maszyn wirtualnych. | Po przeprowadzeniu migracji konta magazynu zostaną zmigrowane obiekty blob VHD znajdujące się za tymi dyskami |
| Sieć | Listy ACL punktów końcowych. | Usuń listy ACL punktów końcowych i ponów próbę migracji. |
| Sieć | Application Gateway | Przed rozpoczęciem migracji Usuń Application Gateway, a następnie ponownie utwórz Application Gateway po zakończeniu migracji. |
| Sieć | Sieci wirtualne korzystające z komunikacji równorzędnej VNet. | Przeprowadź migrację Virtual Network do Menedżer zasobów, elementu równorzędnego. Dowiedz się więcej o [komunikacji równorzędnej sieci](../virtual-network/virtual-network-peering-overview.md)wirtualnych. |

### <a name="unsupported-configurations"></a>Nieobsługiwane konfiguracje
Następujące konfiguracje nie są obecnie obsługiwane.

| Usługa | Konfigurowanie | Zalecenie |
| --- | --- | --- |
| Resource Manager |Role-Based Access Control (RBAC) dla zasobów klasycznych |Ponieważ identyfikator URI zasobów jest modyfikowany po migracji, zaleca się zaplanowanie aktualizacji zasad RBAC, które muszą wystąpić po migracji. |
| Wystąpienia obliczeniowe |Wiele podsieci skojarzonych z maszyną wirtualną |Zaktualizuj konfigurację podsieci, aby odwoływać się tylko do jednej podsieci. Może to wymagać usunięcia pomocniczej karty sieciowej (odwołującej się do innej podsieci) z maszyny wirtualnej i ponownego dołączenia jej po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Maszyny wirtualne należące do sieci wirtualnej, ale nie mają przypisanej bezpośredniej podsieci |Opcjonalnie można usunąć maszynę wirtualną. |
| Wystąpienia obliczeniowe |Maszyny wirtualne z alertami, zasady automatycznego skalowania |Migracja przechodzi przez te ustawienia. Zdecydowanie zaleca się oszacowanie środowiska przed przeprowadzeniem migracji. Alternatywnie można ponownie skonfigurować ustawienia alertów po zakończeniu migracji. |
| Wystąpienia obliczeniowe |Maszyny wirtualne XML Extensions (BGInfo 1. *, debuger programu Visual Studio, Web Deploy i zdalne debugowanie) |To nie jest obsługiwane. Zaleca się usunięcie tych rozszerzeń z maszyny wirtualnej, aby kontynuować migrację, lub zostaną one usunięte automatycznie podczas procesu migracji. |
| Wystąpienia obliczeniowe |Diagnostyka rozruchu z magazynem w warstwie Premium |Przed kontynuowaniem migracji Wyłącz funkcję diagnostyki rozruchu dla maszyn wirtualnych. Po zakończeniu migracji można ponownie włączyć diagnostykę rozruchu w stosie Menedżer zasobów. Ponadto obiekty blob, które są używane na potrzeby zrzutu ekranu i dzienników seryjnych, powinny zostać usunięte, aby nie były już naliczane opłaty za te obiekty blob. |
| Wystąpienia obliczeniowe | Usługi w chmurze zawierające role sieci Web/procesu roboczego | Nie jest to obecnie obsługiwane. |
| Wystąpienia obliczeniowe | Usługi w chmurze, które zawierają więcej niż jeden zestaw dostępności lub wiele zestawów dostępności. |Nie jest to obecnie obsługiwane. Aby przeprowadzić migrację, Przenieś Virtual Machines do tego samego zestawu dostępności. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem Azure Security Center | Azure Security Center automatycznie instaluje rozszerzenia na Virtual Machines w celu monitorowania ich zabezpieczeń i zgłaszania alertów. Rozszerzenia te zwykle są instalowane automatycznie, jeśli zasady Azure Security Center są włączone w ramach subskrypcji. Aby przeprowadzić migrację Virtual Machines, wyłącz zasady usługi Security Center w subskrypcji, co spowoduje usunięcie rozszerzenia monitorowania Security Center z Virtual Machines. |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem kopii zapasowej lub migawek | Te rozszerzenia są instalowane na maszynie wirtualnej skonfigurowanej przy użyciu usługi Azure Backup. Mimo że migracja tych maszyn wirtualnych nie jest obsługiwana, postępuj zgodnie z poniższymi wskazówkami [, aby zachować](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) kopie zapasowe, które zostały wykonane przed migracją.  |
| Wystąpienia obliczeniowe | Maszyna wirtualna z rozszerzeniem Azure Site Recovery | Te rozszerzenia są instalowane na maszynie wirtualnej skonfigurowanej przy użyciu usługi Azure Site Recovery. Migracja magazynu używanego z Site Recovery będzie działać, ale będzie to miało wpływ na bieżącą replikację. Po migracji magazynu należy wyłączyć i włączyć replikację maszyny wirtualnej. |
| Sieć |Sieci wirtualne zawierające maszyny wirtualne i role sieci Web/proces roboczy |Nie jest to obecnie obsługiwane. Przed przeprowadzeniem migracji przenieś role sieci Web/proces roboczy do ich własnych Virtual Network. Po przeprowadzeniu migracji Virtual Network klasycznej Virtual Network zmigrowanego Azure Resource Manager można połączyć się za pomocą komunikacji równorzędnej z klasycznym Virtual Network w celu osiągnięcia podobnej konfiguracji zgodnie z wcześniejszym użyciem.|
| Sieć | Klasyczne obwody usługi Express Route |Nie jest to obecnie obsługiwane. Przed rozpoczęciem migracji IaaS należy zmigrować te obwody do Azure Resource Manager. Aby dowiedzieć się więcej, zobacz [Przechodzenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania do Menedżer zasobów](../expressroute/expressroute-move.md).|
| Azure App Service |Sieci wirtualne, które zawierają środowiska App Service |Nie jest to obecnie obsługiwane. |
| Azure HDInsight |Sieci wirtualne, które zawierają usługi HDInsight |Nie jest to obecnie obsługiwane. |
| Usługi Microsoft Dynamics cykl życia |Sieci wirtualne z maszynami wirtualnymi zarządzanymi przez usługi Dynamics cykle życia |Nie jest to obecnie obsługiwane. |
| Azure API Management |Sieci wirtualne zawierające wdrożenia usługi Azure API Management |Nie jest to obecnie obsługiwane. Aby przeprowadzić migrację sieci wirtualnej IaaS, Zmień sieć wirtualną wdrożenia API Management, która nie jest operacją przestoju. |

## <a name="next-steps"></a>Następne kroki

* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planowanie migracji zasobów rozwiązania IaaS z wdrożenia klasycznego do usługi Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Migrowanie zasobów IaaS z klasycznego do Azure Resource Manager za pomocą programu PowerShell](migration-classic-resource-manager-ps.md)
* [Użyj interfejsu wiersza polecenia do migrowania zasobów IaaS z klasycznego do Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Narzędzia społecznościowe ułatwiające migrację zasobów IaaS z klasycznej do Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Przegląd najbardziej typowych błędów migracji](migration-classic-resource-manager-errors.md)
* [Zapoznaj się z najczęściej zadawanymi pytaniami dotyczącymi migrowania zasobów IaaS z wersji klasycznej do Azure Resource Manager](migration-classic-resource-manager-faq.md)
