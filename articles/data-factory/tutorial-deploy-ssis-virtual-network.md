---
title: Samouczek dotyczący konfigurowania środowiska Azure-SSIS Integration Runtime w celu dołączenia do sieci wirtualnej
description: Dowiedz się, jak dołączyć do środowiska Azure-SSIS Integration Runtime, aby dołączyć do sieci wirtualnej platformy Azure.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391239"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Skonfiguruj środowisko Integration Runtime (IR) platformy SQL Server Integration Services Azure, aby dołączyć do sieci wirtualnej

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ten samouczek zawiera podstawowe kroki służące do konfigurowania Azure Portal platformy Azure — SQL Server Integration Services (SSIS) Integration Runtime (IR) w celu dołączenia do sieci wirtualnej.

Kroki obejmują:

- Skonfiguruj sieć wirtualną.
- Dołącz Azure-SSIS IR do sieci wirtualnej z portalu usługi Azure Data Factory.

## <a name="prerequisites"></a>Wymagania wstępne

- **Środowisko Azure-SSIS Integration Runtime**. Jeśli nie masz środowiska Azure-SSIS Integration Runtime, [zainicjuj środowisko Azure-SSIS Integration Runtime w Azure Data Factory](tutorial-deploy-ssis-packages-azure.md) przed rozpoczęciem.

- **Uprawnienie użytkownika**. Użytkownik, który tworzy Azure-SSIS IR, musi [mieć co najmniej](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) jeden zasób Azure Data Factory z jedną z poniższych opcji:

    - Użyj wbudowanej roli współautor sieci. Ta rola jest dostarczana z _firmą Microsoft. \* Network/_ uprawnieniem o znacznie większym zakresie niż jest to konieczne.
    - Utwórz rolę niestandardową, która zawiera tylko wymagane uprawnienie _Microsoft. Network/virtualNetworks/ \* /Join/Action_ . Jeśli chcesz również przenieść własne publiczne adresy IP do Azure-SSIS IR podczas dołączania do Azure Resource Manager sieci wirtualnej, w roli należy również uwzględnić uprawnienie _Microsoft. Network/adresów publicipaddress/*/Join/Action_ .

- **Sieć wirtualna**.

    - Jeśli nie masz sieci wirtualnej, [Utwórz sieć wirtualną przy użyciu Azure Portal](../virtual-network/quick-create-portal.md).

    - Upewnij się, że grupa zasobów sieci wirtualnej umożliwia tworzenie i usuwanie niektórych zasobów sieci platformy Azure.
    
        Azure-SSIS IR musi utworzyć określone zasoby sieciowe w ramach tej samej grupy zasobów co sieć wirtualna. Te zasoby obejmują:
        - Moduł równoważenia obciążenia platformy Azure o nazwie *\<Guid> -azurebatch-cloudserviceloadbalancer*
        - Sieciową grupę zabezpieczeń o nazwie * \<Guid> -azurebatch-cloudservicenetworksecuritygroup
        - Publiczny adres IP platformy Azure o nazwie-azurebatch-cloudservicepublicip
    
        Te zasoby zostaną utworzone po rozpoczęciu Azure-SSIS IR. Zostaną one usunięte po zatrzymaniu Azure-SSIS IR. Aby zapobiec zablokowaniu Azure-SSIS IR z zatrzymywania, nie używaj ponownie tych zasobów sieciowych w innych zasobach.

    - Upewnij się, że nie masz [blokady zasobów](../azure-resource-manager/management/lock-resources.md) w grupie zasobów/subskrypcji, do której należy Sieć wirtualna. W przypadku skonfigurowania blokady tylko do odczytu/usuwania, uruchamianie i zatrzymywanie Azure-SSIS IR zakończy się niepowodzeniem lub przestanie odpowiadać.

    - Upewnij się, że nie masz przypisania Azure Policy, które uniemożliwia tworzenie następujących zasobów w grupie zasobów/subskrypcji, do której należy Sieć wirtualna:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

- Poniższe scenariusze **konfiguracji sieci** nie zostały omówione w tym samouczku:
    - Jeśli utworzysz własne publiczne adresy IP dla Azure-SSIS IR.
    - Jeśli używasz własnego serwera DNS (Domain Name System).
    - W przypadku używania sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) w podsieci.
    - Jeśli używasz usługi Azure ExpressRoute lub trasy zdefiniowanej przez użytkownika (UDR).
    - Jeśli używasz niestandardowych Azure-SSIS IR.
    
    Aby uzyskać więcej informacji, sprawdź [konfigurację sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration).

## <a name="configure-a-virtual-network"></a>Konfigurowanie sieci wirtualnej

Użyj Azure Portal, aby skonfigurować sieć wirtualną przed podjęciem próby dołączenia do niej Azure-SSIS IR.

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz pozycję **więcej usług**. Odfiltruj i wybierz **sieci wirtualne**.

1. Odfiltruj i wybierz sieć wirtualną na liście.

1. Na stronie **Sieć wirtualna** wybierz pozycję **Właściwości**.

1. Wybierz przycisk Kopiuj dla **identyfikatora zasobu** , aby skopiować identyfikator zasobu dla sieci wirtualnej do Schowka. Zapisz identyfikator ze schowka w programie OneNote lub pliku.

1. Z menu po lewej stronie wybierz pozycję **podsieci**.

    - Upewnij się, że wybrana podsieć ma wystarczającą ilość dostępnej przestrzeni adresowej dla Azure-SSIS IR do użycia. Pozostaw dostępne adresy IP dla co najmniej dwóch razy numeru węzła IR. Platforma Azure rezerwuje niektóre adresy IP w poszczególnych podsieciach. Nie można użyć tych adresów. Pierwsze i ostatnie adresy IP podsieci są zarezerwowane na potrzeby zgodności protokołów, a dla usług platformy Azure są używane trzy więcej adresów. Aby uzyskać więcej informacji, zobacz [czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Nie wybieraj GatewaySubnet do wdrożenia Azure-SSIS IR. Jest on przeznaczony dla bram sieci wirtualnej.
    - Nie należy używać podsieci, która jest przeznaczona wyłącznie dla innych usług platformy Azure (na przykład SQL Database wystąpienia zarządzane SQL, App Service itd.).

1. Sprawdź, czy dostawca Azure Batch jest zarejestrowany w subskrypcji platformy Azure z siecią wirtualną. Lub Zarejestruj dostawcę Azure Batch. Jeśli masz już konto Azure Batch w subskrypcji, Twoja subskrypcja jest zarejestrowana na potrzeby Azure Batch. (Jeśli utworzysz Azure-SSIS IR w portalu Data Factory, dostawca Azure Batch zostanie automatycznie zarejestrowany.)

   1. W Azure Portal w menu po lewej stronie wybierz pozycję **subskrypcje**.

   1. Wybierz subskrypcję.

   1. Po lewej stronie wybierz pozycję **dostawcy zasobów** i upewnij się, że **Microsoft.Batch** jest zarejestrowanym dostawcą.

   ![Potwierdzenie stanu zarejestrowanego](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Jeśli na liście nie widzisz **Microsoft.Batch** , aby zarejestrować ją, [utwórz puste konto Azure Batch](../batch/batch-account-create-portal.md) w subskrypcji. Można go później usunąć.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Dołącz Azure-SSIS IR do sieci wirtualnej

Po skonfigurowaniu sieci wirtualnej Azure Resource Manager lub klasycznej sieci wirtualnej można przyłączyć Azure-SSIS IR do sieci wirtualnej:

1. Uruchom przeglądarkę Microsoft Edge lub Google Chrome. Obecnie tylko te przeglądarki sieci Web obsługują interfejs użytkownika Data Factory.

1. W [Azure Portal](https://portal.azure.com)w menu po lewej stronie wybierz pozycję **fabryki danych**. Jeśli nie widzisz **fabryk danych** w menu, wybierz pozycję **więcej usług**, a następnie w sekcji **Analiza i analiza** wybierz pozycję **fabryki danych**.

   ![Lista fabryk danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Wybierz fabrykę danych z Azure-SSIS IR na liście. Zostanie wyświetlona strona główna fabryki danych. Wybierz kafelek **tworzenie & monitor** . Na oddzielnej karcie zobaczysz interfejs użytkownika Data Factory.

   ![Strona główna fabryki danych](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. W interfejsie użytkownika Data Factory przejdź do karty **Edycja** , wybierz pozycję **połączenia** i przejdź do karty **środowisko Integration Runtime** .

   ![Karta "Integration Runtimes"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Jeśli Azure-SSIS IR jest uruchomiona, na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Zatrzymaj** dla Azure-SSIS IR. Nie można edytować Azure-SSIS IR, dopóki nie zostanie zatrzymane.

   ![Zatrzymaj środowisko IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na liście **środowiska Integration Runtime** w kolumnie **Akcje** wybierz przycisk **Edytuj** dla Azure-SSIS IR.

   ![Edytuj środowisko Integration Runtime](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. W panelu ustawień środowiska Integration Runtime przejdź do sekcji **Ustawienia ogólne** i **Ustawienia SQL** , wybierając przycisk **dalej** .

1. W sekcji **Ustawienia zaawansowane** :
   1. Wybierz **sieć wirtualną, do której chcesz dołączyć Azure-SSIS Integration Runtime, Zezwól na automatyczny dostęp do zasobów sieciowych, a opcjonalnie zaznacz pole wyboru statyczne publiczne adresy IP** .

   1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, która ma swoją sieć wirtualną.

   1. W polu **Lokalizacja** jest wybierana ta sama lokalizacja środowiska Integration Runtime.

   1. W polu **Typ** wybierz typ sieci wirtualnej: klasyczny lub Azure Resource Manager. Zalecamy wybranie Azure Resource Manager sieci wirtualnej, ponieważ klasyczne sieci wirtualne będą wkrótce przestarzałe.

   1. W polu **Nazwa** sieci wirtualnej wybierz nazwę swojej usługi. Powinien być to ten sam, który służy do SQL Database z punktami końcowymi usługi sieci wirtualnej lub wystąpieniem zarządzanym SQL z prywatnym punktem końcowym do hostowania SSISDB. Lub powinien być taki sam, jak jeden połączony z siecią lokalną. W przeciwnym razie może to być dowolna Sieć wirtualna do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. W polu **Nazwa podsieci** wybierz nazwę podsieci dla sieci wirtualnej. Powinien być taki sam, jak używany dla programu SQL Datbase z punktami końcowymi usługi sieci wirtualnej, aby hostować SSISDB. Lub powinien być inną podsiecią niż używana dla wystąpienia zarządzanego SQL z prywatnym punktem końcowym do hostowania SSISDB. W przeciwnym razie może być dowolna podsieć do przenoszenia własnych statycznych publicznych adresów IP dla Azure-SSIS IR.

   1. Wybierz pozycję **weryfikacja sieci wirtualnej**. Jeśli sprawdzanie poprawności zakończy się pomyślnie, wybierz pozycję **Kontynuuj**.

   ![Ustawienia zaawansowane sieci wirtualnej](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na stronie **Podsumowanie** Przejrzyj wszystkie ustawienia Azure-SSIS IR. Następnie wybierz pozycję **Aktualizuj**.

1. Rozpocznij Azure-SSIS IR, wybierając przycisk **Start** w kolumnie **Akcje** dla Azure-SSIS IR. Uruchomienie Azure-SSIS IR dołączania do sieci wirtualnej trwa od 20 do 30 minut.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat przyłączania Azure-SSIS IR do sieci wirtualnej](join-azure-ssis-integration-runtime-virtual-network.md).