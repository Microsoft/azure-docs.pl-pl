---
title: 'Samouczek: monitorowanie zasobów chmurowych platformy Azure przy użyciu alertów i grup akcji | Microsoft Docs'
description: Dowiedz się, jak korzystać z alertów w chmurze wiosennej.
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 12/29/2019
ms.custom: devx-track-java
ms.openlocfilehash: dc8dffb12dcd205671e2219dbef45ac14f9f7df7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892538"
---
# <a name="tutorial-how-to-monitor-spring-cloud-resources-using-alerts-and-action-groups"></a>Samouczek: Jak monitorować zasoby w chmurze wiosny przy użyciu alertów i grup akcji

**Ten artykuł ma zastosowanie do:** ✔️ Java ✔️ C #

Alerty chmurowe platformy Azure obsługują monitorowanie zasobów na podstawie takich warunków, jak dostępne magazyny, częstotliwość żądań lub użycie danych. Alert wysyła powiadomienie, gdy stawki lub warunki spełniają zdefiniowane specyfikacje.

Aby skonfigurować potok alertów, należy wykonać dwie czynności: 
1. Skonfiguruj grupę akcji z akcjami, które mają być podejmowane po wyzwoleniu alertu, na przykład wiadomości e-mail, wiadomości SMS, elementu Runbook lub elementu webhook. Grupy akcji mogą być ponownie używane między różnymi alertami.
2. Skonfiguruj reguły alertów. Reguły wiążą wzorce metryk z grupami akcji w oparciu o zasób docelowy, metrykę, warunek, agregację czasu itp.

## <a name="prerequisites"></a>Wymagania wstępne

Procedury przedstawione w tym samouczku współpracują ze wdrożonym wystąpieniem chmury Azure wiosną.  Aby rozpocząć, Skorzystaj z [przewodnika Szybki Start](spring-cloud-quickstart.md) .

Poniższe procedury inicjują **grupę akcji** i **alert** , rozpoczynając od opcji **alertów** w okienku nawigacji po lewej stronie wystąpienia chmury wiosnowej. (Procedurę można również uruchomić na stronie **Przegląd monitora** w Azure Portal). 

Przejdź do grupy zasobów do wystąpienia chmury wiosennej. W lewym okienku wybierz pozycję **alerty** , a następnie wybierz pozycję **Zarządzaj akcjami**:

![Strona grupy zasobów portalu zrzutu ekranu](media/alerts-action-groups/action-1-a.png)

## <a name="set-up-action-group"></a>Konfigurowanie grupy akcji

Aby rozpocząć procedurę inicjowania nowej **grupy akcji**, wybierz pozycję **+ Dodaj grupę akcji**.

![Dodaj grupę akcji do portalu zrzutu ekranu](media/alerts-action-groups/action-1.png)

Na stronie **Dodaj grupę akcji** :

 1. Określ **nazwę grupy akcji** i **krótką nazwę**.

 1. Określ **subskrypcję** i **grupę zasobów**.

 1. Określ **nazwę akcji**.

 1. Wybierz **Typ akcji**.  Spowoduje to otwarcie kolejnego okienka po prawej stronie, aby zdefiniować akcję, która zostanie podjęta po aktywacji.

 1. Zdefiniuj akcję przy użyciu opcji w okienku po prawej stronie.  W tym przypadku jest stosowane powiadomienie e-mail.

 1. Kliknij przycisk **OK** w prawym okienku akcji.

 1. Kliknij przycisk **OK** w oknie dialogowym **Dodaj grupę akcji** . 

  ![Akcja definiowania portalu zrzutu ekranu](media/alerts-action-groups/action-2.png)

## <a name="set-up-alert"></a>Konfigurowanie alertu 

W poprzednich krokach utworzono **grupę akcji** , która używa poczty e-mail. Możesz również użyć powiadomień na telefon, elementów webhook, usługi Azure Functions i tak dalej. Poniższe kroki konfigurują **alert**.

1. Przejdź z powrotem do strony **alerty** , a następnie kliknij pozycję **Zarządzaj regułami alertów**.

   ![Portal zrzutu ekranu — Definiowanie alertu](media/alerts-action-groups/alerts-2.png)

1. Wybierz **zasób** dla alertu.

1. Kliknij pozycję **+ Nowa reguła alertu**.

   ![Nowa reguła alertu portalu zrzutu ekranu](media/alerts-action-groups/alerts-3.png)

1. Na stronie **Tworzenie reguły** Określ **zasób**.

1. Ustawienie **warunek** zapewnia wiele opcji monitorowania zasobów **chmury wiosnowej** .  Kliknij przycisk **Dodaj** , aby otworzyć okienko **Konfiguruj logikę sygnału** .

1. Wybierz warunek. W tym przykładzie wykorzystuje **procent użycia procesora CPU**.

   ![Portal zrzutów ekranu — Nowa reguła alertu 2](media/alerts-action-groups/alerts-3-1.png)

1. Przewiń w dół okienko **Konfigurowanie logiki sygnału** , aby ustawić **wartość progową** do monitorowania.

   ![Portal zrzutów ekranu — Nowa reguła alertu 3](media/alerts-action-groups/alerts-3-2.png)

1. Kliknij pozycję **Gotowe**.

   Aby uzyskać szczegółowe informacje na temat warunków dostępnych do monitorowania, zobacz [Opcje metryk portalu użytkowników](spring-cloud-concept-metrics.md#user-metrics-options).

1. W obszarze **Akcje**kliknij pozycję **Wybierz grupę akcji**. W okienku **Akcje** wybierz wcześniej zdefiniowaną **grupę akcji**.

   ![Nowy alert dotyczący portalu zrzutu ekranu 4](media/alerts-action-groups/alerts-3-3.png) 

1. Przewiń w dół i w obszarze **szczegóły alertu**Nadaj nazwę regule alertu.

1. Ustaw **ważność**.

1. Kliknij pozycję **Utwórz regułę alertu**.

   ![Portal zrzutów ekranu — Nowa reguła alertu 5](media/alerts-action-groups/alerts-3-4.png)

1. Sprawdź, czy Nowa reguła alertu jest włączona.

   ![Portal zrzutów ekranu — Nowa reguła alertu 6](media/alerts-action-groups/alerts-4.png)

Regułę można również utworzyć za pomocą strony **metryki** :

![Portal zrzutów ekranu — Nowa reguła alertu 7](media/alerts-action-groups/alerts-5.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób konfigurowania alertów i grup akcji dla aplikacji w chmurze ze sprężyną na platformie Azure. Aby dowiedzieć się więcej na temat grup akcji, zobacz:

> [!div class="nextstepaction"]
> [Tworzenie grup akcji i zarządzanie nimi w witrynie Azure Portal](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

> [!div class="nextstepaction"]
> [Zachowanie alertów SMS w grupach akcji](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-sms-behavior)
