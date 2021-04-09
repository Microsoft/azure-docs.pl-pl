---
title: Wymagania dotyczące przepustowości Remote Desktop Protocol Windows Virtual Desktop — Azure
titleSuffix: Azure
description: Informacje o wymaganiach dotyczących przepustowości RDP dla pulpitu wirtualnego systemu Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 6fe12c985f5d9a519380d1d9b5a7d6c2820630c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932339"
---
# <a name="remote-desktop-protocol-rdp-bandwidth-requirements"></a>Wymagania dotyczące przepustowości Remote Desktop Protocol (RDP)

Remote Desktop Protocol (RDP) to zaawansowana technologia, która używa różnych technik do doskonałego dostarczania zdalnej grafiki serwera do urządzenia klienckiego. W zależności od przypadku użycia, dostępności zasobów obliczeniowych i przepustowości sieci protokół RDP dynamicznie dostosowuje różne parametry w celu zapewnienia najlepszego środowiska użytkownika.

Remote Desktop Protocol multipleksuje wiele dynamicznych kanałów wirtualnych (DVCs) do pojedynczego kanału danych wysyłanego przez różne transporty sieciowe. Istnieją osobne DVCs do zdalnej grafiki, danych wejściowych, przekierowywania urządzeń, drukowania i nie tylko. Partnerzy pulpitu wirtualnego systemu Windows mogą również korzystać z rozszerzeń, które używają interfejsów WSPÓŁBIEŻNE.

Ilość danych przesyłanych za pośrednictwem protokołu RDP zależy od działania użytkownika. Na przykład użytkownik może pracować z podstawową zawartością tekstową w przypadku większości sesji i zużywać minimalną przepustowość, ale następnie generować wydruk dokumentu ze strony 200 do drukarki lokalnej. To zadanie drukowania będzie używać znacznej przepustowości sieci.

W przypadku korzystania z sesji zdalnej dostępna przepustowość sieci znacznie ma wpływ na jakość środowiska. Różne aplikacje i rozdzielczości ekranu wymagają różnych konfiguracji sieci, dlatego należy się upewnić, że konfiguracja sieci spełnia Twoje potrzeby.

## <a name="estimating-bandwidth-utilization"></a>Szacowanie wykorzystania przepustowości

Protokół RDP używa różnych algorytmów kompresji dla różnych typów danych. W poniższej tabeli przedstawiono sposób szacowania transferów danych:

| Typ danych | Kierunek | Jak oszacować |
|---|---|---|
| Grafika zdalna | Host sesji dla klienta | [Zapoznaj się ze szczegółowymi wskazówkami](#estimating-bandwidth-used-by-remote-graphics) |
| Pulsów | Oba kierunki | 20 bajtów co 5 sekund  |
| Dane wejściowe | Klient do hosta sesji | Ilość danych jest oparta na aktywności użytkownika, mniejszej niż 100 bajtów w przypadku większości operacji  |
| Transfery plików | Oba kierunki | Transfery plików używają kompresji zbiorczej. Użyj kompresji. zip do przybliżenia |
| Drukowanie | Host sesji dla klienta | Transfer zadań drukowania zależy od sterownika i przy użyciu kompresji zbiorczej, do przybliżania |

Inne scenariusze mogą ulec zmianie w zależności od sposobu ich użycia, na przykład:

* Konferencje głosowe lub wideo
* Komunikacja w czasie rzeczywistym
* Wideo na temat strumienia 4K

## <a name="estimating-bandwidth-used-by-remote-graphics"></a>Szacowanie przepustowości używanej przez zdalną grafikę

Wykorzystanie przepustowości przez Pulpit zdalny jest trudne. Działania użytkownika generują większość ruchu pulpitu zdalnego. Każdy użytkownik jest unikatowy i różnice w ich wzorcach pracy mogą znacząco zmienić użycie sieci.

Najlepszym sposobem na zrozumienie wymagań dotyczących przepustowości jest monitorowanie rzeczywistych połączeń użytkowników. Monitorowanie można wykonać za pomocą wbudowanych liczników wydajności lub sprzętu sieciowego.

Jednak w wielu przypadkach można oszacować wykorzystanie sieci, opisując sposób działania Remote Desktop Protocol i analizując wzorce pracy użytkowników.

Protokół zdalny zapewnia grafikę wygenerowaną przez serwer zdalny w celu wyświetlenia jej na monitorze lokalnym. Dokładniej mówiąc, zapewnia to mapę bitową w całości na serwerze.
Wysyłanie mapy bitowej pulpitu wygląda jak proste zadanie przy pierwszym podejściu, dlatego wymaga znacznej ilości zasobów. Na przykład obraz stacjonarny 1080p w jego nieskompresowanym formacie ma rozmiar 8 MB. Wyświetlanie tego obrazu na monitorze podłączonym lokalnie z nieumiarkowaną częstotliwością odświeżania ekranu 30 Hz wymaga przepustowości około 237 MB/s.

Aby zmniejszyć ilość danych przesyłanych przez sieć, protokół RDP używa kombinacji wielu technik, w tym między innymi

* Optymalizacje szybkości klatek
* Klasyfikacja zawartości ekranu
* Kodery-dekoder dla konkretnych treści
* Kodowanie obrazu progresywnego
* Buforowanie po stronie klienta

Aby lepiej zrozumieć zdalną grafikę, weź pod uwagę następujące kwestie:

* Im bogatsze grafiki, tym większa przepustowość.
  * Tekst, elementy interfejsu użytkownika okna oraz obszary pełnego koloru zużywają mniejszą przepustowość niż inne.
  * Obrazy naturalne są najbardziej znaczącymi współautorami w celu wykorzystania przepustowości. Jednak buforowanie po stronie klienta ułatwia jego zmniejszenie.
* Przesyłane są tylko zmienione części ekranu. Jeśli na ekranie nie ma żadnych widocznych aktualizacji, nie są wysyłane żadne aktualizacje.
* Odtwarzanie wideo i inna zawartość o wysokiej rozdzielczości są zasadniczo pokazem slajdów obrazu. Protokół RDP dynamicznie używa odpowiednich koderów wideo, aby dostarczyć je z szybkością odtwarzania po klatce oryginalna. Jednak nadal jest to grafika i nadal jest to najbardziej znaczący współautor do wykorzystania przepustowości.
* Czas bezczynności w usłudze pulpitu zdalnego oznacza brak aktualizacji ekranu lub ich minimalne; w związku z tym użycie sieci jest minimalne w czasie bezczynności.
* Gdy okno klienta pulpitu zdalnego jest zminimalizowane, nie są wysyłane żadne aktualizacje graficzne z hosta sesji.

Należy pamiętać, że obciążenie w sieci zależy od szybkości klatek danych wyjściowych i rozdzielczości ekranu. Jeśli zwiększy się szybkość klatek lub rozdzielczość ekranu, to również wymaganie dotyczące przepustowości. Na przykład lekkie obciążenie z wyświetlaczem o wysokiej rozdzielczości wymaga większej dostępnej przepustowości niż lekkie obciążenie z normalnymi lub niskimi rozwiązaniami. Różne rozdzielczości wyświetlania wymagają różnych dostępnych przepustowości.

W poniższej tabeli przedstawiono szacowanie danych używanych przez różne scenariusze grafiki. Te numery mają zastosowanie do jednej konfiguracji monitora z rozdzielczością 1920 x 1080 i z trybem grafiki w trybie graficznym H. 264/AVC 444.

| Scenariusz | Tryb domyślny | Tryb H. 264/AVC 444 | Miniaturk | Opis scenariusza |
|:---|---:|---:|---|---|
| Okresy | 0,3 KB/s | 0,3 KB/s |:::image type="content" source="media/idle.png" alt-text="Zrzut ekranu przedstawiający połączenie bezczynne":::| Użytkownik wstrzymał swoją pracę i nie ma aktywnych aktualizacji ekranu |
| Microsoft Word | 100-150 kb/s | 200-300 KB/s |:::image type="content" source="media/word.gif" alt-text="Animacja programu Microsoft Word":::| Użytkownik aktywnie pracuje z programem Microsoft Word, wpisując, wklejając grafikę i przełączając się między dokumentami |
| Microsoft Excel | 150-200 KB/s | 400-500 KB/s |:::image type="content" source="media/excel.gif" alt-text="Animacja programu Microsoft Excel":::| Użytkownik aktywnie pracuje z programem Microsoft Excel, wiele komórek z formułami i wykresami jest jednocześnie aktualizowanych |
| Microsoft PowerPoint | 4 – 4.5 MB/s | 1.6-1.8 MB/s |:::image type="content" source="media/powerpoint.gif" alt-text="Animacja programu Microsoft PowerPoint":::| Użytkownik aktywnie pracuje z programem Microsoft PowerPoint, wpisując, wklejając. Użytkownik modyfikuje także zaawansowaną grafikę i używa efektów przejścia slajdu |
| Przeglądanie sieci Web | 6 — 6.5 MB/s | 0.9-1 MB/s |:::image type="content" source="media/web.gif" alt-text="Animacja przeglądania sieci Web":::| Użytkownik aktywnie pracuje z rozbudowaną graficznie witryną sieci Web, która zawiera wiele obrazów statycznych i animowanych. Użytkownik przewija strony w poziomie i w pionie |
| Galeria obrazów | 3.3-3.6 MB/s | 0.7-0,8 MB/s |:::image type="content" source="media/image-gallery.gif" alt-text="Animacja galerii obrazów":::| Użytkownik aktywnie pracuje z aplikacją galerii obrazów. przeglądanie, powiększanie, zmiana rozmiaru i obracanie obrazów |
| Odtwarzanie wideo | 8,5-9,5 MB/s | 2,5 MB/s |:::image type="content" source="media/video.gif" alt-text="Animacja odtwarzania wideo":::| Użytkownik ogląda wideo o 30 FPS, które wykorzystuje 1/2 ekranu |
| Pełnoekranowe odtwarzanie wideo | 7.5 – 8,5 MB/s | 2.5 – 3.1 MB/s |:::image type="content" source="media/fullscreen-video.gif" alt-text="Animacja pełnoekranowego odtwarzania wideo":::| Użytkownik ogląda wideo o 30 FPS, które zmaksymalizowano do pełnoekranowego |

## <a name="dynamic-bandwidth-allocation"></a>Alokacja przepustowości dynamicznej

Remote Desktop Protocol jest nowoczesnym protokołem zaprojektowanym do dynamicznego dostosowywania do zmieniających się warunków sieciowych.
Zamiast korzystać z stałych limitów wykorzystania przepustowości, protokół RDP korzysta z ciągłego wykrywania sieci, które aktywnie monitoruje dostępną przepustowość sieci i czas błądzenia pakietu. Zgodnie z ustaleniami protokół RDP dynamicznie wybiera Opcje kodowania grafiki i przydziela przepustowość na potrzeby przekierowywania urządzeń i innych kanałów wirtualnych.  
Ta technologia umożliwia używanie przez protokół RDP pełnego potoku sieci, gdy jest on dostępny, i trwa jego szybkie wycofywanie, gdy sieć jest niezbędna dla innych.
Protokół RDP wykrywa i dostosowuje jakość obrazu, szybkość klatek lub algorytmy kompresji, jeśli inne aplikacje żądają sieci.

## <a name="limit-network-bandwidth-use-with-throttle-rate"></a>Ogranicz użycie przepustowości sieci z szybkością ograniczania

W większości scenariuszy nie trzeba ograniczać wykorzystania przepustowości, ponieważ ograniczenie może mieć wpływ na środowisko użytkownika. Jeszcze w ograniczonych sieciach możesz chcieć ograniczyć wykorzystanie sieci. Innym przykładem są dzierżawione sieci, które są obciążane opłatami za ilość używanego ruchu.

W takich przypadkach można ograniczyć wychodzący ruch sieciowy protokołu RDP przez określenie częstotliwości ograniczania w zasadach QoS.

  >[!NOTE]
  > [Upewnij się, że SHORTPATH RDP jest włączona](./shortpath.md) — ograniczanie szybkości ograniczania nie jest obsługiwane w przypadku transportu odwrotnego.

### <a name="implement-throttle-rate-limiting-on-session-host-using-group-policy"></a>Zaimplementuj ograniczenie szybkości ograniczania na hoście sesji przy użyciu zasady grupy

Aby ustawić wstępnie zdefiniowaną częstotliwość ograniczania przepustowości, można użyć Quality of Service opartych na zasadach (QoS) w zasady grupy.

Aby utworzyć zasady QoS dla hostów sesji przyłączonych do domeny, należy najpierw zalogować się na komputerze, na którym zainstalowano zasady grupy zarządzaniem. Otwórz przystawkę Zarządzanie zasady grupyami (wybierz pozycję Start, wskaż pozycję Narzędzia administracyjne, a następnie wybierz pozycję zasady grupy Management), a następnie wykonaj następujące czynności:

1. W obszarze Zarządzanie zasady grupy zlokalizuj kontener, w którym mają zostać utworzone nowe zasady. Na przykład jeśli cała sesja zawiera komputery znajdujące się w jednostce organizacyjnej o nazwie **hosty sesji**, należy utworzyć nowe zasady w sesji HOSTY jednostki organizacyjnej.

2. Kliknij prawym przyciskiem myszy odpowiedni kontener, a następnie wybierz pozycję **Utwórz obiekt zasad grupy w tej domenie i umieść tu łącze**.

3. W oknie dialogowym **nowy obiekt zasad grupy** wpisz nazwę nowego obiektu zasady grupy w polu **Nazwa** , a następnie wybierz przycisk **OK**.

4. Kliknij prawym przyciskiem myszy nowo utworzone zasady, a następnie wybierz polecenie **Edytuj**.

5. W Edytor zarządzania zasadami grupy rozwiń węzeł **Konfiguracja komputera**, rozwiń węzeł **Ustawienia systemu Windows**, kliknij prawym przyciskiem myszy pozycję **QoS oparte na zasadach**, a następnie wybierz pozycję **Utwórz nowe zasady**.

6. W oknie dialogowym **ustawienia QoS oparte na zasadach** na stronie Otwieranie wpisz nazwę nowych zasad w polu **Nazwa** . Wybierz pozycję **Określ szybkość ograniczania ruchu wychodzącego** i ustaw wymaganą wartość, a następnie wybierz pozycję **dalej**.

7. Na następnej stronie wybierz **tylko aplikacje o tej nazwie pliku wykonywalnego** i wprowadź nazwę **svchost.exe**, a następnie wybierz przycisk **dalej**. To ustawienie powoduje, że zasady umożliwiają tylko określanie priorytetów pasującego ruchu z usługi Pulpit zdalny.

8. Na trzeciej stronie upewnij się, że wybrano zarówno **Dowolny źródłowy adres IP** , jak i **docelowy adres IP** . Wybierz opcję **Dalej**. Te dwa ustawienia zapewniają, że pakiety będą zarządzane niezależnie od tego, który komputer (adres IP) wysłał pakiety, a komputer (adres IP) otrzyma pakiety.

9. Na stronie cztery wybierz pozycję **UDP** z listy rozwijanej **Wybierz protokół, którego dotyczą te zasady QoS** .

10. Pod nagłówkiem **Określ numer portu źródłowego**, wybierz **z tego portu źródłowego lub zakresu**. W polu tekstowym towarzyszące wpisz **3390**. Wybierz pozycję **Zakończ**.

Nowo utworzone zasady nie zaczną obowiązywać przed odświeżeniem zasady grupy na komputerach hosta sesji. Mimo że zasady grupy okresowo odświeża własne, można wymusić natychmiastowe odświeżanie, wykonując następujące czynności:

1. Na każdym hoście sesji, dla którego chcesz odświeżyć zasady grupy, Otwórz wiersz polecenia jako administrator (*Uruchom jako administrator*).

2. W wierszu polecenia wprowadź

   ```console
   gpupdate /force
   ```

### <a name="implement-throttle-rate-limiting-on-session-host-using-powershell"></a>Implementowanie ograniczania przepustowości na hoście sesji przy użyciu programu PowerShell

Można ustawić wartość ograniczenia przepustowości dla shortpath RDP przy użyciu poniższego polecenia cmdlet programu PowerShell:

```powershell
New-NetQosPolicy -Name "RDP Shortpath" -AppPathNameMatchCondition "svchost.exe" -IPProtocolMatchCondition UDP -IPSrcPortStartMatchCondition 3390 -IPSrcPortEndMatchCondition 3390 -DSCPAction 46 -NetworkProfile All
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o wymaganiach dotyczących przepustowości dla pulpitu wirtualnego systemu Windows, zobacz [Opis wymagań dotyczących przepustowości Remote Desktop Protocol (RDP) dla pulpitu wirtualnego systemu Windows](rdp-bandwidth.md).
* Aby dowiedzieć się więcej o łączności z siecią pulpitu wirtualnego systemu Windows, zobacz [Opis łączności z siecią pulpitu wirtualnego systemu Windows](network-connectivity.md).
* Aby rozpocząć pracę z usługą Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows, zobacz [implementacja Quality of Service (QoS) dla pulpitu wirtualnego systemu Windows](rdp-quality-of-service-qos.md).
