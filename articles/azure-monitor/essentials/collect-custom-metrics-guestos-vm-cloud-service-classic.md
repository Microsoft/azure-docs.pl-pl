---
title: Wysyłanie metryk Cloud Services klasycznych do bazy danych metryk Azure Monitor
description: Opisuje proces wysyłania metryk wydajności systemu operacyjnego gościa dla klasycznej Cloud Services platformy Azure do magazynu metryk Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 971a3063ff86e2a6b7d1b11f72ff0a257f459da0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613287"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Wysyłaj metryki systemu operacyjnego gościa do magazynu Azure Monitor metryk klasyczny Cloud Services 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dzięki [rozszerzeniu diagnostyki](../agents/diagnostics-extension-overview.md)Azure monitor można zbierać metryki i dzienniki z systemu operacyjnego gościa (systemu operacyjnego gościa) działającego w ramach maszyny wirtualnej, usługi w chmurze lub klastra Service Fabric. Rozszerzenie może wysyłać dane telemetryczne do [wielu różnych lokalizacji.](../platform/data-platform.md?toc=/azure/azure-monitor/toc.json)

W tym artykule opisano proces wysyłania metryk wydajności systemu operacyjnego gościa dla klasycznej Cloud Services platformy Azure do magazynu metryk Azure Monitor. Począwszy od wersji Diagnostics 1,11, można pisać metryki bezpośrednio do magazynu metryk Azure Monitor, w którym są już zbierane metryki platformy standardowej. 

Przechowywanie ich w tej lokalizacji pozwala uzyskać dostęp do tych samych akcji, które są dostępne dla metryk platformy. Akcje obejmują alerty w czasie prawie rzeczywistym, wykresy, routing, dostęp z interfejsu API REST i inne.  W przeszłości rozszerzenie diagnostyki Zapisano do usługi Azure Storage, ale nie do Azure Monitor magazynu danych.  

Proces przedstawiony w tym artykule działa tylko w przypadku liczników wydajności w usłudze Azure Cloud Services. Nie działa on w przypadku innych metryk niestandardowych. 

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz być [administratorem usługi lub współadministratorem](../../cost-management-billing/manage/add-change-subscription-administrator.md) w ramach subskrypcji platformy Azure. 

- Twoja subskrypcja musi być zarejestrowana w usłudze [Microsoft. Insights](../../azure-resource-manager/management/resource-providers-and-types.md). 

- Musisz mieć zainstalowaną [Azure PowerShell](/powershell/azure) lub [Azure Cloud Shell](../../cloud-shell/overview.md) .

- Usługa w chmurze musi znajdować się w [regionie, który obsługuje metryki niestandardowe](../platform/metrics-custom-overview.md#supported-regions).

## <a name="provision-a-cloud-service-and-storage-account"></a>Inicjowanie obsługi administracyjnej usługi w chmurze i konta magazynu 

1. Tworzenie i wdrażanie klasycznej usługi w chmurze. Przykład klasycznej aplikacji Cloud Services i wdrożenia można znaleźć w witrynie [Rozpoczynanie pracy z usługą Azure Cloud Services i ASP.NET](../../cloud-services/cloud-services-dotnet-get-started.md). 

2. Możesz użyć istniejącego konta magazynu lub wdrożyć nowe konto magazynu. Najlepiej, jeśli konto magazynu znajduje się w tym samym regionie co utworzona przez Ciebie klasyczną usługę w chmurze. W Azure Portal przejdź do bloku zasobów **konta magazynu** , a następnie wybierz pozycję **klucze**. Zanotuj nazwę konta magazynu i klucz konta magazynu. Te informacje będą potrzebne w późniejszych krokach.

   ![Klucze kont magazynu](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi 

Utwórz nazwę główną usługi w dzierżawie Azure Active Directory przy użyciu instrukcji w temacie [use Portal, aby utworzyć aplikację Azure Active Directory i nazwę główną usługi, która może uzyskiwać dostęp do zasobów](../../active-directory/develop/howto-create-service-principal-portal.md). Podczas przechodzenia przez ten proces należy pamiętać o następujących kwestiach: 

- Adres URL logowania można umieścić w dowolnym adresie URL.  
- Utwórz nowy klucz tajny klienta dla tej aplikacji.  
- Zapisz klucz i identyfikator klienta do użycia w dalszych krokach.  

Nadaj aplikacji utworzoną w poprzednim kroku uprawnienia do *monitorowania metryki* dla zasobu, dla którego chcesz emitować metryki. Jeśli planujesz używać aplikacji do emitowania metryk niestandardowych względem wielu zasobów, możesz przyznać te uprawnienia na poziomie grupy zasobów lub subskrypcji.  

> [!NOTE]
> Rozszerzenie diagnostyki używa jednostki usługi do uwierzytelniania w oparciu o Azure Monitor i emituje metryki dla usługi w chmurze.

## <a name="author-diagnostics-extension-configuration"></a>Tworzenie konfiguracji rozszerzenia diagnostyki 

Przygotuj plik konfiguracji rozszerzenia diagnostyki. Ten plik określa, które dzienniki i liczniki wydajności mają być zbierane przez rozszerzenie diagnostyki dla usługi w chmurze. Poniżej znajduje się przykładowy plik konfiguracji diagnostyki:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
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

W sekcji "SinksConfig" w pliku diagnostycznym Zdefiniuj nowy Azure Monitor ujścia: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

W sekcji pliku konfiguracji, w której znajduje się lista liczników wydajności do zebrania, Dodaj ujścia Azure Monitor. Ten wpis zapewnia, że wszystkie określone liczniki wydajności są kierowane do Azure Monitor jako metryki. Możesz dodawać lub usuwać liczniki wydajności zgodnie z potrzebami. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Na koniec w konfiguracji prywatnej Dodaj sekcję *konto Azure monitor* . Wprowadź identyfikator klienta i klucz tajny jednostki usługi, który został utworzony wcześniej. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Zapisz ten plik diagnostyczny lokalnie.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>Wdróż rozszerzenie diagnostyki w usłudze w chmurze 

Uruchom program PowerShell i zaloguj się na platformie Azure. 

```powershell
Login-AzAccount 
```

Użyj następujących poleceń, aby zapisać szczegóły utworzonego wcześniej konta magazynu. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Analogicznie Ustaw ścieżkę pliku diagnostycznego na zmienną za pomocą następującego polecenia:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Wdróż rozszerzenie diagnostyki w usłudze w chmurze przy użyciu pliku diagnostycznego z obiektem sink Azure Monitor skonfigurowanym przy użyciu następującego polecenia:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Nadal konieczne jest podanie konta magazynu w ramach instalacji rozszerzenia diagnostyki. Wszystkie dzienniki lub liczniki wydajności, które są określone w pliku konfiguracyjnym diagnostyki, są zapisywane na określonym koncie magazynu.  

## <a name="plot-metrics-in-the-azure-portal"></a>Wykresy w Azure Portal 

1. Przejdź do witryny Azure Portal. 

   ![Zrzut ekranu przedstawia Azure Portal monitor, a następnie wybrane metryki.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. W menu po lewej stronie wybierz pozycję **Monitoruj.**

3. W bloku **monitor** wybierz kartę **Podgląd metryk** .

4. Z menu rozwijanego zasoby wybierz swoją klasyczną usługę w chmurze.

5. W menu rozwijanym obszary nazw wybierz pozycję **Azure. VM. Windows. gość**. 

6. W menu rozwijanym metryk wybierz pozycję **pamięć \ zadeklarowane bajty w użyciu**. 

Możesz użyć funkcji filtrowania wymiarów i dzielenia, aby wyświetlić łączną ilość pamięci używanej przez określoną rolę lub wystąpienie roli. 

 ![Zrzut ekranu przedstawia dane metryk.](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [metrykach niestandardowych](../platform/metrics-custom-overview.md).
