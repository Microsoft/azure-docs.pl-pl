---
title: Często zadawane pytania dotyczące urządzenia migracji platformy Azure
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące urządzenia migracji platformy Azure.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 529ead1baa769ee3e71f6fcf77ef7e020ed196a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529692"
---
# <a name="azure-migrate-appliance-common-questions"></a>Urządzenie migracji platformy Azure: typowe pytania

Ten artykuł zawiera odpowiedzi na typowe pytania dotyczące urządzenia migracji platformy Azure. Jeśli masz inne pytania, sprawdź następujące zasoby:

- [Ogólne pytania](resources-faq.md) dotyczące migracji platformy Azure
- Pytania dotyczące [wizualizacji odnajdowania, oceny i zależności](common-questions-discovery-assessment.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Odpowiedzi na pytania na [forum migracji platformy Azure](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co to jest urządzenie migracji platformy Azure?

Urządzenie migracji platformy Azure to lekkie urządzenie używane przez narzędzie Azure Migrate: Server Assessment do odnajdowania i oceny serwerów lokalnych. Narzędzie Migracji platformy Azure: Migracja serwera używa również urządzenia do bezagentowej migracji lokalnych maszyn wirtualnych VMware.

Oto więcej informacji na temat urządzenia migracji platformy Azure:

- Urządzenie jest wdrażane lokalnie jako maszyna wirtualna lub maszyna fizyczna.
- Urządzenie odnajduje maszyny lokalne i stale wysyła metadane maszyny i dane o wydajności do usługi Azure Migrate.
- Odnajdowanie urządzenia jest bez agenta. Nic nie jest zainstalowane na odnalezionych maszynach.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu.

## <a name="how-can-i-deploy-the-appliance"></a>Jak mogę wdrożyć urządzenie?

Urządzenie można wdrożyć w następujący sposób:

- Używanie szablonu dla maszyn wirtualnych VMware i maszyn wirtualnych funkcji Hyper-V (szablon OVA dla oprogramowania VMware lub VHD dla funkcji Hyper-V).
- Jeśli nie chcesz używać szablonu lub korzystasz z platformy Azure dla instytucji rządowych, możesz wdrożyć urządzenie dla VMware lub Hyper-V przy użyciu skryptu programu PowerShell.
- W przypadku serwerów fizycznych urządzenie jest zawsze wdrażane przy użyciu skryptu.


## <a name="how-does-the-appliance-connect-to-azure"></a>W jaki sposób urządzenie łączy się z platformą Azure?

Urządzenie można połączyć przez Internet lub przy użyciu usługi Azure ExpressRoute z komunikacji równorzędnej publicznych/Microsoft.

## <a name="does-appliance-analysis-affect-performance"></a>Czy analiza urządzeń wpływa na wydajność?

Usługi Azure Migrate profili urządzeń na komputerach lokalnych stale do pomiaru danych wydajności. To profilowanie nie ma prawie żadnego wpływu na wydajność profilowanych maszyn.

## <a name="can-i-harden-the-appliance-vm"></a>Czy mogę utwardzić maszynę wirtualną urządzenia?

Korzystając z pobranego szablonu do utworzenia maszyny Wirtualnej urządzenia, można dodać składniki (na przykład antywirusowe) do szablonu, jeśli pozostawisz w miejscu reguły komunikacji i zapory, które są wymagane dla urządzenia migracji platformy Azure.

## <a name="what-network-connectivity-is-required"></a>Jaka jest łączność sieciowa?


Urządzenie potrzebuje dostępu do adresów URL platformy Azure. [Przejrzyj](migrate-appliance.md#url-access) listę adresów URL.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane gromadzi urządzenie?

Zobacz następujące artykuły, aby uzyskać informacje na temat danych, które urządzenie migracji platformy Azure zbiera na maszynach wirtualnych:

- **VMware VM:** [Przeglądanie](migrate-appliance.md#collected-data---vmware) zebranych danych. [
- **Maszyna wirtualna funkcji Hyper-V:** [przeglądanie](migrate-appliance.md#collected-data---hyper-v) zebranych danych.

## <a name="how-is-data-stored"></a>W jaki sposób przechowywane są dane?

Dane zbierane przez urządzenie migracji platformy Azure są przechowywane w lokalizacji platformy Azure, w której utworzono projekt migracji platformy Azure.

Oto więcej informacji o sposobie przechowywania danych:

- Zebrane dane są bezpiecznie przechowywane w usłudze CosmosDB w ramach subskrypcji firmy Microsoft. Dane są usuwane po usunięciu projektu migracji platformy Azure. Magazyn jest obsługiwany przez usługę Azure Migrate. Nie można wybrać konta magazynu dla zebranych danych.
- Jeśli używasz [wizualizacji zależności,](concepts-dependency-visualization.md)zebrane dane są przechowywane w Stanach Zjednoczonych w obszarze roboczym usługi Azure Log Analytics utworzonym w ramach subskrypcji platformy Azure. Dane są usuwane po usunięciu obszaru roboczego usługi Log Analytics w ramach subskrypcji.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Ile danych jest przesyłanych podczas ciągłego profilowania?

Ilość danych wysyłanych do usługi Azure Migrate zależy od wielu parametrów. Na przykład projekt migracji platformy Azure, który ma 10 maszyn (każdy z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB danych dziennie. Ta wartość jest przybliżona; rzeczywista wartość różni się w zależności od liczby punktów danych dla dysków i kart sieciowych. Jeśli liczba komputerów, dysków lub kart sieciowych wzrasta, wzrost wysyłanych danych jest nieliniowy.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane w spoczynku i podczas przesyłania?

Tak, dla obu:

- Metadane są bezpiecznie wysyłane do usługi Azure Migrate za pośrednictwem Internetu za pośrednictwem protokołu HTTPS.
- Metadane są przechowywane w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) i w [magazynie obiektów Blob platformy Azure](../storage/common/storage-service-encryption.md) w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w spoczynku do przechowywania.
- Dane do analizy zależności również jest szyfrowany podczas przesyłania (przez bezpieczne HTTPS). Jest przechowywany w obszarze roboczym usługi Log Analytics w ramach subskrypcji. Dane są szyfrowane w spoczynku do analizy zależności.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>W jaki sposób urządzenie łączy się z serwerem vCenter Server?

W tych krokach opisano sposób łączenia urządzenia z serwerem VMware vCenter Server:

1. Urządzenie łączy się z serwerem vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia.
2. Urządzenie używa programu VMware PowerCLI do wykonywania zapytań dotyczących serwera vCenter Server w celu zbierania metadanych dotyczących maszyn wirtualnych zarządzanych przez serwer vCenter Server.
3. Urządzenie zbiera dane konfiguracyjne dotyczące maszyn wirtualnych (rdzenie, pamięci, dysków, kart sieciowych) i historii wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca.
4. Zebrane metadane są wysyłane do narzędzia Azure Migrate: Server Assessment tool (przez Internet za pośrednictwem protokołu HTTPS) w celu oceny.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Czy urządzenie usługi Azure Migrate może łączyć się z wieloma serwerami vCenter?

Nie. Istnieje mapowanie jeden do jednego między [urządzeniem migracji platformy Azure](migrate-appliance.md) a serwerem vCenter. Aby odnajdować maszyny wirtualne w wielu wystąpieniach serwera vCenter Server, należy wdrożyć wiele urządzeń. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Czy projekt migracji platformy Azure może mieć wiele urządzeń?
Projekt może mieć podłączone do niego wiele urządzeń. Jednak urządzenie może być skojarzone tylko z jednym projektem. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Czy urządzenie/replikacja usługi Azure migrate może łączyć się z tym samym centrum vCenter?
Tak. Do tego samego serwera vCenter można dodać zarówno urządzenie migracji platformy Azure (używane do oceny i bezagentowej migracji VMware), jak i urządzenie replikacji (używane do migracji maszyn wirtualnych opartych na agentach).


## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych lub serwerów można odkryć za pomocą urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware, do 5000 maszyn wirtualnych hyper-V i do 250 serwerów fizycznych z jednym urządzeniem. Jeśli masz więcej komputerów w środowisku lokalnym, przeczytaj o [skalowaniu oceny funkcji Hyper-V,](scale-hyper-v-assessment.md) [skalowaniu oceny VMware](scale-vmware-assessment.md)i [skalowaniu oceny serwera fizycznego.](scale-physical-assessment.md)

## <a name="can-i-delete-an-appliance"></a>Czy mogę usunąć urządzenie?

Obecnie usuwanie urządzenia z projektu nie jest obsługiwane.

Jedynym sposobem usunięcia urządzenia jest usunięcie grupy zasobów zawierającej projekt migracji platformy Azure skojarzony z urządzeniem.

Jednak usunięcie grupy zasobów usuwa również inne zarejestrowane urządzenia, wykryte zapasy, oceny i wszystkie inne składniki platformy Azure w grupie zasobów, które są skojarzone z projektem.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Czy mogę używać urządzenia z inną subskrypcją lub projektem?

Po użyciu urządzenia do inicjowania odnajdowania nie można ponownie skonfigurować urządzenia do użycia z inną subskrypcją platformy Azure i nie można go używać w innym projekcie migracji platformy Azure. Nie można również odnajdyć maszyn wirtualnych na innym wystąpieniu serwera vCenter. Skonfiguruj nowe urządzenie dla tych zadań.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Czy mogę skonfigurować urządzenie na maszynie Wirtualnej platformy Azure?

Nie. Obecnie ta opcja nie jest obsługiwana. 

## <a name="can-i-discover-on-an-esxi-host"></a>Czy mogę odkryć na hoście ESXi?

Nie. Aby odnajdować maszyny wirtualne VMware, musisz mieć serwer vCenter.

## <a name="how-do-i-update-the-appliance"></a>Jak zaktualizować urządzenie?

Domyślnie urządzenie i jego zainstalowane agentów są aktualizowane automatycznie. Urządzenie sprawdza dostępność aktualizacji co 24 godziny. Aktualizacje, które nie powiodą się, są ponawiane. 

Tylko urządzenia i agentów urządzenia są aktualizowane przez te aktualizacje automatyczne. System operacyjny nie jest aktualizowany przez aktualizacje automatyczne migracji platformy Azure. Aktualizacje systemu Windows umożliwia aktualizowanie systemu operacyjnego.

## <a name="can-i-check-agent-health"></a>Czy mogę sprawdzić stan zdrowia agenta?

Tak. W portalu przejdź na stronę **Kondycja agenta** dla narzędzia Azure Migrate: Server Assessment lub Azure Migrate: Server Migration. Tam można sprawdzić stan połączenia między platformą Azure a agentami odnajdywania i oceny na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Przeczytaj [omówienie migracji platformy Azure](migrate-services-overview.md).
