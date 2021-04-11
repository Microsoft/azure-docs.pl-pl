---
title: Często zadawane pytania dotyczące urządzenia Azure Migrate
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrateowego.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563025"
---
# <a name="azure-migrate-appliance-common-questions"></a>Urządzenie Azure Migrate: typowe pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące urządzenia Azure Migrateowego. Jeśli masz inne pytania, zapoznaj się z następującymi zasobami:

- [Ogólne pytania](resources-faq.md) dotyczące Azure Migrate
- Pytania dotyczące [odnajdywania, oceny i wizualizacji zależności](common-questions-discovery-assessment.md)
- Pytania dotyczące [migracji serwera](common-questions-server-migration.md)
- Uzyskaj odpowiedzi na pytania na [forum Azure Migrate](https://aka.ms/AzureMigrateForum)

## <a name="what-is-the-azure-migrate-appliance"></a>Co to jest urządzenie Azure Migrate?

Urządzenie Azure Migrate to lekkie urządzenie, za pomocą którego Azure Migrate: Narzędzie do odnajdywania i oceny, które umożliwia odnajdywanie i ocenianie serwerów fizycznych lub wirtualnych z poziomu lokalnego lub dowolnych chmur. Azure Migrate: Narzędzie do migracji serwera używa również urządzenia do migracji bez agentów serwerów lokalnych działających w środowisku VMware.

Poniżej przedstawiono więcej informacji o urządzeniu Azure Migrate:

- Urządzenie jest wdrażane lokalnie jako serwer fizyczny lub zwirtualizowany serwer.
- Urządzenie odnajduje serwery lokalne i ciągle wysyła dane o wydajności i metadanych serwera do Azure Migrate.
- Odnajdowanie urządzeń jest bez agentów. Nic nie jest zainstalowane na odnalezionych serwerach.

[Dowiedz się więcej](migrate-appliance.md) o urządzeniu.

## <a name="how-can-i-deploy-the-appliance"></a>Jak można wdrożyć urządzenie?

Urządzenie można wdrożyć przy użyciu kilku metod:

- Urządzenie można wdrożyć przy użyciu szablonu dla serwerów działających w środowisku VMware lub funkcji Hyper-V ([szablon komórki jajowe dla oprogramowania VMware](how-to-set-up-appliance-vmware.md) lub [wirtualnego dysku twardego dla funkcji Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Jeśli nie chcesz używać szablonu, możesz wdrożyć urządzenie dla środowiska VMware lub funkcji Hyper-V przy użyciu [skryptu Instalatora programu PowerShell](deploy-appliance-script.md).
- W Azure Government należy wdrożyć urządzenie przy użyciu skryptu Instalatora programu PowerShell. W [tym miejscu](deploy-appliance-script-government.md)zapoznaj się z krokami wdrożenia.
- W przypadku serwerów fizycznych lub zwirtualizowanych lokalnie lub w innych chmurach urządzenie jest zawsze wdrażane przy użyciu skryptu Instalatora programu PowerShell. W [tym miejscu](how-to-set-up-appliance-physical.md)zapoznaj się z krokami wdrożenia.

## <a name="how-does-the-appliance-connect-to-azure"></a>Jak urządzenie nawiązuje połączenie z platformą Azure?

Urządzenie może nawiązać połączenie za pośrednictwem Internetu lub przy użyciu usługi Azure ExpressRoute. 

- Upewnij się, że urządzenie może połączyć się z tymi [adresami URL platformy Azure](./migrate-appliance.md#url-access). 
- Możesz użyć ExpressRoute z usługą komunikacji równorzędnej firmy Microsoft. Publiczna Komunikacja równorzędna jest przestarzała i nie jest dostępna dla nowych obwodów usługi ExpressRoute.
- Prywatna Komunikacja równorzędna nie jest obsługiwana.


## <a name="does-appliance-analysis-affect-performance"></a>Czy analiza urządzenia ma wpływ na wydajność?

Lokalne profile urządzeń Azure Migrate są stale do mierzenia danych wydajności. To profilowania nie ma prawie wpływu na serwery profilowane.

## <a name="can-i-harden-the-appliance"></a>Czy mogę wzmocnić urządzenie?

W przypadku użycia pobranego szablonu do utworzenia urządzenia można dodać do szablonu składniki (na przykład program antywirusowy), jeśli użytkownik opuści reguły komunikacji i zapory wymagane przez urządzenie Azure Migrate.

## <a name="what-network-connectivity-is-required"></a>Jaka łączność sieciowa jest wymagana?

Urządzenie musi mieć dostęp do adresów URL platformy Azure. [Przejrzyj](migrate-appliance.md#url-access) listę adresów URL.

## <a name="what-data-does-the-appliance-collect"></a>Jakie dane są zbierane przez urządzenie?

Zapoznaj się z następującymi artykułami, aby uzyskać informacje na temat danych zbieranych na serwerach przez urządzenie Azure Migrate:

- **Serwery w środowisku VMware**: [Przejrzyj](migrate-appliance.md#collected-data---vmware) zebrane dane.
- **Serwery w środowisku funkcji Hyper-V**: [Przejrzyj](migrate-appliance.md#collected-data---hyper-v) zebrane dane.
- **Serwery fizyczne lub wirtualne**:[Przegląd](migrate-appliance.md#collected-data---physical) zebranych danych.

## <a name="how-is-data-stored"></a>W jaki sposób przechowywane są dane?

Dane zbierane przez urządzenie Azure Migrate są przechowywane w lokalizacji platformy Azure, w której został utworzony projekt.

Poniżej przedstawiono więcej informacji na temat sposobu przechowywania danych:

- Zebrane dane są bezpiecznie przechowywane w CosmosDB w ramach subskrypcji firmy Microsoft. Dane zostaną usunięte po usunięciu projektu. Magazyn jest obsługiwany przez Azure Migrate. Nie można wybrać konta magazynu dla zbieranych danych.
- Jeśli używasz [wizualizacji zależności](concepts-dependency-visualization.md), zebrane dane są przechowywane w obszarze roboczym usługi Azure log Analytics utworzonym w ramach subskrypcji platformy Azure. Dane zostaną usunięte po usunięciu obszaru roboczego Log Analytics w ramach subskrypcji.

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>Ile danych jest przekazywanych podczas ciągłego profilowania?

Ilość danych wysyłanych do Azure Migrate zależy od wielu parametrów. Na przykład projekt zawierający 10 serwerów (z jednym dyskiem i jedną kartą sieciową) wysyła około 50 MB danych dziennie. Ta wartość jest przybliżona; rzeczywista wartość różni się w zależności od liczby punktów danych na dyskach i w kartach sieciowych. W przypadku zwiększenia liczby serwerów, dysków lub kart sieciowych zwiększenie ilości wysyłanych danych jest nieliniowe.

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>Czy dane są szyfrowane podczas przechowywania i podczas przesyłania?

Tak, dla obu:

- Metadane są bezpiecznie wysyłane do usługi Azure Migrate przez Internet za pośrednictwem protokołu HTTPS.
- Metadane są przechowywane w bazie danych [usługi Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) i w usłudze [Azure Blob Storage](../storage/common/storage-service-encryption.md) w ramach subskrypcji firmy Microsoft. Metadane są szyfrowane w stanie spoczynku dla magazynu.
- Dane związane z analizą zależności są również szyfrowane w trakcie przesyłania (przez bezpieczne HTTPS). Jest ona przechowywana w obszarze roboczym Log Analytics w ramach subskrypcji. Dane są szyfrowane w stanie spoczynku dla analizy zależności.

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>Jak urządzenie nawiązuje połączenie z vCenter Server?

W tych krokach opisano, jak urządzenie łączy się z VMware vCenter Server:

1. Urządzenie łączy się z vCenter Server (port 443) przy użyciu poświadczeń podanych podczas konfigurowania urządzenia.
2. Urządzenie używa programu VMware PowerCLI do wykonywania zapytań vCenter Server, aby zbierać metadane dotyczące serwerów zarządzanych przez vCenter Server.
3. Urządzenie zbiera dane konfiguracyjne dotyczące serwerów (rdzenie, pamięć, dyski, karty sieciowe) i historię wydajności każdego serwera w ostatnim miesiącu.
4. Zebrane metadane są wysyłane do Azure Migrate: narzędzia do odnajdywania i oceny (przez Internet za pośrednictwem protokołu HTTPS) do oceny.

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Czy urządzenie Azure Migrate może nawiązać połączenie z wieloma serwerami vCenter?

Nie. Istnieje mapowanie jeden do jednego między [urządzeniem Azure Migrate](migrate-appliance.md) i vCenter Server. Aby odnajdywać serwery w wielu wystąpieniach vCenter Server, należy wdrożyć wiele urządzeń.

## <a name="can-a-project-have-multiple-appliances"></a>Czy projekt może mieć wiele urządzeń?

Projekt może mieć zarejestrowane wiele urządzeń. Jednak jedno urządzenie może być zarejestrowane tylko w jednym projekcie.

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Czy urządzenie Azure Migrate/urządzenie do replikacji nawiązuje połączenie z tym samym programem vCenter?

Tak. Do tego samego serwera vCenter można dodać urządzenie Azure Migrate (używane do oceny i migracji VMware bez agentów) oraz urządzenie do replikacji (używane w przypadku migracji na podstawie agentów serwerów działających w oprogramowaniu VMware). Pamiętaj jednak, aby nie konfigurować obu urządzeń na tym samym serwerze, co nie jest obecnie obsługiwane.

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Ile serwerów można wykryć przy użyciu urządzenia?

Można odnajdywać maksymalnie 10 000 serwerów w środowisku VMware, do 5 000 serwerów w środowisku funkcji Hyper-V oraz do 1000 serwery fizyczne przy użyciu jednego urządzenia. Jeśli masz więcej serwerów w środowisku lokalnym, zapoznaj się z tematem [skalowanie oceny funkcji Hyper-V](scale-hyper-v-assessment.md), [skalowanie oceny oprogramowania VMware](scale-vmware-assessment.md)i [skalowanie oceny serwera fizycznego](scale-physical-assessment.md).

## <a name="can-i-delete-an-appliance"></a>Czy mogę usunąć urządzenie?

Obecnie Usuwanie urządzenia z projektu nie jest obsługiwane.

Jedynym sposobem usunięcia urządzenia jest usunięcie grupy zasobów zawierającej projekt skojarzony z urządzeniem.

Jednak usunięcie grupy zasobów powoduje również usunięcie innych zarejestrowanych urządzeń, wykrytego spisu, ocen i wszystkich innych składników platformy Azure w grupie zasobów, które są skojarzone z projektem.

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>Czy mogę użyć urządzenia z inną subskrypcją lub projektem?

Aby użyć urządzenia z inną subskrypcją lub projektem, należy ponownie skonfigurować istniejące urządzenie, uruchamiając skrypt Instalatora programu PowerShell dla określonego scenariusza (VMware/Hyper-V/Physical) na urządzeniu. Skrypt będzie czyścił istniejące składniki i ustawienia urządzenia w celu wdrożenia nowego urządzenia. Przed rozpoczęciem korzystania z nowo wdrożonego programu Configuration Manager upewnij się, że pamięć podręczna przeglądarki została wyczyszczona.

Nie można też ponownie użyć istniejącego klucza projektu na urządzeniu, na którym został skonfigurowany. Upewnij się, że Wygenerowano nowy klucz z żądanej subskrypcji/projektu, aby zakończyć rejestrację urządzenia.

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Czy mogę skonfigurować urządzenie na maszynie wirtualnej platformy Azure?

Nie. Obecnie ta opcja nie jest obsługiwana.

## <a name="can-i-discover-on-an-esxi-host"></a>Czy mogę odnaleźć na hoście ESXi?

Nie. Aby odnajdywać serwery w środowisku VMware, musisz mieć vCenter Server.

## <a name="how-do-i-update-the-appliance"></a>Jak mogę zaktualizować urządzenie?

Domyślnie urządzenie i jego zainstalowanych agentów są automatycznie aktualizowane. Urządzenie sprawdza dostępność aktualizacji co 24 godziny. Nieudane aktualizacje. 

Te aktualizacje automatyczne są aktualizowane tylko przez urządzenie i agentów urządzeń. System operacyjny nie został zaktualizowany przez Azure Migrate automatyczne aktualizacje. Użyj aktualizacji systemu Windows, aby zapewnić aktualność systemu operacyjnego.

## <a name="can-i-check-agent-health"></a>Czy mogę sprawdzić kondycję agentów?

Tak. W portalu przejdź do strony **kondycja agenta** , aby uzyskać Azure Migrate: odnajdywanie i ocenianie lub Azure Migrate: Narzędzia migracji serwera. W tym miejscu można sprawdzić stan połączenia między platformą Azure a agentami odnajdywania i oceny na urządzeniu.

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>Czy mogę dodać wiele poświadczeń serwera na urządzeniu VMware?

Tak. teraz Obsługujemy wiele poświadczeń serwera w celu przeprowadzenia spisu oprogramowania (odnajdywania zainstalowanych aplikacji), analizy zależności bez agenta oraz odnajdywania wystąpień SQL Server i baz danych. [Dowiedz się więcej](tutorial-discover-vmware.md#provide-server-credentials) na temat udostępniania poświadczeń w Menedżerze konfiguracji urządzeń.

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>Jakiego typu poświadczenia serwera można dodać na urządzeniu VMware?
W Menedżerze konfiguracji urządzeń można podać domenę/system Windows (niebędący domeną)/Linux (niebędący domeną)/SQL Server poświadczenia uwierzytelniania. [Dowiedz się więcej](add-server-credentials.md) na temat podania poświadczeń i sposobu ich obsługi.

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>Jakiego typu SQL Server właściwości połączenia są obsługiwane przez Azure Migrate na potrzeby odnajdywania SQL?
Azure Migrate zaszyfruje komunikację między urządzeniem Azure Migrate i SQL Server wystąpieniami źródła (z właściwością Szyfruj połączenie ustawioną na wartość TRUE). Te połączenia są szyfrowane za pomocą [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) (wartość true). Warstwa transportu będzie używać protokołu SSL do szyfrowania kanału i pomijania łańcucha certyfikatów w celu zweryfikowania zaufania. Serwer urządzenia musi być skonfigurowany do [zaufania głównego urzędu certyfikacji](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).

Jeśli podczas uruchamiania nie zainicjowano obsługi administracyjnej certyfikatu na serwerze, SQL Server generuje certyfikat z podpisem własnym, który jest używany do szyfrowania pakietów logowania. [Dowiedz się więcej](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine).


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [omówieniem Azure Migrate](migrate-services-overview.md).