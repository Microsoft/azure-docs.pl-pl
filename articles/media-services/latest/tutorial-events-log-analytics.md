---
title: Przechowywanie zdarzeń Azure Media Services na platformie Azure Log Analytics
titleSuffix: Azure Media Services
description: Dowiedz się, jak przechowywać zdarzenia Azure Media Services w usłudze Azure Log Analytics.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/24/2020
ms.author: inhenkel
ms.openlocfilehash: dfd7b3dcc7e27dd1251c35c6272b5aa7f8036006
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88942377"
---
# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Samouczek: przechowywanie zdarzeń Azure Media Services na platformie Azure Log Analytics

## <a name="azure-media-services-events"></a>Zdarzenia Azure Media Services

Azure Media Services v3 emituje zdarzenia na [Azure Event Grid](media-services-event-schemas.md). Możesz subskrybować zdarzenia na wiele sposobów i przechowywać je w magazynach danych. W tym samouczku zasubskrybujesz Media Services zdarzeń przy użyciu [przepływu aplikacji dziennika](https://azure.microsoft.com/services/logic-apps/). Aplikacja logiki zostanie wyzwolona dla każdego zdarzenia i będzie przechowywać treść zdarzenia w usłudze Azure Log Analytics. Gdy zdarzenia znajdują się w usłudze Azure Log Analytics, możesz użyć innych usług platformy Azure, aby utworzyć pulpit nawigacyjny, monitor i alert dotyczący tych zdarzeń, ale nie będzie on omawiany w tym samouczku.

> [!NOTE]
> Jest to przydatne, jeśli masz już doświadczenie z użyciem narzędzia FFmpeg jako kodera lokalnego.  Jeśli nie, to jest dobry. Poniżej znajduje się wiersz poleceń i instrukcje dotyczące przesyłania strumieniowego wideo.

Poznasz następujące czynności:

> [!div class="checklist"]
> * Tworzenie przepływu aplikacji logiki kodu
> * Subskrybowanie tematów dotyczących zdarzeń Azure Media Services
> * Analizowanie zdarzeń i zapisywanie do Log Analytics platformy Azure
> * Wykonywanie zapytań dotyczących zdarzeń z usługi Azure Log Analytics

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

> * [Subskrypcja platformy Azure](how-to-set-azure-subscription.md)
> * Konto [Media Services](create-account-howto.md) i Grupa zasobów.
> * Instalacja programu [Narzędzia FFmpeg](https://ffmpeg.org/download.html) dla systemu operacyjnego.
> * Obszar roboczy [log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Subskrybowanie zdarzenia Media Services za pomocą aplikacji logiki

1. W Azure Portal, jeśli jeszcze tego nie zrobiono, Utwórz [log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) obszar roboczy. Będziesz potrzebować identyfikatora obszaru roboczego i jednego z kluczy, więc należy się zadbać o to, aby okno przeglądarki było otwarte. Następnie otwórz Portal na innej karcie lub w oknie.

1. Przejdź do konta Azure Media Services i wybierz pozycję **zdarzenia**. Spowoduje to wyświetlenie wszystkich metod subskrybowania zdarzeń Azure Media Services.
    > [!div class="mx-imgBorder"]
    > ![Portal Azure Media Services](media/tutorial-events-log-analytics/select-events-01a.png)

1. Wybierz **ikonę Logic Apps** , aby utworzyć aplikację logiki. Spowoduje to otwarcie projektanta aplikacji logiki, w którym można utworzyć przepływ do przechwytywania zdarzeń i wypchnąć je do Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Tworzenie aplikacji logiki](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Wybierz **ikonę +**, wybierz dzierżawcę, którego chcesz użyć, a następnie wybierz pozycję Zaloguj. Zostanie wyświetlony monit logowania firmy Microsoft.
    > [!div class="mx-imgBorder"]
    > ![Nawiązywanie połączenia z Azure Event Grid ](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ Wybieranie dzierżawy](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. Wybierz pozycję **Kontynuuj** , aby subskrybować zdarzenia Media Services.
    > [!div class="mx-imgBorder"]
    > ![Połączono z Azure Event Grid](media/tutorial-events-log-analytics/select-continue-04.png)

1. Na liście **Typ zasobu** Znajdź "Microsoft. Media. MediaServices".
    > [!div class="mx-imgBorder"]
    >![Zdarzenia zasobów Azure Media Services](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Wybierz **element typ zdarzenia**. Zostanie wystawiona lista wszystkich zdarzeń Azure Media Services emisji. Można wybrać zdarzenia, które mają być śledzone. Można dodać wiele typów zdarzeń. (Później zostanie wprowadzona niewielka zmiana przepływu aplikacji logiki w celu przechowywania każdego typu zdarzenia w oddzielnym dzienniku Log Analytics i propagowanie nazwy typu zdarzenia do nazwy dziennika Log Analytics dynamicznie).
    > [!div class="mx-imgBorder"]
    > ![Typ zdarzenia Azure Media Services](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Wybierz pozycję **Zapisz jako**.

1. Nadaj nazwę aplikacji logiki.  Grupa zasobów jest domyślnie zaznaczona. Pozostaw inne ustawienia w taki sposób, a następnie wybierz pozycję **Utwórz**.  Nastąpi powrót do ekranu głównego platformy Azure.
    > [!div class="mx-imgBorder"]
    > ![Interfejs nazewnictwa aplikacji logiki](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Tworzenie akcji

Teraz, gdy subskrybujesz zdarzenia, Utwórz akcję.

1. Jeśli portal przeprowadzi Cię z powrotem do ekranu głównego, przejdź z powrotem do aplikacji logiki, która została właśnie utworzona, wyszukując wszystkie zasoby dla nazwy aplikacji.

1. Wybierz aplikację, a następnie wybierz pozycję **Projektant aplikacji logiki**. Zostanie otwarte okienko projektanta.

1. Wybierz pozycję **+ nowy krok**.

1. Ponieważ chcesz wypchnąć zdarzenia do usługi Azure Log Analytics, wyszukaj ciąg "Azure Log Analytics" i wybierz pozycję "moduł zbierający dane usługi Azure Log Analytics".
    > [!div class="mx-imgBorder"]
    > ![Moduł zbierający dane usługi Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Aby nawiązać połączenie z obszarem roboczym Log Analytics, wymagany jest identyfikator obszaru roboczego i klucz agenta. Otwórz Azure Portal na nowej karcie lub oknie, przejdź do obszaru roboczego Log Analytics utworzonego przed rozpoczęciem pracy z tym samouczkiem.
    > [!div class="mx-imgBorder"]
    > ![Identyfikator obszaru roboczego usługi Azure Log Analytics](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. W menu po lewej stronie Znajdź pozycję **Zarządzanie agentami** i wybierz ją. Zostaną wyświetlone klucze agentów, które zostały wygenerowane.
    > [!div class="mx-imgBorder"]
    > ![Zarządzanie agentami Log Analytics platformy Azure](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Skopiuj *Identyfikator obszaru roboczego*.
    > [!div class="mx-imgBorder"]
    > ![Identyfikator obszaru roboczego kopiowania](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. W innej karcie lub oknie przeglądarki w obszarze moduł zbierający dane Log Analytics Azure wybierz pozycję **Wyślij dane**, nadaj połączeniu nazwę, a następnie wklej *Identyfikator obszaru roboczego* w polu **Identyfikator obszaru roboczego** .

1. Wróć do karty lub okna przeglądarki obszaru roboczego i skopiuj *klucz obszaru roboczego*.
    > [!div class="mx-imgBorder"]
    > ![Klucz podstawowy zarządzania agentami](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. W drugiej karcie lub oknie przeglądarki wklej *klucz obszaru roboczego* w polu **klucz obszaru roboczego** .

1. Wybierz przycisk **Utwórz**. Teraz utworzysz treść żądania JSON i nazwę dziennika niestandardowego.

1. Wybierz pole **treści żądania JSON** .  Zostanie wyświetlony link umożliwiający **dodanie zawartości dynamicznej** .

1. Wybierz pozycję **Dodaj zawartość dynamiczną** , a następnie wybierz pozycję **temat**.

1. Wykonaj te same czynności dla **nazwy dziennika niestandardowego**.
    > [!div class="mx-imgBorder"]
    > ![Wybrany temat](media/tutorial-events-log-analytics/topic-selected.png)

1. Wybierz **Widok kodu** aplikacji logiki. Wyszukaj dane wejściowe i Log-Type wierszy.
    > [!div class="mx-imgBorder"]
    > ![Widok kodu dwóch wierszy](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Zmień `body` wartość z `"@triggerBody()?['topic']"` na `"@{triggerBody()}"` . Służy do analizowania całego komunikatu w celu Log Analytics.

1. Zmień wartość `Log-Type` z `"@triggerBody()?['topic']"` na `"@replace(triggerBody()?['eventType'],'.','')"` . (Spowoduje to zastąpienie ".", ponieważ nie jest to dozwolone w nazwach dzienników Log Analytics.)
    > [!div class="mx-imgBorder"]
    > ![JSON aplikacji logiki po zmianie](media/tutorial-events-log-analytics/changed-lines.png)

1. Wybierz pozycję **Zapisz**.

1. Aby sprawdzić, wybierz pozycję **Projektant aplikacji logiki**.
    > [!div class="mx-imgBorder"]
    > ![Weryfikuj etapy treści i funkcji](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Po sprawdzeniu wszystkich zasobów w grupie zasobów zostanie wyświetlona aplikacja logiki i dwa łączniki interfejsu API aplikacji logiki, jeden dla zdarzeń i jeden dla Log Analytics. Aby uzyskać więcej informacji na temat Event Grid tematów systemowych, Przeczytaj [temat Event Grid tematy systemowe](https://docs.microsoft.com/azure/event-grid/system-topics).
    > [!div class="mx-imgBorder"]
    > ![Zobacz wszystkie nowe zasoby w grupie zasobów](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Testowanie

Aby sprawdzić, jak faktycznie działa, Utwórz wydarzenie na żywo w Azure Media Services. Utwórz wydarzenie RTMP na żywo i użyj narzędzia FFmpeg do wypchnięcia strumienia "Live" na podstawie przykładowego pliku. MP4. Po utworzeniu zdarzenia Uzyskaj adres URL pozyskiwania RTMP.

1. Na koncie usługi Media Services wybierz pozycję **Transmisja strumieniowa na żywo**.
    > [!div class="mx-imgBorder"]
    > ![Tworzenie Azure Media Services zdarzenia na żywo](media/tutorial-events-log-analytics/live-event.png)

1. Wybierz pozycję **Dodaj wydarzenie na żywo**.

1. Wprowadź nazwę w polu **Live Name (Nazwa zdarzenia** ). (Pole **Opis** jest opcjonalne).

1. Wybierz **standardowe** kodowanie w chmurze.

1. Wybierz **domyślną wartość 720** dla ustawienia wstępnego kodowania.

1. Wybierz protokół wejścia **RTMP** .

1. Wybierz pozycję **tak** dla trwałego wejściowego adresu URL.

1. Wybierz pozycję **tak** , aby uruchomić zdarzenie po utworzeniu zdarzenia.

    > [!WARNING]
    > Naliczanie opłat rozpocznie się, jeśli wybierzesz opcję tak.  Jeśli chcesz czekać na uruchomienie strumienia do momentu *przed* rozpoczęciem przesyłania strumieniowego z narzędzia FFmpeg, wybierz pozycję **nie** i pamiętaj o rozpoczęciu wydarzenia na żywo.

    > [!div class="mx-imgBorder"]
    > ![Ustawienia zdarzeń na żywo](media/tutorial-events-log-analytics/live-event-settings.png)

1. Zaznacz pole wyboru **mam wszystkie prawa do używania zawartości/pliku...** .

1. Wybierz pozycję **Przeglądanie + tworzenie**.

1. Przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**.  Zostanie wyświetlona lista zdarzeń na żywo i zostanie wyświetlony adres URL pozyskiwania zdarzeń na żywo.

1. Skopiuj **adres URL** pozyskiwania do Schowka.

1. Wybierz **wydarzenie na żywo** na liście, aby wyświetlić widok producent.

### <a name="stream-with-ffmpeg-cli"></a>Strumień przy użyciu interfejsu wiersza polecenia narzędzia FFmpeg

1. Użyj poniższego wiersza polecenia.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Zmień `<ingestURL>` na adres URL pozyskiwania skopiowany do Schowka.
1. Zmień `<localpathtovideo>` ścieżkę lokalną pliku, który chcesz przesłać strumieniowo z narzędzia FFmpeg.
1. Dodaj unikatową nazwę na końcu, na przykład `mystream` .
1. Dostosuj wiersz polecenia, aby odzwierciedlał plik źródłowy testu i wszystkie inne zmienne systemowe.
1. Uruchom polecenie. Po kilku sekundach gracz "widok producenta" powinien rozpocząć przesyłanie strumieniowe. (Odświeżenie odtwarzacza, jeśli film wideo nie jest wyświetlany automatycznie).

    > [!div class="mx-imgBorder"]
    > ![Weryfikacja prawidłowego pozyskiwania filmów wideo w programie Producer w wersji zapoznawczej](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Weryfikowanie zdarzeń

Za pomocą strumienia na żywo usługa Azure Media Services emituje różne zdarzenia wyzwalające przepływ aplikacji logiki. Aby sprawdzić, przejdź do aplikacji logiki i ustal, czy istnieją wyzwalacze wywoływane przez zdarzenia z Media Services.

1. Przejdź do strony przeglądu aplikacji logiki, aby wyświetlić listę zadań, które zostały ukończone pomyślnie.
    > [!div class="mx-imgBorder"]
    > ![Weryfikowanie pomyślnego wykonania zadania w aplikacji logiki](media/tutorial-events-log-analytics/run-history.png)

1. Wybierz zadanie zakończone powodzeniem. Wyświetlane są szczegóły zadania w czasie wykonywania.
1. Wybierz pozycję **Wyślij dane** , aby ją rozwinąć. W takim przypadku `MicrosoftMediaLiveEventEncoderConnected` zdarzenie pokazuje, że zostało przechwycone oraz przeanalizowana treść. To jest wypychane do obszaru roboczego usługi Azure Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Zobacz wysyłanie danych](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Weryfikowanie dzienników

1. Przejdź do utworzonego wcześniej obszaru roboczego Log Analytics.

1. Wybierz pozycję **dzienniki**.
1. Zamknij okno podręczne przykładowe zapytania.
1. Zostanie wyświetlona lista dzienników niestandardowych. Wybierz strzałkę w dół, aby ją rozwinąć. Zostanie wyświetlona nazwa zdarzenia `MicrosoftMediaLiveEventEncoderConnected` .
1. Wybierz nazwę zdarzenia, aby ją rozwinąć.
1. Po wybraniu ikony "oczy" zostanie wyświetlona wersja zapoznawcza wyniku zapytania.
1. Wybierz pozycję **Zobacz w edytorze zapytań** , a następnie wybierz element w obszarze **TimeGenerated UTC** list, aby go rozwinąć i wyświetlić dane pierwotne.

![Zobacz szczegółowe dane wyjściowe zdarzenia w Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Usuwanie zasobów

Jeśli nie chcesz nadal używać zasobów utworzonych w ramach tego samouczka, upewnij się, że usunięto wszystkie zasoby w grupie zasobów lub nadal będą naliczone opłaty.

## <a name="next-steps"></a>Następne kroki

Możesz tworzyć różne zapytania i je zapisywać. Można je dodać do [pulpitu nawigacyjnego platformy Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards).
