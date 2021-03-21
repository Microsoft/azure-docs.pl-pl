---
title: Instalowanie pakietu usługi Remote Rendering dla środowiska Unity
description: Wyjaśnia, jak zainstalować biblioteki DLL klienta renderowania zdalnego dla aparatu Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 9454bef52798650fc431f8df994e1a964662b453
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720832"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Instalowanie pakietu usługi Remote Rendering dla środowiska Unity

Zdalne renderowanie na platformie Azure używa pakietu Unity do hermetyzacji integracji w środowisku Unity.
Ten pakiet zawiera cały interfejs API języka C#, a także wszystkie pliki binarne wtyczki wymagane do użycia zdalnego renderowania platformy Azure przy użyciu aparatu Unity.
W poniższym schemacie nazewnictwa aparatu Unity dla pakietów pakiet nosi nazwę **com. Microsoft. Azure. Remote-rendering**.

Aby zainstalować pakiet Unity, można wybrać jedną z następujących opcji.

## <a name="install-remote-rendering-package-using-the-mixed-reality-feature-tool"></a>Instalowanie pakietu renderowania zdalnego przy użyciu narzędzia funkcji rzeczywistości mieszanej

[Narzędzie funkcji rzeczywistości mieszanej](https://aka.ms/MRFeatureToolDocs) ([Download](https://aka.ms/mrfeaturetool)) to narzędzie służące do integrowania pakietów funkcji rzeczywistości mieszanej z projektami aparatu Unity. Pakiet nie jest częścią [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering)i nie jest dostępny w rejestrze wewnętrznego pakietu aparatu Unity.

Aby dodać pakiet do projektu, należy wykonać następujące:
1. [Pobierz narzędzie do funkcji rzeczywistości mieszanej](https://aka.ms/mrfeaturetool)
1. Postępuj zgodnie z [pełnymi instrukcjami](https://aka.ms/MRFeatureToolDocs) dotyczącymi korzystania z narzędzia.
1. Na stronie **odnajdywanie funkcji** zaznacz pole wyboru Microsoft Azure pakietu **renderowania zdalnego** i wybierz wersję pakietu, którą chcesz dodać do projektu

![Mixed_Reality_feature_tool_package](media/mixed-reality-feature-tool-package.png)

Aby zaktualizować pakiet lokalny, po prostu wybierz nowszą wersję z narzędzia funkcja rzeczywistości mieszanej i zainstaluj ją. Aktualizacja pakietu może czasami prowadzić do błędów konsoli. W takim przypadku spróbuj zamknąć i ponownie otworzyć projekt.

## <a name="install-remote-rendering-package-manually"></a>Ręcznie zainstaluj pakiet renderowania zdalnego

Aby ręcznie zainstalować pakiet renderowania zdalnego, należy wykonać następujące:

1. Pobierz pakiet z kanału NPM źródła rzeczywistości mieszanej na `https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry` .
    * Możesz użyć [npm](https://www.npmjs.com/get-npm) i uruchomić następujące polecenie, aby pobrać pakiet do bieżącego folderu.
      ```
      npm pack com.microsoft.azure.remote-rendering --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry
      ```

    * Możesz też użyć skryptu programu PowerShell `Scripts/DownloadUnityPackages.ps1` z [repozytorium GitHub Azure-Remote-rendering](https://github.com/Azure/azure-remote-rendering).
        * Edytuj zawartość `Scripts/unity_sample_dependencies.json` do
          ```json
          {
            "packages": [
              {
                "name": "com.microsoft.azure.remote-rendering", 
                "version": "latest", 
                "registry": "https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry"
              }
            ]
          }
          ```

        * Uruchom następujące polecenie w programie PowerShell, aby pobrać pakiet do podanego katalogu docelowego.
          ```
          DownloadUnityPackages.ps1 -DownloadDestDir <destination directory>
          ```

1. [Zainstaluj pobrany pakiet](https://docs.unity3d.com/Manual/upm-ui-tarball.html) za pomocą Menedżera pakietów aparatu Unity.

Aby zaktualizować pakiet lokalny, wystarczy ponownie uruchomić odpowiednie polecenie, które zostało użyte do ponownego zaimportowania pakietu. Aktualizacja pakietu może czasami prowadzić do błędów konsoli. W takim przypadku spróbuj zamknąć i ponownie otworzyć projekt.

## <a name="unity-render-pipelines"></a>Potoki renderowania środowiska Unity

Zdalne renderowanie działa zarówno z, **:::no-loc text="Universal render pipeline":::** jak i **:::no-loc text="Standard render pipeline":::** . Ze względu na wydajność zaleca się używanie uniwersalnego potoku renderowania.

Aby można było korzystać z programu **:::no-loc text="Universal render pipeline":::** , jego pakiet musi być zainstalowany w środowisku Unity. Można to zrobić w interfejsie użytkownika **Menedżera pakietów** środowiska Unity (nazwa pakietu: **uniwersalny RP**, wersja 7.3.1 lub nowsza) lub za pomocą `Packages/manifest.json` pliku, zgodnie z opisem w [samouczku instalacji projektu aparatu Unity](../../tutorials/unity/view-remote-models/view-remote-models.md#include-the-azure-remote-rendering-package).

## <a name="next-steps"></a>Następne kroki

* [Obiekty i składniki gier Unity](objects-components.md)
* [Samouczek: Wyświetlanie modeli zdalnych](../../tutorials/unity/view-remote-models/view-remote-models.md)
