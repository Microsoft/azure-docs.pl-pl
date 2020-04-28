---
title: Dynamicznie zabronione hasła — Azure Active Directory
description: Zablokuj słabe hasła ze środowiska za pomocą dynamicznie zabronionych haseł usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79264000"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>Eliminate bad passwords in your organization (Eliminowanie nieprawidłowych haseł w organizacji)

Liderzy branżowi informują o tym, że nie używasz tego samego hasła w wielu miejscach, aby zapewnić jego złożoność i nie tak, jak "Password123". W jaki sposób organizacje mogą zagwarantować, że ich użytkownicy są zgodnie z najlepszymi wskazówkami? Jak upewnić się, że użytkownicy nie używają słabych haseł, a nawet odmian dla słabych haseł?

Pierwszym krokiem w przypadku silnych haseł jest zapewnienie użytkownikom wskazówek. Bieżące wskazówki dotyczące tego tematu firmy Microsoft można znaleźć w następującym łączu:

[Wskazówki dotyczące haseł firmy Microsoft](https://www.microsoft.com/research/publication/password-guidance)

Posiadanie dobrych wskazówek jest ważne, ale nawet w przypadku, gdy mamy pewność, że wielu użytkowników nadal kończy Wybieranie słabych haseł. Ochrona hasłem w usłudze Azure AD chroni organizację przez wykrywanie i blokowanie znanych słabych haseł oraz ich wariantów, a także opcjonalne blokowanie dodatkowych warunków, które są specyficzne dla Twojej organizacji.

Aby uzyskać więcej informacji na temat bieżących wysiłków związanych z zabezpieczeniami, zobacz [Raport analizy zabezpieczeń firmy Microsoft](https://www.microsoft.com/security/operations/security-intelligence-report).

## <a name="global-banned-password-list"></a>Lista zabronionych haseł globalnych

Zespół Azure AD Identity Protection stale analizuje dane telemetryczne zabezpieczeń usługi Azure AD szukające często używanych słabych lub złamanych haseł lub bardziej szczegółowych warunków podstawowych, które często są używane jako podstawa dla słabych haseł. Po znalezieniu takich słabych terminów są one dodawane do listy globalnie zabronione hasła. Zawartość globalnej listy haseł zabronione nie jest oparta na żadnym zewnętrznym źródle danych. Globalna lista wykluczonych haseł opiera się wyłącznie na bieżących wynikach telemetrii i analizie zabezpieczeń usługi Azure AD.

Za każdym razem, gdy nowe hasło jest zmieniane lub resetowane dla dowolnego użytkownika w dowolnej dzierżawie w usłudze Azure AD, bieżąca wersja globalnej listy haseł jest używana jako dane wejściowe klucza podczas sprawdzania siły hasła. To sprawdzenie poprawności skutkuje znacznie silniejszymi hasłami dla wszystkich klientów usługi Azure AD.

> [!NOTE]
> Cybernetycznymi — przestępcy stosują także podobne strategie w oddziałach. W związku z tym firma Microsoft nie publikuje zawartości tej listy publicznie.

## <a name="custom-banned-password-list"></a>Niestandardowa lista wykluczonych haseł

Niektóre organizacje mogą chcieć jeszcze bardziej poprawić zabezpieczenia poprzez dodanie własnych dostosowań na liście globalnie zakazanych haseł, w których firma Microsoft wywołuje niestandardową listę wykluczonych haseł. Firma Microsoft zaleca, aby warunki dodane do tej listy były głównie skoncentrowane na warunkach określonych w organizacji, takich jak:

- Nazwy marki
- Nazwy produktów
- Lokalizacje (np. oddział firmy)
- Terminy wewnętrzne specyficzne dla firmy
- Skróty, które mają określone znaczenie firmy.

Po dodaniu terminów do listy niestandardowych zakazanych haseł zostaną one połączone z warunkami z listy globalnie zakazanych haseł podczas walidacji haseł.

> [!NOTE]
> Niestandardowa lista wykluczonych haseł jest ograniczona do maksymalnie 1000 warunków. Nie jest ona przeznaczona do blokowania bardzo dużych list haseł. Aby w pełni wykorzystać zalety niestandardowej listy zakazanych haseł, firma Microsoft zaleca, aby najpierw przejrzeć i zrozumieć algorytm oceny haseł (zobacz [jak są oceniane hasła](concept-password-ban-bad.md#how-are-passwords-evaluated)) przed dodaniem nowych warunków do listy zablokowanych niestandardowych. Zrozumienie, jak działa algorytm umożliwi firmie wydajne wykrywanie i blokowanie dużej liczby słabych haseł i ich wariantów.

Na przykład: Rozważmy klienta o nazwie "contoso", który jest oparty na Londynie i który sprawia, że produkt nosi nazwę "widget". W przypadku takiego klienta należy wasteful, a także mniej bezpieczny, aby próbować blokować konkretne wahania tych warunków, takich jak:

- "Contoso! 1"
- "Contoso@London"
- "ContosoWidget"
- "! Contoso
- "LondonHQ"
- ... etcetera

Zamiast tego jest to znacznie bardziej wydajne i bezpieczne, aby blokować tylko kluczowe warunki podstawowe:

- "Contoso"
- Londyn
- Walidacj

Algorytm walidacji hasła będzie automatycznie blokować słabe warianty i kombinacje powyższych.

Niestandardowa lista wykluczonych haseł oraz możliwość włączania integracji Active Directory lokalnych jest zarządzana przy użyciu Azure Portal.

![Modyfikowanie niestandardowej listy wykluczonych haseł w obszarze metody uwierzytelniania](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>Ataki rozpylania hasła i listy haseł z naruszonymi zabezpieczeniami innych firm

Jedną z korzyści związanych z ochroną hasłem w usłudze Azure AD jest ułatwienie obrony przed atakami polegającymi na rozpylaniu hasła. Większość ataków z rozpylaczem hasła nie podejmuje próby ataków na każde pojedyncze konto więcej niż kilka razy, ponieważ takie zachowanie znacznie zwiększa prawdopodobieństwo wykrycia przez zablokowanie konta lub inne środki. Większość ataków z rozpylaczem hasła polega na przesłaniu tylko niewielkiej liczby znanych słabych haseł do poszczególnych kont w przedsiębiorstwie. Dzięki tej metodzie osoba atakująca może szybko wyszukiwać łatwo naruszone konto, jednocześnie unikając potencjalnych progów wykrywania.

Ochrona hasłem w usłudze Azure AD została zaprojektowana w celu wydajnego zablokowania wszystkich znanych słabych haseł, które mogą być używane w atakach z wykorzystaniem hasła, na podstawie rzeczywistych danych telemetrycznych zabezpieczeń widzianych przez usługę Azure AD.  Firma Microsoft wie o witrynach sieci Web innych firm, które wyliczają miliony haseł, które zostały naruszone w ramach wcześniejszych publicznie znanych naruszeń zabezpieczeń. Jest to typowy dla produktów innych firm do sprawdzania poprawności haseł, które są oparte na porównaniu z wymuszeniem rozliczania do tych milionów haseł. Firma Microsoft uważa, że takie techniki nie są najlepszym sposobem na poprawienie ogólnej siły hasła, z uwzględnieniem typowych strategii używanych przez osoby atakujące przy użyciu hasła.

> [!NOTE]
> Lista wykluczonych haseł Microsoft Global nie jest oparta na żadnym ze źródeł danych innych firm, w tym na listach z naruszonymi hasłami.

Mimo że globalna lista zablokowanych firmy Microsoft jest niewielka w porównaniu z niektórymi listami zbiorczymi innych firm, jego skutki bezpieczeństwa są wzmacniane przez fakt, że pochodzi ona z rzeczywistych danych telemetrycznych związanych z bezpieczeństwem, a także fakt, że algorytm weryfikacji hasła firmy Microsoft korzysta z inteligentnych technik dopasowywania rozmytego. Wynikiem tego jest to, że będzie efektywnie wykrywać i blokować miliony najbardziej typowych słabych haseł w przedsiębiorstwie. Klienci, którzy zdecydują się dodać warunki specyficzne dla organizacji do listy niestandardowo zakazane hasła, również korzystają z tego samego algorytmu.

Dodatkowe informacje na temat problemów z zabezpieczeniami opartymi na haśle można sprawdzić na [komputerze PA $ $Word nie ma znaczenia](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984).

## <a name="on-premises-hybrid-scenarios"></a>Lokalne scenariusze hybrydowe

Ochrona kont tylko w chmurze jest przydatna, ale wiele organizacji utrzymuje scenariusze hybrydowe, w tym Active Directory lokalnego systemu Windows Server. Korzyści z używania ochrony hasłem w usłudze Azure AD mogą być również rozszerzane do środowiska Active Directory systemu Windows Server za pośrednictwem instalacji agentów lokalnych. Teraz użytkownicy i Administratorzy, którzy zmienią lub zresetują hasła w Active Directory są zobowiązani do zgodności z tymi samymi zasadami haseł co użytkownicy tylko w chmurze.

## <a name="how-are-passwords-evaluated"></a>Jak są oceniane hasła

Za każdym razem, gdy użytkownik zmienia lub resetuje hasło, nowe hasło jest sprawdzane pod kątem siły i złożoności, sprawdzając, czy jest to połączona lista warunków z globalnych i niestandardowych list zakazanych haseł (Jeśli ta ostatnia jest skonfigurowana).

Nawet jeśli hasło użytkownika zawiera zabronione hasło, hasło może nadal zostać zaakceptowane, jeśli ogólne hasło jest wystarczająco silne. Nowo skonfigurowane hasło przejdzie przez następujące kroki, aby ocenić jego ogólną siłę, aby określić, czy powinien zostać zaakceptowany czy odrzucony.

### <a name="step-1-normalization"></a>Krok 1: Normalizacja

Nowe hasło najpierw przechodzi przez proces normalizacji. Ta technika umożliwia zmapowanie małego zestawu zabronionych haseł do znacznie większego zestawu potencjalnie słabych haseł.

Normalizacja ma dwie części.  Po pierwsze wielkie litery są zmieniane na małą literę.  Sekunda, typowe podstawienia znaków są wykonywane, na przykład:  

| Oryginalna litera  | Zastąpiona litera |
| --- | --- |
| 2,0  | wyjścia |
| jedno  | & |
| '$'  | przeglądarki |
| '\@'  | z |

Przykład: Załóżmy, że hasło "puste" jest zabronione, a użytkownik próbuje zmienić hasło na "Bl@nK". Chociaż "Bl@nk" nie jest jawnie zakazany, proces normalizacji konwertuje to hasło na "puste", które jest zakazanym hasłem.

### <a name="step-2-check-if-password-is-considered-banned"></a>Krok 2. Sprawdzanie, czy hasło jest uznawane za zabronione

#### <a name="fuzzy-matching-behavior"></a>Zachowanie dopasowywania rozmytego

Dopasowywanie rozmyte jest używane na znormalizowanym haśle, aby określić, czy zawiera hasło znalezione na listach globalnych lub niestandardowych zakazanych haseł. Proces dopasowywania zależy od edycji odległości jednego (1) porównania.  

Przykład: Załóżmy, że hasło "abcdef" jest zabronione, a użytkownik próbuje zmienić hasło na jedną z następujących czynności:

"abcdeg" *(ostatni znak został zmieniony z "f" na "g")* "abcdefg" *"(" dołączono do końca ")* " abcd " *(końcowe" f "zostało usunięte z końca)*

Każde z powyższych haseł nie jest jawnie zgodne z zakazanym hasłem "abcdef". Jednakże, ponieważ każdy przykład znajduje się w odległości od 1 niedozwolonego terminu "abcdef", wszystkie są uważane za zgodne z "abcdef".

#### <a name="substring-matching-on-specific-terms"></a>Dopasowywanie podciągów (na określonych warunkach)

Dopasowywanie podciągów jest używane na znormalizowanym haśle, aby sprawdzić, czy nazwa użytkownika i nazwisko oraz nazwę dzierżawy (należy zauważyć, że dopasowywanie nazw dzierżawców nie jest wykonywane podczas weryfikacji haseł na Active Directory kontrolerze domeny).

Przykład: Załóżmy, że mamy użytkownika, pol, który chce zresetować swoje hasło do "P0l123fb". Po normalizacji to hasło stanie się "pol123fb". Dopasowywanie podciągów oznacza, że hasło zawiera imię i nazwisko użytkownika "pol". Mimo że "P0l123fb" nie został jawnie na liście wykluczonych haseł, dopasowanie podciągu znaleziono "pol" w haśle. W związku z tym hasło byłoby odrzucone.

#### <a name="score-calculation"></a>Obliczanie wyniku

Następnym krokiem jest zidentyfikowanie wszystkich wystąpień zakazanych haseł w znormalizowanym nowym haśle użytkownika. Następnie:

1. Każde z zakazanych haseł, które znajdują się w haśle użytkownika, otrzymuje jeden punkt.
2. Każdy pozostały unikatowy znak jest przyznany jeden punkt.
3. Hasło musi zawierać co najmniej pięć (5) punktów, aby można je było zaakceptować.

Załóżmy, że firma Contoso używa ochrony hasłem usługi Azure AD i ma "contoso" na swojej liście niestandardowej. Załóżmy również, że na globalnej liście znajduje się wartość "puste".

Przykład: użytkownik zmienia swoje hasło na "C0ntos0Blank12"

Po normalizacji to hasło przyjmuje wartość "contosoblank12". Proces dopasowywania stwierdza, że to hasło zawiera dwa zabronione hasła: contoso i blank. To hasło otrzymuje wynik:

[contoso] + [puste] + [1] + [2] = 4 punkty, ponieważ to hasło znajduje się poniżej pięciu (5) punktów, zostanie odrzucone.

Przykład: użytkownik zmienia swoje hasło na "ContoS0Bl@nkf9!".

Po normalizacji to hasło zmieni się na "contosoblankf9!". Proces dopasowywania stwierdza, że to hasło zawiera dwa zabronione hasła: contoso i blank. To hasło otrzymuje wynik:

[contoso] + [puste] + [f] + [9] + [!] = 5 punktów, ponieważ to hasło ma co najmniej pięć (5) punktów, jest akceptowane.

   > [!IMPORTANT]
   > Należy pamiętać, że wykluczony algorytm hasła wraz z globalną listą może i wprowadzić zmiany w dowolnym momencie na platformie Azure na podstawie trwającej analizy zabezpieczeń i badań. W przypadku lokalnej usługi agenta DC zaktualizowane algorytmy zaczną obowiązywać dopiero po ponownym zainstalowaniu oprogramowania agenta kontrolera domeny.

## <a name="license-requirements"></a>Wymagania licencyjne

|   | Ochrona hasłem w usłudze Azure AD z listą globalnie zakazanych haseł | Ochrona hasłem w usłudze Azure AD za pomocą niestandardowej listy zablokowanych haseł|
| --- | --- | --- |
| Użytkownicy tylko w chmurze | Usługa Azure AD — warstwa Bezpłatna | Azure AD — wersja Premium P1 lub P2 |
| Użytkownicy zsynchronizowani z lokalnego systemu Windows Server Active Directory | Azure AD — wersja Premium P1 lub P2 | Azure AD — wersja Premium P1 lub P2 |

> [!NOTE]
> Lokalne systemy Windows Server Active Directory użytkownicy, którzy nie są synchronizowani do Azure Active Directory również korzystają z ochrony hasłem usługi Azure AD na podstawie istniejącej licencji dla synchronizowanych użytkowników.

Dodatkowe informacje o licencjonowaniu, w tym koszty, można znaleźć w [witrynie Azure Active Directory cenowej](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-users-see"></a>Co widzą użytkownicy

Gdy użytkownik spróbuje zresetować hasło do elementu, który mógłby zostać zabroniony, zobaczy następujący komunikat o błędzie:

Niestety, hasło zawiera słowo, frazę lub wzorzec, które ułatwiają odgadnięcie hasła. Spróbuj ponownie, używając innego hasła.

## <a name="next-steps"></a>Następne kroki

- [Skonfiguruj niestandardową listę wykluczonych haseł](howto-password-ban-bad.md)
- [Włączanie lokalnych agentów ochrony hasłem usługi Azure AD](howto-password-ban-bad-on-premises-deploy.md)
