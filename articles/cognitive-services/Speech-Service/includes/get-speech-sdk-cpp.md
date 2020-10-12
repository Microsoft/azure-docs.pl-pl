---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 5a06a0663601c221dd456b9cf4437cb9f32a18f8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81399879"
---
:::row:::
    :::column span="3":::
        Zestaw SDK mowy C++ jest dostępny w systemach Windows, Linux i macOS. Aby uzyskać więcej informacji, zobacz <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft. CognitiveServices. <span class="docon docon-navigate-external x-hidden-focus"></span> Speech </a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

#### <a name="c-nuget-package"></a>Pakiet NuGet języka C++

Zestaw SDK mowy C++ można zainstalować z **Menedżera pakietów** przy użyciu następującego `Install-Package` polecenia.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

#### <a name="c-binaries-and-header-files"></a>Plik binarny języka C++ i pliki nagłówkowe

Alternatywnie zestaw SDK mowy C++ można zainstalować z plików binarnych. Pobierz zestaw SDK jako <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">pakiet <span class="docon docon-navigate-external x-hidden-focus"></span> tar</a> i rozpakuj pliki w wybranym katalogu. Zawartość tego pakietu (obejmująca pliki nagłówkowe dla architektury docelowej x86 i x64) jest następująca:

  | Ścieżka                   | Opis                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | Licencja                                              |
  | `ThirdPartyNotices.md` | Uwagi dotyczące innych firm                                  |
  | `include`              | Pliki nagłówkowe dla języka C++                                 |
  | `lib/x64`              | Natywna biblioteka x64 do łączenia z aplikacją |
  | `lib/x86`              | Natywna biblioteka x86 do łączenia z aplikacją |

  Aby utworzyć aplikację, skopiuj lub Przenieś wymagane pliki binarne (i biblioteki) do środowiska deweloperskiego. Uwzględnij je zgodnie z potrzebami w procesie kompilacji.

#### <a name="additional-resources"></a>Zasoby dodatkowe

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/cpp" target="_blank">Windows, Linux i macOS — kod źródłowy języka C++ <span class="docon docon-navigate-external x-hidden-focus"></span></a>