---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.openlocfilehash: 1509d175979bb65c467424db5de967f56825a3f9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545544"
---
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie jest znacząca i czy nie jest odpowiednia dla aplikacji. Zamiara brak jest dodawana do każdej aplikacji w ramach procesu tworzenia, aby określić, czy dla aplikacji klienckiej **nie** powinien odpowiadać wypowiedź.

Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację lub dostarczyć więcej wskazówek pozwalających kontynuować konwersację.

W przypadku pozostawienia pustego zamiaru **wartość** wypowiedź, która powinna zostać przewidywalna poza domenę podmiotu, zostanie przewidywalna w jednym z istniejących intencji domeny podmiotu. W efekcie aplikacja kliencka, taka jak rozmowa bot, wykona nieprawidłowe operacje w oparciu o niewłaściwe prognozowanie.

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

1. Wybierz intencję **None**. Dodaj trzy wyrażenia długości, które użytkownik może wprowadzić, ale które nie są istotne dla aplikacji Pizza Order:

    |`None` przykład wyrażenia długości|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    W tych przykładach nie należy używać wyrazów oczekiwanych w domenie podmiotu, takich jak `pizza` ,, `cheese` `crust` , `pickup` `deliver` .