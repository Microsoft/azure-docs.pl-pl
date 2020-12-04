---
title: Azure Stack Edge & Azure Data Box Gateway 2007 informacje o wersji | Microsoft Docs
description: Opisuje krytyczne problemy i rozwiązania dla Azure Stack Edge i Data Box Gateway, na których działa wersja 2007.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 5dd835c99f5781b3734983ea64709535a75e1fa8
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582787"
---
# <a name="azure-stack-edge-and-azure-data-box-gateway-2007-release-notes"></a>Informacje o wersji Azure Stack Edge i Azure Data Box Gateway 2007

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wydania 2007 dla Azure Stack Edge i Data Box Gateway.

Informacje o wersji są stale aktualizowane i wykryto krytyczne problemy wymagające obejścia. Przed wdrożeniem Azure Stack Edge/Data Box Gateway należy uważnie przejrzeć informacje zawarte w informacjach o wersji.

Ta wersja jest zgodna z wersjami oprogramowania:

- **Azure Stack Edge 2007 (1.6.1280.1667)** — KB 4566549
- **Data Box Gateway 2007 (1.6.1280.1667)** — KB 4566550

> [!NOTE]
> Aktualizacja 2007 może zostać zastosowana tylko do wszystkich urządzeń z wersjami ogólne dostępności oprogramowania lub nowszych.

## <a name="whats-new"></a>Co nowego

Ta wersja zawiera następujące poprawki błędów:

- **Problem z przekazywaniem** — ta wersja rozwiązuje problem z przekazaniem, w którym ponowne uruchomienia z powodu błędów mogą spowalniać ukończenie przekazywania. Ten problem może wystąpić podczas przekazywania zestawu danych, który składa się głównie z dużej ilości plików, w odniesieniu do dostępnej przepustowości, szczególnie, ale nie jest ograniczony do, gdy ograniczanie przepustowości jest aktywne. Ta zmiana gwarantuje, że przed ponownym rozpoczęciem przekazywania dla danego pliku zostanie podana wystarczająca szansa na ukończenie przekazywania.

Ta wersja zawiera również następujące aktualizacje:

- Obraz podstawowy dla wirtualnego dysku twardego systemu Windows został zaktualizowany.
- Wszystkie zbiorcze aktualizacje systemu Windows i aktualizacje programu .NET Framework zostały uwzględnione w wersji 2020 maja.
- Ta wersja obsługuje IoT Edge 1.0.9.3 na Azure Stack urządzeniach brzegowych.

## <a name="known-issues-in-this-release"></a>Znane problemy w tej wersji

W tej wersji nie zaznaczono żadnych nowych problemów. Wszystkie problemy wymienione w wersji zostały przeniesione z poprzednich wersji. Aby wyświetlić listę znanych problemów, przejdź do [znanych problemów w wersji GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrożenia Azure Stack Edge](../databox-online/azure-stack-edge-deploy-prep.md)
- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
