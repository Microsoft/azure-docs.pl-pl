---
title: Pracuj z mapami pomieszczeń w Azure Maps Creator
description: W tym artykule przedstawiono pojęcia dotyczące Azure Maps twórców usług
author: anastasia-ms
ms.author: v-stharr
ms.date: 05/18/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ad1b7ae08e74f455190c44a813dde44b0b683014
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311363"
---
# <a name="creator-for-indoor-maps"></a>Kreator dla map pomieszczeń

W tym artykule przedstawiono pojęcia i narzędzia, które mają zastosowanie do Azure Maps Creator. Zalecamy zapoznanie się z tym artykułem przed rozpoczęciem korzystania z interfejsu API kreatora Azure Maps i zestawu SDK.

Za pomocą kreatora można opracowywać aplikacje z funkcjami mapy opartymi na danych mapy pomieszczeń. W tym artykule opisano proces przekazywania, konwertowania, tworzenia i używania danych mapy. Cały przepływ pracy przedstawiono na poniższym diagramie.

![Przepływ pracy danych mapy autora](./media/creator-indoor-maps/workflow.png)

## <a name="create-azure-maps-creator"></a>Utwórz twórcę Azure Maps

Aby korzystać z usług Creator Services, należy utworzyć Azure Maps twórcę na koncie Azure Maps. Aby uzyskać informacje na temat sposobu tworzenia twórcy Azure Maps w Azure Maps, zobacz [Manage Azure Maps Creator](how-to-manage-creator.md).

## <a name="upload-a-drawing-package"></a>Przekaż pakiet rysowania

Twórca zbiera dane mapy danych wewnętrznych poprzez konwersję przekazanego pakietu rysowania. Pakiet rysowania reprezentuje zabudowaną lub przemodelowaną funkcję. Aby uzyskać informacje o wymaganiach dotyczących pakietów rysowania, zobacz [rysowanie wymagań pakietu](drawing-requirements.md).

Przekaż pakiet rysowania przy użyciu [interfejsu API przekazywania danych Azure Maps](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) .  Po pomyślnym przekazaniu interfejs API przekazywania danych zwróci identyfikator danych użytkownika ( `udid` ). `udid`Zostanie użyta w następnym kroku w celu przekonwertowania przekazanego pakietu na dane mapy pomieszczeń.

## <a name="convert-a-drawing-package"></a>Konwertowanie pakietu rysowania

[Usługa konwersji Azure Maps](https://docs.microsoft.com/rest/api/maps/conversion) konwertuje przekazany pakiet rysowania na dane mapy pomieszczeń. Usługa konwersji również sprawdza poprawność pakietu. Problemy ze sprawdzaniem poprawności są klasyfikowane do dwóch typów: Błędy i ostrzeżenia. W przypadku wykrycia błędów proces konwersji kończy się niepowodzeniem. W przypadku wykrycia ostrzeżeń konwersja zostanie zakończona pomyślnie. Zaleca się jednak przejrzenie i rozwiązanie wszystkich ostrzeżeń. Ostrzeżenie oznacza, że część konwersji została zignorowana lub automatycznie naprawiona. Niepowodzenie rozpoznania ostrzeżeń może spowodować błędy w ostatnim procesie. Aby uzyskać więcej informacji, zobacz [rysowanie ostrzeżeń i błędów pakietu](drawing-conversion-error-codes.md).

Gdy wystąpi błąd, usługa konwersji oferuje link do [wizualizacji błędów rysowania Azure Maps](drawing-error-visualizer.md) autonomicznej aplikacji sieci Web. Możesz użyć wizualizatora błędów rysowania, aby sprawdzić [ostrzeżenia i błędy pakietu rysowania](drawing-conversion-error-codes.md) , które wystąpiły podczas procesu konwersji. Po usunięciu błędów można spróbować przekazać i skonwertować pakiet.

## <a name="create-indoor-map-data"></a>Tworzenie danych mapy pomieszczeń

Kreator Azure Maps udostępnia trzy usługi:

* [Usługa DataSet](https://docs.microsoft.com/rest/api/maps/dataset/createpreview).
Użyj usługi DataSet, aby utworzyć zestaw danych na podstawie przekonwertowanych danych pakietu rysunku.
* [Usługa tileset](https://docs.microsoft.com/rest/api/maps/tileset/createpreview).
Użyj usługi tileset, aby utworzyć reprezentację zestawu danych opartego na wektorach. Aplikacje mogą używać tileset, aby przedstawić wizualny widok zestawu danych oparty na kafelkach.
* [Usługa stanu funkcji](https://docs.microsoft.com/rest/api/maps/featurestate). Użyj usługi stanu funkcji do obsługi stylów mapy dynamicznej. Funkcja stylów mapy dynamicznej umożliwia aplikacjom odzwierciedlenie zdarzeń w czasie rzeczywistym na miejscach dostarczonych przez system IoT.

### <a name="datasets"></a>Zestawy danych

Zestaw danych to kolekcja funkcji mapy pomieszczeń. Funkcja mapy pomieszczeń reprezentuje obiekty zdefiniowane w przekonwertowanego pakietu rysowania. Po utworzeniu zestawu danych za pomocą [usługi DataSet](https://docs.microsoft.com/rest/api/maps/dataset/createpreview)można utworzyć dowolną liczbę [tilesets](#tilesets) lub [funkcji statesets](#feature-statesets).

[Usługa DataSet](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) umożliwia deweloperom w dowolnym momencie Dodawanie lub usuwanie obiektów do istniejącego zestawu danych. Aby uzyskać więcej informacji na temat aktualizowania istniejącego zestawu danych przy użyciu interfejsu API, zobacz opcje dołączania w [zestawie danych](https://docs.microsoft.com/rest/api/maps/dataset/createpreview). Aby zapoznać się z przykładem sposobu aktualizowania zestawu danych, zobacz [konserwacja danych](#data-maintenance).

### <a name="tilesets"></a>Tilesets

Tileset to kolekcja danych wektorowych, która reprezentuje zestaw jednolitych kafelków siatki. Deweloperzy mogą używać [usługi tileset](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) do tworzenia tilesets z zestawu danych.

Aby odzwierciedlić różne etapy zawartości, można utworzyć wiele tilesets z tego samego zestawu danych. Na przykład można utworzyć jeden tileset z umeblowaniem i sprzętem oraz inne tileset bez mebli i sprzętu.  Możesz zdecydować się na wygenerowanie jednego tileset z najnowszymi aktualizacjami danych i jednego bez najnowszych aktualizacji danych.

Oprócz danych wektorowych tileset zawiera metadane dla optymalizacji renderowania mapy. Na przykład metadane tileset zawierają minimalny i maksymalny poziom powiększenia dla tileset. Metadane zawierają również pole ograniczenia definiujące zakres geograficzny tileset. Pole ograniczenia pozwala aplikacji na programowo ustawić prawidłowy punkt środkowy. Aby uzyskać więcej informacji na temat metadanych tileset, zobacz [tileset list API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview).

Po utworzeniu tileset można go pobrać za pomocą [usługi renderowania w wersji 2](#render-v2-service).

Jeśli tileset stanie się nieaktualna i nie jest już przydatna, można usunąć tileset. Aby uzyskać więcej informacji na temat usuwania tilesets, zobacz temat  [konserwacja danych](#data-maintenance).

>[!NOTE]
>Element tileset jest niezależny od zestawu danych, z którego został utworzony. Jeśli utworzysz tilesets z zestawu danych, a następnie zaktualizujesz ten zestaw danych, tilesets nie zostanie zaktualizowany. Aby odzwierciedlić zmiany w zestawie danych, należy utworzyć nowy tilesets. Podobnie po usunięciu elementu tileset nie ma to żadnego oddziaływania na zestaw danych.

### <a name="feature-statesets"></a>Statesets funkcji

Funkcja statesets to kolekcje właściwości dynamicznych (*Stanów*) przypisanych do funkcji zestawu danych, takich jak pokoje lub sprzęt. Przykładem *stanu* może być temperatura lub miejsce zajętości. Każdy *stan* jest parą klucz/wartość zawierającą nazwę właściwości, wartość oraz sygnaturę czasową ostatniej aktualizacji.

[Usługa stanu funkcji](https://docs.microsoft.com/rest/api/maps/featurestate/createstatesetpreview) umożliwia tworzenie i zarządzanie funkcją stateset dla zestawu danych. Stateset jest zdefiniowany przez co najmniej jeden *stan*. Każda funkcja, taka jak pokój, może mieć przypisany jeden *stan* .

Wartość każdego *stanu* w stateset może być aktualizowana lub pobierana przez urządzenia IoT lub inne aplikacje.  Na przykład przy użyciu [interfejsu API aktualizacji stanu funkcji](https://docs.microsoft.com/rest/api/maps/featurestate/updatestatespreview)urządzenia mierzące miejsce zajęte może systematycznie publikować zmiany stanu pokoju.

Aplikacja może używać funkcji stateset do dynamicznego renderowania funkcji w obiekcie zgodnie z ich bieżącym stanem i odpowiednim stylem mapy. Aby uzyskać więcej informacji na temat używania funkcji statesets na potrzeby stylu funkcji na mapie renderowania, zobacz [moduł zestawu SDK sieci Web](#indoor-maps-module).

>[!NOTE]
>Podobnie jak tilesets, zmiana zestawu danych nie wpływa na istniejącą funkcję stateset i usunięcie funkcji stateset nie będzie miało wpływu na zestaw danych, do którego jest dołączony.

## <a name="using-indoor-maps"></a>Używanie map pomieszczeń

### <a name="render-v2-service"></a>Usługa renderowania w wersji 2

Usługa Azure Maps [renderowania w wersji 2 — Interfejs API tworzenia kafelków mapy](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) został rozszerzony do obsługi tilesets kreatora.

[Usługa renderowania w wersji 2 — Interfejs API kafelków stanu mapy](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) umożliwia aplikacjom żądanie tilesets. Tilesets można następnie zintegrować z kontrolką mapy lub zestawem SDK. Przykład kontrolki mapy korzystającej z usługi renderowania w wersji 2 znajduje się w sekcji [Maps](#indoor-maps-module).

### <a name="web-feature-service-api"></a>Interfejs API usługi funkcji sieci Web

Do zestawów danych można wykonywać zapytania przy użyciu [interfejsu API usługi funkcji sieci Web (WFS)](https://docs.microsoft.com/rest/api/maps/wfs). WFS jest zgodna z [funkcjami interfejsu API Open Geospatial Consortium](http://docs.opengeospatial.org/DRAFTS/17-069r1.html). Interfejs API WFS umożliwia wykonywanie zapytań dotyczących funkcji w ramach samego zestawu danych. Na przykład można użyć WFS, aby znaleźć wszystkie pokoje spotkań na średnim poziomie danego obiektu i piętra.

### <a name="indoor-maps-module"></a>Moduł planów wnętrz

[Zestaw SDK sieci Web Azure Maps](https://docs.microsoft.com/azure/azure-maps/) zawiera moduł Maps. Ten moduł oferuje rozszerzone funkcje biblioteki *kontrolka mapy* Azure Maps. Moduł mapy wewnętrzne renderuje mapy wewnętrzne utworzone w ramach twórcy. Integruje widżety, takie jak *Selektor piętr*, który pomaga użytkownikom wizualizować różne piętra.

Moduł mapy wewnętrzne umożliwia tworzenie aplikacji sieci Web, które integrują dane map wewnętrznych z innymi [usługami Azure Maps](https://docs.microsoft.com/azure/azure-maps/). Najczęściej używane konfiguracje aplikacji mogą obejmować dodanie wiedzy do map pomieszczeń z innych map, takich jak Road, rzeczy, Pogoda i tranzyt.

Moduł mapy wewnętrzne obsługuje również style mapy dynamicznej. Aby zapoznać się z przewodnikiem krok po kroku, jak zaimplementować dynamiczne style stateset funkcji w aplikacji, zobacz [jak używać modułu mapy pomieszczeń](how-to-use-indoor-module.md)

### <a name="azure-maps-integration"></a>Integracja Azure Maps

Po rozpoczęciu opracowywania rozwiązań dla map pomieszczeń można odkrywać sposoby integrowania istniejących możliwości Azure Maps. Na przykład można zaimplementować scenariusze śledzenia zasobów lub bezpieczeństwa przy użyciu [interfejsu API Azure Maps geoogrodzenia](https://docs.microsoft.com/rest/api/maps/spatial/postgeofence) z mapami pomieszczeń Creator. Za pomocą interfejsu API geoogrodzenia można określić, na przykład, czy proces roboczy wprowadza lub opuszcza określone obszary wewnętrzne. Aby uzyskać więcej informacji na temat sposobu łączenia Azure Maps ze telemetrią IoT, zobacz [tutaj](tutorial-iot-hub-maps.md).

### <a name="data-maintenance"></a>Obsługa danych

 Kreator Azure Maps lista, aktualizacja i usuwanie interfejsu API umożliwiają wyświetlanie, aktualizowanie i usuwanie zestawów danych, tilesets i funkcji statesets.

>[!NOTE]
>Za każdym razem, gdy przeglądasz listę elementów i zdecydujesz się je usunąć, należy wziąć pod uwagę wpływ tego usunięcia na wszystkie zależne interfejsy API lub aplikacje. Na przykład jeśli należy usunąć element tileset, który jest aktualnie używany przez aplikację za pomocą [interfejsu API renderowania w wersji 2-get](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview), usunięcie tego tileset spowoduje niepowodzenie aplikacji.

### <a name="example-updating-a-dataset"></a>Przykład: Aktualizowanie zestawu danych

W poniższym przykładzie przedstawiono sposób aktualizowania zestawu danych, tworzenia nowego tileset i usuwania starego tileset.

1. Wykonaj kroki opisane w sekcji [przekazywanie pakietu rysowania](#upload-a-drawing-package) i [konwertowanie pakietu rysowania](#convert-a-drawing-package) , aby przekazać i skonwertować nowy pakiet rysowania.

2. Użyj [interfejsu API tworzenia zestawu danych](https://docs.microsoft.com/rest/api/maps/dataset/createpreview) , aby dołączyć skonwertowane dane do istniejącego zestawu danych.

3. Użyj [interfejsu API tworzenia tileset](https://docs.microsoft.com/rest/api/maps/tileset/createpreview) , aby wygenerować nowy tileset z zaktualizowanego zestawu danych. Zapisz nowy tilesetId dla kroku 4.

4. Zaktualizuj identyfikator tileset w aplikacji, aby umożliwić wizualizację zaktualizowanego zestawu danych. Jeśli stary tileset nie jest już używany, można go usunąć.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Tworzenie mapy pomieszczeń dla twórców](tutorial-creator-indoor-maps.md)
