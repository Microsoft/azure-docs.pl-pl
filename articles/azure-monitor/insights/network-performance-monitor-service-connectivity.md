---
title: Network Performance Monitor łączności usługi rozwiązań — Azure Log Analytics
description: Korzystając z funkcji Monitor łączności usługi w Network Performance Monitor, można monitorować łączność sieciową z dowolnym punktem końcowym z otwartym portem TCP.
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 02/20/2018
ms.openlocfilehash: a5b42e61af398e111b2014f3734a0011a4bee778
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734166"
---
# <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

> [!IMPORTANT]
> Od 1 lipca 2021 nie będzie można dodawać nowych testów w istniejącym obszarze roboczym ani włączać nowego obszaru roboczego w Network Performance Monitor. Można nadal korzystać z testów utworzonych przed 1 lipca 2021. Aby zminimalizować zakłócenia działania usługi dla bieżących obciążeń, należy [migrować testy z Network Performance Monitor do nowego monitora połączeń](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) na platformie Azure Network Watcher przed 29 lutego 2024.

Możesz użyć możliwości monitora łączności usług w [Network Performance Monitor](network-performance-monitor.md) , aby monitorować łączność sieciową z dowolnym punktem końcowym, który ma otwarty port TCP. Takie punkty końcowe obejmują witryny sieci Web, aplikacje SaaS, aplikacje PaaS i bazy danych SQL. 

Za pomocą monitora łączności usług można wykonywać następujące funkcje: 

- Monitoruj łączność sieciową z aplikacjami i usługami sieciowymi z wielu oddziałów lub lokalizacji. Aplikacje i usługi sieciowe obejmują Microsoft 365, Dynamics CRM, wewnętrzne aplikacje biznesowe i bazy danych SQL.
- Użyj wbudowanych testów do monitorowania łączności sieciowej z punktami końcowymi Microsoft 365 i Dynamics 365. 
- Określ czas odpowiedzi, opóźnienie sieci i utratę pakietów podczas nawiązywania połączenia z punktem końcowym.
- Ustal, czy niska wydajność aplikacji jest spowodowana przez sieć lub z powodu problemu z zakończeniem działania dostawcy aplikacji.
- Zidentyfikuj punkty przerwania w sieci, które mogą powodować niską wydajność aplikacji, wyświetlając czas opóźnienia spowodowany przez każdy przeskok mapy topologii.


![Monitorowanie łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-intro.png)


## <a name="configuration"></a>Konfigurowanie 
Aby otworzyć konfigurację Network Performance Monitor, Otwórz [rozwiązanie Network Performance Monitor](network-performance-monitor.md) i wybierz pozycję **Konfiguruj**.

![Konfigurowanie rozwiązania Network Performance Monitor](media/network-performance-monitor-service-endpoint/npm-configure-button.png)


### <a name="configure-log-analytics-agents-for-monitoring"></a>Skonfiguruj Log Analytics agentów do monitorowania
Włącz następujące reguły zapory na węzłach używanych do monitorowania, aby rozwiązanie umożliwiało odnajdywanie topologii z węzłów do punktu końcowego usługi: 

```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```

### <a name="create-service-connectivity-monitor-tests"></a>Tworzenie testów monitora łączności usług 

Rozpocznij tworzenie testów do monitorowania łączności sieciowej z punktami końcowymi usługi.

1. Wybierz kartę **monitor łączności usług** .
2. Wybierz pozycję **Dodaj test**, a następnie wprowadź nazwę i opis testu. Można utworzyć maksymalnie 450 testów dla każdego obszaru roboczego. 
3. Wybierz typ testu:<br>

    * Wybierz pozycję **Sieć Web** , aby monitorować łączność z usługą, która odpowiada na żądania HTTP/S, takie jak outlook.office365.com lub Bing.com.<br>
    * Wybierz opcję **Sieć** , aby monitorować łączność z usługą, która odpowiada na żądania TCP, ale nie odpowiada na żądania HTTP/S, takie jak serwer SQL Server, serwer FTP lub port SSH. 
    * Na przykład: Aby utworzyć test sieci Web na koncie usługi BLOB Storage, wybierz pozycję **Sieć Web** i wprowadź element docelowy jako *yourstorageaccount*. blob.Core.Windows.NET. Podobnie można utworzyć testy dla innych magazynów tabel, magazynu kolejek i Azure Files przy użyciu [tego linku.](../../storage/common/storage-account-overview.md#storage-account-endpoints)
4. Jeśli nie chcesz wykonywać pomiarów sieci, takich jak opóźnienie sieci, utrata pakietów i odnajdywanie topologii, wyczyść pole wyboru **Wykonaj pomiary sieci** . Kontynuuj, aby uzyskać maksymalną korzyść z możliwości. 
5. W polu **docelowy** wprowadź adres URL/nazwę FQDN/adres IP, do którego chcesz monitorować łączność sieciową.
6. W polu **numer portu** wprowadź numer portu usługi docelowej. 
7. W polu **częstotliwość testu** wprowadź wartość dla częstotliwości wykonywania testu. 
8. Wybierz węzły, z których chcesz monitorować łączność sieciową do usługi. Upewnij się, że liczba agentów dodanych na test jest mniejsza niż 150. Każdy agent może testować maksymalnie 150 punktów końcowych/agentów.

    >[!NOTE]
    > W przypadku węzłów opartych na systemie Windows Server możliwość wykonywania pomiarów sieci odbywa się przy użyciu żądań opartych na protokole TCP. W przypadku węzłów opartych na kliencie systemu Windows możliwość wykonywania pomiarów sieci odbywa się przy użyciu żądań opartych na protokole ICMP. W niektórych przypadkach aplikacja docelowa blokuje przychodzące żądania oparte na protokole ICMP, gdy węzły są oparte na kliencie systemu Windows. Rozwiązanie nie może wykonać pomiarów sieci. Zalecamy używanie węzłów opartych na systemie Windows Server w takich przypadkach. 

9. Jeśli nie chcesz tworzyć zdarzeń dotyczących kondycji dla wybranych elementów, wyczyść pole wyboru **Włącz monitorowanie kondycji w celach objętych tym testem**. 
10. Wybierz pozycję warunki monitorowania. Możesz ustawić progi niestandardowe dla generowania zdarzeń kondycji, wprowadzając wartości progowe. Za każdym razem, gdy wartość warunku spadnie powyżej wartości progowej wybranej pary sieciowej lub podsieci, zostanie wygenerowane zdarzenie kondycji. 
11. Wybierz pozycję **Zapisz** , aby zapisać konfigurację. 

    ![Konfiguracje testów monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-configuration.png)



## <a name="walkthrough"></a>Przewodnik 

Przejdź do widoku pulpitu nawigacyjnego Network Performance Monitor. Aby uzyskać podsumowanie kondycji różnych utworzonych testów, zapoznaj się ze stroną **monitora łączności usług** . 

![Strona monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-blade.png)

Wybierz kafelek, aby wyświetlić szczegóły testów na stronie **testy** . W tabeli po lewej stronie można wyświetlić kondycję punktu w czasie oraz wartość czasu odpowiedzi usługi, opóźnienia sieci i utraty pakietów dla wszystkich testów. Użyj kontrolki rejestratora stanu sieci, aby wyświetlić migawkę sieci w przeszłości. Wybierz test w tabeli, którą chcesz zbadać. Na wykresach w okienku po prawej stronie można wyświetlić trend historyczny wartości utraconych, opóźnień i czasu odpowiedzi. Wybierz łącze **szczegóły testu** , aby wyświetlić wydajność z każdego węzła.

![Testy monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-tests.png)

W widoku **węzły testów** można obserwować łączność sieciową z każdego węzła. Wybierz węzeł, który ma spadek wydajności. Jest to węzeł, w którym zaobserwowano, że aplikacja działa wolno.

Należy określić, czy niska wydajność aplikacji jest spowodowana przez sieć czy problemem na zakończenie działania dostawcy aplikacji, obserwując korelację między czasem odpowiedzi aplikacji a opóźnieniem sieci. 

* **Problem z aplikacją:** Wzrost czasu odpowiedzi, ale jego spójność w opóźnieniu sieci sugeruje, że sieć działa prawidłowo, a problem może być spowodowany przez problem związany z zakończeniem działania aplikacji. 

    ![Problem z aplikacją monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-application-issue.png)

* **Problem z siecią:** Gwałtowny czas odpowiedzi z uwzględnieniem odpowiedniego przyrostu w opóźnieniu sieci sugeruje, że wzrost czasu odpowiedzi może być spowodowany zwiększeniem opóźnienia sieci. 

    ![Problem z siecią monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-network-issue.png)

Po ustaleniu, że problem jest spowodowany przez sieć, wybierz łącze widok **topologii** , aby zidentyfikować przeskok problematycznych na mapie topologii. Na poniższej ilustracji przedstawiono przykład. Z 105-całkowite opóźnienie między węzłem a punktem końcowym aplikacji 96 MS jest spowodowane przeskokiem oznaczonym kolorem czerwonym. Po zidentyfikowaniu przeskoku problematycznych możesz podejmować działania naprawcze. 

![Topologia punktu końcowego monitora łączności usług](media/network-performance-monitor-service-endpoint/service-endpoint-topology.png)

## <a name="diagnostics"></a>Diagnostyka 

Jeśli zauważysz nieprawidłowość, wykonaj następujące kroki:

* Jeśli czas odpowiedzi usługi, utrata sieci i opóźnienie są wyświetlane jako NA, może to być spowodowane jedną z następujących przyczyn:

    - Aplikacja nie działa.
    - Węzeł używany do sprawdzania łączności sieciowej z usługą nie działa.
    - Wartość docelowa wprowadzona w konfiguracji testu jest niepoprawna.
    - Węzeł nie ma żadnej łączności sieciowej.

* Jeśli zostanie wyświetlony prawidłowy czas odpowiedzi usługi, ale utrata połączenia sieciowego, a także opóźnienia są wyświetlane jako NA, co najmniej jedna z następujących przyczyn może być przyczyną:

    - Jeśli węzeł używany do sprawdzania łączności sieciowej z usługą jest komputerem klienckim z systemem Windows, usługa docelowa blokuje żądania protokołu ICMP lub Zapora sieciowa blokuje żądania protokołu ICMP, które pochodzą z tego węzła.
    - Pole wyboru **Wykonaj pomiary sieci** jest puste w konfiguracji testu. 

* Jeśli czas odpowiedzi usługi wynosi, ale utrata sieci, a opóźnienie jest prawidłowe, usługa docelowa może nie być aplikacją sieci Web. Edytuj konfigurację testu i wybierz typ testowy jako **Sieć** zamiast **sieci Web**. 

* Jeśli aplikacja działa wolno, ustal, czy niska wydajność aplikacji wynika z faktu, że sieć lub występuje problem z zakończeniem działania dostawcy aplikacji.

## <a name="gcc-office-urls-for-us-government-customers"></a>Adresy URL biura w zatoce dla klientów rządowych USA
W przypadku regionu Wirginia dla instytucji rządowych Stanów Zjednoczonych tylko DOD adresy URL są wbudowane w NPM. Klienci korzystający z adresów URL w usłudze w zatoce muszą tworzyć niestandardowe testy i osobno dodawać poszczególne adresy URL.

| Pole | GCC |
|:---   |:--- |
| Portal Office 365 i udostępnione | portal.apps.mil |
| Uwierzytelnianie i tożsamość pakietu Office 365 | * login.microsoftonline.us <br> * api.login.microsoftonline.com <br> * clientconfig.microsoftonline-p.net <br> * login.microsoftonline.com <br> * login.microsoftonline-p.com <br> * login.windows.net <br> * loginex.microsoftonline.com <br> * login-us.microsoftonline.com <br> * nexus.microsoftonline-p.com <br> * mscrl.microsoft.com <br> * secure.aadcdn.microsoftonline-p.com |
| Office Online | * adminwebservice.gov.us.microsoftonline.com <br>  * adminwebservice-s1-bn1a.microsoftonline.com <br> * adminwebservice-s1-dm2a.microsoftonline.com <br> * becws.gov.us.microsoftonline.com <br> * provisioningapi.gov.us.microsoftonline.com <br> * officehome.msocdn.us <br> * prod.msocdn.us <br> * portal.office365.us <br> * webshell.suite.office365.us <br> * www. office365.us <br> * activation.sls.microsoft.com <br> * crl.microsoft.com <br> * go.microsoft.com <br> * insertmedia.bing.office.net <br> * ocsa.officeapps.live.com <br> * ocsredir.officeapps.live.com <br> * ocws.officeapps.live.com <br> * office15client.microsoft.com <br>* officecdn.microsoft.com <br> * officecdn.microsoft.com.edgesuite.net <br> * officepreviewredir.microsoft.com <br> * officeredir.microsoft.com <br> * ols.officeapps.live.com  <br> * r.office.microsoft.com <br> * cdn.odc.officeapps.live.com <br> * odc.officeapps.live.com <br> * officeclient.microsoft.com |
| Exchange Online | * outlook.office365.us <br> * attachments.office365-net.us <br> * autodiscover-s.office365.us <br> * manage.office365.us <br> * scc.office365.us |
| Zespoły firmy Microsoft | gov.teams.microsoft.us | 

## <a name="next-steps"></a>Następne kroki
[Wyszukaj dzienniki](../logs/log-query-overview.md) , aby wyświetlić szczegółowe rekordy danych wydajności sieci.