---
title: Błędy stanu łącznika na pulpicie nawigacyjnym ITSMC
description: Informacje o typowych błędach istniejących na pulpicie nawigacyjnym łącznik zarządzania usługami IT.
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/18/2021
ms.openlocfilehash: 727e744c59d0a8d90cf320e1ee2e2a17e10ff847
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471524"
---
# <a name="connector-status-errors-in-the-itsmc-dashboard"></a>Błędy stanu łącznika na pulpicie nawigacyjnym ITSMC

Na pulpicie nawigacyjnym łącznik zarządzania usługami IT (ITSMC) są wyświetlane błędy, które mogą pomóc w rozwiązywaniu problemów z łącznikiem.

W poniższych sekcjach opisano typowe błędy, które pojawiają się w sekcji stan łącznika pulpitu nawigacyjnego i jak można je rozwiązać.

## <a name="unexpected-response"></a>Nieoczekiwana odpowiedź

**Błąd**: "Nieoczekiwana odpowiedź z usługi ServiceNow wraz z kodem stanu sukcesu. Odpowiedź: {"import_set": "{import_set_id}", "staging_table": "x_mioms_microsoft_oms_incident", "result": [{"transform_map": "zdarzenie pakietu OMS", "Tabela": "zdarzenie", "stan": "błąd", "error_message": "{docelowy rekord nie został znaleziony | Nieprawidłowa tabela | Nieprawidłowa tabela przemieszczania "}"

**Przyczyna**: usługi ServiceNow zwraca ten błąd, gdy:

* Skrypt niestandardowy wdrożony w wystąpieniu usługi ServiceNow powoduje ignorowanie zdarzeń.
* Kod "aplikacja integratora pakietu OMS" została zmodyfikowana po stronie usługi ServiceNow (na przykład za pomocą `onBefore` skryptu).

**Rozwiązanie**: Wyłącz wszystkie niestandardowe skrypty lub modyfikacje kodu.

## <a name="exception-update-failure"></a>Niepowodzenie aktualizacji wyjątku

**Błąd**: "{" Error ": {" Message ":" operacja nie powiodła się "," Detail ":" Aktualizacja wyjątku listy ACL nie powiodła się z powodu ograniczeń zabezpieczeń "}"

**Przyczyna**: uprawnienia usługi ServiceNow są błędnie skonfigurowane.

**Rozwiązanie**: Sprawdź, czy wszystkie role są prawidłowo przypisane zgodnie z [podanym](itsmc-connections-servicenow.md#install-the-user-app-and-create-the-user-role)opisem.

## <a name="problem-sending-a-request"></a>Wystąpił problem podczas wysyłania żądania

**Błąd**: "Wystąpił błąd podczas wysyłania żądania."

**Przyczyna**: wystąpienie usługi ServiceNow jest niedostępne.

**Rozwiązanie**: Sprawdź wystąpienie w usługi ServiceNow. Może być usunięty lub niedostępny.

## <a name="servicenow-rate-problem"></a>Problem z szybkością usługi ServiceNow

**Błąd**: "ServiceDeskHttpBadRequestException: StatusCode = 429"

**Przyczyna**: limity szybkości usługi ServiceNow są zbyt duże lub zbyt niskie.

**Rozwiązanie**: Zwiększ lub Anuluj limity szybkości w wystąpieniu usługi ServiceNow, zgodnie z opisem w [dokumentacji usługi ServiceNow](https://docs.servicenow.com/bundle/london-application-development/page/integrate/inbound-rest/task/investigate-rate-limit-violations.html).

## <a name="invalid-refresh-token"></a>Nieprawidłowy token odświeżania

**Błąd**: 
  * "AccessToken i RefreshToken są nieprawidłowe. Użytkownik musi ponownie przeprowadzić uwierzytelnienie ".
  * "Nie można zsynchronizować konfiguracji szablonów dla zdarzenia, alertu, zdarzenia. Aby uzyskać więcej informacji, zobacz komunikat o wyjątku. "

**Przyczyna**: token odświeżania wygasł.

**Rozwiązanie**: Sync ITSMC w celu wygenerowania nowego tokenu odświeżania, zgodnie z opisem w temacie [Ręczne rozwiązywanie problemów z synchronizacją](./itsmc-resync-servicenow.md).

## <a name="missing-connector"></a>Brak łącznika

**Błąd**: "nie można utworzyć/zaktualizować elementu pracy dla alertu {alertname}. Łącznik ITSM {connectionIdentifier} nie istnieje lub został usunięty. "

**Przyczyna**: ITSMC został usunięty.

**Rozwiązanie**: ITSMC został usunięty, ale zdefiniowane grupy akcji zarządzania usługami IT (narzędzia ITSM) nadal są z nim skojarzone. Istnieją trzy opcje rozwiązania tego problemu:

* Znajdowanie i wyłączanie lub usuwanie tych grup akcji.
* [Skonfiguruj ponownie grupy akcji](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts) , tak aby korzystały z istniejącego wystąpienia ITSMC.
* [Utwórz nowe wystąpienie ITSMC](./itsmc-definition.md#create-an-itsm-connection) i [ponownie skonfiguruj grupy akcji, aby z niego korzystać](itsmc-definition.md#create-itsm-work-items-from-azure-alerts).

## <a name="lack-of-connection-details"></a>Brak szczegółów połączenia

**Błąd**: "Wystąpił problem. Nie można pobrać szczegółów połączenia ". Ten błąd jest wyświetlany podczas definiowania grupy akcji narzędzia ITSM.

**Przyczyna**: ten błąd występuje w jednej z następujących sytuacji:

* Nowo utworzone wystąpienie łącznik ITSM nie zakończyło jeszcze synchronizacji początkowej.
* Łącznik nie został poprawnie zdefiniowany.

**Rozwiązanie**: 

* Po utworzeniu nowego wystąpienia ITSMC rozpocznie się synchronizowanie informacji z systemu narzędzia ITSM, takich jak szablony elementów roboczych i elementy robocze. [ITSMC synchronizacji w celu wygenerowania nowego tokenu odświeżania](./itsmc-resync-servicenow.md).
* [Przejrzyj szczegóły połączenia w ITSMC](./itsmc-connections-servicenow.md#create-a-connection) i sprawdź, czy ITSMC można pomyślnie [synchronizować](./itsmc-resync-servicenow.md).


## <a name="ip-restrictions"></a>Ograniczenia adresów IP
**Błąd**: "nie można dodać połączenia narzędzia ITSM o nazwie" XXX "z powodu nieprawidłowego żądania. Błąd: Nieprawidłowe żądanie. Podano nieprawidłowe parametry połączenia. Wyjątek http: Niedozwolony kod stanu. "

**Przyczyna**: adres IP aplikacji narzędzia ITSM nie zezwala na połączenia narzędzia ITSM z partnerów narzędzia ITSM Tools.

**Rozwiązanie**: Aby wyświetlić listę narzędzia ITSM adresów IP w celu zezwolenia na połączenia narzędzia ITSM z partnerów narzędzia ITSM, zalecamy wyświetlenie listy cały publiczny zakres adresów IP w regionie świadczenia usługi Azure, w którym należy do obszaru roboczego LogAnalytics. [Szczegóły tutaj](https://www.microsoft.com/download/details.aspx?id=56519) W przypadku regionów EUS/UZE/EUS2/WUS2/Południowo-środkowe stany USA klient może wyświetlić listę tylko tag sieci z akcją.
