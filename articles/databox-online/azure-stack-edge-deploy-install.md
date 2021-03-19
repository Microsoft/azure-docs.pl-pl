---
title: Samouczek dotyczący instalowania-rozpakowywania, stojaka, kabla Azure Stack Edge urządzenie fizyczne Pro | Microsoft Docs
description: Drugi samouczek dotyczący instalowania programu Azure Stack EDGE Pro obejmuje sposób rozpakowywania, stojaka i podłączenia do urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Azure Stack Edge Pro in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 9aa02521d91d41380b1bdac3efe50ab3d196a856
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90894196"
---
# <a name="tutorial-install-azure-stack-edge-pro"></a>Samouczek: Instalowanie Azure Stack EDGE Pro

W tym samouczku opisano sposób instalowania urządzenia fizycznego w programie Azure Stack Edge. Procedura instalacji obejmuje rozpakowywanie, montowanie na stojaku i podłączanie kabli urządzenia. 

Instalacja może potrwać około dwóch godzin.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące:

### <a name="for-the-azure-stack-edge-resource"></a>Dla zasobu brzegowego Azure Stack

Przed rozpoczęciem upewnij się, że:

* Wykonano wszystkie kroki z sekcji [przygotowanie do wdrożenia Azure Stack EDGE Pro](azure-stack-edge-deploy-prep.md).
    * Utworzono zasób Azure Stack Edge, aby wdrożyć urządzenie.
    * Klucz aktywacji został wygenerowany w celu aktywowania urządzenia przy użyciu zasobu brzegowego Azure Stack.

 
### <a name="for-the-azure-stack-edge-pro-physical-device"></a>Na urządzeniu fizycznym programu Azure Stack EDGE Pro

Przed wdrożeniem urządzenia:

- Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej.
- Sprawdź, czy lokacja, w której chcesz zamontować urządzenie, ma:
    - standardowe zasilanie prądem przemiennym z niezależnego źródła

        — Lub —
    - jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS
    - Dostępne gniazdo 1U w stojaku, na którym zamierzasz zainstalować urządzenie

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi sieci dotyczącymi wdrażania Azure Stack EDGE Pro i skonfiguruj sieć centrum danych zgodnie z wymaganiami. Aby uzyskać więcej informacji, zobacz [Azure Stack EDGE Pro wymagania dotyczące sieci](azure-stack-edge-system-requirements.md#networking-port-requirements).

- Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.


## <a name="unpack-the-device"></a>Rozpakowywanie urządzenia

To urządzenie jest dostarczane w jednym pudełku. Aby rozpakować urządzenie, wykonaj następujące czynności. 

1. Umieść pudełko na płaskiej, poziomej powierzchni.
2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.
3. Rozpakuj zawartość pudełka. Po rozpakowaniu upewnij się, że masz:
    - Jedna obudowa Azure Stacką urządzenia z krawędzią Pro
    - dwa przewody zasilania,
    - Jeden zestaw szyn
    - Książeczka bezpieczeństwa, środowiska i informacji prawnych

Jeśli wszystkie elementy wymienione w tym miejscu nie zostały odebrane, skontaktuj się z pomocą techniczną Azure Stack EDGE Pro. Następnym krokiem jest zamontowanie urządzenia na stojaku.


## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie należy zainstalować na standardowym, 19-calowym stojaku. Użyj poniższej procedury, aby zainstalować urządzenie w standardowym stojaku z 19 cala.

> [!IMPORTANT]
> Aby poprawna operacja była zamocowana w stojaku, Azure Stack urządzenia z systemem Edge.


### <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem zapoznaj się z instrukcjami dotyczącymi bezpieczeństwa, środowiska i karnetu informacyjnego. Ta broszura została dostarczona z urządzeniem.
- Zacznij instalować szyny w wyznaczonym miejscu znajdującym się najbliżej dolnej części obudowy stojaka.
- Dla konfiguracji zamontowania szyny narzędziowej:
    -  Musisz dostarczyć osiem wkrętów: #10-32, #12-24, #M5 lub #M6. Średnica głowy ramion musi być mniejsza niż 10 mm (0,4 ").
    -  Potrzebny jest śrubokręt o płaskim wykorzystaniu.

### <a name="identify-the-rail-kit-contents"></a>Zidentyfikuj zawartość zestawu szyn

Znajdź składniki służące do instalowania zestawu szyny:
1. Dwa zestawy szyn firmy Dell ReadyRails II z przesuwaniem
2. Dwa taśmy punktu zaczepienia i pętli

    ![Zidentyfikuj zawartość zestawu szyn](./media/azure-stack-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instaluj i usuwaj szyny bez narzędzi (Stojaki kwadratowe lub okrągłe otwory)

> [!TIP]
> Ta opcja jest mniejsza niż narzędzie, ponieważ nie wymaga narzędzi do instalacji i usuwania szyn do kwadratu niewielowątkowych lub okrągłych otworów w stojakach.

1. Umieść części z lewej i prawej szyny oznaczone jako **przód** na zewnątrz i orientuje każdy element końcowy do siedzenia w dziurach znajdujących się po lewej stronie wierzchołków w pionie.
2. Wyrównaj każdy element końcowy w dolnej i górnej dziurie pożądanej spacji U.
3. Zaangażuj wewnętrzną końcówkę szyny do momentu, w którym w pełni nastąpi montaż w pionie i zamknie. Powtórz te kroki, aby pomieścić i umieścić element frontonu na kołnierzu pionowym.
4. Aby usunąć szyny, należy ściągnąć przycisk Zwolnij do końca częściowego punktu środkowego i odłączać każdą kolejkę.

    ![Zainstaluj i Usuń szyny bez narzędzia](./media/azure-stack-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalowanie i usuwanie szyn z narzędziami (Stojaki z gwintowanym otworem)

> [!TIP]
> Ta opcja jest narzędziem, ponieważ wymaga narzędzia (_płaskiego śrubokrętu_) do zainstalowania i usunięcia szyn do okrągłych otworów w stojakach.

1. Usuń numery PIN z wsporników z przodu i z tyłu przy użyciu płasko wychylonego śrubokrętu.
2. Pobierz i obróć podzestawy zamków szyny, aby usunąć je z nawiasów montażowych.
3. Dołącz do lewej i prawej szynę montażową do czołowych pionowych kołnierzy stojaków przy użyciu dwóch par wkrętów.
4. Przesuń w lewo i w prawo nawiasy klamrowe w przód do tylnej krawędzi pionowej i Dołącz je przy użyciu dwóch par wkrętów.

    ![Zainstaluj i Usuń szyny narzędzia](./media/azure-stack-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Instalowanie systemu w stojaku

1. Ciągnij wewnętrzne slajdy z stojaka do momentu zablokowania ich na miejscu.
2. Znajdź Standoff szyny tylnej z każdej strony systemu i Obniż ją do tylnych gniazd J na zestawach slajdów. Obróć system w dół do momentu, w którym wszystkie standoffs szyny są umiejscowione w gniazdach J.
3. Wypchnij system do wewnątrz do momentu kliknięcia dźwigni blokady.
4. Naciśnij przycisk Zablokuj suwak — zwolnij na szynach i przesuń system do stojaka.

    ![Instaluj system w stojaku](./media/azure-stack-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Usuń system z stojaka

1. Znajdź dźwignie blokady po bokach wewnętrznych szyn.
2. Odblokuj każdą dźwignię, obracając ją w jej pozycji wydania.
3. Opanujesz boki systemu i ściągaj je do przodu, aż do momentu, gdy kolejka standoffs nie znajduje się na wierzchu miejsc J. Unieś system z stojakiem i umieść go na powierzchni poziomej.

    ![Usuń system z stojaka](./media/azure-stack-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Angażowanie i zwolnienie zamka Slam

> [!NOTE]
> W przypadku systemów, które nie są wyposażone w zatrzaśnięcia Slam, Zabezpiecz system za pomocą wkrętów zgodnie z opisem w kroku 3 tej procedury.

1. Po lewej stronie Znajdź zatrzask Slam po obu stronach systemu.
2. Zatrzaśnięcia odbywa się automatycznie, gdy system jest wypychany do stojaka i są uwalniane przez ściąganie do zamków.
3. Aby zabezpieczyć system do celów wysyłki w stojaku lub w przypadku innych niestabilnych środowisk, należy odszukać w każdym zatrzasku i zamontować każdy Wkręt z #2 śrubokręt Phillips.

    ![Angażowanie i wydawanie Slam zamka](./media/azure-stack-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

Roześlij kable, a następnie podłącz urządzenie. W poniższych procedurach wyjaśniono, jak podłączyć urządzenie do usługi Azure Stack Edge w sieci na potrzeby połączeń z usługą Power BI.

Aby można było rozpocząć podłączanie kabli urządzenia, potrzebne są następujące elementy:

- Twoje urządzenie fizyczne w Azure Stack Edge, rozpakowane i stelaże.
- Dwa kable zasilające.
- Co najmniej jeden kabel sieciowy 1-GbE RJ-45 służący do łączenia z interfejsem zarządzania. Istnieją dwa interfejsy sieciowe 1-GbE: jeden do zarządzania i drugi stanowiący interfejs danych w urządzeniu.
- Jeden miedziany kabel 25-GbE SFP+ dla każdego interfejsu sieciowego danych do skonfigurowania. Co najmniej jeden sieciowy interfejs danych — PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 — musi być połączony z Internetem (umożliwiając łączność z platformą Azure).  
- Dostęp do dwóch jednostek dystrybucji zasilania (zalecane).

> [!NOTE]
> - Jeśli łączysz tylko jeden interfejs sieciowy danych, zalecamy użycie interfejsu sieciowego 25/10 GbE, takiego jak PORT 3, PORT 4, PORT 5 lub PORT 6 do wysyłania danych do platformy Azure. 
> - Aby uzyskiwać najlepszą wydajność i obsługiwać duże ilości danych, rozważ połączenie wszystkich portów danych.
> - Urządzenie Azure Stack EDGE Pro powinno być połączone z siecią centrum danych, aby można było pozyskać dane z serwerów źródłowych.

Na urządzeniu Azure Stack EDGE Pro:

- Panel przedni ma stacje dysków i przycisk potęgi.

    - Na początku urządzenia dostępne są 10 miejsc dyskowych.
    - Gniazdo 0 ma dysk SATA 240 GB używany jako dysk systemu operacyjnego. Gniazdo 1 jest puste i gniazda od 2 do 9 są dysków SSD interfejsu NVMe używane jako dyski danych.
- Płaszczyzna tylna obejmuje nadmiarowe jednostki zasilacza (PSUs).
- Płaszczyzna tylna ma sześć interfejsów sieciowych:

    - Interfejsy 2 1 GB/s.
    - Interfejsy 4 25 GB/s, które mogą również działać jako interfejsy 10 GB/s.
    - Kontroler zarządzania płytą główną (BMC).

- Płaszczyzna tylna ma dwie karty sieciowe odpowiadające 6 portom:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Aby zapoznać się z pełną listą obsługiwanych kabli, przełączników i urządzeń nadawczych dla tych kart sieciowych, przejdź do [macierzy zgodności serii Cavium FastlinQ 41000](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Zidentyfikuj różne porty na płaszczyźnie tylnej urządzenia.

    ![Płaszczyzna tylna urządzenia z kablem](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

2. Znajdź miejsca na dysku i przycisk energia na przedniej urządzeniu.

    ![Przedsunięta płaszczyzna urządzenia](./media/azure-stack-edge-deploy-install/device-front-plane-labeled-1.png)

3. Podłącz kable zasilające do poszczególnych zasilaczy w obudowie. Aby zapewnić wysoką dostępność, zainstaluj i podłącz oba zasilacze do różnych źródeł zasilania.
4. Podłącz kable zasilające do jednostek dystrybucji zasilania stojaka. Upewnij się, że dwa zasilacze korzystają z oddzielnych źródeł zasilania.
5. Naciśnij przycisk energia, aby włączyć urządzenie.
6. Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.
7. Co najmniej jeden PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 łączący z Internetem/siecią centrum danych.

    - W przypadku łączenia za pomocą portu PORT 2 użyj kabla sieciowego RJ-45.
    - W przypadku interfejsów sieciowych 10/25 GbE należy użyć kabli SFP + miedzianych.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje na temat Azure Stack krawędzi programu Edge Pro, takich jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem oraz je skonfigurować i aktywować.

> [!div class="nextstepaction"]
> [Łączenie i Konfigurowanie Azure Stack EDGE Pro](./azure-stack-edge-deploy-connect-setup-activate.md)
