---
title: Ochrona hasłem w Azure Active Directory
description: Dowiedz się, jak dynamicznie zakazują słabe hasła ze środowiska przy użyciu ochrony hasłem Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4da1066166a3384ffb5f0f94599452829faed1b
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356590"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>Eliminowanie nieprawidłowych haseł przy użyciu ochrony hasłem Azure Active Directory

Wiele wskazówek dotyczących zabezpieczeń zaleca, aby nie używać tego samego hasła w wielu miejscach, aby zapewnić jego złożoność oraz uniknąć prostych haseł, takich jak *Password123*. Możesz zapewnić użytkownikom [wskazówki dotyczące wybierania haseł](https://www.microsoft.com/research/publication/password-guidance), ale słabe lub niezabezpieczone hasła często są nadal używane. Ochrona hasłem w usłudze Azure AD wykrywa i blokuje znane hasła oraz ich warianty, a także może blokować dodatkowe słabe warunki, które są specyficzne dla organizacji.

W przypadku ochrony hasłem w usłudze Azure AD domyślne globalne listy zakazanych haseł są automatycznie stosowane dla wszystkich użytkowników w dzierżawie usługi Azure AD. Aby móc obsługiwać własne potrzeby biznesowe i bezpieczeństwa, możesz definiować wpisy na liście niestandardowych haseł zabronionych. Gdy użytkownicy zmienią lub zresetują swoje hasła, te zabronione listy haseł są sprawdzane w celu wymuszenia użycia silnych haseł.

Należy używać dodatkowych funkcji, takich jak [Azure Multi-Factor Authentication](concept-mfa-howitworks.md), a nie tylko na silnych hasłach wymuszanych przez ochronę hasłem usługi Azure AD. Aby uzyskać więcej informacji na temat używania wielu warstw zabezpieczeń dla zdarzeń logowania, zobacz [PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

> [!IMPORTANT]
> Ten artykuł koncepcyjny wyjaśnia, jak działa ochrona hasłem w usłudze Azure AD. Jeśli jesteś użytkownikiem końcowym już zarejestrowanym do samoobsługowego resetowania hasła i chcesz wrócić do swojego konta, przejdź do strony [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Jeśli Twój zespół IT nie włączył możliwości resetowania własnego hasła, skontaktuj się z pomocą techniczną, aby uzyskać dodatkową pomoc.

## <a name="global-banned-password-list"></a>Lista zabronionych haseł globalnych

Zespół Azure AD Identity Protection stale analizuje dane telemetryczne zabezpieczeń usługi Azure AD szukające często używanych słabych lub złamanych haseł. W związku z tym analiza szuka podstawowych terminów, które często są używane jako podstawa dla słabych haseł. Po znalezieniu słabych terminów są one dodawane do *listy globalnie zabronione hasła*. Zawartość globalnej listy zakazanych haseł nie jest oparta na żadnym zewnętrznym źródle danych, ale na wyniki telemetrii i analizy zabezpieczeń usługi Azure AD.

W przypadku zmiany lub zresetowania hasła dla dowolnego użytkownika w dzierżawie usługi Azure AD bieżąca wersja listy globalna lista haseł jest używana do weryfikowania siły hasła. To sprawdzenie poprawności powoduje użycie silniejszych haseł dla wszystkich klientów usługi Azure AD.

Lista zakazanych haseł globalnych jest automatycznie stosowana dla wszystkich użytkowników w dzierżawie usługi Azure AD. Nie ma nic do włączenia lub skonfigurowania i nie można go wyłączyć. Ta globalna lista wykluczonych haseł jest stosowana dla użytkowników, gdy zmieniają lub zresetują własne hasło za pomocą usługi Azure AD.

> [!NOTE]
> Cybernetycznymi — przestępcy stosują także podobne strategie w oddziałach, aby identyfikować typowe słabe hasła i różnice. Aby zwiększyć bezpieczeństwo, firma Microsoft nie publikuje zawartości listy globalnie zakazanych haseł.

## <a name="custom-banned-password-list"></a>Niestandardowa lista wykluczonych haseł

Niektóre organizacje chcą poprawić bezpieczeństwo i dodać własne dostosowania na liście globalnie zakazanych haseł. Aby dodać własne wpisy, możesz użyć *Niestandardowa lista wykluczonych haseł*. Warunki dodane do listy niestandardowych haseł zabronione powinny być skoncentrowane na warunkach określonych w organizacji, takich jak następujące przykłady:

- Nazwy marki
- Nazwy produktów
- Lokalizacje, takie jak oddział firmy
- Terminy wewnętrzne specyficzne dla firmy
- Skróty, które mają określone znaczenie firmy

Gdy warunki są dodawane do listy niestandardowych zakazanych haseł, są łączone z warunkami z listy globalnie zabronione hasła. Zdarzenia zmiany lub resetowania hasła są weryfikowane pod kątem połączonego zestawu list wykluczonych haseł.

> [!NOTE]
> Niestandardowa lista wykluczonych haseł jest ograniczona do maksymalnie 1000 warunków. Nie jest ona przeznaczona do blokowania bardzo dużych list haseł.
>
> Aby w pełni wykorzystać zalety niestandardowej listy zakazanych haseł, należy najpierw zrozumieć, [jak są oceniane hasła](#how-are-passwords-evaluated) przed dodaniem warunków do listy niestandardowa zabroniony. Takie podejście umożliwia wydajne wykrywanie i blokowanie dużej liczby słabych haseł i ich wariantów.

![Modyfikowanie niestandardowej listy wykluczonych haseł w obszarze metody uwierzytelniania](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

Rozważmy klienta o nazwie *contoso*. Firma jest oparta na Londynie i sprawia, że jest to element *widget*. W przypadku tego przykładowego klienta wasteful i mniej bezpieczny, aby spróbować zablokować konkretne wahania tych warunków, takich jak następujące:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"

Zamiast tego jest to znacznie bardziej wydajne i bezpieczne, aby blokować tylko kluczowe warunki podstawowe, takie jak następujące przykłady:

- "Contoso"
- Londyn
- Walidacj

Algorytm walidacji hasła automatycznie blokuje słabe warianty i kombinacje.

Aby rozpocząć korzystanie z listy niestandardowych zakazanych haseł, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie niestandardowych zakazanych haseł](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataki rozpylania hasła i listy haseł z naruszonymi zabezpieczeniami innych firm

Ochrona hasłem w usłudze Azure AD pomaga chronić przed atakami polegającymi na rozpylaniu hasła. Większość ataków z rozpylaczem hasła nie podejmuje więcej niż kilku razy ataków na poszczególne konta. Takie zachowanie zwiększy prawdopodobieństwo wykrycia przy użyciu blokady konta lub innych metod.

Zamiast tego większość ataków rozpylaczy hasła przesyła tylko niewielką liczbę znanych słabych haseł do poszczególnych kont w przedsiębiorstwie. Ta technika umożliwia atakującemu szybkie wyszukiwanie łatwo naruszonego konta i uniknięcie progów wykrycia.

Ochrona hasłem w usłudze Azure AD efektywnie blokuje wszystkie znane słabe hasła, które mogą być używane w atakach z rozpylaczem hasła. Ta ochrona jest oparta na rzeczywistych danych telemetrycznych zabezpieczeń z usługi Azure AD w celu utworzenia listy globalnych zakazanych haseł.

Niektóre witryny sieci Web innych firm wyliczające miliony haseł, które zostały naruszone w ramach wcześniejszych publicznie znanych naruszeń zabezpieczeń. Jest to typowy dla produktów innych firm do sprawdzania poprawności haseł, które są oparte na porównaniu z wymuszeniem rozliczania do tych milionów haseł. Jednak te techniki nie są najlepszym sposobem poprawienia ogólnej siły hasła przy użyciu typowych strategii używanych przez osoby atakujące.

> [!NOTE]
> Lista zakazanych haseł globalnych nie jest oparta na żadnych źródłach danych innych firm, w tym na listach z naruszonymi hasłami.

Mimo że globalna lista zabronionych jest niewielka w porównaniu z niektórymi listami zbiorczymi innych firm, pochodzi ona z rzeczywistych danych telemetrycznych zabezpieczeń na potrzeby faktycznych ataków na rozpylacze hasła. Takie podejście usprawnia ogólne zabezpieczenia i skuteczność, a algorytm weryfikacji hasła używa również inteligentnych technik dopasowywania rozmytego. W związku z tym usługa Azure AD Password Protection skutecznie wykrywa i blokuje miliony najbardziej typowych słabych haseł z używania w przedsiębiorstwie.

## <a name="on-premises-hybrid-scenarios"></a>Lokalne scenariusze hybrydowe

Wiele organizacji ma model tożsamości hybrydowej, który obejmuje lokalne środowiska Active Directory Domain Services (AD DS). Aby zwiększyć zalety zabezpieczeń usługi Azure AD Password Protection do środowiska AD DS, można zainstalować składniki na serwerach lokalnych. Tacy agenci wymagają zdarzeń zmiany hasła w lokalnym środowisku AD DS, aby zachować zgodność z tymi samymi zasadami haseł co w usłudze Azure AD.

Aby uzyskać więcej informacji, zobacz [Wymuś ochronę hasłem w usłudze Azure AD dla AD DS](concept-password-ban-bad-on-premises.md).

## <a name="how-are-passwords-evaluated"></a>Jak są oceniane hasła

Gdy użytkownik zmieni lub zresetuje hasło, nowe hasło jest sprawdzane pod kątem siły i złożoności, sprawdzając, czy jest to połączona lista warunków z globalnych i niestandardowych list zakazanych haseł.

Nawet jeśli hasło użytkownika zawiera zabronione hasło, hasło może zostać zaakceptowane, jeśli ogólne hasło jest w inny sposób mocne. Nowo skonfigurowane hasło przechodzi przez następujące kroki w celu oceny jego ogólnej siły, aby określić, czy powinien zostać zaakceptowany czy odrzucony:

### <a name="step-1-normalization"></a>Krok 1: Normalizacja

Nowe hasło najpierw przechodzi przez proces normalizacji. Ta technika umożliwia zmapowanie małego zestawu zabronionych haseł do znacznie większego zestawu potencjalnie słabych haseł.

Normalizacja ma dwie następujące części:

* Wszystkie wielkie litery ulegają zmianie na małe litery.
* Następnie są wykonywane typowe podstawienia znaków, takie jak w poniższym przykładzie:

   | Oryginalna litera | Zastąpiona litera |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | s                  |
   | \@              | a                  |

Rozpatrzmy następujący przykład:

* Hasło "puste" jest zabronione.
* Użytkownik próbuje zmienić hasło na " Bl@nK ".
* Chociaż " Bl@nk " nie jest zabronione, proces normalizacji konwertuje to hasło na "puste".
* To hasło zostanie odrzucone.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2. Sprawdzanie, czy hasło jest uznawane za zabronione

Hasło jest następnie sprawdzane pod kątem innych zachowań dopasowania i generowany jest wynik. Ten końcowy wynik określa, czy żądanie zmiany hasła zostało zaakceptowane lub odrzucone.

#### <a name="fuzzy-matching-behavior"></a>Zachowanie dopasowywania rozmytego

Dopasowywanie rozmyte jest używane na znormalizowanym haśle, aby określić, czy zawiera hasło znalezione na listach globalnych lub niestandardowych zakazanych haseł. Proces dopasowywania zależy od edycji odległości jednego (1) porównania.

Rozpatrzmy następujący przykład:

* Hasło "abcdef" jest zabronione.
* Użytkownik próbuje zmienić hasło na jedną z następujących czynności:

   * "abcdeg" — *ostatni znak został zmieniony z "f" na "g"*
   * "abcdefg" — *"g" dołączony do końca*
   * "abcd"- *końcowe "f" zostało usunięte z końca*

* Każde z powyższych haseł nie jest jawnie zgodne z zakazanym hasłem "abcdef".

    Jednak ponieważ każdy przykład znajduje się w zakresie edycji odległości 1 od "abcdef", wszystkie są uważane za zgodne z "abcdef".
* Te hasła zostałyby odrzucone.

#### <a name="substring-matching-on-specific-terms"></a>Dopasowywanie podciągów (na określonych warunkach)

Dopasowywanie podciągów jest używane w znormalizowanym haśle, aby sprawdzić, czy nazwa użytkownika i nazwisko oraz nazwę dzierżawy zostały użyte. Dopasowywanie nazw dzierżawców nie jest wykonywane podczas sprawdzania poprawności haseł na AD DS kontrolerze domeny dla lokalnych scenariuszy hybrydowych.

> [!IMPORTANT]
> Dopasowywanie podciągów jest wymuszane tylko dla nazw i innych warunków, które mają co najmniej cztery znaki.

Rozpatrzmy następujący przykład:

* Użytkownik o nazwie Sondowający, który chce zresetować swoje hasło do "p0LL23fb".
* Po normalizacji to hasło stanie się "poll23fb".
* Dopasowywanie podciągów umożliwia znalezienie, że hasło zawiera imię i nazwisko użytkownika "Sonda".
* Chociaż "poll23fb" nie została zaprojektowana na liście wykluczonych haseł, w haśle znaleziono ciąg "Sonda".
* To hasło zostanie odrzucone.

#### <a name="score-calculation"></a>Obliczanie wyniku

Następnym krokiem jest zidentyfikowanie wszystkich wystąpień zakazanych haseł w znormalizowanym nowym haśle użytkownika. Punkty są przypisywane na podstawie następujących kryteriów:

1. Każde z zakazanych haseł, które znajdują się w haśle użytkownika, otrzymuje jeden punkt.
1. Każdy pozostały unikatowy znak jest przyznany jeden punkt.
1. Hasło musi zawierać co najmniej pięć (5) punktów do zaakceptowania.

W przypadku następnych dwóch przykładowych scenariuszy firma Contoso korzysta z ochrony hasłem usługi Azure AD i ma "contoso" na swojej niestandardowej liście zakazanych haseł. Załóżmy również, że na globalnej liście znajduje się wartość "puste".

W poniższym przykładowym scenariuszu użytkownik zmienia swoje hasło na "C0ntos0Blank12":

* Po normalizacji to hasło przyjmuje wartość "contosoblank12".
* Proces dopasowywania stwierdza, że to hasło zawiera dwa zabronione hasła: "contoso" i "Blank".
* To hasło otrzymuje następujące wyniki:

    *[contoso] + [puste] + [1] + [2] = 4 punkty*

* Ponieważ to hasło znajduje się poniżej pięciu (5) punktów, jest odrzucane.

Spójrzmy nieco inny przykład, aby pokazać, jak dodatkowa złożoność hasła może stworzyć wymaganą liczbę punktów do zaakceptowania. W poniższym przykładowym scenariuszu użytkownik zmienia swoje hasło na " ContoS0Bl@nkf9 !":

* Po normalizacji to hasło zmieni się na "contosoblankf9!".
* Proces dopasowywania stwierdza, że to hasło zawiera dwa zabronione hasła: "contoso" i "Blank".
* To hasło otrzymuje następujące wyniki:

    *[contoso] + [puste] + [f] + [9] + [!] = 5 punktów*

* Ponieważ to hasło ma co najmniej pięć (5) punktów, zostało zaakceptowane.

> [!IMPORTANT]
> Algorytm zakazanych haseł, a także globalna lista wykluczonych haseł, można zmienić w dowolnym momencie na platformie Azure na podstawie trwającej analizy zabezpieczeń i badań.
>
> W przypadku usługi lokalnego agenta kontrolera domeny w scenariuszach hybrydowych zaktualizowane algorytmy zaczną obowiązywać dopiero po uaktualnieniu oprogramowania agenta kontrolera domeny.

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik spróbuje zresetować hasło do elementu, który mógłby zostać zabroniony, zostanie wyświetlony następujący komunikat o błędzie:

*"Niestety, hasło zawiera słowo, frazę lub wzorzec, które ułatwiają odgadnięcie hasła. Spróbuj ponownie, używając innego hasła ".*

## <a name="license-requirements"></a>Wymagania licencyjne

| Użytkownicy | Ochrona hasłem w usłudze Azure AD z listą globalnie zakazanych haseł | Ochrona hasłem w usłudze Azure AD za pomocą niestandardowej listy zablokowanych haseł|
|-------------------------------------------|---------------------------|---------------------------|
| Użytkownicy tylko w chmurze                          | Usługa Azure AD — warstwa Bezpłatna             | Usługa Azure AD — wersja Premium P1 lub P2 |
| Użytkownicy zsynchronizowani z AD DS lokalnych | Usługa Azure AD — wersja Premium P1 lub P2 | Usługa Azure AD — wersja Premium P1 lub P2 |

> [!NOTE]
> Użytkownicy AD DS lokalnych, którzy nie są zsynchronizowani z usługą Azure AD, również korzystają z ochrony hasłem usługi Azure AD na podstawie istniejącej licencji dla synchronizowanych użytkowników.

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć w [witrynie Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z listy niestandardowych zakazanych haseł, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Samouczek: Konfigurowanie niestandardowych zakazanych haseł](tutorial-configure-custom-password-protection.md)

Możesz również [włączyć lokalną ochronę hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md).
