---
title: Dekodowanie komunikatów EDIFACT
description: Sprawdź poprawność EDI i Generuj potwierdzenia przy użyciu dekodera komunikatów EDIFACT dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b36641677dbf36402c7f578b9b1887c52f441afd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91322482"
---
# <a name="decode-edifact-messages-for-azure-logic-apps-with-the-enterprise-integration-pack"></a>Dekodowanie komunikatów EDIFACT dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Za pomocą łącznika komunikatów EDIFACT możesz sprawdzać właściwości usługi EDI i partnerów, dzielić wymiany na zestawy transakcji lub zachowywać całe wymiany i generować potwierdzenia dla przetworzonych transakcji. Aby użyć tego łącznika, musisz dodać go do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Musisz mieć konto integracji, aby używać łącznika komunikatu EDIFACT dekodowanie. 
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , które są już zdefiniowane na koncie integracji
* [Umowa EDIFACT](logic-apps-enterprise-integration-edifact.md) , która jest już zdefiniowana na koncie integracji

## <a name="decode-edifact-messages"></a>Dekodowanie komunikatów EDIFACT

> [!IMPORTANT]
> Łącznik EDIFACT obsługuje tylko znaki UTF-8.
> Jeśli dane wyjściowe zawierają nieoczekiwane znaki, sprawdź, czy w komunikatach EDIFACT jest używany zestaw znaków UTF-8. 

1. [Utwórz aplikację logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik EDIFACT komunikatów dekodowania nie ma wyzwalaczy, dlatego należy dodać wyzwalacz do uruchamiania aplikacji logiki, jak wyzwalacz żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie Dodaj akcję do aplikacji logiki.

3. W polu wyszukiwania wprowadź ciąg "EDIFACT" jako filtr. Wybierz opcję **Dekoduj komunikat EDIFACT**.
   
    ![Wyszukaj EDIFACT](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage1.png)

3. Jeśli nie utworzono wcześniej żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia teraz. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć.
   
    ![Utwórz konto integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage2.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę dla połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracji. Upewnij się, że Twoje konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

4. Po zakończeniu tworzenia połączenia wybierz pozycję **Utwórz**. Szczegóły połączenia powinny wyglądać podobnie do tego przykładu:

    ![Szczegóły konta integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage3.png)  

5. Po utworzeniu połączenia, jak pokazano w tym przykładzie, wybierz komunikat pliku EDIFACT Flat, aby zdekodować.

    ![utworzono połączenie z kontem integracji](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage4.png)  

    Na przykład:

    ![Wybieranie komunikatu prostego pliku EDIFACT do dekodowania](./media/logic-apps-enterprise-integration-edifact-decode/edifactdecodeimage5.png)  

## <a name="edifact-decoder-details"></a>Szczegółowe informacje na temat dekodera EDIFACT

Łącznik EDIFACT dekodowanie wykonuje następujące zadania: 

* Sprawdza poprawność koperty względem umowy partnera handlowego.
* Rozwiązuje umowę przez dopasowanie kwalifikatora nadawcy & identyfikator i kwalifikator odbiorcy & identyfikator.
* Dzieli wymianę na wiele transakcji, gdy wymiana zawiera więcej niż jedną transakcję na podstawie konfiguracji ustawień odbioru umowy.
* Demontażuje wymianę.
* Sprawdza poprawność EDI i właściwości specyficzne dla partnera, w tym:
  * Weryfikacja struktury obwiedni wymiany
  * Sprawdzanie poprawności schematu na kopercie względem schematu kontroli
  * Sprawdzanie poprawności schematu dla elementów danych zestawu transakcji względem schematu komunikatów
  * Sprawdzanie poprawności EDI dla elementów danych zestawu transakcji
* Sprawdza, czy cyfry wymiany, grupy i kontroli zestawu transakcji nie są duplikatami (jeśli są skonfigurowane) 
  * Sprawdza numer kontroli wymiany przed wcześniej odebranymi zmianami. 
  * Sprawdza numer kontrolny grupy pod kątem innych numerów kontroli grupy w wymianie. 
  * Sprawdza numer kontroli zestawu transakcji dla innych numerów kontroli zestawu transakcji w tej grupie.
* Dzieli wymianę na zestawy transakcji lub zachowuje całą transakcję wymiany:
  * Rozdziel wymianę jako zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu: dzieli wymianę na zestawy transakcji i analizuje poszczególne zestawy transakcji. 
  Akcja dekodowania EDIFACT wyprowadza tylko te zestawy transakcji, których Walidacja nie powiodła się `badMessages` , i wyprowadza pozostałe zestawy transakcji do `goodMessages` .
  * Rozdziel wymianę jako zestawy transakcji — Zawieś wymianę w przypadku błędu: dzieli wymianę na zestawy transakcji i analizuje poszczególne zestawy transakcji. 
  Jeśli co najmniej jeden zestaw transakcji w ramach walidacji niepowodzenia wymiany, Akcja dekodowania EDIFACT wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages` .
  * Zachowaj zestawy transakcji zawieszania wymiany w przypadku błędu: Zachowaj wymianę i przetwórz całą partię zbiorczą. 
  Akcja dekodowania EDIFACT wyprowadza tylko te zestawy transakcji, których Walidacja nie powiodła się `badMessages` , i wyprowadza pozostałe zestawy transakcji do `goodMessages` .
  * Zachowaj wymianę — Wstrzymaj wymianę w przypadku błędu: Zachowaj wymianę i przetwórz całą partię zbiorczą. 
  Jeśli co najmniej jeden zestaw transakcji w ramach walidacji niepowodzenia wymiany, Akcja dekodowania EDIFACT wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages` .
* Generuje techniczne (kontrolkę) i/lub potwierdzenie funkcjonalności (jeśli są skonfigurowane).
  * Potwierdzenie techniczne lub potwierdzenie CONTRL raportują wyniki sprawdzania składniowego kompletnej otrzymanej wymiany.
  * Potwierdzenie funkcjonalne potwierdza akceptowanie lub odrzucanie odebranej wymiany lub grupy

## <a name="view-swagger-file"></a>Wyświetl plik Swagger
Aby wyświetlić szczegóły struktury Swagger dla łącznika EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw") 

