---
title: Informacje o przechowywaniu danych w środowisku — usługa Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano dwa ustawienia kontrolujące przechowywanie danych w środowisku Azure Time Series Insightsu.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 26363031aea37c53cce098e2b6cbc2b4d93b918f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307082"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Informacje o przechowywaniu danych w Azure Time Series Insights Gen1

> [!CAUTION]
> To jest artykuł Gen1.

W tym artykule opisano dwa podstawowe ustawienia, które mają wpływ na przechowywanie danych w środowisku Azure Time Series Insightsu.

## <a name="video"></a>Wideo

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Poniższe wideo podsumowuje Azure Time Series Insights przechowywanie danych i sposób ich planowania.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Każde środowisko Azure Time Series Insights ma ustawienie kontrolujące **czas przechowywania danych**. Wartość obejmuje od 1 do 400 dni. Dane są usuwane w zależności od pojemności magazynu środowiska lub czasu przechowywania, w zależności od tego, co nastąpi wcześniej.

Ponadto środowisko Azure Time Series Insights ma ustawienie **zachowania przekroczenia limitu magazynu** . Steruje zachowaniem ruchu przychodzącego i przeczyszczania, gdy osiągnięto maksymalną pojemność środowiska. Podczas konfigurowania go można wybrać dwa zachowania:

- **Przeczyść stare dane** (ustawienie domyślne)  
- **Wstrzymaj ruch przychodzący**

> [!NOTE]
> Domyślnie podczas tworzenia nowego środowiska przechowywanie jest skonfigurowane do **przeczyszczania starych danych**. To ustawienie może być przełączane w razie konieczności po czasie tworzenia przy użyciu Azure Portal na stronie **Konfigurowanie** środowiska Azure Time Series Insights.
>
> - Informacje o sposobie konfigurowania zasad przechowywania można znaleźć w artykule [Konfigurowanie przechowywania w Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

Zasady przechowywania danych są szczegółowo opisane poniżej.

## <a name="purge-old-data"></a>Przeczyść stare dane

- **Przeczyść stare dane** jest domyślnym ustawieniem dla środowisk Azure Time Series Insightsych.  
- Jeśli użytkownicy chcą zawsze korzystać z *najnowszych danych* w środowisku Azure Time Series Insights, należy **przeczyścić stare dane** .
- Ustawienie **Przeczyść stare dane** umożliwia *przeczyszczenie* danych po osiągnięciu limitów środowiska (czasu przechowywania, rozmiaru lub liczby, zależnie od tego, co nastąpi wcześniej). Wartość przechowywania jest domyślnie ustawiona na 30 dni.
- Najstarsze dane pozyskiwane są czyszczone jako pierwsze (podejście "najpierw w pierwszej kolejności").

### <a name="example-one"></a>Przykład jeden

Rozważmy przykładowe środowisko z zachowaniem przechowywania **kontynuuje transfer danych i Przeczyść stare dane**:

**Czas przechowywania danych** jest ustawiony na 400 dni. **Pojemność** jest ustawiona na jednostkę S1, która zawiera 30 GB całkowitej pojemności. Załóżmy, że dane przychodzące są gromadzone do 500 MB każdego dnia. To środowisko może przechowywać tylko 60 dni danych według stawki danych przychodzących, ponieważ maksymalna pojemność osiągnie 60 dni. Dane przychodzące są gromadzone jako: 500 MB każdego dnia x 60 dni = 30 GB.

W danym dniu 61st środowisko pokazuje najświeższe dane, ale Przeczyszcza najstarsze dane, starsze niż 60 dni. Przeczyszczanie powoduje miejsce na nowe przesyłanie strumieniowe danych, dzięki czemu można nadal eksplorować nowe dane. Jeśli użytkownik chce, aby dane były przechowywane dłużej, można zwiększyć rozmiar środowiska przez dodanie dodatkowych jednostek lub wypchnięcie mniejszej ilości danych.  

### <a name="example-two"></a>Przykład dwa

Należy wziąć pod uwagę również środowisko skonfigurowane zachowanie przechowywania **kontynuuje transfer i przeczyszczanie starych danych**. W tym przykładzie **czas przechowywania danych** jest ustawiony na niższą wartość 180 dni. **Pojemność** jest ustawiona na jednostkę S1, która zawiera 30 GB całkowitej pojemności. Aby przechowywać dane przez pełne 180 dni, dzienny ruch przychodzący nie może przekroczyć 0,166 GB (166 MB) dziennie.  

Za każdym razem, gdy dzienny wskaźnik transferu danych w danym środowisku przekracza 0,166 GB dziennie, dane nie mogą być przechowywane przez 180 dni, ponieważ niektóre dane są usuwane. To samo środowisko należy wziąć pod uwagę podczas zajętego okresu. Załóżmy, że szybkość ruchu przychodzącego środowiska może wzrosnąć do średniej 0,189 GB dziennie. W tym czasie zajęte dane są zachowywane około 158 dni (30 GB/0.189 = 158,73 dni przechowywania). Ta godzina jest mniejsza niż wymagana klatka czasowa przechowywania danych.

## <a name="pause-ingress"></a>Wstrzymaj ruch przychodzący

- Ustawienie **Wstrzymaj** ruch przychodzący jest zaprojektowana w celu zapewnienia, że dane nie zostaną usunięte, jeśli limity rozmiaru i liczby zostaną osiągnięte przed okresem przechowywania.  
- **Wstrzymaj** ruch przychodzący zapewnia użytkownikom dodatkowy czas na zwiększenie wydajności środowiska przed przeczyszczeniem danych z powodu naruszenia okresu przechowywania.
- Pomaga ona w ochronie przed utratą danych, ale może utworzyć okazję do utraty najnowszych danych, jeśli ruch przychodzący zostanie wstrzymany poza okresem przechowywania źródła zdarzenia.
- Jednak po osiągnięciu maksymalnej wydajności środowiska środowisko wstrzymuje ruch przychodzący do momentu wykonania następujących dodatkowych akcji:

  - Maksymalną pojemność środowiska można zwiększyć, aby dodać więcej jednostek skalowania, zgodnie z opisem w temacie [Jak skalować środowisko Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
  - Zostanie osiągnięty okres przechowywania danych, a dane zostaną przeczyszczone, przez zwiększenie środowiska poniżej jego maksymalnej wydajności.

### <a name="example-three"></a>Przykład trzy

Rozważ użycie środowiska z zachowaniem zachowania, które zostało skonfigurowane w celu **wstrzymania** transferu danych przychodzących. W tym przykładzie **okres przechowywania danych** jest skonfigurowany na 60 dni. **Pojemność** jest ustawiona na trzy (3) jednostki warstwy S1. Załóżmy, że to środowisko obejmuje dane z 2 GB danych każdego dnia. W tym środowisku ruch przychodzący jest wstrzymywany po osiągnięciu maksymalnej wydajności.

W tym czasie środowisko pokazuje ten sam zestaw danych do momentu wznowienia transferu lub **do momentu** włączenia operacji transferu danych przychodzących (dzięki czemu można przeczyścić starsze dane, aby zwolnić miejsce na nowe dane).

Po wznowieniu transferu danych przychodzących:

- Przepływy danych w kolejności, w jakiej zostały odebrane przez Źródło zdarzenia
- Zdarzenia są indeksowane w oparciu o sygnaturę czasową, chyba że w źródle zdarzeń zostały przekroczone zasady przechowywania. Aby uzyskać więcej informacji na temat konfiguracji przechowywania źródła zdarzeń, [Event Hubs często zadawane pytania](../event-hubs/event-hubs-faq.yml)

> [!IMPORTANT]
> Należy ustawić alerty, aby powiadomić o uniknięciu wstrzymania transferu danych przychodzących. Utrata danych jest możliwa od momentu domyślnego przechowywania dla źródeł zdarzeń platformy Azure. W związku z tym po wstrzymaniu transferu danych przychodzących prawdopodobnie utracisz najnowsze dane, chyba że zostanie podjęta dodatkowa akcja. Należy zwiększyć pojemność lub przełączać zachowanie w celu **przeczyszczenia starych danych** , aby uniknąć utraty danych.

W Event Hubs, których dotyczy problem, Rozważ zmianę właściwości **przechowywania wiadomości** , aby zminimalizować utratę danych podczas wstrzymania ruchu przychodzącego w Azure Time Series Insights.

[![Przechowywanie komunikatu centrum zdarzeń.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Jeśli nie skonfigurowano żadnych właściwości dla źródła zdarzeń ( `timeStampPropertyName` ), Azure Time Series Insights domyślną sygnatura czasowa przybycia w centrum zdarzeń jako oś X. Jeśli `timeStampPropertyName` jest skonfigurowany jako inny, środowisko szuka skonfigurowanej `timeStampPropertyName` w pakiecie danych, gdy zdarzenia są analizowane.

Zapoznaj się z artykułem [Jak skalować środowisko Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md) , aby skalować środowisko w celu zwiększenia pojemności lub zwiększyć czas przechowywania.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat konfigurowania lub zmieniania ustawień przechowywania danych, przejrzyj [Konfigurowanie przechowywania w Azure Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Dowiedz się więcej o [ograniczeniu opóźnienia w Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
