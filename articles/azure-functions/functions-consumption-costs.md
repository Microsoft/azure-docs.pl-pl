---
title: Szacowanie kosztów planu zużycia w Azure Functions
description: Dowiedz się, jak lepiej oszacować koszty, które mogą zostać naliczone podczas uruchamiania aplikacji funkcji w planie zużycia na platformie Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 4967e0ff79a638891da4f784cf2f5f1ca4ddfe51
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578563"
---
# <a name="estimating-consumption-plan-costs"></a>Szacowanie kosztów planu zużycia

Obecnie istnieją trzy typy planów hostingu dla aplikacji, która działa w Azure Functions, przy czym każdy plan ma własny model cen: 

| Planowanie | Opis |
| ---- | ----------- |
| [**Zużycie**](consumption-plan.md) | Opłata jest naliczana tylko za czas, w którym działa aplikacja funkcji. Ten plan obejmuje [bezpłatną][stronę cenową] dotacji dla każdej subskrypcji.|
| [**Premium**](functions-premium-plan.md) | Zapewnia te same funkcje i mechanizm skalowania co plan zużycia, ale z ulepszoną wydajnością i dostępem do sieci wirtualnej. Koszt jest określany na podstawie wybranej warstwy cenowej. Aby dowiedzieć się więcej, zobacz [Azure Functions plan Premium](functions-premium-plan.md). |
| [**Dedykowane (App Service)**](dedicated-plan.md) <br/>(warstwa podstawowa lub wyższa) | Jeśli musisz uruchomić program na dedykowanych maszynach wirtualnych lub w izolacji, użyj niestandardowych obrazów lub chcesz użyć nadmiernej pojemności planu App Service. Stosuje [regularne rozliczanie planu App Service](https://azure.microsoft.com/pricing/details/app-service/). Koszt jest określany na podstawie wybranej warstwy cenowej.|

Wybrano plan, który najlepiej obsługuje wymagania dotyczące wydajności i kosztów. Aby dowiedzieć się więcej, zobacz [Azure Functions skalowanie i hosting](functions-scale.md).

Ten artykuł dotyczy tylko planu zużycia, ponieważ ten plan skutkuje zmiennymi kosztami. Ten artykuł zastępuje artykuł dotyczący [rozliczeń kosztów planu zużycia](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) .

Durable Functions można również uruchomić w planie zużycia. Aby dowiedzieć się więcej na temat zagadnień dotyczących kosztów podczas korzystania z Durable Functions, zobacz [Durable Functions rozliczenia](./durable/durable-functions-billing.md).

## <a name="consumption-plan-costs"></a>Koszty planu zużycia

*Koszt* wykonania pojedynczego wykonania funkcji jest mierzony w *GB-sekund*. Koszt wykonywania jest obliczany przez połączenie jego użycia pamięci z jego czasem wykonywania. Funkcja, która działa dłużej o dłuższy koszt, tak jak funkcja, która zużywa więcej pamięci. 

Rozważ przypadek, w którym ilość pamięci używanej przez funkcję pozostaje stała. W takim przypadku Obliczanie kosztów jest prostą liczebnością. Załóżmy na przykład, że funkcja zużywa 0,5 GB przez 3 sekundy. Następnie koszt wykonania wynosi `0.5GB * 3s = 1.5 GB-seconds` . 

Ponieważ użycie pamięci zmienia się w czasie, obliczenia jest zasadniczo całkowitą ilością użycia pamięci w czasie.  System wykonuje to obliczenie przez próbkowanie użycia pamięci przez proces (wraz z procesami podrzędnymi) w regularnych odstępach czasu. Jak wspomniano na [stronie cennika], użycie pamięci jest zaokrąglane do najbliższego zasobnika 128 MB. Gdy proces korzysta z 160 MB, naliczana jest opłata za 256 MB. Obliczenia uwzględniają współbieżność konta, czyli wiele współbieżnych wykonań funkcji w tym samym procesie.

> [!NOTE]
> Chociaż użycie procesora CPU nie jest brane pod uwagę bezpośrednio w kosztach wykonania, może mieć wpływ na koszt, gdy ma to wpływ na czas wykonywania funkcji.

W przypadku funkcji wyzwalanej przez protokół HTTP w przypadku wystąpienia błędu przed rozpoczęciem wykonywania kodu funkcji nie jest naliczana opłata za wykonanie. Oznacza to, że 401 odpowiedzi z platformy ze względu na weryfikację klucza interfejsu API lub funkcję uwierzytelniania App Service/autoryzacji nie są wliczane do kosztów wykonania. Podobnie odpowiedzi kodu stanu 5xx nie są zliczane, gdy występują na platformie przed przetworzeniem żądania. Odpowiedź 5xx wygenerowana przez platformę po rozpoczęciu wykonywania kodu funkcji jest nadal liczona jako wykonanie, nawet jeśli błąd nie został zgłoszony przez kod funkcji.

## <a name="other-related-costs"></a>Inne powiązane koszty

Podczas szacowania całkowitego kosztu uruchamiania funkcji w dowolnym planie należy pamiętać, że środowisko uruchomieniowe funkcji korzysta z kilku innych usług platformy Azure, które są rozliczane osobno. Przy obliczaniu cen aplikacji funkcji wszystkie wyzwalacze i powiązania, które integrują się z innymi usługami platformy Azure, wymagają utworzenia i uregulowania tych dodatkowych usług. 

W przypadku funkcji działających w ramach planu zużycia łączny koszt to koszt wykonywania funkcji, a także koszt przepustowości i dodatkowych usług. 

Podczas szacowania ogólnych kosztów aplikacji funkcji i powiązanych usług Użyj [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=functions). 

| Koszt pokrewny | Opis |
| ------------ | ----------- |
| **Konto magazynu** | Każda aplikacja funkcji wymaga, aby masz skojarzone [konto usługi Azure Storage](../storage/common/storage-introduction.md#types-of-storage-accounts)ogólnego przeznaczenia, które jest [rozliczane osobno](https://azure.microsoft.com/pricing/details/storage/). To konto jest używane wewnętrznie przez środowisko uruchomieniowe funkcji, ale można go również użyć dla wyzwalaczy i powiązań magazynu. Jeśli nie masz konta magazynu, po utworzeniu aplikacji funkcji jest tworzona jedna z nich. Aby dowiedzieć się więcej, zobacz [wymagania dotyczące konta magazynu](storage-considerations.md#storage-account-requirements).|
| **Application Insights** | Funkcje programu opierają się na [Application Insights](../azure-monitor/app/app-insights-overview.md) , aby zapewnić środowisko monitorowania o wysokiej wydajności dla aplikacji funkcji. Chociaż nie jest to wymagane, należy [włączyć integrację Application Insights](configure-monitoring.md#enable-application-insights-integration). Każdy miesiąc obejmuje bezpłatne przyznawanie danych telemetrycznych. Aby dowiedzieć się więcej, zobacz [stronę z cennikiem Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). |
| **Przepustowość sieci** | Nie płacisz za transfer danych między usługami platformy Azure w tym samym regionie. Można jednak nanieść koszty transferów danych wychodzących do innego regionu lub poza platformą Azure. Aby dowiedzieć się więcej, zobacz [szczegóły cennika dotyczącego przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/). |

## <a name="behaviors-affecting-execution-time"></a>Zachowania mające wpływ na czas wykonywania

Następujące zachowania funkcji mogą mieć wpływ na czas wykonywania:

+ **Wyzwalacze i powiązania**: czas potrzebny do odczytu danych wejściowych z i zapisu danych wyjściowych do [powiązań funkcji](functions-triggers-bindings.md) jest liczony jako czas wykonywania. Na przykład, gdy funkcja używa powiązania danych wyjściowych do zapisywania komunikatu w kolejce usługi Azure Storage, czas wykonywania obejmuje czas potrzebny do zapisania komunikatu w kolejce, który jest uwzględniany w obliczaniu kosztu funkcji. 

+ **Wykonywanie asynchroniczne**: czas oczekiwania funkcji na wyniki żądania asynchronicznego ( `await` w języku C#) jest liczony jako czas wykonywania. Obliczenia GB i s są oparte na godzinie rozpoczęcia i zakończenia funkcji oraz użycia pamięci w tym okresie. Co dzieje się w tym czasie w odniesieniu do działania procesora CPU nie jest uwzględniane w obliczeniach. Przy użyciu [Durable Functions](durable/durable-functions-overview.md)można obniżyć koszty podczas operacji asynchronicznych. Za czas spędzony w funkcjach programu Orchestrator nie są naliczane opłaty.

## <a name="viewing-cost-related-data"></a>Wyświetlanie danych związanych z kosztami

Na [fakturze](../cost-management-billing/understand/download-azure-invoice.md)można wyświetlić dane związane z kosztami **całkowitych wykonań — funkcje** i **czas wykonywania**, a także rzeczywiste koszty rozliczane. Te dane faktury są jednak miesięczną sumą dla przeszłego okresu faktury. 

### <a name="function-app-level-metrics"></a>Metryki na poziomie aplikacji funkcji

Aby lepiej zrozumieć wpływ kosztów funkcji, możesz użyć Azure Monitor, aby wyświetlić metryki związane z kosztami, które są obecnie generowane przez aplikacje funkcji. Aby uzyskać te dane, można użyć narzędzia [Azure monitor Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) w [Azure Portal] lub interfejsie API REST.

#### <a name="monitor-metrics-explorer"></a>Monitorowanie Eksploratora metryk

Użyj [Eksploratora metryk Azure monitor](../azure-monitor/essentials/metrics-getting-started.md) , aby wyświetlić dane dotyczące kosztów dla aplikacji funkcji planu zużycia w formacie graficznym. 

1. W górnej części [Azure Portal] w **usługach wyszukiwania, zasobach i witrynie docs**  Wyszukaj `monitor` i wybierz pozycję **monitor** w obszarze **usługi**.

1. Po lewej stronie wybierz pozycję **metryki**  >  **Wybierz zasób**, a następnie użyj ustawień poniżej obrazu, aby wybrać aplikację funkcji.

    ![Wybierz zasób aplikacji funkcji](media/functions-consumption-costing/select-a-resource.png)

      
    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    | Subskrypcja    |  Twoja subskrypcja  | Subskrypcja z aplikacją funkcji.  |
    | Grupa zasobów     | Twoja grupa zasobów  | Grupa zasobów zawierająca aplikację funkcji.   |
    | Typ zasobu     |  App Services | Aplikacje funkcji są wyświetlane jako wystąpienia App Services w monitorze. |
    | Zasób     |  Aplikacja funkcji  | Aplikacja funkcji do monitorowania.        |

1. Wybierz pozycję **Zastosuj** , aby wybrać aplikację funkcji jako zasób do monitorowania.

1. Z **metryki** wybierz kolejno pozycje **wykonywanie funkcji liczba** i **Suma** dla **agregacji**. Spowoduje to dodanie sumy liczby wykonań w wybranym okresie do wykresu.

    ![Zdefiniuj metrykę aplikacji funkcji, która ma zostać dodana do wykresu](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Wybierz pozycję **Dodaj metrykę** i powtórz kroki 2-4, aby dodać **jednostki wykonywania funkcji** do wykresu. 

Wykres otrzymany zawiera sumy dla obu metryk wykonywania w wybranym zakresie czasu, co w tym przypadku jest dwie godziny.

![Wykres liczników wykonywania funkcji i jednostek wykonywania](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Ponieważ liczba jednostek wykonywania jest znacznie większa niż Liczba wykonań, wykres pokazuje tylko jednostki wykonywania.

Ten wykres przedstawia łączną liczbę 1 110 000 000 `Function Execution Units` zużytych w ciągu dwóch godzin, mierzoną w megabajtach (MB). Aby przekonwertować na GB sekund, Podziel na 1024000. W tym przykładzie aplikacja funkcji wykorzystana `1110000000 / 1024000 = 1083.98` GB-sekund. Możesz posłużyć się tą wartością i pomnożyć przez bieżącą cenę czasu wykonywania na[stronie]cennika [funkcji Functions], która zapewnia koszt tych dwóch godzin, przy założeniu, że już użyto bezpłatnych zasiłków czasu wykonywania. 

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

[Interfejs wiersza polecenia platformy Azure](/cli/azure/) zawiera polecenie do pobierania metryk. Interfejsu wiersza polecenia można użyć z lokalnego środowiska poleceń lub bezpośrednio z portalu przy użyciu [Azure Cloud Shell](../cloud-shell/overview.md). Na przykład następujące polecenie [AZ monitor Metric list](/cli/azure/monitor/metrics#az-monitor-metrics-list) zwraca dane godzinowe w tym samym okresie użytym wcześniej.

Pamiętaj, aby zastąpić `<AZURE_SUBSCRIPTON_ID>` identyfikatorem subskrypcji platformy Azure, na którym działa polecenie.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

To polecenie zwraca ładunek JSON, który wygląda podobnie do następującego przykładu:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Ta konkretna odpowiedź pokazuje, że w przypadku `2019-09-11T21:46` `2019-09-11T23:18` aplikacji wykorzystano 1110000000 MB-milisekund (1083,98 GB-s).

### <a name="function-level-metrics"></a>Metryki na poziomie funkcji

Jednostki wykonywania funkcji są kombinacją czasu wykonywania i użycia pamięci, co sprawia, że jest trudną metryką do poznania użycia pamięci. Dane pamięci nie są obecnie dostępne w Azure Monitor. Jeśli jednak chcesz zoptymalizować użycie pamięci przez aplikację, można użyć danych licznika wydajności zebranych przez Application Insights.  

Jeśli jeszcze tego nie zrobiono, [włącz Application Insights w aplikacji funkcji](configure-monitoring.md#enable-application-insights-integration). Po włączeniu tej integracji można [wysyłać zapytania o dane telemetryczne w portalu](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o monitorowaniu aplikacji funkcji](functions-monitoring.md)

[Strona cennika]:https://azure.microsoft.com/pricing/details/functions/
[Witryna Azure Portal]: https://portal.azure.com
