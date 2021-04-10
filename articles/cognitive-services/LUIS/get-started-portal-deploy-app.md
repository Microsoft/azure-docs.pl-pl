---
title: 'Szybki Start: wdrażanie aplikacji za pomocą portalu LUIS'
description: W tym przewodniku szybki start pokazano, jak wdrożyć aplikację, tworząc zasób punktu końcowego przewidywania, przypisując zasób, szkolenie i publikację aplikacji.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/29/2021
ms.openlocfilehash: f7d5d473b8fe24d8dd5018a718ea96f286b1c030
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106275842"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Szybki Start: wdrażanie aplikacji w portalu LUIS

Gdy aplikacja LUIS jest gotowa do zwrócenia prognoz wypowiedź do aplikacji klienckiej (na przykład bot rozmowy), należy wdrożyć aplikację w punkcie końcowym przewidywania.

W tym przewodniku szybki start dowiesz się, jak wdrożyć aplikację. Tworzysz zasób punktu końcowego przewidywania, przypiszesz zasób do aplikacji, nauczysz aplikację i opublikujesz aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

* Uzyskaj [subskrypcję platformy Azure](https://azure.microsoft.com/free).
* Wykonaj [poprzedni Przewodnik Szybki Start](get-started-portal-build-app.md) lub [Pobierz i zaimportuj aplikację](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/portal-build-app.json).
* Jeśli masz aplikacje, które mają przestarzałe uwierzytelnianie zasobów platformy Azure, [Migruj do zasobu platformy Azure](luis-migration-authoring.md). Niektóre strony portalu wyglądają inaczej, gdy obowiązuje uwierzytelnianie poczty e-mail.

<a name="create-the-endpoint-resource"></a>

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Przypisywanie klucza zasobu do aplikacji LUIS w portalu LUIS

Za każdym razem, gdy tworzysz nowy zasób do prognozowania lub tworzenia zapytania dla LUIS, musisz przypisać zasób do aplikacji LUIS. Po jego przypisaniu nie trzeba wykonać tego kroku ponownie, chyba że zostanie utworzony nowy zasób. Możesz utworzyć nowy zasób, aby rozszerzyć regiony aplikacji lub obsłużyć większą liczbę zapowiadanych zapytań.

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i wybierz aplikację **myEnglishApp** z listy aplikacji.

1. Wybierz pozycję **Zarządzaj** w prawym górnym menu, a następnie wybierz pozycję **zasoby platformy Azure**.

1. Aby dodać LUIS, wybierz pozycję **Dodaj zasób predykcyjny**.

    ![Aby dodać zasób przewidywania LUIS, wybierz pozycję Dodaj zasób predykcyjny](./media/get-started-portal-deploy-app/azure-resources-add-prediction-resource.png)

1. Wybierz dzierżawę, subskrypcję i nazwę zasobu. Wybierz pozycję **Przypisz zasób**.

   > [!div class="mx-imgBorder"]
   > ![Przypisywanie zasobu do aplikacji](./media/get-started-portal-deploy-app/assign-resource.png)

1. Wykonaj te same czynności, aby dodać klucz tworzenia do aplikacji.

1. Znajdź nowy wiersz w tabeli dla nowego zasobu predykcyjnego i skopiuj adres URL punktu końcowego. Jest on poprawnie skonstruowany w celu `HTTP GET` zgłoszenia do punktu końcowego interfejsu API Luis w celu przewidywania.

> [!TIP]
> Jeśli zamierzasz używać usługi Active Learning do ulepszania aplikacji LUIS, wybierz pozycję **Zmień parametry zapytania** i wybierz pozycję **Zapisz dzienniki**. Ta akcja powoduje zmianę przykładowego adresu URL przez dodanie `log=true` parametru QueryString. Skopiuj i użyj zmienionego przykładowego adresu URL zapytania podczas tworzenia zapytań prognozowania do punktu końcowego środowiska uruchomieniowego.

## <a name="train-the-app"></a>Uczenie aplikacji

Jeśli aplikacja została zmieniona od czasu jej ostatniego przeszkolonego, [pouczenie](get-started-portal-build-app.md) aplikacji.

## <a name="publish-the-app-to-the-prediction-endpoint"></a>Publikowanie aplikacji w punkcie końcowym przewidywania

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="prediction-endpoint-request"></a>Żądanie punktu końcowego przewidywania

Na `query=` końcu adresu URL w portalu jest dołączany wypowiedź użytkownika do żądania GET. Po `query=` wprowadzeniu tych samych użytkowników wypowiedź użytych na końcu poprzedniego przewodnika Szybki Start:

```Is there a form named hrf-234098```

Upewnij się, że ciąg zapytania zawiera następujące pary:

* `show-all-intents=true`
* `verbose=true`

W przeglądarce zostanie wyświetlona odpowiedź:

```JSON
{
    "query": "Is there a form named hrf-234098",
    "prediction": {
        "topIntent": "FindForm",
        "intents": {
            "FindForm": {
                "score": 0.9768753
            },
            "None": {
                "score": 0.0216071177
            }
        },
        "entities": {
            "Human Resources Form Number": [
                "hrf-234098"
            ],
            "$instance": {
                "Human Resources Form Number": [
                    {
                        "type": "Human Resources Form Number",
                        "text": "hrf-234098",
                        "startIndex": 22,
                        "length": 10,
                        "modelTypeId": 8,
                        "modelType": "Regex Entity Extractor",
                        "recognitionSources": [
                            "model"
                        ]
                    }
                ]
            }
        }
    }
}
```

Aby wyświetlić ten sam poziom informacji w okienku testów, należy opublikować aplikację. Po opublikowaniu aplikacji wybierz pozycję **Porównaj z opublikowanymi** w okienku testów. Aby wyświetlić ten sam kod JSON w poprzednim kroku, użyj **widoku Pokaż kod JSON** w okienku opublikowany test. W ten sposób można porównać zmiany bieżącej aplikacji, nad którą pracujesz z aplikacją opublikowaną w punkcie końcowym.

[![Porównanie obecnie edytowanej i opublikowanej wersji aplikacji](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu pracy z tym przewodnikiem Szybki Start wybierz pozycję **Moje aplikacje** w górnym menu nawigacji. Zaznacz pole wyboru aplikacji z listy, a następnie wybierz pozycję **Usuń** z paska narzędzi kontekstowego nad listą.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Iteracyjne opracowywanie aplikacji dla LUIS](./luis-concept-app-iteration.md)