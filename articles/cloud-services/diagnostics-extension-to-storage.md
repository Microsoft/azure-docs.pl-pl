---
title: Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage
description: Dowiedz się, jak zbierać dane diagnostyczne platformy Azure na koncie usługi Azure Storage, aby można było wyświetlać je za pomocą jednego z kilku dostępnych narzędzi.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740078"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Przechowywanie i przeglądanie danych diagnostycznych w usłudze Azure Storage

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Dane diagnostyczne nie są trwale przechowywane, chyba że zostanie przesłane do emulator magazynu Microsoft Azure lub do usługi Azure Storage. Jeden z kilku dostępnych narzędzi można wyświetlić tylko raz w magazynie.

## <a name="specify-a-storage-account"></a>Określ konto magazynu
Należy określić konto magazynu, które ma być używane w pliku ServiceConfiguration. cscfg. Informacje o koncie są definiowane jako parametry połączenia w ustawieniu konfiguracji. W poniższym przykładzie przedstawiono domyślne parametry połączenia utworzone dla nowego projektu usługi w chmurze w programie Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Możesz zmienić te parametry połączenia, aby podać informacje o koncie dla konta usługi Azure Storage.

W zależności od typu zbieranych danych diagnostycznych Diagnostyka Azure używa Blob service lub Table service. W poniższej tabeli przedstawiono źródła danych, które są utrwalane i ich format.

| Źródło danych | Format magazynu |
| --- | --- |
| Dzienniki platformy Azure |Tabela |
| Dzienniki usług IIS 7,0 |Obiekt blob |
| Dzienniki infrastruktury diagnostyki Azure |Tabela |
| Dzienniki śledzenia niepomyślnych żądań |Obiekt blob |
| Dzienniki zdarzeń systemu Windows |Tabela |
| Liczniki wydajności |Tabela |
| Zrzuty awaryjne |Obiekt blob |
| Dzienniki błędów niestandardowych |Obiekt blob |

## <a name="transfer-diagnostic-data"></a>Transfer danych diagnostycznych
W przypadku zestawu SDK 2,5 i nowszych żądanie transferu danych diagnostycznych może odbywać się za pomocą pliku konfiguracji. Dane diagnostyczne można transferować w zaplanowanych odstępach czasu zgodnie z konfiguracją.

W przypadku zestawu SDK 2,4 i wcześniejszego można zażądać transferu danych diagnostycznych za pomocą pliku konfiguracji, a także programowo. Podejście programistyczne umożliwia również przeprowadzanie transferów na żądanie.

> [!IMPORTANT]
> Podczas przesyłania danych diagnostycznych do konta usługi Azure Storage są naliczane koszty zasobów magazynu używanych przez dane diagnostyczne.
> 
> 

## <a name="store-diagnostic-data"></a>Przechowywanie danych diagnostycznych
Dane dziennika są przechowywane w magazynie obiektów blob lub Table z następującymi nazwami:

**Tabele**

* **WadLogsTable** — dzienniki zapisane w kodzie przy użyciu odbiornika śledzenia.
* **WADDiagnosticInfrastructureLogsTable** — monitor diagnostyczny i zmiany konfiguracji.
* **WADDirectoriesTable** — katalogi monitorowane przez Monitor diagnostyczny.  Dotyczy to również dzienników usług IIS, dzienników żądań zakończonych niepowodzeniem i katalogów niestandardowych.  Lokalizacja pliku dziennika obiektów BLOB jest określona w polu kontenera, a nazwa obiektu BLOB znajduje się w polu RelativePath.  Pole AbsolutePath wskazuje lokalizację i nazwę pliku, która istniała na maszynie wirtualnej platformy Azure.
* **WADPerformanceCountersTable** — liczniki wydajności.
* **WADWindowsEventLogsTable** — dzienniki zdarzeń systemu Windows.

**Obiekty blob**

* **funkcji wad-Control-Container** — (tylko w przypadku zestawu SDK 2,4 i poprzedniego) zawiera pliki konfiguracji XML, które sterują diagnostyką platformy Azure.
* **funkcji wad-IIS-failedreqlogfiles** — zawiera informacje z dzienników żądania NIEUDANYCH usług IIS.
* **funkcji wad-IIS-LogFiles** — zawiera informacje o DZIENNIKACH usług IIS.
* **"niestandardowy"** — kontener niestandardowy oparty na konfigurowaniu katalogów monitorowanych przez Monitor diagnostyczny.  Nazwa tego kontenera obiektów BLOB zostanie określona w WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Narzędzia do wyświetlania danych diagnostycznych
Dostępnych jest kilka narzędzi do wyświetlania danych po ich przeniesieniu do magazynu. Na przykład:

* Eksplorator serwera w programie Visual Studio — Jeśli zainstalowano narzędzia platformy Azure dla Microsoft Visual Studio, można użyć węzła usługi Azure Storage w Eksplorator serwera, aby wyświetlić dane obiektów BLOB tylko do odczytu i tabele z kont usługi Azure Storage. Możesz wyświetlić dane z lokalnego konta emulatora magazynu, a także z kont magazynu utworzonych dla platformy Azure. Aby uzyskać więcej informacji, zobacz [przeglądanie zasobów magazynu i zarządzanie nimi za pomocą Eksplorator serwera](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest aplikacją autonomiczną, która umożliwia łatwe współdziałanie z danymi usługi Azure Storage w systemach Windows, OSX i Linux.
* [Usługa Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) obejmuje Menedżera Diagnostyka Azure, który umożliwia wyświetlanie i pobieranie danych diagnostycznych zebranych przez aplikacje działające na platformie Azure oraz zarządzanie nimi.

## <a name="next-steps"></a>Następne kroki
[Śledzenie przepływu w aplikacji Cloud Services z Diagnostyka Azure](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


