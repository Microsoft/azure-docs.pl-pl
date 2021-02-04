---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 2/3/21
ms.author: parkerra
ms.openlocfilehash: c97067c66296e8980a36b21298a7b2061e0f9b4c
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550408"
---
Uruchom następujące polecenie zastępujące `<version_number>` wersją zakotwiczeniami przestrzennymi platformy Azure, które chcesz pobrać do bieżącego folderu:

```bash
npm pack com.microsoft.azure.spatial-anchors-sdk.core@<version_number> --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM
```

> [!NOTE]
> Aby wyświetlić listę dostępnych wersji pakietu zakotwiczeń przestrzennych platformy Azure, uruchom następujące polecenie:
>
> ```bash
> npm view com.microsoft.azure.spatial-anchors-sdk.core --registry https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM versions
> ```

Podstawowy pakiet kotwic platformy Azure zostanie pobrany do folderu, w którym uruchomiono polecenie.

Powtórz ten krok, aby pobrać pakiet dla każdej platformy (Android/iOS/HoloLens), która ma być obsługiwana w projekcie.

| Platforma | Nazwa pakietu                                    |
|----------|-------------------------------------------------|
| Android  | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.android@ <version_number> |
| iOS      | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.ios@ <version_number>     |
| HoloLens | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.windows@ <version_number> |