---
title: Co to jest ryzyko? Usługa Azure AD Identity Protection
description: Objaśnienie ryzyka w Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376362"
---
# <a name="what-is-risk"></a>Co to jest ryzyko?

Wykrycia ryzyka w Azure AD Identity Protection obejmują wszelkie zidentyfikowane podejrzane akcje związane z kontami użytkowników w katalogu.

Ochrona tożsamości zapewnia organizacjom dostęp do zaawansowanych zasobów w celu szybkiego zobaczenia tych podejrzanych akcji i reagowania na nie. 

![Przegląd zabezpieczeń przedstawiający ryzykownych użytkowników i logowania](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Ochrona tożsamości generuje wykrycia ryzyka tylko wtedy, gdy są używane poprawne poświadczenia. Jeśli podczas logowania są używane nieprawidłowe poświadczenia, nie reprezentuje to ryzyka naruszenia poświadczeń.

## <a name="risk-types-and-detection"></a>Typy ryzyka i wykrywanie

Istnieją dwa typy ryzyka  **Użytkownik** i Logowanie oraz dwa typy wykrywania lub obliczania w czasie **rzeczywistym** i w trybie **offline.**

Wykrycia w czasie rzeczywistym mogą nie być wyświetlane w raportach przez od pięciu do dziesięciu minut. Wykrycia w trybie offline mogą nie być wyświetlane w raportach od dwóch do dwudziestu czterech godzin.

### <a name="user-risk"></a>Ryzyko związane z użytkownikiem

Ryzyko związane z użytkownikiem reprezentuje prawdopodobieństwo naruszenia zabezpieczeń danej tożsamości lub konta. 

Te zagrożenia są obliczane w trybie offline przy użyciu wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft, w tym badaczy zabezpieczeń, specjalistów ds. egzekwowania prawa, zespołów ds. zabezpieczeń w firmie Microsoft i innych zaufanych źródeł.

| Wykrywanie ryzyka | Opis |
| --- | --- |
| Ujawnione poświadczenia | Ten typ wykrywania ryzyka wskazuje, że prawidłowe poświadczenia użytkownika zostały ujawnione. Gdy cyberprzestępcy naruszyć prawidłowe hasła uprawnionych użytkowników, często współużytkują te poświadczenia. Udostępnianie odbywa się zazwyczaj przez publiczne publikowanie w ciemnej sieci Web, wklejanie witryn lub przez transakcję i sprzedaż poświadczeń na czarnym rynku. Gdy usługa microsoft leaks poświadczeń uzyskuje poświadczenia użytkownika z ciemnej sieci Web, wkleja witryny lub inne źródła, są one sprawdzane pod kątem bieżących prawidłowych poświadczeń użytkowników usługi Azure AD w celu znalezienia prawidłowych dopasowania. Aby uzyskać więcej informacji na temat wycieku poświadczeń, zobacz [Typowe pytania](#common-questions). |
| Analiza zagrożeń w usłudze Azure AD | Ten typ wykrywania ryzyka wskazuje nietypową aktywność użytkownika dla danego użytkownika lub jest zgodna ze znanymi wzorcami ataków opartymi na wewnętrznych i zewnętrznych źródłach analizy zagrożeń firmy Microsoft. |

### <a name="sign-in-risk"></a>Ryzyko związane z logowaniem

Ryzyko związane z logowaniem reprezentuje prawdopodobieństwo, że dane żądanie uwierzytelnienia nie zostało autoryzowane przez właściciela tożsamości. 

Te zagrożenia można obliczyć w czasie rzeczywistym lub obliczyć w trybie offline przy użyciu wewnętrznych i zewnętrznych źródeł analizy zagrożeń firmy Microsoft, w tym badaczy zabezpieczeń, specjalistów ds. wymiaru sprawiedliwości, zespołów ds. zabezpieczeń w firmie Microsoft i innych zaufanych źródeł.

| Wykrywanie ryzyka | Typ wykrywania | Opis |
| --- | --- | --- |
| Anonimowy adres IP | Przesyłanie w czasie rzeczywistym | Ten typ wykrywania ryzyka wskazuje logowania z anonimowego adresu IP (na przykład przeglądarki Tor lub anonimowej sieci VPN). Te adresy IP są zwykle używane przez aktorów, którzy chcą ukryć swoje dane telemetryczne logowania (adres IP, lokalizacja, urządzenie itp.) dla potencjalnie złośliwych intencji. |
| Nietypowa podróż | Tryb offline | Ten typ wykrywania ryzyka identyfikuje dwa logowania pochodzące z odległych geograficznie lokalizacji, gdzie co najmniej jedna z lokalizacji może być również nietypowa dla użytkownika, biorąc pod uwagę wcześniejsze zachowanie. Oprócz kilku innych czynników ten algorytm uczenia maszynowego uwzględnia czas między dwoma logowaniami i czas, który byłby czas podróży użytkownika z pierwszej lokalizacji do drugiej, co wskazuje, że inny użytkownik używa tych samych poświadczeń. <br><br> Algorytm ignoruje oczywiste "fałszywie dodatnie" czynniki wpływające na niemożliwe warunki podróży, takie jak sieci VPN i lokalizacje regularnie używane przez innych użytkowników w organizacji. System ma początkowy okres nauki najwcześniejszy niż 14 dni lub 10 logowań, podczas którego uczy się zachowania nowego użytkownika podczas logowania. |
| Adres IP połączony ze złośliwym oprogramowaniem | Tryb offline | Ten typ wykrywania ryzyka wskazuje logowania z adresów IP zainfekowanych złośliwym oprogramowaniem, o którym wiadomo, że aktywnie komunikują się z serwerem bota. To wykrywanie jest określane przez skorelowanie adresów IP urządzenia użytkownika z adresami IP, które były w kontaktach z serwerem bota, gdy serwer bota był aktywny. |
| Nieznane właściwości logowania | Przesyłanie w czasie rzeczywistym | Ten typ wykrywania ryzyka uwzględnia historię logowania z przeszłości (ip, szerokość geograficzna/długość geograficzna i asn) w celu wyszukiwania anomalii logów. System przechowuje informacje o poprzednich lokalizacjach używanych przez użytkownika i uwzględnia te "znane" lokalizacje. Wykrywanie ryzyka jest wyzwalane, gdy logowanie następuje z lokalizacji, która nie znajduje się jeszcze na liście znanych lokalizacji. Nowo utworzeni użytkownicy będą w "trybie uczenia" przez okres, w którym wykrywanie ryzyka nieznanych właściwości logowania zostanie wyłączone, podczas gdy nasze algorytmy poznają zachowanie użytkownika. Czas trwania trybu uczenia jest dynamiczny i zależy od tego, ile czasu zajmuje algorytmowi zebranie wystarczającej ilości informacji o wzorcach logowania użytkownika. Minimalny czas trwania wynosi pięć dni. Użytkownik może wrócić do trybu uczenia po długim okresie braku aktywności. System ignoruje również logowania ze znanych urządzeń i lokalizacji, które znajdują się geograficznie blisko znanej lokalizacji. <br><br> Uruchamiamy również to wykrywanie dla uwierzytelniania podstawowego (lub starszych protokołów). Ponieważ te protokoły nie mają nowoczesnych właściwości, takich jak identyfikator klienta, istnieje ograniczona telemetria w celu zmniejszenia liczby wyników fałszywie dodatnich. Zalecamy naszym klientom przejście do nowoczesnego uwierzytelniania. |
| Potwierdzone przez administratora naruszenie zabezpieczeń użytkownika | Tryb offline | To wykrywanie wskazuje, że administrator wybrał opcję "Potwierdź, że użytkownik został naruszony" w interfejsie użytkownika ryzykownych użytkowników lub przy użyciu interfejsu API ryzykownych użytkowników. Aby sprawdzić, który administrator potwierdza, że ten użytkownik został naruszony, sprawdź historię ryzyka użytkownika (za pośrednictwem interfejsu użytkownika lub interfejsu API). |
| Złośliwy adres IP | Tryb offline | To wykrywanie wskazuje logowanie się ze złośliwego adresu IP. Adres IP jest uznawany za złośliwy w oparciu o wysoką awarii z powodu nieprawidłowych poświadczeń otrzymanych z adresu IP lub innych źródeł reputacji adresów IP. |
| Podejrzane reguły obsługi skrzynki odbiorczej | Tryb offline | To wykrywanie jest wykrywane [przez Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules) To wykrywanie profiluje środowisko i wyzwala alerty, gdy podejrzane reguły, które usuwają lub przerzucają komunikaty lub foldery, są ustawione w skrzynce odbiorczej użytkownika. To wykrywanie może wskazywać, że konto użytkownika zostało naruszone, wiadomości są celowo ukryte i że skrzynka pocztowa jest używana do rozpowszechniania spamu lub złośliwego oprogramowania w organizacji. |
| Atak rozproszony na hasła | Tryb offline | Atak siłowy na hasła to atak z użyciem wielu nazw użytkowników przy użyciu typowych haseł w ujednolicony sposób siłowy w celu uzyskania nieautoryzowanego dostępu. To wykrywanie ryzyka jest wyzwalane po wykonaniu ataku rozsyłania haseł. |
| Niemożliwa podróż | Tryb offline | To wykrywanie jest wykrywane [przez Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#impossible-travel) To wykrywanie identyfikuje dwa działania użytkownika (jedną lub wiele sesji) pochodzące z odległych geograficznie lokalizacji w okresie krótszym niż czas, w którym użytkownik przebył podróż z pierwszej lokalizacji do drugiej, co wskazuje, że inny użytkownik używa tych samych poświadczeń. |
| Nowy kraj | Tryb offline | To wykrywanie jest wykrywane [przez Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country) To wykrywanie uwzględnia wcześniejsze lokalizacje aktywności w celu określenia nowych i rzadkich lokalizacji. Aparat wykrywania anomalii przechowuje informacje o poprzednich lokalizacjach używanych przez użytkowników w organizacji. |
| Aktywność z anonimowego adresu IP | Tryb offline | To wykrywanie jest wykrywane [przez Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses) To wykrywanie identyfikuje, że użytkownicy byli aktywni na podstawie adresu IP, który został zidentyfikowany jako anonimowy adres IP serwera proxy. |
| Podejrzane przesyłanie dalej wiadomości ze skrzynki odbiorczej | Tryb offline | To wykrywanie jest wykrywane [przez Microsoft Cloud App Security (MCAS).](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding) To wykrywanie wyszukuje podejrzane reguły przekazywania wiadomości e-mail, na przykład jeśli użytkownik utworzył regułę skrzynki odbiorczej, która przekazuje kopię wszystkich wiadomości e-mail na adres zewnętrzny. |

### <a name="other-risk-detections"></a>Inne wykrycia ryzyka

| Wykrywanie ryzyka | Typ wykrywania | Opis |
| --- | --- | --- |
| Wykryto dodatkowe ryzyko | W czasie rzeczywistym lub w trybie offline | To wykrywanie wskazuje, że wykryto jedno z powyższych wykryć premium. Ponieważ wykrycia premium są widoczne tylko dla Azure AD — wersja Premium P2, są one zatytułowane "wykryto dodatkowe ryzyko" dla klientów bez licencji P2 Azure AD — wersja Premium P2. |

## <a name="common-questions"></a>Często zadawane pytania

### <a name="risk-levels"></a>Poziomy ryzyka

Ochrona tożsamości dzieli ryzyko na trzy warstwy: niski, średni i wysoki. Podczas konfigurowania [niestandardowych zasad ochrony tożsamości](./concept-identity-protection-policies.md#custom-conditional-access-policy)można również skonfigurować ich wyzwalanie na **poziomie Brak** ryzyka. Brak ryzyka oznacza, że nie ma aktywnego wskazania, że tożsamość użytkownika została naruszona.

Firma Microsoft nie dostarcza szczegółowych informacji na temat sposobu obliczania ryzyka, ale mówimy, że każdy poziom zwiększa pewność, że użytkownik lub logowanie zostało naruszone. Na przykład coś takiego jak jedno wystąpienie nieznanych właściwości logowania dla użytkownika może nie być tak zagrożone jak wyciek poświadczeń dla innego użytkownika.

### <a name="password-hash-synchronization"></a>Synchronizacja skrótów haseł

Wykrywanie ryzyka, takie jak wyciek poświadczeń, wymaga obecności skrótów haseł w celu wykrycia. Aby uzyskać więcej informacji na temat synchronizacji skrótów haseł, zobacz artykuł Implementowanie synchronizacji skrótów haseł z [synchronizacją Azure AD Connect hasła.](../hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="leaked-credentials"></a>Ujawnione poświadczenia

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Gdzie firma Microsoft znajduje ujawnione poświadczenia?

Firma Microsoft znajduje ujawnione poświadczenia w różnych miejscach, w tym:

- Publiczne witryny wklejania, takie jak pastebin.com i paste.ca, w których źli aktorzy zwykle opublikują taki materiał. Ta lokalizacja to najgorętsze miejsce, w którym aktorzy po raz pierwszy zatrzymają się na polu, aby znaleźć skradzione poświadczenia.
- Ścigania.
- Inne grupy w firmie Microsoft wykonujące ciemne badania internetowe.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Dlaczego nie widzę żadnych ujawnionych poświadczeń?

Ujawnione poświadczenia są przetwarzane za każdym razem, gdy firma Microsoft znajdzie nową, publicznie dostępną partię. Ze względu na poufny charakter ujawnione poświadczenia są usuwane wkrótce po zakończeniu przetwarzania. Tylko nowe ujawnione poświadczenia znalezione po włączeniu synchronizacji skrótów haseł będą przetwarzane w dzierżawie. Sprawdzanie wcześniej znalezionych par poświadczeń nie jest wykonywane. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Od dłuższego czasu nie widać żadnych wycieków zdarzeń o ryzyku poświadczeń?

Jeśli nie są widoczne żadne wycieki zdarzeń o ryzyku poświadczeń, jest to spowodowane następującymi przyczynami:

- Nie masz włączonej funkcji PHS dla dzierżawy.
- Firma Microsoft nie znalazła żadnych ujawnionych par poświadczeń, które pasują do twoich użytkowników.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Jak często firma Microsoft przetwarza nowe poświadczenia?

Poświadczenia są przetwarzane natychmiast po ich znalezioniu, zwykle w wielu partiach dziennie.

## <a name="next-steps"></a>Następne kroki

- [Dostępne zasady ograniczania ryzyka](concept-identity-protection-policies.md)
- [Omówienie zabezpieczeń](concept-identity-protection-security-overview.md)