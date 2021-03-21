---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 08/07/2020
ms.author: glenga
ms.openlocfilehash: a4f03223b5067d18f5d6e747b3bb630a86031b8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96020368"
---
Jeśli nie można używać pakietów rozszerzeń, można użyć Azure Functions Core Tools lokalnie do zainstalowania określonych pakietów rozszerzenia wymaganych przez projekt.

> [!IMPORTANT]
> Nie można jawnie zainstalować rozszerzeń w aplikacji funkcji, która korzysta z pakietów rozszerzeń. `extensionBundle`Przed jawne instalowaniem rozszerzeń Usuń sekcję w *host.js* .

Poniższe elementy opisują niektóre z powodów, dla których może być konieczne ręczne zainstalowanie rozszerzeń:

* Musisz uzyskać dostęp do określonej wersji rozszerzenia, która nie jest dostępna w pakiecie.
* Musisz uzyskać dostęp do niestandardowego rozszerzenia, które nie jest dostępne w pakiecie.
* Musisz uzyskać dostęp do określonej kombinacji rozszerzeń, które nie są dostępne w pojedynczym pakiecie.

> [!NOTE]
> Aby ręcznie zainstalować rozszerzenia przy użyciu narzędzi podstawowych, należy zainstalować [zestaw SDK programu .NET Core 2. x](https://dotnet.microsoft.com/download) . Zestaw .NET Core SDK jest używany przez Azure Functions Core Tools do instalowania rozszerzeń z narzędzia NuGet. Nie musisz znać .NET, aby używać rozszerzeń Azure Functions.

Po jawnie instalacji rozszerzeń, plik projektu .NET o nazwie Extensions. csproj jest dodawany do katalogu głównego projektu. Ten plik definiuje zestaw pakietów NuGet wymaganych przez funkcje. Chociaż można korzystać z odwołań do [pakietów NuGet](/nuget/consume-packages/package-references-in-project-files) w tym pliku, narzędzia podstawowe umożliwiają Instalowanie rozszerzeń bez konieczności ręcznego edytowania pliku.

Istnieje kilka sposobów na używanie podstawowych narzędzi do instalowania wymaganych rozszerzeń w projekcie lokalnym. 

#### <a name="install-all-extensions"></a>Zainstaluj wszystkie rozszerzenia 

Użyj następującego polecenia, aby automatycznie dodać wszystkie pakiety rozszerzeń używane przez powiązania w projekcie lokalnym:

```dotnetcli
func extensions install
```
Polecenie odczytuje *function.jsw* pliku, aby zobaczyć, które pakiety są potrzebne, zainstaluje je i ponownie kompiluje projekt rozszerzeń (rozszerzenia. csproj). Dodaje nowe powiązania w bieżącej wersji, ale nie aktualizuje istniejących powiązań. Użyj `--force` opcji, aby zaktualizować istniejące powiązania do najnowszej wersji podczas instalowania nowych.

Jeśli aplikacja funkcji używa powiązań, których podstawowe narzędzia nie rozpoznają, należy ręcznie zainstalować określone rozszerzenie.

#### <a name="install-a-specific-extension"></a>Instalowanie określonego rozszerzenia

Użyj poniższego polecenia, aby zainstalować określony pakiet rozszerzeń w określonej wersji, w tym przypadku rozszerzenie magazynu:

```dotnetcli
func extensions install --package Microsoft.Azure.WebJobs.Extensions.Storage --version 4.0.2
```
