---
title: Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD | Microsoft Docs
description: Dowiedz się więcej na temat łączników serwer proxy aplikacji usługi Azure AD platformy Azure.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: be5ce5b3eebb2f784469680cf7614df6ca750b55
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658268"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure

Łączniki sprawiają, że platforma Azure serwer proxy aplikacji usługi Azure AD to możliwe. Są one proste, łatwe do wdrożenia i konserwacji oraz są bardzo wydajne. W tym artykule omówiono, jakie łączniki, jak działają, oraz sugestie dotyczące sposobu optymalizacji wdrożenia.

## <a name="what-is-an-application-proxy-connector"></a>Co to jest łącznik serwera proxy aplikacji?

Łączniki to lekkie agenci, którzy są w środowisku lokalnym i ułatwiają połączenie wychodzące z usługą serwera proxy aplikacji. Łączniki muszą być zainstalowane na serwerze z systemem Windows, który ma dostęp do aplikacji zaplecza. Łączniki można organizować w grupy łączników, a każda grupa obsługuje ruch do określonych aplikacji.

## <a name="requirements-and-deployment"></a>Wymagania i wdrażanie

Aby pomyślnie wdrożyć serwer proxy aplikacji, musisz mieć co najmniej jeden łącznik, ale zalecamy dwie lub więcej więcej odporności. Zainstaluj łącznik na komputerze z systemem Windows Server 2012 R2 lub nowszym. Łącznik musi komunikować się z usługą serwera proxy aplikacji oraz z opublikowanymi aplikacjami lokalnymi.

### <a name="windows-server"></a>Server systemu Windows
Potrzebujesz serwera z systemem Windows Server 2012 R2 lub nowszym, na którym można zainstalować łącznik serwera proxy aplikacji. Serwer musi nawiązać połączenie z usługami serwera proxy aplikacji na platformie Azure oraz aplikacjami lokalnymi, które są publikowane.

Przed zainstalowaniem łącznika serwera proxy aplikacji w systemie Windows Server należy włączyć protokół TLS 1,2. Aby włączyć protokół TLS 1,2 na serwerze:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.

Aby uzyskać więcej informacji o wymaganiach sieci dla serwera łącznika, zobacz Wprowadzenie do [serwera proxy aplikacji i Instalowanie łącznika](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Konserwacja

Łączniki i usługi zadbają o wszystkie zadania wysokiej dostępności. Mogą być dodawane lub usuwane dynamicznie. Za każdym razem, gdy nowe żądanie jest kierowane do jednego z łączników, które są obecnie dostępne. Jeśli łącznik jest tymczasowo niedostępny, nie odpowiada na ten ruch.

Łączniki są bezstanowe i nie mają danych konfiguracyjnych na komputerze. Jedyne przechowywane dane to ustawienia umożliwiające połączenie usługi z certyfikatem uwierzytelniania. Po nawiązaniu połączenia z usługą ściągają wszystkie wymagane dane konfiguracji i odświeżają je co kilka minut.

Łączniki również sonduje serwer, aby dowiedzieć się, czy jest dostępna nowsza wersja łącznika. Jeśli zostanie znaleziony, łączniki zostaną zaktualizowane.

Łączniki można monitorować na komputerze, na którym są uruchomione, przy użyciu dziennika zdarzeń i liczników wydajności. Można też wyświetlić ich stan na stronie serwer proxy aplikacji Azure Portal:

![Przykład: Łączniki serwer proxy aplikacji usługi Azure AD platformy Azure](./media/application-proxy-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usuwać łączników, które nie są używane. Gdy łącznik jest uruchomiony, pozostaje aktywny, gdy łączy się z usługą. Nieużywane łączniki są oznaczane jako _nieaktywne_ i usuwane po 10 dniach braku aktywności. Jeśli chcesz odinstalować łącznik, odinstaluj usługę łącznika i usługę Aktualizator z serwera. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD udostępnia aktualizacje automatyczne dla wszystkich wdrożonych łączników. Gdy usługa Aktualizator łączników serwera proxy aplikacji jest uruchomiona, łączniki są automatycznie aktualizowane. Jeśli na serwerze nie widzisz usługi Aktualizator łączników, należy [ponownie zainstalować łącznik](application-proxy-add-on-premises-application.md) , aby pobrać aktualizacje.

Jeśli nie chcesz czekać na przełączenie automatycznej aktualizacji do łącznika, możesz przeprowadzić uaktualnienie ręczne. Przejdź do [strony pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, na którym znajduje się łącznik, a następnie wybierz pozycję **Pobierz**. Ten proces umożliwia rozpoczęcie uaktualniania lokalnego łącznika.

W przypadku dzierżaw z wieloma łącznikami aktualizacje automatyczne są przeznaczone dla jednego łącznika na raz w każdej grupie, aby zapobiec przestojom w danym środowisku.

Podczas aktualizowania łącznika może wystąpić przestój, jeśli:
  
- Masz tylko jeden łącznik zalecamy zainstalowanie drugiego łącznika i [utworzenie grupy łączników](application-proxy-connector-groups.md). Pozwoli to uniknąć przestojów i zapewnić wyższą dostępność.  
- Łącznik był w środku transakcji po rozpoczęciu aktualizacji. Mimo że początkowa transakcja zostanie utracona, przeglądarka powinna automatycznie ponowić próbę wykonania operacji lub można odświeżyć stronę. Gdy żądanie zostanie wysłane ponownie, ruch jest kierowany do łącznika kopii zapasowej.

Aby wyświetlić informacje o poprzednio wydanych wersjach i ich zmianach, zobacz [serwer proxy aplikacji — historia wersji](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Tworzenie grup łączników

Grupy łączników umożliwiają przypisanie określonych łączników do obsługi określonych aplikacji. Można zgrupować wiele łączników, a następnie przypisać poszczególne aplikacje do grupy.

Grupy łączników ułatwiają zarządzanie dużymi wdrożeniami. Zwiększają one również opóźnienia dla dzierżawców, którzy mają aplikacje hostowane w różnych regionach, ponieważ można tworzyć grupy łączników oparte na lokalizacjach, które obsługują tylko aplikacje lokalne.

Aby dowiedzieć się więcej na temat grup łączników, zobacz [publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planowanie pojemności

Ważne jest, aby upewnić się, że zaplanowano wystarczającą ilość miejsca między łącznikami do obsługi oczekiwanego natężenia ruchu. Zalecamy, aby każda grupa łączników miała co najmniej dwa łączniki zapewniające wysoką dostępność i skalowanie. W przypadku, gdy trzy łączniki są optymalne na wypadek, może być konieczne obsługę komputera w dowolnym momencie.

Ogólnie rzecz biorąc, im więcej użytkowników, tym większa jest potrzebna maszyna. Poniżej znajduje się tabela przedstawiająca konspekt woluminu i oczekiwane opóźnienie, które mogą obsłużyć różne maszyny. Należy pamiętać, że jest to wszystko w oparciu o oczekiwane transakcje na sekundę (TPS), a nie przez użytkownika, ponieważ wzorce użycia są różne i nie można ich użyć do przewidywania obciążenia. Istnieją również pewne różnice w zależności od rozmiaru odpowiedzi i rozmiarów odpowiedzi aplikacji zaplecza i krótszych czasów odpowiedzi spowoduje obniżenie maksymalnej TPS. Zalecamy także posiadanie dodatkowych maszyn, aby obciążenie rozproszone na maszynach zawsze zapewniać dużą bufor. Dodatkowa pojemność zapewni wysoką dostępność i odporność.

|Rdzenie|Pamięć RAM|Oczekiwane opóźnienie (MS) — poziomie P99|Maksymalna TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200 *|

\* Ta maszyna użyła ustawienia niestandardowego w celu podniesienia liczby domyślnych limitów połączeń poza zalecanymi ustawieniami platformy .NET. Przed skontaktowaniem się z pomocą techniczną zalecamy przeprowadzenie testu z ustawieniami domyślnymi, aby uzyskać ten limit dla dzierżawy.

> [!NOTE]
> Nie istnieje wiele różnic w maksymalnej TPS między 4, 8 i 16 rdzeniami. Główna różnica między tymi występuje w oczekiwanym opóźnieniu.
>
> W tej tabeli przedstawiono również oczekiwaną wydajność łącznika w zależności od typu maszyny, na której jest zainstalowany. Jest to niezależne od limitów ograniczania przepustowości usługi serwera proxy aplikacji, zobacz [limity i ograniczenia usługi](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Bezpieczeństwo i obsługa sieci

Łączniki można instalować w dowolnym miejscu w sieci, co umożliwia wysyłanie żądań do usługi serwera proxy aplikacji. Ważne jest, aby komputer z uruchomionym łącznikiem miał również dostęp do Twoich aplikacji. Możesz zainstalować łączniki wewnątrz sieci firmowej lub na maszynie wirtualnej działającej w chmurze. Łączniki mogą być uruchamiane w sieci obwodowej, znanej także jako strefa zdemilitaryzowana (DMZ), ale nie jest to konieczne, ponieważ cały ruch jest wychodzący, dzięki czemu sieć pozostaje zabezpieczona.

Łączniki wysyłają tylko żądania wychodzące. Ruch wychodzący jest wysyłany do usługi serwera proxy aplikacji i opublikowanych aplikacji. Nie trzeba otwierać portów przychodzących, ponieważ ruch przebiega w obu kierunkach po ustanowieniu sesji. Nie trzeba również konfigurować dostępu przychodzącego za poorednictwem zapór.

Aby uzyskać więcej informacji na temat konfigurowania reguł zapory dla ruchu wychodzącego, zobacz temat [współpraca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skalowanie dla usługi serwera proxy aplikacji jest przezroczyste, ale skala jest czynnikiem dla łączników. Musisz mieć wystarczającą liczbę łączników, aby obsłużyć szczytowy ruch. Ponieważ łączniki są bezstanowe, nie ma to wpływu na liczbę użytkowników lub sesji. Zamiast tego reagują na liczbę żądań i ich rozmiar ładunku. W przypadku standardowego ruchu w sieci Web, średnia maszyna może obsłużyć kilka tysięcy żądań na sekundę. Konkretna pojemność zależy od dokładnej charakterystyki komputera.

Wydajność łącznika jest ograniczona przez procesor CPU i sieci. W przypadku szyfrowania i odszyfrowywania TLS wymagana jest wydajność procesora, podczas gdy sieć jest ważna, aby uzyskać szybką łączność z aplikacjami i usługą online na platformie Azure.

W przeciwieństwie do pamięci jest mniej problemu w przypadku łączników. Usługa online zajmuje się dużą ilością przetwarzania i całym nieuwierzytelnionym ruchem. Wszystko, co można zrobić w chmurze, odbywa się w chmurze.

Jeśli z jakiegokolwiek powodu łącznik lub maszyna staną się niedostępne, ruch zacznie się przechodzić do innego łącznika w grupie. Ta odporność jest również dlatego, że zalecamy używanie wielu łączników.

Innym czynnikiem wpływającym na wydajność jest jakość sieci między łącznikami, w tym:

- **Usługa online**: połączenia o powolnej lub dużej opóźnieniu z usługą serwera proxy aplikacji na platformie Azure mają wpływ na wydajność łącznika. Aby uzyskać najlepszą wydajność, Połącz swoją organizację z platformą Azure za pomocą usługi Express Route. W przeciwnym razie należy mieć zespół ds. sieci, aby upewnić się, że połączenia z platformą Azure są obsługiwane jak najszybciej.
- **Aplikacje zaplecza**: w niektórych przypadkach istnieją dodatkowe serwery proxy między łącznikiem i aplikacjami zaplecza, które mogą spowalniać lub zapobiegać połączeniami. Aby rozwiązać problem z tym scenariuszem, Otwórz przeglądarkę z serwera łącznika i spróbuj uzyskać dostęp do aplikacji. Jeśli łączniki są uruchamiane na platformie Azure, ale aplikacje są lokalne, środowisko może nie być oczekiwane przez użytkowników.
- **Kontrolery domeny**: Jeśli łączniki wykonują Logowanie jednokrotne (SSO) przy użyciu ograniczonego delegowania protokołu Kerberos, kontaktuje się z kontrolerami domeny przed wysłaniem żądania do zaplecza. Łączniki mają pamięć podręczną biletów protokołu Kerberos, ale w środowisku zajętości czas odpowiedzi kontrolerów domeny może wpłynąć na wydajność. Ten problem jest bardziej typowy w przypadku łączników, które działają na platformie Azure, ale komunikują się z lokalnymi kontrolerami domeny.

Aby uzyskać więcej informacji na temat optymalizowania sieci, zobacz [zagadnienia dotyczące topologii sieci podczas korzystania z serwer proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Przyłączanie do domeny

Łączniki mogą być uruchamiane na komputerze, który nie jest przyłączony do domeny. Jeśli jednak Logowanie jednokrotne (SSO) do aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA), potrzebna jest przyłączony do domeny komputer. W takim przypadku komputery łączników muszą być przyłączone do domeny, która może wykonywać ograniczone delegowanie [protokołu Kerberos](https://web.mit.edu/kerberos) w imieniu użytkowników dla opublikowanych aplikacji.

Łączniki mogą być również przyłączone do domen w lasach, które mają częściowe zaufanie lub do kontrolerów domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Wdrożenia łączników w środowiskach z zaostrzonymi zabezpieczeniami

Zwykle wdrożenie łącznika jest proste i nie wymaga żadnej specjalnej konfiguracji. Istnieją jednak pewne unikatowe warunki, które należy wziąć pod uwagę:

- Organizacje, które ograniczają ruch wychodzący, muszą [otwierać wymagane porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Komputery zgodne ze standardem FIPS mogą być wymagane do zmiany ich konfiguracji, aby umożliwić procesom łączników generowanie i przechowywanie certyfikatu.
- Organizacje, które zablokują swoje środowisko w oparciu o procesy, które wydają żądania sieci, muszą upewnić się, że obie usługi łączników są włączone, aby uzyskać dostęp do wszystkich wymaganych portów i adresów IP.
- W niektórych przypadkach wychodzące serwery proxy przesyłania dalej mogą przerwać uwierzytelnianie certyfikatów dwukierunkowych i spowodować niepowodzenie komunikacji.

## <a name="connector-authentication"></a>Uwierzytelnianie łącznika

Aby zapewnić bezpieczną usługę, łączniki muszą uwierzytelniać się w ramach usługi, a usługa musi uwierzytelniać się w kierunku łącznika. Uwierzytelnianie odbywa się przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenie. W ten sposób nazwa użytkownika i hasło administratora nie są przechowywane na maszynie łącznika.

Używane certyfikaty są specyficzne dla usługi serwera proxy aplikacji. Są one tworzone podczas wstępnej rejestracji i są automatycznie odnawiane przez łączniki co dwa miesiące.

Po pierwszym pomyślnym odnowieniu certyfikatu usługa łącznika usługi Azure serwer proxy aplikacji usługi Azure AD (usługa sieciowa) nie ma uprawnień do usuwania starego certyfikatu z lokalnego magazynu komputera. Jeśli certyfikat wygasł lub nie będzie już używany przez usługę, możesz go bezpiecznie usunąć.

Aby uniknąć problemów z odnowieniem certyfikatu, należy się upewnić, że komunikacja sieciowa z łącznika do [udokumentowanych miejsc docelowych](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) jest włączona.

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, jego certyfikaty mogą być nieaktualne. W takim przypadku Odinstaluj i ponownie zainstaluj łącznik, aby wyzwolić rejestrację. Można uruchomić następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

W przypadku instytucji rządowych należy użyć `-EnvironmentName "AzureUSGovernment"` . Aby uzyskać więcej informacji, zobacz [Install Agent for the Azure Government Cloud](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud).

Aby dowiedzieć się więcej na temat weryfikowania certyfikatu i rozwiązywania problemów, zobacz [Weryfikowanie obsługi składników komputera i zaplecza dla certyfikatu zaufania serwera proxy aplikacji](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Kulisy

Łączniki są oparte na serwerze proxy aplikacji sieci Web systemu Windows Server, więc mają większość tych samych narzędzi do zarządzania, w tym dzienników zdarzeń systemu Windows

![Zarządzanie dziennikami zdarzeń za pomocą Podgląd zdarzeń](./media/application-proxy-connectors/event-view-window.png)

i liczników wydajności systemu Windows.

![Dodawanie liczników do łącznika przy użyciu Monitora wydajności](./media/application-proxy-connectors/performance-monitor.png)

Łączniki mają zarówno dzienniki **administratora** , jak i **sesji** . Dziennik **administratora** zawiera najważniejsze zdarzenia i ich błędy. Dziennik **sesji** zawiera wszystkie transakcje i ich szczegóły przetwarzania.

Aby wyświetlić dzienniki, Otwórz **Podgląd zdarzeń** i przejdź do pozycji **Dzienniki aplikacji i usług**  >  **Microsoft**  >  **AadApplicationProxy**  >  **Connector**. Aby wyświetlić dziennik **sesji** , w menu **Widok** wybierz polecenie **Pokaż dzienniki analityczne i debugowania**. Dziennik **sesji** jest zazwyczaj używany do rozwiązywania problemów i jest domyślnie wyłączony. Włącz, aby rozpocząć zbieranie zdarzeń i wyłączyć je, gdy nie jest już potrzebne.

Stan usługi można przejrzeć w oknie usługi. Łącznik składa się z dwóch usług systemu Windows: rzeczywisty łącznik i Aktualizator. Oba te elementy muszą być uruchomione przez cały czas.

 ![Przykład: okno usług pokazujące lokalne usługi Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach za pomocą grup łączników](application-proxy-connector-groups.md)
- [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji i błędami łącznika](application-proxy-troubleshoot.md)
- [Jak zainstalować łącznik usługi Azure serwer proxy aplikacji usługi Azure AD w trybie dyskretnym](application-proxy-register-connector-powershell.md)