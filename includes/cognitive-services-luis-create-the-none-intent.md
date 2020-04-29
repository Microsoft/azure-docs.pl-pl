---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67183539"
---
Aplikacja kliencka musi wiedzieć, czy wypowiedź nie jest znacząca i czy nie jest odpowiednia dla aplikacji. Intencja **None** jest dodawana do każdej aplikacji w ramach procesu tworzenia, co pozwala określić, czy aplikacja kliencka może odpowiedzieć na wypowiedź.

Jeśli usługa LUIS zwraca intencję wypowiedzi **None**, aplikacja kliencka może zapytać, czy użytkownik chce zakończyć konwersację lub dostarczyć więcej wskazówek pozwalających kontynuować konwersację. 

> [!CAUTION] 
> Intencji **None** nie należy pozostawiać pustej. 

1. Wybierz pozycję **Intents** (Intencje) na lewym panelu.

2. Wybierz intencję **None**. Dodaj trzy wypowiedzi, które może wprowadzić użytkownik, ale które nie są istotne dla tej aplikacji Human Resources:

    | Przykładowe wypowiedzi|
    |--|
    |Barking dogs are annoying (Szczekające psy są irytujące)|
    |Order a pizza for me (Zamów dla mnie pizzę)|
    |Penguins in the ocean (Pingwiny w oceanie)|
