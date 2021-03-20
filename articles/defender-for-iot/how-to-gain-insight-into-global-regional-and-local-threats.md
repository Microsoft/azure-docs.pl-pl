---
title: Uzyskiwanie wglądu w globalne, regionalne i lokalne zagrożenia
description: Uzyskaj wgląd w globalne, regionalne i lokalne zagrożenia, używając mapy witryny w lokalnej konsoli zarządzania.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cde55f3c2a875c593c07ea05427f8075d8599196
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97843547"
---
# <a name="gain-insight-into-global-regional-and-local-threats"></a>Uzyskiwanie wglądu w globalne, regionalne i lokalne zagrożenia

Mapa witryny w lokalnej konsoli zarządzania pozwala uzyskać pełny zakres zabezpieczeń, dzieląc sieć na segmenty geograficzne i logiczne odzwierciedlające topologię biznesową:

- **Poziom obiektu geograficznego**: lokacja programu odzwierciedla wiele urządzeń pogrupowanych według lokalizacji geograficznej przedstawionej na mapie. Domyślnie usługa Azure Defender for IoT udostępnia mapę świata. Należy zaktualizować mapę, aby odzwierciedlała strukturę organizacyjną lub biznesową. Można na przykład użyć mapy, która odzwierciedla lokacje w określonym kraju, mieście lub szkoły. Gdy kolor witryny zostanie zmieniony na mapie, zawiera on zespół SOC ze wskazaniem krytycznego stanu systemu w obiekcie.

  Mapa jest interaktywna i umożliwia otwieranie każdej lokacji i poświęcona im informacji.

- **Globalna warstwa logiczna**: jednostka biznesowa jest sposobem dzielenia przedsiębiorstwa na segmenty logiczne według określonych branż. Po wykonaniu tej czynności topologia biznesowa zostanie odzwierciedlona na mapie.

  Na przykład firma globalna, która zawiera fabryki szklane, fabryki plastikowe i fabryki samochodów, może być zarządzana jako trzy różne jednostki biznesowe. Lokacja fizyczna znajdująca się w Toronto obejmuje trzy różne szklane linie produkcyjne, linię produkcyjną plastik i linię produkcyjną silnika ciężarówkowego. Dlatego ta witryna ma przedstawicieli wszystkich trzech jednostek firmy.

- **Poziom regionu geograficznego**: Utwórz regiony w celu podzielenia globalnego przedsiębiorstwa do regionów geograficznych. Na przykład opisana przez nas firma może korzystać z regionów Ameryka Północna, Europa Zachodnia i Europa Wschodnia. Ameryka Północna ma fabryki z wszystkich trzech jednostek biznesowych. Europa Zachodnia ma fabryki i fabryki szklane, a Europa Wschodnia ma tylko fabryki plastikowe.

- **Lokalny poziom segmentu logicznego**: strefa jest segmentem logicznym w lokacji, która definiuje na przykład obszar funkcjonalny lub linię produkcyjną. Praca z strefami umożliwia wymuszanie zasad zabezpieczeń, które są istotne dla definicji strefy. Na przykład lokacja zawierająca pięć linii produkcyjnych może zostać ujęta w pięć stref.

- **Poziom widoku lokalnego**: Widok lokalny pojedynczej instalacji czujnika zapewnia wgląd w stan operacyjny i zabezpieczenia podłączonych urządzeń.

## <a name="work-with-site-map-views"></a>Pracuj z widokami mapy witryny

Lokalna Konsola zarządzania zapewnia ogólny widok sieci przemysłowej w formie mapy związanej z kontekstem. Ogólny widok mapy przedstawia globalną mapę organizacji z lokalizacją geograficzną każdej lokacji.

:::image type="content" source="media/how-to-work-with-maps/enterprise.png" alt-text="Zrzut ekranu przedstawiający widok mapy przedsiębiorstwa.":::

### <a name="color-coded-map-views"></a>Widoki mapy kodowane kolorami

**Zielony**: liczba zdarzeń zabezpieczeń poniżej wartości progowej zdefiniowanej przez usługę Defender for IoT dla Twojego systemu. Nie jest wymagana żadna akcja.

**Żółty**: liczba zdarzeń zabezpieczeń jest równa wartości progowej, która usługa Defender for IoT została zdefiniowana dla Twojego systemu. Rozważ zbadanie zdarzeń.  

**Czerwony**: liczba zdarzeń zabezpieczeń przekracza wartość progową, która usługa Defender for IoT została zdefiniowana dla Twojego systemu. Wykonaj natychmiastową akcję.

### <a name="risk-level-map-views"></a>Widoki mapy na poziomie ryzyka

**Ocena ryzyka**: widok oceny ryzyka wyświetla informacje o ryzyku lokacji. Informacje o ryzyku ułatwiają określanie priorytetów i tworzenie mapy drogowej w celu zaplanowania ulepszeń zabezpieczeń.

**Odpowiedź na zdarzenia**: Uzyskaj scentralizowany widok wszystkich niepotwierdzonych alertów w każdej lokacji w przedsiębiorstwie. Możesz przejść do szczegółów i zarządzać alertami wykrytymi w określonej lokacji.

:::image type="content" source="media/how-to-work-with-maps/incident-responses.png" alt-text="Zrzut ekranu przedstawiający widok mapy przedsiębiorstwa z odpowiedzią na zdarzenie.":::

**Złośliwe działanie**: w przypadku wykrycia złośliwego oprogramowania lokacja jest wyświetlana na czerwono. Oznacza to, że należy podjąć natychmiastowe działanie.

:::image type="content" source="media/how-to-work-with-maps/malicious-activity.png" alt-text="Zrzut ekranu przedstawiający widok mapy przedsiębiorstwa z złośliwym działaniem.":::

**Alerty operacyjne**: ten widok mapy dla systemów niezwiązanych z systemem zapewnia lepszy wgląd w to, które systemy mogą napotkać zdarzenia operacyjne, takie jak PLC zatrzymane, przekazywanie oprogramowania układowego i przekazywanie programów.

:::image type="content" source="media/how-to-work-with-maps/operational-alerts.png" alt-text="Zrzut ekranu przedstawiający widok mapy przedsiębiorstwa z alertami operacyjnymi.":::

Aby wybrać widok mapy:

1. Wybierz pozycję **Widok domyślny** z mapy.
2. Wybierz widok.

:::image type="content" source="media/how-to-work-with-maps/map-views.png" alt-text="Zrzut ekranu przedstawiający widok domyślny mapy witryny.":::

## <a name="update-the-site-map-image"></a>Aktualizowanie obrazu mapy witryny

Usługa Defender for IoT zawiera domyślną mapę świata. Możesz to zmienić, aby odzwierciedlała Twoją organizację: na przykład mapę kraju lub mapę miasta. 

Aby zamienić mapę:

1. W okienku po lewej stronie wybierz pozycję **Ustawienia systemowe**.

2. Wybierz pozycję **Zmień mapę witryny** i Przekaż plik graficzny, aby zastąpić domyślną mapę.

## <a name="next-step"></a>Następny krok

[Wyświetlanie alertów](how-to-view-alerts.md)
