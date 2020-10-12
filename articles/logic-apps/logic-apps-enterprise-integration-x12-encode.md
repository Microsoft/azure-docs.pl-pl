---
title: Kodowanie komunikatów X12
description: Weryfikowanie EDI i konwertowanie komunikatów zakodowanych w formacie XML za pomocą kodera komunikatów X12 w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 01/27/2017
ms.openlocfilehash: c87a61dc77e656a1cfe667ce87f852303a0cc486
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792351"
---
# <a name="encode-x12-messages-in-azure-logic-apps-with-enterprise-integration-pack"></a>Kodowanie komunikatów X12 w usłudze Azure Logic Apps przy użyciu Pakietu integracyjnego dla przedsiębiorstw

Za pomocą łącznika kodowania komunikatów X12 można walidować właściwości specyficzne dla standardu EDI i partnera, konwertować komunikaty kodowane w formacie XML na zestawy transakcji EDI w ramach wymiany oraz żądać potwierdzenia technicznego, potwierdzenia funkcjonalności lub obu tych potwierdzeń.
Aby użyć tego łącznika, musisz dodać go do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Musisz mieć konto integracji, aby używać łącznika wiadomości X12.
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , które są już zdefiniowane na koncie integracji
* [Umowa X12](logic-apps-enterprise-integration-x12.md) , która jest już zdefiniowana na koncie integracji

## <a name="encode-x12-messages"></a>Kodowanie komunikatów X12

1. [Utwórz aplikację logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik X12 komunikatów kodowania nie ma wyzwalaczy, dlatego należy dodać wyzwalacz do uruchamiania aplikacji logiki, jak wyzwalacz żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie Dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź wartość "X12" dla filtra. Wybierz opcję **X12-encode, aby X12 wiadomość według nazwy umowy** lub **X12-Encode na komunikat X12 przez tożsamość**.
   
    ![Wyszukaj ciąg "X12"](./media/logic-apps-enterprise-integration-x12-encode/x12decodeimage1.png) 

3. Jeśli nie utworzono wcześniej żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia teraz. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć. 
   
    ![połączenie z kontem integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage1.png)

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę dla połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracji. Upewnij się, że Twoje konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.

    ![utworzono połączenie z kontem integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage2.png)

    Połączenie zostało utworzone.

    ![Szczegóły połączenia z kontem integracji](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage3.png) 

#### <a name="encode-x12-messages-by-agreement-name"></a>Koduj komunikaty X12 według nazwy umowy

W przypadku wybrania kodowania komunikatów X12 według nazwy umowy Otwórz nazwę listy **umów X12** , wprowadź lub wybierz istniejącą umowę X12. Wprowadź komunikat XML do zakodowania.

![Wprowadź nazwę umowy X12 i komunikat XML do zakodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage4.png)

#### <a name="encode-x12-messages-by-identities"></a>Koduj komunikaty X12 według tożsamości

W przypadku wybrania kodowania komunikatów X12 według tożsamości wprowadź identyfikator nadawcy, kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy zgodnie z konfiguracją w umowie X12. Wybierz komunikat XML do zakodowania.
   
![Podaj tożsamości dla nadawcy i odbiornika, wybierz wiadomość XML do zakodowania](./media/logic-apps-enterprise-integration-x12-encode/x12encodeimage5.png) 

## <a name="x12-encode-details"></a>Szczegóły kodowania X12

Łącznik kodowania X12 wykonuje następujące zadania:

* Rozwiązywanie umowy przez dopasowanie właściwości kontekstu nadawcy i odbiorcy.
* Serializacja wymiany EDI, konwertowanie komunikatów zakodowanych w formacie XML na zestawy transakcji EDI w wymianie.
* Stosuje nagłówek i segmenty naczepy zestawu transakcji
* Generuje numer kontrolny wymiany, numer kontrolny grupy i numer kontrolny zestawu transakcji dla każdej wychodzącej wymiany
* Zamienia separatory w danych ładunku
* Sprawdza poprawność EDI i właściwości specyficzne dla partnera
  * Sprawdzanie poprawności schematu dla elementów danych zestawu transakcji względem schematu komunikatów
  * Sprawdzanie poprawności EDI wykonane dla elementów danych zestawu transakcji.
  * Przeprowadzono rozszerzoną weryfikację dla elementów danych zestawu transakcji
* Żąda potwierdzeń technicznych i/lub funkcjonalnych (jeśli zostały skonfigurowane).
  * Potwierdzenie techniczne generuje wynik weryfikacji nagłówka. Potwierdzenie techniczne zgłasza stan przetwarzania nagłówka wymiany i przyczepy przez odbiorcę adresu
  * Potwierdzenie funkcjonalne generuje wynik weryfikacji treści. Potwierdzenie funkcjonalne zgłasza każdy błąd napotkany podczas przetwarzania otrzymanego dokumentu

## <a name="view-the-swagger"></a>Wyświetlanie struktury Swagger
Zobacz [szczegóły struktury Swagger](/connectors/x12/). 

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw") 

