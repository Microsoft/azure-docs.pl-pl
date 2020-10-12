---
title: Instalowanie pakietu usługi Remote Rendering dla środowiska Unity
description: Wyjaśnia, jak zainstalować biblioteki DLL klienta renderowania zdalnego dla aparatu Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 3704d1a418baeec18c3303b8203a0185790cbcc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85564305"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalowanie pakietu usługi Remote Rendering dla środowiska Unity

Zdalne renderowanie na platformie Azure używa pakietu Unity do hermetyzacji integracji w środowisku Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Zarządzanie pakietami renderowania zdalnego w środowisku Unity

Pakiety Unity to kontenery, którymi można zarządzać za pomocą [Menedżera pakietów](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)aparatu Unity.
Ten pakiet zawiera cały interfejs API języka C#, a także wszystkie pliki binarne wtyczki wymagane do użycia zdalnego renderowania platformy Azure przy użyciu aparatu Unity.
W poniższym schemacie nazewnictwa aparatu Unity dla pakietów pakiet nosi nazwę **com. Microsoft. Azure. Remote-rendering**.

Pakiet nie jest częścią [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering)i nie jest dostępny w rejestrze wewnętrznego pakietu aparatu Unity. Aby dodać ją do projektu, musisz ręcznie edytować plik projektu, `manifest.md` Aby dodać następujące elementy:

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.31",
    ...existing dependencies...
  }
}
```

Po dodaniu tego programu można użyć Menedżera pakietów Unity, aby upewnić się, że masz najnowszą wersję.
Dokładniejsze instrukcje są podane w [samouczku: Wyświetlanie modeli zdalnych](../../tutorials/unity/view-remote-models/view-remote-models.md).

## <a name="unity-render-pipelines"></a>Potoki renderowania środowiska Unity

Zdalne renderowanie działa zarówno z, **:::no-loc text="Universal render pipeline":::** jak i **:::no-loc text="Standard render pipeline":::** . Ze względu na wydajność zaleca się używanie uniwersalnego potoku renderowania.

Aby można było korzystać z programu **:::no-loc text="Universal render pipeline":::** , jego pakiet musi być zainstalowany w środowisku Unity. Można to zrobić w interfejsie użytkownika **Menedżera pakietów** środowiska Unity (nazwa pakietu: **uniwersalny RP**, wersja 7.3.1 lub nowsza) lub za pomocą `Packages/manifest.json` pliku, zgodnie z opisem w [samouczku instalacji projektu aparatu Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Następne kroki

* [Obiekty i składniki gier Unity](objects-components.md)
* [Samouczek: Wyświetlanie modeli zdalnych](../../tutorials/unity/view-remote-models/view-remote-models.md)
