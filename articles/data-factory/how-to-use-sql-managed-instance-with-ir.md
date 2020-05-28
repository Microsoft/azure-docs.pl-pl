---
title: Używanie Azure SQL Database wystąpienia zarządzanego z platformą Azure SQL Server Integration Services (SSIS) w Azure Data Factory
description: Dowiedz się, jak używać Azure SQL Database wystąpienia zarządzanego z usługą SQL Server Integration Services (SSIS) w Azure Data Factory.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: cd07bf86852d608a6d872f4c6b973b0a81b2a1c3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015288"
---
# <a name="use-azure-sql-database-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Użyj Azure SQL Database wystąpienia zarządzanego z SQL Server Integration Services (SSIS) w Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Teraz możesz przenosić projekty, pakiety i obciążenia SQL Server Integration Services (SSIS) do chmury platformy Azure. Wdrażaj i uruchamiaj projekty SSIS oraz pakiety na Azure SQL Database lub SQL Database zarządzanej instancji przy użyciu znanych narzędzi, takich jak SQL Server Management Studio (SSMS). W tym artykule przedstawiono następujące obszary dotyczące korzystania z Azure SQL Database wystąpienia zarządzanego z usługą Azure-SSIS Integration Runtime (IR):

- [Inicjowanie obsługi Azure-SSIS IR przy użyciu wykazu usług SSIS (SSISDB) hostowanego przez wystąpienie zarządzane Azure SQL Database](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Wykonywanie pakietów usług SSIS za pomocą zadania agenta wystąpienia zarządzanego usługi Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Czyszczenie dzienników SSISDB za pomocą zadania agenta wystąpienia zarządzanego Azure SQL](#clean-up-ssisdb-logs)
- [Azure-SSIS IR trybu failover z wystąpieniem Azure SQL Database zarządzanym](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-database-managed-instance)
- [Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku APD z Azure SQL Database wystąpieniem zarządzanym jako miejsce docelowe obciążeń bazy danych](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Udostępnianie Azure-SSIS IR z usługą SSISDB hostowaną przez wystąpienie zarządzane Azure SQL

### <a name="prerequisites"></a>Wymagania wstępne

1. [Włącz Azure Active Directory (Azure AD) na wystąpieniu zarządzanym Azure SQL Database](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance), wybierając Azure Active Directory uwierzytelnianie.

1. Wybierz sposób połączenia wystąpienia zarządzanego SQL, za pośrednictwem prywatnego punktu końcowego lub za pośrednictwem publicznego punktu końcowego:

    - Za pośrednictwem prywatnego punktu końcowego (preferowany)

        1. Wybierz sieć wirtualną, dla której chcesz dołączyć Azure-SSIS IR:
            - W tej samej sieci wirtualnej co wystąpienie zarządzane SQL z **inną podsiecią**.
            - W innej sieci wirtualnej niż wystąpienie zarządzane SQL za pośrednictwem komunikacji równorzędnej sieci wirtualnych (ograniczone do tego samego regionu ze względu na globalne ograniczenia wirtualnych sieci równorzędnej) lub połączenie z sieci wirtualnej z siecią wirtualną.

            Aby uzyskać więcej informacji na temat łączności wystąpienia zarządzanego SQL, zobacz [łączenie aplikacji z Azure SQL Database wystąpieniem zarządzanym](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app).

        1. [Konfigurowanie sieci wirtualnej](#configure-virtual-network).

    - Za pośrednictwem publicznego punktu końcowego

        Azure SQL Database wystąpienia zarządzane mogą zapewniać łączność za pośrednictwem [publicznych punktów końcowych](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure). Wymagania dotyczące ruchu przychodzącego i wychodzącego muszą być spełnione, aby umożliwić ruch między wystąpieniem zarządzanym SQL i Azure-SSIS IR:

        - gdy nie Azure-SSIS IR w sieci wirtualnej (preferowany)

            **Przychodzące wymagania wystąpienia zarządzanego SQL**w celu zezwolenia na ruch przychodzący z Azure-SSIS IR.

            | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych |
            |---|---|---|---|---|
            |TCP|Tag usługi w chmurze platformy Azure|*|VirtualNetwork|3342|

            Aby uzyskać więcej informacji, zobacz [Zezwalanie na ruch publicznego punktu końcowego w sieciowej grupie zabezpieczeń](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure#allow-public-endpoint-traffic-on-the-network-security-group).

        - gdy Azure-SSIS IR wewnątrz sieci wirtualnej

            Istnieje specjalny scenariusz, gdy wystąpienie zarządzane SQL znajduje się w regionie, który nie obsługuje Azure-SSIS IR, Azure-SSIS IR znajduje się w sieci wirtualnej bez komunikacji równorzędnej wirtualnej ze względu na globalne ograniczenie wirtualnej sieci równorzędnej. W tym scenariuszu **Azure-SSIS IR wewnątrz sieci wirtualnej** nawiązuje połączenie wystąpienia zarządzanego SQL **za pośrednictwem publicznego punktu końcowego**. Użyj poniższej reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zezwolić na ruch między wystąpieniem zarządzanym SQL i Azure-SSIS IR:

            1. **Przychodzące wymagania wystąpienia zarządzanego SQL**w celu zezwolenia na ruch przychodzący z Azure-SSIS IR.

                | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy |Zakres portów docelowych |
                |---|---|---|---|---|
                |TCP|Statyczny adres IP Azure-SSIS IR <br> Aby uzyskać szczegółowe informacje, zobacz temat [przenoszenie własnego publicznego adresu IP do Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network.md#publicIP).|*|VirtualNetwork|3342|

             1. **Wychodzące wymaganie Azure-SSIS IR**, aby zezwolić na ruch wychodzący do wystąpienia zarządzanego SQL.

                | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy |Zakres portów docelowych |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[Adres IP publicznego punktu końcowego wystąpienia zarządzanego SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-find-management-endpoint-ip-address)|3342|

### <a name="configure-virtual-network"></a>Konfigurowanie sieci wirtualnej

1. **Uprawnienie użytkownika**. Użytkownik, który tworzy Azure-SSIS IR, musi [mieć co najmniej](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) jeden zasób Azure Data Factory z jedną z poniższych opcji:

    - Użyj wbudowanej roli współautor sieci. Ta rola jest dostarczana z _firmą Microsoft. \* Network/_ uprawnieniem o znacznie większym zakresie niż jest to konieczne.
    - Utwórz rolę niestandardową, która zawiera tylko wymagane uprawnienie _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Jeśli chcesz również przenieść własne publiczne adresy IP do Azure-SSIS IR podczas dołączania do Azure Resource Manager sieci wirtualnej, w roli należy również włączyć uprawnienie _Microsoft. Network/adresów publicipaddress/*/Join/Action_ .

1. **Sieć wirtualna**.

    1. Upewnij się, że grupa zasobów sieci wirtualnej umożliwia tworzenie i usuwanie niektórych zasobów sieci platformy Azure.

        Azure-SSIS IR musi utworzyć określone zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Te zasoby obejmują:
        - Moduł równoważenia obciążenia platformy Azure o nazwie * \<Guid> -azurebatch-cloudserviceloadbalancer*
        - Sieciową grupę zabezpieczeń o nazwie * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Publiczny adres IP platformy Azure o nazwie-azurebatch-cloudservicepublicip

        Te zasoby zostaną utworzone po rozpoczęciu Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu Azure-SSIS IR. Aby zapobiec zablokowaniu Azure-SSIS IR z zatrzymywania, nie używaj ponownie tych zasobów sieciowych w innych zasobach.

    1. Upewnij się, że nie masz [blokady zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) w grupie zasobów/subskrypcji, do której należy Sieć wirtualna. W przypadku skonfigurowania blokady tylko do odczytu/usuwania, uruchamianie i zatrzymywanie Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

    1. Upewnij się, że nie masz zasad platformy Azure, które uniemożliwiają utworzenie następujących zasobów w grupie zasobów/subskrypcji, do której należy Sieć wirtualna:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

    1. Zezwalaj na ruch w regule sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zezwalać na ruch między wystąpieniem zarządzanym i Azure-SSIS IR SQL oraz ruchem wymaganym przez Azure-SSIS IR.
        1. **Przychodzące wymagania wystąpienia zarządzanego SQL**w celu zezwolenia na ruch przychodzący z Azure-SSIS IR.

            | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|Jeśli zasada połączenia serwera SQL Database jest ustawiona na **serwer proxy** zamiast **przekierowania**, wymagany jest tylko port 1433.|

        1. **Wymagania Wychodzące Azure-SSIS IR**, aby zezwolić na ruch wychodzący do wystąpienia zarządzanego SQL i inny ruch wymagany przez Azure-SSIS IR.

        | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |Zezwalaj na ruch wychodzący do wystąpienia zarządzanego SQL. Jeśli dla zasad połączenia ustawiono wartość **serwer proxy** zamiast **przekierowania**, wymagany jest tylko port 1433. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do uzyskiwania dostępu do usług platformy Azure, takich jak Azure Storage i Azure Event Hubs. |
        | TCP | VirtualNetwork | * | Internet | 80 | Obowiązkowe Węzły Azure-SSIS IR w sieci wirtualnej używają tego portu do pobierania listy odwołania certyfikatów z Internetu. Jeśli zablokujesz ten ruch, może wystąpić obniżenie wydajności podczas uruchamiania środowiska IR i utrata możliwości sprawdzenia listy odwołania certyfikatów w celu użycia certyfikatu. Jeśli chcesz jeszcze bardziej zawęzić miejsce docelowe do określonych nazw FQDN, zapoznaj się z tematem [Korzystanie z usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR)](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network#route).|
        | TCP | VirtualNetwork | * | Magazyn | 445 | Obowiązkowe Ta reguła jest wymagana tylko wtedy, gdy chcesz uruchomić pakiet SSIS przechowywany w Azure Files. |
        |||||||

        1. **Wymagania przychodzące Azure-SSIS IR**, aby zezwolić na ruch wymagany przez Azure-SSIS IR.

        | Protokół transportowy | Element źródłowy | Zakres portów źródłowych | Element docelowy | Zakres portów docelowych | Komentarze |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877 (w przypadku dołączenia środowiska IR do sieci wirtualnej Menedżer zasobów) <br/><br/>10100, 20100, 30100 (Jeśli dołączysz środowisko IR do klasycznej sieci wirtualnej)| Usługa Data Factory używa tych portów do komunikacji z węzłami Azure-SSIS IR w sieci wirtualnej. <br/><br/> Niezależnie od tego, czy tworzysz sieciowej grupy zabezpieczeń poziomu podsieci, Data Factory zawsze konfiguruje sieciowej grupy zabezpieczeń na poziomie kart sieciowych podłączonych do maszyn wirtualnych, które obsługują Azure-SSIS IR. Tylko ruch przychodzący z Data Factory adresów IP na określonych portach jest dozwolony przez ten sieciowej grupy zabezpieczeń na poziomie karty sieciowej. Nawet w przypadku otwarcia tych portów do ruchu internetowego na poziomie podsieci ruch z adresów IP, które nie są Data Factory adresy IP, jest blokowany na poziomie karty sieciowej. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | Obowiązkowe Ta reguła jest wymagana tylko wtedy, gdy dział pomocy technicznej firmy Microsoft prosi klienta o otwarcie w celu zaawansowania rozwiązywania problemów i może być zamknięty bezpośrednio po rozwiązaniu problemu. Tag usługi **CorpNetSaw** zezwala na korzystanie z pulpitu zdalnego tylko zabezpieczonym stacjom roboczym dostępu w sieci firmowej firmy Microsoft. Nie można wybrać tego tagu usługi z portalu i jest on dostępny tylko za pośrednictwem Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. <br/><br/> Na poziomie karty sieciowej sieciowej grupy zabezpieczeń Port 3389 jest otwarty domyślnie i zezwalamy na kontrolowanie portu 3389 na poziomie podsieci sieciowej grupy zabezpieczeń, Tymczasem Azure-SSIS IR nie 3389 zezwolił na ruch wychodzący domyślnie w regule zapory systemu Windows w każdym węźle podczerwieni do ochrony. |
        |||||||

    1. Aby uzyskać więcej informacji, zobacz [Konfiguracja sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration) :
        - Jeśli utworzysz własne publiczne adresy IP dla Azure-SSIS IR
        - Jeśli używasz własnego serwera DNS (Domain Name System)
        - Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR)
        - Jeśli używasz dostosowanych Azure-SSIS IR

### <a name="provision-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime udostępniania

1. Wybierz prywatny punkt końcowy wystąpienia zarządzanego SQL lub publiczny punkt końcowy.

    Podczas [inicjowania obsługi Azure-SSIS IR](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) w aplikacji Azure Portal/ADF na stronie Ustawienia SQL należy użyć **prywatnego punktu końcowego** wystąpienia zarządzanego SQL lub **publicznego punktu końcowego** podczas tworzenia wykazu usług SSIS (SSISDB).

    Nazwa hosta publicznego punktu końcowego jest w formacie <mi_name>. Public. <dns_zone>. database.windows.net i że port używany do połączenia to 3342.  

    ![wykaz — publiczny — punkt końcowy](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. Wybierz pozycję Uwierzytelnianie w usłudze Azure AD, jeśli ma zastosowanie.

    ![wykaz — publiczny — punkt końcowy](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Aby uzyskać więcej informacji o sposobie włączania uwierzytelniania usługi Azure AD, zobacz [Włączanie usługi Azure AD na Azure SQL Database wystąpienia zarządzanego](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance).

1. Dołącz Azure-SSIS IR do sieci wirtualnej, jeśli ma zastosowanie.

    Na stronie Ustawienia zaawansowane wybierz Virtual Network i podsieć do przyłączenia.
    
    Gdy znajduje się w tej samej sieci wirtualnej co wystąpienie zarządzane SQL, wybierz **inną podsieć** niż wystąpienie zarządzane SQL. 

    Aby uzyskać więcej informacji na temat dołączania Azure-SSIS IR do sieci wirtualnej, zobacz [dołączanie środowiska Azure-SSIS Integration Runtime do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).

    ![Dołącz do sieci wirtualnej](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Aby uzyskać więcej informacji na temat sposobu tworzenia Azure-SSIS IR, zobacz [Tworzenie środowiska Azure-SSIS Integration Runtime w programie Azure Data Factory](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime).

## <a name="clean-up-ssisdb-logs"></a>Czyszczenie dzienników SSISDB

Zasady przechowywania dzienników SSISDB są zdefiniowane poniżej właściwości w [wykazie. catalog_properties](https://docs.microsoft.com/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15):

- OPERATION_CLEANUP_ENABLED

    Gdy wartość jest RÓWNa TRUE, szczegóły operacji i komunikaty operacji starsze niż RETENTION_WINDOW (dni) są usuwane z wykazu. Gdy wartość jest równa FALSE, wszystkie szczegóły operacji i komunikaty operacji są przechowywane w wykazie. Uwaga: zadanie SQL Server wykonuje oczyszczanie operacji.

- RETENTION_WINDOW

    Liczba dni, w których szczegóły operacji i komunikaty operacji są przechowywane w wykazie. Gdy wartość to-1, okno przechowywania jest nieskończone. Uwaga: Jeśli nie jest wymagane oczyszczanie, ustaw OPERATION_CLEANUP_ENABLED na FALSE.

Aby usunąć dzienniki SSISDB, które znajdują się poza oknem przechowywania ustawionym przez administratora, można wyzwolić procedurę składowaną `[internal].[cleanup_server_retention_window_exclusive]` . Opcjonalnie można zaplanować wykonywanie zadania agenta wystąpienia zarządzanego przez SQL w celu wyzwolenia procedury składowanej.

## <a name="next-steps"></a>Następne kroki

- [Wykonywanie pakietów usług SSIS za pomocą zadania agenta wystąpienia zarządzanego usługi Azure SQL](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Skonfiguruj ciągłość biznesową i odzyskiwanie po awarii (BCDR)](configure-bcdr-azure-ssis-integration-runtime.md)
- [Migrowanie lokalnych obciążeń usług SSIS do usług SSIS w podajniku APD](scenario-ssis-migration-overview.md)
