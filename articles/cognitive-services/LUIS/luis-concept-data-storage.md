---
title: Magazyn danych — LUIS
titleSuffix: Azure Cognitive Services
description: LUIS przechowuje dane zaszyfrowane w magazynie danych platformy Azure odpowiadające regionowi określonemu przez klucz.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: a58bcff4e39c4a4a907cd8567b47b074ff299bd5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97008456"
---
# <a name="data-storage-and-removal-in-language-understanding-luis-cognitive-services"></a>Magazynowanie i usuwanie danych w Language Understanding (LUIS) Cognitive Services

LUIS przechowuje dane zaszyfrowane w magazynie danych platformy Azure odpowiadające [regionowi](luis-reference-regions.md) określonemu przez klucz. 

* Dane używane do uczenia modelu, takie jak jednostki, intencje i wyrażenia długości, zostaną zapisane w LUIS przez okres istnienia aplikacji. Jeśli właściciel lub współautor usunie aplikację, te dane zostaną z niej usunięte. Jeśli aplikacja nie została użyta w ciągu 90 dni, zostanie usunięta. 

* Autorzy aplikacji mogą [włączyć rejestrowanie](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning) na wyrażenia długości, które są wysyłane do opublikowanej aplikacji. Jeśli ta funkcja jest włączona, wyrażenia długości będzie zapisywana przez 30 dni i może być wyświetlana przez autora aplikacji. Jeśli rejestrowanie nie jest włączone po opublikowaniu aplikacji, te dane nie są przechowywane.

## <a name="export-and-delete-app"></a>Eksportuj i usuwaj aplikację
Użytkownicy mają pełną kontrolę nad [eksportowaniem](luis-how-to-start-new-app.md#export-app) i [usuwaniem](luis-how-to-start-new-app.md#delete-app) aplikacji. 

## <a name="utterances"></a>Wypowiedzi

Wyrażenia długości mogą być przechowywane w dwóch różnych miejscach. 

* Podczas **procesu tworzenia** wyrażenia długości są tworzone i przechowywane w zamierzeniu. Wyrażenia długości w intencjach są wymagane do pomyślnej aplikacji LUIS. Gdy aplikacja zostanie opublikowana i otrzyma zapytania w punkcie końcowym, ciąg QueryString żądania punktu końcowego `log=false` określa, czy punkt końcowy wypowiedź jest przechowywany. Jeśli punkt końcowy jest przechowywany, stał się częścią aktywnego uczenia wyrażenia długości, którą można znaleźć w sekcji **kompilacja** portalu, w sekcji **Przegląd wyrażenia długości punktu końcowego** . 
* Gdy **przeglądasz punkt końcowy wyrażenia długości** i dodasz wypowiedź do celu, wypowiedź nie jest już przechowywana jako część punktu końcowego wyrażenia długości do zweryfikowania. Jest on dodawany do intencji aplikacji. 

<a name="utterances-in-an-intent"></a>

### <a name="delete-example-utterances-from-an-intent"></a>Usuń przykład wyrażenia długości z zamiaru

Usuń przykład wyrażenia długości używany do uczenia [Luis](luis-reference-regions.md). Jeśli usuniesz przykład wypowiedź z aplikacji LUIS, zostanie on usunięty z usługi sieci Web LUIS i jest niedostępny do eksportowania.

<a name="utterances-in-review"></a>

### <a name="delete-utterances-in-review-from-active-learning"></a>Usuń wyrażenia długości z aktywnego uczenia

Wyrażenia długości można usunąć z listy wyrażenia długości użytkownika, którą LUIS sugeruje na **[stronie Recenzja punktu końcowego wyrażenia długości](luis-how-to-review-endpoint-utterances.md)**. Usunięcie wyrażenia długości z tej listy uniemożliwi ich sugerowanie, ale nie usunie ich z dzienników.

Jeśli nie chcesz, aby usługa Active Learning wyrażenia długości, możesz [wyłączyć aktywną naukę](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Wyłączenie usługi Active Learning powoduje także wyłączenie rejestrowania.

### <a name="disable-logging-utterances"></a>Wyłącz rejestrowanie wyrażenia długości
[Wyłączenie aktywnego uczenia](luis-how-to-review-endpoint-utterances.md#disable-active-learning) powoduje wyłączenie rejestrowania.


<a name="accounts"></a>

## <a name="delete-an-account"></a>Usuwanie konta
Jeśli nie przeprowadzono migracji, możesz usunąć konto i wszystkie Twoje aplikacje zostaną usunięte wraz z przykładami wyrażenia długości i dzienników. Dane są przechowywane przez 90 dni, zanim konto i dane zostaną trwale usunięte.

Usuwanie konta jest dostępne na stronie **Ustawienia** . Wybierz nazwę swojego konta w prawym górnym pasku nawigacyjnym, aby przejść do strony **ustawień** .

## <a name="delete-an-authoring-resource"></a>Usuwanie zasobu tworzenia
Jeśli [przeprowadzono migrację do zasobu tworzenia](./luis-migration-authoring.md), usunięcie samego zasobu z Azure Portal spowoduje usunięcie wszystkich aplikacji skojarzonych z tym zasobem wraz z przykładami wyrażenia długości i dzienników. Dane są przechowywane przez 90 dni przed ich trwałe usunięciem.    

Aby usunąć zasób, przejdź do [Azure Portal](https://ms.portal.azure.com/#home) i wybierz zasób autorstwa Luis. Przejdź do karty **Przegląd** , a następnie kliknij przycisk **Usuń** w górnej części strony. Następnie potwierdź, że zasób został usunięty. 

## <a name="data-inactivity-as-an-expired-subscription"></a>Nieaktywność danych jako wygasła subskrypcja
Na potrzeby przechowywania i usuwania danych nieaktywna aplikacja LUIS może być traktowana jako niezależna od _firmy Microsoft_ jako wygasła subskrypcja. Aplikacja jest traktowana jako nieaktywna, jeśli spełnia następujące kryteria dla ostatnich 90 dni: 

* Nie ma **żadnych** wywołań.
* Nie został zmodyfikowany.
* Nie ma przypisanego bieżącego klucza.
* Użytkownik nie ma do niego logowania.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat eksportowania i usuwania aplikacji](luis-how-to-start-new-app.md)