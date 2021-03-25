---
title: Limity żądań i ograniczanie
description: Opisuje sposób korzystania z ograniczania z żądaniami Azure Resource Manager, gdy osiągnięto limity subskrypcji.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024629"
---
# <a name="throttling-resource-manager-requests"></a>Ograniczanie przepływności żądań usługi Resource Manager

W tym artykule opisano, jak Azure Resource Manager ograniczać żądania. Pokazuje, jak śledzić liczbę żądań, które pozostaną przed osiągnięciem limitu, oraz jak odpowiedzieć, gdy osiągnięto limit.

Ograniczanie przepływności odbywa się na dwóch poziomach. Azure Resource Manager ogranicza żądania subskrypcji i dzierżawy. Jeśli żądanie dotyczy limitów ograniczania subskrypcji i dzierżawy, Menedżer zasobów kieruje żądanie do dostawcy zasobów. Dostawca zasobów stosuje limity ograniczania, które są dostosowane do jego operacji. Na poniższej ilustracji przedstawiono sposób, w jaki ograniczenie jest stosowane jako żądanie przechodzą od użytkownika do Azure Resource Manager i dostawcy zasobów.

![Ograniczanie żądań](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Limity subskrypcji i dzierżawy

Wszystkie operacje na poziomie subskrypcji i na poziomie dzierżawy podlegają ograniczeniom ograniczającym. Żądania subskrypcji to te, które obejmują przekazywanie identyfikatora subskrypcji, na przykład pobieranie grup zasobów w ramach subskrypcji. Żądania dzierżawców nie zawierają identyfikatora subskrypcji, takiego jak pobieranie prawidłowych lokalizacji platformy Azure.

Domyślne limity ograniczania na godzinę przedstawiono w poniższej tabeli.

| Zakres | Operacje | Limit |
| ----- | ---------- | ------- |
| Subskrypcja | odczytywan | 12000 |
| Subskrypcja | kaskad | 15000 |
| Subskrypcja | powoduje | 1200 |
| Dzierżawa | odczytywan | 12000 |
| Dzierżawa | powoduje | 1200 |

Limity te dotyczą podmiotu zabezpieczeń (użytkownika lub aplikacji) zgłaszającego te żądania i identyfikatora subskrypcji lub identyfikatora dzierżawy. Jeśli Twoje żądania pochodzą od więcej niż jednego podmiotu zabezpieczeń, limit w ramach subskrypcji lub dzierżawy jest większy niż 12 000 i 1200 na godzinę.

Te limity mają zastosowanie do każdego wystąpienia Azure Resource Manager. W każdym regionie świadczenia usługi Azure istnieje wiele wystąpień, a Azure Resource Manager jest wdrażana we wszystkich regionach świadczenia usługi Azure.  W ten sposób limity są wyższe niż te limity. Żądania od użytkownika są zwykle obsługiwane przez różne wystąpienia Azure Resource Manager.

## <a name="resource-provider-limits"></a>Limity dostawcy zasobów

Dostawcy zasobów stosują swoje własne limity ograniczania. Ponieważ Menedżer zasobów dławienia według identyfikatora podmiotu zabezpieczeń i wystąpienia Menedżer zasobów, dostawca zasobów może odbierać więcej żądań niż domyślne limity w poprzedniej sekcji.

W tej sekcji omówiono limity ograniczania liczby używanych powszechnie dostawców zasobów.

### <a name="storage-throttling"></a>Ograniczanie przepustowości magazynu

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Ograniczanie przepustowości sieci

Dostawca zasobów Microsoft. Network stosuje następujące limity ograniczania przepustowości:

| Operacja | Limit |
| --------- | ----- |
| Zapisz/Usuń (PUT) | 1000 za 5 minut |
| Odczyt (GET) | 10000 za 5 minut |

### <a name="compute-throttling"></a>Ograniczanie obliczeń

Aby uzyskać informacje dotyczące limitów ograniczania dla operacji obliczeniowych, zobacz [Rozwiązywanie problemów z ograniczaniem wydajności interfejsu API — obliczenia](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Aby sprawdzić wystąpienia maszyn wirtualnych w zestawie skalowania maszyn wirtualnych, użyj [operacji Virtual Machine Scale Sets](/rest/api/compute/virtualmachinescalesetvms). Na przykład użyj opcji maszyny wirtualne [zestawu skalowania maszyn wirtualnych](/rest/api/compute/virtualmachinescalesetvms/list) z parametrami, aby sprawdzić stan elektrowni wystąpień maszyn wirtualnych. Ten interfejs API zmniejsza liczbę żądań.

### <a name="azure-resource-graph-throttling"></a>Ograniczanie wykresów zasobów platformy Azure

[Wykres zasobów platformy Azure](../../governance/resource-graph/overview.md) ogranicza liczbę żądań do operacji. Kroki opisane w tym artykule, aby określić pozostałe żądania i jak odpowiedzieć, gdy limit zostanie osiągnięty, również mają zastosowanie do grafu zasobów. Jednak wykres zasobów ustawia własny limit i częstotliwość resetowania. Aby uzyskać więcej informacji, zobacz [nagłówki ograniczania wykresów zasobów](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Inni dostawcy zasobów

Aby uzyskać informacje na temat ograniczania przepustowości w innych dostawcach zasobów, zobacz:

* [Wskazówki dotyczące ograniczania usługi Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [Rozwiązywanie problemów z usługą Azure Kubernetes Service](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Kod błędu

Po osiągnięciu limitu zostanie wyświetlony kod stanu HTTP **429 zbyt wiele żądań**. Odpowiedź zawiera wartość **retry-After** , która określa liczbę sekund oczekiwania aplikacji (lub uśpienia) przed wysłaniem kolejnego żądania. Jeśli wysłano żądanie przed upływem wartości ponowienia próby, żądanie nie zostanie przetworzone i zostanie zwrócona nowa wartość ponownych prób.

Po oczekiwaniu na określony czas możesz również zamknąć i ponownie otworzyć połączenie z platformą Azure. Zresetowanie połączenia może wiązać się z innym wystąpieniem Azure Resource Manager.

Jeśli używasz zestawu Azure SDK, zestaw SDK może mieć konfigurację automatycznej ponownej próby. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące ponawiania prób dla usług platformy Azure](/azure/architecture/best-practices/retry-service-specific).

Niektórzy dostawcy zasobów zwracają 429, aby zgłosić tymczasowy problem. Problem może być warunkiem przeciążenia, które nie jest bezpośrednio spowodowane przez żądanie. Może też reprezentować tymczasowy błąd dotyczący stanu zasobu docelowego lub zasobu zależnego. Na przykład dostawca zasobów sieciowych zwraca 429 z kodem błędu **RetryableErrorDueToAnotherOperation** , gdy zasób docelowy jest zablokowany przez inną operację. Aby określić, czy błąd pochodzi z ograniczenia lub warunku tymczasowego, przejrzyj szczegóły błędu w odpowiedzi.

## <a name="remaining-requests"></a>Pozostałe żądania

Liczbę pozostałych żądań można określić, sprawdzając nagłówki odpowiedzi. Żądania odczytu zwracają wartość w nagłówku dla liczby pozostałych żądań odczytu. Żądania zapisu zawierają wartość dla liczby pozostałych żądań zapisu. W poniższej tabeli opisano nagłówki odpowiedzi, które można przeanalizować dla tych wartości:

| Nagłówek odpowiedzi | Opis |
| --- | --- |
| x-MS-ratelimit-pozostałe-Subscription-READS |Pozostałe odczyty z zakresu subskrypcji. Ta wartość jest zwracana podczas operacji odczytu. |
| x-MS-ratelimit-pozostałe-Subscription-Writes |Pozostałe zapisy w zakresie subskrypcji. Ta wartość jest zwracana podczas operacji zapisu. |
| x-MS-ratelimit-pozostałe-dzierżawy — odczyty |Pozostałe odczyty z zakresu dzierżawy |
| x-MS-ratelimit-pozostałe-dzierżawcy — zapisy |Pozostałe zapisy w zakresie dzierżawy |
| x-MS-ratelimit-pozostałe-Subscription-Resource-Requests |Pozostałe żądania typu zasobu z zakresem subskrypcji pozostało.<br /><br />Ta wartość nagłówka jest zwracana tylko wtedy, gdy usługa została zastąpiona domyślnym limitem. Menedżer zasobów dodaje tę wartość zamiast operacji odczytu lub zapisu subskrypcji. |
| x-MS-ratelimit-pozostało-Subscription-Resource-Entities-Read |Pozostałe żądania kolekcji typów zasobów z zakresem subskrypcji są pozostałe.<br /><br />Ta wartość nagłówka jest zwracana tylko wtedy, gdy usługa została zastąpiona domyślnym limitem. Ta wartość zapewnia liczbę pozostałych żądań kolekcji (Lista zasobów). |
| x-MS-ratelimit-pozostały-dzierżawca-zasób-żądania |Pozostałe żądania typu zasobu z zakresem dzierżawy pozostało.<br /><br />Ten nagłówek jest dodawany tylko do żądań na poziomie dzierżawy i tylko wtedy, gdy usługa została zastąpiona domyślnym limitem. Menedżer zasobów dodaje tę wartość zamiast operacji odczytu lub zapisu dzierżawy. |
| x-MS-ratelimit-pozostały-dzierżawca-obiekty — odczyt |Pozostałe żądania kolekcji typów zasobów z zakresem dzierżawy pozostało.<br /><br />Ten nagłówek jest dodawany tylko do żądań na poziomie dzierżawy i tylko wtedy, gdy usługa została zastąpiona domyślnym limitem. |

Dostawca zasobów może również zwracać nagłówki odpowiedzi z informacjami o pozostałych żądaniach. Informacje o nagłówkach odpowiedzi zwracanych przez dostawcę zasobów obliczeniowych znajdują się w temacie [Informacje o szybkości wywołań — nagłówki odpowiedzi](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Pobieranie wartości nagłówka

Pobieranie tych wartości nagłówka w kodzie lub skrypcie nie jest inne niż pobieranie żadnej wartości nagłówka. 

Na przykład w **języku C#** pobierana jest wartość nagłówka z obiektu **HttpWebResponse** o nazwie **Response** z następującym kodem:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

W programie **PowerShell** pobierana jest wartość nagłówka z Invoke-WebRequest operacji.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Aby zapoznać się z kompletnym przykładem programu PowerShell, zobacz [Check Menedżer zasobów Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Jeśli chcesz zobaczyć pozostałe żądania debugowania, możesz podać parametr **-Debug** w poleceniu cmdlet **programu PowerShell** .

```powershell
Get-AzResourceGroup -Debug
```

Zwracające wiele wartości, w tym następującą wartość odpowiedzi:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Aby uzyskać limity zapisu, użyj operacji zapisu: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Zwracające wiele wartości, w tym następujące wartości:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

W **interfejsie wiersza polecenia platformy Azure** można pobrać wartość nagłówka przy użyciu opcji Więcej informacji.

```azurecli
az group list --verbose --debug
```

Zwracające wiele wartości, w tym następujące wartości:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Aby uzyskać limity zapisu, użyj operacji zapisu: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Zwracające wiele wartości, w tym następujące wartości:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z kompletnym przykładem programu PowerShell, zobacz [Check Menedżer zasobów Limits for a Subscription](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Aby uzyskać więcej informacji o limitach i przydziałach, zobacz [limity subskrypcji i usług platformy Azure, limity przydziału i ograniczenia](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Aby dowiedzieć się więcej o obsłudze asynchronicznych żądań REST, zobacz [śledzenie asynchronicznych operacji na platformie Azure](async-operations.md).