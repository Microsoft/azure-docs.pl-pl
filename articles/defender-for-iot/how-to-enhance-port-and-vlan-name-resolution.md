---
title: Ulepszanie rozpoznawania nazw sieci wirtualnych i portów
description: Dostosuj nazwy portów i sieci VLAN na czujników, aby wzbogacać rozdzielczość urządzeń.
ms.date: 12/13/2020
ms.topic: how-to
ms.openlocfilehash: de6fbe70d5a5359ad4e4c276642b9b9ed0cef00f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784173"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>Zwiększenie rozdzielczości portów, sieci VLAN i systemu operacyjnego

Możesz dostosować nazwy portów i sieci VLAN na czujników, aby wzbogacać rozdzielczość urządzeń.

## <a name="customize-port-names"></a>Dostosowywanie nazw portów

Usługa Azure Defender dla IoT automatycznie przypisuje nazwy do najbardziej uniwersalnych portów zarezerwowanych, takich jak DHCP lub HTTP. Można dostosować nazwy portów dla innych portów wykrywanych przez usługę Defender for IoT. Na przykład Przypisz nazwę do niezastrzeżonego portu, ponieważ ten port pokazuje nietypową aktywność.

Te nazwy są wyświetlane, gdy:

  - Wybierasz **grupy urządzeń** z mapy urządzeń.

  - Tworzysz widżety, które udostępniają informacje o porcie.

### <a name="view-custom-port-names-in-the-device-map"></a>Wyświetlanie niestandardowych nazw portów na mapie urządzeń

Porty zawierające nazwę zdefiniowaną przez użytkowników są wyświetlane na mapie urządzeń w grupie **znane aplikacje** .

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="Zrzut ekranu przedstawiający mapę urządzeń pokazującą grupę znanych aplikacji.":::

### <a name="view-custom-port-names-in-widgets"></a>Wyświetlanie niestandardowych nazw portów w widżetach

Zdefiniowane nazwy portów są wyświetlane w elementach widget, które obejmują ruch przez port.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="Ruch sieciowy.":::

Aby zdefiniować niestandardowe nazwy portów:

1. Wybierz pozycję **Ustawienia systemowe** , a następnie wybierz pozycję **standardowe aliasy**.

2. Wybierz pozycję **Dodaj alias portu**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="Dodaj alias portu.":::

3. Wprowadź numer portu, wybierz pozycję **TCP/UDP** lub wybierz opcję **oba** i Dodaj nazwę.

4. Wybierz pozycję **Zapisz**.

## <a name="configure-vlan-names"></a>Konfigurowanie nazw sieci VLAN

Można wzbogacać dane spisu urządzeń z numerami i tagami sieci VLAN urządzenia. Oprócz wzbogacania danych można wyświetlić liczbę urządzeń w sieci VLAN i wyświetlić przepustowość według widżetów sieci VLAN.

Obsługa sieci VLAN jest oparta na 802.1 q (do sieci VLAN o IDENTYFIKATORze 4094).

Dostępne są dwie metody pobierania informacji o sieci VLAN:

- **Automatycznie odnalezione**: domyślnie czujnik automatycznie ODNAJDUJE sieci VLAN. Sieci VLAN wykryte z ruchem są wyświetlane na ekranie konfiguracji sieci VLAN, w raportach wyszukiwania danych i w innych raportach zawierających informacje o sieci VLAN. Nieużywane sieci VLAN nie są wyświetlane. Nie można edytować ani usuwać tych sieci VLAN. 

  Należy dodać unikatową nazwę do każdej sieci VLAN. Jeśli nie dodasz nazwy, numer sieci VLAN zostanie wyświetlony we wszystkich lokalizacjach, w których raportowana jest sieć VLAN.

- **Dodane ręcznie**: można ręcznie dodać sieci VLAN. Należy dodać unikatową nazwę dla każdej sieci VLAN, która została dodana ręcznie, a także edytować lub usunąć te sieci VLAN.

Nazwy sieci VLAN mogą zawierać maksymalnie 50 znaków ASCII.

> [!NOTE]
> Nazwy sieci VLAN nie są zsynchronizowane między czujnikiem i konsolą zarządzania. Musisz również zdefiniować nazwę w konsoli zarządzania.  
W przypadku przełączników Cisco Dodaj następujący wiersz do konfiguracji zakresu: `monitor session 1 destination interface XX/XX encapsulation dot1q` . W tym poleceniu *XX/XX* to nazwa i numer portu.

Aby skonfigurować nazwy sieci VLAN:

1. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

2. W oknie **Ustawienia systemu** wybierz pozycję **VLAN**.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="Aby edytować sieci VLAN, Użyj ustawień systemowych.":::

3. Dodaj unikatową nazwę obok każdego identyfikatora sieci VLAN.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>Ulepszanie klasyfikacji systemu operacyjnego urządzeń: rozszerzanie danych

Czujniki ciągle wykrywają nowe urządzenia, a także zmiany w wcześniej odnalezionych urządzeniach, w tym typy systemów operacyjnych.

W pewnych okolicznościach w odnalezionych systemach operacyjnych mogą zostać wykryte konflikty. Może się tak zdarzyć na przykład w przypadku systemów operacyjnych, które odnoszą się do komputerów stacjonarnych lub serwerów. W takim przypadku otrzymasz powiadomienie z opcjonalnymi klasyfikacjami systemów operacyjnych.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Zwiększ dane.":::

Zbadaj zalecenia w celu wzbogacania klasyfikacji systemu operacyjnego. Ta klasyfikacja pojawia się w spisie urządzeń, raportach wyszukiwania danych i innych ekranach. Zapewnienie aktualności tych informacji może poprawić dokładność alertów, zagrożeń i raportów analizy ryzyka.

Aby uzyskać dostęp do zaleceń dotyczących systemu operacyjnego:

1. Wybierz pozycję **Ustawienia systemowe**.
1. Wybierz pozycję **rozszerzenie danych**.

## <a name="next-steps"></a>Następne kroki

Wyświetlaj wzbogacone informacje o urządzeniach w różnych raportach:

- [Badanie wykryć czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [Trendy czujnika i raporty statystyczne](how-to-create-trends-and-statistics-reports.md)
- [Zapytania wyszukiwania danych czujników](how-to-create-data-mining-queries.md)
