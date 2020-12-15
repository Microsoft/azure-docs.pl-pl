---
title: 'Azure AD Connect: bezproblemowe pojedyncze Sign-On — Szybki Start | Microsoft Docs'
description: W tym artykule opisano sposób rozpoczynania pracy z Azure Active Directory bezproblemowe pojedyncze Sign-On
services: active-directory
keywords: Co to jest Azure AD Connect, zainstaluj Active Directory, wymagane składniki usługi Azure AD, logowania jednokrotnego, rejestracji jednokrotnej
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eadb20bc570545356508d82c05e1746424a14b71
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504393"
---
# <a name="azure-active-directory-seamless-single-sign-on-quickstart"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: Szybki Start

## <a name="deploy-seamless-single-sign-on"></a>Wdróż bezproblemowe pojedyncze Sign-On

Azure Active Directory (usługa Azure AD) bezproblemowo pojedyncze Sign-On (bezproblemowe logowanie jednokrotne) automatycznie loguje się do użytkowników, gdy znajdują się na swoich komputerach firmowych, które są połączone z siecią firmową. Bezproblemowe logowanie jednokrotne zapewnia użytkownikom łatwy dostęp do aplikacji opartych na chmurze bez konieczności używania dodatkowych składników lokalnych.

> [!NOTE]
> Ten artykuł zawiera odwołania do warunku *dozwolonych*, termin, przez który firma Microsoft już nie używa. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.

Aby wdrożyć bezproblemową rejestrację jednokrotną, wykonaj następujące kroki.

## <a name="step-1-check-the-prerequisites"></a>Krok 1. Sprawdzanie wymagań wstępnych

Upewnij się, że są spełnione następujące wymagania wstępne:

* **Skonfiguruj serwer Azure AD Connect**: Jeśli używasz [uwierzytelniania przekazywanego](how-to-connect-pta.md) jako metody logowania, nie jest wymagane żadne dodatkowe Sprawdzanie wymagań wstępnych. Jeśli używasz [synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) jako metody logowania, a jeśli między Azure AD Connect i usługą Azure AD istnieje Zapora, upewnij się, że:
   - Używasz wersji 1.1.644.0 lub nowszej Azure AD Connect. 
   - Jeśli zapora lub serwer proxy zezwala, Dodaj połączenia do listy dozwolonych adresów URL **\* msappproxy.NET** na porcie 443. W przeciwnym razie Zezwól na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653), które są aktualizowane co tydzień. To wymaganie wstępne jest stosowane tylko po włączeniu funkcji. Nie jest to wymagane w przypadku rzeczywistych logowań użytkownika.

    >[!NOTE]
    >Azure AD Connect wersje 1.1.557.0, 1.1.558.0, 1.1.561.0 i 1.1.614.0 mają problem związany z synchronizacją skrótów haseł. Jeśli _nie_ zamierzasz używać synchronizacji skrótów haseł w połączeniu z uwierzytelnianiem przekazującym, przeczytaj informacje o [wersji Azure AD Connect](./reference-connect-version-history.md) , aby dowiedzieć się więcej.

* **Użyj obsługiwanej topologii Azure AD Connectej**: Upewnij się, że używasz jednej z Azure AD Connect obsługiwanych topologii opisanych [tutaj](plan-connect-topologies.md).

    >[!NOTE]
    >Bezproblemowe logowanie jednokrotne obsługuje wiele lasów usługi AD, bez względu na to, czy istnieją relacje zaufania między nimi.

* **Skonfiguruj poświadczenia administratora domeny**: musisz mieć poświadczenia administratora domeny dla każdego lasu Active Directory, który:
    * Synchronizacja z usługą Azure AD odbywa się za pomocą Azure AD Connect.
    * Zawiera użytkowników, którym chcesz umożliwić bezproblemowe logowanie jednokrotne.
    
* **Włącz nowoczesne uwierzytelnianie**: aby ta funkcja działała, należy włączyć [nowoczesne uwierzytelnianie](/office365/enterprise/modern-auth-for-office-2013-and-2016) w dzierżawie.

* **Użyj najnowszych wersji Microsoft 365 klientów**: Aby uzyskać ciche środowisko logowania z klientami Microsoft 365 (Outlook, Word, Excel i innymi), użytkownicy muszą używać wersji 16.0.8730. xxxx lub nowszej.

## <a name="step-2-enable-the-feature"></a>Krok 2. Włączanie funkcji

Zapewnij bezproblemowe logowanie jednokrotne za pomocą [Azure AD Connect](whatis-hybrid-identity.md).

>[!NOTE]
> Możesz również [włączyć bezproblemowe logowanie jednokrotne przy użyciu programu PowerShell](tshoot-connect-sso.md#manual-reset-of-the-feature) , jeśli Azure AD Connect nie spełnia wymagań. Użyj tej opcji, jeśli masz więcej niż jedną domenę na Active Directory Las, a chcesz mieć większą wartość dowolną dla domeny, dla której chcesz włączyć bezproblemowe logowanie jednokrotne.

Jeśli wykonujesz nową instalację Azure AD Connect, wybierz [niestandardową ścieżkę instalacji](how-to-connect-install-custom.md). Na stronie **logowania użytkownika** wybierz opcję **Włącz logowanie jednokrotne** .

>[!NOTE]
> Opcja będzie dostępna do wybrania tylko wtedy, gdy metoda logowania jest **synchronizacją skrótu hasła** lub **uwierzytelnianiem przekazującym**.

![Azure AD Connect: Logowanie użytkownika](./media/how-to-connect-sso-quick-start/sso8.png)

Jeśli masz już instalację Azure AD Connect, wybierz stronę **logowania użytkownika** w Azure AD Connect, a następnie wybierz przycisk **dalej**. Jeśli używasz Azure AD Connect wersji 1.1.880.0 lub nowszej, opcja **Włącz logowanie** jednokrotne zostanie domyślnie wybrana. Jeśli używasz starszych wersji Azure AD Connect, zaznacz opcję **Włącz logowanie jednokrotne** .

![Azure AD Connect: zmiana logowania użytkownika](./media/how-to-connect-sso-quick-start/changeusersignin.png)

Kontynuuj pracę z kreatorem, dopóki nie uzyskasz na stronie **Włączanie logowania** jednokrotnego. Podaj poświadczenia administratora domeny dla każdego lasu Active Directory, który:

* Synchronizacja z usługą Azure AD odbywa się za pomocą Azure AD Connect.
* Zawiera użytkowników, którym chcesz umożliwić bezproblemowe logowanie jednokrotne.

Po zakończeniu pracy kreatora w dzierżawie jest włączone bezproblemowe logowanie jednokrotne.

>[!NOTE]
> Poświadczenia administratora domeny nie są przechowywane w Azure AD Connect ani w usłudze Azure AD. Są one używane tylko w celu włączenia tej funkcji.

Postępuj zgodnie z tymi instrukcjami, aby sprawdzić, czy włączono bezproblemową rejestrację logowania jednokrotnego:

1. Zaloguj się do [Centrum administracyjnego Azure Active Directory](https://aad.portal.azure.com) przy użyciu poświadczeń administratora globalnego dla dzierżawy.
2. W lewym okienku wybierz pozycję **Azure Active Directory** .
3. Wybierz **Azure AD Connect**.
4. Upewnij się, że funkcja **bezproblemowego logowania jednokrotnego** jest wyświetlana jako **włączona**.

![Azure Portal: Azure AD Connect okienko](./media/how-to-connect-sso-quick-start/sso10.png)

>[!IMPORTANT]
> Bezproblemowe logowanie jednokrotne tworzy konto komputera o nazwie `AZUREADSSOACC` w lokalnym Active Directory (AD) w każdym lesie usługi AD. `AZUREADSSOACC`Konto komputera musi być silnie chronione ze względów bezpieczeństwa. Tylko Administratorzy domeny powinni mieć możliwość zarządzania kontem komputera. Upewnij się, że delegowanie Kerberos na koncie komputera jest wyłączone i że żadne inne konto w Active Directory nie ma uprawnień do delegowania na `AZUREADSSOACC` koncie komputera. Przechowywanie konta komputera w jednostce organizacyjnej (OU), w którym są bezpieczne przed przypadkowym usunięciem i gdzie tylko Administratorzy domeny mają dostęp.

>[!NOTE]
> Jeśli w środowisku lokalnym są używane architektury łagodzenia przed mieszaniem i kradzieżą poświadczeń, wprowadź odpowiednie zmiany, aby upewnić się, że `AZUREADSSOACC` konto komputera nie kończy się w kontenerze kwarantanny. 

## <a name="step-3-roll-out-the-feature"></a>Krok 3. przechodzenie funkcji

Możesz stopniowo wdrożyć bezproblemową rejestrację jednokrotną dla użytkowników, korzystając z instrukcji podanych poniżej. Aby rozpocząć, Dodaj następujący adres URL usługi Azure AD do ustawień strefy intranetowej wszystkich lub wybranych użytkowników za pomocą zasady grupy w Active Directory:

- `https://autologon.microsoftazuread-sso.com`

Ponadto należy włączyć ustawienie zasad strefy intranet o nazwie **Zezwalaj na aktualizacje na pasku stanu za pośrednictwem skryptu** za pośrednictwem zasady grupy. 

>[!NOTE]
> Poniższe instrukcje dotyczą tylko programu Internet Explorer, przeglądarki Microsoft Edge i Google Chrome w systemie Windows (jeśli udostępnia zestaw zaufanych adresów URL witryny w programie Internet Explorer). Zapoznaj się z następną sekcją, aby uzyskać instrukcje dotyczące sposobu konfigurowania przeglądarki Mozilla Firefox i Google Chrome w systemie macOS.

### <a name="why-do-you-need-to-modify-users-intranet-zone-settings"></a>Dlaczego należy zmodyfikować ustawienia strefy intranetowej użytkowników?

Domyślnie przeglądarka automatycznie oblicza poprawną strefę, Internet lub intranet, z określonego adresu URL. Na przykład usługa `http://contoso/` mapuje do strefy intranet, a następnie `http://intranet.contoso.com/` mapuje ją na strefę internetową (ponieważ adres URL zawiera kropkę). Przeglądarki nie będą wysyłać biletów protokołu Kerberos do punktu końcowego w chmurze, takiego jak adres URL usługi Azure AD, chyba że jawnie dodasz adres URL do strefy intranetowej przeglądarki.

Istnieją dwa sposoby modyfikacji ustawień strefy intranetowej użytkowników:

| Opcja | Zagadnienie dla administratorów | Środowisko użytkownika |
| --- | --- | --- |
| Zasady grupy | Administrator blokuje Edytowanie ustawień strefy intranetowej | Użytkownicy nie mogą modyfikować własnych ustawień |
| Preferencja zasad grupy |  Administrator umożliwia edytowanie ustawień strefy intranetowej | Użytkownicy mogą modyfikować własne ustawienia |

### <a name="group-policy-option---detailed-steps"></a>Opcja "zasady grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy, które są stosowane do niektórych lub wszystkich użytkowników. Ten przykład używa **domyślnych zasad domeny**.
3. Przejdź do strony zasady **konfiguracji użytkownika**  >    >  **Szablony administracyjne**  >  **składniki systemu Windows** internetowy  >  **Internet Explorer**  >  **Panel sterowania Internet**  >  . Następnie wybierz pozycję **lokacja do przypisywania stref**.
    ![Zrzut ekranu pokazujący "stronę zabezpieczeń" z wybraną pozycją "Lista przypisań do strefy".](./media/how-to-connect-sso-quick-start/sso6.png)
4. Włącz zasady, a następnie wprowadź następujące wartości w oknie dialogowym:
   - **Nazwa wartości**: adres URL usługi Azure AD, gdzie są przekazywane bilety protokołu Kerberos.
   - **Wartość** (dane): **1** wskazuje strefę intranetową.

     Wynik wygląda następująco:

     Nazwa wartości: `https://autologon.microsoftazuread-sso.com`
  
     Wartość (dane): 1

   >[!NOTE]
   > Aby uniemożliwić niektórym użytkownikom korzystanie z bezproblemowego logowania jednokrotnego (na przykład jeśli użytkownicy logują się do współużytkowanych kiosków), należy ustawić powyższe wartości na **4**. Ta akcja powoduje dodanie adresu URL usługi Azure AD do strefy z ograniczeniami i bezproblemowy dostęp do logowania jednokrotnego.
   >

5. Wybierz przycisk **OK**, a następnie ponownie wybierz przycisk **OK**.

    ![Zrzut ekranu pokazujący okno "Pokaż zawartość" z wybranym przypisaniem strefy.](./media/how-to-connect-sso-quick-start/sso7.png)

6. Przejdź do obszaru zasady **konfiguracji użytkownika**  >    >  **Szablony administracyjne**  >  **składniki systemu Windows** Internet  >  **Explorer** internetowy  >  **Panel sterowania** Internet  >  **Web Strona**  >  **sieci intranet**. Następnie wybierz pozycję **Zezwalaj na aktualizacje na pasku stanu za pomocą skryptu**.

    ![Zrzut ekranu przedstawiający stronę "strefa intranetowa" z wybraną opcją "Zezwalaj na aktualizacje na pasek stanu za pomocą skryptu".](./media/how-to-connect-sso-quick-start/sso11.png)

7. Włącz ustawienie zasad, a następnie wybierz przycisk **OK**.

    ![Zrzut ekranu przedstawiający okno "Zezwalaj na aktualizacje do paska stanu za pośrednictwem skryptu" z włączonym ustawieniem zasad.](./media/how-to-connect-sso-quick-start/sso12.png)

### <a name="group-policy-preference-option---detailed-steps"></a>Opcja "preferencja zasad grupy" — szczegółowe kroki

1. Otwórz narzędzie Edytor zarządzania zasadami grupy.
2. Edytuj zasady grupy, które są stosowane do niektórych lub wszystkich użytkowników. Ten przykład używa **domyślnych zasad domeny**.
3. Przejdź do pozycji Preferencje **konfiguracji użytkownika**  >    >  **rejestr ustawień systemu Windows**  >    >  **Nowy**  >  **element rejestru**.

    ![Zrzut ekranu pokazujący wybraną pozycję "Rejestr" i "element rejestru".](./media/how-to-connect-sso-quick-start/sso15.png)

4. Wprowadź następujące wartości w odpowiednich polach, a następnie kliknij przycisk **OK**.
   - **Ścieżka klucza**: **_Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\microsoftazuread-SSO.com\autologon_* _
   - _* Nazwa wartości * *: **_https_*_
   - _* Typ wartości * *: **_REG_DWORD_*_
   - _* Dane wartości * *: **_00000001_*_
 
     ![Zrzut ekranu przedstawiający okno "nowe właściwości rejestru".](./media/how-to-connect-sso-quick-start/sso16.png)
 
     ![Logowanie jednokrotne](./media/how-to-connect-sso-quick-start/sso17.png)

### <a name="browser-considerations"></a>Zagadnienia dotyczące przeglądarki

#### <a name="mozilla-firefox-all-platforms"></a>Mozilla Firefox (wszystkie platformy)

Mozilla Firefox nie korzysta automatycznie z uwierzytelniania Kerberos. Każdy użytkownik musi ręcznie dodać adres URL usługi Azure AD do ustawień przeglądarki Firefox, wykonując następujące czynności:
1. Uruchom przeglądarkę Firefox i wprowadź `about:config` na pasku adresu. Odrzuć wszystkie wyświetlone powiadomienia.
2. Wyszukaj ciąg _ *Network. Negotiate-auth. Trusted-URI** Preference. To preferencje zawiera Zaufane witryny programu Firefox do uwierzytelniania Kerberos.
3. Kliknij prawym przyciskiem myszy i wybierz polecenie **Modyfikuj**.
4. Wprowadź `https://autologon.microsoftazuread-sso.com` wartość w polu.
5. Wybierz przycisk **OK** , a następnie ponownie otwórz przeglądarkę.

#### <a name="safari-macos"></a>Safari (macOS)

Upewnij się, że maszyna, na której działa macOS, jest przyłączona do usługi AD. Instrukcje dotyczące dołączania urządzenia z systemem macOS są poza zakresem tego artykułu.

#### <a name="microsoft-edge-based-on-chromium-all-platforms"></a>Microsoft Edge oparta na chromie (wszystkie platformy)

Jeśli ustawienia zasad [AuthNegotiateDelegateAllowlist](/DeployEdge/microsoft-edge-policies#authnegotiatedelegateallowlist) lub [AuthServerAllowlist](/DeployEdge/microsoft-edge-policies#authserverallowlist) zostały zastąpione w Twoim środowisku, upewnij się, że dodano do nich adres URL () usługi Azure AD `https://autologon.microsoftazuread-sso.com` .

#### <a name="microsoft-edge-based-on-chromium-macos-and-other-non-windows-platforms"></a>Microsoft Edge w oparciu o chrom (macOS i inne platformy inne niż Windows)

W przypadku programu Microsoft Edge opartego na chromie na macOS i innych platformach innych niż Windows Zapoznaj się z [listą zasad chromu w witrynie Microsoft Edge](/DeployEdge/microsoft-edge-policies#authserverallowlist) , aby uzyskać informacje na temat dodawania adresu URL usługi Azure AD na potrzeby uwierzytelniania zintegrowanego na liście dozwolonych.

#### <a name="google-chrome-all-platforms"></a>Google Chrome (wszystkie platformy)

Jeśli ustawienia zasad [AuthNegotiateDelegateWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthNegotiateDelegateWhitelist) lub [AuthServerWhitelist](https://www.chromium.org/administrators/policy-list-3#AuthServerWhitelist) zostały zastąpione w Twoim środowisku, upewnij się, że dodano do nich adres URL () usługi Azure AD `https://autologon.microsoftazuread-sso.com` .

#### <a name="google-chrome-macos-and-other-non-windows-platforms"></a>Google Chrome (macOS i inne platformy inne niż Windows)

W przypadku usługi Google Chrome na macOS i innych platform innych niż Windows Zapoznaj się z [listą zasad projektu chrom](https://dev.chromium.org/administrators/policy-list-3#AuthServerWhitelist) , aby uzyskać informacje na temat kontrolowania listy dozwolonych adresów URL usługi Azure AD na potrzeby uwierzytelniania zintegrowanego.

Korzystanie z rozszerzeń zasady grupy Active Directory innych firm do wdrożenia adresu URL usługi Azure AD w programie Firefox i Google Chrome na komputerach Mac wykracza poza zakres tego artykułu.

#### <a name="known-browser-limitations"></a>Znane ograniczenia przeglądarki

Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarkach Firefox i Microsoft Edge. Nie działa również w programie Internet Explorer, jeśli przeglądarka działa w trybie rozszerzonym chronionym. W przypadku następnej wersji przeglądarki Microsoft Edge opartej na chromie nie będzie ona działała w trybie InPrivate i gościa przez projektowanie.

## <a name="step-4-test-the-feature"></a>Krok 4. Testowanie funkcji

Aby przetestować funkcję dla określonego użytkownika, upewnij się, że zostały spełnione wszystkie następujące warunki:
  - Użytkownik loguje się na urządzeniu firmowym.
  - Urządzenie jest przyłączone do domeny Active Directory. Urządzenie _nie_ musi być [przyłączone do usługi Azure AD](../devices/overview.md).
  - Urządzenie ma bezpośrednie połączenie z kontrolerem domeny (DC) w sieci przewodowej lub bezprzewodowej firmy lub za pośrednictwem połączenia dostępu zdalnego, takiego jak połączenie sieci VPN.
  - Funkcja dla tego użytkownika została [przeprowadzona](#step-3-roll-out-the-feature) za pomocą zasady grupy.

W celu przetestowania scenariusza, w którym użytkownik wprowadza tylko nazwę użytkownika, ale nie hasło:
   - Zaloguj się do `https://myapps.microsoft.com/` programu w nowej sesji przeglądarki prywatnej.

W celu przetestowania scenariusza, w którym użytkownik nie musi wprowadzać nazwy użytkownika ani hasła, wykonaj jedną z następujących czynności: 
   - Zaloguj się do `https://myapps.microsoft.com/contoso.onmicrosoft.com` programu w nowej sesji przeglądarki prywatnej. Zamień *contoso* na nazwę dzierżawy.
   - Zaloguj się do `https://myapps.microsoft.com/contoso.com` programu w nowej sesji przeglądarki prywatnej. Zastąp *contoso.com* z zweryfikowaną domeną (nie domeną federacyjną) w dzierżawie.

## <a name="step-5-roll-over-keys"></a>Krok 5. przechodzenie między kluczami

W kroku 2 Azure AD Connect tworzy konta komputerów (reprezentujące usługę Azure AD) we wszystkich lasach Active Directory, w których włączono bezproblemowe logowanie jednokrotne. Aby dowiedzieć się więcej, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne: techniczne głębokie szczegółowe](how-to-connect-sso-how-it-works.md).

>[!IMPORTANT]
>Klucz odszyfrowujący protokołu Kerberos na koncie komputera, w przypadku przecieków, może służyć do generowania biletów protokołu Kerberos dla dowolnego użytkownika w jego lesie usługi AD. Złośliwe podmioty mogą następnie personifikować logowania do usługi Azure AD dla użytkowników, których dotyczy naruszenie. Zdecydowanie zalecamy okresowe przeprowadzenie tych kluczy odszyfrowywania Kerberos — co najmniej raz na 30 dni.

Aby uzyskać instrukcje dotyczące sposobu wycofywania kluczy, zobacz [Azure Active Directory bezproblemowe logowanie jednokrotne: często zadawane pytania](how-to-connect-sso-faq.md). Pracujemy nad możliwością wprowadzenia zautomatyzowanego przejmowania kluczy.

>[!IMPORTANT]
>Nie musisz wykonywać tego kroku _natychmiast_ po włączeniu tej funkcji. Przewinięcie kluczy odszyfrowywania Kerberos co najmniej raz na 30 dni.

## <a name="next-steps"></a>Następne kroki

- [Głębokie szczegółowe](how-to-connect-sso-how-it-works.md): zrozumienie, jak działa bezproblemowa funkcja pojedynczej Sign-On.
- [Często zadawane pytania](how-to-connect-sso-faq.md): Uzyskaj odpowiedzi na często zadawane pytania dotyczące bezproblemowego logowania jednokrotnego.
- [Rozwiązywanie problemów](tshoot-connect-sso.md): informacje na temat rozwiązywania typowych problemów z bezproblemową funkcją pojedynczej Sign-On.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): użyj forum Azure Active Directory, aby obsłużyć nowe żądania funkcji.
