---
title: Jak korzystać z diagnostyki Azure (.NET) z Cloud Services (klasyczny) | Microsoft Docs
description: Używanie diagnostyki Azure do zbierania danych z usług Azure Cloud Services na potrzeby debugowania, mierzenia wydajności, monitorowania, analizy ruchu i nie tylko.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 97e68d338580132b6927c4cc8b206db60fe93ba2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703511"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Włączanie Diagnostyka Azure na platformie Azure Cloud Services (klasyczny)

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Zobacz [Diagnostyka Azure przegląd](../azure-monitor/agents/diagnostics-extension-overview.md) dla tła na Diagnostyka Azure.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Jak włączyć diagnostykę w roli procesu roboczego
W tym przewodniku opisano sposób implementacji roli procesu roboczego platformy Azure, która emituje dane telemetryczne przy użyciu klasy EventSource programu .NET. Diagnostyka Azure służy do zbierania danych telemetrycznych i przechowywania ich na koncie usługi Azure Storage. Podczas tworzenia roli procesu roboczego program Visual Studio automatycznie włącza diagnostykę 1,0 w ramach rozwiązania w zestawach SDK platformy Azure dla platformy .NET 2,4 i starszych. Poniższe instrukcje opisują proces tworzenia roli procesu roboczego, wyłączania diagnostyki 1,0 z rozwiązania oraz wdrażania diagnostyki 1,2 lub 1,3 do roli procesu roboczego.

### <a name="prerequisites"></a>Wymagania wstępne
W tym artykule przyjęto założenie, że masz subskrypcję platformy Azure i korzystasz z programu Visual Studio z zestawem Azure SDK. Jeśli nie masz subskrypcji platformy Azure, możesz utworzyć konto [bezpłatnej wersji próbnej][Free Trial]. Upewnij się [, że instalujesz i konfigurujesz Azure PowerShell w wersji 0.8.7 lub nowszej][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>Krok 1. Tworzenie roli procesu roboczego
1. Uruchom **program Visual Studio**.
2. Utwórz projekt **usługi w chmurze platformy Azure** na podstawie szablonu w **chmurze** , który jest przeznaczony dla .NET Framework 4,5.  Nadaj projektowi nazwę "WadExample" i kliknij przycisk OK.
3. Wybierz **rolę proces roboczy** , a następnie kliknij przycisk OK. Projekt zostanie utworzony.
4. W **Eksplorator rozwiązań** kliknij dwukrotnie plik właściwości **WorkerRole1** .
5. Na karcie **Konfiguracja** Cofnij zaznaczenie pola wyboru **Włącz diagnostykę** , aby wyłączyć diagnostykę 1,0 (zestaw Azure SDK 2,4 lub starszy).
6. Skompiluj rozwiązanie, aby sprawdzić, czy nie ma żadnych błędów.

### <a name="step-2-instrument-your-code"></a>Krok 2. Instrumentacja kodu
Zastąp zawartość rola procesu roboczego. cs poniższym kodem. Klasa SampleEventSourceWriter, dziedziczona z [klasy EventSource][EventSource Class], implementuje cztery metody rejestrowania: **SendEnums**, **MessageMethod**, **setother** i **HighFreq**. Pierwszy parametr metody **metody WriteEvent** definiuje identyfikator dla odpowiedniego zdarzenia. Metoda run implementuje nieskończoną pętlę, która wywołuje każdą metodę rejestrowania zaimplementowaną w klasie **SampleEventSourceWriter** co 10 sekund.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>Krok 3. Wdrażanie roli procesu roboczego

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Wdróż rolę procesu roboczego na platformie Azure z poziomu programu Visual Studio, wybierając projekt **WadExample** w Eksplorator rozwiązań następnie **Opublikuj** w menu **kompilacja** .
2. Wybierz subskrypcję.
3. W oknie dialogowym **Microsoft Azure ustawień publikowania** wybierz pozycję **Utwórz nowy..**..
4. W oknie dialogowym **Tworzenie usługi w chmurze i konta magazynu** wprowadź **nazwę** (na przykład "WadExample") i wybierz region lub grupę koligacji.
5. Ustaw **środowisko** jako **przejściowe**.
6. Zmodyfikuj odpowiednio inne **Ustawienia** , a następnie kliknij przycisk **Publikuj**.
7. Po zakończeniu wdrożenia Sprawdź, czy Azure Portal, że usługa w chmurze jest **uruchomiona** .

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>Krok 4. Tworzenie pliku konfiguracji diagnostyki i Instalowanie rozszerzenia
1. Pobierz definicję schematu pliku konfiguracji publicznej, wykonując następujące polecenie programu PowerShell:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Dodaj plik XML do projektu **WorkerRole1** , klikając prawym przyciskiem myszy projekt **WorkerRole1** i wybierając polecenie **Dodaj**  ->  **nowy element...** -> **Elementy**  ->  Visual C# **Dane**  ->  **Plik XML**. Nazwij plik "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Skojarz plik WadConfig. xsd z plikiem konfiguracji. Upewnij się, że okno edytora WadExample.xml jest oknem aktywnym. Naciśnij klawisz **F4** , aby otworzyć okno **Właściwości** . Kliknij właściwość **schematy** w oknie **Właściwości** . Kliknij przycisk **...** we właściwości **schematy** . Kliknij przycisk **Dodaj...** i przejdź do lokalizacji, w której zapisano plik XSD, a następnie wybierz plik WadConfig. xsd. Kliknij przycisk **OK**.

4. Zastąp zawartość pliku konfiguracji WadExample.xml następującym kodem XML i Zapisz plik. Ten plik konfiguracji definiuje kilka liczników wydajności do zebrania: jeden do użycia procesora CPU i jeden do wykorzystania pamięci. Następnie konfiguracja definiuje cztery zdarzenia odpowiadające metodom w klasie SampleEventSourceWriter.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>Krok 5. Instalowanie diagnostyki w roli procesu roboczego
Polecenia cmdlet programu PowerShell służące do zarządzania diagnostyką w ramach roli sieci Web lub procesu roboczego są następujące: Set-AzureServiceDiagnosticsExtension, get-AzureServiceDiagnosticsExtension i Remove-AzureServiceDiagnosticsExtension.

1. Otwórz program Azure PowerShell.
2. Wykonaj skrypt, aby zainstalować diagnostykę w roli procesu roboczego (Zastąp *StorageAccountKey* kluczem konta magazynu dla konta magazynu wadexample i *config_path* ze ścieżką do pliku *WadExample.xml* ):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>Krok 6. Przyjrzyj się danych telemetrii
W programie Visual Studio **Eksplorator serwera** przejdź do konta magazynu wadexample. Po uruchomieniu usługi w chmurze około pięciu (5) minut powinna zostać wyświetlona tabela **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** i **WADSetOtherTable**. Kliknij dwukrotnie jedną z tabel, aby wyświetlić dane telemetryczne, które zostały zebrane.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Schemat pliku konfiguracji
Plik konfiguracji diagnostyki definiuje wartości, które są używane do inicjowania ustawień konfiguracji diagnostyki podczas uruchamiania agenta diagnostyki. Zobacz [najnowsze informacje o schemacie](../azure-monitor/agents/diagnostics-extension-versions.md) dotyczące prawidłowych wartości i przykładów.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli masz problemy, zobacz [Rozwiązywanie problemów Diagnostyka Azure](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) , aby uzyskać pomoc dotyczącą typowych problemów.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [listą artykułów diagnostycznych dotyczących maszyn wirtualnych platformy Azure](../azure-monitor/agents/diagnostics-extension-overview.md) , aby zmienić zbierane dane, rozwiązywać problemy lub dowiedzieć się więcej na temat diagnostyki.

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/