---
title: Tworzenie słowa kluczowego szybkiego startu — usługa mowy
titleSuffix: Azure Cognitive Services
description: Urządzenie zawsze nasłuchuje słowa kluczowego (lub frazy). Gdy użytkownik wyświetla słowo kluczowe, urządzenie wysyła wszystkie kolejne audio do chmury, dopóki użytkownik nie przestanie mówić. Dostosowanie słowa kluczowego jest skutecznym sposobem na odróżnienie urządzenia i wzmocnienie oznakowania.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021953"
---
# <a name="get-started-with-custom-keyword"></a>Wprowadzenie do Niestandardowego słowa kluczowego

W tym przewodniku szybki start przedstawiono podstawowe informacje dotyczące pracy z niestandardowymi słowami kluczowymi przy użyciu programu Speech Studio i zestawu Speech SDK. Słowo kluczowe jest słowem lub krótką frazą umożliwiającą produktowi aktywację głosu. Można tworzyć modele słów kluczowych w programie Speech Studio, a następnie eksportować plik modelu używany z zestawem Speech SDK w aplikacjach.

## <a name="prerequisites"></a>Wymagania wstępne

Kroki opisane w tym artykule wymagają subskrypcji mowy i zestawu Speech SDK. Jeśli nie masz jeszcze subskrypcji, [Wypróbuj usługę mowy bezpłatnie](overview.md#try-the-speech-service-for-free). Aby uzyskać zestaw SDK, zobacz [Przewodnik instalacji](quickstarts/setup-platform.md) dla danej platformy.

## <a name="create-a-keyword-in-speech-studio"></a>Tworzenie słowa kluczowego w programie Speech Studio

Aby można było użyć niestandardowego słowa kluczowego, należy utworzyć słowo kluczowe przy użyciu strony [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) w programie [Speech Studio](https://aka.ms/sdsdk-speechportal). Po podaniu słowa kluczowego tworzy `.table` plik, którego można użyć z zestawem Speech SDK.

> [!IMPORTANT]
> Niestandardowe modele słów kluczowych i pliki powstałe `.table` mogą być tworzone **tylko** w programie Speech Studio.
> Nie można tworzyć niestandardowych słów kluczowych z zestawu SDK lub z wywołaniami REST.

1. Przejdź do programu [Speech Studio](https://aka.ms/sdsdk-speechportal) i **Zaloguj się** lub, jeśli nie masz jeszcze subskrypcji mowy, wybierz pozycję [**Utwórz subskrypcję**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Na stronie [niestandardowe słowo kluczowe](https://aka.ms/sdsdk-wakewordportal) Utwórz **Nowy projekt**. 

1. Wprowadź **nazwę** i opcjonalny **Opis**, a następnie wybierz język. Potrzebny jest jeden projekt dla każdego języka, a obsługa jest obecnie ograniczona do `en-US` języka.

    ![Opisz swój projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-new-project.png)

1. Wybierz swój projekt z listy. 

    ![Wybierz projekt słowa kluczowego](media/custom-keyword/custom-kws-portal-project-list.png)

1. Aby utworzyć nowy model słów kluczowych, kliknij pozycję **uczenie modelu**.

1. Wprowadź **nazwę** modelu, opcjonalny **Opis** i **słowo kluczowe** , a następnie kliknij przycisk **dalej**. Zapoznaj się ze [wskazówkami](./custom-keyword-overview.md#choose-an-effective-keyword) dotyczącymi wybierania obowiązującego słowa kluczowego.

    ![Wprowadź słowo kluczowe](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portal tworzy wymowy kandydatów dla słowa kluczowego. Nasłuchiwanie poszczególnych kandydatów przez kliknięcie przycisku Odtwórz i usunięcie kontroli obok wszelkich wymowy, które są nieprawidłowe. Gdy sprawdzane są tylko dobre wymowy, kliknij pozycję **uczenie** , aby rozpocząć generowanie modelu słowa kluczowego. 

    ![Zrzut ekranu pokazujący, gdzie wybierasz poprawną pronounciations.](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Wygenerowanie modelu może potrwać do 30 minut. Lista słów kluczowych zmieni się z **przetwarzania** na **powodzenie** , gdy model zostanie ukończony. Następnie można pobrać plik.

    ![Przejrzyj słowo kluczowe](media/custom-keyword/custom-kws-portal-download-model.png)

1. Pobrany plik jest `.zip` archiwum. Wyodrębnij archiwum i zobaczysz plik z `.table` rozszerzeniem. Jest to plik używany z zestawem SDK w następnej sekcji, dlatego należy zwrócić uwagę na jego ścieżkę. Nazwa pliku odzwierciedla nazwę słowa kluczowego, na przykład słowo kluczowe **Activate urządzenia** ma nazwę pliku `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Używanie modelu słów kluczowych z zestawem SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Następne kroki

Przetestuj niestandardowe słowo kluczowe przy użyciu [zestawu Speech Devices SDK szybkiego startu](./speech-devices-sdk-quickstart.md?pivots=platform-android).