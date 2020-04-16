---
title: Opis łączników serwera proxy aplikacji usługi Azure AD | Dokumenty firmy Microsoft
description: Obejmuje podstawowe informacje o łącznikach serwera proxy aplikacji usługi Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f1b8b9af8f90629d087246edf0cb3426bd9b66c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406827"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Opis łączników serwera proxy aplikacji usługi Azure AD

Łączniki są tym, co umożliwia serwer proxy aplikacji usługi Azure AD. Są proste, łatwe w wdrażaniu i konserwacji oraz bardzo wydajne. W tym artykule omówiono, jakie są łączniki, jak działają, oraz sugestie dotyczące optymalizacji wdrożenia.

## <a name="what-is-an-application-proxy-connector"></a>Co to jest łącznik serwera proxy aplikacji?

Łączniki są lekkie agentów, które znajdują się w środowisku lokalnym i ułatwiają połączenie wychodzące z usługą proxy aplikacji. Łączniki muszą być zainstalowane w systemie Windows Server, który ma dostęp do aplikacji wewnętrznej bazy danych. Łączniki można organizować w grupy łączników, przy czym każda grupa obsługuje ruch do określonych aplikacji.

## <a name="requirements-and-deployment"></a>Wymagania i wdrażanie

Aby pomyślnie wdrożyć serwer proxy aplikacji, potrzebujesz co najmniej jednego łącznika, ale zaleca się dwa lub więcej dla większej odporności. Zainstaluj łącznik na komputerze z systemem Windows Server 2012 R2 lub nowszym. Łącznik musi komunikować się z usługą serwera proxy aplikacji i aplikacji lokalnych, które publikujesz.

### <a name="windows-server"></a>Server systemu Windows
Potrzebny jest serwer z systemem Windows Server 2012 R2 lub nowszym, na którym można zainstalować łącznik serwera proxy aplikacji. Serwer musi połączyć się z usługami serwera proxy aplikacji na platformie Azure i publikowanych aplikacji lokalnych.

Serwer systemu Windows musi mieć włączony protokół TLS 1.2 przed zainstalowaniem łącznika serwera proxy aplikacji. Aby włączyć TLS 1.2 na serwerze:

1. Ustaw następujące klucze rejestru:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Uruchom ponownie serwer.

Aby uzyskać więcej informacji na temat wymagań sieciowych serwera łączników, zobacz [Wprowadzenie do serwera proxy aplikacji i zainstalowanie łącznika](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Konserwacja

Złącza i usługa zajmują się wszystkimi zadaniami wysokiej dostępności. Można je dodawać lub usuwać dynamicznie. Za każdym razem, gdy pojawia się nowe żądanie, jest ono kierowane do jednego z łączników, który jest obecnie dostępny. Jeśli łącznik jest tymczasowo niedostępny, nie reaguje na ten ruch.

Łączniki są bezstanowe i nie mają danych konfiguracyjnych na komputerze. Jedynymi danymi, które przechowują, są ustawienia łączenia usługi i jej certyfikat uwierzytelniania. Gdy łączą się z usługą, ściągają wszystkie wymagane dane konfiguracyjne i odświeżają je co kilka minut.

Łączniki również sondowania serwera, aby dowiedzieć się, czy istnieje nowsza wersja łącznika. Jeśli zostanie znaleziony, łączniki aktualizują się.

Łączniki można monitorować z komputera, na którego są uruchomione, przy użyciu dziennika zdarzeń i liczników wydajności. Możesz też wyświetlić ich stan na stronie Serwera proxy aplikacji w witrynie Azure portal:

![Przykład: łączniki serwera proxy aplikacji usługi Azure AD](./media/application-proxy-connectors/app-proxy-connectors.png)

Nie trzeba ręcznie usuwać łączników, które nie sąużywane. Gdy łącznik jest uruchomiony, pozostaje aktywny, ponieważ łączy się z usługą. Nieużywane łączniki są oznaczone jako _nieaktywne_ i usuwane po 10 dniach braku aktywności. Jeśli jednak chcesz odinstalować łącznik, odinstaluj z serwera zarówno usługę Łącznika, jak i usługę Updater. Uruchom ponownie komputer, aby całkowicie usunąć usługę.

## <a name="automatic-updates"></a>Automatyczne aktualizacje

Usługa Azure AD udostępnia aktualizacje automatyczne dla wszystkich łączników, które można wdrożyć. Tak długo, jak usługa aktualizacji łącznika serwera proxy aplikacji jest uruchomiona, łączniki są aktualizowane automatycznie. Jeśli nie widzisz usługi aktualizacji łączników na serwerze, musisz [ponownie zainstalować łącznik,](application-proxy-add-on-premises-application.md) aby uzyskać wszelkie aktualizacje.

Jeśli nie chcesz czekać na automatyczną aktualizację do łącznika, możesz wykonać ręczne uaktualnienie. Przejdź do [strony pobierania łącznika](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) na serwerze, na którym znajduje się łącznik, i wybierz pozycję **Pobierz**. Ten proces rozpoczyna uaktualnienie dla łącznika lokalnego.

W przypadku dzierżawców z wieloma łącznikami aktualizacje automatyczne są przeznaczone dla jednego łącznika naraz w każdej grupie, aby zapobiec przestojom w twoim środowisku.

Może wystąpić przestoje podczas aktualizacji łącznika, jeśli:
  
- Masz tylko jedno złącze, zalecamy zainstalowanie drugiego złącza i [utworzenie grupy łączników](application-proxy-connector-groups.md). Pozwoli to uniknąć przestojów i zapewnić wyższą dostępność.  
- Łącznik był w środku transakcji po rozpoczęciu aktualizacji. Mimo że początkowa transakcja została utracona, przeglądarka powinna automatycznie ponowić próbę wykonania operacji lub odświeżyć stronę. Gdy żądanie jest ponownie, ruch jest kierowany do łącznika kopii zapasowej.

Aby wyświetlić informacje o wcześniej wydanych wersjach i o zmianach, które zawierają, zobacz [Historia wersji serwera proxy aplikacji](application-proxy-release-version-history.md).

## <a name="creating-connector-groups"></a>Tworzenie grup łączników

Grupy łączników umożliwiają przypisywanie określonych łączników do obsługi określonych aplikacji. Można zgrupować kilka łączników razem, a następnie przypisać każdą aplikację do grupy.

Grupy łączników ułatwiają zarządzanie dużymi wdrożeniami. Zwiększają one również opóźnienie dla dzierżawców, które mają aplikacje hostowane w różnych regionach, ponieważ można tworzyć grupy łączników oparte na lokalizacji, aby obsługiwać tylko aplikacje lokalne.

Aby dowiedzieć się więcej o grupach łączników, zobacz [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Planowanie pojemności

Ważne jest, aby upewnić się, że zaplanowano wystarczającą pojemność między łącznikami do obsługi oczekiwanego natężenia ruchu. Zaleca się, że każda grupa łączników ma co najmniej dwa łączniki, aby zapewnić wysoką dostępność i skalę. Posiadanie trzech złączy jest optymalne w przypadku, gdy może być konieczne serwis komputera w dowolnym momencie.

Ogólnie rzecz biorąc, im więcej użytkowników masz, tym większa maszyna będzie potrzebna. Poniżej znajduje się tabela przedstawiająca zarys woluminu i oczekiwanego opóźnienia, które mogą obsługiwać różne maszyny. Należy zauważyć, że wszystko jest oparte na oczekiwanych transakcji na sekundę (TPS), a nie przez użytkownika, ponieważ wzorce użycia różnią się i nie można użyć do przewidywania obciążenia. Będą również pewne różnice w zależności od rozmiaru odpowiedzi i czasu odpowiedzi aplikacji wewnętrznej bazy danych — większe rozmiary odpowiedzi i wolniejsze czasy odpowiedzi spowoduje niższy maksymalny TPS. Zaleca się również posiadanie dodatkowych maszyn, aby rozproszone obciążenie w maszynach zawsze zapewniało duży bufor. Dodatkowa pojemność zapewni wysoką dostępność i odporność.

|Rdzenie|Pamięć RAM|Oczekiwane opóźnienie (MS)-P99|Maksymalna wartość TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Na tym komputerze użyto ustawienia niestandardowego do podniesienia niektórych domyślnych limitów połączeń poza zalecane ustawienia .NET. Zaleca się uruchomienie testu z ustawieniami domyślnymi przed skontaktowaniem się z pomocą techniczną, aby uzyskać ten limit zmieniony dla dzierżawy.

> [!NOTE]
> Maksymalna różnica między 4, 8 i 16 maszynami rdzeniowymi nie ma dużej różnicy. Główną różnicą między tymi jest oczekiwane opóźnienie.
>
> Ta tabela koncentruje się również na oczekiwanej wydajności złącza na podstawie typu komputera, na który jest zainstalowany. Jest to niezależne od limitów ograniczania przepustowości usługi proxy aplikacji, zobacz [Limity i ograniczenia usługi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Bezpieczeństwo i tworzenie sieci

Łączniki można zainstalować w dowolnym miejscu w sieci, co umożliwia im wysyłanie żądań do usługi Proxy aplikacji. Ważne jest to, że komputer z łącznikiem ma również dostęp do aplikacji. Łączniki można zainstalować wewnątrz sieci firmowej lub na maszynie wirtualnej, która działa w chmurze. Łączniki mogą działać w sieci obwodowej, znanej również jako strefa zdemilitaryzowana (DMZ), ale nie jest to konieczne, ponieważ cały ruch jest wychodzący, dzięki czemu sieć pozostaje bezpieczna.

Łączniki wysyłają tylko żądania wychodzące. Ruch wychodzący jest wysyłany do usługi Proxy aplikacji i do opublikowanych aplikacji. Nie trzeba otwierać portów przychodzących, ponieważ ruch przepływa w obie strony po ustanowieniu sesji. Nie trzeba też konfigurować dostępu przychodzącego za pośrednictwem zapór.

Aby uzyskać więcej informacji dotyczących konfigurowania reguł zapory wychodzącej, zobacz [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Wydajność i skalowalność

Skala dla usługi proxy aplikacji jest przezroczysta, ale skala jest czynnikiem dla łączników. Musisz mieć wystarczającą ilość łączników do obsługi szczytowego ruchu. Ponieważ łączniki są bezstanowe, nie ma na nie wpływu liczba użytkowników lub sesji. Zamiast tego odpowiadają na liczbę żądań i ich rozmiar ładunku. Przy standardowym ruchu internetowym przeciętna maszyna może obsługiwać kilka tysięcy żądań na sekundę. Szczególna pojemność zależy od dokładnych właściwości maszyny.

Wydajność łącznika jest powiązana z procesorem i siecią. Wydajność procesora CPU jest potrzebna do szyfrowania i odszyfrowywania protokołu TLS, podczas gdy sieć jest ważna, aby uzyskać szybką łączność z aplikacjami i usługą online na platformie Azure.

Z drugiej strony pamięć jest mniej problem dla łączników. Usługa online zajmuje się dużą częścią przetwarzania i całym nieuwierzywym ruchem. Wszystko, co można zrobić w chmurze, odbywa się w chmurze.

Jeśli z jakiegoś powodu łącznik lub komputer stanie się niedostępny, ruch zacznie przechodzić do innego łącznika w grupie. Ta odporność jest również, dlaczego zaleca się posiadanie wielu łączników.

Innym czynnikiem wpływającym na wydajność jest jakość sieci między złączami, w tym:

- **Usługa online:** powolne lub duże opóźnienia połączeń z usługą proxy aplikacji na platformie Azure wpływają na wydajność łącznika. Aby uzyskać najlepszą wydajność, połącz swoją organizację z platformą Azure za pomocą trasy ekspresowej. W przeciwnym razie zespół sieciowy upewnij się, że połączenia z platformą Azure są obsługiwane tak wydajnie, jak to możliwe.
- **Aplikacje wewnętrznej bazy danych:** W niektórych przypadkach istnieją dodatkowe serwery proxy między łącznikiem a aplikacjami wewnętrznej bazy danych, które mogą spowolnić lub uniemożliwić połączenia. Aby rozwiązać ten scenariusz, otwórz przeglądarkę z serwera łączników i spróbuj uzyskać dostęp do aplikacji. Jeśli uruchomisz łączniki na platformie Azure, ale aplikacje są lokalne, środowisko może nie być tym, czego oczekują użytkownicy.
- **Kontrolery domeny:** Jeśli łączniki wykonują logowanie jednokrotne (Logowanie jednokrotne) przy użyciu delegowania ograniczonego protokołu Kerberos, kontaktują się z kontrolerami domeny przed wysłaniem żądania do wewnętrznej bazy danych. Łączniki mają pamięć podręczną biletów Protokołu Kerberos, ale w środowisku zajęty czas reakcji kontrolerów domeny może mieć wpływ na wydajność. Ten problem jest bardziej typowe dla łączników, które działają na platformie Azure, ale komunikować się z kontrolerami domeny, które są lokalne.

Aby uzyskać więcej informacji na temat optymalizacji sieci, zobacz [Zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Dołączanie do domeny

Łączniki można uruchomić na komputerze, który nie jest przyłączony do domeny. Jeśli jednak chcesz jednokrotnego logowania (SSO) do aplikacji korzystających ze zintegrowanego uwierzytelniania systemu Windows (IWA), potrzebujesz komputera przyłączonego do domeny. W takim przypadku maszyny łącznika muszą być przyłączone do domeny, która może wykonywać delegowanie ograniczone [kerberos](https://web.mit.edu/kerberos) w imieniu użytkowników dla opublikowanych aplikacji.

Łączniki można również łączyć z domenami lub lasami, które mają częściowe zaufanie, lub z kontrolerami domeny tylko do odczytu.

## <a name="connector-deployments-on-hardened-environments"></a>Wdrożenia łączników w środowiskach wzmocnionych

Zazwyczaj wdrażanie łącznika jest proste i nie wymaga specjalnej konfiguracji. Istnieją jednak pewne wyjątkowe warunki, które należy wziąć pod uwagę:

- Organizacje ograniczające ruch wychodzący muszą [otworzyć wymagane porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- Maszyny zgodne ze standardem FIPS mogą być wymagane do zmiany konfiguracji, aby umożliwić procesom łącznika generowanie i przechowywanie certyfikatu.
- Organizacje, które blokują swoje środowisko na podstawie procesów, które wystawiają żądania sieciowe, muszą upewnić się, że obie usługi łącznika mają dostęp do wszystkich wymaganych portów i adresów IP.
- W niektórych przypadkach wychodzące serwery proxy przesyłania dalej mogą przerwać uwierzytelnianie certyfikatów dwukierunkowych i spowodować niepowodzenie komunikacji.

## <a name="connector-authentication"></a>Uwierzytelnianie łącznika

Aby zapewnić bezpieczną usługę, łączniki muszą uwierzytelnić się w kierunku usługi, a usługa musi uwierzytelnić się w kierunku łącznika. To uwierzytelnianie odbywa się przy użyciu certyfikatów klienta i serwera, gdy łączniki inicjują połączenie. W ten sposób nazwa użytkownika i hasło administratora nie są przechowywane na komputerze łącznika.

Używane certyfikaty są specyficzne dla usługi Proxy aplikacji. Są one tworzone podczas wstępnej rejestracji i są automatycznie odnawiane przez łączniki co kilka miesięcy.

Po pierwszym pomyślnym odnowieniu certyfikatu usługa łącznika serwera proxy aplikacji usługi Azure AD (Usługa sieciowa) nie ma uprawnień do usuwania starego certyfikatu z lokalnego magazynu maszyn. Jeśli certyfikat wygasł lub nie będzie już używany przez usługę, można go bezpiecznie usunąć.

Aby uniknąć problemów z odnawianiem certyfikatu, upewnij się, że komunikacja sieciowa z łącznika do [udokumentowanych miejsc docelowych](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) jest włączona.

Jeśli łącznik nie jest podłączony do usługi przez kilka miesięcy, jego certyfikaty mogą być nieaktualne. W takim przypadku odinstaluj i zainstaluj ponownie łącznik, aby wyzwolić rejestrację. Można uruchomić następujące polecenia programu PowerShell:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```
Aby dowiedzieć się więcej o tym, jak zweryfikować certyfikat i rozwiązać problemy, zobacz [Weryfikowanie obsługi składników komputera i wewnętrznej bazy danych certyfikatu zaufania serwera proxy aplikacji](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate).

## <a name="under-the-hood"></a>Kulisy

Łączniki są oparte na serwerze proxy aplikacji sieci Web systemu Windows Server, więc mają większość tych samych narzędzi do zarządzania, w tym dzienniki zdarzeń systemu Windows

![Zarządzanie dziennikami zdarzeń za pomocą Podglądu zdarzeń](./media/application-proxy-connectors/event-view-window.png)

i liczniki wydajności systemu Windows.

![Dodawanie liczników do złącza za pomocą Monitora wydajności](./media/application-proxy-connectors/performance-monitor.png)

Łączniki mają dzienniki administracyjne i sesji. Dzienniki administratora zawierają kluczowe zdarzenia i ich błędy. Dzienniki sesji zawierają wszystkie transakcje i ich szczegóły przetwarzania.

Aby wyświetlić dzienniki, przejdź do Podglądu zdarzeń, otwórz menu **Widok** i włącz **pokaż dzienniki analityczne i debugowania**. Następnie włącz je, aby rozpocząć zbieranie zdarzeń. Te dzienniki nie są wyświetlane w serwerze proxy aplikacji sieci Web w systemie Windows Server 2012 R2, ponieważ łączniki są oparte na nowszej wersji.

Można sprawdzić stan usługi w oknie Usługi. Łącznik składa się z dwóch usług systemu Windows: rzeczywistego łącznika i aktualatora. Oba muszą działać cały czas.

 ![Przykład: Okno usług z lokalnymi usługami usługi Azure AD](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Następne kroki

- [Publikowanie aplikacji w oddzielnych sieciach i lokalizacjach przy użyciu grup łączników](application-proxy-connector-groups.md)
- [Praca z istniejącymi lokalnymi serwerami proxy](application-proxy-configure-connectors-with-proxy-servers.md)
- [Rozwiązywanie problemów z błędami serwera proxy i łączników aplikacji](application-proxy-troubleshoot.md)
- [Jak dyskretnie zainstalować łącznik serwera proxy aplikacji usługi Azure AD](application-proxy-register-connector-powershell.md)
