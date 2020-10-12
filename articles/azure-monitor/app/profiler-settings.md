---
title: Użyj okienka ustawień usługi Azure Application Insights Profiler | Microsoft Docs
description: Zobacz temat stan profilera i uruchamianie sesji profilowania
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499413"
---
# <a name="configure-application-insights-profiler"></a>Konfigurowanie Application Insights Profiler

## <a name="updated-profiler-agent"></a>Zaktualizowany Agent profilera
Funkcje wyzwalacza działają tylko w wersji 2,6 lub nowszej agenta profilera. W przypadku korzystania z Azure App Service Agent zostanie automatycznie zaktualizowany. Możesz sprawdzić, która wersja agenta jest uruchamiana, jeśli przejdziesz do adresu URL kudu witryny sieci Web i dodasz \DiagnosticServices na końcu tego elementu:  `https://yourwebsite.scm.azurewebsites.net/diagnosticservices` . Application Insights Profiler Zadania WebJob powinien być w wersji 2,6 lub nowszej. Aby wymusić uaktualnienie, należy ponownie uruchomić aplikację sieci Web. 

Jeśli używasz profilera na maszynie wirtualnej lub w usłudze w chmurze, musisz mieć zainstalowane rozszerzenie systemu Windows Diagnostyka Azure (funkcji wad) w wersji 16.0.4 lub nowszej. Możesz sprawdzić wersję programu funkcji wad, logując się na maszynie wirtualnej i sprawdzając ten katalog: C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. Nazwa katalogu jest zainstalowaną wersją programu funkcji wad. Agent maszyny wirtualnej platformy Azure zaktualizuje funkcji wad automatycznie, gdy będą dostępne nowe wersje.

## <a name="profiler-settings-page"></a>Strona ustawień profilera

Aby otworzyć okienko ustawienia usługi Azure Application Insights Profiler, przejdź do okienka wydajność Application Insights, a następnie wybierz przycisk **Konfiguruj Profiler** .

![Link do otwartej strony ustawień profilera][configure-profiler-entry]

Spowoduje to otwarcie strony, która wygląda następująco:

![Strona ustawień profilera][configure-profiler-page]

Strona **konfigurowanie Application Insights Profiler** zawiera następujące funkcje:

| Cecha | Opis |
|-|-|
Profiluj teraz | Uruchamia sesje profilowania dla wszystkich aplikacji, które są połączone z tym wystąpieniem Application Insights.
Wyzwalacze | Umożliwia skonfigurowanie wyzwalaczy, które powodują uruchomienie profilera. 
Ostatnie sesje profilowania | Wyświetla informacje o przeszłych sesjach profilowania.

## <a name="profile-now"></a>Profiluj teraz
Ta opcja umożliwia rozpoczęcie sesji profilowania na żądanie. Kliknięcie tego linku spowoduje rozpoczęcie przechwytywania profilu przez wszystkich agentów profilera wysyłających dane do tego wystąpienia Application Insights. Po upływie 5 do 10 minut sesja profilu zostanie wyświetlona na poniższej liście.

Aby użytkownik mógł ręcznie wyzwolić sesję profilera, wymaga co najmniej dostępu "zapis" w roli dla składnika Application Insights. W większości przypadków ten dostęp jest uzyskiwany automatycznie i nie trzeba wykonywać żadnych dodatkowych czynności. Jeśli masz problemy, rola zakresu subskrypcji do dodania będzie rolą "Application Insights współautor składnika". [Zobacz więcej na temat kontroli dostępu do ról przy użyciu monitorowania platformy Azure](./resources-roles-access-control.md).

## <a name="trigger-settings"></a>Ustawienia wyzwalacza
![Okno wysuwane ustawień wyzwalacza][trigger-settings-flyout]

Kliknięcie przycisku wyzwalacze na pasku menu spowoduje otwarcie pola ustawienia wyzwalacza. Można skonfigurować wyzwalacz, aby rozpocząć profilowanie, gdy procent użycia procesora lub pamięci osiągnie ustawiony poziom.

| Ustawienie | Opis |
|-|-|
Przycisk włączania/wyłączania | Włączone: profiler może być uruchamiany przez ten wyzwalacz; Wyłączone: Profiler nie zostanie uruchomiony przez ten wyzwalacz.
Próg pamięci | Gdy procent pamięci jest używany, profiler zostanie uruchomiony.
Czas trwania | Ustawia długość czasu, przez który Profiler będzie uruchamiany po wyzwoleniu.
Cooldown | Ustawia długość czasu, przez który Profiler będzie czekał przed wyzwoleniem na ponowne uruchomienie pamięci lub użycie procesora.

## <a name="recent-profiling-sessions"></a>Ostatnie sesje profilowania
Ta sekcja na stronie zawiera informacje o ostatnich sesjach profilowania. Sesja profilowania reprezentuje okres czasu, w którym Agent profilera przetworzy profil na jednej z maszyn obsługujących aplikację. Możesz otworzyć profile z sesji, klikając jeden z wierszy. Dla każdej sesji pokazujemy:

| Ustawienie | Opis |
|-|-|
Wyzwolone przez | Jak sesja została uruchomiona, przez wyzwalacz, profil teraz lub domyślne próbkowanie. 
Nazwa aplikacji | Nazwa aplikacji, która została profilowana.
Wystąpienie maszyny | Nazwa komputera, na którym uruchomiono agenta profilera.
Timestamp | Godzina przechwycenia profilu.
Śledzenie | Liczba śladów, które zostały dołączone do poszczególnych żądań.
CPU % | Procent użycia procesora CPU, który był używany podczas działania profilera.
Rozmiar | Procent pamięci używanej podczas działania profilera.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Używanie testów wydajności sieci Web do generowania ruchu do aplikacji

Możesz wyzwolić Profiler ręcznie przy użyciu jednego kliknięcia. Załóżmy, że uruchamiasz test wydajności sieci Web. Będziesz potrzebować śladów, aby zrozumieć, w jaki sposób aplikacja sieci Web działa pod obciążeniem. Posiadanie kontroli nad tym, kiedy ślady są przechwytywane, jest kluczowe, ponieważ wiesz, kiedy test obciążenia będzie uruchomiony. Ale losowy interwał próbkowania może go pominąć.

W następnych sekcjach przedstawiono sposób działania tego scenariusza:

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>Krok 1. generowanie ruchu do aplikacji sieci Web przez uruchomienie testu wydajności sieci Web

Jeśli aplikacja sieci Web ma już ruch przychodzący lub po prostu chcesz ręcznie generować ruch, Pomiń tę sekcję i przejdź do kroku 2.

1. W portalu Application Insights wybierz pozycję **Konfiguruj**  >  **testowanie wydajności**. 

1. Aby rozpocząć nowy test wydajności, wybierz przycisk **Nowy** .

   ![Utwórz nowy test wydajnościowy][create-performance-test]

1. W okienku **Nowy test wydajności** Skonfiguruj docelowy adres URL testu. Zaakceptuj wszystkie ustawienia domyślne, a następnie wybierz pozycję **Uruchom test** , aby rozpocząć Uruchamianie testu obciążenia.

    ![Konfiguruj test obciążenia][configure-performance-test]

    Nowy test zostanie umieszczony w kolejce, po którym następuje stan *w toku*.

    ![Test obciążenia został przesłany i umieszczony w kolejce][load-test-queued]

    ![Test obciążenia jest uruchomiony w toku][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>Krok 2. Rozpoczynanie sesji profilera na żądanie

1. Gdy test obciążenia jest uruchomiony, uruchom program Profiler, aby przechwycić ślady w aplikacji sieci Web podczas otrzymywania ładowania.

1. Przejdź do okienka **Konfigurowanie profilera** .


### <a name="step-3-view-traces"></a>Krok 3. Wyświetlanie śladów

Po zakończeniu działania profilera postępuj zgodnie z instrukcjami w obszarze powiadomień, aby przejść do okienka wydajność i wyświetlić dane śledzenia.

## <a name="next-steps"></a>Następne kroki
[Włączanie profilera i wyświetlanie śladów](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
