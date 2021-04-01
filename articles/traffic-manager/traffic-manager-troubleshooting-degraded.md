---
title: Rozwiązywanie problemów ze stanem obniżonej wydajności na platformie Azure Traffic Manager
description: Jak rozwiązywać problemy z profilami Traffic Manager, gdy jest on wyświetlany jako obniżony stan.
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: duau
ms.openlocfilehash: b76eab5771d724e4f0ec56b7d5acd5cf5f91edc0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183459"
---
# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Rozwiązywanie problemów ze stanem obniżonej wydajności usługi Azure Traffic Manager

W tym artykule opisano sposób rozwiązywania problemów z profilem usługi Azure Traffic Manager, w którym jest wyświetlany stan obniżonej sprawności. Pierwszym krokiem w rozwiązywaniu problemów z nieobniżonym stanem Traffic Manager na platformie Azure jest włączenie rejestrowania.  Aby uzyskać więcej informacji, zapoznaj się z tematem [Włączanie dzienników zasobów](./traffic-manager-diagnostic-logs.md) . W tym scenariuszu należy zastanowić się, że skonfigurowano profil Traffic Manager wskazujący niektóre usługi hostowane w usłudze cloudapp.net. Jeśli kondycja Traffic Manager zostanie wyświetlony stan **obniżonej wydajności** , może to spowodować **spadek** stanu co najmniej jednego punktu końcowego:

![stan nieprawidłowego punktu końcowego](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degradedifonedegraded.png)

Jeśli kondycja Traffic Manager wyświetla stan **nieaktywny** , wówczas oba punkty końcowe mogą być **wyłączone**:

![Stan nieaktywnej Traffic Manager](./media/traffic-manager-troubleshooting-degraded/traffic-manager-inactive.png)

## <a name="understanding-traffic-manager-probes"></a>Zrozumienie Traffic Manager sond

* Traffic Manager traktuje punkt końcowy jako w trybie ONLINE tylko wtedy, gdy sonda odbiera odpowiedź HTTP 200 z powrotem ze ścieżki sondy. Jeśli aplikacja zwróci jakikolwiek inny kod odpowiedzi HTTP, należy dodać ten kod odpowiedzi do [zakresów kodów oczekiwanego stanu](./traffic-manager-monitoring.md#configure-endpoint-monitoring) Traffic Manager profilu.
* Odpowiedź przekierowania 30x jest traktowana jako błąd, chyba że określono jako prawidłowy kod odpowiedzi w zakresie [kodów stanu oczekiwanego](./traffic-manager-monitoring.md#configure-endpoint-monitoring) w ramach twojego profilu Traffic Manager. Traffic Manager nie sonduje elementu docelowego przekierowania.
* W przypadku sond HTTPs Błędy certyfikatów są ignorowane.
* Rzeczywista zawartość ścieżki sondy nie ma znaczenia, o ile jest zwracana 200. Badanie adresu URL do pewnej zawartości statycznej, takiej jak "/favicon.ico", jest typową techniką. Zawartość dynamiczna, podobnie jak strony ASP, może nie zawsze zwracać 200 nawet wtedy, gdy aplikacja jest w dobrej kondycji.
* Najlepszym rozwiązaniem jest ustawienie dla ścieżki sondy elementu, który ma wystarczającą logikę, aby określić, że lokacja jest w górę lub w dół. W poprzednim przykładzie, przez ustawienie ścieżki na "/favicon.ico", tylko testuje, że w3wp.exe odpowiada. To sondowanie może nie wskazywać, że aplikacja sieci Web jest w dobrej kondycji. Lepszym rozwiązaniem jest ustawienie ścieżki do elementu, takiego jak "/Probe.aspx", który ma logikę do określenia kondycji lokacji. Można na przykład użyć liczników wydajności do użycia procesora CPU lub zmierzyć liczbę żądań zakończonych niepowodzeniem. Możesz też próbować uzyskać dostęp do zasobów bazy danych lub stanu sesji, aby upewnić się, że aplikacja sieci Web działa.
* Jeśli wszystkie punkty końcowe w profilu mają obniżoną wydajność, Traffic Manager traktuje wszystkie punkty końcowe jako zdrowe i kieruje ruch do wszystkich punktów końcowych. Takie zachowanie zapewnia, że problemy z mechanizmem sondowania nie powodują kompletnego przestoju usługi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Aby rozwiązać problem z awarią sondy, potrzebne jest narzędzie, które pokazuje kod stanu HTTP Return z adresu URL sondy. Dostępnych jest wiele narzędzi, które pokazują nieprzetworzoną odpowiedź HTTP.

* [Fiddler](https://www.telerik.com/fiddler)
* [curl](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Ponadto możesz użyć karty sieć narzędzi debugowania F12 w programie Internet Explorer, aby wyświetlić odpowiedzi HTTP.

W tym przykładzie chcemy zobaczyć odpowiedź z naszego adresu URL sondy: http: \/ /watestsdp2008r2.cloudapp.NET:80/Probe. Poniższy przykład programu PowerShell ilustruje ten problem.

```powershell
Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Przykładowe dane wyjściowe:

```output
StatusCode StatusDescription
---------- -----------------
        301 Moved Permanently
```

Zwróć uwagę, że odebrano odpowiedź przekierowania. Jak wspomniano wcześniej, każdy kod błędu inny niż 200 jest traktowany jako błąd. Traffic Manager zmienia stan punktu końcowego na offline. Aby rozwiązać ten problem, sprawdź konfigurację witryny sieci Web, aby upewnić się, że można zwrócić prawidłowy kod StatusCode ze ścieżki sondy. Skonfiguruj ponownie sondę Traffic Manager, aby wskazywała ścieżkę zwracającą 200.

Jeśli sonda korzysta z protokołu HTTPS, może być konieczne wyłączenie sprawdzania certyfikatu w celu uniknięcia błędów protokołu SSL/TLS podczas testu. Następujące instrukcje programu PowerShell Wyłącz weryfikację certyfikatu dla bieżącej sesji programu PowerShell:

```powershell
add-type @"
using System.Net;
using System.Security.Cryptography.X509Certificates;
public class TrustAllCertsPolicy : ICertificatePolicy {
    public bool CheckValidationResult(
    ServicePoint srvPoint, X509Certificate certificate,
    WebRequest request, int certificateProblem) {
    return true;
    }
}
"@
[System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Następne kroki

[Informacje o metodach routingu ruchu Traffic Manager](traffic-manager-routing-methods.md)

[Co to jest Traffic Manager](traffic-manager-overview.md)

[Usługi w chmurze](/previous-versions/azure/jj155995(v=azure.100))

[Azure App Service](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operacje w usłudze Traffic Manager (dokumentacja interfejsu API REST)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Polecenia cmdlet usługi Azure Traffic Manager][1]

[1]: /powershell/module/az.trafficmanager