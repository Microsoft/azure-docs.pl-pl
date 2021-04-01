---
title: Kodowanie komunikatów EDIFACT
description: Sprawdź poprawność EDI i Generuj XML za pomocą kodera komunikatów EDIFACT dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, divswa, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: cee97fddc619840de821ebb70d32e0cab1bbe040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82106529"
---
# <a name="encode-edifact-messages-for-azure-logic-apps-with-enterprise-integration-pack"></a>Koduj komunikaty EDIFACT dla Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Za pomocą łącznika kodowania komunikatów EDIFACT można walidować właściwości specyficzne dla standardu EDI i partnera, generować dokument XML dla każdego zestawu transakcji oraz żądać potwierdzenia technicznego, potwierdzenia funkcjonalności lub obu tych potwierdzeń.
Aby użyć tego łącznika, musisz dodać go do istniejącego wyzwalacza w aplikacji logiki.

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* Konto platformy Azure; Możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free)
* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure. Musisz mieć konto integracji, aby używać łącznika wiadomości EDIFACT. 
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , które są już zdefiniowane na koncie integracji
* [Umowa EDIFACT](logic-apps-enterprise-integration-edifact.md) , która jest już zdefiniowana na koncie integracji

## <a name="encode-edifact-messages"></a>Kodowanie komunikatów EDIFACT

> [!IMPORTANT]
> Łącznik EDIFACT obsługuje tylko znaki UTF-8.
> Jeśli dane wyjściowe zawierają nieoczekiwane znaki, sprawdź, czy w komunikatach EDIFACT jest używany zestaw znaków UTF-8.

1. [Utwórz aplikację logiki](quickstart-create-first-logic-app-workflow.md).

2. Łącznik EDIFACT komunikatów kodowania nie ma wyzwalaczy, dlatego należy dodać wyzwalacz do uruchamiania aplikacji logiki, jak wyzwalacz żądania. W Projektancie aplikacji logiki Dodaj wyzwalacz, a następnie Dodaj akcję do aplikacji logiki.

3.  W polu wyszukiwania wprowadź ciąg "EDIFACT" jako filtr. Wybierz opcję **Koduj komunikat EDIFACT według nazwy umowy** lub **Zakoduj na komunikat EDIFACT przez tożsamość**.
   
    ![Wyszukaj EDIFACT](media/logic-apps-enterprise-integration-edifact-encode/edifactdecodeimage1.png)  

3. Jeśli nie utworzono wcześniej żadnych połączeń z kontem integracji, zostanie wyświetlony monit o utworzenie tego połączenia teraz. Nazwij połączenie i wybierz konto integracji, które chcesz połączyć.

    ![Utwórz połączenie z kontem integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage1.png)  

    Właściwości z gwiazdką są wymagane.

    | Właściwość | Szczegóły |
    | --- | --- |
    | Nazwa połączenia * |Wprowadź dowolną nazwę dla połączenia. |
    | Konto integracji * |Wprowadź nazwę konta integracji. Upewnij się, że Twoje konto integracji i aplikacja logiki znajdują się w tej samej lokalizacji platformy Azure. |

5.  Gdy wszystko będzie gotowe, szczegóły połączenia powinny wyglądać podobnie do tego przykładu. Aby zakończyć tworzenie połączenia, wybierz pozycję **Utwórz**.

    ![Szczegóły połączenia z kontem integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage2.png)

    Połączenie zostało utworzone.

    ![utworzono połączenie z kontem integracji](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage4.png)

#### <a name="encode-edifact-message-by-agreement-name"></a>Koduj komunikat EDIFACT według nazwy umowy

W przypadku wybrania kodowania komunikatów EDIFACT według nazwy umowy Otwórz nazwę listy **umów EDIFACT** , wprowadź lub wybierz nazwę umowy EDIFACT. Wprowadź komunikat XML do zakodowania.

![Wprowadź nazwę umowy EDIFACT i komunikat XML do zakodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage6.png)

#### <a name="encode-edifact-message-by-identities"></a>Koduj komunikat EDIFACT według tożsamości

W przypadku wybrania kodowania komunikatów EDIFACT według tożsamości wprowadź identyfikator nadawcy, kwalifikator nadawcy, identyfikator odbiorcy i kwalifikator odbiorcy zgodnie z konfiguracją w umowie EDIFACT. Wybierz komunikat XML do zakodowania.

![Podaj tożsamości dla nadawcy i odbiornika, wybierz wiadomość XML do zakodowania](media/logic-apps-enterprise-integration-edifact-encode/edifactencodeimage7.png)

## <a name="edifact-encode-details"></a>Szczegóły kodowania EDIFACT

Łącznik EDIFACT kodowania wykonuje następujące zadania: 

* Rozpoznaj umowę, dopasowując kwalifikator nadawcy & identyfikator i kwalifikator i identyfikator adresata
* Serializacja wymiany EDI, konwertowanie komunikatów zakodowanych w formacie XML na zestawy transakcji EDI w wymianie.
* Stosuje nagłówek i segmenty naczepy zestawu transakcji
* Generuje numer kontrolny wymiany, numer kontrolny grupy i numer kontrolny zestawu transakcji dla każdej wychodzącej wymiany
* Zamienia separatory w danych ładunku
* Sprawdza poprawność EDI i właściwości specyficzne dla partnera
  * Sprawdzanie poprawności schematu dla elementów danych zestawu transakcji względem schematu komunikatów.
  * Sprawdzanie poprawności EDI wykonane dla elementów danych zestawu transakcji.
  * Przeprowadzono rozszerzoną weryfikację dla elementów danych zestawu transakcji
* Generuje dokument XML dla każdego zestawu transakcji.
* Żąda potwierdzeń technicznych i/lub funkcjonalnych (jeśli zostały skonfigurowane).
  * Jako potwierdzenie techniczne komunikat CONTRL wskazuje na otrzymanie wymiany.
  * W ramach potwierdzenia funkcjonalności komunikat CONTRL wskazuje na akceptację lub odrzucenie odebranej wymiany, grupy lub komunikatu, z listą błędów lub nieobsługiwaną funkcjonalnością

## <a name="view-swagger-file"></a>Wyświetl plik Swagger
Aby wyświetlić szczegóły struktury Swagger dla łącznika EDIFACT, zobacz [EDIFACT](/connectors/edifact/).

## <a name="next-steps"></a>Następne kroki
[Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md "Dowiedz się więcej o Pakiet integracyjny dla przedsiębiorstw") 

