---
title: Azure Stack EDGE Pro z FPGA 2101 — informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla Azure Stack Edge 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 025694dc020bb18ce66574bac476f34034353721
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072663"
---
# <a name="azure-stack-edge-pro-with-fpga-2101-release-notes"></a>Azure Stack EDGE Pro z FPGA 2101 — informacje o wersji

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wersji 2101 programu Azure Stack EDGE Pro z wbudowaną, programowalną bramą bramy (FPGA).

Informacje o wersji są stale aktualizowane. W przypadku wykrycia problemów krytycznych, które wymagają obejścia, są one dodawane. Przed wdrożeniem urządzenia Azure Stack Edge uważnie Przejrzyj informacje w informacjach o wersji.  

Ta wersja odnosi się do wersji oprogramowania:

- **Azure Stack Edge 2101 (1.6.1475.2528)** — KB 4599267

> [!NOTE]
> Aktualizacja 2101 może zostać zastosowana tylko do urządzeń z wersjami ogólnie dostępnymi dla oprogramowania lub nowszym.

## <a name="whats-new"></a>Co nowego

Ta wersja zawiera następujące poprawki błędów:

- **Problem z przekazywaniem** — ta wersja rozwiązuje problem z przekazywaniem, gdzie ponowne uruchomienia przekazywania spowodowane awarią mogą spowalniać ukończenie przekazywania. Ten problem może wystąpić podczas przekazywania zestawu danych, który obejmuje głównie pliki, które są duże względem dostępnej przepustowości, szczególnie, ale nie ograniczone do, gdy ograniczanie przepustowości jest aktywne. Ta zmiana zapewnia wystarczającą możliwość wykonania operacji przekazywania przed ponownym uruchomieniem przekazywania dla danego pliku.

Ta wersja zawiera również następujące aktualizacje:

- Wszystkie aktualizacje zbiorcze aktualizacji systemu Windows i programu .NET Framework wydane do października 2020.
- Wersja oprogramowania układowego kontrolera zarządzania płytą główną (BMC) jest uaktualniana z 3.32.32.32 do 3.36.36.36 podczas instalacji fabrycznej w celu rozwiązania niezgodności z nowszymi jednostkami zasilacza firmy Dell.
- Statyczny adres IP dla Azure Data Box Gateway jest zachowywany w ramach aktualizacji oprogramowania.
- Ta wersja obsługuje IoT Edge 1.0.9.3 na Azure Stack urządzeniach brzegowych.

## <a name="known-issues-in-this-release"></a>Znane problemy w tej wersji

W tej wersji nie zaznaczono żadnych nowych problemów. Wszystkie problemy wymienione w wersji zostały przeniesione z poprzednich wersji. Aby wyświetlić listę znanych problemów, przejdź do [znanych problemów w wersji GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrożenia Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
