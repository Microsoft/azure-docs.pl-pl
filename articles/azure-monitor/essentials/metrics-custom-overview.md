---
title: Metryki niestandardowe w Azure Monitor (wersja zapoznawcza)
description: Dowiedz się więcej na temat metryk niestandardowych Azure Monitor i sposobu ich modelowania.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/13/2021
ms.openlocfilehash: bd7f19df5eed87f2fb02af4b5f2577340bcbfd60
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812106"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Metryki niestandardowe w Azure Monitor (wersja zapoznawcza)

Podczas wdrażania zasobów i aplikacji na platformie Azure należy rozpocząć zbieranie danych telemetrycznych w celu uzyskania wglądu w ich wydajność i kondycję. Platforma Azure udostępnia pewne metryki od czasu do czasu. Te metryki są nazywane [standardową lub platformą](./metrics-supported.md). Mają one jednak ograniczony charakter. 

Aby uzyskać bardziej szczegółowe informacje, warto zebrać pewne niestandardowe wskaźniki wydajności lub metryki specyficzne dla firmy. Te **metryki** niestandardowe można zbierać za pośrednictwem telemetrii aplikacji, agenta uruchamianego w zasobach platformy Azure, a nawet zewnętrznego systemu monitorowania i przesyłanego bezpośrednio do Azure Monitor. Po ich opublikowaniu w usłudze Azure Monitor można przeglądać i tworzyć zapytania dotyczące metryk niestandardowych oraz aplikacji platformy Azure obok standardowych metryk emitowanych przez platformę Azure.

Azure Monitor metryki niestandardowe są obecnie dostępne w publicznej wersji zapoznawczej. 

## <a name="methods-to-send-custom-metrics"></a>Metody wysyłania metryk niestandardowych

Metryki niestandardowe można wysyłać do usługi Azure Monitor za pomocą kilku metod:
- Instrumentować aplikację przy użyciu zestawu Azure Application Insights SDK i wysyłać niestandardowe dane telemetryczne do Azure Monitor. 
- Zainstaluj agenta Azure Monitor (wersja zapoznawcza) na maszynie wirtualnej [](../agents/data-collection-rule-azure-monitor-agent.md) platformy Azure z systemem Windows lub [Linux](../agents/azure-monitor-agent-overview.md) i użyj reguły zbierania danych, aby wysyłać liczniki wydajności do Azure Monitor metryk.
- Zainstaluj rozszerzenie Windows Diagnostyka Azure (WAD) na maszynie wirtualnej platformy [Azure,](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)zestawie [](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) skalowania maszyn [wirtualnych,](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md) [klasycznej](../essentials/collect-custom-metrics-guestos-vm-classic.md)maszynie wirtualnej lub klasycznym Cloud Services i wyślij liczniki wydajności do usługi Azure Monitor. 
- Zainstaluj agenta [InfluxData Telegraf na](../essentials/collect-custom-metrics-linux-telegraf.md) maszynie wirtualnej z systemem Linux na platformie Azure i wyślij metryki przy Azure Monitor wtyczki danych wyjściowych.
- Wysyłanie metryk niestandardowych [bezpośrednio do interfejsu API REST Azure Monitor ,](./metrics-store-custom-rest-api.md) `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Model cen i przechowywanie

Sprawdź stronę [Azure Monitor cennika,](https://azure.microsoft.com/pricing/details/monitor/) aby uzyskać szczegółowe informacje na temat tego, kiedy będą włączone rozliczenia dla niestandardowych metryk i zapytań dotyczących metryk. Szczegółowe informacje o cenach dla wszystkich metryk, w tym metryk niestandardowych i zapytań dotyczących metryk, są dostępne na tej stronie. Podsumowując, pozyskanie metryk standardowych (metryk platformy) do magazynu metryk usługi Azure Monitor nie wiąże się z żadnym kosztem, ale metryki niestandardowe będą wiązać się z kosztami po wprowadzeniu ogólnej dostępności. Zapytania interfejsu API metryki naliczają koszty.

Metryki niestandardowe są przechowywane przez [ten sam czas co metryki platformy.](../essentials/data-platform-metrics.md#retention-of-metrics) 

> [!NOTE]  
> Metryki wysyłane do Azure Monitor za pośrednictwem Application Insights SDK są rozliczane jako pozysowane dane dziennika. Naliczane są dodatkowe opłaty za metryki tylko wtedy, Application Insights została wybrana funkcja [Włącz alerty](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) dotyczące wymiarów metryk niestandardowych. To pole wyboru wysyła dane do bazy Azure Monitor metryk przy użyciu interfejsu API metryk niestandardowych, aby umożliwić bardziej złożone alerty.  Dowiedz się więcej na [temat Application Insights cen](../app/pricing.md#pricing-model) i cen w Twoim [regionie.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Jak wysyłać metryki niestandardowe

Podczas wysyłania metryk niestandardowych do Azure Monitor każdy zgłaszany punkt danych lub wartość musi zawierać następujące informacje.

### <a name="authentication"></a>Authentication
Aby przesłać metryki niestandardowe do usługi Azure Monitor, jednostka, która przesyła metrykę, musi mieć prawidłowy token Azure Active Directory (Azure AD) w nagłówku elementu **bearer** żądania. Istnieje kilka obsługiwanych sposobów uzyskania prawidłowego tokenu bearer:
1. [Tożsamości zarządzane dla zasobów platformy Azure.](../../active-directory/managed-identities-azure-resources/overview.md) Przekazuje tożsamość do samego zasobu platformy Azure, takiego jak maszyna wirtualna. Tożsamość usługi zarządzanej (MSI) została zaprojektowana tak, aby nadać zasobom uprawnienia do wykonywania niektórych operacji. Przykładem jest umożliwienie zasobowi emitowania metryk dotyczących samego siebie. Zasobowi lub jego pliku MSI można przyznać uprawnienia **wydawcy metryk** monitorowania do innego zasobu. Za pomocą tego uprawnienia usługa MSI może również emitować metryki dla innych zasobów.
2. [Jednostkę usługi Azure AD.](../../active-directory/develop/app-objects-and-service-principals.md) W tym scenariuszu do aplikacji lub usługi Azure AD można przypisać uprawnienia do emitowania metryk dotyczących zasobu platformy Azure.
Aby uwierzytelnić żądanie, Azure Monitor zweryfikuje token aplikacji przy użyciu kluczy publicznych usługi Azure AD. Istniejąca **rola wydawcy metryk monitorowania** ma już to uprawnienie. Jest ona dostępna w Azure Portal. Jednostki usługi, w zależności od zasobów, dla których emituje metryki niestandardowe, można przysłać rolę wydawcy metryk monitorowania w wymaganym zakresie.  Przykłady to subskrypcja, grupa zasobów lub określony zasób.

> [!TIP]  
> Gdy żądasz tokenu usługi Azure AD w celu emitowania metryk niestandardowych, upewnij się, że odbiorcy lub zasób, dla których zażądano tokenu, to `https://monitoring.azure.com/` . Pamiętaj, aby dołączyć końcowe "/".

### <a name="subject"></a>Temat
Ta właściwość przechwytuje identyfikator zasobu platformy Azure, dla którego metryka niestandardowa jest zgłaszana. Te informacje zostaną zakodowane w adresie URL wywołania interfejsu API. Każdy interfejs API może przesyłać wartości metryk tylko dla pojedynczego zasobu platformy Azure.

> [!NOTE]  
> Nie można emitować metryk niestandardowych względem identyfikatora zasobu grupy zasobów lub subskrypcji.


### <a name="region"></a>Region (Region)
Ta właściwość przechwytuje region platformy Azure, w jakim jest wdrożony zasób emitowany metryki. Metryki muszą być emitowane do tego samego Azure Monitor punktu końcowego regionu, w których wdrożono zasób. Na przykład metryki niestandardowe dla maszyny wirtualnej wdrożonej w regionach Zachodnie stany USA muszą być wysyłane do regionalnego punktu końcowego Azure Monitor Zachodnie stany USA. Informacje o regionie są również kodowane w adresie URL wywołania interfejsu API.

> [!NOTE]  
> W publicznej wersji zapoznawczej metryki niestandardowe są dostępne tylko w podzestawie regionów platformy Azure. Lista obsługiwanych regionów jest udokumentowana w dalszej części tego artykułu.
>
>

### <a name="timestamp"></a>Znacznik czasu
Każdy punkt danych wysłany do Azure Monitor musi być oznaczony sygnaturą czasową. Ten znacznik czasu przechwytuje datę i godzina, o której wartość metryki jest mierzona lub zbierana. Azure Monitor przyjmuje dane metryk ze znacznikami czasu nawet 20 minut w przeszłości i 5 minut w przyszłości. Znacznik czasu musi mieć format ISO 8601.

### <a name="namespace"></a>Przestrzeń nazw
Przestrzenie nazw to sposób kategoryzowania lub grupowania podobnych metryk. Za pomocą przestrzeni nazw można uzyskać izolację między grupami metryk, które mogą zbierać różne szczegółowe informacje lub wskaźniki wydajności. Na przykład możesz mieć przestrzeń nazw o nazwie **contosomemorymetrics,** która śledzi metryki użycia pamięci, które profiluje aplikację. Inna przestrzeń nazw **o nazwie contosoapptransaction** może śledzić wszystkie metryki dotyczące transakcji użytkowników w aplikacji.

### <a name="name"></a>Nazwa
**Nazwa** to nazwa zgłaszanej metryki. Zazwyczaj nazwa jest wystarczająco opisowa, aby ułatwić zidentyfikowanie mierzonych danych. Przykładem jest metryka, która mierzy liczbę bajtów pamięci używanych na danej maszynie wirtualnej. Może mieć nazwę metryki, na **przykład Bajty pamięci w użyciu.**

### <a name="dimension-keys"></a>Klucze wymiarów
Wymiar to para klucz lub wartość, która pomaga opisać dodatkowe charakterystyki zbieranej metryki. Korzystając z dodatkowych cech, można zebrać więcej informacji o metryce, co pozwala uzyskać bardziej szczegółowe informacje. Na przykład metryka **Bajty** pamięci w użyciu może mieć klucz wymiaru o nazwie **Proces,** który przechwytuje, ile bajtów pamięci zużywa każdy proces na maszynie wirtualnej. Za pomocą tego klucza można filtrować metrykę, aby zobaczyć, ile procesów specyficznych dla pamięci używa lub zidentyfikować pięć najważniejszych procesów według użycia pamięci.
Wymiary są opcjonalne. Nie wszystkie metryki mogą mieć wymiary. Metryka niestandardowa może mieć maksymalnie 10 wymiarów.

### <a name="dimension-values"></a>Wartości wymiarów
Podczas raportowania punktu danych metryki dla każdego klucza wymiaru w zgłaszanej metryki istnieje odpowiadająca wartość wymiaru. Możesz na przykład zgłosić pamięć używaną przez firmę ContosoApp na maszynie wirtualnej:

* Nazwa metryki będzie mieć wartość **Bajty pamięci w parametrze Użyj**.
* Kluczem wymiaru będzie **Proces**.
* Wartością wymiaru będzie **ContosoApp.exe**.

Podczas publikowania wartości metryki można określić tylko jedną wartość wymiaru na klucz wymiaru. Jeśli zbierzemy takie samo wykorzystanie pamięci dla wielu procesów na maszynie wirtualnej, możesz zgłosić wiele wartości metryk dla tego znacznika czasu. Każda wartość metryki określa inną wartość wymiaru dla klucza **wymiaru** Proces.
Wymiary są opcjonalne. Nie wszystkie metryki mogą mieć wymiary. Jeśli wpis metryki definiuje klucze wymiarów, odpowiednie wartości wymiarów są obowiązkowe.

### <a name="metric-values"></a>Wartości metryk
Azure Monitor przechowuje wszystkie metryki w jednominutowych interwałach szczegółowości. Rozumiemy, że w ciągu danej minuty może być konieczne próbkowania metryki kilka razy. Na przykład na podstawie użycia procesora CPU. Lub może być konieczne pomiar dla wielu zdarzeń dyskretnych. Przykładem są opóźnienia transakcji logowania. Aby ograniczyć liczbę nieprzetworzonych wartości, które trzeba emitować i płacić za nie w Azure Monitor, możesz lokalnie wstępnie agregować i emitować wartości:

* **Min.**: minimalna zaobserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Maksimum:** maksymalna zaobserwowana wartość ze wszystkich próbek i pomiarów w ciągu minuty.
* **Suma:** Suma wszystkich obserwowanych wartości ze wszystkich prób i pomiarów w ciągu minuty.
* **Liczba:** liczba próbek i pomiarów w ciągu minuty.

Jeśli na przykład w ciągu danej minuty w aplikacji były cztery transakcje logowania, mierzone opóźnienia dla każdej z nich mogą być następujące:

|Transakcja 1|Transakcja 2|Transakcja 3|Transakcja 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Następnie wynikowa publikacja metryki w Azure Monitor będzie następująca:
* Min.: 4
* Maks.: 16
* Suma: 40
* Liczba: 4

Jeśli aplikacja nie może wstępnie agregować lokalnie i musi emitować każdą dyskretną próbkę lub zdarzenie natychmiast po pobraniu, możesz emitować nieprzetworzone wartości miary. Na przykład za każdym razem, gdy w aplikacji nastąpi transakcja logowania, należy opublikować metrykę, aby Azure Monitor tylko z jedną miarą. W przypadku transakcji logowania, która trwała 12 ms, publikacja metryki będzie następująca:
* Min. 12
* Maks.: 12
* Suma: 12
* Liczba: 1

Dzięki temu procesowi można emitować wiele wartości dla tej samej kombinacji metryki i wymiaru w ciągu danej minuty. Azure Monitor następnie pobiera wszystkie wartości pierwotne emitowane dla danej minuty i agreguje je razem.

### <a name="sample-custom-metric-publication"></a>Publikowanie przykładowej metryki niestandardowej
W poniższym przykładzie utworzysz metrykę niestandardową o nazwie **Bajty** pamięci w obszarze Użyj w obszarze przestrzeni nazw metryki **Profil** pamięci dla maszyny wirtualnej. Metryka ma jeden wymiar o nazwie **Proces**. Dla danego znacznika czasu emituj wartości metryk dla dwóch różnych procesów:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, rozszerzenie diagnostyki i agent InfluxData Telegraf są już skonfigurowane do emitowania wartości metryk względem poprawnego regionalnego punktu końcowego i przenoszenia wszystkich poprzednich właściwości w każdym z nich.
>
>

## <a name="custom-metric-definitions"></a>Niestandardowe definicje metryk
Nie ma potrzeby wstępnie zdefiniowanej metryki niestandardowej w Azure Monitor, zanim zostanie ona emitowana. Każdy opublikowany punkt danych metryki zawiera informacje o przestrzeni nazw, nazwie i wymiarze. Dlatego przy pierwszym emitowania metryki niestandardowej do Azure Monitor automatycznie tworzona jest definicja metryki. Ta definicja metryki jest następnie wykrywalna dla dowolnego zasobu, dla których metryka jest emitowana za pośrednictwem definicji metryk.

> [!NOTE]  
> Azure Monitor nie obsługuje jeszcze definiowania **jednostek** dla metryki niestandardowej.

## <a name="using-custom-metrics"></a>Korzystanie z metryk niestandardowych
Po przesłaniu metryk niestandardowych do usługi Azure Monitor można je przeglądać za pośrednictwem Azure Portal i za pośrednictwem interfejsów API REST Azure Monitor zapytań. Można również tworzyć dla nich alerty w celu powiadamiania o spełniania określonych warunków.

> [!NOTE]
> Aby wyświetlać metryki niestandardowe, musisz być czytelnikiem lub współautorem. Zobacz [Czytelnik monitorowania](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Przeglądaj metryki niestandardowe za pomocą Azure Portal
1.    Przejdź do witryny [Azure Portal](https://portal.azure.com).
2.    Wybierz okienko **Monitorowanie.**
3.    Wybierz pozycję **Metryki**.
4.    Wybierz zasób, względem który zostały emitowane metryki niestandardowe.
5.    Wybierz przestrzeń nazw metryk dla metryki niestandardowej.
6.    Wybierz metrykę niestandardową.

> [!NOTE]
> Aby uzyskać więcej informacji na temat wyświetlania metryk w Azure Portal, zobacz Getting started with Azure Eksplorator metryk (Wprowadzenie do usługi [Azure Eksplorator metryk).](./metrics-getting-started.md)

## <a name="supported-regions"></a>Obsługiwane regiony
W publicznej wersji zapoznawczej możliwość publikowania metryk niestandardowych jest dostępna tylko w podzestawie regionów platformy Azure. To ograniczenie oznacza, że metryki mogą być publikowane tylko dla zasobów w jednym z obsługiwanych regionów. Aby uzyskać więcej informacji na temat regionów świadczenia usługi Azure, zobacz Obszary geograficzne platformy [Azure.](https://azure.microsoft.com/global-infrastructure/geographies/) Kod regionu platformy Azure używany w poniższych punktach końcowych to tylko nazwa regionu z usuniętymi odstępami W poniższej tabeli wymieniono zestaw obsługiwanych regionów platformy Azure dla metryk niestandardowych. Zawiera on również listę odpowiednich punktów końcowych, w których powinny zostać opublikowane metryki dla zasobów w tych regionach:

|Region platformy Azure |Prefiks regionalnego punktu końcowego|
|---|---|
| Wszystkie regiony chmury publicznej | https://<azure_region_code>.monitoring.azure.com |
| **Azure Government** | |
| US Gov Arizona | https: \/ /usgovarizona.monitoring.azure.us |
| **Chiny** | |
| Chiny Wschodnie 2 | https: \/ /chinaeast2.monitoring.azure.cn |

## <a name="latency-and-storage-retention"></a>Opóźnienie i przechowywanie magazynu

Dodanie zupełnie nowej metryki lub nowego wymiaru dodawanego do metryki może potrwać od 2 do 3 minut. W systemie dane powinny być wyświetlane w mniej niż 30 sekundach przez 99% czasu. 

Jeśli usuniesz metrykę lub wymiar, usunięcie zmiany z systemu może potrwać tydzień lub miesiąc.

## <a name="quotas-and-limits"></a>Limity przydziału i ograniczenia
Azure Monitor nakłada następujące limity użycia na metryki niestandardowe:

|Kategoria|Limit|
|---|---|
|Aktywne szeregi czasowe/subskrypcje/region|50 000|
|Klucze wymiarów na metrykę|10|
|Długość ciągu dla przestrzeni nazw metryk, nazw metryk, kluczy wymiarów i wartości wymiarów|256 znaków|

Aktywny szereg czasowy jest definiowany jako dowolna unikatowa kombinacja metryki, klucza wymiaru lub wartości wymiaru, która ma wartości metryk opublikowane w ciągu ostatnich 12 godzin.

Aby zrozumieć limit 50 000 szeregów czasowych, rozważ następującą metrykę:

*Czas odpowiedzi serwera z* wymiarami: *Region,* *Dział,* *CustomerID*

Dzięki tej metryce, jeśli masz 10 regionów, 20 działów i 100 klientów, którzy dają Ci szeregi czasowe 10 x 20 x 100 = 2000. 

Jeśli masz 100 regionów, 200 działów i 2000 klientów 100 x 200 x 2000 = 40 000 000 szeregów czasowych, czyli znacznie ponad limit tylko dla tej metryki. 

Ponownie ten limit nie dotyczy poszczególnych metryk. Jest to suma wszystkich takich metryk w ramach subskrypcji i regionu.  

## <a name="design-limitations-and-considerations"></a>Ograniczenia i zagadnienia dotyczące projektowania

**Nie używaj Application Insights na** potrzeby inspekcji — potok telemetrii usługi Application Insights jest zoptymalizowany pod kątem minimalizowania wpływu na wydajność i ograniczania ruchu sieciowego z monitorowania aplikacji. W związku z tym ogranicza lub próbki (pobiera tylko procent telemetrii i ignoruje pozostałe), jeśli początkowy zestaw danych staje się zbyt duży. Z powodu tego zachowania nie można używać go do celów inspekcji, ponieważ niektóre rekordy mogą zostać porzucone. 

**Metryki ze zmienną w** nazwie — nie używaj zmiennej jako części nazwy metryki, zamiast tego użyj stałej. Za każdym razem, gdy zmienna zmienia swoją wartość, Azure Monitor wygeneruje nową metrykę, szybko przekroczy limity liczby metryk. Ogólnie rzecz biorąc, gdy deweloperzy chcą uwzględnić zmienną w nazwie metryki, tak naprawdę chcą śledzić wiele zakresów czasu w ramach jednej metryki i powinni używać wymiarów zamiast nazw metryk zmiennych. 

**Wymiary metryk o wysokiej** kardynalności — metryki ze zbyt dużą prawidłową wartością w wymiarze ("wysoka kardynalność") są znacznie bardziej prawdopodobne do przekroowania limitu 50 000. Ogólnie rzecz biorąc, nigdy nie należy używać stale zmieniającej się wartości w nazwie wymiaru lub metryki. Na przykład znacznik czasu NIGDY nie powinien być wymiarem. Można użyć serwera, klienta lub produktu productid, ale tylko wtedy, gdy liczba tych typów jest mniejsza. Jako test zadaj sobie pytanie, czy kiedykolwiek będziesz grafować takie dane na grafie.  Jeśli masz 10 lub nawet 100 serwerów, warto zobaczyć je wszystkie na wykresie do porównania. Jednak jeśli masz 1000, wynikowy wykres będzie prawdopodobnie trudny, jeśli nie niemożliwy do odczytania. Najlepszym rozwiązaniem jest zachowanie do mniejszej do 100 prawidłowych wartości. Maksymalnie 300 to szary obszar.  Jeśli musisz przećlić tę kwotę, użyj Azure Monitor dzienników niestandardowych.   

Jeśli w nazwie lub wymiarze o wysokiej kardynalności znajduje się zmienna, mogą wystąpić następujące zdarzenia:
- Metryki stają się zawodne z powodu ograniczania przepustowości
- Eksplorator metryk nie działa
- Alerty i powiadomienia stają się nieprzewidywalne
- Koszty mogą nieoczekiwanie wzrosnąć — firma Microsoft nie jest obciążana opłatami, gdy metryki niestandardowe z wymiarami są w publicznej wersji zapoznawczej. Jednak po rozpoczęciu opłat w przyszłości zostaną naliczone nieoczekiwane opłaty. Planem jest naliczanie opłat za zużycie metryk na podstawie liczby monitorowanych szeregów czasowych i liczby wywołań interfejsu API.  

## <a name="next-steps"></a>Następne kroki
Użyj metryk niestandardowych z różnych usług: 
 - [Virtual Machines](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Zestaw skalowania maszyn wirtualnych](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Azure Virtual Machines (klasyczna)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Maszyna wirtualna z systemem Linux przy użyciu agenta programu Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [Interfejs API REST](./metrics-store-custom-rest-api.md)
 - [Wersja Cloud Services](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)
