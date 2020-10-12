---
author: IEvangelist
ms.author: dapine
ms.date: 02/19/2020
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2ac93f5aba722eea78267a512999a5581a887b99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77474129"
---
Zapytania do kontenera są rozliczane w warstwie cenowej zasobu platformy Azure, który jest używany przez `ApiKey` .

Kontenery usługi Azure Cognitive Services nie są licencjonowane do uruchamiania bez połączenia z punktem końcowym pomiaru/rozliczeń. Należy włączyć kontenery do przekazywania informacji rozliczeniowych za pomocą punktu końcowego rozliczeń. Kontenery Cognitive Services nie wysyłają danych klienta, takich jak obraz lub tekst, który jest analizowany, do firmy Microsoft.

### <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure

Kontener wymaga wartości argumentu rozliczenia do uruchomienia. Te wartości umożliwiają kontenerowi nawiązanie połączenia z punktem końcowym rozliczeń. Kontener zgłasza użycie co 10 do 15 minut. Jeśli kontener nie łączy się z platformą Azure w dozwolonym przedziale czasu, kontener nadal działa, ale nie obsługuje zapytań do momentu przywrócenia punktu końcowego rozliczania. Próbuje nawiązać połączenie 10 razy w tym samym przedziale czasu wynoszącym od 10 do 15 minut. Jeśli nie można nawiązać połączenia z punktem końcowym rozliczeń w 10 próbach, kontener przestanie obsługiwać żądania.

### <a name="billing-arguments"></a>Argumenty rozliczeń

Polecenie spowoduje uruchomienie kontenera, gdy wszystkie trzy z poniższych opcji zostaną dostarczone z prawidłowymi wartościami: <a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank"> `docker run` <span class="docon docon-navigate-external x-hidden-focus"></span> </a>

| Opcja | Opis |
|--------|-------------|
| `ApiKey` | Klucz interfejsu API zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na klucz interfejsu API dla zasobu aprowizacji określonego w `Billing` . |
| `Billing` | Punkt końcowy zasobu Cognitive Services, który służy do śledzenia informacji dotyczących rozliczeń.<br/>Wartość tej opcji musi być ustawiona na identyfikator URI punktu końcowego zainicjowanego zasobu platformy Azure.|
| `Eula` | Oznacza zaakceptowanie licencji dla kontenera.<br/>Wartość tej opcji musi być ustawiona na **Zaakceptuj**. |
