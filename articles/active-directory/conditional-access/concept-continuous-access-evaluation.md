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
ms.openlocfilehash: d707106d66c77ad1f3a1156906add8bb85fd0ce0
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305977"
---
# <a name="continuous-access-evaluation"></a>Ciągła weryfikacja dostępu

Wygaśnięcie i odświeżenie tokenu jest standardowym mechanizmem w branży. Gdy aplikacja kliencka, taka jak program Outlook, nawiązuje połączenie z usługą, taką jak Exchange Online, żądania interfejsu API są autoryzowane przy użyciu tokenów dostępu OAuth 2,0. Domyślnie te tokeny dostępu są ważne przez jedną godzinę, a po ich wygaśnięciu klient zostanie przekierowany z powrotem do usługi Azure AD w celu jego odświeżenia. Ten okres odświeżania umożliwia ocenę zasad dostępu użytkowników. Na przykład: Firma Microsoft może zrezygnować z odświeżenia tokenu z powodu zasad dostępu warunkowego lub wyłączania użytkownika w katalogu. 

Klienci wyrazili wątpliwości dotyczące zwłoki między zmianami warunków użytkownika, np. lokalizacją sieciową lub kradzieżą poświadczeń, a także w przypadku wymuszania zasad związanych z tą zmianą. Poznamy podejście "Blunt Object" dotyczące krótszych okresów istnienia tokenu, ale znaleziono, że mogą one obniżyć wydajność i niezawodność użytkowników bez eliminowania zagrożeń.

Czasowa reakcja na naruszenia zasad lub problemy z zabezpieczeniami naprawdę wymaga "konwersacji" między wystawcą tokenów, takimi jak Azure AD, a jednostką uzależnioną, taką jak Exchange Online. Ta dwukierunkowa konwersacja daje nam dwie ważne możliwości. Jednostka uzależniona może zwrócić uwagę na to, że zmiany uległy zmianie, takie jak klient pochodzący z nowej lokalizacji i informujący wystawca tokenów. Zapewnia również wystawcy tokenów sposób informowania jednostki uzależnionej o zaprzestanie poszanowania tokenów dla danego użytkownika ze względu na złamanie konta, wyłączenie lub inne problemy. Mechanizmem tej konwersacji jest ciągły dostęp do wersji ewaluacyjnej (CAE). Celem jest osiągnięcie niemal rzeczywistego czasu reakcji, ale w niektórych przypadkach można zaobserwować opóźnienie do 15 minut z powodu czasu propagacji zdarzeń.

Początkowa implementacja oceny ciągłego dostępu koncentruje się na wymianie, zespołach i usłudze SharePoint Online.

Aby przygotować aplikacje do korzystania z programu CAE, zobacz [jak używać interfejsów API do oceny dostępu ciągłego w aplikacjach](../develop/app-resilience-continuous-access-evaluation.md).

### <a name="key-benefits"></a>Najważniejsze korzyści

- Zakończenie lub zmiana hasła użytkownika: odwoływanie sesji użytkownika zostanie wymuszone w czasie niemal rzeczywistym.
- Zmiana lokalizacji sieciowej: zasady lokalizacji dostępu warunkowego będą wymuszane niemal w czasie rzeczywistym.
- Eksport tokenu do maszyny spoza zaufanej sieci można zablokować przy użyciu zasad lokalizacji dostępu warunkowego.

## <a name="scenarios"></a>Scenariusze 

Istnieją dwa scenariusze, które składają się na ciągłą ocenę dostępu, krytyczną ocenę zdarzeń i ocenę zasad dostępu warunkowego.

### <a name="critical-event-evaluation"></a>Krytyczne oszacowanie zdarzeń

Ciągła ocena dostępu jest implementowana przez włączenie usług, takich jak Exchange Online, SharePoint Online i zespoły, aby subskrybować krytyczne zdarzenia w usłudze Azure AD, dzięki czemu te zdarzenia można ocenić i wymusić niemal w czasie rzeczywistym. Ocena zdarzeń krytycznych nie polega na zasadach dostępu warunkowego, tak aby była dostępna w żadnej dzierżawie. Następujące zdarzenia są obecnie oceniane:

- Konto użytkownika zostało usunięte lub wyłączone
- Hasło użytkownika jest zmieniane lub resetowane
- Uwierzytelnianie wieloskładnikowe jest włączone dla użytkownika
- Administrator jawnie odwołuje wszystkie tokeny odświeżania dla użytkownika
- Wykryto duże ryzyko dla użytkownika Azure AD Identity Protection

Ten proces umożliwia scenariuszowi, w którym użytkownicy tracą dostęp do organizacyjnych plików usługi SharePoint Online, wiadomości e-mail, kalendarza lub zadań, a także zespoły z Microsoft 365 aplikacji klienckich w ciągu minut od jednego z tych zdarzeń krytycznych. 

> [!NOTE] 
> Zespoły nie obsługują jeszcze zdarzeń ryzyka użytkownika.

### <a name="conditional-access-policy-evaluation-preview"></a>Ocena zasad dostępu warunkowego (wersja zapoznawcza)

Programy Exchange i SharePoint mogą synchronizować kluczowe zasady dostępu warunkowego, aby mogły być oceniane w ramach samej usługi.

Ten proces umożliwia scenariuszowi, w którym użytkownicy tracą dostęp do plików organizacji, wiadomości e-mail, kalendarza lub zadań z Microsoft 365 aplikacji klienckich lub usługi SharePoint Online bezpośrednio po zmianie lokalizacji sieciowej.

> [!NOTE]
> Nie wszystkie kombinacje aplikacji i dostawcy zasobów są obsługiwane. Zobacz poniższą tabelę. Pakiet Office odnosi się do programów Word, Excel i PowerPoint.

| | Sieć Web programu Outlook | Program Outlook Win32 | Outlook iOS | Program Outlook Android | Komputer Mac z programem Outlook |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Exchange Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

| | Aplikacje sieci Web pakietu Office | Aplikacje pakietu Office Win32 | Pakiet Office dla systemu iOS | Pakiet Office dla systemu Android | Pakiet Office dla komputerów Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Exchange Online** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

| | Usługa OneDrive w sieci Web | Usługa OneDrive Win32 | OneDrive dla systemu iOS | Usługa OneDrive dla systemu Android | Adres MAC usługi OneDrive |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

### <a name="client-side-claim-challenge"></a>Wyzwanie dla żądania po stronie klienta

Przed ciągłą oceną dostępu klienci będą zawsze próbować odtworzyć token dostępu z jego pamięci podręcznej, o ile nie wygasł. Dzięki CAE wprowadzamy nowy przypadek, że dostawca zasobów może odrzucić token nawet wtedy, gdy nie wygasł. Aby poinformować klientów o konieczności obejścia swojej pamięci podręcznej, nawet jeśli tokeny buforowane nie wygasły, wprowadzimy mechanizm o nazwie **wyzwanie** , aby wskazać, że token został odrzucony, a nowy token dostępu musi być wystawiony przez usługę Azure AD. CAE wymaga aktualizacji klienta w celu zrozumienia wyzwania żądania. Najnowsza wersja następujących aplikacji poniżej obsługuje wyzwanie dla żądania:

| | Internet | Win32 | iOS | Android | Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Outlook** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Teams** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **Office** | Nieobsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |
| **OneDrive** | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane | Obsługiwane |

### <a name="token-lifetime"></a>Okres istnienia tokenu

Ze względu na to, że ryzyko i zasady są oceniane w czasie rzeczywistym, klienci, którzy negocjują sesje z obsługą oceny ciągłego dostępu, będą korzystać z CAE zamiast istniejących zasad okresów istnienia tokenu dostępu statycznego, co oznacza, że zasady okresowego okresu istnienia tokenów nie będą już brane pod uwagę dla klientów obsługujących CAE.

Okres istnienia tokenu może być długi, do 28 godzin w sesjach CAE. Odwołanie jest obsługiwane przez krytyczne zdarzenia i oceny zasad, a nie tylko w danym okresie. Ta zmiana zwiększa stabilność aplikacji bez wpływu na stan zabezpieczeń. 

Jeśli nie korzystasz z klientów z systemem CAE, domyślny okres istnienia tokenu dostępu będzie pozostawać w ciągu 1 godziny, chyba że okres istnienia tokenu dostępu został skonfigurowany z użyciem funkcji wersji zapoznawczej [okres istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) .

## <a name="example-flows"></a>Przykładowe przepływy

### <a name="user-revocation-event-flow"></a>Przepływ zdarzeń odwołania użytkownika:

![Przepływ zdarzeń odwołania użytkownika](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. Klient obsługujący CAE przedstawia poświadczenia lub token odświeżenia w usłudze Azure AD z prośbą o token dostępu dla pewnego zasobu.
1. Token dostępu jest zwracany wraz z innymi artefaktami dla klienta.
1. Administrator jawnie [odwołuje wszystkie tokeny odświeżania dla użytkownika](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Zdarzenie odwołania zostanie wysłane do dostawcy zasobów z usługi Azure AD.
1. Dostawca zasobów przedstawia token dostępu. Dostawca zasobów szacuje ważność tokenu i sprawdza, czy istnieją jakieś zdarzenia odwołania dla użytkownika. Dostawca zasobów używa tych informacji do podejmowania decyzji o udzieleniu dostępu do zasobu.
1. W takim przypadku dostawca zasobów nie zezwala na dostęp i wysyła wezwanie do klienta 401 + roszczeń z powrotem.
1. Klient z obsługą CAE rozumie wyzwanie 401 i roszczeń. Pomija pamięć podręczną i wraca do kroku 1, wysyłając token odświeżania wraz z wezwaniem do usługi Azure AD. Usługa Azure AD będzie następnie ponownie szacować wszystkie warunki i monitować użytkownika o ponowne uwierzytelnienie w tym przypadku.

### <a name="user-condition-change-flow-preview"></a>Przepływ zmian warunku użytkownika (wersja zapoznawcza):

W poniższym przykładzie administrator dostępu warunkowego skonfigurował zasady dostępu warunkowego na podstawie lokalizacji, aby zezwalać na dostęp tylko z określonych zakresów adresów IP:

![Przepływ zdarzeń warunku użytkownika](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. Klient obsługujący CAE przedstawia poświadczenia lub token odświeżenia w usłudze Azure AD z prośbą o token dostępu dla pewnego zasobu.
1. Usługa Azure AD szacuje wszystkie zasady dostępu warunkowego, aby sprawdzić, czy użytkownik i klient spełniają warunki.
1. Token dostępu jest zwracany wraz z innymi artefaktami dla klienta.
1. Użytkownik przechodzi poza dozwolony zakres adresów IP
1. Klient przedstawia token dostępu dostawcy zasobów spoza dozwolonego zakresu adresów IP.
1. Dostawca zasobów szacuje ważność tokenu i sprawdza zasady lokalizacji zsynchronizowane z usługą Azure AD.
1. W takim przypadku dostawca zasobów nie zezwala na dostęp i wysyła żądanie 401 + żądanie z powrotem do klienta, ponieważ nie pochodzi z dozwolonego zakresu adresów IP.
1. Klient z obsługą CAE rozumie wyzwanie 401 i roszczeń. Pomija pamięć podręczną i wraca do kroku 1, wysyłając token odświeżania wraz z wezwaniem do usługi Azure AD. Usługa Azure AD przeprowadzi ocenę wszystkich warunków i odmówi dostępu w tym przypadku.

## <a name="enable-or-disable-cae-preview"></a>Włącz lub Wyłącz CAE (wersja zapoznawcza)

1. Zaloguj się do **Azure Portal** jako administrator dostępu warunkowego, administrator zabezpieczeń lub Administrator globalny
1. Przejdź do wersji  >    >  **ewaluacyjnej ciągłego dostępu** Azure Active Directory zabezpieczeń.
1. Wybierz pozycję **Włącz podgląd**.

Na tej stronie możesz opcjonalnie ograniczyć użytkowników i grupy, które będą podlegać wersji zapoznawczej.

![Włączanie podglądu CAE w Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="supported-location-policies"></a>Obsługiwane zasady lokalizacji

W przypadku usługi CAE dostępne są tylko szczegółowe informacje o nazwanych lokalizacjach opartych na adresie IP. Nie mamy szczegółowych informacji na temat innych ustawień lokalizacji, takich jak [Zaufane adresy IP usługi MFA](../authentication/howto-mfa-mfasettings.md#trusted-ips) lub lokalizacje oparte na krajach. Jeśli użytkownik pochodzi z zaufanego adresu IP usługi MFA lub zaufanych lokalizacji, które zawierają zaufane IP lub lokalizacje krajów usługi MFA, CAE nie zostanie wymuszone po przeniesieniu użytkownika do innej lokalizacji. W takich przypadkach zostanie wystawiony token CAE o godzinie 1, bez kontroli przymusowego protokołu IP.

> [!IMPORTANT]
> Podczas konfigurowania lokalizacji na potrzeby oceny ciągłego dostępu należy używać tylko [warunku lokalizacji dostępu warunkowego na podstawie adresu IP](../conditional-access/location-condition.md) i skonfigurować wszystkie adresy IP, w **tym Protokoły IPv4 i IPv6**, które mogą być widoczne dla dostawcy tożsamości i dostawcy zasobów. Nie używaj warunków lokalizacji kraju ani funkcji zaufanych adresów IP, która jest dostępna na stronie ustawień usługi Azure AD Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Konfiguracja adresu IP

Dostawca tożsamości i dostawcy zasobów mogą zobaczyć różne adresy IP. Taka niezgodność może wystąpić ze względu na implementacje serwera proxy sieci w organizacji lub nieprawidłowe konfiguracje protokołu IPv4/IPv6 między dostawcą tożsamości i dostawcą zasobów. Na przykład:

- Dostawca tożsamości widzi jeden adres IP od klienta.
- Dostawca zasobów widzi inny adres IP od klienta po przejściu przez serwer proxy.
- Adres IP widziany dla dostawcy tożsamości jest częścią dozwolonego zakresu adresów IP w zasadach, ale adres IP od dostawcy zasobów nie jest.

Jeśli ten scenariusz istnieje w Twoim środowisku, aby uniknąć nieskończonych pętli, usługa Azure AD wystawia token CAE w ciągu godziny i nie wymusi zmiany lokalizacji klienta. Nawet w tym przypadku Ulepszono zabezpieczenia w porównaniu z tradycyjnymi tokenami jednogodzinnymi, ponieważ nadal oceniamy [inne zdarzenia](#critical-event-evaluation) poza zdarzeniami zmiany lokalizacji klienta.

### <a name="office-and-web-account-manager-settings"></a>Ustawienia pakietu Office i Menedżera kont sieci Web

| Kanał aktualizacji pakietu Office | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual kanału przedsiębiorstwa | Jeśli ustawiona na wartość Enabled lub 1, CAE nie jest obsługiwana. | Jeśli ustawiona na wartość Enabled lub 1, CAE nie jest obsługiwana. |
| Bieżący kanał <br> lub <br> Miesięczny kanał przedsiębiorstwa | CAE jest obsługiwany niezależnie od ustawienia | CAE jest obsługiwany niezależnie od ustawienia |

Aby uzyskać wyjaśnienie kanałów aktualizacji pakietu Office, zobacz [Omówienie kanałów aktualizacji dla aplikacji Microsoft 365](/deployoffice/overview-update-channels). Zalecane jest, aby organizacje nie wyłączać Menedżera kont sieci Web (WAM).

### <a name="group-membership-and-policy-update-effective-time"></a>Członkostwo w grupie i czas obowiązywania aktualizacji zasad

Członkostwo w grupach i aktualizacja zasad podejmowanych przez administratorów może zająć do jednego dnia. Niektóre optymalizacje zostały wykonane w przypadku aktualizacji zasad, co zmniejsza opóźnienie do dwóch godzin. Nie dotyczy to jednak wszystkich scenariuszy. 

Jeśli występują sytuacje awaryjne i konieczne jest zaktualizowanie zasad lub zmiana członkostwa w grupie w celu natychmiastowego zastosowania do określonych użytkowników, należy użyć tego [polecenia programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) lub "odwołaj sesję" na stronie profilu użytkownika, aby odwołać sesję użytkowników, co spowoduje, że zaktualizowane zasady zostaną zastosowane natychmiast.

### <a name="coauthoring-in-office-apps"></a>Współtworzenie w aplikacjach pakietu Office

Gdy wielu użytkowników współpracują nad tym samym dokumentem w tym samym czasie, dostęp użytkownika do dokumentu może nie być natychmiast odwoływany przez CAE na podstawie zdarzenia odwołania lub zmiany zasad przez użytkownika. W takim przypadku użytkownik utraci dostęp całkowicie po, zamykając dokument, zamyka Word, Excel lub PowerPoint lub po upływie 10 godzin.

Aby skrócić ten czas, administrator programu SharePoint może opcjonalnie skrócić maksymalny okres istnienia sesji współtworzenia dla dokumentów przechowywanych w usługach SharePoint Online i OneDrive dla firm, [konfigurując zasady lokalizacji sieciowej w usłudze SharePoint Online](/sharepoint/control-access-based-on-network-location). Po zmianie tej konfiguracji maksymalny okres istnienia sesji współtworzenia będzie krótszy niż 15 minut i można go dodatkowo dostosować przy użyciu polecenia programu SharePoint Online PowerShell "Set-SPOTenant – IPAddressWACTokenLifetime"

### <a name="enable-after-a-user-is-disabled"></a>Włącz po wyłączeniu użytkownika

Jeśli po wyłączeniu zostanie włączone uprawnienie użytkownika. Aby można było włączyć konto, będą dostępne pewne opóźnienia. SPO i zespoły będą mieć opóźnienie 15 minut. Opóźnienie wynosi 35-40 minut dla EXO.

## <a name="faqs"></a>Często zadawane pytania

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Jak będzie CAE z częstotliwością logowania?

Częstotliwość logowania będzie uznawana za CAE lub bez niej.

## <a name="next-steps"></a>Następne kroki

[Ogłaszanie ciągłej oceny dostępu](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)
