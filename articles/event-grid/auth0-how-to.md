---
title: Jak wysyłać zdarzenia z rozwiązanie Auth0 do platformy Azure przy użyciu Azure Event Grid
description: Jak kończyć zdarzenia z rozwiązanie Auth0 do usług platformy Azure za pomocą Azure Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: a66a60cb926b933a6b0628a67506d0d52ab7a905
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077867"
---
# <a name="integrate-azure-event-grid-with-auth0"></a>Integracja Azure Event Grid z usługą rozwiązanie Auth0

W tym artykule opisano sposób łączenia rozwiązanie Auth0 i kont platformy Azure przez utworzenie tematu partnera Event Grid.

Zobacz [kody typów zdarzeń rozwiązanie Auth0](https://auth0.com/docs/logs/references/log-event-type-codes) , aby uzyskać pełną listę zdarzeń obsługiwanych przez rozwiązanie Auth0

## <a name="send-events-from-auth0-to-azure-event-grid"></a>Wysyłaj zdarzenia z rozwiązanie Auth0 do Azure Event Grid
Aby wysłać zdarzenia rozwiązanie Auth0 do platformy Azure:

1. Włączanie dostawcy zasobów usługi Event Grid
1. Konfigurowanie tematu partnera rozwiązanie Auth0 na pulpicie nawigacyjnym rozwiązanie Auth0
1. Aktywowanie tematu partnera na platformie Azure
1. Subskrybowanie zdarzeń z rozwiązanie Auth0

Aby uzyskać więcej informacji na temat tych pojęć, zobacz temat Event Grid [pojęcia](concepts.md).

### <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid
Jeśli wcześniej nie korzystano z Event Grid, należy zarejestrować dostawcę zasobów Event Grid. Jeśli wcześniej używasz Event Grid, przejdź do następnej sekcji.

W witrynie Azure Portal:
1. Z menu po lewej stronie wybierz pozycję subskrypcje
1. Wybierz subskrypcję używaną dla Event Grid
1. W menu po lewej stronie w obszarze Ustawienia wybierz pozycję dostawcy zasobów.
1. Znajdź Microsoft. EventGrid
1. Wybierz pozycję Zarejestruj
1. Odśwież, aby upewnić się, że stan zmieni się na zarejestrowano

### <a name="set-up-an-auth0-partner-topic"></a>Konfigurowanie tematu partnera rozwiązanie Auth0
Częścią procesu integracji jest skonfigurowanie rozwiązanie Auth0 do użycia jako źródło zdarzenia (ten krok odbywa się na [pulpicie nawigacyjnym rozwiązanie Auth0](https://manage.auth0.com/)).

1. Zaloguj się do [pulpitu nawigacyjnego rozwiązanie Auth0](https://manage.auth0.com/).
1. Przejdź do dzienników > strumienie.
1. Kliknij pozycję + Utwórz strumień.
1. Wybierz Azure Event Grid i wprowadź unikatową nazwę nowego strumienia.
1. Utwórz źródło zdarzenia, podając Identyfikator subskrypcji platformy Azure, region platformy Azure i nazwę grupy zasobów. 
1. Kliknij pozycję Zapisz.

### <a name="activate-your-auth0-partner-topic-in-azure"></a>Aktywuj temat partnera rozwiązanie Auth0 na platformie Azure
Aktywowanie tematu rozwiązanie Auth0 na platformie Azure pozwala na przepływ zdarzeń z rozwiązanie Auth0 do platformy Azure.

1. Zaloguj się w witrynie Azure Portal.
1. Wyszukaj `Partner Topics` u góry i kliknij `Event Grid Partner Topics` w obszarze usługi.
1. Kliknij temat pasujący do strumienia utworzonego na pulpicie nawigacyjnym rozwiązanie Auth0.
1. Potwierdź, że `Source` pole jest zgodne z kontem rozwiązanie Auth0.
1. Kliknij pozycję Aktywuj.

### <a name="subscribe-to-auth0-events"></a>Subskrybowanie do zdarzeń usługi Auth0

#### <a name="create-an-event-handler"></a>Utwórz procedurę obsługi zdarzeń
W celu przetestowania tematu partnera potrzebna jest procedura obsługi zdarzeń. Przejdź do subskrypcji platformy Azure i uruchom usługę, która jest obsługiwana jako [program obsługi zdarzeń](event-handlers.md) , taki jak [Funkcja platformy Azure](custom-event-to-function.md).

#### <a name="subscribe-to-your-auth0-partner-topic"></a>Subskrybowanie partnera usługi rozwiązanie Auth0
Subskrybowanie tematu partnerskiego rozwiązanie Auth0 umożliwia poinformowanie Event Grid o miejscu, w którym ma się znaleźć Twoje zdarzenia rozwiązanie Auth0.

1. W bloku tematu partnera dla integracji z programem rozwiązanie Auth0 wybierz pozycję + subskrypcja zdarzeń u góry.
1. Na stronie Tworzenie subskrypcji zdarzeń:
    1. Wprowadź nazwę subskrypcji zdarzeń.
    1. Wybierz usługę platformy Azure lub element webhook, który został utworzony dla typu punktu końcowego.
    1. Postępuj zgodnie z instrukcjami dotyczącymi określonej usługi.
    1. Kliknij pozycję Utwórz.

## <a name="testing"></a>Testowanie
Integracja tematu partnera rozwiązanie Auth0 z platformą Azure powinna być gotowa do użycia.

### <a name="verify-the-integration"></a>Weryfikowanie integracji
Aby sprawdzić, czy integracja działa zgodnie z oczekiwaniami:

1. Zaloguj się do pulpitu nawigacyjnego rozwiązanie Auth0.
1. Przejdź do dzienników > strumienie.
1. Kliknij strumień Event Grid.
1. W strumieniu kliknij kartę Kondycja. Strumień powinien być aktywny i dopóki nie widzisz żadnych błędów, strumień działa.

Spróbuj [wywołać dowolną akcję rozwiązanie Auth0, która wyzwala zdarzenie do opublikowania](https://auth0.com/docs/logs/references/log-event-type-codes) , aby zobaczyć przepływ zdarzeń.

## <a name="delivery-attempts-and-retries"></a>Próby dostarczenia i ponowne próby
Zdarzenia rozwiązanie Auth0 są dostarczane do platformy Azure za pośrednictwem mechanizmu przesyłania strumieniowego. Każde zdarzenie jest wysyłane, gdy zostanie wyzwolone w rozwiązanie Auth0. Jeśli Event Grid nie może odebrać zdarzenia, rozwiązanie Auth0 ponowi próbę w celu dostarczenia zdarzenia. W przeciwnym razie rozwiązanie Auth0 będzie rejestrował niepowodzenie dostarczania w systemie.

## <a name="next-steps"></a>Następne kroki

- [Rozwiązanie Auth0 partnera](auth0-overview.md)
- [Przegląd tematów dotyczących partnerów](partner-events-overview.md)
- [Zostań partnerem Event Grid](partner-onboarding-overview.md)