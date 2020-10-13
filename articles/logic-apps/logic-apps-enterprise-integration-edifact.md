---
title: Komunikaty EDIFACT dla integracji B2B
description: Komunikaty programu Exchange EDIFACT w formacie EDI dla integracji z usługą B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: b0df55e59bd519a816c4022f2434edfcd4460780
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91570770"
---
# <a name="exchange-edifact-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wymiana komunikatów EDIFACT w przypadku integracji B2B dla przedsiębiorstw w usłudze Logic Apps za pomocą pakietu integracyjnego dla przedsiębiorstw

Przed rozpoczęciem wymiany komunikatów EDIFACT dla Azure Logic Apps należy utworzyć umowę EDIFACT i przechować ją na koncie integracji. Poniżej przedstawiono procedurę tworzenia umowy EDIFACT.

> [!NOTE]
> Ta strona obejmuje funkcje EDIFACT dla Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [X12](logic-apps-enterprise-integration-x12.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure  
* Co najmniej dwóch [partnerów](logic-apps-enterprise-integration-partners.md) , które są już zdefiniowane na koncie integracji

> [!NOTE]
> Podczas tworzenia umowy zawartość komunikatów otrzymywanych lub wysyłanych do i z partnera musi być zgodna z typem umowy.

Po [utworzeniu konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) i [dodaniu partnerów](logic-apps-enterprise-integration-partners.md)możesz utworzyć umowę EDIFACT, wykonując następujące kroki.

## <a name="create-an-edifact-agreement"></a>Utwórz umowę EDIFACT 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. W polu wyszukiwania wprowadź ciąg "Integracja", a następnie wybierz pozycję **konta integracji**.

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-edifact/edifact-0.png)

   > [!TIP]
   > Jeśli nie zostaną wyświetlone **wszystkie usługi** , może być konieczne pierwsze rozszerzenie menu. W górnej części zwiniętego menu wybierz pozycję **Pokaż etykiety tekstu**.

3. W obszarze **konta integracji**wybierz konto integracji, w którym chcesz utworzyć umowę.

   ![Wybierz konto integracji, dla którego chcesz utworzyć umowę](./media/logic-apps-enterprise-integration-edifact/edifact-1-4.png)

4. Wybierz pozycję **umowy**. Jeśli nie masz kafelka umów, najpierw Dodaj kafelek.   

   ![Wybierz kafelek "umowy"](./media/logic-apps-enterprise-integration-edifact/edifact-1-5.png)

5. Na stronie umowy wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-enterprise-integration-edifact/edifact-agreement-2.png)

6. W obszarze **Dodaj**wpisz **nazwę** umowy. W obszarze **typ umowy**wybierz pozycję **EDIFACT**. Wybierz **partnera hosta**, **tożsamość hosta**, **partnera gościa**i **tożsamość gościa** dla Twojej umowy.

   ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-edifact/edifact-1.png)

   | Właściwość | Opis |
   | --- | --- |
   | Nazwa |Nazwa umowy |
   | Typ umowy | Powinien być EDIFACT |
   | Partner hosta |Umowa wymaga zarówno hosta, jak i partnera gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
   | Tożsamość hosta |Identyfikator dla partnera hosta |
   | Partner gościa |Umowa wymaga zarówno hosta, jak i partnera gościa. Partner gościa reprezentuje organizację, która wykonuje działalność z partnerem hosta. |
   | Tożsamość gościa |Identyfikator partnera gościa |
   | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez umowę. |
   | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez umowę. |
   ||| 

## <a name="configure-how-your-agreement-handles-received-messages"></a>Skonfiguruj sposób obsługi komunikatów odebranych przez umowę

Po ustawieniu właściwości umowy można skonfigurować, w jaki sposób ta umowa identyfikuje i obsługuje komunikaty przychodzące otrzymane od partnera za pośrednictwem tej umowy.

> [!IMPORTANT]
> Łącznik EDIFACT obsługuje tylko znaki UTF-8.
> Jeśli dane wyjściowe zawierają nieoczekiwane znaki, sprawdź, czy w komunikatach EDIFACT jest używany zestaw znaków UTF-8.

1. W obszarze **Dodaj**wybierz pozycję **Odbierz ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z nim wiadomości. Opisy właściwości znajdują się w tabelach w tej sekcji.

   **Ustawienia odbierania** są zorganizowane w następujące sekcje: identyfikatory, potwierdzenia, schematy, numery kontrolne, walidacja i ustawienia wewnętrzne.

   ![Konfigurowanie "odbierania ustawień"](./media/logic-apps-enterprise-integration-edifact/edifact-2.png)  

2. Po zakończeniu upewnij się, że ustawienia zostały zapisane, wybierając **przycisk OK**.

Teraz Twoja umowa jest gotowa do obsługi wiadomości przychodzących, które są zgodne z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB 6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczną z zakresu od 1 do 14 znaków. |
| UNB 6.2 (kwalifikator odwołania odbiorcy) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie dwa znaki. |

### <a name="acknowledgments"></a>Podziękowania

| Właściwość | Opis |
| --- | --- |
| Otrzymanie wiadomości (CONTRL) |Zaznacz to pole wyboru, aby zwrócić do nadawcy wymiany potwierdzenie techniczne (CONTRL). Potwierdzenie jest wysyłane do nadawcy wymiany na podstawie ustawień wysyłki dla umowy. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, aby zwrócić funkcję (CONTRL) do nadawcy wymiany, potwierdzenie jest wysyłane do nadawcy wymiany na podstawie ustawień wysyłania dla umowy. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH 2.1 (TYP) |Wybierz typ zestawu transakcji. |
| UNH 2.2 (WERSJA) |Wprowadź numer wersji wiadomości. (Minimum, jeden znak; maksimum, trzy znaki). |
| UNH 2.3 (WERSJA) |Wprowadź numer wersji wiadomości. (Minimum, jeden znak; maksimum, trzy znaki). |
| UNH 2.5 (SKOJARZONY KOD PRZYPISANY) |Wprowadź przypisany kod. (Maksymalnie sześć znaków. Musi być alfanumeryczne). |
| UNG 2.1 (IDENTYFIKATOR NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 35 znaków. |
| UNG 2.2 (KWALIFIKATOR KODU NADAWCY APLIKACJI) |Wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. |
| SCHEMATY |Wybierz wcześniej przekazany schemat, którego chcesz użyć ze skojarzonego konta integracji. |

### <a name="control-numbers"></a>Numery kontrolne

| Właściwość | Opis |
| --- | --- |
| Nie Zezwalaj na duplikaty numerów kontrolnych wymiany |Aby zablokować zduplikowane zmiany, wybierz tę właściwość. W przypadku wybrania tej akcji dekodowanie EDIFACT sprawdza, czy numer kontrolki wymiany (UNB5) dla otrzymanej wymiany nie jest zgodny z wcześniej przetworzonym numerem kontroli wymiany. W przypadku wykrycia dopasowania, wymiana nie zostanie przetworzona. |
| Sprawdzaj obecność zduplikowanych UNB5 co (dni) |Jeśli wybrano opcję niezezwalania na zduplikowane numery kontroli wymiany, można określić liczbę dni, po której należy wykonać sprawdzanie, podając odpowiednią wartość tego ustawienia. |
| Nie Zezwalaj na duplikaty numerów kontrolnych grupy |Aby zablokować zmiany ze zduplikowanymi numerami kontrolek grup (UNG5), wybierz tę właściwość. |
| Nie Zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Aby zablokować zmiany za pomocą duplikatów numerów kontroli zestawu transakcji (UNH1), wybierz tę właściwość. |
| Numer kontrolny potwierdzenia EDIFACT |Aby wyznaczyć numery referencyjne zestawu transakcji do użycia w potwierdzeniu, wprowadź wartość dla prefiksu, zakres numerów odwołań i sufiks. |

### <a name="validation"></a>Walidacja

Po ukończeniu każdego wiersza walidacji zostanie automatycznie dodany inny. Jeśli nie określisz żadnych reguł, walidacja używa wiersza "default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ wiadomości EDI. |
| Weryfikacja EDI |Wykonaj walidację EDI dla typów danych, zgodnie z definicją w schemacie właściwości EDI, ograniczenia długości, puste elementy danych i końcowe separatory. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, walidacja dotyczy wymagania elementu danych, dozwolonego powtórzenia, wyliczeń i poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj wszystkie dodatkowe wiodące lub końcowe zera i spacje. Nie usuwaj tych znaków. |
| Przytnij zera wiodące/końcowe |Usuń znaki wiodące lub końcowe zerowe i spacje. |
| Końcowe zasady separatora |Generuj końcowe separatory. <p>Wybierz opcję **niedozwolone** , aby uniemożliwić końcowe ograniczniki i separatory w odebranej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz opcję **opcjonalne** , aby akceptować zmiany, z końcowymi ogranicznikami i separatorami oraz bez nich. <p>Wybierz wartość **obowiązkowe** , Jeśli odebrana wymiana musi mieć końcowe ograniczniki i separatory. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

| Właściwość | Opis |
| --- | --- |
| Utwórz puste tagi XML, jeśli są dozwolone końcowe separatory |Zaznacz to pole wyboru, aby nadawca wymiany mogli uwzględnić puste tagi XML dla końcowych separatorów. |
| Rozdziel wymianę jako zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu|Analizuje każdy zestaw transakcji w ramach wymiany do oddzielnego dokumentu XML przez zastosowanie odpowiedniej koperty do zestawu transakcji. Zawieś tylko zestawy transakcji, których Walidacja nie powiodła się. |
| Podział wymiany jako zestawy transakcji — Zawieś wymianę w przypadku błędu|Analizuje każdy zestaw transakcji w ramach wymiany do oddzielnego dokumentu XML przez zastosowanie odpowiedniej formy. Zawieś całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. | 
| Zachowaj zestawy transakcji zawieszania wymiany w przypadku błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Zawieś tylko zestawy transakcji, których Walidacja nie powiodła się, jednocześnie przetwarzając wszystkie inne zestawy transakcji. |
| Zachowaj wymianę — Wstrzymaj transwymianę w przypadku błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Zawieś całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurowanie sposobu wysyłania komunikatów przez umowę

Można skonfigurować sposób, w jaki ta umowa identyfikuje i obsługuje wiadomości wychodzące wysyłane do partnerów w ramach niniejszej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Wyślij ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, którzy wymieniają wiadomości. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Ustawienia wysyłania** są zorganizowane w następujące sekcje: identyfikatory, potwierdzenia, schematy, koperty, zestawy znaków i separatory, numery kontroli i walidacji.

    ![Konfigurowanie "wysyłania ustawień"](./media/logic-apps-enterprise-integration-edifact/edifact-3.png)    

2. Po zakończeniu upewnij się, że ustawienia zostały zapisane, wybierając **przycisk OK**.

Teraz Twoja umowa jest gotowa do obsługi wiadomości wychodzących, które są zgodne z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

| Właściwość | Opis |
| --- | --- |
| UNB 1.2 (wersja składni) |Wybierz wartość z zakresu od **1** do **4**. |
| UNB 2.3 (adres routingu odwrotnego nadawcy) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 14 znaków. |
| UNB 3.3 (adres routingu odwrotnego odbiorcy) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 14 znaków. |
| UNB 6.1 (hasło odwołania do odbiorcy) |Wprowadź wartość alfanumeryczną z co najmniej jedną i maksymalnie 14 znakami. |
| UNB 6.2 (kwalifikator odwołania odbiorcy) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie dwa znaki. |
| UNB7 (identyfikator odwołania do aplikacji) |Wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami |

### <a name="acknowledgment"></a>Dziękowanie

| Właściwość | Opis |
| --- | --- |
| Otrzymanie wiadomości (CONTRL) |Zaznacz to pole wyboru, jeśli Partner hostowany oczekuje na otrzymanie CONTRL. To ustawienie określa, że partner hostowany, który wysyła wiadomość, żąda potwierdzenia od partnera gościa. |
| Potwierdzenie (CONTRL) |Zaznacz to pole wyboru, jeśli Partner hostowany oczekuje na otrzymanie funkcji (CONTRL). To ustawienie określa, że partner hostowany, który wysyła wiadomość, żąda potwierdzenia od partnera gościa. |
| Generuj pętlę SG1/SG4 dla zaakceptowanych zestawów transakcji |Jeśli wybrano opcję zażądania potwierdzenia funkcjonalnego, zaznacz to pole wyboru, aby wymusić generowanie pętli SG1/SG4 w przypadku zaakceptowanych zestawów transakcji CONTRL. |

### <a name="schemas"></a>Schematy

| Właściwość | Opis |
| --- | --- |
| UNH 2.1 (TYP) |Wybierz typ zestawu transakcji. |
| UNH 2.2 (WERSJA) |Wprowadź numer wersji wiadomości. |
| UNH 2.3 (WERSJA) |Wprowadź numer wersji wiadomości. |
| SCHEMATY |Wybierz schemat do użycia. Schematy znajdują się na koncie integracji. Aby uzyskać dostęp do swoich schematów, najpierw Połącz swoje konto integracji z aplikacją logiki. |

### <a name="envelopes"></a>Koperty

| Właściwość | Opis |
| --- | --- |
| UNB8 (kod priorytetu przetwarzania) |Wprowadź wartość alfabetyczną, która nie zawiera więcej niż jeden znak. |
| UNB10 (Umowa komunikacyjna) |Wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 40 znaków. |
| UNB11 (wskaźnik testu) |Zaznacz to pole wyboru, aby wskazać, że wygenerowanej wymiany są dane testowe |
| Zastosuj segment segmentu UNA (wskazówki dotyczące ciągu usługi) |Zaznacz to pole wyboru, aby wygenerować segment segmentu UNA dla wymiany, która ma zostać wysłana. |
| Zastosuj segmenty UNG (nagłówek grupy funkcji) |Zaznacz to pole wyboru, aby utworzyć segmenty grupowania w nagłówku grupy funkcjonalnej w komunikatach wysyłanych do partnera gościa. Do tworzenia segmentów UNG są używane następujące wartości: <p>Dla **UNG1**wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie sześć znaków. <p>Dla **ung 2.1**wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 35 znaków. <p>Dla **ung 2.2**wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. <p>Dla **ung 3.1**wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie 35 znaków. <p>Dla **ung 3.2**wprowadź wartość alfanumeryczną z maksymalnie czterema znakami. <p>Dla **UNG6**wprowadź wartość alfanumeryczną z co najmniej jedną i maksymalnie trzema znakami. <p>Dla **ung 7.1**wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie trzy znaki. <p>W przypadku **ung 7.2**wprowadź wartość alfanumeryczną zawierającą co najmniej jeden znak i maksymalnie trzy znaki. <p>Dla **ung 7.3**wprowadź wartość alfanumeryczną zawierającą co najmniej 1 znak i maksymalnie 6 znaków. <p>Dla **UNG8**wprowadź wartość alfanumeryczną z co najmniej jednym znakiem i maksymalnie 14 znakami. |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Oprócz zestawu znaków można wprowadzić inny zestaw ograniczników, który będzie używany dla każdego typu wiadomości. Jeśli zestaw znaków nie zostanie określony dla danego schematu wiadomości, zostanie użyty domyślny zestaw znaków.

| Właściwość | Opis |
| --- | --- |
| UNB 1.1 (identyfikator systemu) |Wybierz zestaw znaków EDIFACT do zastosowania w wychodzącej wymianie. |
| Schemat |Z listy rozwijanej wybierz schemat. Po zakończeniu każdego wiersza zostanie automatycznie dodany nowy wiersz. Dla wybranego schematu wybierz zestaw separatorów, który ma być używany, na podstawie opisów separatora poniżej. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Aby oddzielić złożone elementy danych, wprowadź pojedynczy znak. |
| Separator elementów danych |Aby rozdzielić proste elementy danych w ramach złożonych elementów danych, należy wprowadzić pojedynczy znak. |
| Terminator segmentu |Aby wskazać koniec segmentu EDI, wprowadź pojedynczy znak. |
| Przedrostk |Wybierz znak, który jest używany z identyfikatorem segmentu. Jeśli wyznaczysz sufiks, element danych terminatora segmentu może być pusty. Jeśli terminator segmentu pozostaje pusty, należy wyznaczyć sufiks. |

### <a name="control-numbers"></a>Numery kontrolne

| Właściwość | Opis |
| --- | --- |
| UNB5 (numer kontrolny wymiany) |Wprowadź prefiks, zakres wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania wymiany wychodzącej. Prefiks i sufiks są opcjonalne, podczas gdy wymagany jest numer kontrolny. Numer kontrolny jest zwiększany dla każdej nowej wiadomości; prefiks i sufiks pozostają takie same. |
| UNG5 (numer kontrolny grupy) |Wprowadź prefiks, zakres wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania numeru kontrolnego grupy. Prefiks i sufiks są opcjonalne, podczas gdy wymagany jest numer kontrolny. Numer kontrolny jest zwiększany dla każdej nowej wiadomości, dopóki nie zostanie osiągnięta maksymalna wartość; prefiks i sufiks pozostają takie same. |
| UNH1 (Numer odwołania do nagłówka komunikatu) |Wprowadź prefiks, zakres wartości dla numeru kontrolnego wymiany oraz sufiks. Te wartości są używane do generowania numeru odwołania do nagłówka komunikatu. Prefiks i sufiks są opcjonalne, podczas gdy numer referencyjny jest wymagany. Numer referencyjny jest zwiększany dla każdej nowej wiadomości; prefiks i sufiks pozostają takie same. |

### <a name="validation"></a>Walidacja

Po ukończeniu każdego wiersza walidacji zostanie automatycznie dodany inny. Jeśli nie określisz żadnych reguł, walidacja używa wiersza "default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ wiadomości EDI. |
| Weryfikacja EDI |Wykonaj walidację EDI dla typów danych zgodnie z definicją właściwości EDI schematu, ograniczenia długości, puste elementy danych i końcowe separatory. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, walidacja dotyczy wymagania elementu danych, dozwolonego powtórzenia, wyliczeń i poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj wszystkie dodatkowe wiodące lub końcowe zera i spacje. Nie usuwaj tych znaków. |
| Przytnij zera wiodące/końcowe |Usuń znaki wiodące lub końcowe zerowe. |
| Końcowe zasady separatora |Generuj końcowe separatory. <p>Wybierz opcję **niedozwolone** , aby uniemożliwić końcowe ograniczniki i separatory w wysyłanej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz opcję **opcjonalne** , aby wysyłać zmiany z lub bez ograniczników i separatorów końcowego. <p>Wybierz pozycję **obowiązkowe** , jeśli wysłana wymiana musi mieć końcowe ograniczniki i separatory. |

## <a name="find-your-created-agreement"></a>Znajdź utworzoną umowę

1.  Po zakończeniu ustawiania wszystkich właściwości umowy na stronie **Dodaj** wybierz **przycisk OK** , aby zakończyć tworzenie umowy i wrócić do konta integracji.

    Nowo dodana umowa zostanie teraz wyświetlona na liście **umów** .

2.  Możesz również wyświetlić umowy na koncie integracji — Omówienie. W menu konto integracji wybierz pozycję **Przegląd**, a następnie wybierz kafelek **umowy** . 

    ![Zrzut ekranu przedstawiający kafelek umowy.](./media/logic-apps-enterprise-integration-edifact/edifact-4.png)   

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące tego łącznika, takie jak akcje i limity zgodnie z opisem w pliku struktury Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/edifact/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja tego ŁĄCZNIKa ISE z oznaczeniem używa [limitów komunikatów B2B dla ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
