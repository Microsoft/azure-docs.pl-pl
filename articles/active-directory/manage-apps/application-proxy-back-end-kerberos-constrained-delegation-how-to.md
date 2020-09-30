---
title: Rozwiązywanie problemów z ograniczonym delegowaniem protokołu Kerberos — serwer proxy aplikacji
description: Rozwiązywanie problemów dotyczących konfiguracji ograniczonego delegowania protokołu Kerberos dla serwera proxy aplikacji
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/23/2019
ms.author: kenwith
ms.reviewer: asteen, japere
ms.openlocfilehash: 3ca3df010426347846b29734426edfad4536516b
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91568726"
---
# <a name="troubleshoot-kerberos-constrained-delegation-configurations-for-application-proxy"></a>Rozwiązywanie problemów dotyczących konfiguracji ograniczonego delegowania protokołu Kerberos dla serwera proxy aplikacji

Metody umożliwiające uzyskanie rejestracji Jednokrotnej dla opublikowanych aplikacji mogą się różnić w zależności od aplikacji. Jedną z opcji Azure Active Directory (Azure AD) serwer proxy aplikacji domyślnie oferuje ograniczone delegowanie protokołu Kerberos (KCD). Możesz skonfigurować łącznik dla użytkowników, aby uruchomić ograniczone uwierzytelnianie Kerberos w aplikacjach zaplecza.

Procedura włączania KCD jest prosta. Wymaga to nie więcej niż ogólne zrozumienie różnych składników i przepływu uwierzytelniania, które obsługują Logowanie jednokrotne. Jednak czasami KCD SSO nie działa zgodnie z oczekiwaniami. Aby rozwiązać te scenariusze, potrzebne są dobre źródła informacji.

Ten artykuł zawiera pojedynczy punkt odniesienia, który ułatwia rozwiązywanie problemów i samodzielne korygowanie niektórych typowych problemów. Obejmuje również diagnozowanie bardziej złożonych problemów z implementacją.

W tym artykule opisano następujące założenia:

- Wdrożenie usługi Azure serwer proxy aplikacji usługi Azure AD na [wprowadzenie z serwerem proxy aplikacji](application-proxy-add-on-premises-application.md) i ogólny dostęp do aplikacji innych niż KCD działają zgodnie z oczekiwaniami.
- Opublikowana aplikacja docelowa jest oparta na Internet Information Services (IIS) i implementacji protokołu Kerberos firmy Microsoft.
- Hosty serwera i aplikacji znajdują się w jednej domenie Azure Active Directory. Aby uzyskać szczegółowe informacje na temat scenariuszy obejmujących wiele domen i lasów, zobacz [oficjalny dokument KCD](https://aka.ms/KCDPaper).
- Aplikacja podmiotu jest publikowana w dzierżawie platformy Azure z włączonym wstępnym uwierzytelnianiem. Użytkownicy mogą uwierzytelniać się na platformie Azure za pomocą uwierzytelniania opartego na formularzach. Szczegółowe scenariusze uwierzytelniania klientów nie są opisane w tym artykule. Mogą zostać dodane w pewnym momencie w przyszłości.

## <a name="prerequisites"></a>Wymagania wstępne

Usługę Azure serwer proxy aplikacji usługi Azure AD można wdrożyć w wielu typach infrastruktury lub środowiskach. Architektury różnią się od organizacji do organizacji. Najczęstszymi przyczynami problemów związanych z KCDem nie są środowiska. Prosta błędna konfiguracja lub ogólne błędy powodują większość problemów.

Z tego powodu najlepiej upewnić się, że zostały spełnione wszystkie wymagania wstępne dotyczące [korzystania z logowania JEDNOkrotnego KCD z serwerem proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md) przed rozpoczęciem rozwiązywania problemów. Zanotuj sekcję dotyczącą konfigurowania ograniczonego delegowania protokołu Kerberos w witrynie 2012R2. Ten proces wykorzystuje inne podejście do konfigurowania KCD we wcześniejszych wersjach systemu Windows. Należy również mieć na uwadze następujące kwestie:

- Nie zdarza się, aby serwer członkowski domeny otworzył okno dialogowe bezpiecznego kanału z określonym kontrolerem domeny (DC). Następnie serwer może przejść do innego okna dialogowego w danym momencie. Hosty łączników nie ograniczają się do komunikacji tylko z konkretnymi kontrolerami domeny lokacji lokalnej.
- Scenariusze obejmujące wiele domen polegają na odwołaniach, które kierują hosta łącznika do kontrolerów domen, które mogą znajdować się poza siecią lokalną. W takich przypadkach równie ważne jest również wysyłanie ruchu w dół do kontrolerów domen, które reprezentują inne odpowiednie domeny. W przeciwnym razie delegowanie nie powiedzie się.
- Tam, gdzie to możliwe, Unikaj umieszczania aktywnych adresów IP lub identyfikatorów urządzeń między hostami łączników a kontrolerami domeny. Te urządzenia są czasami zbyt inwazyjne i zakłócają podstawowy ruch RPC.

Przekazanie testowe w prostych scenariuszach. Im więcej zmiennych wprowadzisz, tym więcej może być będą konkurować o. Aby zaoszczędzić czas, Ogranicz testowanie do jednego łącznika. Dodaj kolejne łączniki po rozwiązaniu problemu.

Niektóre czynniki środowiskowe mogą również przyczynić się do problemu. Aby uniknąć tych czynników, Zminimalizuj architekturę możliwie największej ilości podczas testowania. Przykładowo nieprawidłowo skonfigurowane listy ACL zapory wewnętrznej są wspólne. Jeśli to możliwe, Wyślij cały ruch z łącznika bezpośrednio do kontrolera domeny i aplikacji zaplecza.

Najlepszym miejscem do położenia łączników jest możliwie najbliżej ich celów. Zapora, która jest wbudowana, gdy testowanie dodaje niepotrzebną złożoność i może przedłużyć swoje badania.

Co zawiera problem z KCD? Istnieje kilka typowych wskazań, na które KCD Logowanie jednokrotne kończy się niepowodzeniem. Pierwsze objawy problemu pojawia się w przeglądarce.

![Przykład: nieprawidłowy błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic1.png)

![Przykład: Autoryzacja nie powiodła się z powodu brakujących uprawnień](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic2.png)

Oba te obrazy przedstawiają ten sam objaw: awaria logowania jednokrotnego. Odmowa dostępu użytkownika do aplikacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Sposób rozwiązywania problemów zależy od problemu i symptomów. Przed przeprowadzeniem dalszych czynności zapoznaj się z następującymi artykułami. Zapewniają przydatne informacje dotyczące rozwiązywania problemów:

- [Rozwiązywanie problemów z serwerem proxy aplikacji i problemów związanych z komunikatami o błędach](application-proxy-troubleshoot.md)
- [Błędy i objawy protokołu Kerberos](application-proxy-troubleshoot.md#kerberos-errors)
- [Praca z logowaniem jednokrotnym w przypadku tożsamości lokalnych i w chmurze nie jest identyczna](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities)

Jeśli masz do tego punktu, główny problem istnieje. Aby rozpocząć, oddziel przepływ na następujące trzy etapy, z którymi możesz rozwiązać problemy.

### <a name="client-pre-authentication"></a>Wstępne uwierzytelnianie klienta

Użytkownik zewnętrzny uwierzytelniający się na platformie Azure za pośrednictwem przeglądarki. Możliwość wstępnego uwierzytelnienia na platformie Azure jest niezbędna do działania rejestracji jednokrotnej KCD. Przetestuj i Rozwiąż tę możliwość w przypadku wystąpienia problemów. Etap wstępnego uwierzytelniania nie jest związany z KCDą ani opublikowaną aplikacją. Można łatwo poprawić wszelkie niezgodności, Sanity sprawdzając, czy konto podmiotu istnieje na platformie Azure. Sprawdź również, czy nie jest on wyłączony ani zablokowany. Odpowiedź na błąd w przeglądarce jest wystarczająco opisowa, aby wyjaśnić przyczynę. Jeśli nie masz pewności, zapoznaj się z innymi artykułami dotyczącymi rozwiązywania problemów firmy Microsoft, aby sprawdzić.

### <a name="delegation-service"></a>Usługa delegowania

Łącznik serwera proxy platformy Azure, który pobiera bilet usługi Kerberos dla użytkowników z centrum dystrybucji kluczy protokołu Kerberos (KCD).

Komunikacja zewnętrzna między klientem a frontonem platformy Azure nie ma wpływu na KCD. Te wiadomości sprawdzają się tylko wtedy, gdy KCD działa. Usługa serwera proxy platformy Azure ma prawidłowy identyfikator użytkownika używany do uzyskiwania biletu protokołu Kerberos. Bez tego identyfikatora KCD nie jest możliwe i kończy się niepowodzeniem.

Jak wspomniano wcześniej, komunikaty o błędach przeglądarki zawierają pewne dobre wskazówki dotyczące przyczyny niepowodzenia. Upewnij się, że w odpowiedzi Zanotuj identyfikator działania i sygnaturę czasową. Te informacje ułatwiają skorelowanie zachowania rzeczywistych zdarzeń w dzienniku zdarzeń serwera proxy platformy Azure.

![Przykład: nieprawidłowy błąd konfiguracji KCD](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic3.png)

Odpowiednie wpisy widoczne w dzienniku zdarzeń są wyświetlane jako zdarzenia 13019 lub 12027. Znajdź dzienniki zdarzeń łącznika w obszarze **Dzienniki aplikacji i usług** &gt; **Microsoft** &gt; **AadApplicationProxy** &gt; **Connector** &gt; **admin**.

![Zdarzenie 13019 z dziennika zdarzeń serwera proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic4.png)

![Zdarzenie 12027 z dziennika zdarzeń serwera proxy aplikacji](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic5.png)

1. Użyj rekordu **A** w wewnętrznym systemie DNS dla adresu aplikacji, a nie **CNAME**.
1. Upewnij się, że host łącznika ma przyznane prawo do delegowania do nazwy SPN wskazanego konta docelowego. Upewnij się, że wybrano **opcja Użyj dowolnego protokołu uwierzytelniania** . Aby uzyskać więcej informacji, zobacz [artykuł Konfiguracja logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md).
1. Sprawdź, czy istnieje tylko jedno wystąpienie nazwy SPN w usłudze Azure AD. Problem `setspn -x` z wiersza polecenia na dowolnym hoście elementu członkowskiego domeny.
1. Sprawdź, czy zasady domeny są wymuszane, które ograniczają [Maksymalny rozmiar wystawionych tokenów Kerberos](https://blogs.technet.microsoft.com/askds/2012/09/12/maxtokensize-and-windows-8-and-windows-server-2012/). Te zasady uniemożliwiają łącznikowi uzyskanie tokenu, jeśli okaże się zbyt długi.

Ślad sieciowy, który przechwytuje wymianę między hostem łącznika a domeną KDC, jest następnym najlepszym krokiem, aby uzyskać więcej szczegółowych informacji o problemach. Aby uzyskać więcej informacji, zobacz [dokument głębokiego rozwiązywania problemów szczegółowe](https://aka.ms/proxytshootpaper).

Jeśli bilet jest dobry, zobaczysz zdarzenie w dziennikach informujących, że uwierzytelnianie nie powiodło się, ponieważ aplikacja zwróciła 401. To zdarzenie oznacza, że aplikacja docelowa odrzuciła bilet. Przejdź do kolejnego etapu.

### <a name="target-application"></a>Aplikacja docelowa

Odbiorca biletu Kerberos dostarczony przez łącznik. Na tym etapie oczekujesz, że łącznik wysłał bilet usługi Kerberos do zaplecza. Ten bilet jest nagłówkiem w pierwszym żądaniu aplikacji.

1. Używając wewnętrznego adresu URL aplikacji zdefiniowanego w portalu, sprawdź, czy aplikacja jest dostępna bezpośrednio z przeglądarki na hoście łącznika. Następnie możesz zalogować się pomyślnie. Szczegóły można znaleźć na stronie **Rozwiązywanie problemów** dotyczących łącznika.
1. Nadal na hoście łącznika upewnij się, że uwierzytelnianie między przeglądarką a aplikacją korzysta z protokołu Kerberos. Wykonaj jedno z następujących działań:
1. Uruchom program DevTools (**F12**) w programie Internet Explorer lub Użyj [programu Fiddler](https://blogs.msdn.microsoft.com/crminthefield/2012/10/10/using-fiddler-to-check-for-kerberos-auth/) z hosta łącznika. Przejdź do aplikacji przy użyciu wewnętrznego adresu URL. Sprawdź proponowane nagłówki autoryzacji WWW zwrócone w odpowiedzi aplikacji, aby upewnić się, że jest obecna wartość Negotiate lub Kerberos.

   - Następny obiekt BLOB protokołu Kerberos, który jest zwracany w odpowiedzi z przeglądarki do aplikacji rozpoczyna się od **YII**. Te litery informują o tym, że protokół Kerberos jest uruchomiony. Z drugiej strony program Microsoft NT LAN Manager (NTLM) zawsze zaczyna się od **TlRMTVNTUAAB**, który odczytuje dostawcę obsługi zabezpieczeń NTLM (NTLMSSP) podczas dekodowania z poziomu algorytmu Base64. Jeśli na początku obiektu BLOB widzisz **TlRMTVNTUAAB** , protokół Kerberos nie jest dostępny. Jeśli nie widzisz **TlRMTVNTUAAB**, prawdopodobnie jest dostępny protokół Kerberos.

      > [!NOTE]
      > Jeśli używasz programu Fiddler, ta metoda wymaga tymczasowego wyłączenia ochrony rozszerzonej w konfiguracji aplikacji w usługach IIS.

      ![Okno inspekcji sieci przeglądarki](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic6.png)

   - Obiekt BLOB na tym rysunku nie zaczyna się od **TIRMTVNTUAAB**. W tym przykładzie jest dostępny protokół Kerberos, a obiekt BLOB protokołu Kerberos nie zaczyna się od **YII**.

1. Tymczasowo Usuń NTLM z listy dostawców w witrynie usług IIS. Uzyskaj dostęp do aplikacji bezpośrednio z programu Internet Explorer na hoście łącznika. Uwierzytelnianie NTLM nie znajduje się już na liście dostawców. Możesz uzyskać dostęp do aplikacji, używając tylko protokołu Kerberos. W przypadku niepowodzenia dostępu może wystąpić problem z konfiguracją aplikacji. Uwierzytelnianie Kerberos nie działa.

   - Jeśli protokół Kerberos jest niedostępny, sprawdź ustawienia uwierzytelniania aplikacji w usługach IIS. Upewnij się, że w górnej części znajduje się wartość **Negocjuj** z uwierzytelnianiem NTLM tuż poniżej. Jeśli widzisz **nie Negocjuj**, **Kerberos lub Negotiate**lub **protokołu PKU2U**, Kontynuuj tylko wtedy, gdy protokół Kerberos jest funkcjonalny.

     ![Dostawcy uwierzytelniania systemu Windows](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic7.png)

   - W przypadku protokołu Kerberos i NTLM w miejscu należy tymczasowo wyłączyć uwierzytelnianie wstępne dla aplikacji w portalu. Spróbuj uzyskać dostęp do niego z Internetu przy użyciu zewnętrznego adresu URL. Zostanie wyświetlony monit o uwierzytelnienie. Możesz to zrobić za pomocą tego samego konta, które zostało użyte w poprzednim kroku. W przeciwnym razie występuje problem z aplikacją zaplecza, a nie KCD.
   - Włącz ponownie uwierzytelnianie wstępne w portalu. Uwierzytelnij się za pośrednictwem platformy Azure, próbując nawiązać połączenie z aplikacją za pośrednictwem zewnętrznego adresu URL. Jeśli logowanie jednokrotne nie powiedzie się, zobaczysz niedozwolony komunikat o błędzie w przeglądarce i zdarzeniu 13022 w dzienniku:

     *Łącznik serwera proxy aplikacji usługi Microsoft AAD nie może uwierzytelnić użytkownika, ponieważ serwer zaplecza odpowiada na próby uwierzytelniania Kerberos z powodu błędu HTTP 401.*

      ![Pokazuje HTTTP 401 — Błąd zabroniony](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic8.png)

   - Sprawdź aplikację IIS. Upewnij się, że skonfigurowana Pula aplikacji i Nazwa SPN są skonfigurowane tak, aby korzystały z tego samego konta w usłudze Azure AD. Przejdź do usług IIS, jak pokazano na poniższej ilustracji:

      ![Okno konfiguracji aplikacji usług IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic9.png)

      Po poznaniu tożsamości upewnij się, że to konto jest skonfigurowane z daną nazwą SPN. Może to być na przykład `setspn –q http/spn.wacketywack.com`. Wprowadź następujący tekst w wierszu polecenia:

      ![Pokazuje okno poleceń SetSPN](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic10.png)

   - Sprawdź nazwę SPN zdefiniowaną w ustawieniach aplikacji w portalu. Upewnij się, że ta sama nazwa SPN skonfigurowana dla docelowego konta usługi Azure AD jest używana przez pulę aplikacji aplikacji.

      ![Konfiguracja nazw SPN w Azure Portal](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic11.png)

   - Przejdź do usług IIS i wybierz opcję **Edytor konfiguracji** dla aplikacji. Przejdź do **System. WebServer/Security/Authentication/WindowsAuthentication**. Upewnij się, że wartość **UseAppPoolCredentials** ma wartość **true**.

      ![Opcja poświadczeń puli aplikacji konfiguracji programu IIS](./media/application-proxy-back-end-kerberos-constrained-delegation-how-to/graphic12.png)

      Zmień tę wartość na **true**. Usuń wszystkie buforowane bilety protokołu Kerberos z serwera zaplecza, uruchamiając następujące polecenie:

      ```powershell
      Get-WmiObject Win32_LogonSession | Where-Object {$_.AuthenticationPackage -ne 'NTLM'} | ForEach-Object {klist.exe purge -li ([Convert]::ToString($_.LogonId, 16))}
      ```

Aby uzyskać więcej informacji, zobacz [przeczyszczanie pamięci podręcznej biletu klienta protokołu Kerberos dla wszystkich sesji](https://gallery.technet.microsoft.com/scriptcenter/Purge-the-Kerberos-client-b56987bf).

Pozostawienie trybu jądra włączone powoduje zwiększenie wydajności operacji protokołu Kerberos. Powoduje również, że bilet dla wymaganej usługi można odszyfrować przy użyciu konta komputera. To konto jest również nazywane systemem lokalnym. Ustaw tę wartość na **true** , aby przerwać KCD, gdy aplikacja jest hostowana na więcej niż jednym serwerze w farmie.

- Jako dodatkowe sprawdzenie Wyłącz również ochronę **rozszerzoną** . W niektórych scenariuszach **rozszerzona** ochrona została złamana KCD, gdy została włączona w określonych konfiguracjach. W takich przypadkach aplikacja została opublikowana jako podfolder domyślnej witryny sieci Web. Ta aplikacja jest skonfigurowana tylko do uwierzytelniania anonimowego. Wszystkie okna dialogowe są wyszarzone, co sugeruje, że obiekty podrzędne nie dziedziczą żadnych aktywnych ustawień. Zalecamy przetestowanie, ale nie zapomni **, aby przywrócić tę wartość, jeśli**jest to możliwe.

  To dodatkowe sprawdzenie umożliwia śledzenie użycia opublikowanej aplikacji. Możesz uruchomić dodatkowe łączniki, które są również skonfigurowane do delegowania. Aby uzyskać więcej informacji, zapoznaj się z bardziej szczegółowym opisem technicznym, [Rozwiązywanie problemów z usługą Azure serwer proxy aplikacji usługi Azure AD](https://aka.ms/proxytshootpaper).

Jeśli nadal nie możesz wykonać tego postępu, pomoc techniczna firmy Microsoft może Ci pomóc. Utwórz bilet pomocy technicznej bezpośrednio w portalu. Inżynier skontaktuje się z Tobą.

## <a name="other-scenarios"></a>Inne scenariusze

- Usługa Azure Application proxy żąda biletu protokołu Kerberos przed wysłaniem żądania do aplikacji. Niektóre aplikacje innych firm nie są podobne do tej metody uwierzytelniania. Aplikacje te oczekują bardziej konwencjonalnych negocjacji. Pierwsze żądanie jest anonimowe, co umożliwia aplikacji reagowanie na typy uwierzytelniania obsługiwane przez 401. Ten typ negocjacji Kerberos można włączyć, wykonując czynności opisane w tym dokumencie: [ograniczone delegowanie protokołu Kerberos na potrzeby logowania](application-proxy-configure-single-sign-on-with-kcd.md)jednokrotnego.
- Uwierzytelnianie z użyciem wieloskładnikowym jest często używane w scenariuszach, w których aplikacja jest warstwowa, z zapleczem i frontonem, gdzie oba wymagają uwierzytelniania, takie jak SQL Server Reporting Services. Aby skonfigurować scenariusz wielodostępny, zobacz artykuł pomocy technicznej [ograniczone delegowanie protokołu Kerberos może wymagać przejścia do protokołu w scenariuszach z przeskokiem](https://support.microsoft.com/help/2005838/kerberos-constrained-delegation-may-require-protocol-transition-in-mul).

## <a name="next-steps"></a>Następne kroki

[Skonfiguruj KCD w domenie zarządzanej](../../active-directory-domain-services/deploy-kcd.md).
