---
title: Zarządzanie pamięcią podręczną HPC i aktualizowanie jej
description: Jak zarządzać pamięcią podręczną HPC i aktualizować go przy użyciu witryny Azure portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537954"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Zarządzanie pamięcią podręczną z witryny Azure portal

Strona omówienie pamięci podręcznej w witrynie Azure Portal zawiera szczegóły projektu, stan pamięci podręcznej i podstawowe statystyki pamięci podręcznej. Ma również formanty, aby zatrzymać lub uruchomić pamięć podręczną, usunąć pamięć podręczną, opróżnić dane do magazynu długoterminowego i zaktualizować oprogramowanie.

Aby otworzyć stronę przeglądu, wybierz zasób pamięci podręcznej w witrynie Azure Portal. Na przykład załaduj stronę **Wszystkie zasoby** i kliknij nazwę pamięci podręcznej.

![zrzut ekranu przedstawiający stronę Przeglądu wystąpienia pamięci podręcznej HPC platformy Azure](media/hpc-cache-overview.png)

Przyciski w górnej części strony mogą pomóc w zarządzaniu pamięcią podręczną:

* **Start** i [**stop**](#stop-the-cache) — zawiesza działanie pamięci podręcznej
* [**Opróżnianie**](#flush-cached-data) — zapisuje zmienione dane w celu docelowych magazynowania
* [**Uaktualnienie**](#upgrade-cache-software) — aktualizuje oprogramowanie pamięci podręcznej
* **Odśwież** — ponownie ładuje stronę przeglądu
* [**Usuń**](#delete-the-cache) — trwale niszczy pamięć podręczną

Przeczytaj więcej o tych opcjach poniżej.

## <a name="stop-the-cache"></a>Zatrzymywać pamięć podręczną

Można zatrzymać pamięć podręczną, aby zmniejszyć koszty w okresie nieaktywnym. Nie są naliczane opłaty za czas pracy bez przestojów, gdy pamięć podręczna jest zatrzymana, ale są naliczane za magazyn dysku przydzielonej pamięci podręcznej. (Szczegółowe informacje można znaleźć na stronie [z cennikiem).](https://aka.ms/hpc-cache-pricing)

Zatrzymana pamięć podręczna nie odpowiada na żądania klientów. Należy odinstalować klientów przed zatrzymaniem pamięci podręcznej.

Przycisk **Zatrzymaj** zawiesza aktywną pamięć podręczną. Przycisk **Zatrzymaj** jest dostępny, gdy stan pamięci podręcznej jest **zdrowy** lub **zdegradowany.**

![zrzut ekranu górnych przycisków z wyróżnionym przyciskiem Stop i wyskakującym komunikatem opisującym akcję zatrzymania i z pytaniem "czy chcesz kontynuować?". z przyciskami Tak (domyślnie) i Nie](media/stop-cache.png)

Po kliknięciu przycisku Tak, aby potwierdzić zatrzymanie pamięci podręcznej, pamięć podręczna automatycznie opróżnia jej zawartość do obiektów docelowych magazynu. Ten proces może zająć trochę czasu, ale zapewnia spójność danych. Na koniec stan pamięci podręcznej zmienia się na **Zatrzymana**.

Aby ponownie aktywować zatrzymaną pamięć podręczną, kliknij przycisk **Start.** Nie jest wymagane potwierdzenie.

![zrzut ekranu przedstawiający górne przyciski z wyróżnionym przyciskiem Start](media/start-cache.png)

## <a name="flush-cached-data"></a>Opróżnianie danych w pamięci podręcznej

Przycisk **Flush** na stronie przegląd informuje pamięć podręczną, aby natychmiast zapisać wszystkie zmienione dane, które są przechowywane w pamięci podręcznej do zaplecza docelowych magazynu. Pamięć podręczna rutynowo zapisuje dane do obiektów docelowych magazynu, więc nie jest konieczne, aby to zrobić ręcznie, chyba że chcesz upewnić się, że system magazynu zaplecza jest aktualny. Na przykład można użyć **Flush** przed zrobieniem migawki magazynu lub sprawdzanie rozmiaru zestawu danych.

> [!NOTE]
> Podczas procesu opróżnienia pamięci podręcznej nie można obsługiwać żądań klientów. Dostęp do pamięci podręcznej jest zawieszony i wznawia po zakończeniu operacji.

![zrzut ekranu górnych przycisków z podświetleniem Flush i wyskakującym komunikatem opisującym akcję opróżniania i z pytaniem "czy chcesz kontynuować?". z przyciskami Tak (domyślnie) i Nie](media/hpc-cache-flush.png)

Po uruchomieniu operacji opróżniania pamięci podręcznej pamięć podręczna przestaje akceptować żądania klientów, a stan pamięci podręcznej na stronie przeglądu zmienia się na **Opróżnianie**.

Dane w pamięci podręcznej są zapisywane w odpowiednich elementach docelowych magazynu. W zależności od ilości danych musi być opróżnione, proces może potrwać kilka minut lub ponad godzinę.

Po zapisaniu wszystkich danych do obiektów docelowych magazynu pamięć podręczna automatycznie rozpoczyna przyjmowanie żądań klientów ponownie. Stan pamięci podręcznej powraca do **w dobrej kondycji**.

## <a name="upgrade-cache-software"></a>Uaktualnianie oprogramowania pamięci podręcznej

Jeśli dostępna jest nowa wersja oprogramowania, przycisk **Uaktualnij** staje się aktywny. U góry strony powinien zostać wyświetlony komunikat dotyczący aktualizowania oprogramowania.

![zrzut ekranu górnego wiersza przycisków z włączonym przyciskiem Uaktualnij](media/hpc-cache-upgrade-button.png)

Dostęp klienta nie jest przerywany podczas uaktualniania oprogramowania, ale wydajność pamięci podręcznej spowalnia. Planowanie uaktualnienia oprogramowania w godzinach poza szczytem użytkowania lub w planowanym okresie konserwacji.

Aktualizacja oprogramowania może potrwać kilka godzin. Pamięci podręczne skonfigurowane z wyższą przepływnością trwać dłużej niż pamięci podręczne z mniejszych wartości przepływności szczytowej.

Gdy aktualizacja oprogramowania jest dostępna, będziesz miał tydzień lub tak, aby zastosować go ręcznie. Data zakończenia jest wymieniona w komunikacie o uaktualnieniu. Jeśli nie uaktualnisz w tym czasie, platforma Azure automatycznie zastosuje aktualizację do pamięci podręcznej. Czas automatycznego uaktualnienia nie jest konfigurowalny. Jeśli obawiasz się wpływu na wydajność pamięci podręcznej, należy uaktualnić oprogramowanie samodzielnie przed upływem okresu.

Jeśli pamięć podręczna zostanie zatrzymana po upływie daty zakończenia, pamięć podręczna automatycznie uaktualni oprogramowanie przy następnym uruchomieniu. (Aktualizacja może nie rozpocząć się natychmiast, ale rozpocznie się w pierwszej godzinie).)

Kliknij przycisk **Uaktualnij,** aby rozpocząć aktualizację oprogramowania. Stan pamięci podręcznej zmienia się na **Uaktualnienie** do czasu zakończenia operacji.

## <a name="delete-the-cache"></a>Usuwanie pamięci podręcznej

Przycisk **Usuń** niszczy pamięć podręczną. Po usunięciu pamięci podręcznej wszystkie jej zasoby są niszczone i nie są już obciążane kontem.

Woluminy magazynu zaplecza używane jako obiekty docelowe magazynu pozostają nienaruszone po usunięciu pamięci podręcznej. Można dodać je do przyszłej pamięci podręcznej później lub zlikwidować je oddzielnie.

> [!NOTE]
> Pamięć podręczna HPC usługi Azure nie automatycznie zapisuje zmienione dane z pamięci podręcznej do systemów magazynu zaplecza przed usunięciem pamięci podręcznej.
>
> Aby upewnić się, że wszystkie dane w pamięci podręcznej zostały zapisane w magazynie długoterminowym, [zatrzymaj pamięć podręczną](#stop-the-cache) przed jej usunięciem. Upewnij się, że pokazuje stan **Zatrzymany** przed kliknięciem przycisku usuń.

## <a name="cache-metrics-and-monitoring"></a>Metryki pamięci podręcznej i monitorowanie

Strona przeglądowa zawiera wykresy dla niektórych podstawowych statystyk pamięci podręcznej — przepływność pamięci podręcznej, operacje na sekundę i opóźnienie.

![zrzut ekranu z trzema wykresami liniowymi przedstawiającymi statystyki wymienione powyżej dla przykładowej pamięci podręcznej](media/hpc-cache-overview-stats.png)

Te wykresy są częścią wbudowanych narzędzi do monitorowania i analizy platformy Azure. Dodatkowe narzędzia i alerty są dostępne na stronach pod nagłówkiem **Monitorowanie** na pasku bocznym portalu. Dowiedz się więcej w sekcji portalu [dokumentacji monitorowania platformy Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [narzędziach do metryk i statystyk platformy Azure](../azure-monitor/index.yml)
* Uzyskaj [pomoc dotyczącą pamięci podręcznej HPC usługi Azure](hpc-cache-support-ticket.md)
