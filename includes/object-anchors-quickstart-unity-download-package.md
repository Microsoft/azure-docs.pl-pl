---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 02/18/2021
ms.author: crtreasu
ms.openlocfilehash: ada83d6263ef033208200d810c53c5f045acc9a7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105070"
---
Następnym krokiem jest pobranie pakietu zakotwiczenia obiektów platformy Azure dla aparatu Unity.

# <a name="download-with-web-browser"></a>[Pobierz za pomocą przeglądarki sieci Web](#tab/unity-package-web-ui)

W [tym miejscu](https://aka.ms/aoa/unity-sdk/package)Znajdź pakiet kotwic obiektów platformy Azure dla aparatu Unity. Wybierz żądaną wersję i Pobierz pakiet przy użyciu przycisku **Pobierz** .

# <a name="download-with-npm"></a>[Pobierz za pomocą NPM](#tab/unity-package-npm)

Ten krok wymaga zainstalowania i udostępnienia <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> .

Uruchom następujące polecenie zastępujące `<version_number>` wersją zakotwiczenia obiektów platformy Azure, które chcesz pobrać:

```bash
npm pack com.microsoft.azure.object-anchors.runtime@<version_number> --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/
```

> [!NOTE]
> Aby wyświetlić listę dostępnych wersji pakietu zakotwiczenia obiektów platformy Azure, uruchom następujące polecenie:
>
> ```bash
> npm view com.microsoft.azure.object-anchors.runtime --registry https://pkgs.dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging/Unity-packages/npm/registry/ versions
> ```

Pakiet kotwic obiektów platformy Azure zostanie pobrany do folderu, w którym uruchomiono polecenie.

# <a name="install-with-mixed-reality-feature-tool-beta"></a>[Zainstaluj za pomocą narzędzia funkcji rzeczywistości mieszanej (beta)](#tab/unity-package-mixed-reality-feature-tool)

Przejdź do następnego kroku. Będziesz używać <a a href="/windows/mixed-reality/develop/unity/welcome-to-mr-feature-tool" target="_blank">Narzędzia funkcji rzeczywistości mieszanej</a> w późniejszym kroku.

---