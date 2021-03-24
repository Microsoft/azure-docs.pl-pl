---
title: Monitorowanie kondycji wystąpień App Service
description: Dowiedz się, jak monitorować kondycję wystąpień App Service przy użyciu kontroli kondycji.
keywords: usługa Azure App Service, aplikacja sieci Web, sprawdzanie kondycji, ruch tras, wystąpienia w dobrej kondycji, ścieżka, monitorowanie,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: e9d92c60e74ac9106246ccd445afaca926065e5f
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871201"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Monitorowanie wystąpień App Service przy użyciu kontroli kondycji

![Niepowodzenie sprawdzania kondycji][2]

W tym artykule zastosowano kontrolę kondycji w Azure Portal do monitorowania wystąpień App Service. Kontrola kondycji zwiększa dostępność aplikacji, usuwając wystąpienia w złej kondycji. [Plan App Service](./overview-hosting-plans.md) powinien zostać przeskalowany do co najmniej dwóch wystąpień, aby można było użyć kontroli kondycji. Ścieżka sprawdzania kondycji powinna sprawdzać krytyczne składniki aplikacji. Na przykład jeśli aplikacja zależy od bazy danych i systemu obsługi komunikatów, punkt końcowy sprawdzania kondycji powinien łączyć się z tymi składnikami. Jeśli aplikacja nie może połączyć się ze składnikiem krytycznym, ścieżka powinna zwrócić kod odpowiedzi 500 na poziomie, aby wskazać, że aplikacja jest w złej kondycji.

## <a name="what-app-service-does-with-health-checks"></a>Co App Service sprawdza kondycję

- Po otrzymaniu ścieżki do aplikacji Sprawdź kondycję wysyła polecenie ping do tej ścieżki we wszystkich wystąpieniach aplikacji App Service w odstępach 1-minutowych.
- Jeśli wystąpienie nie odpowiada z kodem stanu z przedziału od 200-299 (włącznie) po dwóch lub większej liczbie żądań lub nie odpowiada na polecenie ping, system ustali, że jest w złej kondycji i usunie go.
- Po usunięciu Sprawdzanie kondycji kontynuuje wysyłanie polecenia ping do wystąpienia złej kondycji. Jeśli w dalszym ciągu odpowiedź nie powiedzie się, App Service ponownie uruchomi podstawową maszynę wirtualną w celu zwrócenia wystąpienia do prawidłowego stanu.
- Jeśli wystąpienie pozostaje w złej kondycji w ciągu godziny, zostanie zastąpione nowym wystąpieniem.
- Ponadto podczas skalowania w górę lub w dół App Service wysyła polecenie ping do ścieżki sprawdzania kondycji, aby upewnić się, że nowe wystąpienia są gotowe.

> [!NOTE]
> Kontrola kondycji nie postępuje zgodnie z 302 przekierowań. Co najwyżej jedno wystąpienie zostanie zastąpione na godzinę, co najwyżej trzy wystąpienia dziennie na App Service plan.
>

## <a name="enable-health-check"></a>Włącz sprawdzanie kondycji

![Nawigacja w witrynie Azure Portal — Sprawdzanie kondycji][3]

- Aby włączyć sprawdzanie kondycji, przejdź do Azure Portal i wybierz swoją aplikację App Service.
- W obszarze **monitorowanie** wybierz pozycję **Sprawdzanie kondycji**.
- Wybierz pozycję **Włącz** i podaj prawidłową ścieżkę URL w aplikacji, na przykład `/health` lub `/api/health` .
- Kliknij pozycję **Zapisz**.

> [!CAUTION]
> Sprawdzanie kondycji zmiany konfiguracji Uruchom ponownie aplikację. Aby zminimalizować wpływ na aplikacje produkcyjne, zalecamy [skonfigurowanie miejsc przejściowych](deploy-staging-slots.md) i zamianę na środowisko produkcyjne.
>

### <a name="configuration"></a>Konfigurowanie

Oprócz konfigurowania opcji sprawdzania kondycji można również skonfigurować następujące [Ustawienia aplikacji](configure-common.md):

| Nazwa ustawienia aplikacji | Dozwolone wartości | Opis |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Maksymalna liczba błędów ping. Na przykład po ustawieniu na `2` , wystąpienia zostaną usunięte po `2` nieudanych wywołaniach ping. Ponadto podczas skalowania w górę lub w dół App Service wysyła polecenie ping do ścieżki sprawdzania kondycji, aby upewnić się, że nowe wystąpienia są gotowe. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Aby uniknąć przeciążenia wystąpień w dobrej kondycji, nie zostaną wykluczone więcej niż połowę wystąpień. Na przykład jeśli plan App Service jest skalowany w czterech wystąpieniach, a trzy są w złej kondycji, zostaną wykluczone co najwyżej dwa. Pozostałe dwa wystąpienia (w dobrej kondycji i w złej kondycji) będą nadal otrzymywać żądania. W scenariuszu najgorszego przypadku, w którym wszystkie wystąpienia są złej kondycji, żaden z nich nie zostanie wykluczony. Aby przesłonić to zachowanie, należy ustawić ustawienie aplikacji na wartość z zakresu od `0` do `100` . Wyższa wartość oznacza, że więcej wystąpień w złej kondycji zostanie usuniętych (wartość domyślna to 50). |

#### <a name="authentication-and-security"></a>Uwierzytelnianie i zabezpieczenia

Kontrola kondycji integruje się z funkcjami uwierzytelniania i autoryzacji App Service. Jeśli te funkcje zabezpieczeń są włączone, nie są wymagane żadne dodatkowe ustawienia. Jeśli jednak używany jest własny system uwierzytelniania, ścieżka kontroli kondycji musi zezwalać na dostęp anonimowy. Jeśli lokacja jest tylko HTTP **s**, żądanie sprawdzania kondycji zostanie wysłane za pośrednictwem protokołu HTTP **S**.

Duże zespoły programistyczne przedsiębiorstwa często muszą przestrzegać wymagań dotyczących zabezpieczeń dla uwidocznionych interfejsów API. Aby zabezpieczyć punkt końcowy sprawdzania kondycji, należy najpierw użyć funkcji, takich jak [Ograniczenia adresów IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [certyfikaty klienta](app-service-ip-restrictions.md#set-an-ip-address-based-rule)lub Virtual Network, aby ograniczyć dostęp do aplikacji. Punkt końcowy sprawdzania kondycji można zabezpieczyć przez wymaganie `User-Agent` dopasowania żądania przychodzącego `HealthCheck/1.0` . Nie można sfałszować User-Agent, ponieważ żądanie jest już zabezpieczone przez wcześniejsze funkcje zabezpieczeń.

## <a name="monitoring"></a>Monitorowanie

Po podaniu ścieżki sprawdzania kondycji aplikacji można monitorować kondycję lokacji przy użyciu Azure Monitor. W bloku **Sprawdzanie kondycji** w portalu kliknij **metryki** na górnym pasku narzędzi. Spowoduje to otwarcie nowego bloku, w którym można zobaczyć historyczny stan kondycji lokacji i utworzyć nową regułę alertu. Więcej informacji o monitorowaniu lokacji [znajduje się w przewodniku na Azure monitor](web-sites-monitor.md).

## <a name="limitations"></a>Ograniczenia

Kontrola kondycji nie powinna być włączona w witrynach funkcji w warstwie Premium. Ze względu na szybkie skalowanie funkcji w warstwie Premium żądania sprawdzania kondycji mogą spowodować niepotrzebne wahania ruchu HTTP. Funkcje Premium mają własne wewnętrzne sondy kondycji, które są używane do informowania decyzji o skalowaniu.

## <a name="next-steps"></a>Następne kroki
- [Tworzenie alertu dziennika aktywności w celu monitorowania wszystkich operacji aparatu automatycznego skalowania w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Utwórz alert dziennika aktywności, aby monitorować wszystkie operacje skalowania automatycznego skalowania w poziomie i skalowania w poziomie w ramach subskrypcji](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
