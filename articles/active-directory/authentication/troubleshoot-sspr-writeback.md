---
title: Rozwiązywanie problemów z funkcją zapisywania zwrotnego samoobsługowego resetowania haseł — Azure Active Directory
description: Dowiedz się, jak rozwiązywać typowe problemy i kroki rozwiązania do samoobsługowego resetowania hasła w Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0620304de1866d24719b137836419502cd25bee9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682241"
---
# <a name="troubleshoot-self-service-password-reset-writeback-in-azure-active-directory"></a>Rozwiązywanie problemów z funkcją stornowania samoobsługowego resetowania haseł w Azure Active Directory

Usługa Azure Active Directory (Azure AD) Samoobsługowe resetowanie haseł (SSPR) umożliwia użytkownikom Resetowanie swoich haseł w chmurze. Funkcja zapisywania zwrotnego haseł jest funkcją włączoną [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) , która umożliwia zapisywanie zmian haseł w chmurze z powrotem do istniejącego katalogu lokalnego w czasie rzeczywistym.

Jeśli masz problemy z funkcją zapisywania zwrotnego SSPR, następujące kroki rozwiązywania problemów i typowe błędy mogą pomóc. Jeśli nie możesz znaleźć odpowiedzi na Twój problem, [nasze zespoły pomocy technicznej są zawsze dostępne](#contact-microsoft-support) , aby pomóc Ci w dalszej próbie.

## <a name="troubleshoot-connectivity"></a>Rozwiązywanie problemów z łącznością

Jeśli masz problemy z funkcją zapisywania zwrotnego haseł dla Azure AD Connect, zapoznaj się z poniższymi krokami, które mogą pomóc w rozwiązaniu problemu. W celu odzyskania usługi zalecamy wykonanie następujących czynności w kolejności:

* [Potwierdź łączność sieciową](#confirm-network-connectivity)
* [Uruchom ponownie usługę synchronizacji Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł](#disable-and-re-enable-the-password-writeback-feature)
* [Zainstaluj najnowszą wersję Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](#common-password-writeback-errors)

### <a name="confirm-network-connectivity"></a>Potwierdź łączność sieciową

Najbardziej typowym punktem awarii jest niepoprawne skonfigurowanie zapory lub portów proxy lub limitów czasu bezczynności.

W przypadku Azure AD Connect w wersji *wersji 1.1.443.0* lub nowszej dostęp do *wychodzącego protokołu HTTPS* jest wymagany dla następujących adresów:

* *\*. passwordreset.microsoftonline.com*
* *\*. servicebus.windows.net*

[Punkty końcowe](../../azure-government/compare-azure-government-global-azure.md#guidance-for-developers)usługi Azure gov:

* *\*. passwordreset.microsoftonline.us*
* *\*. servicebus.usgovcloudapi.net*

Jeśli potrzebujesz większej szczegółowości, zapoznaj się z [listą zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Ta lista jest aktualizowana każdą środę i zacznie obowiązywać w następnym poniedziałek.

Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące łączności dla Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md).

### <a name="restart-the-azure-ad-connect-sync-service"></a>Uruchom ponownie usługę synchronizacji Azure AD Connect

Aby rozwiązać problemy z łącznością lub inne problemy przejściowe w usłudze, wykonaj następujące kroki, aby ponownie uruchomić usługę synchronizacji Azure AD Connect:

1. Jako administrator na serwerze, na którym działa Azure AD Connect, wybierz pozycję **Uruchom**.
1. W polu wyszukiwania wprowadź ciąg *Services. msc* , a następnie wybierz pozycję **wprowadź**.
1. Wyszukaj wpis *synchronizacji Microsoft Azure AD* .
1. Kliknij prawym przyciskiem myszy wpis usługi, wybierz pozycję **Uruchom ponownie** i poczekaj na zakończenie operacji.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/service-restart.png" alt-text="Uruchom ponownie usługę Azure AD Sync przy użyciu graficznego interfejsu użytkownika" border="false":::

Te kroki powodują ponowne nawiązanie połączenia z usługą Azure AD i rozwiązanie problemów z łącznością.

Jeśli ponowne uruchomienie usługi synchronizacji Azure AD Connect nie rozwiąże problemu, spróbuj wyłączyć, a następnie ponownie włączyć funkcję zapisywania zwrotnego haseł w następnej sekcji.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł

Aby kontynuować rozwiązywanie problemów, wykonaj następujące kroki, aby wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł:

1. Jako administrator na serwerze, na którym działa Azure AD Connect, Otwórz **Kreatora konfiguracji Azure AD Connect**.
1. W obszarze **nawiązywanie połączenia z usługą Azure AD** wprowadź swoje poświadczenia administratora globalnego usługi Azure AD.
1. W obszarze **Połącz z AD DS** wprowadź poświadczenia administratora lokalnego Active Directory Domain Services.
1. W **unikatowy sposób identyfikujący użytkowników**, wybierz przycisk **dalej** .
1. W obszarze **funkcje opcjonalne** wyczyść pole wyboru **zapisywanie zwrotne haseł** .
1. Wybierz pozycję **dalej** przez pozostałe strony okna dialogowego bez zmiany wszystkiego, dopóki nie przejdziesz do strony **gotowy do konfiguracji** .
1. Sprawdź, czy **Strona gotowy do konfiguracji** zawiera opcję *zapisywania zwrotnego haseł* jako *wyłączoną*. Wybierz przycisk zielonej **konfiguracji** , aby zatwierdzić wprowadzone zmiany.
1. Na **koniec** wyczyść opcję **Synchronizuj teraz** , a następnie wybierz pozycję **Zakończ** , aby zamknąć kreatora.
1. Otwórz ponownie **Kreatora konfiguracji Azure AD Connect**.
1. Powtórz kroki 2-8, tym razem wybierając opcję *zapisywania zwrotnego haseł* na stronie **funkcje opcjonalne** , aby ponownie włączyć usługę.

Te kroki powodują ponowne nawiązanie połączenia z usługą Azure AD i rozwiązanie problemów z łącznością.

Jeśli wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł nie rozwiąże problemu, Zainstaluj Azure AD Connect w następnej sekcji.

### <a name="install-the-latest-azure-ad-connect-release"></a>Zainstaluj najnowszą wersję Azure AD Connect

Ponowne zainstalowanie Azure AD Connect może rozwiązać problemy związane z konfiguracją i połączeniem między usługą Azure AD a lokalnym środowiskiem Active Directory Domain Services. Zalecamy wykonanie tego kroku dopiero po wykonaniu poprzednich kroków w celu zweryfikowania i rozwiązania problemów z łącznością.

> [!WARNING]
> Jeśli dostosowano wbudowane reguły synchronizacji, *przed kontynuowaniem uaktualniania należy wykonać ich kopię zapasową, a następnie ręcznie wdrożyć je ponownie po zakończeniu.*

1. Pobierz najnowszą wersję Azure AD Connect z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Ponieważ Azure AD Connect już zainstalowana, wykonaj uaktualnienie w miejscu, aby zaktualizować instalację Azure AD Connect do najnowszej wersji.

    Uruchom pobrany pakiet i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zaktualizować Azure AD Connect.

Te kroki powinny ponownie nawiązać połączenie z usługą Azure AD i rozwiązać problemy z łącznością.

Jeśli instalacja najnowszej wersji serwera Azure AD Connect nie rozwiąże problemu, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł w ostatnim kroku po zainstalowaniu najnowszej wersji.

## <a name="verify-that-azure-ad-connect-has-the-required-permissions"></a>Sprawdź, czy Azure AD Connect ma wymagane uprawnienia

Azure AD Connect wymaga uprawnień do AD DS **resetowania hasła** , aby można było wykonać funkcję zapisywania zwrotnego haseł. Aby sprawdzić, czy Azure AD Connect ma wymagane uprawnienie dla danego lokalnego konta użytkownika AD DS, użyj funkcji **uprawnienia efektywne systemu Windows** :

1. Zaloguj się na serwerze Azure AD Connect i uruchom **Synchronization Service Manager** , wybierając pozycję **Uruchom**  >  **usługę synchronizacji**.
1. Na karcie **Łączniki** wybierz pozycję lokalna **Active Directory Domain Services** łącznika, a następnie wybierz pozycję **Właściwości**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager.png" alt-text="Synchronization Service Manager pokazujący, jak edytować właściwości" border="false":::
  
1. W oknie podręcznym wybierz pozycję **Połącz z lasem Active Directory** i zanotuj Właściwość **Nazwa użytkownika** . Ta właściwość jest kontem AD DS używanym przez Azure AD Connect do przeprowadzenia synchronizacji katalogów.

    Aby Azure AD Connect do wykonywania funkcji zapisywania zwrotnego haseł, konto AD DS musi mieć uprawnienie resetowania hasła. Uprawnienia do tego konta użytkownika są sprawdzane w poniższych krokach.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/synchronization-service-manager-properties.png" alt-text="Znajdowanie usługi synchronizacji Active Directory konta użytkownika" border="false":::
  
1. Zaloguj się do lokalnego kontrolera domeny i uruchom aplikację **Active Directory Użytkownicy i komputery** .
1. Wybierz pozycję **Widok** i upewnij się, że jest włączona opcja **funkcje zaawansowane** .  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-advanced-features.png" alt-text="Active Directory Użytkownicy i komputery pokazują funkcje zaawansowane" border="false":::
  
1. Znajdź AD DS konto użytkownika, które chcesz zweryfikować. Kliknij prawym przyciskiem myszy nazwę konta i wybierz pozycję **Właściwości**.  
1. W oknie podręcznym przejdź do karty **zabezpieczenia** i wybierz pozycję **Zaawansowane**.  
1. W oknie podręcznym **Zaawansowane ustawienia zabezpieczeń dla administratora** przejdź do karty **dostęp czynny** .
1. Wybierz **pozycję Wybierz użytkownika**, wybierz konto AD DS używane przez Azure AD Connect, a następnie wybierz pozycję **Wyświetl dostęp czynny**.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-effective-access.png" alt-text="Karta dostęp czynny z informacjami o koncie synchronizacji" border="false":::
  
1. Przewiń w dół i Wyszukaj **hasło resetowania hasła**. Jeśli wpis ma znacznik wyboru, konto AD DS ma uprawnienia do resetowania hasła wybranego Active Directorygo konta użytkownika.  

    :::image type="content" source="./media/troubleshoot-sspr-writeback/check-permissions.png" alt-text="Sprawdzanie, czy konto synchronizacji ma uprawnienie Resetowanie hasła" border="false":::

## <a name="common-password-writeback-errors"></a>Typowe błędy zapisywania zwrotnego haseł

W przypadku zapisywania zwrotnego haseł mogą wystąpić następujące bardziej szczegółowe problemy. Jeśli masz jeden z tych błędów, Przejrzyj proponowane rozwiązanie i sprawdź, czy zapisywanie zwrotne haseł działa poprawnie.

| Błąd | Rozwiązanie |
| --- | --- |
| Usługa resetowania haseł nie jest uruchamiana lokalnie. Błąd 6800 jest wyświetlany w dzienniku zdarzeń aplikacji maszyny Azure AD Connect. <br> <br> Po dołączeniu, federacyjnym, uwierzytelnianiu lub uwierzytelnianiem w skrócie hasła użytkownicy nie mogą resetować haseł. | Po włączeniu funkcji zapisywania zwrotnego haseł aparat synchronizacji wywołuje bibliotekę zapisywania zwrotnego w celu przeprowadzenia konfiguracji (dołączania), komunikując się z usługą w chmurze. Wszelkie błędy napotkane podczas dołączania lub podczas uruchamiania punktu końcowego Windows Communication Foundation (WCF) na potrzeby zapisywania zwrotnego haseł w dzienniku zdarzeń na komputerze Azure AD Connect. <br> <br> Podczas ponownego uruchamiania usługi Azure AD Sync (ADSync), jeśli został skonfigurowany zapis, zostanie uruchomiony punkt końcowy WCF. Jeśli jednak uruchomienie punktu końcowego nie powiedzie się, rejestrujemy zdarzenie 6800 i zezwala na uruchomienie usługi synchronizacji. Obecność tego zdarzenia oznacza, że punkt końcowy zapisywania zwrotnego haseł nie został uruchomiony. Szczegóły dziennika zdarzeń dla tego zdarzenia 6800, a także wpisy dziennika zdarzeń generowane przez składnik PasswordResetService, wskazują dlaczego nie można uruchomić punktu końcowego. Przejrzyj te błędy w dzienniku zdarzeń i spróbuj ponownie uruchomić Azure AD Connect, jeśli zapisywanie zwrotne haseł nadal nie działa. Jeśli problem będzie się powtarzał, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.
| Gdy użytkownik podejmie próbę zresetowania hasła lub odblokowania konta z włączoną funkcją zapisywania zwrotnego haseł, operacja kończy się niepowodzeniem. <br> <br> Ponadto zostanie wyświetlone zdarzenie w dzienniku zdarzeń Azure AD Connect zawierający: "aparat synchronizacji zwrócił błąd HR = 800700CE, Message = nazwa pliku lub rozszerzenie jest zbyt długie" po wystąpieniu operacji odblokowywania. | Znajdź konto Active Directory dla Azure AD Connect i zresetuj hasło, tak aby nie zawierało więcej niż 256 znaków. Następnie otwórz **usługę synchronizacji** z menu **Start** . Przejdź do **łączników** i Znajdź **Active Directory łącznik**. Zaznacz go, a następnie wybierz pozycję **Właściwości**. Przejdź do strony **poświadczenia** i wprowadź nowe hasło. Wybierz **przycisk OK** , aby zamknąć stronę. |
| W ostatnim kroku procesu instalacji Azure AD Connect zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można skonfigurować funkcji zapisywania zwrotnego haseł. <br> <br> Dziennik zdarzeń aplikacji Azure AD Connect zawiera błąd 32009 z tekstem "błąd podczas pobierania tokenu uwierzytelniania". | Ten błąd występuje w następujących dwóch przypadkach: <br><ul><li>Podano nieprawidłowe hasło do konta administratora globalnego podanego na początku procesu instalacji Azure AD Connect.</li><li>Podjęto próbę użycia użytkownika federacyjnego dla konta administratora globalnego określonego na początku procesu instalacji Azure AD Connect.</li></ul> Aby rozwiązać ten problem, upewnij się, że nie używasz konta federacyjnego dla administratora globalnego określonego na początku procesu instalacji, i że określone hasło jest poprawne. |
| Dziennik zdarzeń Azure AD Connect Machine zawiera błąd 32002, który jest generowany przez uruchomienie PasswordResetService. <br> <br> Błąd: "błąd podczas nawiązywania połączenia z ServiceBus. Dostawca tokenu nie może dostarczyć tokenu zabezpieczającego ". | W środowisku lokalnym nie można nawiązać połączenia z punktem końcowym Azure Service Bus w chmurze. Ten błąd jest zwykle spowodowany przez regułę zapory blokującą połączenie wychodzące z określonym portem lub adresem sieci Web. Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące łączności](../hybrid/how-to-connect-install-prerequisites.md) . Po zaktualizowaniu tych reguł należy ponownie uruchomić serwer Azure AD Connect i zapisywanie zwrotne haseł powinno zacząć działać ponownie. |
| Po pewnym czasie, federacyjnym, uwierzytelnianiem przekazującym lub użytkownikami z synchronizacją skrótu hasła nie można resetować haseł. | W niektórych rzadkich przypadkach usługa zapisywania zwrotnego haseł może zakończyć się niepowodzeniem po ponownym uruchomieniu Azure AD Connect. W takich przypadkach należy najpierw sprawdzić, czy funkcja zapisywania zwrotnego haseł jest włączona lokalnie. Możesz sprawdzić za pomocą Kreatora Azure AD Connect lub programu PowerShell. Jeśli funkcja jest wyświetlona, spróbuj ponownie włączyć lub wyłączyć tę funkcję. Jeśli ten krok rozwiązywania problemów nie działa, spróbuj przeprowadzić pełne odinstalowanie i ponowne zainstalowanie Azure AD Connect. |
| Federacyjnego, przekazywane uwierzytelnianie lub Użytkownicy z synchronizacją hasła, którzy próbują zresetować swoje hasła, zobaczą błąd po próbie przesłania hasła. Błąd wskazuje, że wystąpił problem z usługą. <br ><br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony komunikat o błędzie, że agent zarządzania odmówił dostępu w lokalnych dziennikach zdarzeń. | Jeśli te błędy są widoczne w dzienniku zdarzeń, upewnij się, że konto agenta zarządzania Active Directory (ADMA) określone w Kreatorze w momencie konfiguracji ma uprawnienia wymagane do zapisywania zwrotnego haseł. <br> <br> Po podaniu tego uprawnienia może upłynąć do godziny, gdy uprawnienia Trickle się w `sdprop` tle na kontrolerze domeny (DC). <br> <br> Aby Resetowanie hasła działało, uprawnienie musi być oznaczone przy użyciu deskryptora zabezpieczeń obiektu użytkownika, którego hasło jest resetowane. Dopóki to uprawnienie nie zostanie wyświetlone w obiekcie User, Resetowanie hasła nadal kończy się niepowodzeniem z komunikatem o odmowie dostępu. |
| Federacyjne, przekazywane uwierzytelnianie lub Użytkownicy ze skróconymi hasłami, którzy próbują zresetować swoje hasła, zobaczą błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony komunikat o błędzie w dziennikach zdarzeń z usługi Azure AD Connectej wskazujący, że nie można odnaleźć obiektu. | Ten błąd zazwyczaj wskazuje, że aparat synchronizacji nie może znaleźć obiektu użytkownika w obszarze łącznika usługi Azure AD lub połączonym obiekcie Metaverse (MV) lub w powiązanym z nim łącznika usługi Azure AD. <br> <br> Aby rozwiązać ten problem, upewnij się, że użytkownik jest rzeczywiście synchronizowany z lokalnego do usługi Azure AD za pośrednictwem bieżącego wystąpienia Azure AD Connect i sprawdź stan obiektów w miejscach łączników i MV. Upewnij się, że obiekt usługi certyfikatów Active Directory (AD CS) jest połączony z obiektem MV za pośrednictwem reguły "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federacyjne, przekazywane uwierzytelnianie lub Użytkownicy z synchronizacją hasła, którzy próbują zresetować swoje hasła, zobaczą błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony błąd w dziennikach zdarzeń z usługi Azure AD Connect, która wskazuje, że występuje błąd "znaleziono wiele dopasowań". | Oznacza to, że aparat synchronizacji wykrył, że obiekt MV jest podłączony do więcej niż jednego obiektu usług AD CS za pośrednictwem "Microsoft.InfromADUserAccountEnabled.xxx". Oznacza to, że użytkownik ma włączone konto w więcej niż jednym lesie. Ten scenariusz nie jest obsługiwany w przypadku zapisywania zwrotnego haseł. |
| Operacje na haśle kończą się niepowodzeniem z powodu błędu konfiguracji. Dziennik zdarzeń aplikacji zawiera błąd Azure AD Connect 6329 z tekstem "0x8023061f (operacja nie powiodła się, ponieważ nie włączono synchronizacji haseł na tym agencie zarządzania)". | Ten błąd występuje w przypadku zmiany konfiguracji Azure AD Connect w celu dodania nowego lasu Active Directory (lub usunięcia i odczytania istniejącego lasu) po włączeniu funkcji zapisywania zwrotnego haseł. Operacje na hasłach dla użytkowników w tych ostatnio dodanych lasach kończą się niepowodzeniem. Aby rozwiązać ten problem, Wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego haseł po zakończeniu zmian konfiguracji lasu. |

## <a name="password-writeback-event-log-error-codes"></a>Kody błędów w dzienniku zdarzeń zapisywania zwrotnego haseł

Najlepszym rozwiązaniem w przypadku rozwiązywania problemów z funkcją zapisywania zwrotnego haseł jest zbadanie dziennika zdarzeń aplikacji na komputerze Azure AD Connect. Ten dziennik zdarzeń zawiera zdarzenia z dwóch źródeł na potrzeby zapisywania zwrotnego haseł. Źródło *PasswordResetService* opisuje operacje i problemy związane z operacją zapisywania zwrotnego haseł. Źródło *ADSync* opisuje operacje i problemy związane z ustawianiem haseł w środowisku Active Directory Domain Servicesu.

### <a name="if-the-source-of-the-event-is-adsync"></a>Jeśli źródłem zdarzenia jest ADSync

| Kod | Nazwa lub komunikat | Opis |
| --- | --- | --- |
| 6329 | Poprzednia: MMS (4924) 0x80230619: "ograniczenie uniemożliwia zmianę hasła do bieżącego określonego." | To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku, historii, złożoności lub filtrowania hasła domeny. <br> <br> Jeśli użytkownik ma minimalny okres ważności hasła i ostatnio zmienił hasło w tym oknie, nie można ponownie zmienić hasła, dopóki nie osiągnie on określonego wieku w domenie. W celach testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli są włączone wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *n* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ciągu ostatnich *N* razy, zobaczysz błąd w tym przypadku. W celach testowych historia hasła powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli włączono filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany zakończy się niepowodzeniem. |
| 6329 | MMS (3040): admaexport. cpp (2837): serwer nie zawiera kontroli zasad haseł LDAP. | Ten problem występuje, gdy w kontrolerach domen nie jest włączony formant LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066). Aby użyć funkcji zapisywania zwrotnego haseł, należy włączyć kontrolkę. W tym celu kontrolery domeny muszą być w systemie Windows Server 2008R2 lub nowszym. |
| HR 8023042 | Aparat synchronizacji zwrócił błąd HR = 80230402, Message = próba pobrania obiektu nie powiodła się, ponieważ istnieją zduplikowane wpisy z tą samą kotwicą. | Ten błąd występuje, gdy ten sam identyfikator użytkownika jest włączony w wielu domenach. Przykładem jest synchronizacja lasów kont i zasobów oraz posiadanie tego samego identyfikatora użytkownika i włączenie go w każdym lesie. <br> <br> Ten błąd może również wystąpić, jeśli używasz nieunikatowego atrybutu zakotwiczenia, takiego jak alias lub nazwa UPN, i dwóch użytkowników współużytkują ten sam atrybut zakotwiczenia. <br> <br> Aby rozwiązać ten problem, upewnij się, że nie masz żadnych zduplikowanych użytkowników w domenach i używasz unikatowego atrybutu zakotwiczenia dla każdego użytkownika. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Jeśli źródłem zdarzenia jest PasswordResetService

| Kod | Nazwa lub komunikat | Opis |
| --- | --- | --- |
| 31001 | PasswordResetStart | To zdarzenie oznacza, że Usługa lokalna wykryła żądanie zresetowania hasła dla federacyjnego, przekazywanego uwierzytelniania lub użytkownika z synchronizacją skrótu hasła, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania hasła. |
| 31002 | PasswordResetSuccess | To zdarzenie oznacza, że użytkownik wybrał nowe hasło podczas operacji resetowania hasła. Ustalono, że to hasło spełnia wymagania dotyczące hasła firmowego. Hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31003 | PasswordResetFail | To zdarzenie oznacza, że użytkownik wybrał hasło, a hasło zostało pomyślnie dostarczone do środowiska lokalnego. Ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtru dla domeny. Aby rozwiązać ten problem, Utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takiej jak domena lub grupa administratorów przedsiębiorstwa, która nie zezwala na operacje ustawiania haseł.</li></ul>|
| 31004 | OnboardingEventStart | To zdarzenie występuje, jeśli włączysz funkcję zapisywania zwrotnego haseł w Azure AD Connect i rozpoczęto dołączanie organizacji do usługi sieci Web zapisywania zwrotnego haseł. |
| 31005 | OnboardingEventSuccess | To zdarzenie wskazuje, że proces dołączania zakończył się pomyślnie i że funkcja zapisywania zwrotnego haseł jest gotowa do użycia. |
| 31006 | ChangePasswordStart | To zdarzenie oznacza, że Usługa lokalna wykryła żądanie zmiany hasła dla federacyjnego, przekazywanego uwierzytelniania lub użytkownika z synchronizacją skrótu hasła, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem podczas każdej operacji zapisywania zwrotnego zmiany hasła. |
| 31007 | ChangePasswordSuccess | To zdarzenie oznacza, że użytkownik wybrał nowe hasło podczas operacji zmiany hasła, ustalił, że hasło spełnia wymagania dotyczące hasła firmowego i że hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31008 | ChangePasswordFail | To zdarzenie oznacza, że użytkownik wybrał hasło i że hasło zostało pomyślnie dostarczone do środowiska lokalnego, ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtru dla domeny. Aby rozwiązać ten problem, Utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika należy do grupy ochrony, takiej jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacje ustawiania haseł.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Usługa lokalna wykryła żądanie zresetowania hasła dla federacyjnego, uwierzytelniania przekazywanego lub użytkownika z synchronizacją skrótu z uprawnieniami administratora w imieniu użytkownika. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania hasła, która została zainicjowana przez administratora. |
| 31010 | ResetUserPasswordByAdminSuccess | Administrator zaznaczył nowe hasło podczas operacji inicjowania hasła zainicjowane przez administratora. Ustalono, że to hasło spełnia wymagania dotyczące hasła firmowego. Hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Administrator zaznaczył hasło w imieniu użytkownika. Hasło zostało pomyślnie dostarczone do środowiska lokalnego. Ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności ani filtru dla domeny. Spróbuj użyć nowego hasła, aby rozwiązać ten problem.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika należy do grupy ochrony, takiej jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacje ustawiania haseł.</li></ul>  |
| 31012 | OffboardingEventStart | To zdarzenie występuje w przypadku wyłączenia funkcji zapisywania zwrotnego haseł przy użyciu Azure AD Connect i wskazuje, że rozpoczęto odłączanie organizacji do usługi sieci Web zapisywania zwrotnego haseł. |
| 31013| OffboardingEventSuccess| To zdarzenie wskazuje, że proces odłączania zakończył się powodzeniem, a funkcja zapisywania zwrotnego haseł została pomyślnie wyłączona. |
| 31014| OffboardingEventFail| To zdarzenie wskazuje, że proces odłączania nie powiódł się. Może to być spowodowane błędem uprawnień w chmurze lub lokalnym kontem administratora określonym podczas konfiguracji. Ten błąd może również wystąpić, jeśli próbujesz użyć federacyjnego administratora globalnego w chmurze podczas wyłączania zapisywania zwrotnego haseł. Aby rozwiązać ten problem, Sprawdź uprawnienia administracyjne i upewnij się, że nie korzystasz z konta federacyjnego podczas konfigurowania funkcji zapisywania zwrotnego haseł.|
| 31015| WriteBackServiceStarted| To zdarzenie wskazuje, że usługa zapisywania zwrotnego haseł została uruchomiona pomyślnie. Jest gotowy do akceptowania żądań zarządzania hasłami z chmury.|
| 31016| WriteBackServiceStopped| To zdarzenie wskazuje, że usługa zapisywania zwrotnego haseł została zatrzymana. Wszystkie żądania zarządzania hasłami z chmury nie będą się powieść.|
| 31017| AuthTokenSuccess| To zdarzenie wskazuje, że został pomyślnie pobrany Token autoryzacji dla administratora globalnego określonego podczas instalacji Azure AD Connect, aby rozpocząć proces odłączania lub dołączania.|
| 31018| KeyPairCreationSuccess| To zdarzenie wskazuje, że klucz szyfrowania hasła został pomyślnie utworzony. Ten klucz służy do szyfrowania haseł z chmury do wysłania do środowiska lokalnego.|
| 31034| ServiceBusListenerError| To zdarzenie wskazuje, że wystąpił błąd podczas nawiązywania połączenia z odbiornikiem Service Bus dzierżawy. Jeśli komunikat o błędzie zawiera wartość "certyfikat zdalny jest nieprawidłowy", upewnij się, że serwer Azure AD Connect ma wszystkie wymagane główne urzędy certyfikacji zgodnie z opisem w temacie [zmiany certyfikatu protokołu TLS platformy Azure](../../security/fundamentals/tls-certificate-changes.md). |
| 32000| UnknownError| To zdarzenie wskazuje, że podczas operacji zarządzania hasłami wystąpił nieznany błąd. Aby uzyskać więcej informacji, zobacz tekst wyjątku w zdarzeniu. Jeśli masz problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł. Jeśli to nie pomoże, Dołącz kopię dziennika zdarzeń wraz z IDENTYFIKATORem śledzenia określonym podczas otwierania żądania obsługi.|
| 32001| Wystąpił błąd| To zdarzenie wskazuje, że wystąpił błąd podczas nawiązywania połączenia z usługą resetowania hasła w chmurze. Ten błąd występuje zazwyczaj, gdy Usługa lokalna nie może nawiązać połączenia z usługą sieci Web resetowania hasła.|
| 32002| ServiceBusError| To zdarzenie wskazuje, że wystąpił błąd podczas nawiązywania połączenia z wystąpieniem Service Bus dzierżawy. Taka sytuacja może wystąpić, jeśli blokujesz połączenia wychodzące w środowisku lokalnym. Sprawdź zaporę, aby zezwolić na połączenia za pośrednictwem protokołu TCP 443 i do programu https://ssprdedicatedsbprodncu.servicebus.windows.net , a następnie spróbuj ponownie. Jeśli nadal występują problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32003| InPutValidationError| To zdarzenie oznacza, że dane wejściowe przesłane do naszego interfejsu API usługi sieci Web były nieprawidłowe. Spróbuj ponownie wykonać operację.|
| 32004| DecryptionError| To zdarzenie wskazuje, że wystąpił błąd podczas odszyfrowywania hasła, które dotarło do chmury. Może to być spowodowane niezgodnością klucza odszyfrowywania między usługą w chmurze i środowiskiem lokalnym. Aby rozwiązać ten problem, Wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego haseł w środowisku lokalnym.|
| 32005| ConfigurationError| Podczas dołączania zapisujemy informacje specyficzne dla dzierżawy w pliku konfiguracji w środowisku lokalnym. To zdarzenie wskazuje, że wystąpił błąd podczas zapisywania tego pliku lub gdy usługa została uruchomiona, wystąpił błąd podczas odczytu pliku. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł w celu wymuszenia ponownego zapisania pliku konfiguracji.|
| 32007| OnBoardingConfigUpdateError| Podczas dołączania dane są wysyłane z chmury do lokalnej usługi resetowania haseł. Te dane są następnie zapisywane w pliku w pamięci przed wysłaniem do usługi synchronizacji w celu bezpiecznego przechowywania na dysku. To zdarzenie wskazuje, że występuje problem z zapisywaniem lub aktualizowaniem tych danych w pamięci. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł, aby wymusić ponowne zapisywanie tego pliku konfiguracji.|
| 32008| ValidationError| To zdarzenie wskazuje, że odebrano nieprawidłową odpowiedź z usługi sieci Web resetowania hasła. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32009| AuthTokenError| To zdarzenie wskazuje, że nie można uzyskać tokenu autoryzacji dla konta administratora globalnego określonego podczas instalacji Azure AD Connect. Ten błąd może być spowodowany przez nieprawidłową nazwę użytkownika lub hasło określone dla konta administratora globalnego. Ten błąd może również wystąpić, jeśli określone konto administratora globalnego jest federacyjne. Aby rozwiązać ten problem, należy ponownie uruchomić konfigurację przy użyciu prawidłowej nazwy użytkownika i hasła oraz upewnić się, że administrator jest kontem zarządzanym (tylko w chmurze lub z synchronizacją).|
| 32010| CryptoError| To zdarzenie wskazuje, że wystąpił błąd podczas generowania klucza szyfrowania hasła lub odszyfrowywania hasła przychodzącego z usługi w chmurze. Ten błąd najkorzystnie wskazuje na problem z Twoim środowiskiem. Zapoznaj się ze szczegółowymi informacjami o dzienniku zdarzeń, aby dowiedzieć się więcej o tym, jak rozwiązać ten problem. Możesz również spróbować wyłączyć i ponownie włączyć usługę zapisywania zwrotnego haseł.|
| 32011| OnBoardingServiceError| To zdarzenie oznacza, że Usługa lokalna nie może prawidłowo skomunikować się z usługą sieci Web resetowania hasła w celu zainicjowania procesu dołączania. Może to być spowodowane regułą zapory lub problemem z uzyskaniem tokenu uwierzytelniania dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że połączenia wychodzące nie są blokowane przez protokoły TCP 443 i TCP 9350-9354 lub do https://ssprdedicatedsbprodncu.servicebus.windows.net . Upewnij się również, że konto administratora usługi Azure AD, które jest używane do dołączania, nie jest federacyjne.|
| 32013| OffBoardingError| To zdarzenie oznacza, że Usługa lokalna nie może prawidłowo skomunikować się z usługą sieci Web resetowania hasła w celu zainicjowania procesu odłączania. Może to być spowodowane regułą zapory lub problemem z uzyskaniem tokenu autoryzacji dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że nie blokujesz połączeń wychodzących przez 443 lub do https://ssprdedicatedsbprodncu.servicebus.windows.net i że konto administratora Azure Active Directory, którego używasz do odłączania nie jest federacyjne.|
| 32014| ServiceBusWarning| To zdarzenie wskazuje, że musiałeś ponowić próbę nawiązania połączenia z wystąpieniem Service Bus dzierżawy. W normalnych warunkach nie powinna to stanowić problemu, ale jeśli widzisz to zdarzenie wiele razy, rozważ sprawdzenie połączenia sieciowego w celu Service Bus, szczególnie jeśli jest to połączenie o dużej opóźnieniu lub niskiej przepustowości.|
| 32015| ReportServiceHealthError| W celu monitorowania kondycji usługi zapisywania zwrotnego haseł wysyłamy dane pulsu do usługi sieci Web resetowania hasła co pięć minut. To zdarzenie wskazuje, że wystąpił błąd podczas wysyłania informacji o kondycji z powrotem do usługi sieci Web w chmurze. Te informacje o kondycji nie obejmują żadnych danych osobowych i są wyłącznie pulsem i podstawową statystyką usług, dzięki czemu możemy zapewnić informacje o stanie usługi w chmurze.|
| 33001| ADUnKnownError| To zdarzenie wskazuje, że wystąpił nieznany błąd zwrócony przez Active Directory. Aby uzyskać więcej informacji, sprawdź dziennik zdarzeń serwera Azure AD Connect pod kątem zdarzeń ze źródła ADSync.|
| 33002| ADUserNotFoundError| To zdarzenie oznacza, że użytkownik próbujący zresetować lub zmienić hasło nie został znaleziony w katalogu lokalnym. Ten błąd może wystąpić, jeśli użytkownik został usunięty z lokalizacji lokalnej, ale nie w chmurze. Ten błąd może również wystąpić, jeśli wystąpił problem z synchronizacją. Sprawdź dzienniki synchronizacji i ostatnie szczegóły uruchamiania synchronizacji, aby uzyskać więcej informacji.|
| 33003| ADMutliMatchError| Gdy żądanie resetowania lub zmiany hasła pochodzi z chmury, używamy kotwicy w chmurze określonej podczas procesu instalacji Azure AD Connect, aby określić, jak połączyć żądanie z powrotem do użytkownika w środowisku lokalnym. To zdarzenie oznacza, że znaleźliśmy dwóch użytkowników w katalogu lokalnym z tym samym atrybutem zakotwiczenia w chmurze. Sprawdź dzienniki synchronizacji i ostatnie szczegóły uruchamiania synchronizacji, aby uzyskać więcej informacji.|
| 33004| ADPermissionsError| To zdarzenie oznacza, że konto usługi Active Directory Management Agent (ADMA) nie ma odpowiednich uprawnień dla danego konta, aby ustawić nowe hasło. Upewnij się, że konto ADMA w lesie użytkownika ma uprawnienia do resetowania hasła do wszystkich obiektów w lesie. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz krok 4. Konfigurowanie odpowiednich uprawnień Active Directory. Ten błąd może również wystąpić, gdy atrybut użytkownika AdminCount jest ustawiony na 1.|
| 33005| ADUserAccountDisabled| To zdarzenie oznacza, że podjęto próbę zresetowania lub zmiany hasła dla konta, które zostało wyłączone lokalnie. Włącz konto, a następnie spróbuj ponownie wykonać operację.|
| 33006| ADUserAccountLockedOut| To zdarzenie oznacza, że podjęto próbę zresetowania lub zmiany hasła dla konta, które zostało zablokowane w środowisku lokalnym. Blokady mogą wystąpić, gdy użytkownik próbował wykonać operację zmiany lub resetowania hasła zbyt wiele razy w krótkim czasie. Odblokuj konto, a następnie spróbuj ponownie wykonać operację.|
| 33007| ADUserIncorrectPassword| To zdarzenie oznacza, że użytkownik określił nieprawidłowe bieżące hasło podczas wykonywania operacji zmiany hasła. Określ prawidłowe bieżące hasło i spróbuj ponownie.|
| 33008| ADPasswordPolicyError| To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku, historii, złożoności lub filtrowania hasła domeny. <br> <br> Jeśli użytkownik ma minimalny okres ważności hasła i ostatnio zmienił hasło w tym oknie, nie można ponownie zmienić hasła, dopóki nie osiągnie on określonego wieku w domenie. W celach testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli włączono wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *N* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ciągu ostatnich *N* razy, zobaczysz błąd w tym przypadku. W celach testowych historia hasła powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli włączono filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany zakończy się niepowodzeniem.|
| 33009| ADConfigurationError| To zdarzenie oznacza, że wystąpił problem z zapisaniem hasła z powrotem w katalogu lokalnym z powodu problemu z konfiguracją Active Directory. Sprawdź dziennik zdarzeń aplikacji maszyny Azure AD Connect, aby uzyskać więcej informacji na temat tego błędu.|

## <a name="azure-ad-forums"></a>Fora usługi Azure AD

Jeśli masz ogólne pytania dotyczące usługi Azure AD i samoobsługowego resetowania hasła, możesz poproś społeczność o pomoc na stronie pytań i odpowiedzi na [pytania firmy&Microsoft dotyczące Azure Active Directory](/answers/topics/azure-active-directory.html). Członkowie społeczności obejmują inżynierów, menedżerów produktów, MVP i innych specjalistów IT.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedzi na problem, nasze zespoły pomocy technicznej są zawsze dostępne, aby pomóc Ci w dalszej próbie.

Aby prawidłowo pomóc, prosimy o podanie możliwie największej szczegółowości podczas otwierania sprawy. Te szczegóły obejmują następujące elementy:

* **Ogólny opis błędu**: jaki jest błąd? Jak zauważono zachowanie? Jak odtworzyć błąd? Podaj tyle szczegółów, ile to możliwe.
* **Strona**: jaka strona została zadana, gdy zauważysz błąd? Dołącz adres URL, jeśli możesz i zrzut ekranu strony.
* **Kod pomocy technicznej**: jaki był kod pomocy technicznej wygenerowany podczas napotkania błędu przez użytkownika?
   * Aby znaleźć ten kod, Odtwórz błąd, a następnie wybierz link **kod pomocy technicznej** u dołu ekranu i Wyślij do niego identyfikator GUID.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Kod pomocy technicznej znajduje się w prawym dolnym rogu okna przeglądarki sieci Web.":::

  * Jeśli jesteś na stronie bez kodu pomocy technicznej u dołu, wybierz klawisz F12 i wyszukaj identyfikatory SID i CID i Wyślij te dwa wyniki do inżyniera pomocy technicznej.
* **Data, godzina i strefa czasowa**: Uwzględnij dokładną datę i godzinę *w strefie czasowej* wystąpienia błędu.
* **Identyfikator użytkownika**: użytkownik, który wystąpił błąd? Przykładem jest *user \@ contoso.com*.
   * Czy ten użytkownik jest federacyjny?
   * Czy to jest użytkownik uwierzytelniania Pass-through?
   * Czy to jest użytkownik z synchronizacją skrótu hasła?
   * Czy jest to użytkownik tylko w chmurze?
* **Licencjonowanie**: czy użytkownik ma przypisaną licencję usługi Azure AD?
* **Dziennik zdarzeń aplikacji**: Jeśli używasz funkcji zapisywania zwrotnego haseł, a błąd znajduje się w infrastrukturze lokalnej, Dołącz spakowaną kopię dziennika zdarzeń aplikacji z serwera Azure AD Connect.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SSPR, zobacz [jak to działa: Samoobsługowe resetowanie haseł w usłudze Azure AD](concept-sspr-howitworks.md) lub [sposób działania funkcji zapisywania zwrotnego resetowania hasła w usłudze Azure AD?](concept-sspr-writeback.md).