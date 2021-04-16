---
title: Ciągła ocena dostępu w usłudze Azure AD
description: Szybsze reagowanie na zmiany stanu użytkownika dzięki ciągłej ocenie dostępu w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74009759bb9ca2a0516148fc1387b150b67452ab
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387908"
---
# <a name="continuous-access-evaluation"></a>Ciągła weryfikacja dostępu

Wygaśnięcie i odświeżanie tokenu to standardowy mechanizm w branży. Gdy aplikacja kliency, na przykład Outlook, łączy się z usługą, np. Exchange Online, żądania interfejsu API są autoryzowane przy użyciu tokenów dostępu OAuth 2.0. Domyślnie te tokeny dostępu są ważne przez jedną godzinę, a po wygaśnięciu klient jest przekierowywany z powrotem do usługi Azure AD w celu ich odświeżenia. Ten okres odświeżania daje możliwość ponownej wyceny zasad dostępu użytkowników. Na przykład: możemy zdecydować, aby nie odświeżać tokenu z powodu zasad dostępu warunkowego lub dlatego, że użytkownik został wyłączony w katalogu. 

Klienci wyrazili obawy dotyczące opóźnienia między zmianami warunków dla użytkownika, na przykład lokalizacją sieciową lub kradzieżą poświadczeń, a wymuszania zasad związanych z tymi zmianami. Eksperymentujemy z podejściem "obiektu tępego" w przypadku skróconych okresów istnienia tokenów, ale okazało się, że mogą one obniżyć jakość obsługi i niezawodność użytkowników bez eliminowania ryzyka.

Terminowe reagowanie na naruszenia zasad lub problemy z zabezpieczeniami naprawdę wymaga "konwersacji" między wystawcą tokenu, np. usługą Azure AD, a jednostką jednostki zależnej, np. usługą Exchange Online. Ta dwukierunkowa konwersacja daje nam dwie ważne możliwości. Jednostki zależne mogą zauważyć zmiany, takie jak klient z nowej lokalizacji, i poinformować wystawcę tokenu. Daje również wystawcy tokenu sposób na nakłonienie jednostki zależnej, aby przestała uwzględniać tokeny dla danego użytkownika z powodu naruszenia zabezpieczeń konta, wyłączenia lub innych problemów. Mechanizmem tej konwersacji jest ciągła ocena dostępu (CAE). Celem jest, aby odpowiedź była prawie w czasie rzeczywistym, ale w niektórych przypadkach można zaobserwować opóźnienie do 15 minut z powodu czasu propagacji zdarzenia.

Początkowa implementacja ciągłej oceny dostępu koncentruje się na programach Exchange, Teams i SharePoint Online.

Aby przygotować aplikacje do korzystania z funkcji CAE, zobacz How to use Ciągła weryfikacja dostępu enabled APIs in your applications (Jak używać interfejsów API z włączoną [obsługą interfejsów API w aplikacjach).](../develop/app-resilience-continuous-access-evaluation.md)

### <a name="key-benefits"></a>Najważniejsze korzyści

- Zakończenie działania użytkownika lub zmiana/zresetowanie hasła: odwołanie sesji użytkownika zostanie wymuszone niemal w czasie rzeczywistym.
- Zmiana lokalizacji sieciowej: zasady lokalizacji dostępu warunkowego będą wymuszane niemal w czasie rzeczywistym.
- Eksportowanie tokenu na maszynę spoza zaufanej sieci można uniemożliwić przy użyciu zasad lokalizacji dostępu warunkowego.

## <a name="scenarios"></a>Scenariusze 

Istnieją dwa scenariusze, które są procesem ciągłej oceny dostępu: ocena krytycznych zdarzeń i ocena zasad dostępu warunkowego.

### <a name="critical-event-evaluation"></a>Ocena zdarzeń krytycznych

Ciągła ocena dostępu jest implementowana przez umożliwienie usługom, np. Exchange Online, SharePoint Online i Teams, subskrybowania zdarzeń krytycznych w usłudze Azure AD, aby zdarzenia te można było oceniać i wymuszać niemal w czasie rzeczywistym. Ocena zdarzeń krytycznych nie zależy od zasad dostępu warunkowego, dlatego jest dostępna w dowolnej dzierżawie. Obecnie oceniane są następujące zdarzenia:

- Konto użytkownika zostało usunięte lub wyłączone
- Hasło użytkownika zostało zmienione lub zresetowane
- Uwierzytelnianie wieloskładnikowe jest włączone dla użytkownika
- Administrator jawnie odwołuje wszystkie tokeny odświeżania dla użytkownika
- Wysokie ryzyko związane z użytkownikiem wykryte przez Azure AD Identity Protection

Ten proces umożliwia scenariusz, w którym użytkownicy utracą dostęp do organizacyjnych plików usługi SharePoint Online, poczty e-mail, kalendarza lub zadań oraz aplikacji Teams z aplikacji klienckich usługi Microsoft 365 w ciągu kilku minut po jednym z tych zdarzeń krytycznych. 

> [!NOTE] 
> Usługi Teams i SharePoint Online nie obsługują jeszcze zdarzeń o ryzyku użytkowników.

### <a name="conditional-access-policy-evaluation-preview"></a>Ocena zasad dostępu warunkowego (wersja zapoznawcza)

Program Exchange i SharePoint mogą synchronizować kluczowe zasady dostępu warunkowego, aby można je było ocenić w ramach samej usługi.

Ten proces umożliwia scenariusz, w którym użytkownicy utracą dostęp do plików organizacji, poczty e-mail, kalendarza lub zadań z aplikacji klienckich usługi Microsoft 365 Lub SharePoint Online natychmiast po zmianie lokalizacji sieciowej.

> [!NOTE]
> Nie wszystkie kombinacje aplikacji i dostawcy zasobów są obsługiwane. Zobacz tabelę poniżej. Pakiet Office odnosi się do programów Word, Excel i PowerPoint.

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Exchange Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

| | Aplikacje internetowe pakietu Office | Aplikacje Win32 pakietu Office | Pakiet Office dla systemu iOS | Pakiet Office dla systemu Android | Pakiet Office dla komputerów Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Exchange Online** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

| | OneDrive — Internet | OneDrive Win32 | OneDrive dla systemu iOS | OneDrive Android | OneDrive Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

### <a name="client-side-claim-challenge"></a>Żądanie oświadczenia po stronie klienta

Przed ciągłą oceną dostępu klienci zawsze próbowali odtworzyć token dostępu ze swojej pamięci podręcznej, o ile nie wygasł. Wraz z cae wprowadzamy nowy przypadek, w którym dostawca zasobów może odrzucić token nawet wtedy, gdy nie wygasł. Aby poinformować klientów o pomijaniu pamięci podręcznej, nawet jeśli buforowane tokeny nie wygasły, wprowadzamy mechanizm nazywany żądaniem oświadczenia, aby wskazać, że token został odrzucony, i że usługa Azure AD musi wystawić nowy token dostępu.  CaE wymaga aktualizacji klienta, aby zrozumieć wyzwanie oświadczenia. Najnowsza wersja poniższych aplikacji obsługuje żądanie roszczenia:

| | Internet | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Teams** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Office** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **OneDrive** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

### <a name="token-lifetime"></a>Okres istnienia tokenu

Ponieważ ryzyko i zasady są oceniane w czasie rzeczywistym, klienci, którzy negocjują sesje z świadomą ciągłą oceną dostępu, będą polegać na cae zamiast istniejących zasadach okresu istnienia tokenu dostępu statycznego, co oznacza, że konfigurowalne zasady okresu istnienia tokenu nie będą już honorowane dla klientów z możliwością oceny dostępu cae, którzy negocjują sesje z usługą CAE.

Okres istnienia tokenu jest zwiększany do długiego okresu istnienia(do 28 godzin) w sesjach CAE. Odwoływanie jest oparte na krytycznych zdarzeniach i ocenie zasad, a nie tylko na dowolnym okresie. Ta zmiana zwiększa stabilność aplikacji bez wpływu na poziom zabezpieczeń. 

Jeśli nie używasz klientów z funkcją CAE, domyślny okres istnienia tokenu dostępu pozostanie 1 godzinę, chyba że skonfigurowano okres istnienia tokenu dostępu za pomocą funkcji konfigurowalnego okresu istnienia tokenu [(CTL)](../develop/active-directory-configurable-token-lifetimes.md) w wersji zapoznawczej.

## <a name="example-flows"></a>Przykładowe przepływy

### <a name="user-revocation-event-flow"></a>Przepływ zdarzeń odwołania użytkownika:

![Przepływ zdarzeń odwołania użytkownika](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient z możliwością cae przedstawia poświadczenia lub token odświeżania w usłudze Azure AD z prośbą o token dostępu dla pewnego zasobu.
1. Token dostępu jest zwracany wraz z innymi artefaktami do klienta.
1. Administrator [jawnie odwołuje wszystkie tokeny odświeżania dla użytkownika](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Zdarzenie odwołania zostanie wysłane do dostawcy zasobów z usługi Azure AD.
1. Dostawca zasobów przedstawia token dostępu. Dostawca zasobów ocenia poprawność tokenu i sprawdza, czy istnieje jakiekolwiek zdarzenie odwołania dla użytkownika. Dostawca zasobów używa tych informacji do podjęcia decyzji o przyznaniu dostępu do zasobu.
1. W takim przypadku dostawca zasobów nie zezwala na dostęp i wysyła żądanie oświadczeń 401+ do klienta.
1. Klient z możliwością obsługi cae rozumie ponad 401 wyzwań roszczenia. Pomija pamięci podręczne i wraca do kroku 1, wysyłając token odświeżania wraz z żądaniem oświadczenia z powrotem do usługi Azure AD. Usługa Azure AD następnie ponownie przesprawdzi wszystkie warunki i wyświetli użytkownikowi monit o ponowne uwierzytelnienie w tym przypadku.

### <a name="user-condition-change-flow-preview"></a>Przepływ zmiany warunku użytkownika (wersja zapoznawcza):

W poniższym przykładzie administrator dostępu warunkowego skonfigurował zasady dostępu warunkowego opartego na lokalizacji, aby zezwalać na dostęp tylko z określonych zakresów adresów IP:

![Przepływ zdarzeń warunku użytkownika](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Klient z możliwością cae przedstawia poświadczenia lub token odświeżania usłudze Azure AD z prośbą o token dostępu dla pewnego zasobu.
1. Usługa Azure AD ocenia wszystkie zasady dostępu warunkowego, aby sprawdzić, czy użytkownik i klient spełniają warunki.
1. Token dostępu jest zwracany do klienta wraz z innymi artefaktami.
1. Użytkownik przenosi się poza dozwolony zakres adresów IP
1. Klient przedstawia dostawcy zasobów token dostępu spoza dozwolonego zakresu adresów IP.
1. Dostawca zasobów ocenia poprawność tokenu i sprawdza zasady lokalizacji zsynchronizowane z usługą Azure AD.
1. W takim przypadku dostawca zasobów odmawia dostępu i wysyła do klienta żądanie oświadczenia 401+, ponieważ nie pochodzi ono z dozwolonego zakresu adresów IP.
1. Klient z możliwością obsługi cae rozumie ponad 401 wyzwania związane z oświadczeniem. Pomija pamięci podręczne i wraca do kroku 1, wysyłając token odświeżania wraz z żądaniem oświadczenia z powrotem do usługi Azure AD. Usługa Azure AD ponownieocjuje wszystkie warunki i w tym przypadku odmówi dostępu.

## <a name="enable-or-disable-cae-preview"></a>Włączanie lub wyłączanie funkcji CAE (wersja zapoznawcza)

1. Zaloguj się do witryny **Azure Portal** jako administrator dostępu warunkowego, administrator zabezpieczeń lub administrator globalny
1. Przejdź do **witryny Azure Active Directory**  >    >  **ciągłej oceny dostępu zabezpieczeń.**
1. Wybierz pozycję **Włącz podgląd.**

Na tej stronie możesz opcjonalnie ograniczyć użytkowników i grupy, które będą objęte podglądem.

![Włączanie podglądu urzędu certyfikacji w Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="supported-location-policies"></a>Obsługiwane zasady lokalizacji

W przypadku technologii CAE mamy wgląd tylko w nazwane nazwane lokalizacje oparte na adresach IP. Nie ma wglądu w inne ustawienia lokalizacji, takie jak zaufane [ip usługi MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) lub lokalizacje oparte na kraju. Jeśli użytkownik pochodzi z zaufanego adresu IP usługi MFA lub zaufanych lokalizacji, które obejmują zaufane adresy IP usługi MFA lub lokalizację kraju, urząd certyfikacji usługi MFA nie zostanie wymuszony po przeniesienia użytkownika do innej lokalizacji. W takich przypadkach wystawiamy 1-godzinny token CAE bez natychmiastowego sprawdzania wymuszania adresów IP.

> [!IMPORTANT]
> Podczas konfigurowania lokalizacji na użytek ciągłej oceny dostępu należy użyć tylko warunku lokalizacji dostępu warunkowego opartego na protokole IP i skonfigurować wszystkie adresy [IP,](../conditional-access/location-condition.md) w tym **adresy IPv4 i IPv6,** które są widoczne dla dostawcy tożsamości i dostawcy zasobów. Nie używaj warunków lokalizacji kraju ani funkcji zaufanych adresów IP, która jest dostępna na stronie ustawień usługi Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Konfiguracja adresu IP

Dostawcy tożsamości i dostawcy zasobów mogą widzieć różne adresy IP. Ta niezgodność może wystąpić z powodu implementacji serwera proxy sieci w organizacji lub nieprawidłowych konfiguracji protokołu IPv4/IPv6 między dostawcą tożsamości i dostawcą zasobów. Na przykład:

- Dostawca tożsamości widzi jeden adres IP od klienta.
- Dostawca zasobów widzi inny adres IP niż klient po przejściu przez serwer proxy.
- Adres IP, który widzi dostawca tożsamości, jest częścią dozwolonego zakresu adresów IP w zasadach, ale adres IP od dostawcy zasobów nie jest.

Jeśli ten scenariusz istnieje w Twoim środowisku w celu uniknięcia nieskończonych pętli, usługa Azure AD wyemituje godzinny token CAE i nie będzie wymuszać zmiany lokalizacji klienta. Nawet w tym przypadku zabezpieczenia są lepsze w porównaniu do tradycyjnych [](#critical-event-evaluation) tokenów godzinowych, ponieważ nadal oceniamy inne zdarzenia oprócz zdarzeń zmiany lokalizacji klienta.

### <a name="office-and-web-account-manager-settings"></a>Ustawienia pakietu Office Menedżer kont internetowych pakietu Office

| Kanał aktualizacji pakietu Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enterprise Channel | Jeśli ustawiono wartość włączone lub 1, funkcja CAE nie jest obsługiwana. | Jeśli ustawiono wartość włączone lub 1, funkcja CAE nie jest obsługiwana. |
| Bieżący kanał <br> lub <br> Miesięczny kanał przedsiębiorstwa | CaE jest obsługiwany niezależnie od ustawienia | CaE jest obsługiwany niezależnie od ustawienia |

Aby uzyskać informacje o kanałach aktualizacji pakietu Office, zobacz [Overview of update channels for Microsoft 365 Apps (Omówienie](/deployoffice/overview-update-channels)kanałów aktualizacji Microsoft 365 Apps). Zaleca się, aby organizacje nie wyłączały Menedżer kont internetowych (CI).

### <a name="group-membership-and-policy-update-effective-time"></a>Członkostwo w grupie i czas wejścia w życie aktualizacji zasad

Aby członkostwo w grupie i aktualizacja zasad wprowadzone przez administratorów było skuteczne, może upłynieć nawet jeden dzień. Niektóre optymalizacje zostały wykonane dla aktualizacji zasad, które zmniejszają opóźnienie do dwóch godzin. Nie obejmuje on jednak jeszcze wszystkich scenariuszy. 

W razie zagrożenia i konieczności natychmiastowego zaktualizowania zasad lub natychmiastowego zastosowania zmiany członkostwa w grupach do niektórych użytkowników należy użyć tego polecenia programu [PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) lub polecenia "Odwołaj sesję" na stronie profilu użytkownika, aby odwołać sesję użytkowników, co zapewnia natychmiastowe zastosowanie zaktualizowanych zasad.

### <a name="coauthoring-in-office-apps"></a>Współtworzenie w aplikacjach pakietu Office

Jeśli wielu użytkowników jednocześnie współpracuje nad tym samym dokumentem, dostęp użytkownika do dokumentu może nie zostać natychmiast odwołany przez urząd certyfikacji na podstawie zdarzeń odwołania użytkownika lub zmiany zasad. W takim przypadku użytkownik traci dostęp całkowicie po zamknięciu dokumentu, zamknięciu programu Word, Excel lub PowerPoint lub po upływie 10 godzin.

Aby skrócić ten czas, administrator programu SharePoint może opcjonalnie skrócić maksymalny okres istnienia sesji współuwierzytelniania dla dokumentów przechowywanych w usługach SharePoint Online i OneDrive dla Firm, konfigurując zasady lokalizacji sieciowej w usłudze [SharePoint Online.](/sharepoint/control-access-based-on-network-location) Po zmianie tej konfiguracji maksymalny okres istnienia sesji współtworzenie zostanie skrócony do 15 minut i będzie można go dostosować za pomocą polecenia programu PowerShell usługi SharePoint Online "Set-SPOTenant –IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Włącz po wyłączeniu użytkownika

Jeśli włączysz użytkownika bezpośrednio po jego wyłączeniu. Zanim będzie można włączyć konto, będzie pewne opóźnienie. Spo i Teams będą miały opóźnienie 15 minut. Opóźnienie wynosi 35–40 minut dla exo.

## <a name="faqs"></a>Często zadawane pytania

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak będzie działać urząd certyfikacji cae z częstotliwością logowania?

Częstotliwość logowania będzie honorowana z cae lub bez.

## <a name="next-steps"></a>Następne kroki

[Announcing continuous access evaluation (Ogłaszamy ciągłą ocenę dostępu)](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
