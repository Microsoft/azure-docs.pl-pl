---
title: Typowe kody błędów dla Instance Metadata Service platformy Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Przegląd typowych kodów błędów i odpowiednich metod zaradczych dla platformy Azure Instance Metadata Service (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519188"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Kody błędów: typowe kody błędów podczas korzystania z IMDS do pobierania informacji modułu równoważenia obciążenia

W tym artykule opisano typowe błędy wdrażania i sposoby rozwiązywania tych błędów podczas korzystania z usługi Azure Instance Metadata Service (IMDS).

## <a name="error-codes"></a>Kody błędów

| Kod błędu | Komunikat o błędzie | Szczegóły i środki zaradcze |
| --- | ---------- | ----------------- |
| 400 | Brak wymaganego parametru " \<ParameterName> ". Usuń żądanie i spróbuj ponownie. | Kod błędu wskazuje brak parametru. </br> Aby uzyskać więcej informacji na temat dodawania brakującego parametru, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | Wartość parametru jest niedozwolona lub wartość parametru " \<ParameterValue> " jest niedozwolona dla parametru "ParameterName". Usuń żądanie i spróbuj ponownie. | Kod błędu wskazuje, że format żądania nie jest prawidłowo skonfigurowany. </br> Aby uzyskać więcej informacji, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) w celu usunięcia treści żądania i wystawienia ponowienia próby. |
| 400 | Nieoczekiwane żądanie. Sprawdź parametry zapytania i spróbuj ponownie. | Kod błędu wskazuje, że format żądania nie jest prawidłowo skonfigurowany. </br> Aby uzyskać więcej informacji, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) w celu usunięcia treści żądania i wystawienia ponowienia próby. |
| 404 | Nie znaleziono metadanych modułu równoważenia obciążenia. Sprawdź, czy maszyna wirtualna korzysta z modułu równoważenia obciążenia innej niż podstawowa, i spróbuj ponownie później. | Kod błędu wskazuje, że maszyna wirtualna nie jest skojarzona z usługą równoważenia obciążenia, lub usługa równoważenia obciążenia jest podstawową jednostką SKU zamiast standardową. </br> Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie publicznego modułu równoważenia obciążenia w celu równoważenia obciążenia maszyn wirtualnych przy użyciu Azure Portal](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) do wdrożenia standardowego modułu równoważenia obciążenia.|
| 404 | Nie znaleziono interfejsu API: ścieżka = " \<UrlPath> ", Metoda = " \<Method> " | Kod błędu wskazuje błędną konfigurację ścieżki. </br> Aby uzyskać więcej informacji, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) w celu usunięcia treści żądania i wystawienia ponowienia próby.|
| 405 | Metoda http jest niedozwolona: ścieżka = " \<UrlPath> ", Metoda = " \<Method> " | Kod błędu wskazuje nieobsługiwane zlecenie HTTP. </br> Aby uzyskać więcej informacji, zobacz [Azure instance Metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) dla obsługiwanych zleceń. |
| 429 | Zbyt wiele żądań | Kod błędu wskazuje Limit szybkości. </br> Aby uzyskać więcej informacji na temat ograniczania szybkości, zobacz [Azure instance Metadata Service (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | Treść żądania jest większa niż MaxBodyLength:... | Kod błędu wskazuje żądanie większe niż MaxBodyLength. </br> Aby uzyskać więcej informacji na temat długości treści, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Długość klucza parametru jest większa niż MaxParameterKeyLength:... | Kod błędu wskazuje długość klucza parametru większego niż MaxParameterKeyLength. </br> Aby uzyskać więcej informacji na temat długości treści, zobacz [jak pobrać metadane modułu równoważenia obciążenia za pomocą usługi Azure instance Metadata Service (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | Długość wartości parametru jest większa niż MaxParameterValueLength:... | Kod błędu wskazuje długość klucza parametru większego niż MaxParameterValueLength. </br> Aby uzyskać więcej informacji na temat długości wartości, zobacz [jak pobrać metadane usługi równoważenia obciążenia za pomocą instance Metadata Service platformy Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | Długość wartości nagłówka parametru jest większa niż MaxHeaderValueLength:... | Kod błędu wskazuje długość wartości nagłówka parametru, która jest większa niż MaxHeaderValueLength. </br> Aby uzyskać więcej informacji na temat długości wartości, zobacz [jak pobrać metadane usługi równoważenia obciążenia za pomocą instance Metadata Service platformy Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | Interfejs API metadanych Load Balancer nie jest teraz dostępny. Spróbuj ponownie później | Kod błędu wskazuje, że interfejs API może być aprowizacji. Spróbuj ponowić żądanie później. |
| 404 | /Metadata/loadbalancer nie jest obecnie dostępna | Kod błędu wskazuje, że interfejs API jest w toku. Spróbuj ponowić żądanie później. |
| 503 | Usługa wewnętrzna jest niedostępna. Spróbuj ponownie później  | Kod błędu wskazuje, że interfejs API jest tymczasowo niedostępny. Spróbuj ponowić żądanie później. |
|  |  |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [usłudze Azure instance Metadata Service](../virtual-machines/windows/instance-metadata-service.md)

