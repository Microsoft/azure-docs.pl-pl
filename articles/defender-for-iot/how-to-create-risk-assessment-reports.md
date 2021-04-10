---
title: Tworzenie raportów oceny ryzyka
description: Uzyskaj wgląd w zagrożenia sieci wykrywane przez poszczególne czujniki lub Zagregowany widok zagrożeń wykrywanych przez wszystkie czujniki.
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 853157ef1b97fefdd15785b2a71c7ccc5d06a9a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784258"
---
# <a name="risk-assessment-reporting"></a>Raportowanie oceny ryzyka

## <a name="about-risk-assessment-reports"></a>Raporty oceny ryzyka — informacje

Raporty oceny ryzyka zapewniają:

- Ogólny wynik zabezpieczeń dla urządzeń wykrywanych przez czujniki organizacyjne.

- Ocena zabezpieczeń dla każdego urządzenia sieciowego wykryta przez pojedyncze czujnika.

- Podział liczby zagrożonych urządzeń, urządzeń, które wymagają ulepszeń i bezpiecznych urządzeń.

-  Wgląd w problemy związane z bezpieczeństwem i działaniem:

    - Problemy z konfiguracją

    - Usterka urządzenia z priorytetem według poziomu zabezpieczeń

    - Problemy z zabezpieczeniami sieci

    - Problemy operacyjne sieci

    - Połączenia z sieciami ICS

    - Połączenia internetowe

    - Wskaźniki złośliwego oprogramowania

    - Problemy dotyczące protokołu

    - Wektory ataków

### <a name="risk-mitigation"></a>Łagodzenie ryzyka

Raporty zawierają zalecenia ułatwiające ulepszanie oceny zabezpieczeń. Na przykład zainstaluj najnowsze aktualizacje zabezpieczeń, Uaktualnij oprogramowanie układowe do najnowszej wersji lub postępuj zgodnie z alertami.

## <a name="about-security-scores"></a>Informacje o ocenie zabezpieczeń

W każdym raporcie jest generowany ogólny wynik zabezpieczeń sieci. Wynik przedstawia wartość procentową zabezpieczeń 100%. Na przykład wynik 30% wskazuje, że sieć jest zabezpieczona 30%.

Wyniki oceny ryzyka są oparte na informacjach uzyskiwanych z inspekcji pakietów, aparatów modelowania zachowań i projektu maszynowego z określonym stanem SCADA.

**Zabezpieczenia urządzeń** są urządzeniami z wynikiem zabezpieczeń wyższym niż 90%.

**Urządzenia wymagające poprawy jakości** obsługi: urządzenia z wynikiem zabezpieczeń między 70% a 89%.

**Zagrożone urządzenia** to urządzenia z wynikiem zabezpieczeń niższym niż 70%.

## <a name="create-risk-assessment-reports"></a>Tworzenie raportów oceny ryzyka

Utwórz raport oceny ryzyka dla PDF. Nazwa raportu jest generowana automatycznie jako risk-assessment-report-1.pdf. Liczba jest aktualizowana dla każdego nowego tworzonego raportu.  Zostanie wyświetlona godzina i dzień tworzenia.

### <a name="create-a-sensor-risk-assessment-report"></a>Utwórz raport oceny ryzyka czujnika

Utwórz raport oceny ryzyka na podstawie wykrytych wykryć czujnika.

Aby utworzyć raport:

1. Zaloguj się do konsoli czujnika.
1. Wybierz pozycję **Ocena ryzyka** w menu po stronie.
1. Wybierz pozycję **Generuj raport**. Raport zostanie wyświetlony w sekcji zarchiwizowane raporty.
1. Wybierz Raport z sekcji zarchiwizowane raporty, aby go pobrać.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Widok oceny ryzyka.":::

Aby zaimportować logo firmy:

- Wybierz pozycję **Importuj logo**.

### <a name="create-an-on-premises-management-console-risk-assessment-report"></a>Tworzenie raportu oceny ryzyka w lokalnej konsoli zarządzania

Utwórz raport oceny ryzyka na podstawie wykrytych czujników przez dowolne czujniki zarządzane przez lokalną konsolę zarządzania. 

Aby utworzyć raport:

1. Wybierz pozycję **Ocena ryzyka** w menu po stronie.

2. Wybierz czujnik z listy rozwijanej **Wybieranie czujnika** .

3. Wybierz pozycję **Generuj raport**.

4. Wybierz pozycję **Pobierz** w sekcji **zarchiwizowane raporty** .

Aby zaimportować logo firmy:

- Wybierz pozycję **Importuj logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Zaimportuj logo za pomocą widoku oceny ryzyka.":::

## <a name="see-also"></a>Zobacz też

[Raportowanie wektorów ataków](how-to-create-attack-vector-reports.md)

