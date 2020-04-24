---
title: Komunikaty X12 dla integracji B2B
description: Komunikaty programu Exchange X12 w formacie EDI dla integracji z usługą B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/31/2017
ms.openlocfilehash: 12a1cd3c170fd7444362d1eabba1541cefb37d1a
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82115554"
---
# <a name="exchange-x12-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Komunikaty programu Exchange X12 dla integracji z usługą B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Przed rozpoczęciem wymiany komunikatów X12 dla Azure Logic Apps należy utworzyć umowę X12 i przechować ją na koncie integracji. Poniżej przedstawiono procedurę tworzenia umowy X12.

> [!NOTE]
> Ta strona obejmuje funkcje X12 dla Azure Logic Apps. Aby uzyskać więcej informacji, zobacz [EDIFACT](logic-apps-enterprise-integration-edifact.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Oto elementy, których potrzebujesz:

* [Konto integracji](logic-apps-enterprise-integration-create-integration-account.md) , które jest już zdefiniowane i skojarzone z subskrypcją platformy Azure
* Co najmniej dwóch [partnerów](../logic-apps/logic-apps-enterprise-integration-partners.md) zdefiniowanych w ramach konta integracji i skonfigurowanych przy użyciu identyfikatora X12 w obszarze **tożsamości biznesowe**    
* Wymagany [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) , który można przekazać do konta integracji

Po [utworzeniu konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), [dodaniu partnerów](logic-apps-enterprise-integration-partners.md)i utworzeniu [schematu](../logic-apps/logic-apps-enterprise-integration-schemas.md) , który ma być używany, można utworzyć umowę X12, wykonując następujące kroki.

## <a name="create-an-x12-agreement"></a>Utwórz umowę X12

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com "Azure Portal"). 

2. W głównym menu platformy Azure wybierz pozycję **wszystkie usługi**. 
   W polu wyszukiwania wprowadź ciąg "Integracja", a następnie wybierz pozycję **konta integracji**.  

   ![Znajdowanie konta integracji](./media/logic-apps-enterprise-integration-x12/account-1.png)

   > [!TIP]
   > Jeśli nie zostaną wyświetlone **wszystkie usługi** , może być konieczne pierwsze rozszerzenie menu. W górnej części menu rozwijanego wybierz pozycję **Pokaż menu**.

3. W obszarze **konta integracji**wybierz konto integracji, do którego chcesz dodać umowę.

   ![Wybierz konto integracji, dla którego chcesz utworzyć umowę](./media/logic-apps-enterprise-integration-x12/account-3.png)

4. Wybierz pozycję **Przegląd**, a następnie wybierz kafelek **umowy** . 
   Jeśli nie masz kafelka umów, najpierw Dodaj kafelek. 

   ![Wybierz kafelek "umowy"](./media/logic-apps-enterprise-integration-x12/agreement-1.png)

5. W obszarze **umowy**wybierz pozycję **Dodaj**.

   ![Wybierz pozycję "Dodaj"](./media/logic-apps-enterprise-integration-x12/agreement-2.png)     

6. W obszarze **Dodaj**wpisz **nazwę** umowy. 
   W polu Typ umowy wybierz pozycję **X12**. 
   Wybierz **partnera hosta**, **tożsamość hosta**, **partnera gościa**i **tożsamość gościa** dla Twojej umowy. 
   Aby uzyskać więcej informacji o właściwościach, zobacz tabelę w tym kroku.

    ![Podaj szczegóły umowy](./media/logic-apps-enterprise-integration-x12/x12-1.png)  

    | Właściwość | Opis |
    | --- | --- |
    | Nazwa |Nazwa umowy |
    | Typ umowy | Powinien być X12 |
    | Partner hosta |Umowa wymaga zarówno hosta, jak i partnera gościa. Partner hosta reprezentuje organizację, która konfiguruje umowę. |
    | Tożsamość hosta |Identyfikator dla partnera hosta |
    | Partner gościa |Umowa wymaga zarówno hosta, jak i partnera gościa. Partner gościa reprezentuje organizację, która wykonuje działalność z partnerem hosta. |
    | Tożsamość gościa |Identyfikator partnera gościa |
    | Ustawienia odbierania |Te właściwości mają zastosowanie do wszystkich komunikatów odebranych przez umowę. |
    | Ustawienia wysyłania |Te właściwości mają zastosowanie do wszystkich komunikatów wysłanych przez umowę. |  

   > [!NOTE]
   > Rozpoznawanie umów X12 zależy od dopasowania kwalifikatora i identyfikatora nadawcy oraz kwalifikatora i identyfikatora odbiorcy zdefiniowanego w komunikacie partnera i przychodzącego. Jeśli te wartości są zmieniane dla partnera, zaktualizuj umowę.

## <a name="configure-how-your-agreement-handles-received-messages"></a>Skonfiguruj sposób obsługi komunikatów odebranych przez umowę

Po ustawieniu właściwości umowy można skonfigurować, w jaki sposób ta umowa identyfikuje i obsługuje komunikaty przychodzące otrzymane od partnera za pośrednictwem tej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Odbierz ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, który wymienia z nim wiadomości. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Ustawienia odbierania** są zorganizowane w następujące sekcje: identyfikatory, potwierdzenia, schematy, koperty, numery kontrolne, walidacje i ustawienia wewnętrzne.

2. Po zakończeniu upewnij się, że ustawienia zostały zapisane, wybierając **przycisk OK**.

Teraz Twoja umowa jest gotowa do obsługi wiadomości przychodzących, które są zgodne z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

![Ustaw właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-2.png)  

| Właściwość | Opis |
| --- | --- |
| ISA1 (kwalifikator autoryzacji) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Element opcjonalny. Wprowadź wartość informacji o autoryzacji. Jeśli wartość wprowadzona dla ISA1 jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| ISA3 (kwalifikator zabezpieczeń) |Wybierz z listy rozwijanej wartość kwalifikatora zabezpieczeń. |
| ISA4 |Element opcjonalny. Wprowadź wartość informacji o zabezpieczeniach. Jeśli wartość wprowadzona dla ISA3 jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |

### <a name="acknowledgment"></a>Dziękowanie

![Ustawianie właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-3.png) 

| Właściwość | Opis |
| --- | --- |
| Oczekiwano TA1 |Zwraca potwierdzenie techniczne nadawcy wymiany |
| Przewidywany FA |Zwraca potwierdzenie funkcjonalne do nadawcy wymiany. Następnie wybierz, czy chcesz potwierdzeń 997 lub 999 na podstawie wersji schematu |
| Uwzględnij pętlę AK2/IK2 |Włącza generowanie pętli AK2 w potwierdzeniach funkcjonalnych dla akceptowanych zestawów transakcji |

### <a name="schemas"></a>Schematy

Wybierz schemat dla każdego typu transakcji (ST1) i aplikacji nadawcy (GS2). Potok odbierania rozkłada komunikat przychodzący przez dopasowanie wartości parametrów ST1 i GS2 w komunikacie przychodzącym z wartościami ustawionymi w tym miejscu oraz schematem komunikatu przychodzącego ze schematem ustawionym w tym miejscu.

![Wybierz schemat](./media/logic-apps-enterprise-integration-x12/x12-33.png) 

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz wersję X12 |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| Aplikacja nadawcy (GS02) |Wybierz aplikację nadawcy |
| Schemat |Wybierz plik schematu, którego chcesz użyć. Schematy są dodawane do konta integracji. |

> [!NOTE]
> Skonfiguruj wymagany [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) , który jest przekazywany do [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określ separator w zestawie transakcji: Wybierz standardowy identyfikator lub separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-34.png)

| Właściwość | Opis |
| --- | --- |
| ISA11 użycie |Określa separator, który ma być używany w zestawie transakcji: <p>Wybierz opcję **Identyfikator standardowy** , aby użyć kropki (.) dla notacji dziesiętnej, a nie notacji dziesiętnej dokumentu przychodzącego w potoku odbierania EDI. <p>Wybierz pozycję **separator powtórzeń** , aby określić separator powtórzonych wystąpień prostego elementu danych lub powtarzanej struktury danych. Na przykład zwykle daszek (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatów. |

### <a name="control-numbers"></a>Numery kontrolne

![Wybierz, jak obsłużyć duplikaty numerów kontrolnych](./media/logic-apps-enterprise-integration-x12/x12-35.png) 

| Właściwość | Opis |
| --- | --- |
| Nie Zezwalaj na duplikaty numerów kontrolnych wymiany |Blokuj zduplikowane zmiany. Sprawdza numer kontroli wymiany (ISA13) dla odebranego numeru kontrolnego wymiany. W przypadku wykrycia dopasowania potok odbierania nie przetwarza wymiany. Możesz określić liczbę dni, przez jaką należy wykonać sprawdzanie, podając wartość dla opcji *Sprawdź duplikaty ISA13 co (dni)*. |
| Nie Zezwalaj na duplikaty numerów kontrolnych grupy |Blokuj zmiany ze zduplikowanymi numerami kontrolek grup. |
| Nie Zezwalaj na duplikaty numerów kontrolnych zestawu transakcji |Blokuj zmiany za pomocą duplikatów numerów kontrolnych zestawu transakcji. |

### <a name="validation"></a>Walidacja

![Ustawianie właściwości walidacji dla odebranych komunikatów](./media/logic-apps-enterprise-integration-x12/x12-36.png) 

Po ukończeniu każdego wiersza walidacji zostanie automatycznie dodany inny. Jeśli nie określisz żadnych reguł, walidacja używa wiersza "default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ wiadomości EDI. |
| Weryfikacja EDI |Wykonaj walidację EDI dla typów danych, zgodnie z definicją w schemacie właściwości EDI, ograniczenia długości, puste elementy danych i końcowe separatory. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, walidacja dotyczy wymagania elementu danych, dozwolonego powtórzenia, wyliczeń i poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj wszystkie dodatkowe wiodące lub końcowe zera i spacje. Nie usuwaj tych znaków. |
| Przytnij zera wiodące/końcowe |Usuń znaki wiodące lub końcowe zerowe i spacje. |
| Końcowe zasady separatora |Generuj końcowe separatory. <p>Wybierz opcję **niedozwolone** , aby uniemożliwić końcowe ograniczniki i separatory w odebranej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz opcję **opcjonalne** , aby akceptować zmiany, z końcowymi ogranicznikami i separatorami oraz bez nich. <p>Wybierz wartość **obowiązkowe** , gdy w wymianie muszą być końcowe ograniczniki i separatory. |

### <a name="internal-settings"></a>Ustawienia wewnętrzne

![Wybierz ustawienia wewnętrzne](./media/logic-apps-enterprise-integration-x12/x12-37.png) 

| Właściwość | Opis |
| --- | --- |
| Konwertuj implikowany format dziesiętny "NN" na podstawową wartość liczbową 10 |Konwertuje numer EDI, który jest określony w formacie "NN" na wartość liczbową dziesiętną. |
| Utwórz puste tagi XML, jeśli są dozwolone końcowe separatory |Zaznacz to pole wyboru, aby nadawca wymiany mogli uwzględnić puste tagi XML dla końcowych separatorów. |
| Rozdziel wymianę jako zestawy transakcji — Zawieś zestawy transakcji w przypadku błędu|Analizuje każdy zestaw transakcji w ramach wymiany do oddzielnego dokumentu XML przez zastosowanie odpowiedniej koperty do zestawu transakcji. Wstrzymuje tylko transakcje, w których weryfikacja nie powiedzie się. |
| Podział wymiany jako zestawy transakcji — Zawieś wymianę w przypadku błędu|Analizuje każdy zestaw transakcji w ramach wymiany do oddzielnego dokumentu XML przez zastosowanie odpowiedniej formy. Wstrzymuje całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. | 
| Zachowaj zestawy transakcji zawieszania wymiany w przypadku błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Wstrzymuje tylko zestawy transakcji, których Walidacja nie powiodła się, a jednocześnie przetwarza wszystkie inne zestawy transakcji. |
| Zachowaj wymianę — Wstrzymaj transwymianę w przypadku błędu |Pozostawia wymianę bez zmian, tworzy dokument XML dla całej wymiany wsadowej. Wstrzymuje całą wymianę, gdy co najmniej jeden zestaw transakcji w ramach walidacji nie powiodło się. |

## <a name="configure-how-your-agreement-sends-messages"></a>Konfigurowanie sposobu wysyłania komunikatów przez umowę

Można skonfigurować sposób, w jaki ta umowa identyfikuje i obsługuje wiadomości wychodzące wysyłane do partnera w ramach niniejszej umowy.

1.  W obszarze **Dodaj**wybierz pozycję **Wyślij ustawienia**.
Skonfiguruj te właściwości na podstawie umowy z partnerem, którzy wymieniają wiadomości. Opisy właściwości znajdują się w tabelach w tej sekcji.

    **Ustawienia wysyłania** są zorganizowane w następujące sekcje: identyfikatory, potwierdzenia, schematy, koperty, zestawy znaków i separatory, numery kontrolne i walidacja.

2. Po zakończeniu upewnij się, że ustawienia zostały zapisane, wybierając **przycisk OK**.

Teraz Twoja umowa jest gotowa do obsługi wiadomości wychodzących, które są zgodne z wybranymi ustawieniami.

### <a name="identifiers"></a>Identyfikatory

![Ustaw właściwości identyfikatora](./media/logic-apps-enterprise-integration-x12/x12-4.png)  

| Właściwość | Opis |
| --- | --- |
| Kwalifikator autoryzacji (ISA1) |Wybierz wartość kwalifikatora autoryzacji z listy rozwijanej. |
| ISA2 |Wprowadź wartość informacji o autoryzacji. Jeśli ta wartość jest inna niż 00, wprowadź co najmniej jeden znak alfanumeryczny i maksymalnie 10. |
| Kwalifikator zabezpieczeń (ISA3) |Wybierz z listy rozwijanej wartość kwalifikatora zabezpieczeń. |
| ISA4 |Wprowadź wartość informacji o zabezpieczeniach. Jeśli ta wartość jest inna niż 00, dla pola tekstowego wartość (ISA4) wprowadź co najmniej jedną wartość alfanumeryczną i maksymalnie 10. |

### <a name="acknowledgment"></a>Dziękowanie

![Ustawianie właściwości potwierdzenia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Oczekiwano TA1 |Zwróć potwierdzenie techniczne (TA1) do nadawcy wymiany. To ustawienie określa, że partner hosta, który wysyła wiadomość, żąda potwierdzenia od partnera gościa w umowie. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
| Przewidywany FA |Zwróć potwierdzenie funkcjonalności (FA) do nadawcy wymiany. Wybierz, czy mają być potwierdzeń 997 lub 999 na podstawie wersji schematu, z którymi pracujesz. Te potwierdzenia są oczekiwane przez partnera hosta na podstawie ustawień odbioru umowy. |
| Wersja FA |Wybierz wersję FA |

### <a name="schemas"></a>Schematy

![Wybierz schemat do użycia](./media/logic-apps-enterprise-integration-x12/x12-5.png)  

| Właściwość | Opis |
| --- | --- |
| Wersja |Wybierz wersję X12 |
| Typ transakcji (ST01) |Wybierz typ transakcji |
| SCHEMATY |Wybierz schemat do użycia. Schematy znajdują się na koncie integracji. Jeśli wybierzesz najpierw schemat, automatycznie skonfiguruje wersję i typ transakcji  |

> [!NOTE]
> Skonfiguruj wymagany [schemat](../logic-apps/logic-apps-enterprise-integration-schemas.md) , który jest przekazywany do [konta integracji](../logic-apps/logic-apps-enterprise-integration-accounts.md).

### <a name="envelopes"></a>Koperty

![Określ separator w zestawie transakcji: Wybierz standardowy identyfikator lub separator powtórzeń](./media/logic-apps-enterprise-integration-x12/x12-6.png) 

| Właściwość | Opis |
| --- | --- |
| ISA11 użycie |Określa separator, który ma być używany w zestawie transakcji: <p>Wybierz opcję **Identyfikator standardowy** , aby użyć kropki (.) dla notacji dziesiętnej, a nie notacji dziesiętnej dokumentu przychodzącego w potoku odbierania EDI. <p>Wybierz pozycję **separator powtórzeń** , aby określić separator powtórzonych wystąpień prostego elementu danych lub powtarzanej struktury danych. Na przykład zwykle daszek (^) jest używany jako separator powtórzeń. W przypadku schematów HIPAA można używać tylko karatów. |

### <a name="control-numbers"></a>Numery kontrolne

![Określ właściwości numeru kontrolnego](./media/logic-apps-enterprise-integration-x12/x12-8.png) 

| Właściwość | Opis |
| --- | --- |
| Numer wersji kontrolki (ISA12) |Wybierz wersję standardu X12 |
| Wskaźnik użycia (ISA15) |Wybierz kontekst wymiany.  Wartości to informacje, dane produkcyjne lub dane testowe |
| Schemat |Generuje segmenty GS i ST dla wymiany zakodowanej w X12, która wysyła do potoku wysyłania |
| GS1 |Opcjonalne, wybierz wartość dla kodu funkcjonalnego z listy rozwijanej |
| GS2 |Opcjonalny, nadawca aplikacji |
| GS3 |Opcjonalne, odbiorca aplikacji |
| GS4 |Opcjonalne, wybierz pozycję CCYYMMDD lub YYMMDD |
| GS5 |Opcjonalne, Select HHMM, HHMMSS lub HHMMSSdd |
| GS7 |Opcjonalne, wybierz wartość dla odpowiedzialnej agencji z listy rozwijanej |
| GS8 |Opcjonalna, wersja dokumentu |
| Numer kontrolny wymiany (ISA13) |Wymagane, wprowadź zakres wartości dla numeru kontrolnego wymiany. Wprowadź wartość liczbową z co najmniej 1 i maksymalnie 999999999 |
| Numer kontrolny grupy (GS06) |Wymagane, wprowadź zakres liczb dla numeru kontroli grupy. Wprowadź wartość liczbową z co najmniej 1 i maksymalnie 999999999 |
| Numer kontrolny zestawu transakcji (ST02) |Wymagane, wprowadź zakres liczb dla numeru kontrolnego zestawu transakcji. Wprowadź zakres wartości liczbowych z wartością minimalną 1 i maksymalną 999999999 |
| Prefiks |Opcjonalne, wyznaczony dla zakresu numerów kontrolnych zestawu transakcji użytych w potwierdzeniu. Wprowadź wartość liczbową dla środkowych dwóch pól i wartość alfanumeryczną (jeśli to konieczne) dla pól prefiksu i sufiksu. Pola środkowe są wymagane i zawierają wartości minimalne i maksymalne dla numeru kontrolnego |
| Przedrostk |Opcjonalne, wyznaczony dla zakresu numerów kontrolnych zestawu transakcji użytych w potwierdzeniu. Wprowadź wartość liczbową dla dwóch pól środkowych i wartość alfanumeryczną (jeśli to konieczne) dla pól prefiksu i sufiksu. Pola środkowe są wymagane i zawierają wartości minimalne i maksymalne dla numeru kontrolnego |

### <a name="character-sets-and-separators"></a>Zestawy znaków i separatory

Oprócz zestawu znaków można wprowadzić inny zestaw ograniczników dla każdego typu wiadomości. Jeśli zestaw znaków nie został określony dla danego schematu wiadomości, zostanie użyty domyślny zestaw znaków.

![Określanie ograniczników dla typów komunikatów](./media/logic-apps-enterprise-integration-x12/x12-9.png) 

| Właściwość | Opis |
| --- | --- |
| Zestaw znaków do użycia |Aby sprawdzić poprawność właściwości, wybierz zestaw znaków X12. Dostępne są opcje Basic, Extended i UTF8. |
| Schemat |Z listy rozwijanej wybierz schemat. Po zakończeniu każdego wiersza zostanie automatycznie dodany nowy wiersz. Dla wybranego schematu wybierz zestaw separatorów, który ma być używany, na podstawie opisów separatora poniżej. |
| Typ danych wejściowych |Wybierz typ danych wejściowych z listy rozwijanej. |
| Separator składników |Aby oddzielić złożone elementy danych, wprowadź pojedynczy znak. |
| Separator elementów danych |Aby rozdzielić proste elementy danych w ramach złożonych elementów danych, należy wprowadzić pojedynczy znak. |
| Znak zastępczy |Wprowadź znak zastępczy używany do zastępowania wszystkich znaków separatora w danych ładunku podczas generowania wychodzącego komunikatu X12. |
| Terminator segmentu |Aby wskazać koniec segmentu EDI, wprowadź pojedynczy znak. |
| Przedrostk |Wybierz znak, który jest używany z identyfikatorem segmentu. Jeśli wyznaczysz sufiks, element danych terminatora segmentu może być pusty. Jeśli terminator segmentu pozostaje pusty, należy wyznaczyć sufiks. |

> [!TIP]
> Aby podać wartości znaków specjalnych, Edytuj umowę jako kod JSON i podaj wartość ASCII dla znaku specjalnego.

### <a name="validation"></a>Walidacja

![Ustawianie właściwości walidacji na potrzeby wysyłania wiadomości](./media/logic-apps-enterprise-integration-x12/x12-10.png) 

Po ukończeniu każdego wiersza walidacji zostanie automatycznie dodany inny. Jeśli nie określisz żadnych reguł, walidacja używa wiersza "default".

| Właściwość | Opis |
| --- | --- |
| Typ komunikatu |Wybierz typ wiadomości EDI. |
| Weryfikacja EDI |Wykonaj walidację EDI dla typów danych, zgodnie z definicją w schemacie właściwości EDI, ograniczenia długości, puste elementy danych i końcowe separatory. |
| Rozszerzona weryfikacja |Jeśli typ danych nie jest EDI, walidacja dotyczy wymagania elementu danych, dozwolonego powtórzenia, wyliczeń i poprawności długości elementu danych (min/max). |
| Zezwalaj na zera wiodące/końcowe |Zachowaj wszystkie dodatkowe wiodące lub końcowe zera i spacje. Nie usuwaj tych znaków. |
| Przytnij zera wiodące/końcowe |Usuń znaki wiodące lub końcowe zerowe. |
| Końcowe zasady separatora |Generuj końcowe separatory. <p>Wybierz opcję **niedozwolone** , aby uniemożliwić końcowe ograniczniki i separatory w wysyłanej wymianie. Jeśli wymiana ma końcowe ograniczniki i separatory, wymiana jest zadeklarowana jako nieprawidłowa. <p>Wybierz opcję **opcjonalne** , aby wysyłać zmiany z lub bez ograniczników i separatorów końcowego. <p>Wybierz pozycję **obowiązkowe** , jeśli wysłana wymiana musi mieć końcowe ograniczniki i separatory. |

## <a name="find-your-created-agreement"></a>Znajdź utworzoną umowę

1.  Po zakończeniu ustawiania wszystkich właściwości umowy na stronie **Dodaj** wybierz **przycisk OK** , aby zakończyć tworzenie umowy i wrócić do konta integracji.

    Nowo dodana umowa zostanie teraz wyświetlona na liście **umów** .

2.  Możesz również wyświetlić umowy na koncie integracji — Omówienie. W menu konto integracji wybierz pozycję **Przegląd**, a następnie wybierz kafelek **umowy** .

    ![Wybierz kafelek "umowy"](./media/logic-apps-enterprise-integration-x12/x12-1-5.png)   

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne dotyczące tego łącznika, takie jak akcje i limity zgodnie z opisem w pliku struktury Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/x12/). 

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja tego ŁĄCZNIKa ISE z oznaczeniem używa [limitów komunikatów B2B dla ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
