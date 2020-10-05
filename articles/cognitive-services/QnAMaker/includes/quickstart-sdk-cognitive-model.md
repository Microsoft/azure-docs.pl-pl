---
ms.openlocfilehash: c87b9dc22ecd937abb27417aeddc1e30c0d724e7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85114554"
---

## <a name="using-this-example-knowledge-base"></a>Korzystanie z tej przykładowej bazy wiedzy

Baza wiedzy w tym przewodniku szybki start rozpoczyna się od 2 par QnA konwersacji. jest to wykonywane w celu uproszczenia tego przykładu i ma wysoce przewidywalne identyfikatory używane w metodzie Update, kojarząc monity z pytaniami dotyczącymi nowych par. To zostało zaplanowane i zaimplementowane w określonej kolejności dla tego przewodnika Szybki Start.

Jeśli planujesz opracowywać bazę wiedzy w czasie z monitami o kolejne instrukcje, które są zależne od istniejących par QnA, możesz wybrać następujące opcje:
* W przypadku większych bazy wiedzy Zarządzaj bazą wiedzy w edytorze tekstu lub w narzędziu TSV, które obsługują automatyzację, a następnie całkowicie Zastąp bazę wiedzy na raz z aktualizacją.
* W przypadku mniejszych bazy wiedzy Zarządzaj monitami z instrukcjami w całości w portalu QnA Maker.

Szczegóły dotyczące par QnA używanych w tym przewodniku szybki start:
* Typy pary QnA — w tej bazie wiedzy istnieją dwa typy par QnA, po aktualizacji: chitchat i informacje specyficzne dla domeny. Jest to typowe, jeśli baza danych jest powiązana z aplikacją do konwersacji, taką jak chatbot.
* Chociaż odpowiedzi bazy wiedzy mogą być filtrowane według metadanych lub używać monitów monitujące, ten przewodnik Szybki Start nie pokazuje tego. Zapoznaj się z tymi przykładami języka niezależny od generateAnswer [tutaj](../Quickstarts/get-answer-from-knowledge-base-using-url-tool.md).
* Tekst odpowiedzi jest w promocji i może zawierać [szeroką gamę promocji](../reference-markdown-format.md) , takich jak obrazy (publicznie dostępne obrazy internetowe), linki (do publicznie dostępnych adresów URL) i punkty punktowane, ten przewodnik Szybki Start nie używa tej odmiany.
