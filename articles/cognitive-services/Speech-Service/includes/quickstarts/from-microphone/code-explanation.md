---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400764"
---
Klucz i region subskrypcji usługi Speech są wymagane do utworzenia obiektu konfiguracji mowy. Obiekt konfiguracji jest wymagany do utworzenia wystąpienia obiektu aparatu rozpoznawania mowy.

Wystąpienie aparatu rozpoznawania udostępnia wiele metod rozpoznawania mowy. W tym przykładzie rozpoznawanie mowy jest rozpoznawane jeden raz. Ta funkcja umożliwia usłudze rozpoznawania mowy wysyłanie pojedynczej frazy do rozpoznawania oraz to, że po zidentyfikowaniu frazy do zatrzymania rozpoznawania mowy. Po uzyskaniu wyniku kod zapisze przyczynę rozpoznawania w konsoli programu.

> [!TIP]
> Zestaw Speech SDK będzie domyślnie rozpoznawał użycie `en-us` dla języka, zobacz [Określanie języka źródłowego dla zamiany mowy na tekst](../../../how-to-specify-source-language.md) , aby uzyskać informacje na temat wybierania języka źródłowego.