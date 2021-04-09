---
title: Lokalna Ochrona hasłem usługi Azure AD — często zadawane pytania
description: Zapoznaj się z często zadawanymi pytaniami dotyczącymi ochrony hasłem w usłudze Azure AD w środowisku lokalnym Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625132"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Ochrona hasłem w usłudze Azure AD — często zadawane pytania

Ta sekcja zawiera odpowiedzi na wiele często zadawanych pytań dotyczących ochrony hasłem w usłudze Azure AD.

## <a name="general-questions"></a>Pytania ogólne

**P: jakie wskazówki powinny być podane dla użytkowników w celu wybrania bezpiecznego hasła?**

Bieżące wskazówki dotyczące tego tematu firmy Microsoft można znaleźć w następującym łączu:

[Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance)

**P: czy lokalna Ochrona hasłem usługi Azure AD jest obsługiwana w chmurach niepublicznych?**

Lokalna Ochrona hasłem usługi Azure AD jest obsługiwana w chmurze publicznej i w chmurze Arlington. Nie ogłoszono daty dostępności w innych chmurach.

Portal usługi Azure AD umożliwia modyfikację konfiguracji specyficznej dla lokalnego "ochrony hasłem dla systemu Windows Server Active Directory" nawet w przypadku nieobsługiwanych chmur. takie zmiany zostaną utrwalone, ale w przeciwnym razie nie zostaną zastosowane. Rejestracja lokalnych agentów proxy lub lasów nie jest obsługiwana w chmurach nieobsługiwanych, a wszystkie takie próby rejestracji będą zawsze kończyć się niepowodzeniem.

**P: Jak mogę zastosować korzyści z ochrony hasłem w usłudze Azure AD do podzbioru użytkowników lokalnych?**

Nieobsługiwane. Po wdrożeniu i włączeniu ochrony hasłem usługi Azure AD nie należy odróżniać — wszyscy użytkownicy otrzymują równe korzyści z zabezpieczeń.

**P: Jaka jest różnica między zmianą hasła i ustawieniem hasła (lub resetowania)?**

Zmiana hasła jest konieczna, gdy użytkownik wybierze nowe hasło po udowodnieniu, że ma wiedzę o starym haśle. Na przykład zmiana hasła ma miejsce, gdy użytkownik loguje się do systemu Windows i zostanie wyświetlony monit o wybranie nowego hasła.

Ustawienie hasła (nazywane czasem resetowaniem hasła) polega na tym, że administrator zastępuje hasło na koncie z nowym hasłem, na przykład za pomocą narzędzia do zarządzania użytkownikami i komputerami Active Directory. Ta operacja wymaga wysokiego poziomu uprawnień (zazwyczaj administratora domeny), a osoba wykonująca operację zwykle nie ma informacji o starym haśle. Scenariusze pomocy technicznej często wykonują zbiory haseł, na przykład podczas wspomagania użytkownika, który zapomniał hasło. W przypadku tworzenia nowego konta użytkownika po raz pierwszy przy użyciu hasła będą widoczne także zdarzenia ustawiania hasła.

Zasady walidacji hasła działają tak samo, niezależnie od tego, czy są wykonywane zmiany lub ustawienia hasła. Usługa agenta DC ochrony hasłem w usłudze Azure AD rejestruje różne zdarzenia w celu powiadomienia użytkownika o tym, czy operacja zmiany lub ustawienia hasła została ukończona.  Zobacz [monitorowanie i rejestrowanie w usłudze Azure AD Password Protection](./howto-password-ban-bad-on-premises-monitor.md).

**P: czy ochrona hasłem usługi Azure AD sprawdza istniejące hasła po zainstalowaniu?**

Nie — Ochrona hasłem w usłudze Azure AD może wymuszać tylko zasady haseł dla haseł ze zwykłym tekstem podczas operacji zmiany lub ustawienia hasła. Po zaakceptowaniu hasła przez Active Directory są zachowywane wyłącznie wartości skrótu specyficzne dla protokołu uwierzytelniania. Hasło w postaci zwykłego tekstu nigdy nie jest utrwalane, dlatego Ochrona hasłem w usłudze Azure AD nie może zweryfikować istniejących haseł.

Po początkowym wdrożeniu ochrony hasłem usługi Azure AD wszyscy użytkownicy i konta ostatecznie rozpoczną korzystanie z hasła zweryfikowanego przez ochronę hasłem usługi Azure AD, ponieważ istniejące hasła wygasają zwykle w czasie. W razie potrzeby ten proces może być przyspieszony przez jednorazowe ręczne wygaśnięcie haseł kont użytkowników.

Konta skonfigurowane z opcją "hasło nigdy nie wygasa" nigdy nie będą wymuszane do zmiany hasła, chyba że zostanie wykonane ręczne wygaśnięcie.

**P: Dlaczego są zduplikowane zdarzenia odrzucania hasła zarejestrowane podczas próby ustawienia słabego hasła przy użyciu przystawki Zarządzanie użytkownikami i komputerami Active Directory?**

Przystawka Zarządzanie użytkownikami i komputerami Active Directory najpierw podejmie próbę ustawienia nowego hasła przy użyciu protokołu Kerberos. Gdy wystąpi awaria, przystawka wykona kolejną próbę ustawienia hasła przy użyciu starszego protokołu (SAM protokół RPC) (używane protokoły nie są ważne). Jeśli nowe hasło jest uznawane za słabe przez ochronę hasłem usługi Azure AD, to zachowanie przystawce spowoduje zarejestrowanie dwóch zestawów zdarzeń odrzucenia resetowania hasła.

**P: Dlaczego są rejestrowane zdarzenia walidacji hasła ochrony haseł usługi Azure AD za pomocą pustej nazwy użytkownika?**

Active Directory obsługuje możliwość przetestowania hasła w celu sprawdzenia, czy przeszło bieżące wymagania dotyczące złożoności hasła domeny, na przykład za pomocą interfejsu API [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Po sprawdzeniu poprawności hasła w ten sposób testowanie obejmuje również sprawdzanie poprawności przez produkty oparte na bibliotece Password-Filter-dll, takie jak ochrona hasłem usługi Azure AD, ale nazwy użytkowników przekazane do danej biblioteki DLL filtru haseł będą puste. W tym scenariuszu Ochrona hasłem usługi Azure AD będzie nadal weryfikować hasło przy użyciu obecnie obowiązujących zasad haseł i wystawia komunikat dziennika zdarzeń w celu przechwycenia wyniku, ale komunikat dziennika zdarzeń będzie miał puste pola nazwy użytkownika.

**P: czy jest obsługiwana, aby zainstalować ochronę hasłem usługi Azure AD obok innych produktów opartych na filtrze haseł?**

Tak. Obsługa wielu zarejestrowanych bibliotek DLL filtru haseł jest podstawową funkcją systemu Windows i nie jest specyficzna dla ochrony hasłem usługi Azure AD. Przed zaakceptowaniem hasła wszystkie zarejestrowane biblioteki DLL filtru haseł muszą być zgodne.

**P: jak można wdrożyć i skonfigurować ochronę hasłem usługi Azure AD w środowisku Active Directory bez korzystania z platformy Azure?**

Nieobsługiwane. Ochrona hasłem w usłudze Azure AD to funkcja platformy Azure, która obsługuje rozszerzanie do lokalnego środowiska Active Directory.

**P: Jak mogę zmodyfikować zawartość zasad na poziomie Active Directory?**

Nieobsługiwane. Zasady można administrować tylko za pomocą portalu usługi Azure AD. Zobacz również poprzednie pytanie.

**P: Dlaczego Usługa DFSR jest wymagana na potrzeby replikacji folderu SYSVOL?**

Usługa FRS (technologia poprzednika do DFSR) ma wiele znanych problemów i jest całkowicie nieobsługiwana w nowszych wersjach systemu Windows Server Active Directory. W domenach skonfigurowanych przez usługę FRS nie będzie przeprowadzane testowanie ochrony hasłem w usłudze Azure AD.

Aby uzyskać więcej informacji, zobacz następujące artykuły:

[Przypadek migrowania replikacji folderu SYSVOL do usługi DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr)

[Koniec to Nigh dla usługi FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

Jeśli domena nie korzysta już z usługi DFSR, przed zainstalowaniem ochrony przy użyciu usługi Azure AD Password należy przeprowadzić migrację do niej. Aby uzyskać więcej informacji, zobacz następujący link:

[Przewodnik migracji replikacji folderu SYSVOL: usługa FRS do Replikacja systemu plików DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Oprogramowanie agenta DC ochrony hasłem w usłudze Azure AD będzie obecnie instalowane na kontrolerach domeny w domenach, które nadal używają usługi FRS do replikacji folderu SYSVOL, ale oprogramowanie nie będzie działało prawidłowo w tym środowisku. Dodatkowe negatywne efekty uboczne obejmują pojedyncze pliki, których replikacja nie powiodła się, a procedury przywracania folderu SYSVOL pojawiają się po awarii, ale w trybie dyskretnym nie można zreplikować wszystkich plików. Należy przeprowadzić migrację domeny tak szybko, jak to możliwe, zarówno w przypadku związanych z nią korzyści, jak i do odblokowania wdrożenia ochrony hasłem usługi Azure AD. Przyszłe wersje oprogramowania zostaną automatycznie wyłączone po uruchomieniu w domenie, która nadal korzysta z usługi FRS.

**P: jaka ilość miejsca na dysku jest wymagana przez funkcję w udziale Sysvol domeny?**

Precyzyjne użycie miejsca zależy od tego, czy jest to zależne od czynników, takich jak liczba i długość zabronionych tokenów na liście globalnie zabronionych firmy Microsoft i na liście niestandardowej dla dzierżawy oraz narzuty szyfrowania. Zawartość tych list może się zwiększać w przyszłości. Z tego względu uzasadnione jest, że funkcja będzie potrzebować co najmniej pięciu (5) megabajtów miejsca w udziale Sysvol domeny.

**P: Dlaczego jest wymagane ponowne uruchomienie w celu zainstalowania lub uaktualnienia oprogramowania agenta kontrolera domeny?**

Ten wymóg jest spowodowany przez podstawowe zachowanie systemu Windows.

**P: czy istnieje możliwość skonfigurowania agenta DC do korzystania z określonego serwera proxy?**

Nie. Ponieważ serwer proxy jest bezstanowy, nie ma znaczenia, który z nich jest używany.

**P: Czy można wdrożyć usługę proxy ochrony hasłem usługi Azure AD obok innych usług, takich jak Azure AD Connect?**

Tak. Usługa serwera proxy ochrony hasłem usługi Azure AD i Azure AD Connect nigdy nie powinna powodować konfliktu bezpośrednio ze sobą.

Niestety, znaleziono niezgodność między wersją usługi Microsoft Azure AD Connect Agent Aktualizator, która jest instalowana przez oprogramowanie serwera proxy ochrony hasłem w usłudze Azure AD i wersję usługi zainstalowanej przez oprogramowanie [serwer proxy aplikacji usługi Azure Active Directory](../manage-apps/application-proxy.md) . Niezgodność może spowodować, że usługa Aktualizator agenta nie będzie mogła skontaktować się z platformą Azure w celu uzyskania aktualizacji oprogramowania. Nie zaleca się instalowania serwera proxy ochrony hasłem usługi Azure AD i serwer proxy aplikacji usługi Azure Active Directory na tym samym komputerze.

**P: w jakiej kolejności mają być zainstalowane i zarejestrowane agenci i serwery proxy kontrolera domeny?**

Obsługiwane jest dowolna kolejność instalacji agenta proxy, instalacji agenta kontrolera domeny, rejestracji lasów oraz rejestracji serwera proxy.

**P: czy należy wiedzieć o osiągnięciu wydajności na kontrolerach domeny od wdrożenia tej funkcji?**

Usługa agenta DC ochrony hasłem w usłudze Azure AD nie powinna znacząco wpływać na wydajność kontrolera domeny w istniejącym wdrożeniu Active Directory w dobrej kondycji.

W przypadku większości Active Directory operacje zmiany hasła są niewielką częścią ogólnego obciążenia na dowolnym kontrolerze domeny. Załóżmy na przykład, że Active Directory domeny z kontami użytkowników 10000 i zasadami MaxPasswordAge ustawionymi na 30 dni. Średnio w tej domenie zobaczysz w każdym dniu 10000/30 = ~ 333 operacji zmiany hasła, co stanowi niewielką liczbę operacji dla nawet jednego kontrolera domeny. Rozważmy potencjalny scenariusz najgorszego przypadku: Załóżmy, że te ~ 333 zmiany hasła na jednym kontrolerze domeny zostały wykonane w ciągu jednej godziny. Na przykład ten scenariusz może wystąpić, gdy wielu pracowników ma działać w poniedziałek rano. Nawet w takim przypadku nadal trwają około 333/60 minut = sześć zmian haseł na minutę, co nie jest znaczącym obciążeniem.

Jeśli jednak bieżące kontrolery domeny są już uruchomione na ograniczonych poziomach wydajności (na przykład maxed się w odniesieniu do procesora CPU, miejsca na dysku, we/wy dysku itp.), zaleca się dodanie kolejnych kontrolerów domeny lub rozwinięcie dostępnego miejsca na dysku przed wdrożeniem tej funkcji. Należy również zapoznać się z powyższymi pytaniami dotyczącymi użycia miejsca na dysku SYSVOL.

**P: Chcę przetestować ochronę hasłem usługi Azure AD tylko w kilku domenach w domenie. Czy istnieje możliwość wymuszenia zmiany hasła użytkownika na korzystanie z tych konkretnych kontrolerów domeny?**

Nie. System operacyjny klienta systemu Windows kontroluje, który kontroler domeny jest używany, gdy użytkownik zmienia swoje hasło. Kontroler domeny jest wybierany w oparciu o takie czynniki jak Active Directory przypisań lokacji i podsieci, konfiguracji sieci specyficznej dla środowiska itp. Ochrona hasłem w usłudze Azure AD nie kontroluje tych czynników i nie ma wpływu na kontroler domeny wybrany do zmiany hasła użytkownika.

Jednym ze sposobów na częściowe osiągnięcie tego celu jest wdrożenie ochrony hasłem usługi Azure AD na wszystkich kontrolerach domeny w danej lokacji Active Directory. Takie podejście zapewni rozsądne pokrycie dla klientów systemu Windows przypisanych do tej lokacji, w związku z tym również dla użytkowników logujących się na tych klientach i zmieniających ich hasła.

**P: Jeśli zainstaluję usługę agenta DC ochrony hasłem usługi Azure AD tylko na podstawowym kontrolerze domeny (PDC), wszystkie inne kontrolery domeny w domenie będą również chronione?**

Nie. Gdy hasło użytkownika jest zmieniane na danym kontrolerze domeny innego niż kontroler PDC, hasło w postaci zwykłego tekstu nigdy nie jest wysyłane do kontrolera PDC (ten pomysł jest typowym niepercepcjem). Po zaakceptowaniu nowego hasła dla danego kontrolera domeny kontroler ten używa tego hasła do utworzenia różnych wartości skrótu właściwych dla protokołu uwierzytelniania danego hasła, a następnie utrwala te skróty w katalogu. Hasło w postaci zwykłego tekstu nie jest utrwalone. Zaktualizowane skróty są następnie replikowane do podstawowego kontrolera domeny. W niektórych przypadkach hasła użytkowników mogą być zmieniane bezpośrednio na podstawowym kontrolerze domeny, w zależności od różnych czynników, takich jak topologia sieci i Active Directory projektowanie lokacji. (Zobacz poprzednie pytanie).

Podsumowując, wdrożenie usługi agenta DC ochrony hasła usługi Azure AD na podstawowym kontrolerze domeny jest wymagane do uzyskania dostępu do 100% tej funkcji w całej domenie. Wdrożenie funkcji na podstawowym kontrolerze PDC nie zapewnia korzyści związanych z bezpieczeństwem ochrony hasłem usługi Azure AD dla innych kontrolerów domeny w domenie.

**P: Dlaczego niestandardowa inteligentna blokada nie działa nawet po zainstalowaniu agentów w środowisku lokalnym Active Directory?**

Niestandardowa blokada inteligentna jest obsługiwana tylko w usłudze Azure AD. Zmiany niestandardowych ustawień inteligentnego blokowania w portalu usługi Azure AD nie mają wpływu na lokalne środowisko Active Directory, nawet z zainstalowanymi agentami.

**P: czy dla ochrony haseł usługi Azure AD jest dostępny System Center Operations Manager pakiet administracyjny?**

Nie.

**P: Dlaczego usługa Azure AD nadal odrzuca słabe hasła, mimo że skonfigurowano zasady tak, aby były w trybie inspekcji?**

Tryb inspekcji jest obsługiwany tylko w środowisku lokalnym Active Directory. Usługa Azure AD jest niejawnie zawsze w trybie "Wymuś" podczas obliczania haseł.

**P: w przypadku odrzucenia hasła przez ochronę hasłem usługi Azure AD użytkownicy widzą tradycyjny komunikat o błędzie systemu Windows. Czy można dostosować ten komunikat o błędzie, aby użytkownicy wiedzieli, co naprawdę się stało?**

Nie. Komunikat o błędzie wyświetlany przez użytkowników w przypadku odrzucenia hasła przez kontroler domeny jest kontrolowany przez komputer kliencki, a nie przez kontroler domeny. To zachowanie występuje niezależnie od tego, czy hasło jest odrzucane przez domyślne zasady haseł Active Directory, czy przez rozwiązanie oparte na filtrze hasła, takie jak ochrona hasłem usługi Azure AD.

## <a name="password-testing-procedures"></a>Procedury testowania hasła

Aby sprawdzić poprawność działania oprogramowania i lepiej zrozumieć [algorytm oceny haseł](concept-password-ban-bad.md#how-are-passwords-evaluated), można wykonać podstawowe testy różnych haseł. W tej sekcji przedstawiono metodę testowania, która została zaprojektowana w celu wygenerowania powtarzalnych wyników.

Dlaczego należy wykonać te kroki? Istnieje kilka czynników, które utrudniają kontrolowane, powtarzalne testowanie haseł w środowisku lokalnym Active Directory:

* Zasady haseł są konfigurowane i utrwalane na platformie Azure, a kopie tych zasad są synchronizowane okresowo przez agentów lokalnego kontrolera domeny przy użyciu mechanizmu sondowania. Opóźnienie związane z tym cyklem sondowania może spowodować pomyłkę. Na przykład, jeśli skonfigurujesz zasady na platformie Azure, ale zapomnisz zsynchronizować ją z agentem DC, testy mogą nie dać oczekiwanych wyników. Interwał sondowania jest obecnie stałe raz na godzinę, ale oczekiwanie na godzinę między zmianami zasad jest nieidealny dla scenariusza testów interaktywnych.
* Po zsynchronizowaniu nowych zasad haseł z kontrolerem domeny, podczas replikowania danych do innych kontrolerów domeny nastąpi więcej opóźnień. Te opóźnienia mogą spowodować nieoczekiwane wyniki w przypadku przetestowania zmiany hasła na kontrolerze domeny, który nie otrzymał jeszcze najnowszej wersji zasad.
* Testowanie zmian haseł za pośrednictwem interfejsu użytkownika utrudnia ich wyniki. Na przykład można łatwo nieprawidłowo wprowadzić nieprawidłowe hasło do interfejsu użytkownika, szczególnie w przypadku większości interfejsów użytkownika haseł Ukryj dane wejściowe użytkownika (na przykład, takie jak Windows Ctrl-Alt-Delete-> Change Password UI).
* Nie jest możliwe ścisłe sterowanie kontrolerem domeny używanym do testowania zmian haseł z klientów przyłączonych do domeny. System operacyjny klienta systemu Windows wybiera kontroler domeny w oparciu o takie czynniki jak Active Directory przypisania lokacji i podsieci, konfiguracja sieci specyficzną dla środowiska itp.

Aby uniknąć tych problemów, poniższe kroki są oparte na teście wiersza polecenia resetowania hasła podczas logowania do kontrolera domeny.

> [!WARNING]
> Te procedury powinny być używane tylko w środowisku testowym, ponieważ wszystkie zmiany i resetowanie hasła przychodzącego zostaną zaakceptowane bez sprawdzania poprawności, gdy usługa agenta kontrolera domeny zostanie zatrzymana, a także w celu uniknięcia zwiększonego ryzyka związanego z logowaniem się do kontrolera domeny.

W poniższych krokach przyjęto założenie, że na co najmniej jednym kontrolerze domeny zainstalowano agenta DC, zainstalowano co najmniej jeden serwer proxy i zarejestrowano zarówno serwer proxy, jak i Las.

1. Zaloguj się do kontrolera domeny przy użyciu poświadczeń administratora domeny (lub innych poświadczeń, które mają wystarczające uprawnienia do tworzenia kont użytkowników testowych i resetowania haseł), które mają zainstalowane oprogramowanie agenta kontrolera domeny i zostały uruchomione ponownie.
1. Otwórz Podgląd zdarzeń i przejdź do [dziennika zdarzeń administratora agenta kontrolera domeny](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log).
1. Otwórz okno wiersza polecenia z podwyższonym poziomem uprawnień.
1. Tworzenie konta testowego na potrzeby testowania hasła

   Istnieje wiele sposobów tworzenia konta użytkownika, ale w tym miejscu jest oferowana opcja wiersza polecenia w celu ułatwienia podczas powtarzania cykli testowania:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   W celach dyskusji poniżej założono, że utworzono konto testowe o nazwie "ContosoUser", na przykład:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Otwórz przeglądarkę sieci Web (może być konieczne użycie oddzielnego urządzenia zamiast kontrolera domeny), zalogować się do [Azure Portal](https://portal.azure.com)i przejdź do Azure Active Directory > zabezpieczenia > metod uwierzytelniania > ochrony hasłem.
1. Zmodyfikuj zasady ochrony haseł usługi Azure AD zgodnie z potrzebami w testowaniu, które chcesz wykonać.  Można na przykład skonfigurować tryb wymuszony lub inspekcji lub zmodyfikować listę zabronionych terminów na liście niestandardowo zakazanych haseł.
1. Zsynchronizuj nowe zasady, zatrzymując i uruchamiając ponownie usługę agenta kontrolera domeny.

   Ten krok można wykonać na różne sposoby. Jednym ze sposobów jest użycie konsoli administracyjnej zarządzania usługami przez kliknięcie prawym przyciskiem myszy usługi agenta DC ochrony hasłem w usłudze Azure AD i wybranie opcji "Uruchom ponownie". Innym sposobem może być wykonanie z okna wiersza polecenia, takiego jak:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Sprawdź Podgląd zdarzeń, aby sprawdzić, czy zostały pobrane nowe zasady.

   Za każdym razem, gdy usługa agenta kontrolera domeny jest zatrzymana i uruchomiona, powinny być widoczne zdarzenia 2 30006, które wydano w zamknięciu. Pierwsze zdarzenie 30006 będzie odzwierciedlać zasady, które były przechowywane w pamięci podręcznej na dysku w udziale Sysvol. Drugie zdarzenie 30006 (jeśli istnieje) powinno mieć zaktualizowaną datę zasad dzierżawy, a jeśli tak, będzie odzwierciedlało zasady pobrane z platformy Azure. Wartość daty zasad dzierżawy jest obecnie zakodowana, aby wyświetlać przybliżoną sygnaturę czasową, którą zasady zostały pobrane z platformy Azure.
   
   Jeśli drugie zdarzenie 30006 nie zostanie wyświetlone, należy rozwiązać problem przed kontynuowaniem.
   
   Zdarzenia 30006 będą wyglądać podobnie do tego przykładu:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Na przykład zmiana między wymuszaniem a trybem inspekcji spowoduje zmodyfikowanie flagi AuditOnly (powyższe zasady z AuditOnly = 0 są w trybie wymuszonym); zmiany na liście niestandardowych haseł zabronionych nie są bezpośrednio odzwierciedlone w powyższym zdarzeniu 30006 (i nie są rejestrowane w innym miejscu ze względów bezpieczeństwa). Pomyślnie powiodło się pobranie zasad z platformy Azure po takiej zmianie spowoduje to również zmodyfikowaną niestandardową listę wykluczonych haseł.

1. Uruchom test, próbując zresetować nowe hasło na koncie użytkownika testowego.

   Ten krok można wykonać za pomocą okna wiersza polecenia, takiego jak:

   ```text
   net.exe user ContosoUser <password>
   ```

   Po uruchomieniu polecenia możesz uzyskać więcej informacji na temat wyniku polecenia, przeglądając Podgląd zdarzeń. Zdarzenia dotyczące wyniku weryfikacji hasła są udokumentowane w temacie dotyczącym [dziennika zdarzeń administratora agenta kontrolera domeny](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) ; te zdarzenia będą używane do walidacji wyniku testu, a nie danych wyjściowych z poleceń net.exe.

   Wypróbujmy przykład: podjęto próbę ustawienia hasła, które jest zabronione przez globalną listę firmy Microsoft (należy zauważyć, że lista [nie jest udokumentowana](concept-password-ban-bad.md#global-banned-password-list) , ale możemy przetestować ją w tym miejscu pod znanym niedozwolonym terminem). W tym przykładzie przyjęto założenie, że zasady zostały skonfigurowane tak, aby były w trybie wymuszonym i dodały zerowe warunki do listy niestandardowo zakazanych haseł.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   Zgodnie z dokumentacją, ponieważ nasze testy były operacją resetowania hasła, należy zobaczyć zdarzenie 10017 i 30005 dla użytkownika ContosoUser.

   Zdarzenie 10017 powinno wyglądać podobnie do tego przykładu:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Zdarzenie 30005 powinno wyglądać podobnie do tego przykładu:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   To było zabawne, spróbujmy użyć innego przykładu. Tym razem Podejmiemy próbę ustawienia hasła, które jest zabronione przez niestandardową listę zabronionych, gdy zasady są w trybie inspekcji. W tym przykładzie przyjęto założenie, że wykonano następujące czynności: skonfigurowano zasady w trybie inspekcji, dodano termin "lachrymose" do listy niestandardowych zakazanych haseł i zsynchronizowano wynikowe nowe zasady z kontrolerem domeny przez wypełnianie usługi agenta DC zgodnie z powyższym opisem.

   OK, ustaw odmianę zabronionego hasła:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Należy pamiętać, że ta godzina zakończyła się pomyślnie, ponieważ zasady są w trybie inspekcji. Powinno zostać wyświetlone zdarzenie 10025 i 30007 dla użytkownika ContosoUser.

   Zdarzenie 10025 powinno wyglądać podobnie do tego przykładu:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   Zdarzenie 30007 powinno wyglądać podobnie do tego przykładu:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Kontynuuj testowanie różnych wybranych haseł i sprawdzaj wyniki w Podglądzie zdarzeń, korzystając z procedur opisanych w poprzednich krokach. Jeśli musisz zmienić zasady w Azure Portal, nie zapomnij zsynchronizować nowych zasad z agentem DC zgodnie z wcześniejszym opisem.

Uwzględniono procedury umożliwiające sterowanie zachowaniem weryfikacji hasła w usłudze Azure AD Password Protection. Resetowanie haseł użytkowników z wiersza polecenia bezpośrednio na kontrolerze domeny może wydawać się nietypowymi sposobami wykonywania takich testów, ale zgodnie z wcześniejszym opisem można generować powtarzalne wyniki. Podczas testowania różnych haseł należy zachować [algorytm oceny haseł](concept-password-ban-bad.md#how-are-passwords-evaluated) , ponieważ może to pomóc w wyjaśnieniu nieoczekiwanych wyników.

> [!WARNING]
> Gdy wszystkie testy zostaną zakończone, nie zapomnij usunąć wszystkich kont użytkowników utworzonych na potrzeby testowania.

## <a name="additional-content"></a>Dodatkowa zawartość

Następujące linki nie są częścią podstawowej dokumentacji ochrony hasłem usługi Azure AD, ale mogą być użytecznym źródłem dodatkowych informacji na temat tej funkcji.

[Ochrona hasłem w usłudze Azure AD jest teraz ogólnie dostępna!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Przewodnik dotyczący ochrony przed wyłudzaniem wiadomości e-mail — część 15: implementacja usługi ochrony hasłem Microsoft Azure AD (w przypadku miejsca lokalnego!)](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Usługa Azure AD Password Protection i inteligentna blokada są teraz dostępne w publicznej wersji zapoznawczej.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529)

## <a name="microsoft-premierunified-support-training-available"></a>Dostępne szkolenia Microsoft Premier\Unified support

Jeśli chcesz dowiedzieć się więcej o ochronie haseł usługi Azure AD i wdrażaniu jej w środowisku, możesz skorzystać z usługi Microsoft proaktywnie dostępnej dla tych klientów z umową Premier lub Unified support. Usługa jest nazywana Azure Active Directory: Ochrona hasłem. Aby uzyskać więcej informacji, skontaktuj się z kierownikiem ds. klientów.

## <a name="next-steps"></a>Następne kroki

Jeśli masz lokalne pytanie ochrony hasła usługi Azure AD, na które nie udzielono odpowiedzi, Prześlij element opinii poniżej — Dziękujemy!

[Wdrażanie ochrony haseł w usłudze Azure AD](howto-password-ban-bad-on-premises-deploy.md)