---
title: Instalowanie i konfigurowanie rozszerzenia Diagnostyka Windows Azure (WAD)
description: Dowiedz się więcej o instalowaniu i konfigurowaniu rozszerzenia diagnostyki systemu Windows. Dowiedz się również, w jaki sposób opis sposobu przechowywania danych w usługach i na koncie usługi Azure Storage.
services: azure-monitor
author: bwren
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3ff752b673c49047551c48c4c8693b00d7b5edeb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787409"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>Instalowanie i konfigurowanie rozszerzenia Diagnostyka Windows Azure (WAD)
[Rozszerzenie diagnostyki platformy Azure](diagnostics-extension-overview.md) to agent w Azure Monitor, który zbiera dane monitorowania z systemu operacyjnego gościa i obciążeń maszyn wirtualnych platformy Azure i innych zasobów obliczeniowych. Ten artykuł zawiera szczegółowe informacje na temat instalowania i konfigurowania rozszerzenia diagnostyki systemu Windows oraz opis sposobu przechowywania danych na koncie usługi Azure Storage.

Rozszerzenie diagnostyki jest [](../../virtual-machines/extensions/overview.md) implementowane jako rozszerzenie maszyny wirtualnej na platformie Azure, dlatego obsługuje te same opcje instalacji przy użyciu szablonów Resource Manager, programu PowerShell i interfejsu wiersza polecenia. Zobacz [Rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows,](../../virtual-machines/extensions/features-windows.md) aby uzyskać szczegółowe informacje na temat instalowania i obsługi rozszerzeń maszyny wirtualnej.

## <a name="overview"></a>Omówienie
Podczas konfigurowania rozszerzenia diagnostyki platformy Microsoft Azure należy określić konto magazynu, do którego będą wysyłane wszystkie określone dane. Opcjonalnie możesz dodać co najmniej jedno *ujścia danych,* aby wysłać dane do różnych lokalizacji.

- Azure Monitor ujścia — wysyła dane wydajności gościa do Azure Monitor metryk.
- Ujście centrum zdarzeń — wysyła dane dotyczące wydajności gościa i dzienników do usługi Azure Event Hubs do przekazywania dalej poza platformę Azure. Tego ujścia nie można skonfigurować w Azure Portal.


## <a name="install-with-azure-portal"></a>Instalowanie przy użyciu Azure Portal
Rozszerzenie diagnostyki można zainstalować i skonfigurować na poszczególnych maszynach wirtualnych w programie Azure Portal udostępnia interfejs, w przeciwieństwie do pracy bezpośrednio z konfiguracją. Po włączeniu rozszerzenia diagnostyki automatycznie użyje ono konfiguracji domyślnej z najczęściej występującymi licznikami wydajności i zdarzeniami. Tę konfigurację domyślną można zmodyfikować zgodnie z określonymi wymaganiami.

> [!NOTE]
> Poniżej opisano najbardziej typowe ustawienia rozszerzenia diagnostyki. Aby uzyskać szczegółowe informacje na temat wszystkich opcji konfiguracji, zobacz [Schemat rozszerzenia diagnostyki systemu Windows](diagnostics-extension-schema-windows.md).

1. Otwórz menu maszyny wirtualnej w Azure Portal.

2. Kliknij pozycję **Ustawienia diagnostyczne** w **sekcji Monitorowanie** menu maszyny wirtualnej.

3. Kliknij **pozycję Włącz monitorowanie na poziomie gościa,** jeśli rozszerzenie diagnostyki nie zostało jeszcze włączone.

   ![Włączanie monitorowania](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. Dla maszyny wirtualnej zostanie utworzone nowe konto usługi Azure Storage o nazwie opartej na nazwie grupy zasobów maszyny wirtualnej, a następnie zostanie wybrany domyślny zestaw liczników wydajności gościa i dzienników.

   ![Ustawienia diagnostyczne](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. Na karcie **Liczniki wydajności** wybierz metryki gościa, które mają być zbierane z tej maszyny wirtualnej. Użyj ustawienia **Niestandardowe,** aby uzyskać bardziej zaawansowane opcje wyboru.

   ![Liczniki wydajności](media/diagnostics-extension-windows-install/performance-counters.png)

6. Na karcie **Dzienniki** wybierz dzienniki do zebrania z maszyny wirtualnej. Dzienniki mogą być wysyłane do magazynu lub centrów zdarzeń, ale nie do Azure Monitor. Użyj agenta [usługi Log Analytics do](../agents/log-analytics-agent.md) zbierania dzienników gościa w Azure Monitor.

   ![Zrzut ekranu przedstawia kartę Dzienniki z różnymi dziennikami wybranymi dla maszyny wirtualnej.](media/diagnostics-extension-windows-install/logs.png)

7. Na karcie **Zrzuty awaryjne** określ wszystkie procesy do zbierania zrzutów pamięci po awarii. Dane zostaną zapisane na koncie magazynu w celu ustawienia diagnostycznego i opcjonalnie można określić kontener obiektów blob.

   ![Zrzuty awaryjne](media/diagnostics-extension-windows-install/crash-dumps.png)

8. Na karcie **Ujścia** określ, czy dane mają być wysyłane do lokalizacji innych niż usługa Azure Storage. W przypadku wybrania **Azure Monitor** dane wydajności gościa będą wysyłane do Azure Monitor Metryki. Ujścia usługi Event Hubs nie można skonfigurować przy użyciu Azure Portal.

   ![Zrzut ekranu przedstawiający kartę Ujścia z opcją Wyślij dane diagnostyczne Azure Monitor włączone.](media/diagnostics-extension-windows-install/sinks.png)
   
   Jeśli nie włączono tożsamości przypisanej przez system skonfigurowanej dla maszyny wirtualnej, podczas zapisywania konfiguracji z Azure Monitor ujściem może zostać Azure Monitor ostrzeżenie. Kliknij transparent, aby włączyć tożsamość przypisaną przez system.
   
   ![Jednostka zarządzana](media/diagnostics-extension-windows-install/managed-entity.png)

9. W **programie Agent** można zmienić konto magazynu, ustawić limit przydziału dysku i określić, czy dzienniki infrastruktury diagnostycznej mają być zbierane.  

   ![Zrzut ekranu przedstawia kartę Agent z opcją ustawienia konta magazynu.](media/diagnostics-extension-windows-install/agent.png)

10. Kliknij **przycisk Zapisz,** aby zapisać konfigurację. 

> [!NOTE]
> Konfigurację rozszerzenia diagnostyki można sformatować w formacie JSON lub XML, jednak każda konfiguracja Azure Portal zawsze będzie przechowywana w formacie JSON. Jeśli używasz kodu XML z inną metodą konfiguracji, a następnie zmieniasz konfigurację za pomocą Azure Portal, ustawienia zostaną zmienione na JSON.

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager
Zobacz Temat Use monitoring and diagnostics with a Windows VM (Używanie monitorowania i diagnostyki z maszyną [wirtualną z systemem Windows) oraz Azure Resource Manager szablonów](../../virtual-machines/extensions/diagnostics-template.md) wdrażania rozszerzenia diagnostyki przy Azure Resource Manager szablonów. 

## <a name="azure-cli-deployment"></a>Wdrażanie interfejsu wiersza polecenia platformy Azure
Interfejs wiersza polecenia platformy Azure może służyć do wdrażania rozszerzenia Diagnostyka Azure istniejącej maszyny wirtualnej przy użyciu polecenia [az vm extension set,](/cli/azure/vm/extension#az_vm_extension_set) jak w poniższym przykładzie. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

Chronione ustawienia są zdefiniowane w [elemencie PrivateConfig](diagnostics-extension-schema-windows.md#privateconfig-element) schematu konfiguracji. Poniżej przedstawiono minimalny przykład pliku ustawień chronionych, który definiuje konto magazynu. Zobacz [Przykładowa konfiguracja,](diagnostics-extension-schema-windows.md#privateconfig-element) aby uzyskać szczegółowe informacje o ustawieniach prywatnych.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

Ustawienia publiczne są zdefiniowane w [elemencie Public](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Poniżej przedstawiono minimalny przykład pliku ustawień publicznych, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i pojedynczy dziennik zdarzeń. Zobacz [Przykładowa konfiguracja,](diagnostics-extension-schema-windows.md#publicconfig-element) aby uzyskać szczegółowe informacje o ustawieniach publicznych.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>Wdrażanie przy użyciu programu PowerShell
Program PowerShell może służyć do wdrażania rozszerzenia Diagnostyka Azure istniejącej maszyny wirtualnej przy użyciu [polecenia Set-AzVMDiagnosticsExtension,](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension) jak w poniższym przykładzie. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

Ustawienia prywatne są definiowane w [elemencie PrivateConfig,](diagnostics-extension-schema-windows.md#privateconfig-element)a ustawienia publiczne są zdefiniowane w [elemencie Public](diagnostics-extension-schema-windows.md#publicconfig-element) schematu konfiguracji. Możesz również określić szczegóły konta magazynu jako parametry polecenia cmdlet Set-AzVMDiagnosticsExtension zamiast uwzględniać je w ustawieniach prywatnych.

Poniżej przedstawiono minimalny przykład pliku konfiguracji, który umożliwia zbieranie dzienników infrastruktury diagnostycznej, pojedynczy licznik wydajności i pojedynczy dziennik zdarzeń. Zobacz [Przykładowa konfiguracja,](diagnostics-extension-schema-windows.md#publicconfig-element) aby uzyskać szczegółowe informacje o ustawieniach prywatnych i publicznych. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

Zobacz też [włączanie obsługi programu PowerShell Diagnostyka Azure maszynie wirtualnej z systemem Windows.](../../virtual-machines/extensions/diagnostics-windows.md)

## <a name="data-storage"></a>Magazyn danych
W poniższej tabeli wymieniono różne typy danych zebranych z rozszerzenia diagnostyki oraz to, czy są one przechowywane jako tabela, czy obiekt blob. Dane przechowywane w tabelach mogą być również przechowywane w obiektach blob w zależności od ustawienia [StorageType](diagnostics-extension-schema-windows.md#publicconfig-element) w konfiguracji publicznej.


| Dane | Typ magazynu | Opis |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | Tabela | Monitor diagnostyczny i zmiany konfiguracji. |
| WADDirectoriesTable | Tabela | Katalogi monitorowane przez monitor diagnostyczny.  Obejmuje to dzienniki usług IIS, dzienniki żądań usług IIS, które zakończyły się niepowodzeniem, oraz katalogi niestandardowe.  Lokalizacja pliku dziennika obiektów blob jest określona w polu Kontener, a nazwa obiektu blob znajduje się w polu RelativePath.  Pole AbsolutePath wskazuje lokalizację i nazwę pliku, który istniał na maszynie wirtualnej platformy Azure. |
| WadLogsTable | Tabela | Dzienniki zapisane w kodzie przy użyciu odbiornika śledzenia. |
| WADPerformanceCountersTable | Tabela | Liczniki wydajności. |
| WADWindowsEventLogsTable | Tabela | Dzienniki zdarzeń systemu Windows. |
| wad-iis-failedreqlogfiles | Obiekt blob | Zawiera informacje z dzienników żądań nieudanych usług IIS. |
| wad-iis-logfiles | Obiekt blob | Zawiera informacje o dziennikach usług IIS. |
| "niestandardowy" | Obiekt blob | Kontener niestandardowy oparty na konfigurowaniu katalogów monitorowanych przez monitor diagnostyczny.  Nazwa tego kontenera obiektów blob zostanie określona w tabeli WADDirectoriesTable. |

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Dostępnych jest kilka narzędzi do wyświetlania danych po ich przeniesieniu do magazynu. Na przykład:

* Eksplorator serwera usłudze Visual Studio — jeśli zainstalowano narzędzia platformy Azure dla programu Microsoft Visual Studio, możesz użyć węzła usługi Azure Storage w usłudze Eksplorator serwera, aby wyświetlić dane obiektów blob i tabel tylko do odczytu z kont usługi Azure Storage. Dane można wyświetlać z lokalnego konta emulatora magazynu, a także z kont magazynu utworzonych dla platformy Azure. Aby uzyskać więcej informacji, zobacz Przeglądanie zasobów magazynu i [zarządzanie nimi za pomocą Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Eksplorator usługi Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) to autonomiczna aplikacja, która umożliwia łatwe korzystanie z danych usługi Azure Storage w systemach Windows, OSX i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) zawiera usługę Diagnostyka Azure Manager, która umożliwia wyświetlanie i pobieranie danych diagnostycznych zbieranych przez aplikacje działające na platformie Azure oraz zarządzanie nimi.

## <a name="next-steps"></a>Następne kroki
- Zobacz [Wysyłanie danych z rozszerzenia diagnostyki platformy Windows Azure](diagnostics-extension-stream-event-hubs.md) do usługi Event Hubs, aby uzyskać szczegółowe informacje na temat przekazywania danych monitorowania do usługi Azure Event Hubs.
