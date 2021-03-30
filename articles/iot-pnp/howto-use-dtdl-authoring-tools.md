---
title: Tworzenie i weryfikowanie modeli DTDL za pomocą narzędzia | Microsoft Docs
description: Zainstaluj Edytor DTDL dla Visual Studio Code lub Visual Studio 2019 i użyj go do tworzenia modeli Plug and Play IoT.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280199"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Instalowanie narzędzi autorskich DTDL i korzystanie z nich

Modele [Digital bliźniaczych reprezentacji Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) to pliki JSON. Możesz użyć rozszerzenia dla programu Visual Studio Code lub Visual Studio 2019 do tworzenia i weryfikowania tych plików modeli.

## <a name="install-and-use-the-vs-code-extension"></a>Instalowanie rozszerzenia VS Code i korzystanie z niego

Rozszerzenie DTDL dla VS Code dodaje następujące funkcje tworzenia DTDL:

- Weryfikacja składni DTDL v2.
- Technologia IntelliSense, w tym funkcja autouzupełniania, ułatwiająca składnię języka.
- Możliwość tworzenia interfejsów z poziomu palety poleceń.

Aby zainstalować rozszerzenie DTDL, przejdź do [edytora DTDL w celu Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Możesz również wyszukać **DTDL** w widoku rozszerzenia w vs Code.

Po zainstalowaniu rozszerzenia, użyj go, aby ułatwić tworzenie plików modelu DTDL w programie VS Code:

- Rozszerzenie zapewnia weryfikację składni w plikach modelu DTDL, wyróżnianie błędów, jak pokazano na poniższym zrzucie ekranu:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Walidacja modelu w VS Code":::

- Podczas edytowania modeli DTDL Używaj funkcji IntelliSense i autouzupełniania:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Używanie funkcji IntelliSense dla modeli DTDL w VS Code":::

- Utwórz nowy interfejs DTDL. **DTDL: Create Interface** polecenie tworzy plik JSON z nowym interfejsem. Interfejs zawiera przykładowe dane telemetryczne, właściwość i definicje poleceń.

## <a name="install-and-use-the-visual-studio-extension"></a>Instalowanie rozszerzenia programu Visual Studio i korzystanie z niego

Rozszerzenie DTDL dla programu Visual Studio 2019 dodaje następujące funkcje tworzenia DTDL:

- Weryfikacja składni DTDL v2.
- Technologia IntelliSense, w tym funkcja autouzupełniania, ułatwiająca składnię języka.

Aby zainstalować rozszerzenie DTDL, przejdź do [pomocy technicznej języka DTDL dla programu VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Możesz również wyszukać **DTDL** w obszarze **Zarządzanie rozszerzeniami** w programie Visual Studio.

Po zainstalowaniu rozszerzenia, użyj go, aby ułatwić tworzenie plików modelu DTDL w programie Visual Studio:

- Rozszerzenie zapewnia weryfikację składni w plikach modelu DTDL, wyróżnianie błędów, jak pokazano na poniższym zrzucie ekranu:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Walidacja modelu w programie Visual Studio":::

- Podczas edytowania modeli DTDL Używaj funkcji IntelliSense i autouzupełniania:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Używanie funkcji IntelliSense dla modeli DTDL w programie Visual Studio":::

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób używania rozszerzeń DTDL dla Visual Studio Code i programu Visual Studio 2019 do tworzenia i weryfikowania plików modelu DTDL. Sugerowanym następnym krokiem jest nauczenie się, jak używać programu [Azure IoT Explorer z modelami i urządzeniami](./howto-use-iot-explorer.md).
