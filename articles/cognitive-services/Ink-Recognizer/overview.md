---
title: Co to jest aparat rozpoznawania atramentu? -Interfejs API rozpoznawania atramentu
titleSuffix: Azure Cognitive Services
description: Zintegruj aparat rozpoznawania farb z aplikacjami, witrynami sieci Web, narzędziami i innymi rozwiązaniami, aby umożliwić identyfikowanie i używanie danych pociągnięć odręcznych jako danych wejściowych.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: overview
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 6c1a720e7e9bd9c71f925f104ca7fc70a1a5ef59
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "89051070"
---
# <a name="what-is-the-ink-recognizer-api"></a>Co to jest interfejs API rozpoznawania pisma odręcznego?

[!INCLUDE [ink-recognizer-deprecation](includes/deprecation-note.md)]

Usługa poznawczego rozpoznawania pisma odręcznego zapewnia oparty na chmurze interfejs API REST umożliwiający analizowanie i rozpoznawanie zawartości cyfrowego atramentu. W przeciwieństwie do usług korzystających z optycznego rozpoznawania znaków (OCR), interfejs API wymaga cyfrowego pociągnięć atramentu jako danych wejściowych. Cyfrowe pociągnięcia atramentu to uporządkowane w czasie zestawy punktów 2D (współrzędne X, Y), które reprezentują ruch narzędzi wejściowych, takich jak pióra cyfrowe lub palca. Następnie rozpoznaje kształty i zawartość odręcznie z danych wejściowych i zwraca odpowiedź JSON zawierającą wszystkie rozpoznane jednostki.

![Schemat blokowy opisujący wysyłanie danych wejściowych pociągnięć odręcznych do interfejsu API](media/ink-recognizer-pen-graph.svg)

## <a name="features"></a>Funkcje

Za pomocą interfejsu API rozpoznawania pisma odręcznego można łatwo rozpoznać zawartość odręczną w aplikacjach. 

|Cecha  |Opis  |
|---------|---------|
| Rozpoznawanie pisma ręcznego | Rozpoznawanie zawartości napisanej ręcznie w [językach 63 i lokalnych](language-support.md). | 
| Rozpoznawanie układu | Uzyskaj informacje strukturalne na temat zawartości cyfrowego atramentu. Podziel zawartość na pisanie regionów, akapitów, linii, wyrazów, list punktowanych. Aplikacje mogą następnie używać informacji o układzie do tworzenia dodatkowych funkcji, takich jak automatyczne formatowanie listy i wyrównania kształtu. |
| Rozpoznawanie kształtów | Rozpoznawaj najczęściej używane [kształty geometryczne](concepts/send-ink-data.md#shapes-recognized-by-the-ink-recognizer-api) podczas sporządzania notatek. |
| Połączone kształty i rozpoznawanie tekstu | Rozpoznawaj, które pociągnięcia odręczne należą do kształtów lub zawartości napisanej ręcznie, i oddzielnie klasyfikowanie.|

## <a name="workflow"></a>Przepływ pracy

Interfejs API rozpoznawania atramentu to usługa sieci Web RESTful, ułatwiająca wywoływanie z dowolnego języka programowania, który może wykonywać żądania HTTP i analizować dane JSON.

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]

Po zarejestrowaniu:

1. Wypełnij dane pociągnięcia odręcznego i [sformatuj je](concepts/send-ink-data.md#sending-ink-data) w prawidłowym formacie JSON. Interfejs API akceptuje do 1500 pociągnięć odręcznych na żądanie. 
1. Wyślij żądanie do interfejsu API rozpoznawania pisma odręcznego z danymi.
1. Przetwórz odpowiedź interfejsu API, analizując zwrócony komunikat JSON.

## <a name="next-steps"></a>Następne kroki

Wypróbuj szybki start w następujących językach, aby rozpocząć wywoływanie interfejsu API rozpoznawania pisma odręcznego.
* [C#](quickstarts/csharp.md)
* [Java](quickstarts/java.md)
* [JavaScript](quickstarts/javascript.md)

Aby zobaczyć, jak działa interfejs API rozpoznawania pisma odręcznego w aplikacji do cyfrowego odkróla, zapoznaj się z następującymi przykładowymi aplikacjami w witrynie GitHub:
* [C# i platforma uniwersalna systemu Windows (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# i Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [Aplikacja języka JavaScript dla przeglądarki internetowej](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Aplikacja mobilna języka Java i systemu Android](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Aplikacja mobilna języka Swift i systemu iOS](https://go.microsoft.com/fwlink/?linkid=2089805)
