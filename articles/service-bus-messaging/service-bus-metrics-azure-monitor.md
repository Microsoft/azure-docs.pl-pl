---
title: Metryki Azure Service Bus w Azure Monitor | Microsoft Docs
description: W tym artykule wyjaśniono, jak używać Azure Monitor do monitorowania jednostek Service Bus (kolejek, tematów i subskrypcji).
ms.topic: article
ms.date: 09/30/2020
ms.openlocfilehash: 169edb651a59302d0ea1245fd48787404dd3e555
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598126"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Metryki Azure Service Bus w Azure Monitor

Metryki Service Bus umożliwiają udostępnienie stanu zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję zasobów Service Bus, nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu Service Bus. Metryki mogą również pomóc w rozwiązywaniu problemów dotyczących głównych przyczyn, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Azure Monitor zapewnia ujednolicone interfejsy użytkownika do monitorowania różnych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie w Microsoft Azure](../azure-monitor/overview.md) i [pobieranie metryk Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

> [!IMPORTANT]
> Gdy nie dojdzie do żadnej interakcji z jednostką przez 2 godziny, metryki rozpoczną wyświetlanie "0" jako wartości do momentu, gdy jednostka nie będzie już w stanie bezczynności.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor zapewnia wiele sposobów uzyskiwania dostępu do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak dzienniki Azure Monitor i Event Hubs. Aby uzyskać więcej informacji, zobacz [metryki w Azure monitor](../azure-monitor/platform/data-platform-metrics.md).

Metryki są domyślnie włączone i można uzyskać dostęp do najnowszych danych z ostatnich 30 dni. Jeśli chcesz zachować dane przez dłuższy czas, możesz zarchiwizować dane metryk na koncie usługi Azure Storage. Ta wartość jest konfigurowana w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Można monitorować metryki w czasie w [Azure Portal](https://portal.azure.com). Poniższy przykład pokazuje, jak wyświetlić pomyślne żądania i żądania przychodzące na poziomie konta:

![Zrzut ekranu przedstawiający stronę monitor-metryki (wersja zapoznawcza) w Azure Portal.][1]

Możesz również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij pozycję **metryki**. Aby wyświetlić metryki odfiltrowane do zakresu jednostki, wybierz jednostkę, a następnie kliknij pozycję **metryki**.

![Zrzut ekranu przedstawiający stronę monitor-metryki (wersja zapoznawcza) przefiltrowaną do zakresu jednostki.][2]

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru.

## <a name="billing"></a>Rozliczenia

Metryki i alerty dotyczące Azure Monitor są naliczane według poszczególnych alertów. Opłaty te powinny być dostępne w portalu po skonfigurowaniu alertu i przed jego zapisaniem. 

Dodatkowe rozwiązania, w przypadku których dane metryk są rozliczane bezpośrednio przez te rozwiązania. Na przykład w przypadku archiwizowania danych metryk na koncie usługi Azure Storage są naliczane opłaty za usługę Azure Storage. Opłaty są naliczane według Log Analytics, jeśli przesyłasz strumieniowo dane metryk do Log Analytics na potrzeby zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Gdy są one przenoszone pod inną nazwę, jest przestarzałe kilka metryk. Może to wymagać aktualizacji odwołań. Metryki oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane do przodu.

Wszystkie wartości metryk są wysyłane do Azure Monitor co minutę. Stopień szczegółowości czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Service Bus wynosi 1 minutę.

## <a name="request-metrics"></a>Metryki żądań

Zlicza żądania operacji na danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące| Liczba żądań wysyłanych do usługi Service Bus w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Żądania pomyślne|Liczba pomyślnych żądań do usługi Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Błędy serwera|Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Błędy użytkowników (zobacz następującą podsekcję)|Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Ograniczone żądania|Liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|

### <a name="user-errors"></a>Błędy użytkownika

Następujące dwa typy błędów są klasyfikowane jako błędy użytkownika:

1. Błędy po stronie klienta (w przypadku protokołu HTTP, które byłyby błędy 400).
2. Błędy, które występują podczas przetwarzania komunikatów, takie jak [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Metryki komunikatów

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące|Liczba zdarzeń lub komunikatów wysłanych do Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Komunikaty wychodzące|Liczba zdarzeń lub komunikatów odebranych z Service Bus w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
| Komunikaty| Liczba komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Wymiar: Nazwa jednostki |
| Aktywne komunikaty| Liczba aktywnych komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/> Wymiar: Nazwa jednostki |
| Wiadomości utracone| Liczba utraconych wiadomości w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia <br/>Wymiar: Nazwa jednostki |
| Zaplanowane wiadomości| Liczba zaplanowanych komunikatów w kolejce/temacie. <br/><br/> Jednostka: liczba <br/> Typ agregacji: Średnia  <br/> Wymiar: Nazwa jednostki |
| Rozmiar | Rozmiar jednostki (kolejki lub tematu) w bajtach. <br/><br/>Jednostka: liczba <br/>Typ agregacji: Średnia <br/>Wymiar: Nazwa jednostki | 

> [!NOTE]
> Wartości dla następujących metryk są wartościami punktu w czasie. Komunikaty przychodzące, które były używane natychmiast po tym punkcie w czasie, mogą nie być odzwierciedlone w tych metrykach. 
> - Komunikaty
> - Aktywne komunikaty 
> - Wiadomości utracone 
> - Zaplanowane wiadomości 

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Aktywne połączenia|Liczba aktywnych połączeń w przestrzeni nazw, jak również dla jednostki w przestrzeni nazw. Wartość tej metryki to wartość wskazywana przez punkt w czasie. Połączenia, które były aktywne natychmiast po tym punkcie w czasie, mogą nie być odzwierciedlone w metryce.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Otwarte połączenia |Liczba otwartych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|
|Połączenia zamknięte |Liczba zamkniętych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: Nazwa jednostki|

## <a name="resource-usage-metrics"></a>Metryki użycia zasobów

> [!NOTE] 
> Następujące metryki są dostępne tylko w warstwie **Premium** . 
> 
> Ważne metryki do monitorowania dla przestrzeni nazw warstwy Premium są następujące: **użycie procesora CPU na przestrzeń** nazw i **rozmiar pamięci na przestrzeń nazw**. [Skonfiguruj alerty](../azure-monitor/platform/alerts-metric.md) dla tych metryk przy użyciu Azure monitor.
> 
> Inną metryką, którą można monitorować, jest: **żądania ograniczone**. Nie powinno to być problemem, o ile przestrzeń nazw pozostaje w limitach połączeń z pamięcią, PROCESORem i brokerem. Aby uzyskać więcej informacji, zobacz [ograniczanie przepustowości w Azure Service Bus warstwie Premium](service-bus-throttling.md#throttling-in-azure-service-bus-premium-tier)

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Użycie procesora CPU na przestrzeń nazw|Procent użycia procesora w przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksimum <br/> Wymiar: Nazwa jednostki|
|Użycie rozmiaru pamięci na przestrzeń nazw|Procent użycia pamięci w przestrzeni nazw.<br/><br/> Jednostka: procent <br/> Typ agregacji: maksimum <br/> Wymiar: Nazwa jednostki|

## <a name="metrics-dimensions"></a>Wymiary metryk

Azure Service Bus obsługuje następujące wymiary dla metryk w Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określane na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|Nazwa jednostki| Service Bus obsługuje jednostki obsługi komunikatów w przestrzeni nazw.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurowanie alertów dotyczących metryk

1. Na karcie **metryki** **obszaru nazw Service Bus** wybierz pozycję **Konfiguruj alerty**. 

    ![Strona metryk — Konfigurowanie alertów](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Wybierz opcję **Wybierz cel** i wykonaj następujące czynności na stronie **Wybierz zasób** : 
    1. Wybierz **Service Bus przestrzenie nazw** dla pola **Filtr według typu zasobu** . 
    2. Wybierz subskrypcję dla pola **Filtruj według subskrypcji** .
    3. Wybierz z listy **przestrzeń nazw usługi Service Bus** . 
    4. Wybierz pozycję **Gotowe**. 
    
        ![Wybierz przestrzeń nazw](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Wybierz pozycję **Dodaj kryteria**i wykonaj następujące czynności na stronie **Konfigurowanie logiki sygnałów** :
    1. Wybierz **metryki** dla **typu sygnału**. 
    2. Wybierz sygnał. Na przykład: **błędy usługi**. 

        ![Wybieranie błędów serwera](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Wybierz opcję **większe niż** w przypadku **warunku**.
    2. Wybierz pozycję **Suma** dla **agregacji czasu**. 
    3. Wprowadź wartość **5** w obszarze **próg**. 
    4. Wybierz pozycję **Gotowe**.    

        ![Określ warunek](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. Na stronie **Tworzenie reguły** rozwiń pozycję **Zdefiniuj szczegóły alertu**i wykonaj następujące czynności:
    1. Wprowadź **nazwę** alertu. 
    2. Wprowadź **Opis** alertu.
    3. Wybierz pozycję **ważność** dla alertu. 

        ![Zrzut ekranu przedstawiający stronę Tworzenie reguły. Zdefiniuj szczegóły alertu, a pola dla nazwy, opisu i ważności reguły alertu są wyróżnione.](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. Na stronie **Tworzenie reguły** rozwiń węzeł **Zdefiniuj grupę akcji**, wybierz pozycję **Nowa grupa akcji**i wykonaj następujące czynności na **stronie Dodaj grupę akcji**. 
    1. Wprowadź nazwę grupy akcji.
    2. Wprowadź krótką nazwę grupy akcji. 
    3. Wybierz subskrypcję. 
    4. Wybierz grupę zasobów. 
    5. W tym instruktażu wprowadź nazwę **Wyślij wiadomość e-mail** dla **akcji**.
    6. Wybierz pozycję **poczta e-mail/SMS/wypychanie/głos** dla **typu akcji**. 
    7. Wybierz pozycję **Edytuj szczegóły**. 
    8. Na stronie **wiadomości e-mail/SMS/wypychanie/głos** wykonaj następujące czynności:
        1. Wybierz pozycję **Adres e-mail**. 
        2. Wpisz **adres e-mail**. 
        3. Wybierz przycisk **OK**.

            ![Zrzut ekranu przedstawiający stronę Dodawanie grupy akcji. Akcja o nazwie "Wyślij wiadomość e-mail" z akcją typu email/SMS/push/Voice jest dodawana do grupy.](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. Na stronie **Dodaj grupę akcji** wybierz pozycję **OK**. 
1. Na stronie **Tworzenie reguły** wybierz pozycję **Utwórz regułę alertu**. 

    ![Przycisk tworzenia reguły alertu](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Następne kroki

Zobacz [omówienie Azure monitor](../azure-monitor/overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png