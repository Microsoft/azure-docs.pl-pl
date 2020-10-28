---
title: Jak monitorować konto usługi Azure Storage w Azure Portal | Microsoft Docs
description: Dowiedz się, jak monitorować konto magazynu na platformie Azure przy użyciu Azure Portal i analityka magazynu platformy Azure.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bfecae4775655f732df8fd6cffea613cd39fb828
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782012"
---
# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Monitorowanie konta magazynu w witrynie Azure Portal

[Analityka magazynu platformy Azure](storage-analytics.md) udostępnia metryki dla wszystkich usług magazynu oraz dzienników obiektów blob, kolejek i tabel. Możesz użyć [Azure Portal](https://portal.azure.com) , aby skonfigurować metryki i dzienniki, które są rejestrowane dla Twojego konta, i skonfigurować wykresy, które umożliwiają wizualne reprezentacje danych metryk. 

Zalecamy zapoznanie się [Azure monitor z magazynem](../../azure-monitor/insights/storage-insights-overview.md) (wersja zapoznawcza). Jest to funkcja Azure Monitor, która oferuje kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony wgląd w wydajność, wydajność i dostępność usług Azure Storage. Nie wymaga to włączania ani konfigurowania niczego i można od razu wyświetlać te metryki ze wstępnie zdefiniowanych wykresów interaktywnych i innych wizualizacji.

> [!NOTE]
> Istnieją koszty związane z badaniem danych monitorowania w Azure Portal. Aby uzyskać więcej informacji, zobacz [analityka magazynu](storage-analytics.md).
>
> Azure Files obecnie obsługuje metryki analityka magazynu, ale nie obsługuje jeszcze rejestrowania.
>
> Konta magazynu blokowych obiektów BLOB wydajności w warstwie Premium nie obsługują metryk analitycznych magazynu, ale obsługują rejestrowanie. Rejestrowanie można włączyć programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Aby wyświetlić metryki z kontami magazynu obiektów BLOB wydajności Premium, należy rozważyć użycie [metryk usługi Azure Storage w Azure monitor](../blobs/monitor-blob-storage.md).
>
> Aby uzyskać szczegółowy przewodnik dotyczący używania analityka magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).
>

## <a name="configure-monitoring-for-a-storage-account"></a>Konfigurowanie monitorowania dla konta magazynu

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **konta magazynu** , a następnie nazwę konta magazynu, aby otworzyć pulpit nawigacyjny konta.
1. Wybierz pozycję **Diagnostyka** w sekcji **monitorowanie** w bloku menu.

    ![Zrzut ekranu, który podświetla opcję Ustawienia diagnostyczne (klasyczne) w sekcji Monitorowanie (klasyczne).](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Wybierz **Typ** danych metryk dla każdej **usługi** , którą chcesz monitorować, oraz **zasady przechowywania** danych. Monitorowanie można również wyłączyć, ustawiając **stan** na **wyłączone** .

    ![MonitoringOptions](./media/storage-monitor-storage-account/storage-enable-metrics-01.png)

   Aby ustawić zasady przechowywania danych, przenieś suwak **przechowywanie (dni)** lub wprowadź liczbę dni, po których dane mają zostać zachowane, od 1 do 365. Wartość domyślna dla nowych kont magazynu wynosi siedem dni. Jeśli nie chcesz ustawiać zasad przechowywania, wprowadź wartość zero. Jeśli nie ma zasad przechowywania, można usunąć dane monitorowania.

   > [!WARNING]
   > Opłaty są naliczone po ręcznym usunięciu danych metryk. Nieodświeżone dane analityczne (dane starsze niż zasady przechowywania) są usuwane przez system bez ponoszenia kosztów. Zalecamy ustawienie zasad przechowywania w zależności od tego, jak długo mają być przechowywane dane analizy magazynu dla Twojego konta. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozliczeń za magazyn](storage-analytics-metrics.md#billing-on-storage-metrics) .
   >

1. Po zakończeniu konfigurowania monitorowania wybierz pozycję **Zapisz** .

Domyślny zestaw metryk jest wyświetlany na wykresach w bloku konta magazynu, a także w przypadku poszczególnych bloków usługi (obiektów blob, kolejek, tabel i plików). Po włączeniu metryk dla usługi może upłynąć do godziny, aby dane były widoczne na wykresach. Możesz wybrać opcję **Edytuj** na dowolnym wykresie metryki, aby skonfigurować metryki, które są wyświetlane na wykresie.

Zbieranie metryk i rejestrowanie można wyłączyć, ustawiając **stan** na **wyłączone** .

> [!NOTE]
> Usługa Azure Storage używa [magazynu tabel](storage-introduction.md#table-storage) do przechowywania metryk dla konta magazynu i przechowuje metryki w tabelach na Twoim koncie. Aby uzyskać więcej informacji, zobacz [Jak są przechowywane metryki](storage-analytics-metrics.md#how-metrics-are-stored).
>

## <a name="customize-metrics-charts"></a>Dostosuj wykresy metryk

Aby określić, które metryki magazynu mają być wyświetlane na wykresie metryk, należy wykonać poniższą procedurę.

1. Zacznij od wyświetlenia wykresu metryki magazynu w Azure Portal. Wykresy można znaleźć w **bloku konta magazynu** i w bloku **metryk** dla poszczególnych usług (obiektów blob, kolejek, tabel, plików).

   W tym przykładzie używa następującego wykresu, który jest wyświetlany w **bloku konta magazynu** :

   ![Wybór wykresu w Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-00.png)

1. Kliknij w dowolnym miejscu na wykresie, aby edytować wykres.

1. Następnie wybierz **zakres czasu** metryk, które mają być wyświetlane na wykresie, oraz **usługę** (obiekt BLOB, kolejka, tabela, plik), których metryki chcesz wyświetlić. W tym miejscu są wybrane metryki z zeszłego tygodnia do wyświetlenia dla usługi BLOB Service:

   ![Zakres czasu i wybór usługi w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-time-range.png)

1. Wybierz poszczególne **metryki** , które mają być wyświetlane na wykresie, a następnie kliknij przycisk **OK** .

   ![Wybór poszczególnych metryk w bloku Edytuj wykres](./media/storage-monitor-storage-account/storage-edit-metric-selections.png)

Ustawienia wykresu nie wpływają na zbieranie, agregowanie ani przechowywanie danych monitorowania na koncie magazynu.

### <a name="metrics-availability-in-charts"></a>Dostępność metryk na wykresach

Lista dostępnych metryk zmienia się w zależności od wybranej usługi na liście rozwijanej oraz typu jednostki edytowanego wykresu. Na przykład można wybrać metryki procentowe, takie jak *wzrost percentnetworkerror* i *wzrost percentthrottlingerror* , tylko w przypadku edytowania wykresu, w którym wyświetlane są jednostki w procentach:

![Wykres procentowy błędu żądania w Azure Portal](./media/storage-monitor-storage-account/stg-customize-chart-04.png)

### <a name="metrics-resolution"></a>Rozwiązanie metryk

Metryki wybrane w obszarze **Diagnostyka** określają rozdzielczość metryk, które są dostępne dla Twojego konta:

* Funkcja monitorowania **zagregowanego** zapewnia takie metryki jak ruch przychodzący/wychodzący, dostępność, opóźnienie i procent sukcesu. Te metryki są agregowane z usług obiektów blob, tabel, plików i kolejek.
* **Na interfejs API** zapewnia bardziej precyzyjną rozdzielczość, z metrykami dostępnymi dla poszczególnych operacji magazynu, poza agregacjami na poziomie usług.

## <a name="configure-metrics-alerts"></a>Konfigurowanie alertów metryk

Można utworzyć alerty powiadamiające o osiągnięciu progów dla metryk zasobów magazynu.

1. Aby otworzyć **blok reguły alertów** , przewiń w dół do **sekcji monitorowanie** w **bloku menu** i wybierz pozycję **alerty (klasyczne)** .
2. Wybierz pozycję **Dodaj alert metryki (klasyczny)** , aby otworzyć blok **Dodawanie reguły alertu**
3. Wprowadź **nazwę** i **Opis** nowej reguły alertu.
4. Wybierz **metrykę** , dla której chcesz dodać alert, **warunek** alertu i **próg** . Typ jednostki progowej zmienia się w zależności od wybranej metryki. Na przykład "Count" jest typem jednostki dla *ContainerCount* , podczas gdy jednostka dla metryki *wzrost percentnetworkerror* jest wartością procentową.
5. Wybierz **okres** . Metryki osiągające lub przekroczenia wartości progowej w ramach okresu wyzwalają alert.
6. Obowiązkowe Konfigurowanie powiadomień **e-mail** i elementów **webhook** . Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Konfigurowanie elementu webhook w ramach alertu dotyczącego metryki platformy Azure](../../azure-monitor/platform/alerts-webhooks.md). Jeśli nie skonfigurujesz powiadomień dotyczących poczty e-mail lub elementu webhook, alerty będą wyświetlane tylko w Azure Portal.

!["Dodaj blok reguły alertu" w Azure Portal](./media/storage-monitor-storage-account/add-alert-rule.png)

## <a name="add-metrics-charts-to-the-portal-dashboard"></a>Dodawanie wykresów metryk do pulpitu nawigacyjnego portalu

Wykresy metryk usługi Azure Storage można dodać do pulpitu nawigacyjnego portalu na dowolnym koncie magazynu.

1. Wybierz pozycję **Edytuj pulpit nawigacyjny** podczas wyświetlania pulpitu nawigacyjnego w [Azure Portal](https://portal.azure.com).
1. W **galerii kafelków** wybierz pozycję **Znajdź kafelki według**  >  **typu** .
1. Wybierz pozycję **Typ**  >  **konta magazynu** .
1. W obszarze **zasoby** wybierz konto magazynu, którego metryki chcesz dodać do pulpitu nawigacyjnego.
1. Wybierz pozycję **Kategorie**  >  **monitorowanie** .
1. Przeciągnij i upuść kafelek wykresu na pulpicie nawigacyjnym dla metryki, która ma być wyświetlana. Powtórz te czynności dla wszystkich metryk, które mają być wyświetlane na pulpicie nawigacyjnym. Na poniższej ilustracji wykres "obiekty blob — łączna liczba żądań" został wyróżniony jako przykład, ale wszystkie wykresy są dostępne do umieszczenia na pulpicie nawigacyjnym.

   ![Galeria kafelków w Azure Portal](./media/storage-monitor-storage-account/storage-customize-dashboard.png)
1. Wybierz pozycję **gotowe Dostosowywanie** w górnej części pulpitu nawigacyjnego po zakończeniu dodawania wykresów.

Po dodaniu wykresów do pulpitu nawigacyjnego można je dodatkowo dostosować zgodnie z opisem w temacie dostosowywanie wykresów metryk.

## <a name="configure-logging"></a>Konfigurowanie rejestrowania

Można nakazać usłudze Azure Storage zapisywanie dzienników diagnostycznych dla żądań odczytu, zapisu i usuwania dla usług BLOB, tabel i kolejek. Ustawione zasady przechowywania danych dotyczą również tych dzienników.

> [!NOTE]
> Azure Files obecnie obsługuje metryki analityka magazynu, ale nie obsługuje jeszcze rejestrowania.
>

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **konta magazynu** , a następnie nazwę konta magazynu, aby otworzyć blok konto magazynu.
1. Wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** w sekcji **monitorowanie (klasyczne)** w bloku menu.

    ![Element menu Diagnostyka w obszarze monitorowanie w Azure Portal.](./media/storage-monitor-storage-account/storage-enable-metrics-00.png)

1. Upewnij się, że **stan** jest ustawiony na **włączone** , a następnie wybierz **usługi** , dla których chcesz włączyć rejestrowanie.

    ![Skonfiguruj rejestrowanie w Azure Portal.](./media/storage-monitor-storage-account/enable-diagnostics.png)
1. Kliknij pozycję **Zapisz** .

Dzienniki diagnostyczne są zapisywane w kontenerze obiektów BLOB o nazwie *$Logs* na koncie magazynu. Dane dziennika można wyświetlić za pomocą Eksploratora usługi Storage, takiego jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com), lub programowo przy użyciu biblioteki klienta usługi Storage lub programu PowerShell.

Aby uzyskać informacje o uzyskiwaniu dostępu do kontenera $logs, zobacz [Rejestrowanie analizy magazynu](storage-analytics-logging.md).

## <a name="next-steps"></a>Następne kroki

* Więcej informacji na temat [metryk, rejestrowania i rozliczeń](storage-analytics.md) można znaleźć w analityka magazynu.