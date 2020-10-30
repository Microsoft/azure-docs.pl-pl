---
title: Azure IoT Edge środowisko programistyczne | Microsoft Docs
description: Poznaj obsługiwane systemy i narzędzia deweloperskie pierwszej firmy, które ułatwią tworzenie modułów IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4064acd6d2857ebc00a38ea7f6ff5d5e30bbd699
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041376"
---
# <a name="prepare-your-development-and-test-environment-for-iot-edge"></a>Przygotuj środowisko deweloperskie i testowe dla IoT Edge

Azure IoT Edge przenosi istniejącą logikę biznesową na urządzenia działające na brzegu. Aby przygotować aplikacje i obciążenia do uruchamiania jako [moduły IoT Edge](iot-edge-modules.md), należy je skompilować jako kontenery. Ten artykuł zawiera wskazówki dotyczące konfigurowania środowiska programistycznego, aby można było pomyślnie utworzyć rozwiązanie IoT Edge. Po skonfigurowaniu środowiska programistycznego można dowiedzieć się, jak [opracowywać własne moduły IoT Edge](module-development.md).

W przypadku rozwiązań IoT Edge istnieje co najmniej dwie komputery, które należy wziąć pod uwagę. Jednym z nich jest urządzenie IoT Edge (lub urządzenia), które uruchamia moduł IoT Edge. Druga to komputer deweloperski, który służy do kompilowania, testowania i wdrażania modułów. Ten artykuł koncentruje się głównie na komputerze deweloperskim. Na potrzeby testowania te dwie maszyny mogą być takie same. Można uruchomić IoT Edge na komputerze deweloperskim i wdrożyć w nim moduły.

## <a name="operating-system"></a>System operacyjny

Azure IoT Edge działa w określonym zestawie [obsługiwanych systemów operacyjnych](support.md). W przypadku programowania dla IoT Edge można użyć większości systemów operacyjnych, które mogą uruchamiać aparat kontenerów. Aparat kontenerów jest wymaganiem na maszynie deweloperskiej, aby kompilować moduły jako kontenery i wypchnąć je do rejestru kontenerów.

Jeśli na komputerze deweloperskim nie można uruchomić Azure IoT Edge, Kontynuuj w tym artykule, aby dowiedzieć się więcej o [narzędziach do testowania](#testing-tools) , które ułatwiają testowanie i debugowanie lokalnie.

System operacyjny maszyny deweloperskiej nie musi być zgodny z systemem operacyjnym urządzenia IoT Edge. Jednak system operacyjny kontenera musi być spójny między maszyną deweloperskią a urządzeniem IoT Edge. Na przykład można opracowywać moduły na komputerze z systemem Windows i wdrażać je na urządzeniu z systemem Linux. Maszyna z systemem Windows musi uruchamiać kontenery systemu Linux w celu skompilowania modułów dla urządzenia z systemem Linux.

## <a name="container-engine"></a>Aparat kontenera

Centralna koncepcja IoT Edge polega na tym, że można zdalnie wdrożyć logikę biznesową i chmurową na urządzeniach przez spakowanie jej w kontenery. Do kompilowania kontenerów potrzebny jest aparat kontenerów na komputerze deweloperskim.

Jedyne Obsługiwane aparaty kontenerów dla urządzeń IoT Edge w środowisku produkcyjnym to Moby. Jednak wszelkie aparaty kontenerów zgodne z inicjatywą Open Container, takie jak Docker, mogą tworzyć obrazy modułu IoT Edge.

## <a name="development-tools"></a>Narzędzia programistyczne

Zarówno program Visual Studio, jak i Visual Studio Code mają rozszerzenia dodatków ułatwiające tworzenie rozwiązań IoT Edge. Te rozszerzenia zapewniają szablony specyficzne dla języka, które ułatwiają tworzenie i wdrażanie nowych scenariuszy IoT Edge. Rozszerzenia Azure IoT Edge dla programu Visual Studio i Visual Studio Code pomagają w kodzie, tworzeniu, wdrażaniu i debugowaniu rozwiązań IoT Edge. Można utworzyć całe IoT Edge rozwiązanie, które zawiera wiele modułów, a rozszerzenia automatycznie aktualizują szablon manifestu wdrożenia przy użyciu każdego nowego modułu. Dzięki rozszerzeniom można także zarządzać urządzeniami IoT z poziomu programu Visual Studio lub Visual Studio Code. Wdrażaj moduły na urządzeniu, monitoruj stan i wyświetlaj komunikaty w miarę ich przybycia do IoT Hub. Oba rozszerzenia używają [narzędzia deweloperskiego IoT EdgeHub](#iot-edgehub-dev-tool) , aby umożliwić również lokalne uruchamianie i debugowanie modułów na komputerze deweloperskim.

Jeśli wolisz opracowywać przy użyciu innych edytorów lub interfejsu wiersza polecenia, narzędzie Azure IoT Edge dev udostępnia polecenia, aby można było opracowywać i testować z wiersza poleceń. Można tworzyć nowe scenariusze IoT Edge, tworzyć obrazy modułów, uruchamiać moduły w symulatorze i monitorować komunikaty wysyłane do IoT Hub.

### <a name="visual-studio-code-extension"></a>Rozszerzenie programu Visual Studio Code

Rozszerzenie Azure IoT Edge dla Visual Studio Code udostępnia szablony modułów IoT Edge opartych na językach programowania, takich jak C, C#, Java, Node.js i Python, a także usługi Azure Functions w języku C#.

Aby uzyskać więcej informacji i pobrać, zobacz [narzędzia Azure IoT Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Oprócz rozszerzeń IoT Edge może być przydatne zainstalowanie dodatkowych rozszerzeń na potrzeby programowania. Na przykład możesz użyć [obsługi platformy Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) , aby Visual Studio Code do zarządzania obrazami, kontenerami i rejestrami. Ponadto wszystkie główne obsługiwane języki mają rozszerzenia dla Visual Studio Code, które mogą pomóc podczas opracowywania modułów.

#### <a name="prerequisites"></a>Wymagania wstępne

Szablony modułów dla niektórych języków i usług mają wymagania wstępne, które są niezbędne do skompilowania folderów projektu na komputerze deweloperskim przy użyciu Visual Studio Code.

| Szablon modułu | Wymaganie wstępne |
| --------------- | ------------ |
| Azure Functions | [Zestaw SDK platformy .NET Core 2,1](https://www.microsoft.com/net/download) |
| C | [Git](https://git-scm.com/) |
| C# | [Zestaw SDK platformy .NET Core 2,1](https://www.microsoft.com/net/download) |
| Java | <ul><li>[Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) <li> [Ustaw zmienną środowiskową JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) <li> [Maven](https://maven.apache.org/)</ul> |
| Node.js | <ul><li>[Node.js](https://nodejs.org/) <li> [Narzędzia Yeoman](https://www.npmjs.com/package/yo) <li> [Generator modułów Node.js Azure IoT Edge](https://www.npmjs.com/package/generator-azure-iot-edge-module)</ul> |
| Język Python |<ul><li> [Python](https://www.python.org/downloads/) <li> [Pip](https://pip.pypa.io/en/stable/installing/#installation) <li> [Git](https://git-scm.com/) </ul> |

### <a name="visual-studio-20172019-extension"></a>Rozszerzenie programu Visual Studio 2017/2019

Narzędzia Azure IoT Edge Tools for Visual Studio oferują szablon modułu IoT Edge oparty na języku C# i C.

Aby uzyskać więcej informacji i pobrać, zobacz [Azure IoT Edge Tools for Visual studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) lub [Azure IoT Edge Tools for Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools).

### <a name="iot-edge-dev-tool"></a>IoT Edge narzędzia deweloperskiego

Azure IoT Edge narzędzia deweloperskiego upraszcza programowanie IoT Edge przy użyciu możliwości wiersza polecenia. To narzędzie dostarcza poleceń interfejsu wiersza polecenia do tworzenia, debugowania i testowania modułów. IoT Edge narzędzia deweloperskiego współpracuje z systemem programistycznym, niezależnie od tego, czy zostały zainstalowane ręcznie zależności na komputerze, czy za pomocą IoT Edge kontenera dev.

Aby uzyskać więcej informacji i rozpocząć pracę, zobacz [IoT Edge wiki narzędzia deweloperskiego](https://github.com/Azure/iotedgedev/wiki).

## <a name="testing-tools"></a>Narzędzia do testowania

Istnieje kilka narzędzi do testowania, które ułatwiają bardziej wydajne symulowanie IoT Edge urządzeń lub modułów debugowania. W poniższej tabeli przedstawiono porównanie wysokiego poziomu między narzędziami, a następnie poszczególne sekcje opisują poszczególne narzędzia bardziej szczegółowo.

Tylko środowisko uruchomieniowe IoT Edge jest obsługiwane w przypadku wdrożeń produkcyjnych, ale następujące narzędzia umożliwiają symulowanie lub łatwe tworzenie IoT Edge urządzeń do celów deweloperskich i testowych. Te narzędzia nie wykluczają się wzajemnie, ale mogą współdziałać ze sobą, aby uzyskać pełne środowisko programistyczne.

| Narzędzie | Znana także jako | Obsługiwane platformy | Optymalne zastosowanie |
| ---- | ------------- | ------------------- | --------- |
| Narzędzie deweloperskie EdgeHub IoT  | iotedgehubdev | Windows, Linux, macOS | Symulowanie urządzenia do modułów debugowania. |
| IoT Edgenie kontenera dev | iotedgedev | Windows, Linux, macOS | Programowanie bez instalowania zależności. |
| IoT Edge środowiska uruchomieniowego w kontenerze | iotedgec | Windows, Linux, macOS, ARM | Testowanie na urządzeniu, które może nie obsługiwać środowiska uruchomieniowego. |
| Kontener urządzenia IoT Edge | toolboc/Azure-IoT-Device-Container | Windows, Linux, macOS, ARM | Testowanie scenariusza z wieloma IoT Edge urządzeniami w odpowiedniej skali. |

### <a name="iot-edgehub-dev-tool"></a>Narzędzie deweloperskie EdgeHub IoT

Narzędzie deweloperskie usługi Azure IoT EdgeHub udostępnia lokalne środowisko programistyczne i debugowania. Narzędzie pomaga uruchomić Moduły IoT Edge bez środowiska uruchomieniowego IoT Edge, co umożliwia lokalne tworzenie, opracowywanie, testowanie, uruchamianie i debugowanie modułów IoT Edge oraz rozwiązań. Nie jest konieczne wypychanie obrazów do rejestru kontenerów i wdrażanie ich na urządzeniu w celu przetestowania.

Narzędzie deweloperskie EdgeHub IoT zostało zaprojektowane tak, aby działało wspólnie z rozszerzeniami programu Visual Studio i Visual Studio Code, a także z IoT Edge narzędzia deweloperskiego. Obsługuje ona programowanie pętli wewnętrznej oraz testowanie pętli zewnętrznej, dlatego integruje się również z narzędziami DevOps.

Aby uzyskać więcej informacji i zainstalować, zobacz [narzędzie deweloperskie EdgeHub Azure IoT](https://pypi.org/project/iotedgehubdev/).

### <a name="iot-edge-dev-container"></a>IoT Edgenie kontenera dev

Azure IoT Edge dev Container to kontener platformy Docker, który ma wszystkie zależności, które są potrzebne do IoT Edge projektowania. Ten kontener ułatwia rozpoczęcie pracy z dowolnym językiem, który ma być opracowywany w programie, w tym C#, Python, Node.js i Java. Wystarczy zainstalować aparat kontenerów, na przykład Docker lub Moby, aby ściągnąć kontener na komputer deweloperski.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge dev Container](https://github.com/Azure/iotedgedev/wiki/quickstart-with-iot-edge-dev-container).

### <a name="iot-edge-runtime-in-a-container"></a>IoT Edge środowiska uruchomieniowego w kontenerze

Środowisko uruchomieniowe IoT Edge w kontenerze zapewnia kompletne środowisko uruchomieniowe, które pobiera parametry połączenia urządzenia jako zmienną środowiskową. Ten kontener umożliwia przetestowanie modułów IoT Edge i scenariuszy w systemie, który może nie obsługiwać natywnego środowiska uruchomieniowego, takiego jak macOS. Wszystkie wdrożone moduły zostaną uruchomione poza kontenerem środowiska uruchomieniowego. Jeśli chcesz, aby środowisko uruchomieniowe i wszystkie wdrożone moduły istniały w tym samym kontenerze, rozważ zamiast tego kontener urządzeń IoT Edge.

Aby uzyskać więcej informacji, zobacz [uruchamianie Azure IoT Edge w kontenerze](https://github.com/Azure/iotedgedev/tree/master/docker/runtime).

### <a name="iot-edge-device-container"></a>Kontener urządzenia IoT Edge

Kontener urządzeń IoT Edge to kompletne urządzenie IoT Edge gotowe do uruchomienia na dowolnym komputerze z aparatem kontenera. Kontener urządzeń zawiera IoT Edge środowiska uruchomieniowego i aparatu kontenera. Każde wystąpienie kontenera to w pełni funkcjonalne urządzenie IoT Edge samoobsługowego udostępniania. Kontener urządzeń obsługuje zdalne debugowanie modułów, pod warunkiem, że istnieje Trasa sieciowa do modułu. Kontener urządzeń jest dobry do szybkiego tworzenia dużej liczby IoT Edge urządzeń do testowania scenariuszy lub Azure Pipelines. Obsługuje również wdrażanie w Kubernetes za pośrednictwem Helm.

Aby uzyskać więcej informacji, zobacz [Azure IoT Edge kontener urządzeń](https://github.com/toolboc/azure-iot-edge-device-container).

## <a name="devops-tools"></a>Narzędzia DevOps

Gdy wszystko jest gotowe do opracowania rozwiązań w skali dla obszernych scenariuszy produkcyjnych, Skorzystaj z nowoczesnych zasad DevOps, takich jak Automatyzacja, monitorowanie i usprawnione procesy inżynierii oprogramowania. IoT Edge ma rozszerzenia do obsługi narzędzi DevOps, w tym Azure DevOps, Azure DevOps Projects i Jenkins. Jeśli chcesz dostosować istniejący potok lub użyć innego narzędzia DevOps, takiego jak CircleCI lub TravisCI, możesz to zrobić za pomocą funkcji interfejsu wiersza polecenia dostępnych w narzędziu deweloperskim IoT Edge.

Aby uzyskać więcej informacji, wskazówek i przykładów, zobacz następujące strony:

* [Ciągła integracja i ciągłe wdrażanie do Azure IoT Edge](how-to-continuous-integration-continuous-deployment.md)
* [Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla IoT Edge przy użyciu usługi Azure DevOps Starter](how-to-devops-starter.md)
* [Wtyczka Azure IoT Edge Jenkins](https://plugins.jenkins.io/azure-iot-edge)
* [IoT Edge DevOps repozytorium GitHub](https://github.com/toolboc/IoTEdge-DevOps)