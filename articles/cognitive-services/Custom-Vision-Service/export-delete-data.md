---
title: Eksportowanie lub usuwanie danych — Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Zachowasz pełną kontrolę nad danymi. W tym artykule wyjaśniono, jak można wyświetlać, eksportować i usuwać dane w Custom Vision Service.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: pafarley
ms.openlocfilehash: 82d9f4508db376ebbe69ef772c15fb732391a31d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73718970"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Eksportowanie lub usuwanie danych użytkownika w Custom Vision

Custom Vision zbiera dane użytkowników w celu obsługi usługi, ale klienci mają pełną kontrolę nad przeglądaniem, eksportowaniem i usuwaniem danych przy użyciu [interfejsów API szkolenia](https://go.microsoft.com/fwlink/?linkid=865446)Custom Vision.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Aby dowiedzieć się, jak eksportować i usuwać dane użytkowników w Custom Vision, zobacz poniższą tabelę.

| Dane | Operacja eksportowania | Operacja usuwania |
| ---- | ---------------- | ---------------- |
| Informacje o koncie (klucze subskrypcji) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Usuń przy użyciu Azure Portal (subskrypcje platformy Azure). Lub przy użyciu przycisku "Usuń konto" na stronie ustawień CustomVision.ai (subskrypcje kont Microsoft) | 
| Szczegóły iteracji | [Getiteracji](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Szczegóły wydajności iteracji | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Lista iteracji | [Getiteracjis](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| Projekty i szczegóły projektu | [Getproject](https://go.microsoft.com/fwlink/?linkid=865446) i [getprojects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Tagi obrazu | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) i [getTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Obrazy | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zapewnia identyfikator URI do pobrania obrazu) i [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (zapewnia identyfikator URI do pobrania obrazu) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| Eksportowane modele | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | Usunięte po usunięciu konta |
