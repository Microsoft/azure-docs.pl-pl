---
title: Informacje o wersji Azure Data Box Gateway 2101 | Microsoft Docs
description: W tym artykule opisano krytyczne problemy i rozwiązania dla Azure Data Box Gateway z uruchomioną wersją 2101.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99072732"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Informacje o wersji Azure Data Box Gateway 2101

Poniższe informacje o wersji dotyczą krytycznych problemów otwartych i rozwiązanych problemów dotyczących wersji 2101 Azure Data Box Gateway.

Informacje o wersji są stale aktualizowane. W przypadku wykrycia problemów krytycznych, które wymagają obejścia, są one dodawane. Przed wdrożeniem Azure Data Box Gateway należy uważnie zapoznać się z informacjami w informacjach o wersji.  

Ta wersja jest zgodna z wersjami oprogramowania:

- **Data Box Gateway 2101 (1.6.1475.2528)** — KB 4603462

> [!NOTE]
> Aktualizacja 2101 może zostać zastosowana tylko do wszystkich urządzeń z wersjami ogólne dostępności oprogramowania lub nowszych.

## <a name="whats-new"></a>Co nowego

Ta wersja zawiera następujące poprawki błędów:

- **Problem z przekazywaniem** — ta wersja rozwiązuje problem z przekazywaniem z powodu niepowodzeń, który może spowolnić przekazywanie. Ten problem może wystąpić podczas przekazywania zestawu danych, który składa się głównie z dużej ilości plików, w odniesieniu do dostępnej przepustowości, szczególnie, ale nie jest ograniczony do, gdy ograniczanie przepustowości jest aktywne. Ta zmiana zapewnia wystarczającą możliwość wykonania operacji przekazywania przed ponownym uruchomieniem przekazywania dla danego pliku.

Ta wersja zawiera również następujące aktualizacje:

- Wszystkie aktualizacje zbiorcze aktualizacji systemu Windows i programu .NET Framework wydane do października 2020.
- Statyczny adres IP dla Azure Data Box Gateway jest zachowywany w ramach aktualizacji oprogramowania.

## <a name="known-issues-in-this-release"></a>Znane problemy w tej wersji

W tej wersji nie zaznaczono żadnych nowych problemów. Wszystkie problemy wymienione w wersji zostały przeniesione z poprzednich wersji. Aby wyświetlić listę znanych problemów, przejdź do [znanych problemów w wersji GA](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Następne kroki

- [Przygotowywanie do wdrażania usługi Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
