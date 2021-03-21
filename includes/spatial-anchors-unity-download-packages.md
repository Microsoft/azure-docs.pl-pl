---
author: msftradford
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 03/18/2021
ms.author: parkerra
ms.openlocfilehash: d91c0aeda2b7ae2f133d2099cbc9d238fd19d287
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719926"
---
Aby pobrać wymagane pakiety, konieczne będzie zainstalowanie <a href="https://www.npmjs.com/get-npm" target="_blank">npm</a> .

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

> [!WARNING]
> Zestaw ASA SDK 2.7.0 to minimalna obsługiwana wersja. W przypadku korzystania z aparatu Unity 2020 2.9.0 zestawu ASA SDK to minimalna obsługiwana wersja.

Podstawowy pakiet kotwic platformy Azure zostanie pobrany do folderu, w którym uruchomiono polecenie.

Powtórz ten krok, aby pobrać pakiet dla każdej platformy (Android/iOS/HoloLens), która ma być obsługiwana w projekcie.

| Platforma | Nazwa pakietu                                    |
|----------|-------------------------------------------------|
| Android  | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.android@ <version_number> |
| iOS      | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.ios@ <version_number>     |
| HoloLens | obiekty com. Microsoft. Azure. przestrzenny — kotwice — sdk.windows@ <version_number> |