---
title: Tworzenie niestandardowych słów kluczowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Gdy użytkownik mówi słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik przestanie mówić. Dostosowywanie słowa kluczowego to skuteczny sposób na odróżnienie urządzenia i wzmocnienie marki.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400059"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Tworzenie niestandardowego słowa kluczowego przy użyciu Programu Mowy

Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Na przykład "Hey Cortana" to słowo kluczowe dla asystenta Cortany. Gdy użytkownik mówi słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik przestanie mówić. Dostosowywanie słowa kluczowego to skuteczny sposób na odróżnienie urządzenia i wzmocnienie marki.

Z tego artykułu dowiesz się, jak utworzyć niestandardowe słowo kluczowe dla urządzenia.

## <a name="create-your-keyword"></a>Tworzenie słowa kluczowego

Aby można było użyć niestandardowego słowa kluczowego, musisz utworzyć słowo kluczowe za pomocą strony [Niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) w [Studiu mowy](https://aka.ms/sdsdk-speechportal). Po podaniem słowa kluczowego tworzy plik, który można wdrożyć na urządzeniu.

1. Przejdź do [Studia mowy](https://aka.ms/sdsdk-speechportal) i Zaloguj **się** lub, jeśli nie masz jeszcze subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stronie [Niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) utwórz **nowy projekt**. 

1. Wprowadź **nazwę**, opcjonalny **opis**i wybierz język. Będziesz potrzebował jednego projektu na język, a obsługa jest obecnie ograniczona do języka en-US.

    ![Opis projektu słowa kluczowego](media/custom-keyword/custom-kws-portal-new-project.png)

1. Wybierz projekt z listy. 

    ![Wybierz projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-project-list.png)

1. Aby uruchomić nowy model słowa kluczowego, kliknij **opcję Trenuj model**.

1. Wprowadź **nazwę** dla modelu słowa kluczowego oraz **opcjonalny opis** i wpisz wybrane **słowo kluczowe,** a następnie kliknij przycisk **Dalej**. Mamy kilka [wskazówek,](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) które pomogą wybrać skuteczne słowo kluczowe.

    ![Wpisz słowo kluczowe](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portal będzie teraz tworzyć wymowę kandydatów dla słowa kluczowego. Odsłuchaj każdego kandydata, klikając przyciski odtwarzania i usuń czeki obok wszystkich błędnych wymow. Po zaznaczeniu tylko dobrej wymowy kliknij przycisk **Trenuj,** aby rozpocząć generowanie słowa kluczowego. 

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Może upłynąć do trzydziestu minut dla modelu, które mają być generowane. Lista słów kluczowych zmieni się z **Przetwarzanie** na **Pomyślnie** po zakończeniu modelu. Następnie można pobrać plik.

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-download-model.png)

1. Zapisz plik zip na komputerze. Ten plik będzie potrzebny do wdrożenia niestandardowego słowa kluczowego na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe za pomocą narzędzia [SDK Szybki start funkcji Urządzenia mowy](https://aka.ms/sdsdk-quickstart).
