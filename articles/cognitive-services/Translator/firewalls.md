---
title: Tłumaczenie za zapory — translator
titleSuffix: Azure Cognitive Services
description: Usługa Azure Cognitive Services translator może przetłumaczyć za zapory za pomocą nazwy domeny lub filtrowania adresów IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 78a53c99f5f184c1b6b45d59d86c23efb898d7dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83996962"
---
# <a name="how-to-translate-behind-ip-firewalls-with-translator"></a>Jak przetłumaczyć zapory IP za pomocą translatora

Translator może przetłumaczyć za zapory przy użyciu nazwy domeny lub filtrowania adresów IP. Filtrowanie nazw domen jest preferowaną metodą. **Nie zaleca** się uruchamiania usługi Microsoft Translator za pomocą odfiltrowanej zapory protokołu IP. Instalacja może się pojawić w przyszłości bez powiadomienia.

## <a name="translator-ip-addresses"></a>Adresy IP translatora
Adresy IP dla api.cognitive.microsofttranslator.com-translator od 21 sierpnia 2019:

* **Azja i Pacyfik:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Ameryka Północna:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tłumaczenie za zapory IP w translatorze](reference/v3-0-translate.md)
