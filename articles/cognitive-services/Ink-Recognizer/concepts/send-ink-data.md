---
title: Wysyłanie danych pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego
titleSuffix: Azure Cognitive Services
description: Dowiedz się więcej o wywoływaniu interfejsu API analizatora farb dla różnych aplikacji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: aahi
ms.openlocfilehash: 9ecf3ad43b46d491d5f15270790251a0bc4e882e
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365367"
---
# <a name="send-ink-data-to-the-ink-recognizer-api"></a>Wysyłanie danych pisma odręcznego do interfejsu API rozpoznawania pisma odręcznego 

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

Cyfrowe pismo odręczne odnosi się do technologii, które umożliwiają tworzenie cyfrowych reprezentacji danych wejściowych, takich jak pismo ręczne i rysunki. Jest to zazwyczaj realizowane przy użyciu dyskretyzatora, który przechwytuje ruchy urządzeń wejściowych, takich jak pióro. Urządzenia udostępniają zaawansowane środowiska cyfrowego pisma odręcznego, dlatego sztuczna inteligencja i systemy uczenia maszynowego umożliwiają rozpoznawanie napisanych kształtów i tekstu w dowolnym kontekście. Interfejs API rozpoznawania pisma odręcznego umożliwia wysyłanie pociągnięć odręcznych i uzyskiwanie szczegółowych informacji o nich. 

## <a name="the-ink-recognizer-api-vs-ocr-services"></a>Interfejs API rozpoznawania atramentu i usługi OCR

Interfejs API rozpoznawania pisma odręcznego nie używa optycznego rozpoznawania znaków (OCR). Usługa OCR przetwarza dane pikseli z obrazów w celu zapewnienia rozpoznawania pisma ręcznego i tekstu. Jest to czasami nazywane rozpoznawaniem w trybie offline. Zamiast tego interfejs API rozpoznawania atramentu wymaga danych cyfrowych pociągnięć odręcznych, które są przechwytywane jako urządzenie wejściowe. Przetwarzanie danych cyfrowych atramentu w ten sposób może dawać dokładniejsze wyniki rozpoznawania w porównaniu z usługami OCR. 

## <a name="sending-ink-data"></a>Wysyłanie danych atramentu

Interfejs API rozpoznawania pisma odręcznego wymaga współrzędnych X i Y, które reprezentują pociągnięcia odręczne utworzone przez urządzenie wejściowe, od momentu dotknięcia powierzchni wykrywania do momentu jego zniesienia. Punkty każdego pociągnięcia muszą być ciągiem wartości rozdzielanych przecinkami i być sformatowane w formacie JSON, jak w poniższym przykładzie. Ponadto każde pociągnięcie atramentu musi mieć unikatowy identyfikator w każdym żądaniu. Jeśli identyfikator powtarza się w ramach tego samego żądania, interfejs API zwróci błąd. Aby uzyskać najbardziej dokładne wyniki rozpoznawania, należy mieć co najmniej osiem cyfr po przecinku dziesiętnym. Miejsce pochodzenie (0, 0) kanwy jest w lewym górnym rogu kanwy pisma odręcznego.

> [!NOTE]
> Poniższy przykład nie jest prawidłowym kodem JSON. W usłudze [GitHub](https://go.microsoft.com/fwlink/?linkid=2089909)można znaleźć żądanie pełnego aparatu rozpoznawania pisma odręcznego.
 
```json
{
  "language": "en-US",
  "strokes": [
   {
    "id": 43,
    "points": 
        "5.1365, 12.3845,
        4.9534, 12.1301,
        4.8618, 12.1199,
        4.7906, 12.2217,
        4.7906, 12.5372,
        4.8211, 12.9849,
        4.9534, 13.6667,
        5.0958, 14.4503,
        5.3299, 15.2441,
        5.6555, 16.0480,
        ..."
   },
    ...
  ]
}
```

## <a name="ink-recognizer-response"></a>Odpowiedź rozpoznawania atramentu

Interfejs API rozpoznawania pisma odręcznego zwraca odpowiedź analizy na temat obiektów rozpoznawanych przez zawartość odręczną. Odpowiedź zawiera jednostki rozpoznawania opisujące relacje między różnymi pociągnięciami odręcznymi. Na przykład, pociągnięcia, które tworzą odrębne, oddzielne kształty będą zawarte w różnych jednostkach. Każda jednostka zawiera szczegółowe informacje na temat pociągnięć odręcznych, w tym rozpoznanego obiektu, jego współrzędne i inne atrybuty rysunku.

## <a name="shapes-recognized-by-the-ink-recognizer-api"></a>Kształty rozpoznawane przez interfejs API rozpoznawania pisma odręcznego

Interfejs API rozpoznawania pisma odręcznego może identyfikować najczęściej używane kształty w notatce. Na poniższym obrazie przedstawiono niektóre podstawowe przykłady. Aby uzyskać pełną listę kształtów i inne treści odręczne rozpoznawane przez interfejs API, zobacz [artykuł dotyczący interfejsu API](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer). 

![Lista kształtów rozpoznanych przez interfejs API rozpoznawania pisma odręcznego](../media/shapes.png)

## <a name="recommended-calling-patterns"></a>Zalecane wzorce wywoływania

Interfejs API REST aparatu rozpoznawania atramentu można wywołać w różnych wzorcach zgodnie z aplikacją. 

### <a name="user-initiated-api-calls"></a>Wywołania interfejsu API zainicjowane przez użytkownika

Jeśli tworzysz aplikację, która korzysta z danych wejściowych użytkownika (na przykład aplikacja pobierająca lub Adnotacja), możesz chcieć dać im kontrolę nad tym, kiedy i które pismo odręczne zostanie wysłane do interfejsu API rozpoznawania pisma odręcznego. Ta funkcja jest szczególnie przydatna, gdy tekst i kształty są obecne na kanwie, a użytkownicy chcą wykonywać różne akcje dla każdego z nich. Rozważ dodanie funkcji wyboru (takich jak Lasso lub inne narzędzie do zaznaczania geometrycznego), które umożliwiają użytkownikom wybranie elementów wysyłanych do interfejsu API.  

### <a name="app-initiated-api-calls"></a>Zainicjowane przez aplikacje wywołania interfejsu API

Możesz również mieć aplikację wywołującą interfejs API rozpoznawania pisma odręcznego po upływie limitu czasu. Wysyłając bieżące pociągnięcia odręczne do interfejsu API rutynowo, można przechowywać wyniki rozpoznawania w miarę ich tworzenia podczas poprawiania czasu odpowiedzi interfejsu API. Na przykład można wysłać wiersz tekstu odręcznego do interfejsu API po wykryciu przez niego użytkownika. 

Posiadanie wyników rozpoznawania zapewnia informacje o cechach pociągnięć odręcznych, które są ze sobą powiązane. Na przykład, które pociągnięcia są pogrupowane w celu utworzenia tego samego wyrazu, wiersza, listy, akapitu lub kształtu. Te informacje mogą ulepszyć funkcje wyboru odręcznego aplikacji przez możliwość wyboru grup pociągnięć jednocześnie.

## <a name="integrate-the-ink-recognizer-api-with-windows-ink"></a>Integrowanie interfejsu API rozpoznawania pisma odręcznego z programem Windows Ink

Program [Windows Ink](/windows/uwp/design/input/pen-and-stylus-interactions) udostępnia narzędzia i technologie umożliwiające obsługę cyfrowego pisma odręcznego na różnych urządzeniach. Możesz połączyć platformę Windows Ink z interfejsem API rozpoznawania pisma odręcznego, aby tworzyć aplikacje, które wyświetlają i interpretują cyfrowe pociągnięcia atramentu.

## <a name="next-steps"></a>Następne kroki

* [Co to jest interfejs API rozpoznawania pisma odręcznego?](../overview.md)
* [Dokumentacja interfejsu API REST aparatu rozpoznawania atramentu](/rest/api/cognitiveservices/inkrecognizer/inkrecognizer)

* Rozpocznij wysyłanie danych pociągnięcia cyfrowego atramentu przy użyciu:
    * [C#](../quickstarts/csharp.md)
    * [Java](../quickstarts/java.md)
    * [JavaScript](../quickstarts/javascript.md)