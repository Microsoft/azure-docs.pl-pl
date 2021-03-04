---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032939"
---
Zapytania do kontenera są rozliczane w warstwie cenowej zasobu platformy Azure, który jest używany przez `ApiKey` .

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z punktem końcowym pomiaru/rozliczeń. Należy włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą punktu końcowego rozliczeń. Kontenery Cognitive Services nie wysyłają danych klienta, takich jak obraz lub tekst, który jest analizowany, do firmy Microsoft.

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener wymaga wartości argumentu rozliczenia do uruchomienia. Te wartości umożliwiają kontenerowi nawiązanie połączenia z punktem końcowym rozliczeń. Kontener zgłasza użycie co 10 do 15 minut. Jeśli kontener nie łączy się z platformą Azure w dozwolonym przedziale czasu, kontener nadal działa, ale nie obsługuje zapytań do momentu przywrócenia punktu końcowego rozliczania. Próbuje nawiązać połączenie 10 razy w tym samym przedziale czasu wynoszącym od 10 do 15 minut. Jeśli nie można nawiązać połączenia z punktem końcowym rozliczeń w 10 próbach, kontener przestanie obsługiwać żądania. Zapoznaj się z [kontenerem Cognitive Services — często zadawane pytania](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work) dotyczące przykładu informacji wysyłanych do firmy Microsoft w celu rozliczenia.

### <a name="billing-arguments"></a>Argumenty rozliczeń

Polecenie spowoduje uruchomienie kontenera, gdy wszystkie trzy z poniższych opcji zostaną dostarczone z prawidłowymi wartościami: <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na klucz interfejsu API dla zasobu aprowizacji określonego w `Billing` . |
| `Billing` | Punkt końcowy zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na identyfikator URI punktu końcowego zainicjowanego zasobu platformy Azure.|
| `Eula` | Oznacza zaakceptowanie licencji dla kontenera.<br/>Wartość tej opcji musi być ustawiona na **Zaakceptuj**. |
