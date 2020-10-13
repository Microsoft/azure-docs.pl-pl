---
title: Rozwiązywanie błędów HTTP 502 i HTTP 503
description: Rozwiązywanie 502 problemów z nieprawidłowym błędem usługi bramy i 503 w aplikacji hostowanej w Azure App Service.
tags: top-support-issue
keywords: 502 zła brama, usługa 503 jest niedostępna, błąd 503, błąd 502
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9a41c5912ec285094c1f47540caf3744ddd4d469
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88958330"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Rozwiązywanie problemów z błędami HTTP "502 złej Gateway" i "503 Usługa niedostępna" w Azure App Service
"502 zła brama" i "503 Usługa niedostępna" to typowe błędy w aplikacji hostowanej w [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). Ten artykuł pomaga rozwiązywać te błędy.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure w [witrynie MSDN Azure i Stack Overflow forów](https://azure.microsoft.com/support/forums/). Alternatywnie możesz także zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i kliknij pozycję **Uzyskaj pomoc techniczną**.

## <a name="symptom"></a>Objaw
Po przejściu do aplikacji zwraca błąd HTTP "502 złą bramę" lub "HTTP 503" niedostępną usługę ".

## <a name="cause"></a>Przyczyna
Ten problem często jest spowodowany problemami na poziomie aplikacji, takimi jak:

* żądania trwające długo
* Aplikacja korzystająca z dużej ilości pamięci/procesora
* awaria aplikacji z powodu wyjątku.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Rozwiązywanie problemów dotyczących błędów "502 złej Gateway" i "503 usługi niedostępnych"
Rozwiązywanie problemów można podzielić na trzy różne zadania, w kolejności sekwencyjnej:

1. [Obserwuj i monitoruj zachowanie aplikacji](#observe)
2. [Zbieranie danych](#collect)
3. [Eliminowanie problemu](#mitigate)

[App Service](overview.md) oferuje różne opcje w każdym kroku.

<a name="observe"></a>

### <a name="1-observe-and-monitor-application-behavior"></a>1. Obserwuj i monitoruj zachowanie aplikacji
#### <a name="track-service-health"></a>Śledzenie kondycji usługi
Microsoft Azure jest w trakcie każdej przerwy w świadczeniu usług lub obniżenia wydajności. Kondycję usługi można śledzić w witrynie [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji, zobacz [śledzenie kondycji usługi](../service-health/service-notifications.md).

#### <a name="monitor-your-app"></a>Monitorowanie aplikacji
Ta opcja umożliwia dowiedzieć się, czy aplikacja ma jakiekolwiek problemy. W bloku aplikacji kliknij kafelek **żądania i błędy** . W bloku **metryk** zostaną wyświetlone wszystkie metryki, które można dodać.

Niektóre metryki, które mogą być monitorowane dla aplikacji, to

* Średni zestaw roboczy pamięci
* Średni czas odpowiedzi
* Czas procesora CPU
* Zestaw roboczy pamięci
* Żądania

![Monitoruj aplikację w celu rozwiązywania błędów HTTP 502 niewłaściwej bramy i usługi 503](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Aby uzyskać więcej informacji, zobacz:

* [Monitorowanie aplikacji w Azure App Service](web-sites-monitor.md)
* [Otrzymywanie powiadomień o alertach](../azure-monitor/platform/alerts-overview.md)

<a name="collect"></a>

### <a name="2-collect-data"></a>2. Zbierz dane
#### <a name="use-the-diagnostics-tool"></a>Korzystanie z narzędzia diagnostycznego
App Service zapewnia inteligentne i interaktywne środowisko ułatwiające rozwiązywanie problemów z aplikacją bez konieczności konfigurowania. Gdy wystąpią problemy z aplikacją, narzędzie diagnostyczne wskaże, co się nie stało, aby poprowadzić Cię do właściwych informacji, aby łatwiej i szybko rozwiązywać problemy oraz rozwiązać ten problem.

Aby uzyskać dostęp do diagnostyki App Service, przejdź do aplikacji App Service lub App Service Environment w [Azure Portal](https://portal.azure.com). W lewym okienku nawigacji kliknij pozycję **Diagnozuj i rozwiąż problemy**.

#### <a name="use-the-kudu-debug-console"></a>Korzystanie z konsoli debugowania kudu
App Service obejmuje konsolę debugowania, której można użyć do debugowania, eksplorowania, przekazywania plików, a także punktów końcowych JSON do uzyskiwania informacji o środowisku. Jest to nazywane *konsolą kudu* lub *pulpitem nawigacyjnym usługi SCM* dla aplikacji.

Możesz uzyskać dostęp do tego pulpitu nawigacyjnego, przechodząc do linku **https:// &lt; nazwę aplikacji>. SCM.azurewebsites.NET/**.

Dostępne są następujące elementy kudu:

* Ustawienia środowiska dla aplikacji
* strumień dziennika
* zrzut diagnostyczny
* Konsola debugowania, w której można uruchamiać polecenia cmdlet programu PowerShell i podstawowe polecenia DOS.

Kolejną przydatną funkcją kudu jest to, że w przypadku, gdy aplikacja zgłasza wyjątki pierwszej szansy, można użyć kudu i narzędzi SysInternals ProcDump, aby utworzyć zrzuty pamięci. Zrzuty pamięci są migawkami procesu i często mogą pomóc w rozwiązywaniu bardziej skomplikowanych problemów z aplikacją.

Aby uzyskać więcej informacji na temat funkcji dostępnych w programie kudu, zobacz [temat narzędzia online usługi Azure Websites, które należy wiedzieć](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate"></a>

### <a name="3-mitigate-the-issue"></a>3. Zmniejsz problem
#### <a name="scale-the-app"></a>Skalowanie aplikacji
W Azure App Service, aby zwiększyć wydajność i przepływność, można dostosować skalę, w której jest uruchamiana aplikacja. Skalowanie aplikacji w górę obejmuje dwie powiązane akcje: zmiana planu App Service na wyższą warstwę cenową oraz skonfigurowanie niektórych ustawień po przełączeniu do wyższej warstwy cenowej.

Aby uzyskać więcej informacji na temat skalowania, zobacz [skalowanie aplikacji w Azure App Service](manage-scale-up.md).

Ponadto możesz uruchomić aplikację w więcej niż jednym wystąpieniu. Zapewnia to nie tylko możliwość przetwarzania, ale zapewnia także pewną ilość odporności na uszkodzenia. Jeśli proces ulegnie awarii na jednym wystąpieniu, inne wystąpienie nadal będzie obsługiwać żądania.

Skalowanie można ustawić jako ręczne lub automatyczne.

#### <a name="use-autoheal"></a>Użyj autozabliźnione
Autozabliźnione odtwarza proces roboczy aplikacji w oparciu o wybrane ustawienia (na przykład zmiany konfiguracji, żądania, limity pamięci lub czas wymagany do wykonania żądania). W większości przypadków odtwarzanie procesu jest najszybszym sposobem na odzyskanie sprawności po wystąpieniu problemu. Mimo że zawsze możesz ponownie uruchomić aplikację bezpośrednio w witrynie Azure Portal, automatyczne zaleczonie wykona to automatycznie. Wszystko, co musisz zrobić, to dodanie wyzwalaczy w web.config głównym aplikacji. Należy pamiętać, że te ustawienia będą działały w taki sam sposób, nawet jeśli aplikacja nie jest platformą .NET.

Aby uzyskać więcej informacji, zobacz [Funkcja autonaprawy witryn sieci Web systemu Azure](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/).

#### <a name="restart-the-app"></a>Uruchom ponownie aplikację
Jest to często najprostszy sposób na odzyskanie po jednorazowych problemach. W witrynie [Azure Portal](https://portal.azure.com/)w bloku aplikacji są dostępne opcje zatrzymania lub ponownego uruchomienia aplikacji.

 ![Uruchom ponownie aplikację, aby rozwiązać błędy HTTP 502 niewłaściwe bramy i niedostępność usługi 503](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Możesz również zarządzać aplikacją przy użyciu programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz temat [Using Azure PowerShell with Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Używanie programu Azure PowerShell z usługą Azure Resource Manager).