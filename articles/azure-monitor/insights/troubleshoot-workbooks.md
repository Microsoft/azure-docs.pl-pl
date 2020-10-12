---
title: Rozwiązywanie problemów Azure Monitor szczegółowych informacji opartych na skoroszycie
description: Zawiera wskazówki dotyczące rozwiązywania problemów dotyczących Azure Monitor szczegółowych informacji opartych na skoroszycie dla usług takich jak Azure Key Vault, Azure CosmosDB, Azure Storage i Azure cache for Redis.
services: azure-monitor
ms.author: mbullwin
author: mrbullwinkle
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: 90743a213092aacfa10139c12f47bc11faf0d86d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86498716"
---
# <a name="troubleshooting-workbook-based-insights"></a>Rozwiązywanie problemów dotyczących szczegółowych informacji opartych na skoroszycie

Ten artykuł pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z Azure Monitor szczegółowych informacji opartych na skoroszycie.


## <a name="why-can-i-only-see-200-resources"></a>Dlaczego można zobaczyć tylko zasoby 200

Liczba wybranych zasobów ma limit 200, niezależnie od liczby wybranych subskrypcji.

## <a name="what-happens-when-i-click-on-a-recently-pinned-tile-in-the-dashboard"></a>Co się stanie po kliknięciu ostatnio przypiętego kafelka na pulpicie nawigacyjnym

* Jeśli klikniesz w dowolnym miejscu kafelka, przejdziesz do karty, z której został przypięty kafelek. Na przykład w przypadku przypinania grafu na karcie "przegląd" po kliknięciu tego kafelka na pulpicie nawigacyjnym zostanie otwarty ten widok domyślny, jednak w przypadku przypinania grafu ze swojej zapisanej kopii zostanie otwarty widok zapisanej kopii.
* Ikona filtru w lewym górnym rogu tytułu otwiera kartę "Konfigurowanie ustawień kafelków".
* Ikona wielokropka w prawym górnym rogu będzie zawierać opcje "Dostosowywanie danych tytułu", "Dostosowywanie", "Odśwież" i "Usuwanie z pulpitu nawigacyjnego".

## <a name="what-happens-when-i-save-a-workbook"></a>Co się stanie, gdy zapisuję skoroszyt

* Po zapisaniu skoroszytu można utworzyć nową kopię skoroszytu z wprowadzonymi zmianami i zmienić tytuł. Zapisywanie nie zastępuje skoroszytu, bieżący skoroszyt będzie zawsze widokiem domyślnym.
* **Niezapisany** skoroszyt jest tylko widokiem domyślnym.

## <a name="why-dont-i-see-all-my-subscriptions-in-the-portal"></a>Dlaczego nie widzę wszystkich moich subskrypcji w portalu

W portalu zostaną wyświetlone tylko dane dla wybranych subskrypcji podczas uruchamiania portalu. Aby zmienić wybrane subskrypcje, przejdź do prawego górnego rogu i kliknij Notes z ikoną filtru. Ta opcja spowoduje wyświetlenie karty **katalog i subskrypcje** .

![Katalog i subskrypcja](./media/storage-insights-overview/fqa3.png)

## <a name="what-is-time-range"></a>Co to jest zakres czasu

Zakres czasu pokazuje dane z określonego przedziału czasu. Na przykład jeśli zakres czasu wynosi 24 godziny, to pokazuje dane z ostatnich 24 godzin.

## <a name="what-is-time-granularity-time-grain"></a>Co to jest stopień szczegółowości czasu (ziarno czasu)

Stopień szczegółowości czasu to różnica czasu między dwoma punktami danych. Na przykład, jeśli ziarno czasu jest ustawione na 1 sekundę, oznacza to, że metryki są zbierane w każdej sekundzie.

## <a name="what-is-the-time-granularity-once-we-pin-any-part-of-the-workbooks-to-a-dashboard"></a>Jaki jest stopień szczegółowości czasu, gdy przypinam wszystkie części skoroszytów do pulpitu nawigacyjnego

Domyślny stopień szczegółowości czasu jest ustawiony na automatyczny, obecnie nie można go zmienić.

## <a name="how-do-i-change-the-timespan-time-range-of-the-workbook-step-on-my-dashboard"></a>Jak mogę zmienić przedział czasu/godziny dla etapu skoroszytu na moim pulpicie nawigacyjnym

Domyślnie zakres TimeSpan/czasu na kafelku pulpitu nawigacyjnego jest ustawiony na 24 godziny, aby zmienić Kliknięcie wielokropka w prawym górnym rogu, wybierz pozycję **Dostosuj dane kafelka**, zaznacz pole wyboru Zastąp ustawienia czasowe pulpitu nawigacyjnego na poziomie tytułu, a następnie wybierz przedział czasu przy użyciu menu rozwijanego.  

![Wybierz wielokropek w prawym górnym rogu kafelka i wybierz pozycję Dostosuj te dane](./media/storage-insights-overview/fqa-data-settings.png)

![W obszarze Konfiguruj ustawienia kafelków wybierz listę rozwijaną TimeSpan, aby zmienić zakres czasu/godziny](./media/storage-insights-overview/fqa-timespan.png)

## <a name="how-do-i-change-the-title-of-the-workbook-or-a-workbook-step-i-pinned-to-a-dashboard"></a>Jak mogę zmienić tytułu skoroszytu lub kroku, który został przypięty do pulpitu nawigacyjnego

Tytuł lub krok skoroszytu, który jest przypięty do pulpitu nawigacyjnego, zachowuje tę samą nazwę w skoroszycie. Aby zmienić tytuł, musisz zapisać własną kopię skoroszytu. Następnie będzie można nazwać skoroszyt przed naciśnięciem przycisku Zapisz.

![Wybierz pozycję Zapisz u góry, aby zapisać kopię skoroszytu i zmienić jej nazwę](./media/storage-insights-overview/fqa-change-workbook-name.png)

Aby zmienić nazwę kroku w zapisanym skoroszycie, wybierz opcję Edytuj w obszarze krok, a następnie wybierz koła zębatego u dołu ustawień.

![Wybierz pozycję Edytuj u dołu kroku skoroszytu, aby otworzyć ustawienia ](./media/storage-insights-overview/fqa-edit.png)
 ![ w obszarze Ustawienia wybierz koła zębatego u dołu, aby zmienić nazwę kroku.](./media/storage-insights-overview/fqa-change-name.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat scenariuszy, w których są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty oraz [tworzyć raporty interaktywne przy użyciu skoroszytów Azure monitor](../platform/workbooks-overview.md).
