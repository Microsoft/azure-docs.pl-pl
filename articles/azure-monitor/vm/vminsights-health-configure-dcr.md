---
title: Konfigurowanie monitorowania w usłudze VM Insights gość przy użyciu reguł zbierania danych (wersja zapoznawcza)
description: Opisuje sposób modyfikowania domyślnego monitorowania kondycji gościa usługi VM Insights na dużą skalę przy użyciu szablonów Menedżer zasobów.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 0db6ed7566c53429f8b9798ac8cdafe76ca7bd5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102052147"
---
# <a name="configure-monitoring-in-vm-insights-guest-health-using-data-collection-rules-preview"></a>Konfigurowanie monitorowania w usłudze VM Insights gość przy użyciu reguł zbierania danych (wersja zapoznawcza)
[Kondycja gościa usługi VM Insights](vminsights-health-overview.md) umożliwia wyświetlenie informacji o kondycji maszyny wirtualnej zdefiniowanej przez zestaw pomiarów wydajności, które są próbkowane w regularnych odstępach czasu. W tym artykule opisano, jak można modyfikować domyślne monitorowanie wielu maszyn wirtualnych przy użyciu reguł zbierania danych.


## <a name="monitors"></a>Monitory
Stan kondycji maszyny wirtualnej jest określany przez [zestawienie kondycji](vminsights-health-overview.md#health-rollup-policy) z poszczególnych monitorów. Istnieją dwa typy monitorów w obszarze kondycja gościa usługi VM Insights, jak pokazano w poniższej tabeli.

| Monitor | Opis |
|:---|:---|
| Monitor jednostkowy | Mierzy pewien aspekt zasobu lub aplikacji. Może to spowodować sprawdzenie licznika wydajności w celu określenia wydajności zasobu lub jego dostępności. |
| Monitor agregacji | Grupuje wiele monitorów, aby zapewnić pojedynczy zagregowany stan kondycji. Monitor agregacji może zawierać co najmniej jeden monitor jednostkowy i inne monitory agregacji. |

Nie można bezpośrednio zmienić zestawu monitorów używanych przez kondycję gościa usługi VM Insights i ich konfiguracji. Można utworzyć [zastąpienia](#overrides) , chociaż modyfikując zachowanie konfiguracji domyślnej. Zastąpienia są zdefiniowane w regułach zbierania danych. Można utworzyć wiele reguł zbierania danych zawierających wiele zastąpień, aby osiągnąć wymaganą konfigurację monitorowania.

## <a name="monitor-properties"></a>Właściwości monitora
W poniższej tabeli opisano właściwości, które można skonfigurować na każdym monitorze.

| Właściwość | Monitory | Opis |
|:---|:---|:---|
| Enabled (Włączony) | Agregacja<br>Jednostka | W przypadku wartości true Monitor stanu jest obliczany i przyczynia się do kondycji maszyny wirtualnej. Może wyzwolić alert o włączeniu alertów. |
| Generowanie alertów | Agregacja<br>Jednostka | W przypadku wartości true alert jest wyzwalany dla monitora po przejściu w stan złej kondycji. W przypadku wartości false stan monitora będzie nadal współtworzyć kondycję maszyny wirtualnej, która może wyzwolić alert. |
| Ostrzeżenie | Jednostka | Kryteria dla stanu ostrzegawczego. Jeśli nie, wówczas monitor nigdy nie wprowadzi stanu ostrzegawczego. |
| Krytyczne | Jednostka | Kryteria stanu krytycznego. Jeśli nie, wówczas monitor nigdy nie przejdzie do stanu krytycznego. |
| Częstotliwość oceny | Jednostka | Częstotliwość oceny kondycji. |
| Lookback | Jednostka | Rozmiar okna lookback w sekundach. Zobacz [element monitorConfiguration](#monitorconfiguration-element) , aby uzyskać szczegółowy opis. |
| Typ oceny | Jednostka | Określa wartość, która ma zostać użyta z zestawu próbek. Zobacz [element monitorConfiguration](#monitorconfiguration-element) , aby uzyskać szczegółowy opis. |
| Minimalny przykład | Jednostka | Minimalna liczba wartości do użycia w celu obliczenia wartości. |
| Maksymalna liczba próbek | Jednostka | Maksymalna liczba wartości do użycia w celu obliczenia wartości. |


## <a name="default-configuration"></a>Konfiguracja domyślna
W poniższej tabeli wymieniono konfigurację domyślną dla każdego monitora. Tej konfiguracji domyślnej nie można zmienić bezpośrednio, ale można zdefiniować [zastąpienia](#overrides) modyfikujące konfigurację monitora dla niektórych maszyn wirtualnych.


| Monitor | Enabled (Włączony) | Generowanie alertów | Ostrzeżenie | Krytyczne | Częstotliwość oceny | Lookback | Typ oceny | Minimalny przykład | Maksymalna liczba próbek |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| Wykorzystanie procesora  | Prawda | Fałsz | Brak | \> 90%    | 60 sek. | 240 s | Min | 2 | 3 |
| Dostępna pamięć | Prawda | Fałsz | Brak | \< 100 MB | 60 sek. | 240 s | Maks. | 2 | 3 |
| System plików      | Prawda | Fałsz | Brak | \< 100 MB | 60 sek. | 120 s | Maks. | 1 | 1 |


## <a name="overrides"></a>Przesłonięcia
*Zastąpienie* zmienia jedną rudy więcej właściwości monitora. Na przykład przesłonięcie mogło wyłączyć monitor, który jest domyślnie włączony, zdefiniować kryteria ostrzegawcze dla monitora lub zmodyfikować Próg krytyczny monitora. 

Zastąpienia są zdefiniowane w [regule zbierania danych (DCR)](../agents/data-collection-rule-overview.md). Można utworzyć wiele DCR z różnymi zestawami zastąpień i zastosować je do wielu maszyn wirtualnych. Do maszyny wirtualnej stosuje się DCR, tworząc skojarzenie zgodnie z opisem w temacie [Konfigurowanie zbierania danych dla agenta Azure monitor (wersja zapoznawcza)](../agents/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations).


## <a name="multiple-overrides"></a>Wiele zastąpień

Jeden monitor może mieć wiele zastąpień. Jeśli zastąpień definiują różne właściwości, wówczas wynikiem konfiguracji jest kombinacja wszystkich zastąpień.

Na przykład monitor nie `memory|available` określa progu ostrzeżenia ani domyślnie włącza alertów. Należy wziąć pod uwagę następujące zastąpienia zastosowane do tego monitora:

- Zastąpienie 1 definiuje `alertConfiguration.isEnabled` wartość właściwości jako `true`
- Przesłonięcie 2 definiuje `monitorConfiguration.warningCondition` z warunkiem progu `< 250` .

Utworzona konfiguracja będzie monitorem, który przechodzi w stan kondycji ostrzegawczej, gdy jest dostępna mniej niż 250 MB pamięci, i tworzy alert o ważności 2, a także ma krytyczny stan kondycji, gdy jest dostępna mniej niż 100 MB dostępnej pamięci i tworzy alert o ważności 1 (lub zmienia istniejący alert z ważności od 2 do 1), jeśli już istnieje).

Jeśli dwa zastąpień definiują tę samą właściwość na tym samym monitorze, pierwszeństwo ma jedna wartość. Zastąpienia zostaną zastosowane w zależności od ich [zakresu](#scopes-element), od najbardziej ogólnego do najbardziej szczegółowego. Oznacza to, że większość konkretnych zastąpień będzie miała największą szansę stosowania. Określona kolejność jest następująca:

1. Globalnie 
2. Subskrypcja
3. Grupa zasobów
4. Maszyna wirtualna. 

Jeśli wiele zastąpień na tym samym poziomie zakresu definiuje tę samą właściwość na tym samym monitorze, są one stosowane w kolejności, w której występują w DCR. Jeśli przesłonięcia znajdują się w różnych DCR, są one stosowane w kolejności alfabetycznej identyfikatorów zasobów DCR.


## <a name="data-collection-rule-configuration"></a>Konfiguracja reguły zbierania danych
Elementy JSON w regule zbierania danych definiujące przesłonięcia są opisane w poniższych sekcjach. Pełny przykład jest dostępny w ramach [przykładowej reguły zbierania danych](#sample-data-collection-rule).

## <a name="extensions-structure"></a>Struktura rozszerzeń
Kondycja gościa jest implementowana jako rozszerzenie agenta Azure Monitor, więc zastąpienia są definiowane w `extensions` elemencie reguły zbierania danych. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Element | Wymagane | Opis |
|:---|:---|:---|
| `name` | Tak | Ciąg zdefiniowany przez użytkownika dla rozszerzenia. |
| `streams` | Tak | Lista strumieni, do których będą wysyłane dane kondycji gościa. Musi on obejmować **firmę Microsoft-HealthStateChange**.  |
| `extensionName` | Tak | Nazwa rozszerzenia. Musi to być **HealthExtension**. |
| `extensionSettings` | Tak | Tablica `healthRuleOverride` elementów, która ma zostać zastosowana do konfiguracji domyślnej. |


## <a name="extensionsettings-element"></a>Poprawny ExtensionSettings, element
Zawiera ustawienia dla rozszerzenia.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Element | Wymagane | Opis |
|:---|:---|:---|
| `schemaVersion` | Tak | Ciąg zdefiniowany przez firmę Microsoft, który reprezentuje oczekiwany schemat elementu. Obecnie musi być ustawiona na 1,0 |
| `contentVersion` | Nie | Ciąg zdefiniowany przez użytkownika, aby śledzić różne wersje konfiguracji kondycji, jeśli jest to wymagane. |
| `healthRuleOverrides` | Tak | Tablica `healthRuleOverride` elementów, która ma zostać zastosowana do konfiguracji domyślnej. |

## <a name="healthrulesoverrides-element"></a>healthRulesOverrides, element
Zawiera co najmniej jeden `healthRuleOverride` element, który definiuje przesłonięcie.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Element | Wymagane | Opis |
|:---|:---|:---|
| `scopes` | Tak | Lista co najmniej jednego zakresu, w którym można określić maszyny wirtualne, których dotyczy to zastąpienie. Mimo że DCR jest skojarzony z maszyną wirtualną, maszyna wirtualna musi należeć do zakresu, aby można było zastosować przesłonięcie. |
| `monitors` | Tak | Lista co najmniej jednego ciągu, który określa, które monitory będą otrzymywać te przesłonięcia.  |
| `monitorConfiguration` | Nie | Konfiguracja monitora, w tym Stanów kondycji i sposobu ich obliczania. |
| `alertConfiguration` | Nie | Konfiguracja alertów dla monitora. |
| `isEnabled` | Nie | Określa, czy monitor jest włączony, czy nie. Wyłączone przełączniki monitora w specjalnym stanie *wyłączonej* kondycji i Stany wyłączone, chyba że zostanie on włączony. W przypadku pominięcia monitor będzie dziedziczyć stan z monitora nadrzędnego w hierarchii. |


## <a name="scopes-element"></a>Scopes, element
Każde zastępujące ma co najmniej jeden zakres definiowania maszyn wirtualnych, do których należy zastosować zastąpienie. Zakresem może być subskrypcja, Grupa zasobów lub Pojedyncza maszyna wirtualna. Nawet jeśli przesłonięcie znajduje się w DCR skojarzonym z konkretną maszyną wirtualną, jest ono stosowane tylko do tej maszyny wirtualnej, jeśli maszyna wirtualna znajduje się w jednym z zakresów zastąpień. Dzięki temu można w szerokim zakresie skojarzyć mniejszą liczbę DCR z zestawem maszyn wirtualnych, ale zapewnić szczegółową kontrolę nad przypisaniem każdego przesłonięcia w obrębie samego DCR. Może być konieczne utworzenie małego zestawu DCR i skojarzenie ich z zestawem maszyn wirtualnych przy użyciu zasad podczas określania zastąpień monitora kondycji dla różnych podzestawów tych maszyn wirtualnych przy użyciu elementu Scopes.

W poniższej tabeli przedstawiono przykłady różnych zakresów.

| Zakres | Przykład |
|:---|:---|
| Pojedyncza maszyna wirtualna | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Wszystkie maszyny wirtualne w grupie zasobów | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Wszystkie maszyny wirtualne w ramach subskrypcji | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Wszystkie maszyny wirtualne, z którymi jest skojarzona Reguła zbierania danych | `*` |


## <a name="monitors-element"></a>Monitors — element
Lista co najmniej jednego ciągu, który definiuje, które monitory w hierarchii kondycji otrzymają to przesłonięcie. Każdy element może być nazwą monitora lub nazwą typu zgodną z co najmniej jednym monitorem, który otrzyma to zastąpienie. 

```json
"monitors": [
    "<monitor name>"
 ],
```



W poniższej tabeli wymieniono bieżące dostępne nazwy monitorów.

| Nazwa typu | Nazwa | Opis |
|:---|:---|:---|
| root | root | Monitor najwyższego poziomu reprezentujący kondycję maszyny wirtualnej. | |
| wykorzystanie procesora CPU | wykorzystanie procesora CPU | Monitor wykorzystania procesora CPU. | |
| dyski logiczne | dyski logiczne | Monitor zbiorczy stanu kondycji wszystkich monitorowanych dysków w maszynie wirtualnej z systemem Windows. | |
| dyski logiczne\|* | dyski logiczne \| C:<br>dyski logiczne \| D: | Zagregowane kondycje śledzenia monitora dla danego dysku w maszynie wirtualnej z systemem Windows. | 
| \| * \| ilość wolnego miejsca na dyskach logicznych | dyski logiczne \| C: \| wolne miejsce<br>dyski logiczne \| D: \| wolne miejsce | Monitor wolnego miejsca na dysku w maszynie wirtualnej z systemem Windows. |
| systemy plików | systemy plików | Zagregowany monitor kondycji wszystkich systemów plików na maszynie wirtualnej z systemem Linux. |
| systemy plików\|* | systemy plików\|/<br>/MNT systemu plików \| | Agregowanie kondycji śledzenia monitora dla systemu plików maszyny wirtualnej z systemem Linux. | systemy plików|/var/log |
| \| * \| wolne miejsce w systemie plików | \| / \| wolne miejsce w systemie plików<br>\|/mnt \| wolne miejsce w systemie plików | Monitor wolnego miejsca na dysku w systemie plików maszyn wirtualnych z systemem Linux. | 
| pamięć | pamięć | Monitor zagregowany kondycji pamięci maszyny wirtualnej. | |
| \|dostępna pamięć| \|dostępna pamięć | Monitoruje dostępną pamięć na maszynie wirtualnej. | |


## <a name="alertconfiguration-element"></a>alertConfiguration, element
Określa, czy należy utworzyć alert z monitora.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Element | Obowiązkowy | Opis | 
|:---|:---|:---|
| `isEnabled` | Nie | Jeśli zostanie ustawiona na wartość true, monitor wygeneruje alert podczas przełączania do stanu krytycznego lub ostrzegawczego i rozwiązuje alert w przypadku powrotu do prawidłowego stanu. Jeśli wartość jest równa FAŁSZ lub pominięta, alert nie zostanie wygenerowany.  |


## <a name="monitorconfiguration-element"></a>monitorConfiguration, element
Dotyczy tylko monitorów jednostkowych. Definiuje konfigurację monitora, w tym Stany kondycji i sposób ich obliczania.

Parametry definiują algorytm obliczający wartość metryki w celu porównania z progami. Zamiast działania na jednym przykładzie danych z metryki źródłowej, monitor ocenia kilka próbek metryk odebranych w oknie od czasu oceny i `lookbackSec` temu. Wszystkie próbki odebrane w tym przedziale czasu są brane pod uwagę, a liczba próbek jest większa niż `maxSamples` , starsze próbki powyżej `maxSamples` są ignorowane. 

W przypadku gdy w interwale lookback są mniej próbek niż `minSamples` , monitor przejdzie do *nieznanego* stanu kondycji, co oznacza, że nie ma wystarczającej ilości danych, aby uzyskać świadomą decyzję dotyczącą kondycji podstawowych metryk. Jeśli większa liczba próbek `minSamples` jest dostępna, funkcja agregacji określona przez parametr evaluationtype parametru US działa nad zestawem, aby obliczyć pojedynczą wartość.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Element | Obowiązkowy | Opis | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Nie | Definiuje częstotliwość oceny stanu kondycji. Każdy monitor jest oceniany w momencie uruchomienia agenta i w regularnych odstępach czasu, zdefiniowany przez ten parametr. |
| `lookbackSecs`   | Nie | Rozmiar okna lookback w sekundach. |
| `evaluationType` | Nie | `min` — należy przyjmować wartość minimalną z całego zestawu próbek<br>`max` -Weź wartość maksymalną z całego zestawu próbek<br>`avg` — Pobieraj średnie wartości z zestawu próbek<br>`all` — Porównaj każdą pojedynczą wartość w zestawie wartości progowych. Monitoruje stan przełączników, jeśli i tylko wtedy, gdy wszystkie próbki w zestawie spełniają warunek progu. |
| `minSamples`     | Nie | Minimalna liczba wartości do użycia w celu obliczenia wartości. |
| `maxSamples`     | Nie | Maksymalna liczba wartości do użycia w celu obliczenia wartości. |
| `warningCondition`  | Nie | Logika progu i porównania dla warunku ostrzeżenia. |
| `criticalCondition` | Nie | Logika progu i porównania dla warunku krytycznego. |


## <a name="warningcondition-element"></a>warningCondition, element
Definiuje logikę progową i porównania dla warunku ostrzeżenia. Jeśli ten element nie jest uwzględniony, monitor nigdy nie przejdzie do ostrzegawczego stanu kondycji.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Właściwość | Obowiązkowy | Opis | 
|:---|:---|:---|
| `isEnabled` | Nie | Określa, czy warunek jest włączony. Jeśli ustawiona na **wartość false**, warunek jest wyłączony, mimo że można ustawić właściwości progu i operatora. |
| `threshold` | Nie | Definiuje próg w celu porównania obliczonej wartości. |
| `operator`  | Nie | Definiuje operator porównania do użycia w wyrażeniu progowym. Możliwe wartości: >, <, >=, <=, = =. |


## <a name="criticalcondition-element"></a>criticalCondition, element
Definiuje logikę progową i porównania dla warunku krytycznego. Jeśli ten element nie jest uwzględniony, monitor nigdy nie przejdzie do krytycznego stanu kondycji.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Właściwość | Obowiązkowy | Opis | 
|:---|:---|:---|
| `isEnabled` | Nie | Określa, czy warunek jest włączony. Jeśli ustawiona na **wartość false**, warunek jest wyłączony, mimo że można ustawić właściwości progu i operatora. |
| `threshold` | Nie | Definiuje próg w celu porównania obliczonej wartości. |
| `operator`  | Nie | Definiuje operator porównania do użycia w wyrażeniu progowym. Możliwe wartości: >, <, >=, <=, = =. |

## <a name="sample-data-collection-rule"></a>Zasada przykładowej zbierania danych
Aby zapoznać się z przykładową regułą zbierania danych dotyczących funkcji monitorowania gościa, zobacz [Włączanie maszyny wirtualnej przy użyciu szablonu Menedżer zasobów](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Następne kroki

- Przeczytaj więcej na temat [reguł zbierania danych](../agents/data-collection-rule-overview.md).