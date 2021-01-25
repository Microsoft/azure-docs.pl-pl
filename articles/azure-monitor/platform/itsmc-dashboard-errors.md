---
title: Typowe błędy
description: Ten dokument zawiera informacje o typowych błędach istniejących na pulpicie nawigacyjnym
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: c3e6408a6a3473380edbe2966eba41970bd73562
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762768"
---
# <a name="errors-in-the-connector-status-section"></a>Błędy w sekcji stan łącznika

W sekcji Lista Stanów łącznika na pulpicie nawigacyjnym można znaleźć błędy, które mogą pomóc w rozwiązaniu problemów z łącznikiem narzędzia ITSM.

## <a name="status-common-errors"></a>Typowe błędy stanu

W tej sekcji znajdują się typowe błędy, które przedstawiono w sekcji stan łącznika i jak należy je rozwiązać:

* **Błąd**: "Nieoczekiwana odpowiedź z usługi ServiceNow wraz z kodem stanu sukcesu. Odpowiedź: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "zdarzenie pakietu OMS", "Tabela": "zdarzenie", "stan": "błąd", "error_message": "{docelowy rekord nie został znaleziony | Nieprawidłowa tabela | Nieprawidłowa tabela przemieszczania "}"

    **Przyczyna**: ten błąd jest zwracany z usługi ServiceNow, gdy:
  * Skrypt niestandardowy wdrożony w wystąpieniu usługi ServiceNow powoduje ignorowanie zdarzeń.
  * "Aplikacja integratora pakietu OMS" została zmodyfikowana po stronie usługi ServiceNow, np. za pomocą skryptu onbefore.

  **Rozwiązanie**: Wyłącz wszystkie niestandardowe skrypty lub modyfikacje kodu.

* **Błąd**: "{" Error ": {" Message ":" operacja nie powiodła się "," Detail ":" Aktualizacja wyjątku listy ACL nie powiodła się z powodu ograniczeń zabezpieczeń "}"

    **Przyczyna**: nieusługi servicenowa konfiguracja uprawnień

    **Rozwiązanie**: Sprawdź, czy wszystkie role zostały prawidłowo przypisane zgodnie z [podanym](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)opisem.

* **Błąd**: "Wystąpił błąd podczas wysyłania żądania."

    **Przyczyna**: "wystąpienie usługi ServiceNow niedostępne"

    **Rozwiązanie**: Sprawdź wystąpienie w usługi ServiceNow, które może zostać usunięte lub niedostępne.

* **Błąd**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

    **Przyczyna**: limity szybkości usługi ServiceNow są zbyt wysokie/niskie.

    **Rozwiązanie**: Zwiększ lub Anuluj limity szybkości w wystąpieniu usługi ServiceNow, jak wyjaśniono [tutaj](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

* **Błąd**: "AccessToken i RefreshToken są nieprawidłowe. Użytkownik musi ponownie przeprowadzić uwierzytelnienie ".

    **Przyczyna**: token odświeżania wygasł.

    **Rozwiązanie**: zsynchronizuj łącznik ITSM, aby wygenerować nowy token odświeżania, jak wyjaśniono [tutaj](./itsmc-resync-servicenow.md).

* **Błąd**: "nie można utworzyć/zaktualizować elementu pracy dla alertu {alertname}. Łącznik ITSM {connectionIdentifier} nie istnieje lub został usunięty. "

    **Przyczyna**: Łącznik ITSM został usunięty.

    **Rozwiązanie**: Łącznik ITSM zostało usunięte, ale nie zdefiniowano dla niej grup akcji narzędzia ITSM. Istnieją dwie opcje rozwiązania tego problemu:
  * Znajdowanie i wyłączanie lub usuwanie tych grup akcji
  * [Skonfiguruj ponownie grupę akcji](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) , aby użyć istniejącej łącznik ITSM.
  * [Utwórz nowy łącznik narzędzia ITSM](./itsmc-definition.md#create-an-itsm-connection) i [Skonfiguruj ponownie grupę akcji, aby z niej korzystać](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="ui-common-errors"></a>Typowe błędy interfejsu użytkownika

* **Błąd**: "Wystąpił problem. Nie można pobrać szczegółów połączenia ". Ten błąd jest wyświetlany, gdy Klient definiuje grupę akcji narzędzia ITSM.

    **Przyczyna**: nowo utworzona łącznik ITSM nie mogła ukończyć synchronizacji początkowej.

    **Rozwiązanie**: po utworzeniu nowego łącznika narzędzia ITSM łącznik ITSM zaczyna synchronizować informacje z systemu narzędzia ITSM, takie jak szablony elementów roboczych i elementy robocze. Zsynchronizuj łącznik ITSM, aby wygenerować nowy token odświeżania, jak wyjaśniono [tutaj](./itsmc-resync-servicenow.md).
