---
title: Understand Azure AD serwer proxy aplikacji connectors | (Opis łączników usługi Azure AD | Microsoft Docs
description: Dowiedz się więcej na temat łączników serwer proxy aplikacji Azure AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b54e3cb3b4ce7eb8f541755df75e8d6a22eb7c2
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575313"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Informacje o łącznikach serwera proxy aplikacji usługi Azure AD

Łączniki są tym, co umożliwia korzystanie serwer proxy aplikacji Azure AD. Są proste, łatwe w wdrażaniu i konserwacji oraz bardzo zaawansowane. W tym artykule omówiono, czym są łączniki, jak działają, i kilka sugestii dotyczących sposobu optymalizacji wdrożenia.

## <a name="what-is-an-application-proxy-connector"></a>Co to jest serwer proxy aplikacji łącznika?

Łączniki są lekkimi agentami, którzy znajdują się w środowisku lokalnym i ułatwiają połączenie wychodzące z serwer proxy aplikacji usługą. Łączniki muszą być zainstalowane w systemie Windows Server, który ma dostęp do aplikacji zaplecza. Łączniki można organizować w grupy łączników, a każda grupa będzie obsługiła ruch do określonych aplikacji. Aby uzyskać więcej informacji na temat serwera proxy aplikacji i diagramowej reprezentacji architektury serwera proxy aplikacji, zobacz [Using Azure AD serwer proxy aplikacji to publish on-premises apps for remote users](what-is-application-proxy.md#application-proxy-connectors) (Używanie usługi Azure AD serwer proxy aplikacji do publikowania aplikacji lokalnych dla użytkowników zdalnych).

## <a name="requirements-and-deployment"></a>Wymagania i wdrożenie

Aby pomyślnie serwer proxy aplikacji, potrzebny jest co najmniej jeden łącznik, ale zalecamy co najmniej dwa łączniki w celu zapewnienia większej odporności. Zainstaluj łącznik na maszynie z systemem Windows Server 2012 R2 lub nowszym. Łącznik musi komunikować się z usługą serwer proxy aplikacji i lokalnymi aplikacjami, które publikujesz.

### <a name="windows-server"></a>Server systemu Windows
Potrzebny jest serwer z systemem Windows Server 2012 R2 lub nowszym, na którym można zainstalować serwer proxy aplikacji łącznika. Serwer musi łączyć się z usługami serwer proxy aplikacji na platformie Azure i lokalnymi aplikacjami, które publikujesz.

Przed zainstalowaniem łącznika usługi serwer proxy aplikacji Windows Server musi mieć włączony serwer proxy aplikacji TLS 1.2. Aby włączyć na serwerze TLS 1.2:

1. Ustaw następujące klucze rejestru:

    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.

Aby uzyskać więcej informacji o wymaganiach dotyczących sieci dla serwera łącznika, zobacz Get started with serwer proxy aplikacji and install a connector (Rozpoczynanie pracy z serwer proxy aplikacji [i instalowanie łącznika).](application-proxy-add-on-premises-application.md)

## <a name="maintenance"></a>Konserwacja

Łączniki i usługa dbają o wszystkie zadania o wysokiej dostępności. Można je dodawać lub usuwać dynamicznie. Każde nowe żądanie jest kierowane do jednego z obecnie dostępnych łączników. Jeśli łącznik jest tymczasowo niedostępny, nie odpowiada na ten ruch.

Łączniki są bez stanowe i nie mają żadnych danych konfiguracyjnych na maszynie. Jedynymi danymi, które przechowują, są ustawienia łączenia usługi i jej certyfikatu uwierzytelniania. Gdy łączą się z usługą, ściągają wszystkie wymagane dane konfiguracji i odświeżają je co kilka minut.

Łączniki sondają również serwer, aby dowiedzieć się, czy istnieje nowsza wersja łącznika. Jeśli zostanie znaleziony, łączniki aktualizują się.

Łączniki można monitorować na maszynie, na których są uruchomione, przy użyciu dziennika zdarzeń i liczników wydajności. Możesz też wyświetlić ich stan na serwer proxy aplikacji stronie Azure Portal:

![Przykład: łączniki serwer proxy aplikacji Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usuwać nieużywanych łączników. Gdy łącznik jest uruchomiony, pozostaje aktywny podczas połączenia z usługą. Nieużywane łączniki są oznaczane _jako_ nieaktywne i są usuwane po upływie 10 dni braku aktywności. Jeśli jednak chcesz odinstalować łącznik, odinstaluj zarówno usługę łącznika, jak i usługę Updater z serwera. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD udostępnia aktualizacje automatyczne dla wszystkich wdrażanych łączników. Tak długo, jak serwer proxy aplikacji Aktualizator łączników jest uruchomiona, łączniki są automatycznie aktualizowane [przy użyciu najnowszej wersji łącznika.](application-proxy-faq.yml#why-is-my-connector-still-using-an-older-version-and-not-auto-upgraded-to-latest-version-) Jeśli nie widzisz usługi Aktualizator łączników na serwerze, musisz ponownie [](application-proxy-add-on-premises-application.md) zainstalować łącznik, aby uzyskać aktualizacje.

Jeśli nie chcesz czekać na automatyczną aktualizację łącznika, możesz wykonać uaktualnienie ręczne. Przejdź do strony [pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, na którym znajduje się łącznik, i wybierz pozycję **Pobierz.** Ten proces rozpoczyna uaktualnienie łącznika lokalnego.

W przypadku dzierżaw z wieloma łącznikami aktualizacje automatyczne mają na celu po jednym łączniku w każdej grupie, aby zapobiec przestojom w środowisku.

Może wystąpić przestój podczas aktualizacji łącznika, jeśli:
  
- Zalecamy zainstalowanie tylko jednego łącznika i utworzenie [grupy łączników.](application-proxy-connector-groups.md) Pozwoli to uniknąć przestojów i zapewnić wyższą dostępność.  
- Łącznik był w trakcie transakcji, gdy rozpoczynała się aktualizacja. Mimo że początkowa transakcja zostanie utracona, przeglądarka powinna automatycznie ponowić próbę wykonania operacji lub odświeżyć stronę. Gdy żądanie zostanie ponownie wysłane, ruch jest przekierowyny do łącznika kopii zapasowej.

Aby wyświetlić informacje o wcześniej wydanych wersjach i wprowadzonych w nich zmianach, [zobacz serwer proxy aplikacji — historia wersji.](application-proxy-release-version-history.md)

## <a name="creating-connector-groups"></a>Tworzenie grup łączników

Grupy łączników umożliwiają przypisywanie określonych łączników w celu obsługi określonych aplikacji. Możesz zgrupować wiele łączników, a następnie przypisać poszczególne aplikacje do grupy.

Grupy łączników ułatwiają zarządzanie dużymi wdrożeniami. Poprawiają one również opóźnienie dla dzierżaw, które mają aplikacje hostowane w różnych regionach, ponieważ można tworzyć grupy łączników oparte na lokalizacji, które będą obsługiwać tylko aplikacje lokalne.

Aby dowiedzieć się więcej na temat grup łączników, zobacz [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników.](application-proxy-connector-groups.md)

## <a name="capacity-planning"></a>Planowanie pojemności

Ważne jest, aby upewnić się, że zaplanowano wystarczającą pojemność między łącznikami do obsługi oczekiwanego natężenia ruchu. Zalecamy, aby każda grupa łączników ma co najmniej dwa łączniki w celu zapewnienia wysokiej dostępności i skali. Posiadanie trzech łączników jest optymalne w przypadku, gdy w dowolnym momencie może być konieczne serwisienie maszyny.

Ogólnie rzecz biorąc, im więcej użytkowników, tym większa maszyna będzie potrzebna. Poniżej znajduje się tabela z konspektem woluminu i oczekiwanym opóźnieniem, które mogą obsłużyć różne maszyny. Należy pamiętać, że wszystko jest oparte na oczekiwanych transakcjach na sekundę,a nie przez użytkownika, ponieważ wzorce użycia różnią się i nie mogą być używane do przewidywania obciążenia. Istnieją również pewne różnice w zależności od rozmiaru odpowiedzi i czasu odpowiedzi aplikacji zaplecza — większe rozmiary odpowiedzi i wolniejsze czasy odpowiedzi będą powodować niższą maksymalną wartość TPS. Zalecamy również posiadanie dodatkowych maszyn, aby rozproszone obciążenie na maszynach zawsze zapewniało wiele buforów. Dodatkowa pojemność zapewni wysoką dostępność i odporność.

|Rdzenie|Pamięć RAM|Oczekiwane opóźnienie (MS)-P99|Maksymalna liczba tps|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Ta maszyna użyła ustawienia niestandardowego, aby podnieść niektóre domyślne limity połączeń poza ustawieniami zalecanymi przez platformę .NET. Zalecamy uruchomienie testu z ustawieniami domyślnymi przed skontaktowaniem się z pomocą techniczną w celu zmiany tego limitu dla dzierżawy.

> [!NOTE]
> Nie ma dużej różnicy w maksymalnej tps między 4, 8 i 16 rdzeni maszyn. Główna różnica między nimi polega na oczekiwanym opóźnieniu.
>
> W tej tabeli skupiono się również na oczekiwanej wydajności łącznika na podstawie typu maszyny, na których jest zainstalowany. Jest to oddzielone od serwer proxy aplikacji limitów ograniczania usługi, zobacz [Limity i ograniczenia usługi](../enterprise-users/directory-service-limits-restrictions.md).

## <a name="security-and-networking"></a>Zabezpieczenia i sieć

Łączniki można instalować w dowolnym miejscu w sieci, co umożliwia wysyłanie żądań do serwer proxy aplikacji usługi. Ważne jest, aby komputer z uruchomionym łącznikiem również ma dostęp do aplikacji. Łączniki można instalować wewnątrz sieci firmowej lub na maszynie wirtualnej, która działa w chmurze. Łączniki mogą działać w sieci obwodowej, znanej także jako strefa zdemilitaryzowana (DMZ), ale nie jest to konieczne, ponieważ cały ruch jest wychodzący, dzięki czemu sieć pozostaje bezpieczna.

Łączniki wysyłają tylko żądania wychodzące. Ruch wychodzący jest wysyłany do usługi serwer proxy aplikacji i opublikowanych aplikacji. Nie trzeba otwierać portów przychodzących, ponieważ ruch przepływa w obie strony po nawiązanych sesjach. Nie trzeba również konfigurować dostępu przychodzącego za pośrednictwem zapór.

Aby uzyskać więcej informacji na temat konfigurowania reguł zapory dla ruchu wychodzącego, zobacz [Praca z istniejącymi lokalnymi serwerami proxy.](application-proxy-configure-connectors-with-proxy-servers.md)

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skalowanie dla usługi serwer proxy aplikacji jest niewidoczne, ale skalowanie jest czynnikiem dla łączników. Potrzebujesz wystarczającej ilości łączników do obsługi szczytowego ruchu. Ponieważ łączniki są bez stanowe, nie ma na nie wpływu liczba użytkowników ani sesji. Zamiast tego odpowiadają na liczbę żądań i rozmiar ładunku. W przypadku standardowego ruchu internetowego średnia maszyna może obsłużyć kilka tysięcy żądań na sekundę. Konkretną pojemność zależy od dokładnych cech maszyny.

Wydajność łącznika jest powiązana z procesorem CPU i siecią. Do szyfrowania i odszyfrowywania protokołu TLS potrzebna jest wydajność procesora, a sieć jest ważna, aby uzyskać szybką łączność z aplikacjami i usługą online na platformie Azure.

Z kolei pamięć nie stanowi problemu dla łączników. Usługa online zajmuje się przetwarzaniem i całym nieuwierzytnionym ruchem. Wszystko, co można zrobić w chmurze, odbywa się w chmurze.

Jeśli z jakiegokolwiek powodu ten łącznik lub maszyna staną się niedostępne, ruch zacznie trafiać do innego łącznika w grupie. Ta odporność jest również tym, dlaczego zalecamy używanie wielu łączników.

Innym czynnikiem, który wpływa na wydajność, jest jakość sieci między łącznikami, w tym:

- **Usługa online:** Wolne lub o dużym opóźnieniu połączenia z usługą serwer proxy aplikacji na platformie Azure wpływają na wydajność łącznika. Aby uzyskać najlepszą wydajność, połącz swoją organizację z platformą Azure za pomocą usługi Express Route. W przeciwnym razie zespół sieciowy musi upewnić się, że połączenia z platformą Azure są obsługiwane tak efektywnie, jak to możliwe.
- **Aplikacje zaplecza:** W niektórych przypadkach istnieją dodatkowe proxy między łącznikiem a aplikacjami zaplecza, które mogą spowalniać lub uniemożliwiać połączenia. Aby rozwiązać ten scenariusz, otwórz przeglądarkę z serwera łącznika i spróbuj uzyskać dostęp do aplikacji. Jeśli uruchamiasz łączniki na platformie Azure, ale aplikacje są lokalne, środowisko może nie być tym, czego oczekują użytkownicy.
- **Kontrolery domeny:** jeśli łączniki wykonują logowanie jednokrotne przy użyciu ograniczonego delegowania protokołu Kerberos, kontaktują się z kontrolerami domeny przed wysłaniem żądania do zaplecza. Łączniki mają pamięć podręczną biletów protokołu Kerberos, ale w środowisku zajętym czas odpowiedzi kontrolerów domeny może mieć wpływ na wydajność. Ten problem występuje częściej w przypadku łączników, które działają na platformie Azure, ale komunikują się z lokalnymi kontrolerami domeny.

Aby uzyskać więcej informacji na temat optymalizacji sieci, zobacz [Zagadnienia dotyczące topologii](application-proxy-network-topology.md)sieci podczas korzystania z Azure Active Directory serwer proxy aplikacji .

## <a name="domain-joining"></a>Przyłączanie do domeny

Łączniki można uruchamiać na maszynie, która nie jest przyłączona do domeny. Jeśli jednak chcesz korzystać z logowania jednokrotnego (SSO) w aplikacjach, które korzystają Integrated Windows Authentication (IWA), potrzebujesz maszyny przyłączone do domeny. W takim przypadku maszyny łącznika muszą być przyłączone do domeny, która może wykonywać ograniczone delegowanie [protokołu Kerberos](https://web.mit.edu/kerberos) w imieniu użytkowników dla opublikowanych aplikacji.

Łączniki mogą być również przyłączone do domen w lasach, które mają częściowe zaufanie, lub do kontrolerów domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Wdrożenia łączników w środowiskach ze wzmacnianiem zabezpieczeń

Zazwyczaj wdrażanie łącznika jest proste i nie wymaga specjalnej konfiguracji. Istnieją jednak pewne unikatowe warunki, które należy wziąć pod uwagę:

- Organizacje, które ograniczają ruch wychodzący, [muszą otworzyć wymagane porty.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Maszyny zgodne ze standardem FIPS mogą wymagać zmiany konfiguracji, aby umożliwić procesom łącznika wygenerowanie i przechowywanie certyfikatu.
- Organizacje, które blokują swoje środowisko na podstawie procesów, które wystawiają żądania sieciowe, muszą upewnić się, że obie usługi łącznika mają dostęp do wszystkich wymaganych portów i ip.
- W niektórych przypadkach wychodzące usługi proxy mogą przerwać dwukierunkowe uwierzytelnianie certyfikatu i spowodować niepowodzenie komunikacji.

## <a name="connector-authentication"></a>Uwierzytelnianie łącznika

Aby zapewnić bezpieczną usługę, łączniki muszą uwierzytelniać się w usłudze, a usługa musi uwierzytelnić się w łączniku. To uwierzytelnianie odbywa się przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenie. W ten sposób nazwa użytkownika i hasło administratora nie są przechowywane na maszynie łącznika.

Używane certyfikaty są specyficzne dla usługi serwer proxy aplikacji usługi. Są one tworzone podczas początkowej rejestracji i są automatycznie odnawiane przez łączniki co kilka miesięcy.

Po pierwszym pomyślnym odnowieniu certyfikatu usługa Azure AD serwer proxy aplikacji Connector (usługa sieciowa) nie ma uprawnień do usunięcia starego certyfikatu z magazynu komputera lokalnego. Jeśli certyfikat wygasł lub nie będzie już używany przez usługę, możesz go bezpiecznie usunąć.

Aby uniknąć problemów z odnawianiem certyfikatu, upewnij się, że włączono komunikację sieciową z łącznika do [udokumentowanych miejsc](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) docelowych.

Jeśli łącznik nie jest połączony z usługą przez kilka miesięcy, jego certyfikaty mogą być nieaktualne. W takim przypadku odinstaluj i ponownie zainstaluj łącznik, aby wyzwolić rejestrację. Możesz uruchomić następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector -EnvironmentName "AzureCloud"
```

W przypadku instytucji rządowych `-EnvironmentName "AzureUSGovernment"` użyj . Aby uzyskać więcej informacji, zobacz [Install Agent for the Azure Government Cloud (Instalowanie agenta dla Azure Government Cloud).](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)

Aby dowiedzieć się więcej na temat sposobu weryfikowania certyfikatu i rozwiązywania problemów, zobacz [Verify Machine and backend components support for serwer proxy aplikacji trust certificate](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)(Weryfikowanie obsługi składników maszyny i zaplecza dla certyfikatu serwer proxy aplikacji zaufania).

## <a name="under-the-hood"></a>Kulisy

Łączniki są oparte na programie Windows Server Web serwer proxy aplikacji, więc mają większość tych samych narzędzi do zarządzania, w tym dzienniki zdarzeń systemu Windows

![Zarządzanie dziennikami zdarzeń za pomocą Podgląd zdarzeń](./media/application-proxy-connectors/event-view-window.png)

i liczniki wydajności systemu Windows.

![Dodawanie liczników do łącznika za pomocą monitor wydajności](./media/application-proxy-connectors/performance-monitor.png)

Łączniki mają dzienniki **administratora** **i** sesji. Dziennik **administratora** zawiera kluczowe zdarzenia i ich błędy. Dziennik **sesji** zawiera wszystkie transakcje i ich szczegóły przetwarzania.

Aby wyświetlić dzienniki, otwórz **Podgląd zdarzeń** i przejdź do tematu **Dzienniki** aplikacji i usług  >  **Łącznik Microsoft**  >  **AadApplicationProxy.**  >   Aby wyświetlić **dziennik sesji,** w menu **Widok** wybierz pozycję Pokaż dzienniki analityczne i **debugowania.** Dziennik **sesji** jest zwykle używany do rozwiązywania problemów i jest domyślnie wyłączony. Włącz ją, aby rozpocząć zbieranie zdarzeń i wyłączyć je, gdy nie będą już potrzebne.

Stan usługi można sprawdzić w oknie Usługi. Łącznik składa się z dwóch usług systemu Windows: łącznika rzeczywistego i aktualizującego. Oba muszą działać przez cały czas.

 ![Przykład: okno usług z lokalnymi usługami Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md)
- [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
- [Rozwiązywanie problemów serwer proxy aplikacji łączników i łączników](application-proxy-troubleshoot.md)
- [Jak dyskretnie zainstalować łącznik azure AD serwer proxy aplikacji Connector](application-proxy-register-connector-powershell.md)
