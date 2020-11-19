---
title: Szybki Start — obsługa zdarzeń SMS
titleSuffix: An Azure Communication Services quickstart
description: Dowiedz się, jak obsługiwać zdarzenia SMS przy użyciu usług Azure Communications Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e8decbe22f869573bf7a2221099d2bc30c00fc8a
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888848"
---
# <a name="quickstart-handle-sms-events"></a>Szybki Start: obsługa zdarzeń programu SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu Azure Event Grid do obsługi zdarzeń programu SMS dotyczących usług komunikacyjnych. 

## <a name="about-azure-event-grid"></a>Informacje o Azure Event Grid

[Azure Event Grid](../../../event-grid/overview.md) to oparta na chmurze usługa obsługi zdarzeń. W tym artykule dowiesz się, jak subskrybować zdarzenia dla [zdarzeń usługi komunikacyjnej](../../concepts/event-handling.md)i wyzwalać zdarzenie, aby wyświetlić wynik. Zazwyczaj użytkownik wysyła zdarzenia do punktu końcowego, w którym następuje przetwarzanie danych zdarzenia i są wykonywane akcje. W tym artykule wyślemy zdarzenia do aplikacji sieci Web, która gromadzi i wyświetla komunikaty.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Zasób usługi komunikacyjnej platformy Azure. Więcej szczegółowych informacji można znaleźć w przewodniku Szybki Start [dotyczący tworzenia zasobu komunikacyjnego platformy Azure](../create-communication-resource.md) .
- Numer telefonu z włączoną funkcją SMS. [Pobierz numer telefonu](./get-phone-number.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="enable-event-grid-resource-provider"></a>Włączanie dostawcy zasobów usługi Event Grid

Jeśli Event Grid w ramach subskrypcji platformy Azure nie były wcześniej używane, może być konieczne zarejestrowanie dostawcy zasobów Event Grid, wykonując poniższe kroki:

W witrynie Azure Portal:

1. W menu po lewej stronie wybierz pozycję **subskrypcje** .
2. Wybierz subskrypcję, której używasz dla usługi Event Grid.
3. W menu po lewej stronie w obszarze **Ustawienia** wybierz pozycję **dostawcy zasobów**.
4. Znajdź dostawcę **Microsoft.EventGrid**.
5. Jeśli nie jest on zarejestrowany, wybierz pozycję **Zarejestruj**. 

Ukończenie rejestracji może chwilę potrwać. Wybierz pozycję **Odśwież**, aby zaktualizować stan. Gdy **Stan** będzie miał wartość **Zarejestrowano**, możesz kontynuować.

### <a name="event-grid-viewer-deployment"></a>Wdrożenie przeglądarki Event Grid

W tym przewodniku szybki start użyjemy [przykładowej przeglądarki Azure Event Grid](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) do wyświetlania zdarzeń w czasie niemal rzeczywistym. Pozwoli to użytkownikowi na korzystanie z kanału informacyjnego w czasie rzeczywistym. Ponadto ładunek każdego zdarzenia powinien być również dostępny do celów inspekcji.  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>Subskrybowanie zdarzeń programu SMS przy użyciu elementów webhook

W portalu przejdź do utworzonego zasobu usługi Azure Communications Services. W obszarze zasób usługi komunikacyjnej wybierz pozycję **zdarzenia** z menu po lewej stronie **usługi komunikacyjne** .

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="Zrzut ekranu przedstawiający Wybieranie przycisku subskrypcji zdarzeń na stronie zdarzenia zasobu.":::

Naciśnij pozycję **Dodaj subskrypcję zdarzeń** , aby wprowadzić Kreatora tworzenia.

Na stronie **Tworzenie subskrypcji zdarzeń** wprowadź **nazwę** subskrypcji zdarzeń.

Możesz subskrybować określone zdarzenia, aby poinformować Event Grid, które zdarzenia programu SMS mają być śledzone, i miejsce, w którym mają być wysyłane zdarzenia. Wybierz zdarzenia, które chcesz subskrybować, z menu rozwijanego. W przypadku programu SMS można wybrać opcję `SMS Received` i `SMS Delivery Report Received` . 

Jeśli zostanie wyświetlony monit o podanie **nazwy tematu systemu**, możesz udostępnić unikatowy ciąg. To pole nie ma wpływu na środowisko użytkownika i służy do celów wewnętrznych telemetrii.

Zapoznaj się z pełną listą [zdarzeń obsługiwanych przez usługi Azure Communication Services](../../concepts/event-handling.md).

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="Zrzut ekranu przedstawiający wybrane typy zdarzeń odebrane przez SMS i raport dostarczania SMS.":::

Wybierz **element Hook sieci Web** dla **typu punktu końcowego**. 

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="Zrzut ekranu przedstawiający pole typu punktu końcowego ustawione na element Hook sieci Web.":::

W przypadku **punktu końcowego** kliknij pozycję **Wybierz punkt końcowy**, a następnie wprowadź adres URL aplikacji sieci Web.

W takim przypadku będziemy używać adresu URL z [przykładowej przeglądarki Azure Event Grid Viewer](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) , która została wcześniej skonfigurowana w przewodniku Szybki Start. Adres URL dla przykładu będzie mieć format: `https://{{site-name}}.azurewebsites.net/api/updates`

Następnie wybierz pozycję **Potwierdź wybór**.

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="Zrzut ekranu przedstawiający potwierdzenie punktu końcowego elementu webhook.":::

## <a name="viewing-sms-events"></a>Wyświetlanie zdarzeń programu SMS

### <a name="triggering-sms-events"></a>Wyzwalanie zdarzeń SMS

Aby wyświetlić wyzwalacze zdarzeń, należy wygenerować zdarzenia w pierwszej kolejności.

- `SMS Received` zdarzenia są generowane, gdy numer telefonu usług komunikacyjnych otrzymuje wiadomość SMS. Aby wyzwolić zdarzenie, po prostu wyślij wiadomość od telefonu do numeru telefonu dołączonego do zasobu usług komunikacyjnych.
- `SMS Delivery Report Received` zdarzenia są generowane podczas wysyłania wiadomości SMS do użytkownika przy użyciu numeru telefonu usług komunikacyjnych. Aby wyzwolić zdarzenie, musisz włączyć `Delivery Report` w opcjach [wysłanych wiadomości SMS](../telephony-sms/send.md). Spróbuj wysłać komunikat do telefonu za pomocą `Delivery Report` . Wykonanie tej akcji wiąże się z niewielkim kosztem kilku USD lub mniej na koncie platformy Azure.

Zapoznaj się z pełną listą [zdarzeń obsługiwanych przez usługi Azure Communication Services](../../concepts/event-handling.md).

### <a name="receiving-sms-events"></a>Otrzymywanie zdarzeń SMS

Po wykonaniu każdej akcji powyżej zobaczysz, że `SMS Received` `SMS Delivery Report Received` zdarzenia i są wysyłane do punktu końcowego. Te zdarzenia będą wyświetlane w [przykładowej Azure Event Grid przeglądarki](/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/) , którą ustawimy na początku. Aby zobaczyć cały ładunek, możesz nacisnąć ikonę oka obok zdarzenia. Zdarzenia będą wyglądać następująco:

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="Zrzut ekranu przedstawiający schemat Event Grid dla zdarzenia otrzymanego SMS.":::

:::image type="content" source="./media/handle-sms-events/sms-delivery-report-received.png" alt-text="Zrzut ekranu przedstawiający schemat Event Grid dla zdarzenia raportu dostarczania SMS.":::

Dowiedz się więcej o [schematach zdarzeń i innych pojęciach związanych z zdarzeniami](../../concepts/event-handling.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób korzystania z zdarzeń programu SMS. Wiadomości SMS można odbierać przez utworzenie subskrypcji Event Grid.

> [!div class="nextstepaction"] 
> [Wyślij wiadomość SMS](../telephony-sms/send.md)

Możesz również chcieć:

 - [Informacje o pojęciach dotyczących obsługi zdarzeń](../../concepts/event-handling.md)
 - [Dowiedz się więcej o Event Grid](../../../event-grid/overview.md)