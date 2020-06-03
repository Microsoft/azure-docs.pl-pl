---
title: Dodawanie walidacji w poleceniach niestandardowych wersja zapoznawcza — usługa mowy
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak dodać walidacje do parametru polecenia w aplikacji poleceń niestandardowych w wersji zapoznawczej.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310414"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>Dodawanie walidacji do parametru polecenia w aplikacji poleceń niestandardowych w wersji zapoznawczej

W tym artykule dowiesz się, jak dodać walidacje do parametrów i wyświetlać instrukcje dla korekty.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w następujących artykułach:

> [!div class="checklist"]
 
> * [Szybki Start: Tworzenie aplikacji niestandardowych poleceń w wersji zapoznawczej](./quickstart-custom-speech-commands-create-new.md)
> * [Szybki Start: Tworzenie poleceń niestandardowych dla aplikacji w wersji zapoznawczej przy użyciu parametrów](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>Utwórz polecenie settemperaturę

Aby zademonstrować walidacje, Utwórz nowe polecenie, które umożliwia użytkownikom ustawianie temperatury.

1. W programie [Speech Studio](https://speech.microsoft.com/)Otwórz utworzoną przez siebie aplikację z poleceniami Custom Preview.
1. Utwórz nowe polecenie **Settemperaturę** .
1. Dodaj parametr temperatury, który ma następującą konfigurację:

   | Konfiguracja parametrów           | Sugerowana wartość    |Opis                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **Nazwa**              | **Temperatura**                       | Nazwa opisowa parametru                                |
   | **Wymagane**          | Zaznaczono                           | Pole wyboru wskazujące, czy przed ukończeniem polecenia jest wymagana wartość tego parametru |
   | **Odpowiedź dotycząca wymaganego parametru**     | **Prosty edytor — > jakiej temperatury chcesz?**  | Monit o podanie wartości tego parametru, gdy nie jest on znany |
   | **Typ**              | **Liczba**                            | Typ parametru, taki jak Number, String, DateTime lub geography   |

1. Dodaj weryfikację parametru temperatury.

    1. Na stronie Konfiguracja **parametrów** dla parametru temperatura wybierz pozycję **Dodaj weryfikację** w sekcji **walidacji** .

    1. W oknie podręcznym **nowe sprawdzanie poprawności** Skonfiguruj weryfikację w następujący sposób:
  
       | Konfiguracja parametrów         | Sugerowana wartość                                          | Opis                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **Wartość minimalna**        | **60**               | Dla parametrów liczbowych minimalna wartość, którą może przyjąć ten parametr |
       | **Wartość maksymalna**        | **80**               | Dla parametrów liczbowych wartość maksymalna, którą może przyjąć ten parametr |
       | **Niepowodzenie — prosty edytor**| **Pierwsza odmiana — Niestety, można ustawić tylko między 60 i 80 stopni**      | Monituj o podanie nowej wartości, Jeśli weryfikacja nie powiedzie się                                       |

       > [!div class="mx-imgBorder"]
       > ![Dodawanie walidacji zakresu](media/custom-speech-commands/validations-add-temperature.png)

1. Wybierz pozycję **Utwórz**.

1. Dodaj przykładowe zdania.

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. Dodaj regułę uzupełniania, która ma następującą konfigurację. Ta reguła potwierdza wynik.

   | Ustawienie    | Sugerowana wartość                                           |Opis                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | Nazwa       | Komunikat z potwierdzeniem                                      |Nazwa opisująca przeznaczenie reguły |
   | **Warunki** | **Parametry wymagane — temperatura**                       |Warunki określające, kiedy można uruchomić regułę    |   
   | **Akcje**    | **Wyślij odpowiedź na mowę — OK, ustawianie temperatury na {temperatura} stopni** | Akcja, która ma zostać podjęta po spełnieniu warunku reguły |

> [!TIP]
> Ten przykład używa odpowiedzi mowy, aby potwierdzić wynik. Przykłady wykonywania polecenia z akcją klienta znajdują się w temacie [How to: zrealizować polecenia na kliencie przy użyciu zestawu Speech SDK](./how-to-custom-speech-commands-fulfill-sdk.md).

## <a name="try-it-out"></a>Wypróbowywanie działania

1. Wybierz pozycję **uczenie**.

1. Po zakończeniu szkolenia wybierz pozycję **test**, a następnie spróbuj wykonać następujące interakcje:

    - Dane wejściowe: Ustaw temperaturę na 72 stopni
    - Wynik: OK, ustawianie temperatury na 72 stopni
    - Dane wejściowe: Ustaw temperaturę na 45 stopni
    - Wynik: Niestety, można ustawić tylko między 60 i 80 stopni
    - Dane wejściowe: Zwiększ do 72 stopni.
    - Wynik: OK, ustawianie temperatury na 72 stopni

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dodawanie potwierdzeń do polecenia w aplikacji polecenia niestandardowe w wersji zapoznawczej](./how-to-custom-speech-commands-confirmations.md)
