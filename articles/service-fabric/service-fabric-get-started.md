---
title: Konfigurowanie środowiska programistycznego systemu Windows
description: Zainstaluj środowisko uruchomieniowe, zestaw SDK i narzędzia oraz utwórz lokalny klaster projektowy. Po ukończeniu tej konfiguracji wszystko będzie gotowe do kompilowania aplikacji w systemie Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 04/20/2020
ms.custom: sfrev
ms.openlocfilehash: 1b43c838537e46ffbaf6c4adcfb117f6718bd046
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731753"
---
# <a name="prepare-your-development-environment-on-windows"></a>Przygotowywanie środowiska deweloperskiego w systemie Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>

Aby kompilować i uruchamiać [aplikacje usługi Azure Service Fabric][1] na maszynie deweloperskiej z systemem Windows, należy zainstalować środowisko uruchomieniowe, zestaw SDK oraz narzędzia usługi Service Fabric. Należy również [włączyć wykonywanie skryptów programu Windows PowerShell](#enable-powershell-script-execution) zawartych w zestawie SDK.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="supported-operating-system-versions"></a>Obsługiwane wersje systemu operacyjnego

Na potrzeby tworzenia aplikacji obsługiwane są następujące wersje systemu operacyjnego:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Obsługa systemu Windows 7:
> - System Windows 7 domyślnie zawiera program Windows PowerShell wyłącznie w wersji 2.0. Polecenia cmdlet programu PowerShell usługi Service Fabric wymagają programu PowerShell w wersji 3.0 lub nowszej. Program [Windows PowerShell 5.1][powershell5-download] można pobrać z Centrum pobierania Microsoft.
> - Serwer proxy usługi Service Fabric nie jest dostępny w systemie Windows 7.

## <a name="install-the-sdk-and-tools"></a>Instalowanie zestawu SDK i narzędzi

Instalator platformy sieci Web (WebPI) jest zalecanym sposobem zainstalowania zestawu SDK i narzędzi. Jeśli zostanie wyświetlonych błędów środowiska uruchomieniowego przy użyciu interfejsu WebPI, można również znaleźć bezpośrednie łącza do instalatorów w informacjach o wersji dla określonej wersji sieci szkieletowej usług. Informacje o wersji można znaleźć w różnych ogłoszeniach o wersjach na [blogu zespołu sieci szkieletowej usług](https://blogs.msdn.microsoft.com/azureservicefabric/).

> [!NOTE]
> Uaktualnienia klastra deweloperów lokalnej sieci szkieletowej nie są obsługiwane.

### <a name="to-use-visual-studio-2017-or-2019"></a>Aby korzystać z programu Visual Studio 2017 lub 2019

Narzędzia sieci szkieletowej usług są częścią obciążenia programu Azure Development w programie Visual Studio 2017 i 2019. Włącz to obciążenie w ramach instalacji programu Visual Studio.
Ponadto należy zainstalować zestaw SDK i środowisko uruchomieniowe usługi Microsoft Azure Service Fabric przy użyciu Instalatora platformy sieci Web.

* [Instalowanie zestawu SDK usługi Microsoft Azure Service Fabric][core-sdk]

### <a name="to-use-visual-studio-2015-requires-visual-studio-2015-update-2-or-later"></a>Używanie programu Visual Studio 2015 (wymaga programu Visual Studio 2015 Update 2 lub nowszego)

W przypadku programu Visual Studio 2015 narzędzia usługi Service Fabric są instalowane razem z zestawem SDK i środowiskiem uruchomieniowym przy użyciu Instalatora platformy sieci Web:

* [Instalowanie zestawu SDK i narzędzi usługi Microsoft Azure Service Fabric][full-bundle-vs2015]

### <a name="sdk-installation-only"></a>Instalowanie samego zestawu SDK

Jeśli potrzebujesz tylko zestawu SDK, możesz zainstalować ten pakiet:

* [Instalowanie zestawu SDK usługi Microsoft Azure Service Fabric][core-sdk]

Bieżące wersje:

* Zestaw SDK i narzędzia sieci szkieletowej usług 4.1.409
* Środowisko uruchomieniowe sieci szkieletowej usług 7.1.409

Aby uzyskać listę obsługiwanych wersji, zobacz [Wersje sieci szkieletowej usług](service-fabric-versions.md)

> [!NOTE]
> Pojedyncze klastry maszyn (OneBox) nie są obsługiwane dla uaktualnień aplikacji lub klastra; usuń klaster OneBox i utwórz go ponownie, jeśli chcesz wykonać uaktualnienie klastra lub masz jakiekolwiek problemy z wykonywaniem uaktualnienia aplikacji. 

## <a name="enable-powershell-script-execution"></a>Włączanie wykonywania skryptów programu PowerShell

Platforma Service Fabric korzysta ze skryptów programu Windows PowerShell do tworzenia lokalnego klastra projektowego i do wdrażania aplikacji z programu Visual Studio. Domyślnie system Windows blokuje uruchamianie tych skryptów. Aby je włączyć, należy zmienić zasady wykonywania w programie PowerShell. Uruchom program PowerShell jako administrator i wprowadź następujące polecenie:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Instalowanie platformy Docker (opcjonalnie)

[Usługa Sieci szkieletowej jest koordynatorem kontenerów](service-fabric-containers-overview.md) do wdrażania mikrousług w klastrze komputerów. Aby uruchomić aplikacje kontenerów systemu Windows w lokalnym klastrze deweloperów, należy najpierw zainstalować platformę Docker dla systemu Windows. Pobierz [docker CE dla Windows (stabilny)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Po zainstalowaniu i uruchomieniu programu Docker kliknij prawym przyciskiem myszy jego ikonę na pasku zadań i wybierz pozycję **Switch to Windows containers** (Przełącz na kontenery systemu Windows). Ten krok jest wymagany do uruchomienia obrazów platformy Docker opartych na systemie Windows.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu środowiska projektowego możesz zacząć kompilować i uruchamiać aplikacje.

* [Dowiedz się, jak tworzyć, wdrażać aplikacje i zarządzać nimi](service-fabric-tutorial-create-dotnet-app.md)
* [Informacje o modelach programowania: Reliable Services i Reliable Actors](service-fabric-choose-framework.md)
* [Przykłady kodu platformy Service Fabric w witrynie GitHub](https://aka.ms/servicefabricsamples)
* [Wizualizowanie klastra przy użyciu usługi Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Strona kampanii usługi Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "VS 2015 WebPI — link"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Dev15 WebPI — link"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Core SDK WebPI — link"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
