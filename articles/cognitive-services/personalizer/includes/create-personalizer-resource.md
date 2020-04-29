---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 11abd52681d7c9962af4e5bf0728f97b256223c1
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122901"
---
## <a name="create-a-personalizer-azure-resource"></a>Tworzenie zasobu platformy Azure dla programu personalizacji

Utwórz zasób dla narzędzia Personalizacja przy użyciu [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) na komputerze lokalnym. Możesz również wykonać następujące czynności:

* Uzyskaj [klucz wersji próbnej](https://azure.microsoft.com/try/cognitive-services) ważny przez 7 dni bezpłatnie. Po zarejestrowaniu program będzie dostępny w [witrynie sieci Web systemu Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Wyświetl zasób na [Azure Portal](https://portal.azure.com/).

Po otrzymaniu klucza z subskrypcji próbnej lub zasobu Utwórz dwie [zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `PERSONALIZER_RESOURCE_KEY`dla klucza zasobu.
* `PERSONALIZER_RESOURCE_ENDPOINT`dla punktu końcowego zasobu.

W Azure Portal wartości klucza i punktu końcowego są dostępne na stronie **szybkiego startu** .
