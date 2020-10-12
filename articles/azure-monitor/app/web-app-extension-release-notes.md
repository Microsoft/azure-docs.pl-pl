---
title: Informacje o wersji rozszerzenia aplikacji sieci Web platformy Azure — Application Insights
description: Informacje o wersji rozszerzenia Web Apps platformy Azure dla Instrumentacji środowiska uruchomieniowego z Application Insights.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: b9da3b0647d5abe60dd8f6cb21163ff21f07205b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86539724"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Informacje o wersji rozszerzenia aplikacji internetowej platformy Azure dla Application Insights

Ten artykuł zawiera informacje o wersjach rozszerzenia Web Apps platformy Azure dla Instrumentacji środowiska uruchomieniowego z Application Insights. Ma to zastosowanie tylko w przypadku wstępnie zainstalowanych rozszerzeń.

[Dowiedz się](azure-web-apps.md) więcej o rozszerzeniu aplikacji sieci Web platformy Azure dla Application Insights.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

- Jak sprawdzić, która wersja rozszerzenia jest obecnie włączona?
    - Przejdź do adresu `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Aby uzyskać więcej informacji [, zobacz Przewodnik rozwiązywania problemów krok po kroku dotyczący monitorowania na podstawie rozszerzeń i agentów](./azure-web-apps.md?tabs=net#troubleshooting) .

- Co zrobić, jeśli używam prywatnych rozszerzeń?
    - Odinstaluj rozszerzenia lokacji prywatnych, ponieważ nie jest już obsługiwane.

## <a name="release-notes"></a>Informacje o wersji

### <a name="2833"></a>2.8.33

- .NET, .NET Core, Java i Node.js Agents oraz rozszerzenie systemu Windows: obsługa dla suwerennych chmur. Parametry połączeń mogą służyć do wysyłania danych do Niesuwerennych chmur.

### <a name="2831"></a>2.8.31

- Agent ASP.NET Core: Rozwiązano problem związany z jednym z zaktualizowanych odwołań Application Insights SDK (zobacz znane problemy dotyczące 2.8.26). Jeśli nieprawidłowa wersja programu `System.Diagnostics.DiagnosticSource.dll` jest już załadowana przez środowisko uruchomieniowe, rozszerzenie bezkodowe nie spowoduje teraz awarii aplikacji i wycofa się. W przypadku klientów, których dotyczy ten problem, zaleca się usunięcie `System.Diagnostics.DiagnosticSource.dll` z folderu bin lub starszej wersji rozszerzenia przez ustawienie "ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24"; w przeciwnym razie monitorowanie aplikacji nie jest włączone.

### <a name="2826"></a>2.8.26

- Agent ASP.NET Core: Rozwiązano problem związany z aktualizacją Application Insights SDK. Agent nie spróbuje załadować `AiHostingStartup` , jeśli ApplicationInsights.dll znajduje się już w folderze bin. Rozwiązuje to problemy związane z odbiciem za pośrednictwem zestawu \<AiHostingStartup\> . GetTypes ().
- Znane problemy: można `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` zgłosić wyjątek, jeśli `DiagnosticSource` zostanie załadowana inna wersja biblioteki DLL. Może się tak zdarzyć na przykład wtedy, gdy występuje `System.Diagnostics.DiagnosticSource.dll` w folderze publikacji. Jako środek zaradczy Użyj poprzedniej wersji rozszerzenia, ustawiając ustawienia aplikacji w usługach App Services: ApplicationInsightsAgent_EXTENSIONVERSION = 2.8.24.

### <a name="2824"></a>2.8.24

- Odpakowanie wersji 2.8.21.

### <a name="2823"></a>2.8.23

- Dodano obsługę monitorowania bezkodowego ASP.NET Core 3,0.
- Zaktualizowano zestaw SDK ASP.NET Core [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) dla środowiska uruchomieniowego w wersji 2,1, 2,2 i 3,0. Aplikacje ukierunkowane na platformę .NET Core 2,0 nadal używają elementu 2.1.1 zestawu SDK.

### <a name="2814"></a>2.8.14

- Zaktualizowano wersję zestawu SDK ASP.NET Core od 2.3.0 do najnowszej (b) dla aplikacji przeznaczonych dla platformy .NET Core 2,1, 2,2. Aplikacje ukierunkowane na platformę .NET Core 2,0 nadal używają elementu 2.1.1 zestawu SDK.

### <a name="2812"></a>2.8.12

- Obsługa aplikacji ASP.NET Core 2,2.
- Naprawiono usterkę w rozszerzeniu ASP.NET Core powodującą iniekcję zestawu SDK nawet wtedy, gdy aplikacja jest już objęta zestawem SDK. W przypadku aplikacji 2,1 i 2,2 obecność ApplicationInsights.dll w folderze aplikacji spowoduje teraz przywrócenie rozszerzenia. W przypadku aplikacji 2,0 rozszerzenie jest wyłączane tylko wtedy, gdy ApplicationInsights jest włączona z `UseApplicationInsights()` wywołaniem.

- Stała poprawka dla niekompletnej odpowiedzi HTML dla aplikacji ASP.NET Core. Ta poprawka ma teraz rozszerzoną obsługę aplikacji platformy .NET Core 2,2.

- Dodano obsługę, aby wyłączyć iniekcję kodu JavaScript dla aplikacji ASP.NET Core ( `APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting` ). W przypadku ASP.NET rdzeń, iniekcja kodu JavaScript jest domyślnie w trybie "rezygnacji", chyba że jest jawnie wyłączona. (Ustawienie domyślne jest wykonywane, aby zachować bieżące zachowanie).

- Naprawiono usterkę rozszerzenia ASP.NET Core, która spowodowała wstrzyknięcie nawet wtedy, gdy nie było obecności iKey.
- Naprawiono usterkę w logice prefiksu wersji zestawu SDK, która spowodowała nieprawidłową wersję zestawu SDK w telemetrii.

- Dodano prefiks wersji zestawu SDK dla aplikacji ASP.NET Core, aby określić sposób zbierania danych telemetrycznych.
- Stała Strona ApplicationInsights SCM — aby poprawnie pokazać wersję wstępnie zainstalowanego rozszerzenia.

### <a name="2810"></a>2.8.10

- Poprawka dla niekompletnej odpowiedzi HTML dla aplikacji ASP.NET Core.

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [dokumentacją Azure App Service](azure-web-apps.md) , aby uzyskać więcej informacji na temat konfigurowania monitorowania dla usługi Azure App Services. 
