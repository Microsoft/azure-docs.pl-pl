---
title: Przetłumacz za zapory — interfejs API tłumaczenia tekstu w usłudze Translator
titleSuffix: Azure Cognitive Services
description: Usługa Azure Cognitive Services interfejs API tłumaczenia tekstu w usłudze Translator może przetłumaczyć za zapory za pomocą nazwy domeny lub filtrowania adresów IP.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cd7904fedd3ab3f64315cb6f98d99b8fd12254f6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837402"
---
# <a name="how-to-translate-behind-ip-firewalls-with-the-translator-text-api"></a>Jak przetłumaczyć zapory IP za pomocą interfejs API tłumaczenia tekstu w usłudze Translator

Interfejs API tłumaczenia tekstu w usłudze Translator można przetłumaczyć za zapory przy użyciu nazwy domeny lub filtrowania adresów IP. Filtrowanie nazw domen jest preferowaną metodą. **Nie zaleca** się uruchamiania usługi Microsoft Translator za pomocą odfiltrowanej zapory protokołu IP. Instalacja może się pojawić w przyszłości bez powiadomienia.

## <a name="translator-ip-addresses"></a>Adresy IP translatora
Adresy IP dla api.cognitive.microsofttranslator.com — Microsoft interfejs API tłumaczenia tekstu w usłudze Translator od 21 sierpnia 2019:

* **Azja i Pacyfik:** 20.40.125.208, 20.43.88.240, 20.184.58.62, 40.90.139.163, 104.44.89.44
* **Europa:** 40.90.138.4, 40.90.141.99, 51.105.170.64, 52.155.218.251
* **Ameryka Północna:** 40.90.139.36, 40.90.139.2, 40.119.2.134, 52.224.200.129, 52.249.207.163


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Tłumaczenie za zapory IP w wywołaniu interfejsu API translatora](reference/v3-0-translate.md)
