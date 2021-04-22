---
title: Azure IoT Edge środowiska projektowego | Microsoft Docs
description: Dowiedz się więcej o obsługiwanych systemach i narzędziach programistyzacyjnych, które pomogą Ci tworzyć IoT Edge modułów
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1070a5ddf298ad88100e7803635e970f9a314e52
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869585"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Przygotowywanie środowiska projektowego i testowego na IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure IoT Edge przenosi istniejącą logikę biznesową na urządzenia działające na brzegu sieci. Aby przygotować aplikacje i obciążenia do uruchamiania jako [IoT Edge modułów](iot-edge-modules.md), należy je skompilować jako kontenery. Ten artykuł zawiera wskazówki dotyczące konfigurowania środowiska projektowego w celu pomyślnego utworzenia IoT Edge dewelopera. Po skonfigurowaniu środowiska projektowego możesz dowiedzieć się, jak opracowywać własne [moduły IoT Edge dewelopera.](module-development.md)

W każdym IoT Edge rozwiązania istnieją co najmniej dwie maszyny, które należy wziąć pod uwagę. Jednym z nich jest IoT Edge (lub urządzenia), które uruchamia IoT Edge moduł. Druga to maszyna dewelopera, która umożliwia tworzenie, testowanie i wdrażanie modułów. Ten artykuł koncentruje się głównie na komputerze dewelopera. W celach testowych te dwie maszyny mogą być takie same. Możesz uruchamiać IoT Edge na komputerze dewelopera i wdrażać na nim moduły.

## <a name="operating-system"></a>System operacyjny

Azure IoT Edge działa w określonym zestawie [obsługiwanych systemów operacyjnych.](support.md) Do tworzenia aplikacji IoT Edge można użyć większości systemów operacyjnych, w których można uruchomić aparat kontenerów. Aparat kontenerów jest wymaganiem na komputerze dewelopera, aby kompilować moduły jako kontenery i wypychać je do rejestru kontenerów.

Jeśli nie można uruchomić maszyny deweloperskie Azure IoT Edge, przejdź do [](#testing-tools) tego artykułu, aby dowiedzieć się więcej o narzędziach do testowania, które ułatwiają lokalne testowanie i debugowanie.

System operacyjny komputera dewelopera nie musi odpowiadać systemowi operacyjneowi twojego IoT Edge dewelopera. Jednak system operacyjny kontenera musi być spójny między maszyną dewelopera a IoT Edge urządzeniem. Można na przykład opracowywać moduły na maszynie z systemem Windows i wdrażać je na urządzeniu z systemem Linux. Maszyna z systemem Windows musi uruchamiać kontenery systemu Linux w celu skompilowania modułów dla urządzenia z systemem Linux.

## <a name="container-engine"></a>Aparat kontenerów

Centralną koncepcją IoT Edge jest zdalne wdrażanie logiki biznesowej i logiki w chmurze na urządzeniach przez pakowanie jej do kontenerów. Do kompilowania kontenerów potrzebny jest aparat kontenerów na komputerze dewelopera.

Jedynym obsługiwanym aparatem kontenerów dla IoT Edge w środowisku produkcyjnym jest moby. Jednak każdy aparat kontenerów zgodny z inicjatywą Open Container Initiative, taki jak Docker, może budować obrazy IoT Edge modułu.

## <a name="development-tools"></a>Narzędzia programistyczne

Obie Visual Studio i Visual Studio Code mają dodatkowe rozszerzenia, które ułatwiają tworzenie IoT Edge rozwiązań. Te rozszerzenia zapewniają szablony specyficzne dla języka, które ułatwiają tworzenie i wdrażanie nowych IoT Edge scenariuszy. Rozszerzenia Azure IoT Edge dla Visual Studio i Visual Studio Code pomagają kodować, kompilować, wdrażać i debugować IoT Edge rozwiązań. Można utworzyć całe rozwiązanie IoT Edge zawierające wiele modułów, a rozszerzenia automatycznie aktualizują szablon manifestu wdrożenia przy każdym dodatku nowego modułu. Za pomocą tych rozszerzeń można również zarządzać urządzeniami IoT z poziomu Visual Studio lub Visual Studio Code. Wdrażanie modułów na urządzeniu, monitorowanie stanu i wyświetlanie komunikatów w momencie ich IoT Hub. Oba rozszerzenia używają narzędzia [deweloperskiego IoT EdgeHub,](#iot-edgehub-dev-tool) aby umożliwić lokalne uruchamianie i debugowanie modułów na komputerze deweloperskim.

Jeśli wolisz programować za pomocą innych edytorów lub interfejsu wiersza polecenia, narzędzie dewelopera Azure IoT Edge udostępnia polecenia umożliwiające tworzenie i testowanie z wiersza polecenia. Możesz tworzyć nowe scenariusze IoT Edge, kompilować obrazy modułów, uruchamiać moduły w symulatorze i monitorować komunikaty wysyłane do IoT Hub.

### <a name="visual-studio-code-extension"></a>Rozszerzenie programu Visual Studio Code

Rozszerzenie Azure IoT Edge dla języka Visual Studio Code udostępnia szablony modułów IoT Edge zbudowane na podstawie języków programowania, takich jak C, C#, Java, Node.js i Python, a także funkcje platformy Azure w języku C#.

Aby uzyskać więcej informacji i pobrać pliki, [zobacz Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Oprócz rozszerzeń IoT Edge przydatne może okazać się zainstalowanie dodatkowych rozszerzeń do instalowania. Na przykład można użyć obsługi platformy [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) dla Visual Studio Code do zarządzania obrazami, kontenerami i rejestrami. Ponadto wszystkie główne obsługiwane języki mają rozszerzenia dla Visual Studio Code które mogą pomóc podczas tworzenia modułów.

#### <a name="prerequisites"></a>Wymagania wstępne

Szablony modułów dla niektórych języków i usług mają wymagania wstępne, które są niezbędne do tworzenia folderów projektu na komputerze dewelopera przy użyciu Visual Studio Code.

| Szablon modułu | Wymaganie wstępne |
| --------------- | ------------ |
| Azure Functions | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| C | [Usługa Git](https://git-scm.com/) |
| C# | [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Ustawianie JAVA_HOME środowiskowej](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Yeoman](https://www.npmjs.com/package/yo) <li> [Azure IoT Edge Node.js generatora modułów](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Usługa Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Visual Studio 2017/2019

Narzędzia Azure IoT Edge dla języka Visual Studio zawierają szablon modułu IoT Edge, który jest zbudowany na językach C# i C.

Aby uzyskać więcej informacji i pobrać, zobacz [Azure IoT Edge Tools for Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) lub [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge dewelopera

Narzędzie Azure IoT Edge dev upraszcza IoT Edge tworzenia aplikacji dzięki możliwościom wiersza polecenia. To narzędzie udostępnia polecenia interfejsu wiersza polecenia do tworzenia, debugowania i testowania modułów. Narzędzie IoT Edge współpracuje z systemem dewelopera, niezależnie od tego, czy zależności na maszynie zostały zainstalowane ręcznie, czy też używasz kontenera dewelopera IoT Edge dewelopera.

Aby uzyskać więcej informacji i rozpocząć pracę, zobacz [IoT Edge wiki narzędzia dewelopera](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Narzędzia do testowania

Istnieje kilka narzędzi do testowania, które ułatwiają symulowanie IoT Edge lub bardziej wydajne debugowanie modułów. W poniższej tabeli przedstawiono wysokiego poziomu porównanie narzędzi, a następnie poszczególne sekcje opisują poszczególne narzędzia bardziej szczegółowo.

Tylko środowisko IoT Edge uruchomieniowe jest obsługiwane w przypadku wdrożeń produkcyjnych, ale następujące narzędzia umożliwiają symulowanie lub łatwe tworzenie IoT Edge na potrzeby testowania i testowania. Te narzędzia nie wykluczają się wzajemnie, ale mogą współpracować ze sobą w celu pełnego tworzenia aplikacji.

| Narzędzie | Znane również jako | Obsługiwane platformy | Optymalne zastosowanie |
| ---- | ------------- | ------------------- | --------- |
| Narzędzie deweloperskie usługi IoT EdgeHub  | iotedgehubdev | Windows, Linux, macOS | Symulowanie urządzenia w celu debugowania modułów. |
| IoT Edge kontenera dewelopera | iotedgedev | Windows, Linux, macOS | Tworzenie bez instalowania zależności. |
| IoT Edge uruchomieniowe w kontenerze | iotedgec | Windows, Linux, macOS, ARM | Testowanie na urządzeniu, które może nie obsługiwać środowiska uruchomieniowego. |
| IoT Edge kontenera urządzeń | toolboc/azure-iot-edge-device-container | Windows, Linux, macOS, ARM | Testowanie scenariusza z wieloma urządzeniami IoT Edge na dużą skalę. |

### <a name="iot-edgehub-dev-tool"></a>Narzędzie deweloperskie usługi IoT EdgeHub

Narzędzie deweloperskie Azure IoT EdgeHub zapewnia lokalne środowisko deweloperskie i debugowania. Narzędzie ułatwia uruchamianie IoT Edge bez środowiska uruchomieniowego IoT Edge, dzięki czemu można tworzyć, opracowywać, testować, uruchamiać i IoT Edge modułów i rozwiązań lokalnie. Nie trzeba wypychać obrazów do rejestru kontenerów i wdrażać ich na urządzeniu w celu testowania.

Narzędzie deweloperskie usługi IoT EdgeHub zostało zaprojektowane do współpracy z rozszerzeniami Visual Studio i Visual Studio Code, a także z IoT Edge deweloperskimi. Obsługuje tworzenie pętli wewnętrznej, a także testowanie pętli zewnętrznej, dlatego integruje się również z narzędziami DevOps.

Aby uzyskać więcej informacji i dowiedzieć się, jak je zainstalować, zobacz Narzędzie deweloperskie [Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edge kontenera dewelopera

Kontener Azure IoT Edge dev to kontener platformy Docker, który zawiera wszystkie zależności potrzebne do IoT Edge tworzenia aplikacji. Ten kontener ułatwia rozpoczynanie pracy z językiem, w którym chcesz programować, w tym C#, Python, Node.js i Java. Wystarczy zainstalować aparat kontenerów, taki jak Docker lub Moby, aby ściągnąć kontener na maszynę dewelopera.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge kontenera dewelopera](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge środowiska uruchomieniowego w kontenerze

Środowisko IoT Edge uruchomieniowe w kontenerze zapewnia kompletne środowisko uruchomieniowe, które pobiera ciąg połączenia urządzenia jako zmienną środowiskową. Ten kontener umożliwia testowanie modułów IoT Edge scenariuszy w systemie, który może nie obsługiwać środowiska uruchomieniowego natywnie, takiego jak macOS. Wszystkie wdrażane moduły zostaną uruchomione poza kontenerem środowiska uruchomieniowego. Jeśli chcesz, aby środowisko uruchomieniowe i wszystkie wdrożone moduły istniały w tym samym kontenerze, rozważ IoT Edge kontenera urządzenia.

Aby uzyskać więcej informacji, zobacz [Running Azure IoT Edge in a container (Uruchamianie Azure IoT Edge w kontenerze).](https://github.com/Azure/iotedgedev/tree/master/docker/runtime)

### <a name="iot-edge-device-container"></a>IoT Edge kontenera urządzenia

Kontener IoT Edge to kompletne urządzenie IoT Edge gotowe do użycia na dowolnej maszynie z aparatem kontenera. Kontener urządzenia zawiera środowisko IoT Edge i sam aparat kontenera. Każde wystąpienie kontenera jest w pełni funkcjonalną funkcją samoobsługowej IoT Edge urządzenia. Kontener urządzeń obsługuje zdalne debugowanie modułów, o ile istnieje trasa sieciowa do modułu. Kontener urządzeń jest dobrym rozwiązaniem do szybkiego tworzenia dużej liczby IoT Edge do testowania scenariuszy na dużą skalę lub Azure Pipelines. Obsługuje ona również wdrażanie na kubernetes za pośrednictwem programu Helm.

Aby uzyskać więcej informacji, [zobacz Azure IoT Edge kontenera urządzeń.](https://github.com/toolboc/azure-iot-edge-device-container)

## <a name="devops-tools"></a>Narzędzia DevOps

Gdy wszystko będzie gotowe do opracowywania rozwiązań na dużą skalę dla rozbudowanych scenariuszy produkcyjnych, skorzystaj z nowoczesnych zasad Metodyki DevOps, takich jak automatyzacja, monitorowanie i usprawnione procesy inżynierii oprogramowania. IoT Edge rozszerzenia do obsługi narzędzi DevOps, w tym Azure DevOps, Azure DevOps Projects i Jenkins. Jeśli chcesz dostosować istniejący potok lub użyć innego narzędzia DevOps, takiego jak CircleCI lub TravisCI, możesz to zrobić za pomocą funkcji interfejsu wiersza polecenia IoT Edge narzędzia deweloperskiego.

Aby uzyskać więcej informacji, wskazówek i przykładów, zobacz następujące strony:

* [Ciągła integracja i ciągłe wdrażanie w Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Tworzenie potoku cicha/cd dla IoT Edge za pomocą Azure DevOps Starter](how-to-devops-starter.md)
* [IoT Edge DevOps w witrynie GitHub](https://github.com/toolboc/IoTEdge-DevOps)