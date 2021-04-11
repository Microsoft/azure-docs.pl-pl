---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/30/2021
ms.author: parkerra
ms.openlocfilehash: 7faab3340483d99fa276de06f3fd7787457edb9e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076711"
---
Następnym krokiem jest pobranie pakietów zakotwiczeń przestrzennych platformy Azure dla aparatu Unity. 

> [!WARNING]
> Zestaw ASA SDK 2.7.0 to minimalna obsługiwana wersja. W przypadku korzystania z aparatu Unity 2020 2.9.0 zestawu ASA SDK to minimalna obsługiwana wersja.

Aby korzystać z kotwic przestrzennych platformy Azure w środowisku Unity, należy pobrać pakiet podstawowy oraz pakiet specyficzny dla platformy dla każdej platformy (Android/iOS/HoloLens), która ma być obsługiwana.

| Platforma | Nazwa pakietu                                    |
|----------|-------------------------------------------------|
| System Android/iOS/HoloLens  | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.core@ <version_number> |
| Android  | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.android@ <version_number> |
| iOS      | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.ios@ <version_number>     |
| HoloLens | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.windows@ <version_number> |

# <a name="download-with-web-browser"></a>[Pobierz za pomocą przeglądarki sieci Web](#tab/unity-package-web-ui)

Tutaj Znajdź podstawowy pakiet kotwic platformy Azure dla aparatu Unity [tutaj](https://aka.ms/aoa/unity-sdk/package). Wybierz żądaną wersję i Pobierz pakiet przy użyciu przycisku **Pobierz** . Powtórz ten krok, aby pobrać pakiet dla każdej platformy, która ma być obsługiwana.

# <a name="download-with-npm"></a>[Pobierz za pomocą NPM](#tab/unity-package-npm)

Ten krok wymaga zainstalowania i udostępnienia <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> .

Uruchom następujące polecenie zastępujące `<version_number>` wersją zakotwiczeniami przestrzennymi platformy Azure, które chcesz pobrać do bieżącego folderu:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Aby wyświetlić listę dostępnych wersji pakietu zakotwiczeń przestrzennych platformy Azure, uruchom następujące polecenie:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Podstawowy pakiet kotwic platformy Azure zostanie pobrany do folderu, w którym uruchomiono polecenie. Powtórz ten krok, aby pobrać pakiet dla każdej platformy, która ma być obsługiwana.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Zainstaluj za pomocą narzędzia funkcji rzeczywistości mieszanej (beta)](#tab/unity-package-mixed-reality-feature-tool)

Przejdź do następnego kroku. Będziesz używać <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Narzędzia funkcji rzeczywistości mieszanej</a> w późniejszym kroku.

---