---
title: 'Samouczek: Instalowanie urządzenia fizycznego usługi Azure FXT Edge'
description: Jak rozpakować, stojak i obmontować składnik urządzenia fizycznego w pamięci podręcznej magazynu hybrydowego Microsoft Azure FXT Edge
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 48ee6de28bcd76d4c484b77c981062bad1a3754d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219755"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Samouczek: Instalowanie usługi Azure FXT Edge

W tym samouczku opisano sposób instalowania węzła sprzętowego dla hybrydowej pamięci podręcznej magazynu usługi Azure FXT Edge. Należy zainstalować co najmniej trzy węzły sprzętu, aby utworzyć klaster usługi Azure FXT Edge.

Procedura instalacji obejmuje Rozpakowanie i zamontowanie w stojaku urządzenia, a następnie dołączenie ramienia zarządzania kablem (CMA) i z przodu. W oddzielnym samouczku wyjaśniono, jak dołączyć kable sieciowe i podłączyć moc.

Zainstalowanie węzła pliku usługi Azure FXT Edge trwa około godziny.

Ten samouczek obejmuje następujące kroki instalacji:

> [!div class="checklist"]
>
> * Rozpakowywanie urządzenia
> * Instalowanie urządzenia w stojaku
> * Zainstaluj przednią ramkę (opcjonalnie)

## <a name="installation-prerequisites"></a>Wymagania wstępne instalacji

Przed rozpoczęciem upewnij się, że używane centrum danych i stojak są następujące:

* Dostępne miejsce 1U w stojaku, w którym ma zostać zainstalowane urządzenie.
* Zasilacz sieciowy i systemy chłodzenia, które spełniają potrzeby usługi Azure FXT Edge. (Aby uzyskać pomoc w planowaniu i zmianie rozmiarów instalacji), należy zapoznać się [z](fxt-specs.md#power-and-thermal-specifications) tematem.  

  > [!NOTE]
  > Aby w pełni wykorzystać dwie nadmiarowe jednostki zasilacza (PSUs), należy użyć jednostek dystrybucji zasilania w dwóch różnych obwodach gałęzi podczas dołączania zasilania sieciowego. Aby uzyskać szczegółowe informacje, Przeczytaj [łącza do kabli zasilających](fxt-network-power.md#connect-power-cables) .  

## <a name="unpack-the-hardware-node"></a>Rozpakowywanie węzła sprzętowego

Każdy węzeł usługi Azure FXT Edge jest dostarczany w jednym polu. Wykonaj te kroki, aby rozpakować urządzenie.

1. Umieść pudełko na płaskiej, poziomej powierzchni.

2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.

3. Rozpakuj zawartość pudełka. Upewnij się, że zawiera on następujące elementy:
   * Jedno urządzenie FXT z jednym obudową
   * dwa przewody zasilania,
   * Jedna zewnętrzna osłona i klucz
   * Jeden zestaw szyn
   * Jedna ARM zarządzania kablem (CMA)
   * Książeczka instrukcji instalacji CMA
   * Broszura z instrukcjami dotyczącymi instalacji stojaka
   * Broszura dotycząca bezpieczeństwa, środowiska i informacji prawnych

Jeśli nie odebrano wszystkich elementów na liście, skontaktuj się z dostawcą urządzenia, aby uzyskać pomoc techniczną.

Upewnij się, że urządzenie miało wystarczająco dużo czasu, aby osiągnąć tę samą temperaturę co pomieszczenie przed zainstalowaniem jej lub włączenia. Jeśli zauważysz kondensację na dowolnej części urządzenia, odczekaj co najmniej 24 godziny przed zainstalowaniem programu.

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie usługi Azure FXT Edge musi być zainstalowane w standardowym stojaku z 19 cala.

Hybrydowa pamięć podręczna magazynu usługi Azure FXT Edge składa się z trzech lub większej liczby urządzeń usługi Azure FXT Edge. Powtórz kroki instalacji stojaka dla każdego urządzenia, które jest częścią systemu.

### <a name="rack-install-prerequisites"></a>Wymagania wstępne instalacji stojaka

* Przed rozpoczęciem należy zapoznać się z instrukcjami dotyczącymi bezpieczeństwa, środowiska i karnetu informacji o przepisach, które zostały dostarczone z urządzeniem.

  > [!NOTE]
  > Należy zawsze używać dwóch osób podczas podnoszenia węzła, w tym podczas instalowania go w stojaku lub usuwania z stojaka.

* Określ typ instalacji szyny używanej w stojaku sprzętowym.
  * W przypadku stojaków z przystawką kwadratową lub okrągłą należy postępować zgodnie z instrukcjami dotyczącymi szyny.
  * W przypadku stojaków z gwintowanym otworem postępuj zgodnie z instrukcjami dotyczącymi szyny dla wątków.
  
    W przypadku konfiguracji z zainstalowaniem szyny dla narzędzia należy dostarczyć osiem wkrętów, typu 10-32, 12-24, M5 lub M6. Średnica głowy ramion musi być mniejsza niż 10 mm (0,4 ").

### <a name="identify-the-rail-kit-contents"></a>Zidentyfikuj zawartość zestawu szyn

Znajdź składniki służące do instalowania zestawu szyny:

1. Dwa zestawy szynowe firmy Dell ReadyRails II (1)
1. Dwa punkty zaczepienia i pętle pętli (2)

![Numerowany rysunek zawartości zestawu szynowego](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Zestawienie szynowe — szyny bez narzędzia (Stojaki kwadratowe lub okrągłe otwory)

W przypadku stojaków z przystawką kwadratowych lub okrągłych otworów postępuj zgodnie z tą procedurą, aby połączyć i zainstalować szyny.

1. Umieść piony po lewej i prawej stronie z etykietą od **przodu** do wewnątrz. Umieść każdy element końcowy tak, aby znajdował się w dziurach z lewej strony kołnierza stojaka pionowego. jedno

2. Wyrównaj każdy element końcowy w dolnej i górnej części regału w miejscu, w którym chcesz go zainstalować. (2)

3. Zaangażuj wewnętrzną końcówkę szyny do momentu, w którym w pełni nastąpi montaż w pionie i zamknie. Powtórz te kroki, aby pomieścić i umieścić element frontonu na kołnierzu pionowym. r.3

> [!TIP]
> Aby usunąć szyny, należy ściągnąć przycisk Zwolnij do końca częściowego punktu środkowego (4) i odłączać każdą kolejkę.

![Diagram instalacji i usuwania szyn z mniejszą ilością narzędzi, z numerami kroków](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Szyny oparte na zestawie szynowym (Stojaki z gwintowanym otworem)

W przypadku stojaków z otworami gwintowanych należy wykonać tę procedurę, aby połączyć i zainstalować szyny.

1. Usuń numery PIN z nawiasów montażowych przednich i tylnych z płaskim śrubokrętem. jedno
1. Pobierz i obróć podzestawy zamków szyny, aby usunąć je z nawiasów montażowych. (2)
1. Dołącz do lewej i prawej szynę montażową do czołowych pionowych kołnierzy stojaków przy użyciu dwóch par wkrętów. r.3
1. Przesuń w lewo i w prawo nawiasy klamrowe w przód do tylnej krawędzi pionowej i Dołącz je przy użyciu dwóch par wkrętów. czwart

![Diagram instalacji i usuwania szyn z narzędziami z numerami kroków](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Instalowanie systemu w stojaku

Wykonaj następujące kroki, aby zainstalować urządzenie usługi Azure FXT Edge w stojaku.

1. Ciągnij wewnętrzne slajdy z stojaka do momentu zablokowania ich na miejscu. jedno
1. Znajdź Standoff szyny tylnej na każdej stronie urządzenia i Obniż je do tylnych gniazd J na zestawach slajdów. (2)
1. Obróć urządzenie w dół do momentu, aż wszystkie standoffs szyny zostaną przyłączone do miejsca J-gnieździe. r.3
1. Wypchnij urządzenie do wewnątrz do momentu kliknięcia dźwigni blokady.
1. Naciśnij przyciski blokady przesuń w dół na szynach (4) i przesuń urządzenie do stojaka.

![Zainstaluj system na diagramie stojaków z numerami kroków](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Usuń system z stojaka

Aby usunąć urządzenie z stojaka, wykonaj poniższą procedurę.

1. Znajdź dźwignie blokady po bokach szyn wewnętrznych (1).
2. Odblokuj każdą dźwignię, obracając ją w jej pozycji wydania (2).
3. Opanujesz boki systemu i ściągaj je do przodu, aż do momentu, gdy kolejka standoffs nie znajduje się na wierzchu miejsc J. Unieś system z stojakiem i umieść go na powierzchni poziomej (3).

![Ilustracja przedstawiająca Usuwanie systemu z stojaka z ponumerowanymi krokami](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Korzystanie z zamka Slam

1. Po lewej stronie Znajdź zatrzask Slam (1) po obu stronach systemu.
2. Zatrzaśnięcia odbywa się automatycznie, gdy system jest wypychany do stojaka.

Aby zwolnić zamki podczas usuwania systemu, wyciągnij je (2).

W celu zabezpieczenia systemu w stojaku na potrzeby przesyłania lub w innych środowiskach niestabilnych są udostępniane opcjonalne wtyczki twarde instalacji. Znajdź złączenie w każdym zatrzasku i zwiększ je za pomocą #2 śrubokrętu (3).

![Ponumerowana ilustracja atrakcyjności i zwalniania zamka Slam](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Instalowanie ARM zarządzania kablem

Opcjonalna ARM zarządzania kablem (CMA) jest udostępniana z programem FXT Edge. Instrukcje dotyczące instalacji są dostępne w pakiecie.

1. Rozpakuj i zidentyfikuj składniki zestawu ARM zarządzania kablem:
   * CMA (1)
   * CMA (2)
   * Zawijanie kabli światłowodowych (3)
   * CMA — nawiasy kwadratowe (4)
   * Kabel wskaźnika stanu (5)

   > [!TIP]
   > Aby zabezpieczyć CMA do wysłania w stojaku, należy zapętlić, aby powiązać ją z koszykami i cinch. Zabezpieczenie CMA w ten sposób spowoduje również zabezpieczenie systemu w środowiskach niestabilnych.

   ![Ilustracja przedstawiająca części CMA](media/fxt-install/cma-kit-400.png)

2. Zainstaluj zasobnik CMA.

   Zasobnik CMA zapewnia pomoc techniczną i pełni rolę dla CMA.

   1. Wyrównaj i Zaangażuj każdą stronę paska z nawiasami odbiorczymi na wewnętrznych krawędziach szyn.
   1. Wypchnij zasobnik do przodu do momentu kliknięcia na miejscu. jedno
   1. Aby usunąć ten pasek, należy pozbyć się przycisków zatrzasków w kierunku centrum i wyciągnąć z nich zasobnik z nawiasami odbiorczymi (2).

   ![Ilustracja przedstawiająca instalację zasobnika CMA](media/fxt-install/cma-tray-install-400.png)

3. Zainstaluj nawiasy klamrowe CMA.

   > [!NOTE]
   >
   > * Możesz dołączyć CMA do prawej lub lewej szyny montażowej, w zależności od tego, w jaki sposób ma zostać rozesłane kable z systemu.
   > * Dla wygody Zainstaluj CMA po stronie przeciwległej do zasilaczy (bok A). Jeśli jest zainstalowana na stronie B, CMA musi zostać rozłączona, aby usunąć zasilacz zewnętrzny.
   > * Zawsze usuwaj zasobnik przed usunięciem zasilaczy.

   ![Ilustracja przedstawiająca instalację CMAego nawiasu](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Wybierz odpowiedni CMAy wspornik dla strony, w której chcesz zainstalować CMA (strona B lub bok A).
   1. Zainstaluj CMAy nawiasy kwadratowe z odpowiednimi znakami A lub po lewej stronie.
   1. Wyrównaj otwory w nawiasach przy użyciu numerów PIN na szynie. Wypchnij nawias w dół do momentu jego zablokowania.

4. Zainstaluj CMA.

   1. W tylnej części systemu dopasowuje zatrzaśnięcie na frontonie CMAy w wewnętrznym nawiasie montażowym zestawu slajdu do momentu, gdy zamkzy zaangażują (1).
   1. Dopasowuje inne zatrzaśnięcie na końcu zewnętrznego nawiasu, aż do chwili zatrzaska (2).
   1. Aby usunąć CMA, nie należy naciskać obydwu zamków, naciskając przyciski wydania CMA w górnej części obudowy zamków wewnętrznych i zewnętrznych (3).

   ![Ilustracja przedstawiająca podstawową instalację CMA](media/fxt-install/cma-install-400.png)

   CMA można obrócić do systemu w celu uzyskania dostępu i usługi. Na zawiasie, unieś CMA z tacy, aby nie nawiązać z nią miejsca (1). Po odłączeniu od zasobnika CMA od systemu (2).

   ![Ilustracja CMA obrócona dla usługi](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Zainstaluj przednią ramkę (opcjonalnie)

W tej sekcji wyjaśniono, jak zainstalować i usunąć ramkę frontu (Faceplate) dla sprzętu usługi Azure FXT Edge.

Aby zainstalować przednią ramkę:

1. Zlokalizuj i Usuń klucz osłony, który znajduje się w pakiecie z osłoną.
1. Wyrównaj ramkę do przodu obudowy i Wstaw pinezki po prawej stronie osłony do otworów w stojaku po prawej stronie węzła.
1. Dopasowuje lewy koniec okna do obudowy. Naciskaj ramkę do momentu kliknięcia przycisku po lewej stronie.
1. Zablokuj ramkę kluczem.

Aby usunąć ramkę przednią:

1. Odblokuj ramkę przy użyciu klawisza.
1. Naciśnij przycisk Zwolnij po lewej stronie i przeciągnij lewy koniec osłony z obudowy.
1. Odpinaj prawy koniec i Usuń ramkę.

   ![Obraz przedstawiający przycisk Zwolnij znajdujący się po lewej stronie i jak go usunąć, pobierając na zewnątrz od lewej strony](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Następne kroki

Po rozpakowaniu urządzenia i przełączeniu go do programu Kontynuuj instalację, dołączając kable sieciowe i łącząc zasilanie z gniazdem zasilania z usługą Azure FXT Edge.

> [!div class="nextstepaction"]
> [Okablowanie portów sieciowych i zasilanie](fxt-network-power.md)
