---
title: plik dołączania
description: plik dołączania
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 9965e4c856fdef2af17b116264ad5344ebc97eb2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84466799"
---
Utwórz aplikację Pizza.

1. Wybierz pozycję [pizza-app-for-luis-v6.js](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) , aby wyświetlić stronę usługi GitHub dla tego `pizza-app-for-luis.json` pliku.
1. Kliknij prawym przyciskiem myszy lub naciśnij przycisk **RAW** , a następnie wybierz pozycję **Zapisz link jako** , aby zapisać `pizza-app-for-luis.json` na komputerze.
1. Zaloguj się do [portalu Luis](https://www.luis.ai).
1. Wybierz pozycję [Moje aplikacje](https://www.luis.ai/applications).
1. Na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**.
1. Wybierz pozycję **Importuj jako plik JSON**.
1. W oknie dialogowym **Importuj nową aplikację** wybierz przycisk **Wybierz plik** .
1. Wybierz `pizza-app-for-luis.json` pobrany plik, a następnie wybierz pozycję **Otwórz**.
1. W polu **Nazwa** okna dialogowego **Importowanie nowej aplikacji** wprowadź nazwę aplikacji Pizza, a następnie wybierz przycisk **gotowe** .

Aplikacja zostanie zaimportowana.

Jeśli zobaczysz okno dialogowe **jak utworzyć skuteczną aplikację Luis**, Zamknij okno dialogowe.

## <a name="train-and-publish-the-pizza-app"></a>Uczenie i publikowanie aplikacji Pizza

Na stronie **intencje** powinna zostać wyświetlona lista intencji w aplikacji Pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Dodawanie zasobu tworzenia do aplikacji Pizza

1. Wybierz pozycję **Zarządzaj**.
1. Wybierz pozycję **Azure Resources** (Zasoby platformy Azure).
1. Wybierz pozycję **zasób tworzenia**.
1. Wybierz pozycję **Zmień zasób tworzenia**.

Jeśli masz zasób autorstwa, wprowadź **nazwę dzierżawy**, **nazwę subskrypcji**i **nazwę zasobu Luis** zasobu tworzenia.

Jeśli nie masz zasobu do tworzenia:

1. Wybierz pozycję **Utwórz nowy zasób**.
1. Wprowadź nazwę **dzierżawy**, **nazwę zasobu**, **nazwę subskrypcji**i **nazwę grupy zasobów platformy Azure**.

Aplikacja Pizza jest teraz gotowa do użycia.

## <a name="record-the-access-values-for-your-pizza-app"></a>Rejestrowanie wartości dostępu dla aplikacji Pizza

Aby można było korzystać z nowej aplikacji Pizza, wymagany jest identyfikator aplikacji, klucz tworzenia i punkt końcowy tworzenia aplikacji Pizza.

Aby znaleźć te wartości:

1. Na stronie **intencje** wybierz pozycję **Zarządzaj**.
1. Na stronie **Ustawienia aplikacji** Zapisz **Identyfikator aplikacji**.
1. Wybierz pozycję **Azure Resources** (Zasoby platformy Azure).
1. Wybierz pozycję **zasób tworzenia**.
1. Na karcie **zasób tworzenia** Zapisz **klucz podstawowy**. Ta wartość jest kluczem tworzenia.
1. Zapisz **adres URL punktu końcowego**. Ta wartość jest punktem końcowym tworzenia.
