---
title: Wysyłanie metryk klasycznej maszyny wirtualnej z systemem Windows do bazy danych metryk Azure Monitor
description: Wysyłanie metryk systemu operacyjnego gościa do magazynu danych Azure Monitor dla maszyny wirtualnej z systemem Windows (wersja klasyczna)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 48c7323a630f5c79c7ecb16e26c9ec79ec2014a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613337"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Wysyłanie metryk systemu operacyjnego gościa do bazy danych metryk Azure Monitor dla maszyny wirtualnej z systemem Windows (wersja klasyczna)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Rozszerzenie diagnostyki](../agents/diagnostics-extension-overview.md) Azure monitor (znane jako "funkcji wad" lub "Diagnostyka") umożliwia zbieranie metryk i dzienników z systemu operacyjnego gościa (system operacyjny gościa) działającego w ramach maszyny wirtualnej, usługi w chmurze lub klastra Service Fabric. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](../platform/data-platform.md?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla maszyny wirtualnej z systemem Windows (klasyczny) do bazy danych metryk Azure Monitor. Począwszy od wersji Diagnostics 1,11, można pisać metryki bezpośrednio do magazynu metryk Azure Monitor, w którym są już zbierane metryki platformy standardowej. 

Przechowywanie ich w tej lokalizacji pozwala uzyskać dostęp do tych samych akcji co w przypadku metryk platformy. Akcje obejmują alerty w czasie prawie rzeczywistym, wykresy, routing, dostęp z interfejsu API REST i inne. W przeszłości rozszerzenie diagnostyki Zapisano do usługi Azure Storage, ale nie do Azure Monitor magazynu danych. 

Proces przedstawiony w tym artykule działa tylko w przypadku klasycznych maszyn wirtualnych z systemem operacyjnym Windows.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być [administratorem usługi lub współadministratorem](../../cost-management-billing/manage/add-change-subscription-administrator.md) w ramach subskrypcji platformy Azure. 

- Twoja subskrypcja musi być zarejestrowana w usłudze [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure) lub [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Zasób maszyny wirtualnej musi znajdować się w [regionie, który obsługuje metryki niestandardowe](../platform/metrics-custom-overview.md#supported-regions).

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Tworzenie klasycznej maszyny wirtualnej i konta magazynu

1. Utwórz klasyczną maszynę wirtualną przy użyciu Azure Portal.
   ![Tworzenie klasycznej maszyny wirtualnej](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. Podczas tworzenia tej maszyny wirtualnej wybierz opcję utworzenia nowego konta magazynu klasycznego. To konto magazynu jest używane w dalszych krokach.

1. W Azure Portal przejdź do bloku zasobów **konta magazynu** . Wybierz pozycję **klucze** i zanotuj nazwę konta magazynu i klucz konta magazynu. Te informacje są potrzebne w dalszych krokach.
   ![Klucze dostępu do magazynu](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Utwórz nazwę główną usługi w dzierżawie Azure Active Directory przy użyciu instrukcji w temacie [Create a Service Principal](../../active-directory/develop/howto-create-service-principal-portal.md). Podczas przechodzenia przez ten proces należy pamiętać o następujących kwestiach: 
- Utwórz nowy klucz tajny klienta dla tej aplikacji.
- Zapisz klucz i identyfikator klienta do użycia w dalszych krokach.

Nadaj tej aplikacji uprawnienia do monitorowania wydawcy metryk do zasobu, dla którego chcesz emitować metryki. Możesz użyć grupy zasobów lub całej subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki używa jednostki usługi do uwierzytelniania względem Azure Monitor i emisji metryk dla klasycznej maszyny wirtualnej.

## <a name="author-diagnostics-extension-configuration"></a>Tworzenie konfiguracji rozszerzenia diagnostyki

1. Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik określa, które dzienniki i liczniki wydajności mają być zbierane przez rozszerzenie diagnostyki dla klasycznej maszyny wirtualnej. Oto przykład:

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
            </PerformanceCounters>
            <WindowsEventLog scheduledTransferPeriod="PT1M">
                <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" />
                <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" />
            </WindowsEventLog>
            <CrashDumps>
                <CrashDumpConfiguration processName="WaIISHost.exe" />
                <CrashDumpConfiguration processName="WaWorkerHost.exe" />
                <CrashDumpConfiguration processName="w3wp.exe" />
            </CrashDumps>
            <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" />
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
        </DiagnosticMonitorConfiguration>
        <SinksConfig>
        </SinksConfig>
        </WadCfg>
        <StorageAccount />
    </PublicConfig>
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <StorageAccount name="" endpoint="" />
    </PrivateConfig>
    <IsEnabled>true</IsEnabled>
    </DiagnosticsConfiguration>
    ```
1. W sekcji "SinksConfig" w pliku diagnostycznym Zdefiniuj nowy obiekt ujścia Azure Monitor w następujący sposób:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. W sekcji pliku konfiguracji, w której znajduje się lista liczników wydajności do zebrania, Roześlij liczniki wydajności do Azure Monitor ujścia "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. W konfiguracji prywatnej Zdefiniuj konto Azure Monitor. Następnie Dodaj informacje o jednostce usługi do użycia w celu emisji metryk.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Zapisz ten plik lokalnie.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdróż rozszerzenie diagnostyki w usłudze w chmurze

1. Uruchom program PowerShell i zaloguj się.

    ```powershell
    Login-AzAccount
    ```

1. Zacznij od ustawienia kontekstu dla klasycznej maszyny wirtualnej.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Ustaw kontekst klasycznego konta magazynu, które zostało utworzone przy użyciu maszyny wirtualnej.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Ustaw ścieżkę pliku diagnostycznego na zmienną za pomocą następującego polecenia:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Przygotuj aktualizację dla klasycznej maszyny wirtualnej z plikiem diagnostycznym, dla którego skonfigurowano obiekt ujścia Azure Monitor.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Wdróż aktualizację na maszynie wirtualnej, uruchamiając następujące polecenie:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Nadal jest wymagane podanie konta magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracyjnym diagnostyki, zostaną zapisane na określonym koncie magazynu.

## <a name="plot-the-metrics-in-the-azure-portal"></a>Wykreśl metryki w Azure Portal

1.  Przejdź do witryny Azure Portal. 

1.  W menu po lewej stronie wybierz pozycję **Monitoruj.**

1.  W bloku **monitor** wybierz pozycję **metryki**.

    ![Nawigowanie po metrykach](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Z menu rozwijanego zasoby Wybierz klasyczną maszynę wirtualną.

1. W menu rozwijanym obszary nazw wybierz pozycję **Azure. VM. Windows. gość**.

1. W menu rozwijanym metryk wybierz pozycję **pamięć \ zadeklarowane bajty w użyciu**.
   ![Pomiary wykresów](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [metrykach niestandardowych](../platform/metrics-custom-overview.md).
