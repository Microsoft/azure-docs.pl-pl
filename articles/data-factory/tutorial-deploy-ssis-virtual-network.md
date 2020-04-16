---
title: Samouczek konfigurowania środowiska wykonawczego integracji platformy Azure-SSIS w celu dołączenia do sieci wirtualnej
description: Dowiedz się, jak dołączyć do środowiska wykonawczego integracji platformy Azure-SSIS z siecią wirtualną platformy Azure.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 7470af23059ccd07e48050e6dc34521e299a986f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418613"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Konfigurowanie środowiska uruchomieniowego integracji usług integracyjnych usługi Azure-SQL Server (SSIS) (IR) w celu dołączenia do sieci wirtualnej

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten samouczek zawiera podstawowe kroki dotyczące korzystania z witryny Azure Portal w celu skonfigurowania środowiska uruchomieniowego integracji usługi Azure-SQL Server Integration Services (IR) w celu dołączenia do sieci wirtualnej.

Kroki obejmują:

- Skonfiguruj sieć wirtualną.
- Dołącz do usługi Azure-SSIS IR do sieci wirtualnej z portalu usługi Azure Data Factory.

## <a name="prerequisites"></a>Wymagania wstępne

- **Środowisko uruchomieniowe integracji platformy Azure-SSIS**. Jeśli nie masz środowiska wykonawczego integracji platformy Azure-SSIS, [aprowizuj środowisko uruchomieniowe integracji platformy Azure-SSIS w usłudze Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) przed rozpoczęciem.

- **Uprawnienie użytkownika**. Użytkownik, który tworzy azure-SSIS IR musi mieć [przypisanie roli](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) co najmniej w zasobie usługi Azure Data Factory z jedną z poniższych opcji:

    - Użyj wbudowanej roli Współautor sieci. Ta rola jest dostępna z uprawnieniem _Microsoft.Network/,\* _ które ma znacznie większy zakres niż jest to konieczne.
    - Utwórz rolę niestandardową, która zawiera tylko niezbędne _uprawnienie Microsoft.Network/virtualNetworks/\*/join/action._ Jeśli chcesz również przynieść własne publiczne adresy IP dla usługi Azure-SSIS IR podczas łączenia go do sieci wirtualnej usługi Azure Resource Manager, dołącz również _Microsoft.Network/publicIPAddresses/*/join/action_ permission w roli.

- **Sieć wirtualna**.

    - Jeśli nie masz sieci wirtualnej, [utwórz sieć wirtualną za pomocą portalu Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal).

    - Upewnij się, że grupa zasobów sieci wirtualnej może tworzyć i usuwać określone zasoby sieci platformy Azure.
    
        Usługa Azure-SSIS IR musi utworzyć pewne zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Zasoby te obejmują:
        - Moduł równoważenia obciążenia platformy Azure o nazwie * \<Guid>-azurebatch-cloudserviceloadbalancer*
        - Sieciowa grupa zabezpieczeń o\<nazwie * Guid>-azurebatch-cloudservicenetworksecuritygroup
        - Publiczny adres IP platformy Azure o nazwie -azurebatch-cloudservicepublicip
    
        Te zasoby zostaną utworzone po uruchomieniu usługi Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu usługi Azure-SSIS IR. Aby uniknąć blokowania zatrzymania usługi Azure-SSIS IR, nie należy ponownie używać tych zasobów sieciowych w innych zasobach.

    - Upewnij się, że nie masz [blokady zasobów](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) w grupie zasobów/subskrypcji, do której należy sieć wirtualna. Jeśli skonfigurujesz blokadę tylko do odczytu/usunięcia, uruchamianie i zatrzymywanie usługi Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

    - Upewnij się, że nie masz zasad platformy Azure, które uniemożliwiają tworzenie następujących zasobów w ramach grupy zasobów/subskrypcji, do której należy sieć wirtualna:
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- Poniższe scenariusze **konfiguracji sieci** nie zostały omówione w tym samouczku:
    - Jeśli przywieziesz własne publiczne adresy IP dla usługi Azure-SSIS IR.
    - Jeśli używasz własnego serwera DNS (Domain Name System).
    - Jeśli używasz sieciowej grupy zabezpieczeń (NSG) w podsieci.
    - Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR).
    - Jeśli używasz dostosowane Azure-SSIS IR.
    
    Aby uzyskać więcej informacji, sprawdź [konfigurację sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Użyj witryny Azure Portal, aby skonfigurować sieć wirtualną przed podjęciem próby dołączenia do usługi Azure-SSIS IR do niej.

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki internetowe obsługują interfejs użytkownika fabryki danych.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **pozycję Więcej usług**. Filtruj i wybierz **pozycję Sieci wirtualne**.

1. Filtruj i wybierz sieć wirtualną na liście.

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**.

1. Wybierz przycisk kopiowania **identyfikatora zasobu,** aby skopiować identyfikator zasobu dla sieci wirtualnej do schowka. Zapisz identyfikator ze schowka w programie OneNote lub w pliku.

1. W menu po lewej stronie wybierz pozycję **Podsieci**.

    - Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla usługi Azure-SSIS IR do użycia. Pozostaw dostępne adresy IP dla co najmniej dwa razy numeru węzła podczerwego. Platforma Azure rezerwuje niektóre adresy IP w każdej podsieci. Tych adresów nie można używać. Pierwszy i ostatni adres IP podsieci są zarezerwowane dla zgodności protokołu, a trzy kolejne adresy są używane dla usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Nie należy wybierać GatewaySubnet do wdrożenia usługi Azure-SSIS IR. Jest przeznaczony dla bram sieci wirtualnej.
    - Nie należy używać podsieci, która jest zajęta wyłącznie przez inne usługi platformy Azure (na przykład wystąpienie zarządzanej bazy danych SQL, usługa App Service i tak dalej).

1. Sprawdź, czy dostawca usługi Azure Batch jest zarejestrowany w subskrypcji platformy Azure, która ma sieć wirtualną. Możesz też zarejestrować dostawcę usługi Azure Batch. Jeśli masz już konto usługi Azure Batch w ramach subskrypcji, twoja subskrypcja jest zarejestrowana dla usługi Azure Batch. (Jeśli utworzysz identyfikator Azure-SSIS IR w portalu usługi Data Factory, dostawca usługi Azure Batch zostanie automatycznie zarejestrowany).

   1. W witrynie Azure portal po lewej stronie menu wybierz pozycję **Subskrypcje**.

   1. Wybierz subskrypcję.

   1. Po lewej stronie wybierz pozycję **Dostawcy zasobów**i upewnij się, że **firma Microsoft.Batch** jest zarejestrowanym dostawcą.

   ![Potwierdzenie statusu "Zarejestrowanego"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli nie widzisz **microsoft.batch** na liście, aby go zarejestrować, [należy utworzyć puste konto usługi Azure Batch](../batch/batch-account-create-portal.md) w ramach subskrypcji. Można go usunąć później.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz do usługi Azure-SSIS IR w sieci wirtualnej

Po skonfigurowaniu sieci wirtualnej usługi Azure Resource Manager lub klasycznej sieci wirtualnej można dołączyć do usługi Azure-SSIS IR do sieci wirtualnej:

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki internetowe obsługują interfejs użytkownika fabryki danych.

1. W [witrynie Azure portal](https://portal.azure.com)po lewej stronie menu wybierz pozycję **Fabryki danych**. Jeśli w menu nie widać **fabryk danych,** wybierz pozycję **Więcej usług**, a następnie w sekcji ANALIZY **+ ANALIZY** wybierz pozycję **Fabryki danych**.

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z azure-SSIS IR na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **Monitor & Autor.** Interfejs użytkownika fabryki danych jest widoczny na osobnej karcie.

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika fabryki danych przełącz się na kartę **Edycja,** wybierz pozycję **Połączenia**i przełącz się na kartę **Środowiska wykonawcze integracji.**

   ![Karta "Środowiska wykonawcze integracji"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli twój identyfikator IR azure-SSIS jest uruchomiony, na liście **Środowiska wykonawcze integracji,** w **akcji** kolumny, wybierz przycisk **Zatrzymaj** dla azure-SSIS IR. Nie można edytować usługi Azure-SSIS IR, dopóki go nie zatrzymasz.

   ![Zatrzymaj podczerwę](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście **Środowiska wykonawcze integracji** w kolumnie **Akcje** wybierz przycisk **Edytuj** dla urządzenia Azure-SSIS IR.

   ![Edytowanie środowiska wykonawczego integracji](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu konfiguracji środowiska wykonawczego integracji przejdź przez sekcje **Ustawienia ogólne** i **Ustawienia SQL,** wybierając przycisk **Dalej.**

1. W sekcji **Ustawienia zaawansowane:**
   1. Wybierz **sieć wirtualną dla środowiska wykonawczego integracji platformy Azure-SSIS do dołączenia, zezwolić podajniku ADF na tworzenie pewnych zasobów sieciowych i opcjonalnie przywieźć własne statyczne publiczne adresy IP.**

   1. W przypadku **subskrypcji**wybierz subskrypcję platformy Azure, która ma sieć wirtualną.

   1. W przypadku **lokalizacji**wybrano tę samą lokalizację środowiska wykonawczego integracji.

   1. W przypadku **opcji Typ**wybierz typ sieci wirtualnej: klasyczny lub Usługę Azure Resource Manager. Zaleca się wybranie sieci wirtualnej usługi Azure Resource Manager, ponieważ klasyczne sieci wirtualne zostaną wkrótce przestarzałe.

   1. W przypadku **nazwy sieci wirtualnej**wybierz nazwę sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym z prywatnym punktem końcowym do hostowania usługi SSISDB. Lub powinien być ten sam, który jest połączony z siecią lokalną. W przeciwnym razie może to być dowolna sieć wirtualna, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

   1. W obszarze **Nazwa podsieci**wybierz nazwę podsieci dla sieci wirtualnej. Powinien być taki sam, który jest używany dla serwera usługi Azure SQL Database z punktami końcowymi usługi sieci wirtualnej do obsługi usługi SSISDB. Lub powinna być inna podsieć niż używana dla wystąpienia zarządzanego z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć, aby przynieść własne statyczne publiczne adresy IP dla usługi Azure-SSIS IR.

   1. Wybierz **pozycję Sprawdzanie poprawności sieci wirtualnej**. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz przycisk **Kontynuuj**.

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. W sekcji **Podsumowanie** przejrzyj wszystkie ustawienia dla usługi Azure-SSIS IR. Następnie wybierz pozycję **Aktualizuj**.

1. Uruchom usługę Azure-SSIS IR, wybierając przycisk **Start** w kolumnie **Akcje** dla usługi Azure-SSIS IR. Trwa około 20 do 30 minut, aby uruchomić azure-SSIS IR, który łączy się z siecią wirtualną.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [dołączaniu usługi Azure-SSIS IR do sieci wirtualnej.](join-azure-ssis-integration-runtime-virtual-network.md)
