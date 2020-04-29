---
title: Tworzenie niestandardowych słów kluczowych — usługa mowy
titleSuffix: Azure Cognitive Services
description: Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: trbye
ms.openlocfilehash: 8e67d624c77eb838f7646731bbdedd8f97f81b96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81400059"
---
# <a name="create-a-custom-keyword-using-speech-studio"></a>Tworzenie niestandardowego słowa kluczowego przy użyciu programu Speech Studio

Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Na przykład "Hey Cortana" to słowo kluczowe dla Asystenta Cortany. Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.

W tym artykule dowiesz się, jak utworzyć niestandardowe słowo kluczowe dla urządzenia.

## <a name="create-your-keyword"></a>Utwórz słowo kluczowe

Aby można było użyć niestandardowego słowa kluczowego, należy utworzyć słowo kluczowe przy użyciu strony [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) w programie [Speech Studio](https://aka.ms/sdsdk-speechportal). Po podaniu słowa kluczowego tworzy plik, który jest wdrażany na urządzeniu.

1. Przejdź do programu [Speech Studio](https://aka.ms/sdsdk-speechportal) i **Zaloguj się** lub, jeśli nie masz jeszcze subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stronie [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) Utwórz **Nowy projekt**. 

1. Wprowadź **nazwę**i opcjonalny **Opis**, a następnie wybierz język. Potrzebny będzie jeden projekt dla każdego języka i pomoc techniczna jest obecnie ograniczona do języka en-US.

    ![Opisz swój projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-new-project.png)

1. Wybierz swój projekt z listy. 

    ![Wybierz projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-project-list.png)

1. Aby rozpocząć nowy model słów kluczowych, kliknij pozycję **Testuj model**.

1. Wprowadź **nazwę** modelu słowa kluczowego i opcjonalny **Opis** , a następnie wpisz **słowo kluczowe** , a następnie kliknij przycisk **dalej**. Mamy pewne [wskazówki](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) ułatwiające wybór efektywnego słowa kluczowego.

    ![Wprowadź słowo kluczowe](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portal utworzy teraz wymowy kandydatów dla słowa kluczowego. Nasłuchiwanie poszczególnych kandydatów przez kliknięcie przycisku Odtwórz i usunięcie kontroli obok wszelkich wymowy, które są nieprawidłowe. Gdy sprawdzane są tylko dobre wymowy, kliknij pozycję **uczenie** , aby rozpocząć generowanie słowa kluczowego. 

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Wygenerowanie modelu może potrwać do 30 minut. Lista słów kluczowych zmieni się z **przetwarzania** na **powodzenie** , gdy model zostanie ukończony. Następnie można pobrać plik.

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-download-model.png)

1. Zapisz plik zip na komputerze. Ten plik będzie potrzebny do wdrożenia niestandardowego słowa kluczowego na urządzeniu.

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe przy użyciu [zestawu Speech Devices SDK szybkiego startu](https://aka.ms/sdsdk-quickstart).
