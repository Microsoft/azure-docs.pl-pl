---
title: Zarządzanie zdarzeniami alertów
description: Zarządzaj zdarzeniami alertów wykrytymi w sieci.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/07/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: 866ff4cad74b7092dda11a20f8f7bcadf91233ae
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526905"
---
# <a name="manage-alert-events"></a>Zarządzanie zdarzeniami alertów

Dostępne są następujące opcje zarządzania zdarzeniami alertów:

 | Akcja | Opis |
 |--|--|
 | **Learn** | Autoryzuj wykryte zdarzenie. Aby uzyskać więcej informacji, zobacz [Informacje o nauce i rozuczeniu zdarzeń](#about-learning-and-unlearning-events). |
 | **Potwierdzenie** | Ukryj alert raz dla wykrytego zdarzenia. Alert zostanie wyzwolony ponownie, jeśli zdarzenie zostanie wykryte ponownie. Aby uzyskać więcej informacji, zobacz [Informacje o potwierdzaniu i niepotwierdzaniu zdarzeń](#about-acknowledging-and-unacknowledging-events). |
 | **Głos** | Ciągle Ignoruj działania z identycznymi urządzeniami i porównywalnym ruchem. Aby uzyskać więcej informacji, zobacz [Informacje o zdarzeniach wyciszania i wyciszania](#about-muting-and-unmuting-events). |
 
Możesz również eksportować informacje o alertach.
## <a name="about-learning-and-unlearning-events"></a>Informacje o nauce i rozuczeniu zdarzeń

Zdarzenia wskazujące odchylenia sieci poznaniej mogą odzwierciedlać prawidłowe zmiany w sieci. Przykładami mogą być nowe autoryzowane urządzenia dołączone do sieci lub autoryzowanej aktualizacji oprogramowania układowego.

Po wybraniu opcji **nauka** czujnik rozważa ruch, konfiguracje lub prawidłowe działanie. Oznacza to, że alerty nie będą już wyzwalane dla zdarzenia. Oznacza to również, że zdarzenie nie zostanie obliczone, gdy czujnik generuje ocenę ryzyka, wektor ataków i inne raporty.

Na przykład zostanie wyświetlony alert informujący o aktywności skanowania na urządzeniu, które nie było wcześniej zdefiniowane przez skaner sieciowy. Jeśli to urządzenie zostało dodane do sieci w celu przeprowadzenia skanowania, można nakazać czujnikowi nauczenie urządzenia jako urządzenia skanującego.

:::image type="content" source="media/how-to-work-with-alerts-sensor/detected.png" alt-text="Wykryto okno skanowania dla adresu.":::

Zapoznaj się z niektórymi zdarzeniami. Gdy czujnik zawiera informacje o zdarzeniach, spowoduje to ponowne wyzwolenie alertów związanych z tym zdarzeniem.

## <a name="about-acknowledging-and-unacknowledging-events"></a>Informacje o potwierdzaniu i niepotwierdzaniu zdarzeń

W niektórych sytuacjach może się zdarzyć, że czujnik nie pouczy się o wykrytym zdarzeniu lub opcja może być niedostępna. Zamiast tego zdarzenie może wymagać ograniczenia. Na przykład:

- **Eliminowanie konfiguracji sieci lub urządzenia**: otrzymasz alert wskazujący, że w sieci wykryto nowe urządzenie. Podczas badania, można stwierdzić, że urządzenie jest nieautoryzowanym urządzeniem sieciowym. Zdarzenie jest obsługiwane przez odłączenie urządzenia od sieci.
- **Aktualizacja konfiguracji czujnika**: zostanie wyświetlony alert informujący o tym, że serwer zainicjował zbyt wiele połączeń zdalnych. Ten alert został wyzwolony z powodu zdefiniowania progów anomalii czujnika w celu wyzwalania alertów przekraczających określoną liczbę sesji w ciągu jednej minuty. Zdarzenie jest obsługiwane przez zaktualizowanie progów.

Po przeprowadzeniu środków zaradczych lub badań można wydać czujnikowi możliwość ukrycia alertu, wybierając pozycję **Potwierdź**. Jeśli zdarzenie zostanie wykryte ponownie, alert zostanie wyzwolony.

Aby wyczyścić Alert:

  - Wybierz pozycję **Potwierdź**.

Aby ponownie wyświetlić Alert:

  - Uzyskaj dostęp do alertu i wybierz pozycję Anuluj **potwierdzenie**.

Nie potwierdzaj alertów, jeśli wymagane jest dalsze badanie.

## <a name="about-muting-and-unmuting-events"></a>Informacje o wyciszeniu i odciszeniu zdarzeń

W pewnych okolicznościach może być konieczne poinstruować czujnika, aby ignorował określony scenariusz w sieci. Na przykład:

  - Aparat **anomalii** wyzwala alert dotyczący wzrostu przepustowości między dwoma urządzeniami, ale skok jest prawidłowy dla tych urządzeń.

  - Aparat **naruszenia protokołu** wyzwala alert dotyczący odchylenia protokołu wykrytego między dwoma urządzeniami, ale odchylenia są prawidłowe między urządzeniami.

W takich sytuacjach uczenie nie jest dostępne. Gdy nie można przeprowadzić uczenia się i chcesz pominąć alert i usunąć urządzenie przy obliczaniu ryzyka i wektorów ataków, możesz zamiast tego wyciszyć wydarzenie alertu.

> [!NOTE] 
> Nie można wyciszać zdarzeń, w których urządzenie internetowe jest zdefiniowane jako źródło lub miejsce docelowe.

### <a name="what-alert-activity-is-muted"></a>Jakie działania alertu są wyciszone?

Wyciszony scenariusz obejmuje urządzenia sieciowe i ruch wykryty dla zdarzenia. Tytuł alertu opisuje ruch, który jest wyciszony.

Urządzenie lub urządzenia, które są wyciszone, będą wyświetlane jako obraz w alercie. Jeśli są wyświetlane dwa urządzenia, nastąpi wyciszenie określonego ruchu z alertami między nimi.

**Przykład 1**

Gdy zdarzenie jest wyciszone, jest ignorowane, gdy podstawowy (źródłowy) wysyła pomocniczy (miejsce docelowe) niedozwolony kod funkcji zdefiniowany przez dostawcę.

:::image type="content" source="media/how-to-work-with-alerts-sensor/secondary-device-connected.png" alt-text="Odebrano urządzenie pomocnicze.":::

**Przykład 2**

Gdy zdarzenie jest wyciszone, jest ignorowane, gdy źródło wysyła nagłówek HTTP z niedozwoloną zawartością, niezależnie od miejsca docelowego.

:::image type="content" source="media/how-to-work-with-alerts-sensor/illegal-http-header-content.png" alt-text="Zrzut ekranu przedstawiający niedozwoloną zawartość nagłówka HTTP.":::

**Gdy zdarzenie jest wyciszone:**

- Alert będzie dostępny w widoku **potwierdzonych** alertów, dopóki nie zostanie wywyciszony.

- Akcja Wycisz zostanie wyświetlona na **osi czasu zdarzeń**.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/muted-event-notification-screenshot.png" alt-text="Wykryto i wyciszono zdarzenie.":::

- Czujnik będzie ponownie obliczać urządzenia podczas generowania oceny ryzyka, wektora ataków i innych raportów. Na przykład w przypadku wyciszenia alertu, który wykrył złośliwy ruch na urządzeniu, urządzenie nie zostanie obliczone w raporcie oceny ryzyka.

**Aby wyciszyć i wyłączyć wyciszenie alertu:**

- Wybierz ikonę **Wycisz** w alercie.

**Aby wyświetlić wyciszone alerty:**

1. Wybierz opcję **potwierdzenie** na ekranie głównym **alertów** .

2. Umieść kursor nad alertem, aby sprawdzić, czy jest wyciszony.  

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
1. Wybierz pozycję Eksportuj Rozszerzone alerty, aby wyeksportować informacje o alercie w osobnych wierszach dla każdego alertu obejmującego wiele urządzeń. Po wybraniu pozycji Eksportuj alerty rozszerzone zostanie utworzony zduplikowany wiersz zdarzenia alertu z unikatowymi elementami w każdym wierszu. Użycie tej opcji ułatwia badanie wyeksportowanych zdarzeń alertów.

## <a name="see-also"></a>Zobacz też

[Sterowanie monitorowanym ruchem](how-to-control-what-traffic-is-monitored.md)
