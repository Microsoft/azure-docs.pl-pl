---
title: Często zadawane pytania dotyczące urządzenia Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrateowego.
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 7839c2678152837cc9217e6afe13f7cca36ab4b0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630495"
---
# <a name="azure-migrate-appliance-common-questions"></a>Urządzenie Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrateowego. Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [odnajdywania, oceny i wizualizacji zależności](common-questions-discovery-assessment.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co to jest urządzenie Azure Migrate?

Urządzenie Azure Migrate to lekkie urządzenie, za pomocą którego Azure Migrate: Narzędzie do oceny serwera służy do odnajdywania i oceniania serwerów fizycznych lub wirtualnych z poziomu lokalnego lub dowolnych chmur. Azure Migrate: Narzędzie do migracji serwera używa również urządzenia do migracji bez agentów lokalnych maszyn wirtualnych VMware.

Poniżej przedstawiono więcej informacji o urządzeniu Azure Migrate:

- Urządzenie jest wdrażane lokalnie jako maszynę wirtualną lub na komputerze fizycznym.
- Urządzenie umożliwia odnalezienie maszyn lokalnych i ciągłe wysyłanie metadanych maszyn i danych wydajności do Azure Migrate.
- Odnajdowanie urządzeń jest bez agentów. Nic nie jest zainstalowane na odnalezionych maszynach.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu.

## <a name="how-can-i-deploy-the-appliance"></a>Jak można wdrożyć urządzenie?

Urządzenie można wdrożyć w następujący sposób:

- Używanie szablonu do odnajdywania maszyn wirtualnych VMware (. Plik komórki jajowe) i maszyny wirtualne funkcji Hyper-V (. Plik VHD), aby utworzyć nową maszynę wirtualną, która hostuje urządzenie.
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie na istniejącej maszynie fizycznej lub wirtualnej na potrzeby odnajdywania maszyn wirtualnych VMware lub maszyn wirtualnych funkcji Hyper-V przy użyciu skryptu Instalatora programu PowerShell dostępnego do pobrania w pliku zip z portalu.
- W przypadku serwerów fizycznych lub wirtualnych z poziomu lokalnego lub dowolnej chmury zawsze można wdrożyć urządzenie przy użyciu skryptu na istniejącym serwerze.
- W przypadku Azure Government wszystkie trzy urządzenia można wdrożyć tylko przy użyciu skryptu Instalatora programu PowerShell.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak urządzenie nawiązuje połączenie z platformą Azure?

Urządzenie może nawiązać połączenie za pośrednictwem Internetu lub przy użyciu usługi Azure ExpressRoute. Upewnij się, że te [adresy URL](https://docs.microsoft.com/azure/migrate/migrate-appliance#url-access) są zatwierdzone do nawiązania połączenia z platformą Azure.

- Aby można było używać usługi Azure ExpressRoute Azure Migrate do obsługi ruchu związanego z replikacją, wymagana jest Komunikacja równorzędna firmy Microsoft lub istniejąca publiczna Komunikacja równorzędna (publiczna Komunikacja równorzędna jest przestarzała w przypadku nowych tworzenia obiektów.).
- Replikacja za pośrednictwem usługi Azure ExpressRoute z włączoną obsługą prywatnej komunikacji równorzędnej nie jest obsługiwana.

Usługa Azure ExpressRoute z konfiguracją komunikacji równorzędnej firmy Microsoft jest zalecaną domeną routingu dla ruchu związanego z replikacją.

## <a name="does-appliance-analysis-affect-performance"></a>Czy analiza urządzenia ma wpływ na wydajność?

Lokalne profile urządzeń Azure Migrate są stale do mierzenia danych wydajności. Takie Profilowanie nie ma prawie żadnego wpływu na wydajność maszyn.

## <a name="can-i-harden-the-appliance-vm"></a>Czy mogę zabezpieczyć maszynę wirtualną urządzenia?

W przypadku użycia pobranego szablonu do utworzenia maszyny wirtualnej urządzenia można dodać do szablonu składniki (na przykład program antywirusowy), jeśli użytkownik opuści reguły komunikacji i zapory wymagane przez urządzenie Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Jaka łączność sieciowa jest wymagana?

Urządzenie musi mieć dostęp do adresów URL platformy Azure. [Przejrzyj](migrate-appliance.md#url-access) listę adresów URL.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Zapoznaj się z następującymi artykułami, aby uzyskać informacje na temat danych zbieranych na maszynach wirtualnych przez urządzenie Azure Migrate:

- **Maszyna wirtualna VMware**: [przeglądanie](migrate-appliance.md#collected-data---vmware) zebranych danych.
- **Maszyna wirtualna funkcji Hyper-V**: [Przegląd](migrate-appliance.md#collected-data---hyper-v) zebranych danych.
- **Serwery fizyczne lub wirtualne**:[Przegląd](migrate-appliance.md#collected-data---physical) zebranych danych.

## <a name="how-is-data-stored"></a>W jaki sposób przechowywane są dane?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure, w której został utworzony projekt Azure Migrate.

Poniżej przedstawiono więcej informacji na temat sposobu przechowywania danych:

- Zebrane dane są bezpiecznie przechowywane w CosmosDB w ramach subskrypcji firmy Microsoft. Dane zostaną usunięte po usunięciu projektu Azure Migrate. Magazyn jest obsługiwany przez Azure Migrate. Nie można wybrać konta magazynu dla zbieranych danych.
- Jeśli używasz [wizualizacji zależności](concepts-dependency-visualization.md), zebrane dane są przechowywane w obszarze roboczym usługi Azure log Analytics utworzonym w ramach subskrypcji platformy Azure. Dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji. 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Ile danych jest przekazywanych podczas ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate zależy od wielu parametrów. Na przykład projekt Azure Migrate zawierający 10 maszyn (z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB danych dziennie. Ta wartość jest przybliżona; rzeczywista wartość różni się w zależności od liczby punktów danych na dyskach i w kartach sieciowych. W przypadku zwiększenia liczby maszyn, dysków lub kart sieciowych zwiększenie ilości wysyłanych danych jest nieliniowe.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i podczas przesyłania?

Tak, dla obu:

- Metadane są bezpiecznie wysyłane do usługi Azure Migrate przez Internet za pośrednictwem protokołu HTTPS.
- Metadane są przechowywane w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) i w usłudze [Azure Blob Storage](../storage/common/storage-service-encryption.md) w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w stanie spoczynku dla magazynu.
- Dane związane z analizą zależności są również szyfrowane w trakcie przesyłania (przez bezpieczne HTTPS). Jest ona przechowywana w obszarze roboczym Log Analytics w ramach subskrypcji. Dane są szyfrowane w stanie spoczynku dla analizy zależności.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak urządzenie nawiązuje połączenie z vCenter Server?

W tych krokach opisano, jak urządzenie łączy się z VMware vCenter Server:

1. Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia.
2. Urządzenie używa programu VMware PowerCLI do wykonywania zapytań vCenter Server, aby zbierać metadane dotyczące maszyn wirtualnych zarządzanych przez vCenter Server.
3. Urządzenie zbiera dane konfiguracyjne o maszynach wirtualnych (rdzenie, pamięć, dyski, karty sieciowe) i historię wydajności każdej maszyny wirtualnej w ciągu ostatniego miesiąca.
4. Zebrane metadane są wysyłane do Azure Migrate: narzędzia do oceny serwera (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Czy urządzenie Azure Migrate może nawiązać połączenie z wieloma serwerami vCenter?

Nie. Istnieje mapowanie jeden do jednego między [urządzeniem Azure Migrate](migrate-appliance.md) i vCenter Server. Aby odnajdywać maszyny wirtualne w wielu wystąpieniach vCenter Server, należy wdrożyć wiele urządzeń. 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Czy projekt Azure Migrate ma wiele urządzeń?

Projekt może mieć dołączone wiele urządzeń. Urządzenie może być jednak skojarzone tylko z jednym projektem. 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Czy urządzenie Azure Migrate/urządzenie do replikacji nawiązuje połączenie z tym samym programem vCenter?

Tak. Do tego samego serwera vCenter można dodać urządzenie Azure Migrate (używane do oceny i migracji VMware bez agentów) oraz urządzenie do replikacji (używane do migracji opartej na agentach maszyn wirtualnych VMware). Pamiętaj jednak, aby nie konfigurować obu urządzeń na tej samej maszynie wirtualnej, co nie jest obecnie obsługiwane.

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>Ile maszyn wirtualnych lub serwerów można wykryć przy użyciu urządzenia?

Możesz odkryć do 10 000 maszyn wirtualnych VMware, do 5 000 maszyn wirtualnych funkcji Hyper-V, a nawet do 1000 serwery fizyczne przy użyciu jednego urządzenia. Jeśli masz więcej maszyn w środowisku lokalnym, zapoznaj się z tematem [skalowanie oceny funkcji Hyper-V](scale-hyper-v-assessment.md), [skalowanie oceny oprogramowania VMware](scale-vmware-assessment.md)i [skalowanie oceny serwera fizycznego](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Czy mogę usunąć urządzenie?

Obecnie Usuwanie urządzenia z projektu nie jest obsługiwane.

Jedynym sposobem usunięcia urządzenia jest usunięcie grupy zasobów zawierającej projekt Azure Migrate, który jest skojarzony z urządzeniem.

Jednak usunięcie grupy zasobów powoduje również usunięcie innych zarejestrowanych urządzeń, wykrytego spisu, ocen i wszystkich innych składników platformy Azure w grupie zasobów, które są skojarzone z projektem.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Czy mogę użyć urządzenia z inną subskrypcją lub projektem?

Aby można było użyć urządzenia z inną subskrypcją lub projektem, należy ponownie skonfigurować istniejące urządzenie, uruchamiając skrypt Instalatora programu PowerShell dla określonego scenariusza (VMware/Hyper-V/Physical) na komputerze. Skrypt będzie czyścił istniejące składniki i ustawienia urządzenia w celu wdrożenia nowego urządzenia. Przed rozpoczęciem korzystania z nowo wdrożonego programu Configuration Manager upewnij się, że pamięć podręczna przeglądarki została wyczyszczona.

Nie można również użyć istniejącego klucza projektu Azure Migrate na urządzeniu, na którym został skonfigurowany. Upewnij się, że Wygenerowano nowy klucz z żądanej subskrypcji/projektu, aby zakończyć rejestrację urządzenia.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Czy mogę skonfigurować urządzenie na maszynie wirtualnej platformy Azure?

Nie. Obecnie ta opcja nie jest obsługiwana. 

## <a name="can-i-discover-on-an-esxi-host"></a>Czy mogę odnaleźć na hoście ESXi?

Nie. Aby odnaleźć maszyny wirtualne VMware, musisz mieć vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Jak mogę zaktualizować urządzenie?

Domyślnie urządzenie i jego zainstalowanych agentów są automatycznie aktualizowane. Urządzenie sprawdza dostępność aktualizacji co 24 godziny. Nieudane aktualizacje. 

Te aktualizacje automatyczne są aktualizowane tylko przez urządzenie i agentów urządzeń. System operacyjny nie został zaktualizowany przez Azure Migrate automatyczne aktualizacje. Użyj aktualizacji systemu Windows, aby zapewnić aktualność systemu operacyjnego.

## <a name="can-i-check-agent-health"></a>Czy mogę sprawdzić kondycję agentów?

Tak. W portalu przejdź do strony **kondycja agenta** , aby uzyskać Azure Migrate: Ocena serwera lub Azure Migrate: Narzędzie migracji serwera. W tym miejscu można sprawdzić stan połączenia między platformą Azure a agentami odnajdywania i oceny na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).
