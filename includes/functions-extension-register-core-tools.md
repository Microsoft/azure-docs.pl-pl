---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: 2936d22eacef73daef4433b3fd296dd4757fa410
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88031131"
---
Jeśli nie można używać pakietów rozszerzeń, można użyć Azure Functions Core Tools lokalnie do zainstalowania określonych pakietów rozszerzenia wymaganych przez projekt. 

> [!NOTE]
> Aby ręcznie zainstalować rozszerzenia przy użyciu narzędzi podstawowych, należy zainstalować zestaw SDK programu .NET Core 2. x.

Po jawnie instalacji rozszerzeń, plik projektu .NET o nazwie Extensions. csproj jest dodawany do katalogu głównego projektu. Ten plik definiuje zestaw pakietów NuGet wymaganych przez funkcje. Chociaż można korzystać z odwołań do [pakietów NuGet](/nuget/consume-packages/package-references-in-project-files) w tym pliku, narzędzia podstawowe umożliwiają Instalowanie rozszerzeń bez konieczności ręcznego edytowania pliku.

Istnieje kilka sposobów na używanie podstawowych narzędzi do instalowania wymaganych rozszerzeń w projekcie lokalnym. 

#### <a name="install-all-extensions"></a>Zainstaluj wszystkie rozszerzenia 

Użyj następującego polecenia, aby automatycznie dodać wszystkie pakiety rozszerzeń używane przez powiązania w projekcie lokalnym:

```dotnetcli
func extensions install
```
Polecenie odczytuje *function.jsw* pliku, aby zobaczyć, które pakiety są potrzebne, zainstaluje je i ponownie kompiluje projekt rozszerzeń (rozszerzenia. csproj). Dodaje nowe powiązania w bieżącej wersji, ale nie aktualizuje istniejących powiązań. Użyj `--force` opcji, aby zaktualizować istniejące powiązania do najnowszej wersji podczas instalowania nowych.

#### <a name="install-a-specific-extension"></a>Instalowanie określonego rozszerzenia

Użyj poniższego polecenia, aby zainstalować określony pakiet rozszerzeń w określonej wersji, w tym przypadku rozszerzenie magazynu:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```