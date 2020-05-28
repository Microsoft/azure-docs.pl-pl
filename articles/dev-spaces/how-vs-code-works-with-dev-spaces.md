---
title: Jak działa Visual Studio Code z Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Dowiedz się, jak Visual Studio Code i Azure Dev Spaces ułatwiają debugowanie i szybkie Iterowanie aplikacji Kubernetes
keywords: Azure Dev Spaces, Spaces dev, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontenery
ms.openlocfilehash: c2f6f2ef072748bddee5b3004dc69f3a9c03be16
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996231"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Jak działa Visual Studio Code z Azure Dev Spaces

Możesz użyć Visual Studio Code i [rozszerzenia Azure dev Spaces][azds-extension] do przygotowywania, uruchamiania i debugowania usług przy użyciu Azure dev Spaces. Za pomocą Visual Studio Code i rozszerzenia Azure Dev Spaces można:

* Generowanie zasobów do uruchamiania i debugowania usług w AKS
* Uruchamianie usług Java, Node. js i .NET Core w przestrzeni deweloperów
* Bezpośrednie debugowanie usług Java, Node. js i .NET Core działających w obszarze dev

## <a name="generate-assets"></a>Generuj elementy zawartości

Visual Studio Code i rozszerzenie Azure Dev Spaces generują następujące zasoby dla projektu:

* Wieloetapowe dockerfile for Java — aplikacje Maven, Node. js i aplikacje platformy .NET Core
* Helm wykresy dla niemal dowolnego języka z pliku dockerfile
* `azds.yaml`Plik, który jest [Azure dev Spaces plikiem konfiguracji][azds-yaml] dla projektu
* `.vscode`Folder z konfiguracją Visual Studio Code uruchamiania projektu dla aplikacji Java korzystających z Maven, aplikacji node. js i aplikacji platformy .NET Core

Pliku dockerfile, wykres Helm i `azds.yaml` pliki są tymi samymi zasobami, które są generowane podczas uruchamiania `azds prep` . Te pliki mogą być również używane poza programem Visual Studio Code do uruchamiania projektu w programie AKS, na przykład uruchomione `azds up` . Ten `.vscode` folder jest używany tylko przez program Visual Studio Code do uruchamiania projektu w programie AKS z Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Uruchamianie usługi w AKS

Po wygenerowaniu zasobów dla projektu można uruchomić usługi Java, Node. js i .NET Core w istniejącym miejscu deweloperskim z Visual Studio Code. Na stronie *debugowanie* Visual Studio Code można wywołać konfigurację uruchamiania z `.vscode` katalogu, aby uruchomić projekt.

Należy utworzyć klaster AKS i włączyć Azure Dev Spaces w klastrze poza Visual Studio Code. Można ponownie użyć istniejących wieloetapowe dockerfile, wykresów Helm i `azds.yaml` plików utworzonych poza Visual Studio Code, takich jak zasoby wygenerowane przez program `azds prep` . W przypadku ponownego użycia zasobów wygenerowanych poza programem Visual Studio Code nadal trzeba mieć `.vscode` katalog. Ten `.vscode` katalog może być ponownie generowany przez program Visual Studio Code i rozszerzenie Azure dev Spaces i nie spowoduje zastąpienia istniejących zasobów.

W przypadku projektów .NET Core musisz mieć zainstalowane [rozszerzenie języka C#][csharp-extension] , aby uruchomić usługę .net z Visual Studio Code. Również w przypadku projektów języka Java korzystających z programu Maven musi być zainstalowany [debuger Java dla rozszerzenia Azure dev Spaces][java-extension] , [a także Maven zainstalowany i skonfigurowany][maven] do uruchamiania usługi Java z Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Debugowanie usługi w AKS

Po uruchomieniu projektu można debugować usługi Java, Node. js i .NET Core działające w obszarze dev, bezpośrednio z Visual Studio Code. Konfiguracja uruchamiania w `.vscode` katalogu zawiera dodatkowe informacje debugowania dotyczące uruchamiania usługi z włączoną funkcją debugowania w obszarze dev. Visual Studio Code również dołącza do procesu debugowania w uruchomionym kontenerze w miejscach programistycznych, co pozwala na ustawianie punktów przerwania, inspekcję zmiennych i wykonywanie innych operacji debugowania.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Użyj Visual Studio Code z Azure Dev Spaces

W następujących przewodnikach szybki start można zobaczyć Visual Studio Code i rozszerzenie Azure Dev Spaces pracujące z Azure Dev Spacesami:

* [Szybkie iteracje i debugowanie za pomocą Visual Studio Code i języka Java][quickstart-java]
* [Szybkie iteracje i debugowanie przy użyciu Visual Studio Code i platformy .NET][quickstart-netcore]
* [Szybkie iteracje i debugowanie za pomocą Visual Studio Code i środowiska Node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
