---
title: Weryfikowanie wystąpienia kontenera kondycji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera kondycji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91779802"
---
### <a name="verify-that-a-container-is-running"></a>Sprawdź, czy kontener jest uruchomiony

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki, a następnie wprowadź adres IP. Na przykład wpisz `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zostanie wyświetlona strona główna kontenera, która informuje o tym, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz link **opis interfejsu API usługi** , aby przejść do strony struktury Swagger kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API, a następnie wybierz opcję **Wypróbuj**. Wyświetlane są parametry, w tym przykładowe dane wejściowe.

Istnieje kilka adresów URL, których można także użyć do sprawdzenia, czy kontener jest uruchomiony.

|Żądanie|Przeznaczenie|
|--|--|
|`http://localhost:5000/`|Kontener zawiera stronę główną.|
|`http://localhost:5000/ready`|Zażądano przy użyciu GET, dzięki czemu można sprawdzić, czy kontener jest gotowy do akceptowania zapytania względem modelu. To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes.|
|`http://localhost:5000/status`|Żądanie GET, podobnie jak punkt końcowy/Ready, sprawdza, czy kontener jest uruchomiony bez ponoszenia zapytania względem modelu. To żądanie może służyć do [sondowania na żywo i gotowości](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)Kubernetes.|
|`http://localhost:5000/swagger`|Za pomocą tego adresu URL kontener zawiera pełen zestaw dokumentacji dla punktów końcowych i `Try it now` funkcji. Korzystając z tej funkcji, można wprowadzić ustawienia w formie HTML opartej na sieci Web i utworzyć zapytanie bez konieczności pisania kodu. Po powrocie zapytania zostanie podane przykładowe ZWINIĘCIE polecenia, aby przedstawić nagłówki HTTP i format treści, który jest wymagany. |
|`http://localhost:5000/demo`| Zażądano za pośrednictwem przeglądarki, ta funkcja zapewnia interaktywną wizualizację wyników zapytań dotyczących wejściowych próbek tekstowych lub jeden z nich.  |

Użyj tego adresu URL żądania — `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` w celu przesłania zapytania do kontenera.
