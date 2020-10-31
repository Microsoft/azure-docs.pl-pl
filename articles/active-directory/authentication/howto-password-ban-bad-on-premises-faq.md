---
title: Lokalna Ochrona hasłem usługi Azure AD — często zadawane pytania
description: Zapoznaj się z często zadawanymi pytaniami dotyczącymi ochrony hasłem w usłudze Azure AD w środowisku lokalnym Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27075af51c8deb886a060c81927f30bb70b5f638
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077663"
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