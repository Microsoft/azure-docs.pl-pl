---
title: Usługa Azure Application Insights Snapshot Debugger dla aplikacji platformy .NET
description: Migawki debugowania są automatycznie zbierane, gdy wyjątki są zgłaszane w produkcyjnych aplikacjach .NET
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: ab142b4e0a2d5486727ffc71fc94ae4944513052
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88935810"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Debugowanie migawek wyjątków w aplikacjach .NET
Gdy wystąpi wyjątek, można automatycznie zebrać migawkę debugowania z działającej aplikacji sieci Web. Migawka pokazuje stan kodu źródłowego i zmienne w momencie, gdy wyjątek został zgłoszony. Snapshot Debugger w [usłudze Azure Application Insights](./app-insights-overview.md) monitoruje dane telemetryczne wyjątku z aplikacji sieci Web. Zbiera migawki na najważniejszych wyjątkach, aby uzyskać informacje potrzebne do diagnozowania problemów w środowisku produkcyjnym. Dołącz [pakiet NuGet modułu zbierającego migawki](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) do aplikacji i opcjonalnie Skonfiguruj parametry kolekcji w [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Migawki są wyświetlane na [wyjątkach](./asp-net-exceptions.md) w portalu Application Insights.

Migawki debugowania można wyświetlić w portalu, aby zobaczyć stos wywołań i sprawdzić zmienne w każdej ramce tego stosu. Aby uzyskać bardziej zaawansowane środowisko debugowania z kodem źródłowym, Otwórz migawki w programie Visual Studio 2019 Enterprise. W programie Visual Studio można także [ustawić punkty przyciągania, aby interaktywnie podejmować migawki](https://aka.ms/snappoint) bez oczekiwania na wyjątek.

Migawki debugowania są przechowywane przez 15 dni. Te zasady przechowywania są ustawiane dla poszczególnych aplikacji. Jeśli musisz zwiększyć tę wartość, możesz poprosić o zwiększenie, otwierając przypadek pomocy technicznej w Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Włącz Snapshot Debugger Application Insights dla aplikacji
Kolekcja migawek jest dostępna dla:
* .NET Framework i aplikacje ASP.NET z systemem .NET Framework 4,5 lub nowszym.
* Aplikacje .NET Core 2,0 i ASP.NET Core 2,0 działające w systemie Windows.

Obsługiwane są następujące środowiska:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Platforma Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem operacyjnym 4 lub nowszym
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) działające w systemie Windows Server 2012 R2 lub nowszym
* [Platforma Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) z systemem Windows Server 2012 R2 lub nowszym lub Windows 8.1 lub nowszym

> [!NOTE]
> Aplikacje klienckie (na przykład WPF, Windows Forms lub platformy UWP) nie są obsługiwane.

Jeśli włączono Snapshot Debugger, ale nie widzisz migawek, zapoznaj się [z naszym przewodnikiem rozwiązywania problemów](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Udzielenie uprawnień

Dostęp do migawek jest chroniony przez kontrolę dostępu na podstawie ról (RBAC). Aby przeprowadzić inspekcję migawki, najpierw właściciel subskrypcji musi Cię dodać do niezbędnej roli.

> [!NOTE]
> Właściciele i współautorzy nie mają tej roli automatycznie. Jeśli chcesz wyświetlić migawki, muszą dodać siebie do roli.

Właściciele subskrypcji powinni przypisać `Application Insights Snapshot Debugger` rolę do użytkowników, którzy będą przeprowadzali inspekcję migawek. Tę rolę można przypisać do poszczególnych użytkowników lub grup według właścicieli subskrypcji dla docelowego zasobu Application Insights lub jego grupy zasobów lub subskrypcji.

1. Przejdź do zasobu Application Insights w Azure Portal.
1. Kliknij pozycję **Kontrola dostępu (IAM)** .
1. Kliknij przycisk **+ Dodaj przypisanie roli** .
1. Wybierz pozycję **Application Insights Snapshot Debugger** z listy rozwijanej **role** .
1. Wyszukaj i wprowadź nazwę użytkownika, który ma zostać dodany.
1. Kliknij przycisk **Zapisz** , aby dodać użytkownika do roli.


> [!IMPORTANT]
> Migawki mogą potencjalnie zawierać informacje osobiste i inne poufne w wartościach zmiennych i parametrów.

## <a name="view-snapshots-in-the-portal"></a>Wyświetlanie migawek w portalu

Gdy w aplikacji wystąpił wyjątek, a migawka została utworzona, należy utworzyć migawki do wyświetlenia. Wykonanie migawki w portalu może potrwać od 5 do 10 minut od wyjątku. Aby wyświetlić migawki, w okienku **awaria** wybierz przycisk **operacje** podczas wyświetlania karty **operacje** lub wybierz przycisk **wyjątki** podczas wyświetlania karty **wyjątki** :

![Strona błędów](./media/snapshot-debugger/failures-page.png)

Wybierz operację lub wyjątek w okienku po prawej stronie, aby otworzyć okienko **szczegóły końca transakcji** , a następnie wybierz zdarzenie wyjątku. Jeśli dla danego wyjątku dostępna jest migawka, w okienku po prawej stronie zostanie wyświetlony przycisk **Otwórz migawkę debugowania** z szczegółowymi informacjami dotyczącymi [wyjątku](./asp-net-exceptions.md).

![Przycisk otwierania migawki debugowania dla wyjątku](./media/snapshot-debugger/e2e-transaction-page.png)

W widoku migawki debugowania zobaczysz stos wywołań i okienko zmiennych. Po wybraniu ramek stosu wywołań w okienku stosu wywołań można wyświetlić zmienne lokalne i parametry dla tego wywołania funkcji w okienku zmienne.

![Wyświetl migawkę debugowania w portalu](./media/snapshot-debugger/open-snapshot-portal.png)

Migawki mogą zawierać informacje poufne i domyślnie nie są widoczne. Aby wyświetlić migawki, musisz mieć `Application Insights Snapshot Debugger` przypisaną rolę.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Wyświetlanie migawek w programie Visual Studio 2017 Enterprise lub nowszym
1. Kliknij przycisk **Pobierz migawkę** , aby pobrać `.diagsession` plik, który może zostać otwarty przez Visual Studio Enterprise.

2. Aby otworzyć `.diagsession` plik, należy zainstalować składnik programu Snapshot Debugger Visual Studio. Składnik Snapshot Debugger jest wymaganym składnikiem obciążenia ASP.net w programie Visual Studio i można go wybrać z listy poszczególnych składników w Instalatorze programu Visual Studio. Jeśli używasz wersji programu Visual Studio starszej niż wersja 15,5 programu Visual 2017 Studio, musisz zainstalować rozszerzenie z [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Po otwarciu pliku migawki zostanie wyświetlona strona debugowanie minizrzutu w programie Visual Studio. Kliknij pozycję **Debuguj kod zarządzany** , aby rozpocząć debugowanie migawki. Migawka zostanie otwarta w wierszu kodu, w którym został zgłoszony wyjątek, aby można było debugować bieżący stan procesu.

    ![Wyświetl migawkę debugowania w programie Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Pobrana migawka zawiera pliki symboli, które zostały znalezione na serwerze aplikacji sieci Web. Te pliki symboli są wymagane do skojarzenia danych migawki z kodem źródłowym. W przypadku aplikacji App Service upewnij się, że włączono wdrażanie symboli podczas publikowania aplikacji sieci Web.

## <a name="how-snapshots-work"></a>Jak działają migawki

Snapshot Collector jest implementowana jako [procesor Telemetria usługi Application Insights](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Gdy aplikacja zostanie uruchomiona, procesor telemetrii Snapshot Collector zostanie dodany do potoku telemetrii aplikacji.
Za każdym razem, gdy aplikacja wywołuje element [trackexception](./asp-net-exceptions.md#exceptions), Snapshot Collector oblicza identyfikator problemu na podstawie typu zgłaszanego wyjątku i metody zgłaszania.
Za każdym razem, gdy aplikacja wywołuje metodę Trackexception, licznik jest zwiększany dla odpowiedniego identyfikatora problemu. Gdy licznik osiągnie `ThresholdForSnapshotting` wartość, identyfikator problemu zostanie dodany do planu kolekcji.

Snapshot Collector również monitoruje wyjątki, ponieważ są zgłaszane przez zasubskrybowanie zdarzenia [AppDomain. CurrentDomain —. FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) . Gdy zdarzenie zostanie wyzwolone, identyfikator problemu wyjątku jest obliczany i porównywany z identyfikatorami problemów w planie kolekcji.
Jeśli istnieje dopasowanie, tworzona jest migawka uruchomionego procesu. Do migawki jest przypisany unikatowy identyfikator, a wyjątek jest sygnaturą tego identyfikatora. Po powrocie procedury obsługi FirstChanceException, zgłoszony wyjątek jest przetwarzany jako normalny. Ostatecznie wyjątek osiągnie metodę Trackexception, a wraz z identyfikatorem migawki jest raportowany do Application Insights.

Główny proces nadal działa i obsługuje ruch do użytkowników z małym zakłóceniem. W tym czasie migawka jest przekazana do procesu obiektu przekazującego migawek. Migawka obiektu przekazującego tworzy minizrzutu i przekazuje ją do Application Insights wraz z wszelkimi odpowiednimi plikami symboli (. pdb).

> [!TIP]
> - Migawka procesu jest zawieszonym klonem uruchomionego procesu.
> - Tworzenie migawki trwa od 10 do 20 milisekund.
> - Wartość domyślna dla `ThresholdForSnapshotting` wynosi 1. Jest to również wartość minimalna. W związku z tym aplikacja musi wyzwolić ten sam wyjątek **dwa razy** przed utworzeniem migawki.
> - Ustaw `IsEnabledInDeveloperMode` wartość true, jeśli chcesz generować migawki podczas debugowania w programie Visual Studio.
> - Szybkość tworzenia migawek jest ograniczona przez `SnapshotsPerTenMinutesLimit` ustawienie. Domyślnie limit jest jedną migawką co dziesięć minut.
> - Maksymalnie 50 migawek dziennie można przekazać.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi 15 dni. Dla każdego wystąpienia Application Insights dozwolonych jest maksymalnie 50 migawek dziennie.

### <a name="publish-symbols"></a>Publikuj symbole
Snapshot Debugger wymaga plików symboli na serwerze produkcyjnym, aby zdekodować zmienne i zapewnić środowisko debugowania w programie Visual Studio.
Wersja 15,2 (lub nowsza) programu Visual Studio 2017 publikuje symbole dla kompilacji wydań domyślnie, gdy publikuje je w App Service. W poprzednich wersjach należy dodać następujący wiersz do pliku profilu publikowania, `.pubxml` Aby symbole zostały opublikowane w trybie wydania:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

W przypadku obliczeń na platformie Azure i innych typów upewnij się, że pliki symboli znajdują się w tym samym folderze głównej aplikacji. dll (zazwyczaj `wwwroot/bin` ) lub są dostępne w bieżącej ścieżce.

> [!NOTE]
> Więcej informacji o różnych opcjach symboli dostępnych można znaleźć w [dokumentacji programu Visual Studio](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Aby uzyskać najlepsze wyniki, zalecamy użycie "Full", "Portable" lub "Embedded".

### <a name="optimized-builds"></a>Zoptymalizowane kompilacje
W niektórych przypadkach zmienne lokalne nie mogą być wyświetlane w kompilacjach wydania z powodu optymalizacji, które są stosowane przez kompilator JIT.
Jednak w usłudze Azure App Services Snapshot Collector może dezoptymalizować wyrzucanie metod, które są częścią planu kolekcji.

> [!TIP]
> Zainstaluj rozszerzenie witryny Application Insights w App Service, aby uzyskać pomoc w zakresie deoptymalizacji.

## <a name="next-steps"></a>Następne kroki
Włącz Snapshot Debugger Application Insights dla aplikacji:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Usługi Service Fabric platformy Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokalne maszyny wirtualne lub fizyczne](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Poza Application Insights Snapshot Debugger:
 
* [Ustaw punkty przyciągania w kodzie](/visualstudio/debugger/debug-live-azure-applications) , aby uzyskać migawki bez oczekiwania na wyjątek.
* [Diagnozuj wyjątki w aplikacjach sieci Web](./asp-net-exceptions.md) wyjaśnia, jak zwiększyć liczbę wyjątków widocznych dla Application Insights.
* [Inteligentne wykrywanie](./proactive-diagnostics.md) automatycznie wykrywa anomalie wydajności.

