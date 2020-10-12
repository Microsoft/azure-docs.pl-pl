---
title: Dekodowanie komunikatów X12
description: Sprawdź poprawność EDI i Generuj potwierdzenia za pomocą dekodera komunikatów X12 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: 918516a5629f8570d54c641ffc29f2367937266f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792377"
---
# <a name="decode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Dekodowanie komunikatów X12 w usłudze Azure Logic Apps przy użyciu Pakietu integracyjnego dla przedsiębiorstw

Za pomocą łącznika dekodowania komunikatów X12 możesz walidować kopertę względem sprawdzać właściwości usługi EDI i partnerów, dzielić wymiany na zestawy transakcji lub zachowywać całe wymiany i generować potwierdzenia dla przetworzonych transakcji. Aby użyć tego łącznika, musisz dodać go do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Musisz mieć konto integracji, aby używać łącznika komunikatu X12 dekodowanie.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , które są już zdefiniowane na koncie integracji
* [Umowa X12](logic-apps-enterprise-integration-x12.md) , która jest już zdefiniowana na koncie integracji

## <a name="decode-x12-messages"></a>Dekodowanie komunikatów X12

1. [Utwórz aplikację logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik X12 komunikatów dekodowania nie ma wyzwalaczy, dlatego należy dodać wyzwalacz do uruchamiania aplikacji logiki, jak wyzwalacz żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie Dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź wartość "X12" dla filtra. Wybierz **X12 dekodowanie komunikatu X12**.
   
    ![Wyszukaj ciąg "X12"](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage1.png)  

3. Jeśli nie utworzono wcześniej żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia teraz. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć. 

    ![Podaj szczegóły połączenia z kontem integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage4.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę dla połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracji. Upewnij się, że Twoje konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.
   
    ![Szczegóły połączenia z kontem integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage5.png) 

6. Po utworzeniu połączenia, jak pokazano w tym przykładzie, wybierz komunikat pliku X12 Flat, aby zdekodować.

    ![utworzono połączenie z kontem integracji](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage6.png) 

    Na przykład:

    ![Wybieranie komunikatu prostego pliku X12 do dekodowania](media/logic-apps-enterprise-integration-x12-decode/x12decodeimage7.png) 

   > [!NOTE]
   > Rzeczywista zawartość komunikatów lub ładunek dla tablicy komunikatów, dobre lub złe, jest zakodowana w formacie base64. Dlatego należy określić wyrażenie, które przetwarza tę zawartość.
   > Oto przykład, który przetwarza zawartość jako XML, którą można wprowadzić w widoku kodu lub przy użyciu Konstruktora wyrażeń w projektancie.
   > ``` json
   > "content": "@xml(base64ToBinary(item()?['Payload']))"
   > ```
   > ![Przykład zawartości](media/logic-apps-enterprise-integration-x12-decode/content-example.png)
   >


## <a name="x12-decode-details"></a>Szczegóły dekodowania X12

Łącznik dekodowania X12 wykonuje następujące zadania:

* Sprawdza poprawność koperty pod kątem umowy z partnerem handlowym
* Sprawdza poprawność EDI i właściwości specyficzne dla partnera
  * Sprawdzanie poprawności strukturalnej EDI i rozszerzone sprawdzanie poprawności schematu
  * Weryfikacja struktury obwiedni wymiany.
  * Sprawdzanie poprawności schematu na kopercie względem schematu kontroli.
  * Sprawdzanie poprawności schematu dla elementów danych zestawu transakcji względem schematu komunikatów.
  * Sprawdzanie poprawności EDI dla elementów danych zestawu transakcji 
* Sprawdza, czy cyfry wymiany, grupy i kontroli zestawu transakcji nie są duplikatami
  * Sprawdza numer kontroli wymiany przed wcześniej odebranymi zmianami.
  * Sprawdza numer kontrolny grupy pod kątem innych numerów kontroli grupy w wymianie.
  * Sprawdza numer kontroli zestawu transakcji dla innych numerów kontroli zestawu transakcji w tej grupie.
* Dzieli wymianę na zestawy transakcji lub zachowuje całą transakcję wymiany:
  * Rozdziel wymianę jako zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu: dzieli wymianę na zestawy transakcji i analizuje poszczególne zestawy transakcji. 
  Akcja dekodowania X12 wyprowadza tylko te zestawy transakcji, których Walidacja nie powiodła się `badMessages` , i wyprowadza pozostałe zestawy transakcji do `goodMessages` .
  * Rozdziel wymianę jako zestawy transakcji — Zawieś wymianę w przypadku błędu: dzieli wymianę na zestawy transakcji i analizuje poszczególne zestawy transakcji. 
  Jeśli co najmniej jeden zestaw transakcji w ramach walidacji niepowodzenia wymiany, Akcja dekodowania X12 wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages` .
  * Zachowaj zestawy transakcji zawieszania wymiany w przypadku błędu: Zachowaj wymianę i przetwórz całą partię zbiorczą. 
  Akcja dekodowania X12 wyprowadza tylko te zestawy transakcji, których Walidacja nie powiodła się `badMessages` , i wyprowadza pozostałe zestawy transakcji do `goodMessages` .
  * Zachowaj wymianę — Wstrzymaj wymianę w przypadku błędu: Zachowaj wymianę i przetwórz całą partię zbiorczą. 
  Jeśli co najmniej jeden zestaw transakcji w ramach walidacji niepowodzenia wymiany, Akcja dekodowania X12 wyprowadza wszystkie zestawy transakcji w tej wymianie do `badMessages` . 
* Generuje potwierdzenie techniczne i/lub funkcjonalne (jeśli jest skonfigurowany).
  * Potwierdzenie techniczne generuje wynik weryfikacji nagłówka. Potwierdzenie techniczne raportuje stan przetwarzania nagłówka wymiany i przyczepy przez odbiorcę adresu.
  * Potwierdzenie funkcjonalne generuje wynik weryfikacji treści. Potwierdzenie funkcjonalne zgłasza każdy błąd napotkany podczas przetwarzania otrzymanego dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury Swagger
Zobacz [szczegóły struktury Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw") 

