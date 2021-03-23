---
title: Praca z alertami w lokalnej konsoli zarządzania
description: Korzystając z lokalnej konsoli zarządzania, można uzyskać wgląd w najnowsze zagrożenia w sieci i lepiej zrozumieć, jak użytkownicy czujnika obsługują te informacje.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: a99c489c54b2671a463c0c3dad1909c74b4fa203
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104781470"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Praca z alertami w lokalnej konsoli zarządzania 

W oknie **alerty** w konsoli zarządzania można wykonać następujące czynności:

- Pracuj z filtrami alertów

- Pracuj z licznikami alertów

- Wyświetl informacje o alertach

- Zarządzanie zdarzeniami alertów

- Utwórz reguły wykluczania alertu

- Wyzwalanie reguł wykluczania alertów z systemów zewnętrznych

- Przyspiesz przepływ pracy zdarzeń z grupami alertów

## <a name="view-alerts-in-the-on-premises-management-console"></a>Wyświetlanie alertów w lokalnej konsoli zarządzania

Lokalna Konsola zarządzania agreguje alerty ze wszystkich połączonych czujników. Zapewnia to korporacyjny widok ostatnich zagrożeń w sieci i pomaga lepiej zrozumieć, jak są obsługiwane przez użytkowników czujnika.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Zrzut ekranu przedstawiający okno alerty.":::

### <a name="work-with-alert-filters"></a>Pracuj z filtrami alertów

W oknie **alerty** są wyświetlane alerty wygenerowane przez czujniki połączone z lokalną konsolą zarządzania. Można wyświetlić wszystkie alerty dotyczące połączonych czujników lub przedstawić alerty wysyłane z określonego:

- Witryna

- Strefa

- Urządzenie

- Czujnik

Wybierz pozycję **Wyczyść filtry** , aby wyświetlić wszystkie alerty.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Wyczyść filtry, wybierając przycisk Wyczyść filtry.":::

### <a name="work-with-alert-counters"></a>Pracuj z licznikami alertów

Liczniki alertów zapewniają podział alertów według ważności i potwierdzonego stanu.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="Licznik alertu pokazuje, ile posiadanych alertów.":::

W liczniku alertu pojawią się następujące poziomy ważności:

- **Krytyczne**: wskazuje złośliwy atak, który powinien być obsłużony natychmiast.

- **Wersja główna**: wskazuje zagrożenie bezpieczeństwa, które jest ważne dla adresu.

- **Pomocniczy**: wskazuje pewne odchylenie od zachowania linii bazowej, które może zawierać zagrożenie bezpieczeństwa.

- **Ostrzeżenie**: wskazuje pewne odchylenie od zachowania linii bazowej bez zagrożeń bezpieczeństwa.

Poziomy ważności są wstępnie zdefiniowane.

Można dostosować licznik, aby zapewnić liczbę na podstawie potwierdzonych i niepotwierdzonych alertów. Alerty niepotwierdzone zostały wyzwolone w usłudze Defender dla czujników IoT, ale nie zostały jeszcze zweryfikowane przez operatorów na czujniku.

Po wybraniu opcji **Pokaż potwierdzone alerty** w oknie **alerty** są wyświetlane wszystkie potwierdzone alerty.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Wyświetlanie potwierdzonych alertów.":::

### <a name="view-alert-information"></a>Wyświetl informacje o alertach

Ten alert przedstawia następujące informacje:

- Podsumowanie zdarzenia alertu.

- Ważność alertu.

- Link do alertu w czujniku, który go wykryje.

- Identyfikator UUID alertu. Identyfikator UUID składa się z identyfikatora alertu, który jest skojarzony ze zdarzeniem alertu wykrytym na czujniku, oddzielonym łącznikiem, po którym następuje unikatowy numer IDENTYFIKACYJNy systemu.

**Identyfikator UUID alertu lokalnej konsoli zarządzania**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Urządzenie jest połączone, ale nie ma autoryzacji.":::

**Identyfikator alertu czujnika**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Identyfikator alertu czujnika.":::

Praca z identyfikatorami UUID gwarantuje, że każdy alert wyświetlany w lokalnej konsoli zarządzania jest możliwy do wyszukania i zidentyfikowania przy użyciu unikatowego numeru. Jest to wymagane, ponieważ alerty generowane na podstawie wielu czujników mogą generować ten sam identyfikator alertu.

> [!NOTE]
> Domyślnie Identyfikatory UUID są wyświetlane w następujących systemach partnerskich podczas definiowania reguł przekazywania: ArcSight, serwery dziennika systemowego, QRadar, wskaźnikiem i monitorem. Instalacja nie jest wymagana.

Aby wyświetlić informacje o alercie:

- Z listy alertów wybierz alert.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Zrzut ekranu przedstawiający informacje o alercie.":::

Aby wyświetlić alert w czujniku:

- Wybierz pozycję **Otwórz czujnik** z alertu.

Aby wyświetlić urządzenia na mapie stref:

- Aby wyświetlić mapę urządzenia z fokusem na urządzeniu z alertami i wszystkimi podłączonymi do niego urządzeniami, wybierz pozycję **Pokaż urządzenia**.

## <a name="manage-alert-events"></a>Zarządzanie zdarzeniami alertów

Dostępne są różne opcje zarządzania zdarzeniami alertów z lokalnej konsoli zarządzania.

- Dowiedz się lub potwierdź zdarzenia alertów. Wybierz pozycję **Dowiedz się, & potwierdzić** , aby poznać wszystkie zdarzenia alertów, które mogą być autoryzowane, i zatwierdzić wszystkie zdarzenia alertów, które nie są obecnie potwierdzone.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Wybierz pozycję Dowiedz się, & potwierdzić, aby dowiedzieć się więcej.":::

- Wyciszenie i wyciszenie zdarzeń alertów.

Aby dowiedzieć się więcej na temat uczenia, potwierdzania i wyciszania zdarzeń alertów, zobacz artykuł [dotyczący czujnika zarządzania zdarzeniami](how-to-manage-the-alert-event.md) .

## <a name="export-alert-information"></a>Eksportowanie informacji o alercie

Eksportowanie informacji o alercie do pliku CSV. Możesz wyeksportować informacje o wszystkich alertach wykrytych lub wyeksportować informacje na podstawie filtrowanego widoku. Eksportowane są następujące informacje:

- Adres źródłowy
- Adres docelowy
- Tytuł alertu
- Ważność alertu
- Komunikat alertu
- Dodatkowe informacje
- Stan potwierdzenia
- Dostępność PCAP

Aby wyeksportować:

1. Z menu po lewej stronie wybierz pozycję alerty.
1. Wybierz pozycję Eksportuj.
1. Wybierz pozycję Eksportuj Rozszerzone alerty, aby wyeksportować informacje o alercie w osobnych wierszach dla każdego alertu obejmującego wiele urządzeń. Po wybraniu pozycji Eksportuj alerty rozszerzone zostanie utworzony zduplikowany wiersz alertu z unikatowymi elementami w każdym wierszu. Użycie tej opcji ułatwia badanie wyeksportowanych zdarzeń alertów.  

## <a name="create-alert-exclusion-rules"></a>Utwórz reguły wykluczania alertu

Poinstruuj usługę Defender for IoT, aby ignorował wyzwalacze alertów na podstawie:

- Strefy czasowe i okresy

- Adres urządzenia (IP, MAC, podsieć)

- Nazwy alertów

- Określony czujnik

Utwórz reguły wykluczania alertów, jeśli chcesz, aby usługa Defender for IoT ignorował działanie, które wyzwoli alert.

Jeśli na przykład wiadomo, że wszystkie urządzenia, które są monitorowane przez określony czujnik, przechodzą przez procedury konserwacji przez dwa dni, można zdefiniować regułę wykluczania, która instruuje usługę Defender for IoT, aby pominąć alerty wykryte przez ten czujnik w wstępnie zdefiniowanym okresie.

### <a name="alert-exclusion-logic"></a>Logika wykluczenia alertu

Oparta na logice reguła alertu `AND` . Oznacza to, że alert zostanie wyzwolony tylko wtedy, gdy spełnione są wszystkie warunki reguły.

Jeśli warunek reguły nie jest zdefiniowany, warunek będzie zawierać wszystkie opcje. Na przykład, jeśli nie dołączysz nazwy czujnika w regule, zostanie ona zastosowana do wszystkich czujników.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Zrzut ekranu przedstawiający widok Reguła tworzenia wykluczenia.":::

Podsumowania reguł są wyświetlane w oknie **reguła wykluczania** .

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Zrzut ekranu przedstawiający Widok podsumowania reguły wykluczania.":::

Oprócz pracy z regułami wykluczeń można pominąć alerty, wyłączając je.

### <a name="create-exclusion-rules"></a>Utwórz reguły wykluczania

Aby utworzyć reguły wykluczania:

1. W lewym okienku lokalnej konsoli zarządzania wybierz opcję **wykluczanie alertów**. Zdefiniuj nową regułę wykluczania, wybierając  ikonę Dodaj :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: w prawym górnym rogu okna, które zostanie otwarte. Zostanie otwarte okno dialogowe **Utwórz regułę wykluczania** .

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Utwórz wykluczenie alertu, wypełniając odpowiednie informacje.":::

2. Wprowadź nazwę reguły w polu **Nazwa** . Nazwa nie może zawierać cudzysłowów ( `"` ).

3. W sekcji **według strefy czasowej/okresu** wprowadź przedział czasu w określonej strefie czasowej. Użyj tej funkcji, jeśli reguła wykluczania zostanie utworzona dla określonego okresu w jednej strefie czasowej, ale powinna być zaimplementowana w innych strefach czasowych. Na przykład może być konieczne zastosowanie reguły wykluczania między 8:00 AM i 10:00 w trzech różnych strefach czasowych. W takim przypadku należy utworzyć trzy oddzielne reguły wykluczania, które używają tego samego czasu i odpowiedniej strefy czasowej.

4. Wybierz pozycję **Dodaj**. W okresie wykluczania nie są tworzone żadne alerty dotyczące połączonych czujników.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Zrzut ekranu przedstawiający widok przez okres.":::

5. W sekcji **według adresu urządzenia** Zdefiniuj:

  - Adres IP urządzenia, adres MAC lub adres podsieci, które mają zostać wykluczone.

  - Kierunek ruchu dla wykluczonych urządzeń, źródła i miejsca docelowego.

6. Wybierz pozycję **Dodaj**.

7. W sekcji **o tytule alertu** Rozpocznij wpisywanie tytułu alertu. Z listy rozwijanej wybierz tytuł lub tytuły alertów, które mają zostać wykluczone.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Zrzut ekranu przedstawiający widok tytułu alertów.":::

8. Wybierz pozycję **Dodaj**.

9. W sekcji **według nazwy czujnika** wpisz nazwę czujnika. Z listy rozwijanej wybierz czujnik lub czujniki, które chcesz wykluczyć.

10. Wybierz pozycję **Dodaj**.

11. Wybierz pozycję **Zapisz**. Nowa reguła zostanie wyświetlona na liście reguł.

Możesz pominąć alerty, wyciszyć lub utworzyć reguły wykluczania alertów. W tej sekcji opisano potencjalne przypadki użycia obu funkcji.

- **Reguła wykluczania**. Zapisz regułę wykluczania w przypadku:

  - Wcześniej wiadomo, że zdarzenie ma zostać wykluczone z bazy danych. Na przykład wiadomo, że scenariusz wykryty w określonym czujniku będzie wyzwalał nieistotne alerty. Na przykład będziesz wykonywać czynności konserwacyjne na PLCs organizacji w określonej lokacji i chcieć pominąć alerty związane z PLCs dla tej witryny.

  - Usługa Defender for IoT ma ignorować zdarzenia przez określony zakres czasu (w przypadku zadań konserwacji systemu).

  - Chcesz zignorować zdarzenia w określonej podsieci.

  - Chcesz kontrolować zdarzenia alertów wygenerowane z kilku czujników przy użyciu jednej reguły.

  - Nie chcesz śledzić wykluczenia alertu jako zdarzenia w dzienniku zdarzeń.

- **Wycisz**. Wycisz alert, gdy:

  - Elementy, które muszą zostać wyciszone, nie są planowane. Nie wiesz przed czasem, które zdarzenia będą nieistotne.

  - Chcesz pominąć alert z okna **alerty** , ale nadal chcesz go śledzić w dzienniku zdarzeń.

  - Chcesz zignorować zdarzenia w określonym kanale.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Wyzwalanie reguł wykluczania alertów z systemów zewnętrznych

Wyzwalanie reguł wykluczania alertów z systemów zewnętrznych. Na przykład Zarządzaj regułami wykluczeń z systemów biletów dla przedsiębiorstw lub systemów, które zarządzają procesami konserwacji sieci.

Zdefiniuj czujniki, aparaty, godzinę rozpoczęcia i godzinę zakończenia, aby zastosować regułę. Aby uzyskać więcej informacji, zobacz temat [czujnik interfejsów API usługi Defender for IoT i interfejsy API konsoli zarządzania](references-work-with-defender-for-iot-apis.md).

Reguły tworzone za pomocą interfejsu API pojawiają się w oknie **reguła wykluczania** jako ro.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Zrzut ekranu przedstawiający widok reguły wykluczania.":::

## <a name="see-also"></a>Zobacz też

[Pracuj z alertami na czujniku](how-to-work-with-alerts-on-your-sensor.md)
