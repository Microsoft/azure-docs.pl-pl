---
title: Korzystanie z pulpitu nawigacyjnego konsoli czujnika
description: Pulpit nawigacyjny umożliwia szybkie wyświetlenie stanu zabezpieczeń sieci. Zapewnia ogólne omówienie zagrożeń dla całego systemu na osi czasu wraz z informacjami o powiązanych urządzeniach.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c9afc22cd123a782c9ee0247952c78c72ee916c0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523638"
---
# <a name="the-dashboard"></a>Pulpit nawigacyjny

Pulpit nawigacyjny umożliwia szybkie wyświetlenie stanu zabezpieczeń sieci. Zapewnia ogólne omówienie zagrożeń dla całego systemu na osi czasu wraz z informacjami o powiązanych urządzeniach, w tym:

- Alerty o różnych poziomach ważności:

- Krytyczne

- Duży

- Mały

- Ostrzeżenia

- Dwa wskaźniki na środku strony pokazują pakiety na sekundę (PPS) i niepotwierdzone alerty (UA). **PPS** to liczba pakietów potwierdzonych przez system na sekundę. **UA** to liczba alertów, które nie zostały jeszcze potwierdzone.

- Lista niepotwierdzonych alertów wraz z ich opisem.

- Oś czasu z opisem alertu.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Pulpit nawigacyjny":::

## <a name="viewing-the-latest-alerts"></a>Wyświetlanie najnowszych alertów

Wskaźnik niepotwierdzonych alertów (UA) na środku strony wskazuje liczbę takich alertów. Aby wyświetlić listę alertów, wybierz pozycję **więcej alertów** w dolnej części strony pulpit nawigacyjny lub wybierz pozycję **alerty** w menu po stronie.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Niepotwierdzone alerty":::

## <a name="status-boxes"></a>Pola stanu

Każde pole Stan zostało opisane w tej sekcji.

| Pole stanu i mierniki | Opis |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Alerty krytyczne"::: | **Alerty krytyczne** — pole w górnej części strony wskazuje liczbę alertów krytycznych. Zaznacz to pole, aby wyświetlić opisy alertów na osi czasu i na liście w miernikach (jeśli istnieją).                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Alerty główne"::: | **Alerty główne** — pole w prawym górnym rogu strony wskazuje liczbę głównych alertów. Zaznacz to pole, aby wyświetlić opisy alertów na osi czasu i na liście w miernikach (jeśli istnieją).                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Alerty pomocnicze"::: | **Alerty pomocnicze** — pole w lewym dolnym rogu strony wskazuje liczbę drobnych alertów. Zaznacz to pole, aby wyświetlić opisy alertów na osi czasu i na liście w miernikach (jeśli istnieją).                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="Alerty ostrzegawcze"::: | **Alerty ostrzegawcze** — pole w dolnej części strony wskazuje liczbę alertów ostrzegawczych. Zaznacz to pole, aby wyświetlić opisy alertów na osi czasu i na liście w miernikach (jeśli istnieją).                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="All Alerts"::: | **Wszystkie alerty** — pole w prawym dolnym rogu strony wskazuje łączną liczbę alertów krytycznych, głównych, pomocniczych i ostrzeżeń. Zaznacz to pole, aby wyświetlić opisy alertów na osi czasu i na liście w miernikach (jeśli istnieją). |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Liczba pakietów na sekundę"::: | **Pakiety na sekundę (PPS)** — Metryka PPS jest wskaźnikiem wydajności sieci. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Zdarzenia niepotwierdzone (UA)"::: | **Niepotwierdzone zdarzenia** — ta Metryka wskazuje liczbę niepotwierdzonych zdarzeń.

## <a name="using-the-timeline"></a>Korzystanie z osi czasu

Alerty są wyświetlane wzdłuż pionowej osi czasu, która zawiera informacje o dacie i godzinie.

Oś czasu wyświetlana graficznie:

- Alerty krytyczne

- Alerty główne

- Alerty pomocnicze

- Alerty ostrzegawcze

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="Wykres osi czasu":::

## <a name="viewing-alerts"></a>Wyświetlanie alertów

Wybierz strzałkę w dół znajdującą **się u dołu** pola alertu, aby wyświetlić informacje o wpisie i urządzeniach alertu.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Informacje o wpisie i urządzeniach alertu":::

- Wybierz urządzenie, aby wyświetlić mapę trybu fizycznego. Urządzenia poddane są wyróżnione.

- Kliknij w dowolnym miejscu w oknie alertu, aby wyświetlić dodatkowe szczegóły dotyczące alertu. Zostanie wyświetlone okno podręczne podobne do przedstawionego poniżej

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: , aby wyeksportować plik CSV dotyczący alertu.

- Tylko Administratorzy i analitycy zabezpieczeń — wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="Potwierdź wszystkie"::: , aby **potwierdzić wszystkie** skojarzone alerty.

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::, aby pobrać raport o alercie jako plik PDF.

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="Przypnij":::, aby przypiąć lub odpiąć alert. Wybranie do numeru PIN spowoduje dodanie go do okna **przypiętych alertów** na ekranie **alertów** .

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="Pobierz"::: , aby zbadać alert przez pobranie powiązanego pliku PCAP zawierającego analizę protokołu sieciowego.

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="chmura"::: , aby pobrać powiązany plik PCAP, który zawiera tylko pakiety powiązane z alertami, co zmniejsza rozmiar pliku wyjściowego i pozwala na bardziej ukierunkowaną analizę. Można go wyświetlić za pomocą programu [Wireshark](https://www.wireshark.org/).

- Wybierz pozycję :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="Nawigacja"::: , aby przejść do osi czasu zdarzenia w czasie żądanego alertu. Dzięki temu można ocenić inne zdarzenia, które mogą być wykonywane wokół określonego alertu.

- Administratorzy i analitycy zabezpieczeń — zmiana stanu alertu z niepotwierdzonego na potwierdzony. Wybierz pozycję nauka, aby zatwierdzić wykrytą aktywność.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Wykryto nieautoryzowaną łączność z Internetem":::

## <a name="see-also"></a>Zobacz też

[Pracuj z alertami na czujniku](how-to-work-with-alerts-on-your-sensor.md)
