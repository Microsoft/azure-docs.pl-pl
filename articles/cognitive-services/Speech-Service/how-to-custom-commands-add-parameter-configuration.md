---
title: 'Instrukcje: Konfigurowanie parametru jako jednostki zewnętrznej'
titleSuffix: Azure Cognitive Services
description: W tym artykule wyjaśniono, jak skonfigurować parametr ciągu do odwoływania się do wykazu uwidocznionego za pośrednictwem punktu końcowego sieci Web.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
ms.openlocfilehash: 45dba3b7f46ec558c46b8505da26fd3ef4de4cbc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87284187"
---
# <a name="add-configurations-to-commands-parameters"></a>Dodawanie konfiguracji do parametrów poleceń

W tym artykule dowiesz się więcej na temat zaawansowanej konfiguracji parametrów, w tym:

 - Jak wartości parametrów mogą należeć do zestawu zdefiniowanego zewnętrznie do aplikacji poleceń niestandardowych
 - Jak dodać klauzule walidacji do wartości parametrów

## <a name="prerequisites"></a>Wymagania wstępne

Należy wykonać czynności opisane w następujących artykułach:

> [!div class="checklist"]
> * [Instrukcje: Tworzenie aplikacji przy użyciu prostych poleceń](./how-to-custom-commands-create-application-with-simple-commands.md)
> * [Instrukcje: Dodawanie parametrów do poleceń](./how-to-custom-commands-add-parameters-to-commands.md)


## <a name="configure-parameter-as-external-catalog-entity"></a>Konfigurowanie parametru jako jednostki zewnętrznego katalogu

W tej sekcji skonfigurujesz parametry typu String, aby odwoływać się do katalogów zewnętrznych hostowanych przez punkt końcowy sieci Web. Umożliwia to niezależne aktualizowanie wykazu zewnętrznego bez wprowadzania zmian w aplikacji poleceń niestandardowych. Takie podejście jest przydatne w przypadkach, w których wpisy katalogu mogą być duże w liczbie.

Ponownie Użyj parametru **SubjectDevice** z polecenia **TurnOnOff** . Bieżąca konfiguracja tego parametru **akceptuje wstępnie zdefiniowane dane wejściowe z wykazu wewnętrznego**. Odnosi się to do statycznej listy urządzeń, zgodnie z definicją w konfiguracji parametrów. Chcemy przenieść tę zawartość do zewnętrznego źródła danych, które można aktualizować niezależnie.

Aby to zrobić, Zacznij od dodania nowego punktu końcowego sieci Web. Przejdź do sekcji **punkty końcowe sieci Web** w lewym okienku i Dodaj nowy punkt końcowy sieci Web z następującą konfiguracją.

| Ustawienie | Sugerowana wartość |
|----|----|
| Nazwa | `getDevices` |
| Adres URL | `https://aka.ms/speech/cc-sampledevices` |
| Metoda | GET |


Jeśli Sugerowana wartość dla adresu URL nie działa prawidłowo, należy skonfigurować i hostować prosty internetowy punkt końcowy, który zwraca kod JSON zawierający listę urządzeń, które mogą być kontrolowane. Punkt końcowy sieci Web powinien zwrócić kod JSON sformatowany w następujący sposób:
    
```json
{
    "fan" : [],
    "refrigerator" : [
        "fridge"
    ],
    "lights" : [
        "bulb",
        "bulbs",
        "light"
        "light bulb"
    ],
    "tv" : [
        "telly",
        "television"
        ]
}

```


Następnie przejdź do strony ustawień parametrów **SubjectDevice** i zmień właściwości na następujące.

| Ustawienie | Sugerowana wartość |
| ----| ---- |
| Konfiguracja | Zaakceptuj wstępnie zdefiniowane dane wejściowe z wykazu zewnętrznego |                               
| Punkt końcowy katalogu | GetDevices |
| Metoda | GET |

Następnie wybierz pozycję **Zapisz**.

> [!IMPORTANT]
> Nie zostanie wyświetlona opcja konfigurowania parametru w celu akceptowania danych wejściowych z wykazu zewnętrznego, chyba że w sekcji **punkt końcowy sieci Web** w okienku po lewej stronie jest ustawiony punkt końcowy sieci Web.

### <a name="try-it-out"></a>Wypróbowywanie działania

Wybierz pozycję **uczenie** i poczekaj na zakończenie szkolenia. Po zakończeniu szkolenia wybierz pozycję **Testuj** i wypróbuj kilka interakcji.

* Dane wejściowe: Włącz
* Dane wyjściowe: urządzenie, które chcesz kontrolować?
* Dane wejściowe: sygnalizatory
* Wynik: OK, Włączanie świateł

> [!NOTE]
> Zwróć uwagę na to, jak można teraz kontrolować wszystkie urządzenia hostowane w punkcie końcowym sieci Web. Nadal konieczne jest nauczenie aplikacji do testowania nowych zmian i ponowne opublikowanie aplikacji.

## <a name="add-validation-to-parameters"></a>Dodawanie walidacji do parametrów

**Walidacje** to konstrukcje mające zastosowanie do niektórych typów parametrów, które umożliwiają konfigurowanie ograniczeń dotyczących wartości parametru i monitują o korektę, jeśli wartości nie mieszczą się w ograniczeniach. Aby uzyskać pełną listę typów parametrów rozszerzających konstrukcję walidacji, przejdź do pozycji [odwołania](./custom-commands-references.md)

Sprawdzanie poprawności przy użyciu polecenia **Settemperatura** . Aby dodać weryfikację parametru **temperatury** , wykonaj następujące czynności.

1. W lewym okienku wybierz polecenie **Settemperature** .
1. Wybierz pozycję  **temperatura** w środkowym okienku.
1. Wybierz pozycję **Dodaj weryfikację** obecną w okienku po prawej stronie.
1. W oknie **Nowa Walidacja** Skonfiguruj weryfikację w następujący sposób, a następnie wybierz pozycję **Utwórz**.


    | Konfiguracja parametrów | Sugerowana wartość | Opis |
    | ---- | ---- | ---- |
    | Wartość minimalna | `60` | Dla parametrów liczbowych minimalna wartość, którą może przyjąć ten parametr |
    | Wartość maksymalna | `80` | Dla parametrów liczbowych wartość maksymalna, którą może przyjąć ten parametr |
    | Niepowodzenie odpowiedzi |  Edytor prosty > pierwszej odmiany > `Sorry, I can only set temperature between 60 and 80 degrees. What temperature do you want?` | Monituj o podanie nowej wartości, Jeśli weryfikacja nie powiedzie się |

    > [!div class="mx-imgBorder"]
    > ![Dodawanie walidacji zakresu](media/custom-commands/add-validations-temperature.png)

### <a name="try-it-out"></a>Wypróbowywanie działania

1. Wybierz ikonę **szkolenia** znajdującą się u góry okienka po prawej stronie.

1. Po zakończeniu szkolenia wybierz pozycję **test** i spróbuj wykonać kilka działań:

    - Dane wejściowe: Ustaw temperaturę na 72 stopni
    - Wynik: OK, ustawianie temperatury na 72 stopni
    - Dane wejściowe: Ustaw temperaturę na 45 stopni
    - Wynik: Niestety, można ustawić tylko temperaturę między 60 i 80 stopni
    - Dane wejściowe: Zwiększ do 72 stopni.
    - Wynik: OK, ustawianie temperatury na 72 stopni

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Instrukcje: dodawanie reguł interakcji](./how-to-custom-commands-add-interaction-rules.md)
