---
title: Szacowanie kosztów planu zużycia w Azure Functions
description: Dowiedz się, jak lepiej oszacować koszty, które mogą być naliczane podczas uruchamiania aplikacji funkcji w planie zużycie na platformie Azure.
ms.date: 9/20/2019
ms.topic: conceptual
ms.openlocfilehash: 648be6325cce5bad36795b113c8bbccb3e21d37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774007"
---
# <a name="estimating-consumption-plan-costs"></a>Szacowanie kosztów planu zużycia

Obecnie istnieją trzy typy planów hostingu dla aplikacji uruchamianej w Azure Functions, przy użyciu których każdy plan ma własny model cenowy: 

| Planowanie | Opis |
| ---- | ----------- |
| [**Zużycie**](consumption-plan.md) | Opłaty są naliczane tylko za czas działania aplikacji funkcji. Ten plan obejmuje stronę [z cennikiem bezpłatnych][grantów] dla każdej subskrypcji.|
| [**Premium**](functions-premium-plan.md) | Zapewnia te same funkcje i mechanizm skalowania co plan Zużycie, ale z większą wydajnością i dostępem do sieci wirtualnej. Koszt jest oparty na wybranej warstwie cenowej. Aby dowiedzieć się więcej, [zobacz Azure Functions Premium.](functions-premium-plan.md) |
| [**Dedykowane (App Service)**](dedicated-plan.md) <br/>(warstwa Podstawowa lub wyższa) | Jeśli musisz działać na dedykowanych maszyn wirtualnych lub w izolacji, użyj obrazów niestandardowych lub chcesz użyć nadmiarowego planu App Service pojemności. Używa [regularnych rozliczeń App Service planu.](https://azure.microsoft.com/pricing/details/app-service/) Koszt jest oparty na wybranej warstwie cenowej.|

Wybrano plan, który najlepiej spełnia wymagania dotyczące wydajności i kosztów funkcji. Aby dowiedzieć się więcej, zobacz [Azure Functions skalowanie i hostowanie](functions-scale.md).

Ten artykuł dotyczy tylko planu Zużycie, ponieważ powoduje to zmienne koszty. Ten artykuł zawiera często zadawane pytania dotyczące rozliczeń kosztów [planu](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ) zużycia.

Durable Functions można również uruchomić w planie Zużycie. Aby dowiedzieć się więcej na temat kwestii dotyczących kosztów podczas korzystania z Durable Functions, zobacz [Durable Functions rozliczeń.](./durable/durable-functions-billing.md)

## <a name="consumption-plan-costs"></a>Koszty planu zużycia

Koszt wykonywania *pojedynczej* funkcji jest mierzony w *GB-sekundy.* Koszt wykonywania jest obliczany przez połączenie użycia pamięci z czasem wykonywania. Funkcja, która działa dłużej, kosztuje więcej, podobnie jak funkcja, która zużywa więcej pamięci. 

Rozważmy przypadek, w którym ilość pamięci używanej przez funkcję pozostaje stała. W tym przypadku obliczenie kosztu jest prostym mnożeniem. Załóżmy na przykład, że funkcja zużywała 0,5 GB przez 3 sekundy. Następnie koszt wykonania to `0.5GB * 3s = 1.5 GB-seconds` . 

Ponieważ użycie pamięci zmienia się w czasie, obliczenie jest zasadniczo integralną częścią użycia pamięci w czasie.  System robi to przez próbkowanie użycia pamięci przez proces (wraz z procesami podrzędnym) w regularnych odstępach czasu. Jak wspomniano na stronie [cennika,]użycie pamięci jest zaokrąglane w górę do najbliższego zasobnika o rozmiarze 128 MB. Jeśli proces używa 160 MB, opłata jest naliczana za 256 MB. Obliczenie uwzględnia współbieżność, czyli wiele współbieżnych wykonań funkcji w tym samym procesie.

> [!NOTE]
> Użycie procesora CPU nie jest bezpośrednio rozważane w kosztach wykonywania, ale może mieć wpływ na koszt, gdy ma wpływ na czas wykonywania funkcji.

W przypadku funkcji wyzwalanych przez protokół HTTP, gdy wystąpi błąd przed rozpoczęciem wykonywania kodu funkcji, nie są naliczane opłaty za wykonanie. Oznacza to, że odpowiedzi 401 z platformy z powodu weryfikacji klucza interfejsu API lub funkcji uwierzytelniania/autoryzacji usługi App Service nie są wliczane do kosztów wykonania. Analogicznie odpowiedzi kodu stanu 5xx nie są zliczane, gdy występują na platformie przed przetworzeniem żądania przez funkcję. Odpowiedź 5xx wygenerowana przez platformę po zakończeniu wykonywania kodu funkcji jest nadal liczona jako wykonanie, nawet jeśli błąd nie zostanie wygenerowany przez kod funkcji.

## <a name="other-related-costs"></a>Inne powiązane koszty

Podczas szacowania ogólnego kosztu działania funkcji w dowolnym planie należy pamiętać, że środowisko uruchomieniowe usługi Functions używa kilku innych usług platformy Azure, z których każda jest rozliczana oddzielnie. Podczas obliczania cen dla aplikacji funkcji wszystkie posiadane wyzwalacze i powiązania zintegrowane z innymi usługami platformy Azure wymagają utworzenia i zapłacenia za te dodatkowe usługi. 

W przypadku funkcji działających w planie Zużycie całkowity koszt to koszt wykonywania funkcji oraz koszt przepustowości i dodatkowych usług. 

Podczas szacowania ogólnych kosztów aplikacji funkcji i powiązanych usług użyj [kalkulatora cen platformy Azure.](https://azure.microsoft.com/pricing/calculator/?service=functions) 

| Powiązany koszt | Opis |
| ------------ | ----------- |
| **Konto magazynu** | Każda aplikacja funkcji wymaga skojarzonego konta Ogólnego przeznaczenia [Azure Storage,](../storage/common/storage-introduction.md#types-of-storage-accounts)które jest [rozliczane oddzielnie.](https://azure.microsoft.com/pricing/details/storage/) To konto jest używane wewnętrznie przez środowisko uruchomieniowe usługi Functions, ale można go również używać dla wyzwalaczy i powiązań usługi Storage. Jeśli nie masz konta magazynu, zostanie utworzone podczas tworzenia aplikacji funkcji. Aby dowiedzieć się więcej, zobacz [Wymagania dotyczące konta magazynu.](storage-considerations.md#storage-account-requirements)|
| **Application Insights** | Funkcje są [Application Insights,](../azure-monitor/app/app-insights-overview.md) aby zapewnić wysokowydajne środowisko monitorowania dla aplikacji funkcji. Chociaż nie jest to wymagane, należy [włączyć Application Insights integracji.](configure-monitoring.md#enable-application-insights-integration) Bezpłatne przyznawanie danych telemetrycznych jest uwzględniane co miesiąc. Aby dowiedzieć się więcej, [zobacz Azure Monitor cennika.](https://azure.microsoft.com/pricing/details/monitor/) |
| **Przepustowość sieci** | Nie płacisz za transfer danych między usługami platformy Azure w tym samym regionie. Można jednak ponieść koszty transferów danych wychodzących do innego regionu lub poza platformą Azure. Aby dowiedzieć się więcej, zobacz [Szczegóły cennika przepustowości.](https://azure.microsoft.com/pricing/details/bandwidth/) |

## <a name="behaviors-affecting-execution-time"></a>Zachowania wpływające na czas wykonywania

Następujące zachowania funkcji mogą mieć wpływ na czas wykonywania:

+ **Wyzwalacze i powiązania:** czas trwania odczytywania danych wejściowych z i zapisu danych wyjściowych w powiązaniach funkcji jest liczony jako czas wykonywania. [](functions-triggers-bindings.md) Jeśli na przykład funkcja używa powiązania wyjściowego do zapisu komunikatu w kolejce usługi Azure Storage, czas wykonywania obejmuje czas, który jest czas do zapisu komunikatu w kolejce, który jest uwzględniany podczas obliczania kosztu funkcji. 

+ **Wykonywanie asynchroniczne:** czas oczekiwania funkcji na wyniki żądania asynchronicznego (w języku `await` C#) jest liczony jako czas wykonywania. Obliczanie sekundy GB jest oparte na godzinie rozpoczęcia i zakończenia funkcji oraz użyciu pamięci w tym okresie. To, co dzieje się w tym czasie pod względem aktywności procesora CPU, nie jest uwzględniane w obliczeniach. Możesz zmniejszyć koszty podczas operacji asynchronicznych przy użyciu [Durable Functions](durable/durable-functions-overview.md). Nie są naliczane rachunki za czas spędzony w oczekiwaniu na funkcje orkiestratora.

## <a name="viewing-cost-related-data"></a>Wyświetlanie danych związanych z kosztami

Na [fakturze](../cost-management-billing/understand/download-azure-invoice.md)można wyświetlić powiązane z kosztami dane łącznej liczby wykonań — **funkcje** i czas wykonania **— funkcje** oraz rzeczywiste rozliczane koszty. Jednak te dane faktury są agregacją miesięczną dla ostatniego okresu faktury. 

### <a name="function-app-level-metrics"></a>Metryki na poziomie aplikacji funkcji

Aby lepiej zrozumieć wpływ funkcji na koszty, możesz użyć usługi Azure Monitor do wyświetlania metryk związanych z kosztami, które są obecnie generowane przez aplikacje funkcji. W celu uzyskania [tych Azure Monitor możesz](../azure-monitor/essentials/metrics-getting-started.md) użyć eksploratora metryk usługi [Azure Portal] lub interfejsów API REST.

#### <a name="monitor-metrics-explorer"></a>Eksplorator metryk monitorowania

Użyj [Azure Monitor metryk,](../azure-monitor/essentials/metrics-getting-started.md) aby wyświetlić dane związane z kosztami dla aplikacji funkcji planu Zużycie w formacie graficznym. 

1. W górnej części strony [Azure Portal] **wyszukiwania usług, zasobów** i dokumentów wyszukaj i wybierz pozycję `monitor` **Monitoruj** w obszarze **Usługi.**

1. Po lewej stronie wybierz **pozycję Metryki** Wybierz  >  **zasób, a** następnie użyj ustawień poniżej obrazu, aby wybrać aplikację funkcji.

    ![Wybieranie zasobu aplikacji funkcji](media/functions-consumption-costing/select-a-resource.png)

      
    |Ustawienie  |Sugerowana wartość  |Opis  |
    |---------|---------|---------|
    | Subskrypcja    |  Twoja subskrypcja  | Subskrypcja z aplikacją funkcji.  |
    | Grupa zasobów     | Twoja grupa zasobów  | Grupa zasobów zawierająca aplikację funkcji.   |
    | Typ zasobu     |  App Services | Aplikacje funkcji są wyświetlane jako App Services w monitorze. |
    | Zasób     |  Twoja aplikacja funkcji  | Aplikacja funkcji do monitorowania.        |

1. Wybierz **pozycję Zastosuj,** aby wybrać aplikację funkcji jako zasób do monitorowania.

1. W **metryce** wybierz **pozycję Liczba wykonań funkcji** i **Sumuj dla** **agregacji**. Powoduje to dodanie sumy liczby wykonań w wybranym okresie do wykresu.

    ![Definiowanie metryki aplikacji funkcji do dodania do wykresu](media/functions-consumption-costing/monitor-metrics-add-metric.png)

1. Wybierz **pozycję Dodaj metrykę** i powtórz kroki 2–4, aby dodać **jednostki wykonywania** funkcji do wykresu. 

Wynikowy wykres zawiera sumy dla obu metryk wykonania w wybranym zakresie czasu, czyli w tym przypadku dwie godziny.

![Wykres liczby wykonań funkcji i jednostek wykonywania](media/functions-consumption-costing/monitor-metrics-execution-sum.png)

Ponieważ liczba jednostek wykonywania jest znacznie większa niż liczba wykonań, wykres przedstawia tylko jednostki wykonania.

Ten wykres przedstawia łączną liczbę 1,11 mld zużytych w okresie dwóch godzin mierzonych w `Function Execution Units` MB-milisekundach. Aby przekonwertować na GB sekund, podziel wartość przez 1024000. W tym przykładzie aplikacja funkcji zużywała `1110000000 / 1024000 = 1083.98` GB sekund. Tę wartość można pomnożyć przez bieżącą cenę czasu wykonywania na stronie cennika usługi [Functions,][]która zapewnia koszt tych dwóch godzin przy założeniu, że zostały już użyte bezpłatne granty czasu wykonywania. 

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Interfejs [wiersza polecenia platformy Azure](/cli/azure/) ma polecenia służące do pobierania metryk. Interfejsu wiersza polecenia można używać z lokalnego środowiska poleceń lub bezpośrednio z portalu przy użyciu [Azure Cloud Shell](../cloud-shell/overview.md). Na przykład następujące polecenie [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) zwraca dane godzinowe w tym samym okresie, który był używany wcześniej.

Pamiętaj, aby zastąpić `<AZURE_SUBSCRIPTON_ID>` identyfikator subskrypcji platformy Azure, uruchamiając polecenie .

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
Ta określonej odpowiedzi pokazuje, że od do , aplikacja zużywa `2019-09-11T21:46` `2019-09-11T23:18` 1110000000 MB milisekund (1083,98 GB sekund).

### <a name="function-level-metrics"></a>Metryki na poziomie funkcji

Jednostki wykonywania funkcji są kombinacją czasu wykonywania i użycia pamięci, co sprawia, że jest to trudna metryka do zrozumienia użycia pamięci. Dane pamięci nie są obecnie metryką dostępną za pośrednictwem Azure Monitor. Jeśli jednak chcesz zoptymalizować użycie pamięci przez aplikację, możesz użyć danych licznika wydajności zebranych przez Application Insights.  

Jeśli jeszcze tego nie zrobiono, [włącz Application Insights w aplikacji funkcji.](configure-monitoring.md#enable-application-insights-integration) Po włączeniu tej integracji można zapytania [dotyczące tych danych telemetrycznych w portalu](analyze-telemetry-data.md#query-telemetry-data). 

[!INCLUDE [functions-consumption-metrics-queries](../../includes/functions-consumption-metrics-queries.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o monitorowaniu aplikacji funkcji](functions-monitoring.md)

[strona cennika]:https://azure.microsoft.com/pricing/details/functions/
[Witryna Azure Portal]: https://portal.azure.com
