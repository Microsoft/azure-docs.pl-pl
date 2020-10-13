---
title: Azure Event Grid — Przewodnik rozwiązywania problemów
description: Ten artykuł zawiera listę kodów błędów, komunikatów o błędach, opisów i zalecanych akcji.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: ab52cea6ab43763cf2d9dc2b57b7f369072a399e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119042"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Rozwiązywanie problemów z błędami Azure Event Grid
Ten przewodnik rozwiązywania problemów zawiera listę Azure Event Grid kodów błędów, komunikatów o błędach, ich opisach oraz zalecanych czynnościach, które należy wykonać po otrzymaniu tych błędów. 

## <a name="error-code-400"></a>Kod błędu: 400
| Kod błędu | Komunikat o błędzie | Opis | Zalecenie |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nazwa tematu musi mieć długość od 3 do 50 znaków. | Nazwa tematu niestandardowego powinna mieć długość od 3 do 50 znaków. W nazwie tematu są dozwolone tylko litery alfanumeryczne, cyfry i znaki "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zarezerwowanych: <ul><li>Microsoft</li><li>EventGrid</li><li>System</li></ul> | Wybierz inną nazwę tematu, która jest zgodna z wymaganiami dotyczącymi nazwy tematu. |
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nazwa domeny musi mieć długość od 3 do 50 znaków. | Nazwa domeny powinna mieć długość od 3 do 50 znaków. W nazwie tematu są dozwolone tylko litery alfanumeryczne, cyfry i znaki "-". Ponadto nazwa nie powinna zaczynać się od następujących słów zarezerwowanych:<ul><li>Microsoft</li><li>EventGrid</li><li>System</li> | Wybierz inną nazwę domeny, która jest zgodna z wymaganiami dotyczącymi nazwy domeny. |
| HttpStatusCode. nieprawidłowego żądania<br/>400 | Nieprawidłowy czas wygaśnięcia. | Czas wygaśnięcia subskrypcji zdarzenia określa, kiedy subskrypcja zdarzeń zostanie wycofana. Ta wartość powinna być prawidłową wartością daty/godziny w przyszłości.| Upewnij się, że czas wygaśnięcia subskrypcji zdarzenia w prawidłowym formacie daty i godziny jest ustawiony na wartość w przyszłości. |

## <a name="error-code-409"></a>Kod błędu: 409
| Kod błędu | Komunikat o błędzie | Opis | Zalecana akcja |
| ---------- | ------------- | ----------- | -------------- | 
| HttpStatusCode. konflikt <br/>409 | Temat o podanej nazwie już istnieje. Wybierz inną nazwę tematu.   | Nazwa tematu niestandardowego powinna być unikatowa w jednym regionie świadczenia usługi Azure, aby zapewnić poprawną operację publikowania. Tej samej nazwy można używać w różnych regionach świadczenia usługi Azure. | Wybierz inną nazwę tematu. |
| HttpStatusCode. konflikt <br/> 409 | Domena o określonej już istnieje. Wybierz inną nazwę domeny. | Nazwa domeny powinna być unikatowa w jednym regionie świadczenia usługi Azure, aby zapewnić poprawną operację publikowania. Tej samej nazwy można używać w różnych regionach świadczenia usługi Azure. | Wybierz inną nazwę domeny. |
| HttpStatusCode. konflikt<br/>409 | Osiągnięto limit przydziału. Aby uzyskać więcej informacji na temat tych limitów, zobacz [limity Azure Event Grid](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits).  | Dla każdej subskrypcji platformy Azure obowiązuje limit liczby zasobów Azure Event Grid, których może używać. Przekroczono część lub wszystkie te limity przydziału i nie można utworzyć więcej zasobów. |    Sprawdź bieżące użycie zasobów i usuń te, które nie są potrzebne. Jeśli nadal trzeba zwiększyć limit przydziału, Wyślij wiadomość e-mail na adres [aeg@microsoft.com](mailto:aeg@microsoft.com) z dokładną liczbą potrzebnych zasobów. |

## <a name="error-code-403"></a>Kod błędu: 403

| Kod błędu | Komunikat o błędzie | Opis | Zalecana akcja |
| ---------- | ------------- | ----------- | ------------------ |
| HttpStatusCode. zabroniony <br/>403 | Publikowanie w programie {temat/domena} przez klienta {IpAddress} zostało odrzucone z powodu nieipaddress reguł filtrowania. | Temat lub domena ma skonfigurowane reguły zapory adresów IP i dostęp jest ograniczony tylko do skonfigurowanych adresów IP. | Dodawanie adresu IP do reguł zapory adresów IP, zobacz [Konfigurowanie zapory IP](configure-firewall.md) |
| HttpStatusCode. zabroniony <br/> 403 | Publikowanie w {temacie/Domain} przez klienta zostało odrzucone, ponieważ żądanie pochodzi z prywatnego punktu końcowego i nie znaleziono pasującego połączenia z prywatnym punktem końcowym dla tego zasobu. | Temat lub domena ma skonfigurowane prywatne punkty końcowe i żądanie publikowania pochodzi z prywatnego punktu końcowego, który nie jest skonfigurowany/zatwierdzony. | Skonfiguruj prywatny punkt końcowy dla tematu/domeny. [Konfigurowanie prywatnych punktów końcowych](configure-private-endpoints.md) |

## <a name="troubleshoot-event-subscription-validation"></a>Rozwiązywanie problemów z walidacją subskrypcji zdarzeń

W trakcie tworzenia subskrypcji zdarzeń, jeśli widzisz komunikat o błędzie, taki jak `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation` , oznacza to, że wystąpił błąd uzgadniania walidacji. Aby rozwiązać ten problem, sprawdź następujące aspekty:

- Wykonaj wpis HTTP w adresie URL elementu webhook za pomocą [przykładowej](webhook-event-delivery.md#validation-details) treści żądania SubscriptionValidationEvent przy użyciu elementu Poster lub zwinięcie lub podobnego narzędzia.
- Jeśli element webhook implementuje mechanizm uzgadniania synchronicznego walidacji, należy sprawdzić, czy ValidationCode jest zwracany jako część odpowiedzi.
- Jeśli element webhook implementuje mechanizm uzgadniania asynchronicznego walidacji, sprawdź, czy wpis HTTP jest zwracany 200 OK.
- Jeśli element webhook zwraca 403 (zabroniony) w odpowiedzi, sprawdź, czy element webhook znajduje się za usługą Azure Application Gateway lub zaporą aplikacji sieci Web. W przeciwnym razie należy wyłączyć te reguły zapory i ponownie wykonać POST protokołu HTTP:

  920300 (żądanie nie zawiera nagłówka Accept, możemy to naprawić)

  942430 (ograniczone wykrycie anomalii znaku SQL (args): Przekroczono liczbę znaków specjalnych (12))

  920230 (wykryto wielokrotne kodowanie adresu URL)

  942130 (atak wstrzykiwania kodu SQL: wykryto tautology SQL).

  931130 (możliwe, że ataku zdalnego dołączania plików (RFI) = Off-Domain odwołanie/łącze)


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy, Opublikuj swój problem na [forum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) lub Otwórz [bilet pomocy technicznej](https://azure.microsoft.com/support/options/). 
