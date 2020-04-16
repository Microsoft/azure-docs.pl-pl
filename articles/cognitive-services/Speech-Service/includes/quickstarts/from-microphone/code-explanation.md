---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400764"
---
Klucz subskrypcji zasobów mowy i region są wymagane do utworzenia obiektu konfiguracji mowy. Obiekt konfiguracji jest potrzebny do wystąpienia obiektu rozpoznawania mowy.

Wystąpienie aparatu rozpoznawania udostępnia wiele sposobów rozpoznawania mowy. W tym przykładzie mowa jest rozpoznawana raz. Ta funkcja pozwala usługi mowy wiedzieć, że wysyłasz jedną frazę do rozpoznawania i że po zidentyfikowaniu frazy, aby zatrzymać rozpoznawanie mowy. Po uzyskaniu wyniku kod zapisze przyczynę rozpoznawania do konsoli.

> [!TIP]
> SDK mowy domyślnie rozpoznaje `en-us` za pomocą języka, zobacz [Określanie języka źródłowego mowy do tekstu, aby](../../../how-to-specify-source-language.md) uzyskać informacje na temat wybierania języka źródłowego.