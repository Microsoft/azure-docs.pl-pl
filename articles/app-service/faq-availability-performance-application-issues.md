---
title: Często zadawane pytania dotyczące wydajności aplikacji
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące dostępności, wydajności i problemów z aplikacjami w programie Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: dfaeee6a6e2a9728d7e63fb5681c487fbbd6139e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88959004"
---
# <a name="application-performance-faqs-for-web-apps-in-azure"></a>Często zadawane pytania dotyczące wydajności aplikacji dla Web Apps na platformie Azure

> [!NOTE]
> Niektóre z poniższych wskazówek mogą funkcjonować tylko w systemie Windows lub Linux App Services. Na przykład system Linux App Services domyślnie uruchamiany w trybie 64-bitowym.
>

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące problemów z wydajnością aplikacji [Web Apps funkcji Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-is-my-app-slow"></a>Dlaczego moja aplikacja jest powolna?

Wiele czynników może przyczynić się do spowolnienia wydajności aplikacji. Aby uzyskać szczegółowe instrukcje dotyczące rozwiązywania problemów, zobacz [Rozwiązywanie problemów z niską wydajnością aplikacji sieci Web](troubleshoot-performance-degradation.md).

## <a name="how-do-i-troubleshoot-a-high-cpu-consumption-scenario"></a>Jak mogę Rozwiązywanie problemów z dużym scenariuszem zużycia procesora CPU?

W przypadku niektórych dużych scenariuszy użycia procesora CPU aplikacja może naprawdę wymagać większej ilości zasobów obliczeniowych. W takim przypadku Rozważ przeskalowanie do wyższej warstwy usług, aby aplikacja mogła pobierać wszystkie potrzebne zasoby. Czasami duże użycie procesora CPU może być spowodowane przez złą pętlę lub przez zaawansowaną technikę kodowania. Uzyskanie wglądu w informacje o tym, co wyzwala użycie procesora CPU, jest procesem dwuetapowym. Najpierw Utwórz zrzut procesu, a następnie Przeanalizuj zrzut procesu. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie pliku zrzutu w celu zapewnienia wysokiego użycia procesora CPU dla Web Apps](/archive/blogs/asiatech/how-to-capture-dump-when-intermittent-high-cpu-happens-on-azure-web-app).

## <a name="how-do-i-troubleshoot-a-high-memory-consumption-scenario"></a>Jak mogę Rozwiązywanie problemów dotyczących dużego użycia pamięci?

W niektórych dużych scenariuszach zużycia pamięci aplikacja może naprawdę wymagać większej ilości zasobów obliczeniowych. W takim przypadku Rozważ przeskalowanie do wyższej warstwy usług, aby aplikacja mogła pobierać wszystkie potrzebne zasoby. Czasami usterka w kodzie może spowodować przeciek pamięci. Sposób tworzenia kodu może również zwiększyć zużycie pamięci. Uzyskanie wglądu w informacje o tym, co wyzwala duże użycie pamięci, jest procesem dwuetapowym. Najpierw Utwórz zrzut procesu, a następnie Przeanalizuj zrzut procesu. Diagnostykę awarii z galerii rozszerzeń witryn Azure można efektywnie wykonać obie te czynności. Aby uzyskać więcej informacji, zobacz [przechwytywanie i analizowanie pliku zrzutu w celu przeprowadzenia sporadycznej dużej ilości pamięci dla Web Apps](/archive/blogs/asiatech/how-to-capture-and-analyze-dump-for-intermittent-high-memory-on-azure-web-app).

## <a name="how-do-i-automate-app-service-web-apps-by-using-powershell"></a>Jak mogę Automatyzowanie App Service aplikacji sieci Web przy użyciu programu PowerShell?

Za pomocą poleceń cmdlet programu PowerShell można zarządzać App Service aplikacjami sieci Web i obsługiwać je. W naszym wpisie w blogu [Automatyzowanie aplikacji sieci Web hostowanych w Azure App Service przy użyciu programu PowerShell](/archive/blogs/puneetgupta/automating-webapps-hosted-in-azure-app-service-through-powershell-arm-way)opisujemy sposób używania poleceń cmdlet programu PowerShell opartych na Azure Resource Manager do automatyzowania typowych zadań. Wpis w blogu zawiera również przykładowy kod dla różnych zadań zarządzania aplikacjami sieci Web. Opisy i składnia wszystkich poleceń cmdlet App Service Web Apps można znaleźć w temacie [AZ. websites](/powershell/module/az.websites).

## <a name="how-do-i-view-my-web-apps-event-logs"></a>Jak mogę wyświetlić dzienniki zdarzeń aplikacji sieci Web?

Aby wyświetlić dzienniki zdarzeń aplikacji sieci Web:

1. Zaloguj się do **witryny sieci Web kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. W menu wybierz kolejno polecenia **Debuguj konsolę**  >  **cmd**.
3. Wybierz folder **LogFiles** .
4. Aby wyświetlić dzienniki zdarzeń, wybierz ikonę ołówka obok pozycji **eventlog.xml**.
5. Aby pobrać dzienniki, uruchom polecenie cmdlet programu PowerShell `Save-AzureWebSiteLog -Name webappname` .

## <a name="how-do-i-capture-a-user-mode-memory-dump-of-my-web-app"></a>Jak mogę przechwycić zrzut pamięci w trybie użytkownika aplikacji sieci Web?

Aby przechwycić zrzut pamięci w trybie użytkownika aplikacji sieci Web:

1. Zaloguj się do **witryny sieci Web kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Wybierz menu **Eksploratora procesów** .
3. Kliknij prawym przyciskiem myszy proces **w3wp.exe** lub proces WebJob.
4. Wybierz pozycję **Pobierz zrzut pamięci**  >  **pełny zrzut**.

## <a name="how-do-i-view-process-level-info-for-my-web-app"></a>Jak mogę wyświetlić informacje na poziomie procesu dla mojej aplikacji sieci Web?

Dostępne są dwie opcje wyświetlania informacji na poziomie procesu dla aplikacji sieci Web:

*   W witrynie Azure Portal:
    1. Otwórz **Eksploratora procesów** dla aplikacji sieci Web.
    2. Aby wyświetlić szczegóły, wybierz proces **w3wp.exe** .
*   W konsoli kudu:
    1. Zaloguj się do **witryny sieci Web kudu** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
    2. Wybierz menu **Eksploratora procesów** .
    3. Dla procesu **w3wp.exe** wybierz pozycję **Właściwości**.

## <a name="when-i-browse-to-my-app-i-see-error-403---this-web-app-is-stopped-how-do-i-resolve-this"></a>Po przejściu do mojej aplikacji pojawia się komunikat "Błąd 403 — ta aplikacja sieci Web została zatrzymana". Jak rozwiązać ten problem?

W przypadku tego błędu mogą wystąpić trzy warunki:

* Aplikacja sieci Web osiągnęła limit rozliczeniowy, a witryna została wyłączona.
* Aplikacja sieci Web została zatrzymana w portalu.
* Aplikacja sieci Web osiągnęła limit przydziału zasobów, który może być stosowany do planu usług w warstwie Bezpłatna lub współdzielona.

Aby zobaczyć, co jest przyczyną błędu i rozwiązać ten problem, wykonaj czynności opisane w [Web Apps: "błąd 403 — ta aplikacja sieci Web została zatrzymana"](/archive/blogs/waws/azure-web-apps-error-403-this-web-app-is-stopped).

## <a name="where-can-i-learn-more-about-quotas-and-limits-for-various-app-service-plans"></a>Gdzie można dowiedzieć się więcej o limitach przydziału i limitów dla różnych planów App Service?

Informacje o przydziałach i limitach można znaleźć w temacie [App Service limitów](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

## <a name="how-do-i-decrease-the-response-time-for-the-first-request-after-idle-time"></a>Jak mogę skrócić czas odpowiedzi dla pierwszego żądania po czasie bezczynności?

Domyślnie aplikacje sieci Web są zwalniane, jeśli są bezczynne przez określony czas. W ten sposób system może zaoszczędzić zasoby. Minusem polega na tym, że odpowiedź na pierwsze żądanie po wyładowaniu aplikacji sieci Web jest większa, co pozwala aplikacji sieci Web ładować i uruchamiać odpowiedzi. W planach usług w warstwach Podstawowa i Standardowa można włączyć ustawienie **zawsze włączone** , aby zachować zawsze załadowane aplikacje. Eliminuje to dłuższe czasy ładowania, gdy aplikacja jest bezczynna. Aby zmienić ustawienie **zawsze włączone** :

1. W Azure Portal przejdź do aplikacji sieci Web.
2. Wybierz **konfigurację**
3. Wybierz pozycję **Ustawienia ogólne**.
4. Dla opcji **zawsze włączone** wybierz pozycję **włączone**.

## <a name="how-do-i-turn-on-failed-request-tracing"></a>Jak mogę włączyć śledzenia nieudanych żądań?

Aby włączyć śledzenie nieudanych żądań:

1. W Azure Portal przejdź do aplikacji sieci Web.
3. Wybierz kolejno pozycje **wszystkie ustawienia**  >  **dzienniki diagnostyki**.
4. W przypadku **śledzenia nieudanych żądań** wybierz pozycję **włączone**.
5. Wybierz pozycję **Zapisz**.
6. W bloku aplikacja sieci Web wybierz pozycję **Narzędzia**.
7. Wybierz pozycję **Visual Studio Online**.
8. Jeśli to ustawienie nie jest **włączone**, wybierz pozycję **włączone**.
9. Wybierz pozycję **Przejdź**.
10. Wybierz **Web.config**.
11. W obszarze System. WebServer Dodaj tę konfigurację (aby przechwycić określony adres URL):

    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*api*" />
    <add path="*api*">
    <traceAreas>
    <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
12. Aby rozwiązać problemy związane z niską wydajnością, Dodaj tę konfigurację (Jeśli żądanie przechwytywania trwa dłużej niż 30 sekund):
    ```xml
    <system.webServer>
    <tracing> <traceFailedRequests>
    <remove path="*" />
    <add path="*">
    <traceAreas> <add provider="ASP" verbosity="Verbose" />
    <add provider="ASPNET" areas="Infrastructure,Module,Page,AppServices" verbosity="Verbose" />
    <add provider="ISAPI Extension" verbosity="Verbose" />
    <add provider="WWW Server" areas="Authentication,Security,Filter,StaticFile,CGI,Compression, Cache,RequestNotifications,Module,FastCGI" verbosity="Verbose" />
    </traceAreas>
    <failureDefinitions timeTaken="00:00:30" statusCodes="200-999" />
    </add> </traceFailedRequests>
    </tracing>
    ```
13. Aby pobrać ślady żądań zakończonych niepowodzeniem, w [portalu](https://portal.azure.com)przejdź do witryny sieci Web.
15. Wybierz pozycję **Narzędzia**  >  **kudu**  >  **Przejdź**.
18. W menu wybierz kolejno polecenia **Debuguj konsolę**  >  **cmd**.
19. Wybierz folder **LogFiles** , a następnie wybierz folder o nazwie rozpoczynającej się od **W3SVC**.
20. Aby wyświetlić plik XML, wybierz ikonę ołówka.

## <a name="i-see-the-message-worker-process-requested-recycle-due-to-percent-memory-limit-how-do-i-address-this-issue"></a>Wyświetlany jest komunikat "zażądano odtworzenia procesu roboczego z powodu limitu" procent pamięci ". Jak mogę rozwiązać ten problem?

Maksymalna dostępna ilość pamięci dla procesu 32-bitowego (nawet z 64-bitowym systemem operacyjnym) wynosi 2 GB. Domyślnie proces roboczy jest ustawiony na 32-bitowy w App Service (w celu zapewnienia zgodności ze starszymi aplikacjami sieci Web).

Rozważ przełączenie na 64-bitowe procesów, aby można było korzystać z dodatkowej pamięci dostępnej w roli procesu roboczego sieci Web. Spowoduje to wyzwolenie ponownego uruchomienia aplikacji sieci Web, więc zaplanuj odpowiednią wartość.

Należy również zauważyć, że środowisko 64-bitowe wymaga planu usługi Basic lub standard. Plany bezpłatna i współdzielona są zawsze uruchamiane w środowisku 32-bitowym.

Aby uzyskać więcej informacji, zobacz [Konfigurowanie aplikacji sieci Web w App Service](configure-common.md).

## <a name="why-does-my-request-time-out-after-230-seconds"></a>Dlaczego moje żądanie przekroczyło limit czasu po 230 sekundach?

Azure Load Balancer ma domyślne ustawienia limitu czasu bezczynności wynoszące cztery minuty. Jest to zazwyczaj rozsądny limit czasu odpowiedzi dla żądania sieci Web. Jeśli aplikacja sieci Web wymaga przetwarzania w tle, zalecamy użycie Azure WebJobs. Aplikacja sieci Web platformy Azure może wywoływać Zadania WebJob i otrzymywać powiadomienia o zakończeniu przetwarzania w tle. Można wybierać spośród wielu metod do korzystania z zadań WebJob, w tym kolejek i wyzwalaczy.

Zadania WebJob są przeznaczone do przetwarzania w tle. W ramach zadania WebJob można wykonać dowolną ilość przetwarzania w tle. Aby uzyskać więcej informacji o zadaniach WebJob, zobacz [Uruchamianie zadań w tle za pomocą zadań WebJob](webjobs-create.md).

## <a name="aspnet-core-applications-that-are-hosted-in-app-service-sometimes-stop-responding-how-do-i-fix-this-issue"></a>ASP.NET Core aplikacje hostowane w App Service czasem nie odpowiadają. Jak mogę rozwiązać ten problem?

Znany problem z wcześniejszą [wersją Kestrel](https://github.com/aspnet/KestrelHttpServer/issues/1182) może spowodować, że aplikacja ASP.NET Core 1,0, która jest hostowana w App Service, aby sporadycznie przestać reagować. Ten komunikat może również zostać wyświetlony: "określona aplikacja CGI napotkała błąd i serwer zakończył proces".

Ten problem został rozwiązany w wersji Kestrel 1.0.2. Ta wersja jest uwzględniona w ASP.NET Core aktualizacji 1.0.3. Aby rozwiązać ten problem, upewnij się, że Twoje zależności aplikacji zostały zaktualizowane, tak aby korzystały z Kestrel 1.0.2. Alternatywnie możesz użyć jednego z dwóch obejść, które opisano w wpisie w blogu [ASP.NET Core 1,0 wolnych problemów z wydajności w aplikacjach App Service Web Apps](/archive/blogs/waws/asp-net-core-slow-perf-issues-on-azure-websites).


## <a name="i-cant-find-my-log-files-in-the-file-structure-of-my-web-app-how-can-i-find-them"></a>Nie mogę znaleźć plików dziennika w strukturze plików mojej aplikacji sieci Web. Jak można je znaleźć?

W przypadku korzystania z funkcji lokalnej pamięci podręcznej App Service, ma to na przykład strukturę folderu LogFiles i foldery danych dla wystąpienia App Service. Gdy lokalna pamięć podręczna jest używana, podfoldery są tworzone w dziennikach i w folderach danych magazynu. Podfoldery używają wzorca nazewnictwa "unikatowy identyfikator" + sygnatura czasowa. Każdy podfolder odnosi się do wystąpienia maszyny wirtualnej, w którym działa aplikacja sieci Web lub która została uruchomiona.

Aby określić, czy korzystasz z lokalnej pamięci podręcznej, sprawdź kartę **Ustawienia aplikacji** App Service. Jeśli lokalna pamięć podręczna jest używana, ustawienie aplikacji `WEBSITE_LOCAL_CACHE_OPTION` jest ustawione na `Always` .

Jeśli nie korzystasz z lokalnej pamięci podręcznej i występuje ten problem, Prześlij żądanie pomocy technicznej.

## <a name="i-see-the-message-an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions-how-do-i-resolve-this"></a>Zobaczym komunikat "podjęto próbę uzyskania dostępu do gniazda w sposób zabroniony przez jego uprawnienia dostępu". Jak rozwiązać ten problem?

Ten błąd występuje zazwyczaj, gdy wychodzące połączenia TCP w wystąpieniu maszyny wirtualnej zostaną wyczerpane. W App Service limity są wymuszane dla maksymalnej liczby połączeń wychodzących, które można wykonać dla każdego wystąpienia maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [limity liczbowe między maszynami](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)wirtualnymi.

Ten błąd może również wystąpić, jeśli próbujesz uzyskać dostęp do adresu lokalnego z aplikacji. Aby uzyskać więcej informacji, zobacz [żądania adresów lokalnych](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#local-address-requests).

Aby uzyskać więcej informacji o połączeniach wychodzących w aplikacji sieci Web, zobacz wpis w blogu dotyczący [połączeń wychodzących z usługą Azure Websites](https://www.freekpaans.nl/2015/08/starving-outgoing-connections-on-windows-azure-web-sites/).

## <a name="how-do-i-use-visual-studio-to-remote-debug-my-app-service-web-app"></a>Jak mogę użyć programu Visual Studio do zdalnego debugowania mojej aplikacji internetowej App Service?

Aby uzyskać szczegółowy przewodnik, w którym pokazano, jak debugować aplikację sieci Web przy użyciu programu Visual Studio, zobacz [zdalne debugowanie aplikacji internetowej App Service](/archive/blogs/benjaminperkins/remote-debug-your-azure-app-service-web-app).