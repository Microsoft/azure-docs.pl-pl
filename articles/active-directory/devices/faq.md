---
title: Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami | Microsoft Docs
description: Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 509d267dcdaef58a9792609a60c8a9401e90e867
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199713"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory często zadawane pytania dotyczące zarządzania urządzeniami

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>P: ostatnio zarejestrowano urządzenie. Dlaczego nie widzę urządzenia w obszarze Moje informacje o użytkowniku w Azure Portal? Lub dlaczego właściciel urządzenia jest oznaczony jako N/A dla urządzeń, które są dołączone do hybrydowego Azure Active Directory (Azure AD)?

Odp **.:** Urządzenia z systemem Windows 10, które są dołączone do hybrydowej usługi Azure AD, nie są wyświetlane w obszarze **urządzenia użytkowników**.
Użyj widoku **wszystkie urządzenia** w Azure Portal. Można również użyć polecenia cmdlet [Get-MsolDevice](/powershell/module/msonline/get-msoldevice) programu PowerShell.

W obszarze **urządzenia użytkowników** są wyświetlane tylko następujące urządzenia:

- Wszystkie urządzenia osobiste, które nie są dołączone do hybrydowej usługi Azure AD. 
- Wszystkie urządzenia z systemem innym niż Windows 10 lub Windows Server 2016.
- Wszystkie urządzenia z systemem innym niż Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>P: Jak mogę wiedzieć, czym jest stan rejestracji urządzenia klienta?

Odp **.:** W Azure Portal przejdź do pozycji **wszystkie urządzenia**. Wyszukaj urządzenie przy użyciu identyfikatora urządzenia. Sprawdź wartość w kolumnie Typ sprzężenia. Czasami urządzenie może być resetowane lub odtworzenie obrazu. W związku z tym należy również sprawdzić stan rejestracji urządzenia na urządzeniu:

- W przypadku urządzeń z systemem Windows 10 i Windows Server 2016 lub nowszym Uruchom polecenie `dsregcmd.exe /status` .
- W przypadku wcześniejszych wersji systemu operacyjnego Uruchom polecenie `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` .

Odp **.:** Informacje dotyczące rozwiązywania problemów można znaleźć w następujących artykułach:
- [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md)
- [Rozwiązywanie problemów z Azure Active Directory hybrydowych dołączonych do urządzeń z systemami Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>P: widzę rekord urządzenia pod informacjami o UŻYTKOWNIKu w Azure Portal. I widzę stan zarejestrowany na urządzeniu. Czy poprawnie skonfigurowano korzystanie z dostępu warunkowego?

Odp **.:** Stan dołączania urządzenia, wyświetlany przez **deviceID**, musi być zgodny z stanem w usłudze Azure AD i spełniać wszystkie kryteria oceny dostępu warunkowego. Aby uzyskać więcej informacji, zobacz [Wymagaj zarządzanych urządzeń dla dostępu do aplikacji w chmurze przy użyciu dostępu warunkowego](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>P: Dlaczego mój użytkownicy widzą komunikat o błędzie informujący o tym, że Twoja organizacja usunęła urządzenie "lub" Twoja organizacja wyłączyła urządzenie "na swoich urządzeniach z systemem Windows 10?

Odp **.:** Na urządzeniach z systemem Windows 10 dołączonych lub zarejestrowanych w usłudze Azure AD użytkownicy są wystawiani jako [podstawowy token odświeżania (PRT)](concept-primary-refresh-token.md) , który umożliwia logowanie jednokrotne. Ważność PRT jest oparta na ważności samego urządzenia. Ten komunikat jest wyświetlany, jeśli urządzenie zostało usunięte lub wyłączone w usłudze Azure AD bez inicjowania akcji z samego urządzenia. Urządzenie można usunąć lub wyłączyć w usłudze Azure AD w jednym z następujących scenariuszy: 

- Użytkownik wyłącza urządzenie w portalu My Apps. 
- Administrator (lub użytkownik) usuwa lub wyłącza urządzenie w Azure Portal lub przy użyciu programu PowerShell
- Tylko dołączona do hybrydowej usługi Azure AD: Administrator usuwa z zakresu synchronizacji urządzenia poza zakresem, co spowodowało usunięcie urządzeń z usługi Azure AD.
- Uaktualnianie programu Azure AD Connect do wersji 1.4. XX. x. [Zrozumienie Azure AD Connect 1.4. XX. x i znikanie urządzenia](../hybrid/reference-connect-device-disappearance.md).


Poniżej znajdują się informacje o tym, jak można skorygować te akcje.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>P: wyłączono lub usunięto urządzenie w Azure Portal lub przy użyciu programu Windows PowerShell. Jednak stan lokalny na urządzeniu wskazuje, że jest on nadal zarejestrowany. Co mam zrobić?

Odp **.:** Ta operacja jest zaprojektowana. W takim przypadku urządzenie nie ma dostępu do zasobów w chmurze. Administratorzy mogą wykonać tę akcję dla starych, zagubionych lub skradzionych urządzeń, aby zapobiec nieautoryzowanemu dostępowi. Jeśli ta akcja została wykonana przypadkowo, należy ponownie włączyć lub ponownie zarejestrować urządzenie zgodnie z poniższym opisem

- Jeśli urządzenie zostało wyłączone w usłudze Azure AD, administrator z odpowiednimi uprawnieniami może je włączyć z poziomu portalu usługi Azure AD.  
  > [!NOTE]
  > W przypadku synchronizowania urządzeń przy użyciu Azure AD Connect urządzenia dołączone do hybrydowej usługi Azure AD zostaną automatycznie ponownie włączone w następnym cyklu synchronizacji. Jeśli więc musisz wyłączyć urządzenie dołączone do hybrydowej usługi Azure AD, musisz wyłączyć je z lokalnej usługi AD

 - Jeśli urządzenie zostało usunięte w usłudze Azure AD, należy ponownie zarejestrować urządzenie. Aby ponownie zarejestrować, musisz wykonać akcję ręczną na urządzeniu. Poniżej znajdują się instrukcje dotyczące ponownej rejestracji w zależności od stanu urządzenia. 

      Aby ponownie zarejestrować hybrydowe urządzenia z systemem Windows 10 i Windows Server 2016/2019 dołączone do usługi Azure AD, wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź `dsregcmd.exe /debug /leave`.
      1. Wyloguj się i zaloguj się, aby wyzwolić zaplanowane zadanie, które ponownie rejestruje urządzenie w usłudze Azure AD. 

      W przypadku wersji systemu operacyjnego Windows niższego poziomu, które są dołączone do hybrydowej usługi Azure AD, wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator.
      1. Wprowadź `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Wprowadź `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      W przypadku urządzeń z systemem Windows 10 przyłączonych do usługi Azure AD wykonaj następujące czynności:

      1. Otwórz wiersz polecenia jako administrator
      1. Wprowadź `dsregcmd /forcerecovery` (Uwaga: musisz być administratorem, aby wykonać tę akcję).
      1. W oknie dialogowym kliknij pozycję "Zaloguj się" i Kontynuuj proces logowania.
      1. Wyloguj się i zaloguj się ponownie na urządzeniu, aby zakończyć odzyskiwanie.

      W przypadku urządzeń z systemem Windows 10 zarejestrowanych w usłudze Azure AD wykonaj następujące czynności:

      1. Przejdź do pozycji **Ustawienia**  >  **konta**  >  **dostęp do zasobów służbowych**. 
      1. Wybierz konto i wybierz pozycję **Rozłącz**.
      1. Kliknij pozycję "+ Połącz" i ponownie Zarejestruj urządzenie, przechodząc przez proces logowania.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>P: Dlaczego w Azure Portal są widoczne zduplikowane wpisy urządzeń?

**Z**

- W przypadku systemów Windows 10 i Windows Server 2016 powtórzone próby odłączenia i ponownego dołączenia do tego samego urządzenia mogą spowodować zduplikowane wpisy. 
- Każdy użytkownik systemu Windows korzystający z **dodawania konta służbowego** tworzy nowy rekord urządzenia o tej samej nazwie.
- W przypadku wersji systemu Windows niższego poziomu, które są przyłączone do lokalnej domeny usługi Azure Directory, automatyczna rejestracja powoduje utworzenie nowego rekordu urządzenia o tej samej nazwie dla każdego użytkownika domeny, który loguje się na urządzeniu. 
- Komputer przyłączony do usługi Azure AD, który został wyczyszczony, ponownie zainstalowany i z tą samą nazwą, będzie wyświetlany jako inny rekord o tej samej nazwie.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>P: czy rejestracja urządzeń z systemem Windows 10 w usłudze Azure AD obsługuje moduły TPM w trybie FIPS?

Odp **.:** Rejestracja urządzeń z systemem Windows 10 jest obsługiwana tylko w przypadku modułu TPM zgodnego ze standardem FIPS 2,0 i nie jest obsługiwana przez moduł TPM 1,2. Jeśli na urządzeniach jest używany moduł TPM zgodny ze standardem FIPS 1,2, należy je wyłączyć przed przejściem do usługi Azure AD Join lub hybrydowego sprzężenia usługi Azure AD. Firma Microsoft nie udostępnia żadnych narzędzi do wyłączania trybu FIPS dla moduły TPM, ponieważ jest on zależny od producenta modułu TPM. Skontaktuj się z producentem OEM, aby uzyskać pomoc techniczną. 

---

**P: dlaczego użytkownik może nadal uzyskiwać dostęp do zasobów z urządzenia, które zostało wyłączone w Azure Portal?**

Odp **.:** Aby można było zastosować odwołanie od momentu, gdy urządzenie usługi Azure AD zostanie oznaczone jako wyłączone, zajmie do godziny.

>[!NOTE] 
>W przypadku zarejestrowanych urządzeń zalecamy wyczyszczenie urządzenia, aby upewnić się, że użytkownicy nie będą mogli uzyskać dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [co to jest rejestracja urządzenia?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>P: Dlaczego istnieją urządzenia oznaczone jako "oczekujące" w zarejestrowanej kolumnie w Azure Portal?

Odp **.:** oczekiwanie wskazuje, że urządzenie nie jest zarejestrowane. Ten stan wskazuje, że urządzenie zostało zsynchronizowane przy użyciu programu Azure AD Connect z lokalnej usługi AD i jest gotowe do rejestracji urządzeń. Te urządzenia mają ustawiony typ SPRZĘŻENIa "hybrydowa usługa Azure AD". Dowiedz się więcej na [temat planowania implementacji dołączania hybrydowego Azure Active Directory](hybrid-azuread-join-plan.md).

>[!NOTE]
>Urządzenie może także ulec zmianie z stanu zarejestrowanego na "oczekiwanie"
>* Jeśli urządzenie zostanie najpierw usunięte z usługi Azure AD i zsynchronizowane z lokalną usługą AD.
>* Jeśli urządzenie zostanie usunięte z zakresu synchronizacji w Azure AD Connect i dodane z powrotem.
>
>W obu przypadkach należy ponownie zarejestrować urządzenie ręcznie na każdym z tych urządzeń. Aby sprawdzić, czy urządzenie zostało wcześniej zarejestrowane, możesz [rozwiązać problemy z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md).

---

### <a name="q-i-cannot-add-more-than-3-azure-ad-user-accounts-under-the-same-user-session-on-a-windows-10-device-why"></a>P: nie mogę dodać więcej niż 3 kont użytkowników usługi Azure AD w ramach tej samej sesji użytkownika na urządzeniu z systemem Windows 10. Dlaczego?

Odp **.: usługa** Azure AD dodaliśmy obsługę wielu kont usługi Azure AD w systemie Windows 10 1803 Release. Jednak system Windows 10 ogranicza liczbę kont usługi Azure AD na urządzeniu do 3, aby ograniczyć rozmiar żądań tokenów i włączyć niezawodne Logowanie jednokrotne. Po dodaniu 3 kont użytkownicy zobaczą błąd w przypadku kolejnych kont. Dodatkowe informacje o problemie na ekranie błędu zawierają następujący komunikat informujący o tym, że operacja dodawania konta jest zablokowana, ponieważ osiągnięto limit konta. 

---
## <a name="azure-ad-join-faq"></a>Usługa Azure AD Join — często zadawane pytania

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>P: Jak mogę odłączania urządzenia dołączonego do usługi Azure AD lokalnie na urządzeniu?

Odp **.:** W przypadku urządzeń z czystym usługą Azure AD upewnij się, że masz konto administratora lokalnego w trybie offline lub utwórz je. Nie można zalogować się przy użyciu żadnych poświadczeń użytkownika usługi Azure AD. Następnie przejdź do pozycji **Ustawienia**  >  **konta**  >  **dostęp do zasobów służbowych**. Wybierz swoje konto i wybierz pozycję **Rozłącz**. Po wyświetleniu monitu postępuj zgodnie z monitami i podaj poświadczenia administratora lokalnego. Uruchom ponownie urządzenie, aby zakończyć proces rozłączania.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>P: Czy moje użytkownicy mogą logować się do urządzeń przyłączonych do usługi Azure AD, które są usuwane lub wyłączone w usłudze Azure AD?

**Odpowiedź:** tak. System Windows ma wbudowaną funkcję username i Password, która umożliwia użytkownikom zalogowanym wcześniej dostęp do pulpitu nawet bez połączenia sieciowego. 

Gdy urządzenie zostanie usunięte lub wyłączone w usłudze Azure AD, nie jest znane jako urządzenie z systemem Windows. Użytkownicy, którzy wcześniej zalogowani, nadal uzyskują dostęp do pulpitu przy użyciu buforowanej nazwy użytkownika i hasła. Jednak po usunięciu lub wyłączeniu urządzenia użytkownicy nie mogą uzyskać dostępu do żadnych zasobów chronionych przez dostęp warunkowy oparty na urządzeniach. 

Użytkownicy, którzy nie zalogować się wcześniej, nie mogą uzyskać dostępu do urządzenia. Nie włączono dla nich buforowanej nazwy użytkownika i hasła. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>P: czy użytkownik może być zalogowany lub usunięty na urządzeniach dołączonych do usługi Azure AD

Odp **.:** Tak, ale tylko przez ograniczony czas. Gdy użytkownik zostanie usunięty lub wyłączony w usłudze Azure AD, nie jest on natychmiast znany na urządzeniu z systemem Windows. Wcześniej zalogowani użytkownicy mogą uzyskać dostęp do pulpitu przy użyciu buforowanej nazwy użytkownika i hasła. 

Zazwyczaj urządzenie ma świadomość stanu użytkownika w czasie krótszym niż cztery godziny. Następnie system Windows blokuje dostęp tych użytkowników do pulpitu. Gdy użytkownik jest usuwany lub wyłączony w usłudze Azure AD, wszystkie ich tokeny są odwoływane. Nie mogą uzyskać dostępu do żadnych zasobów. 

Usunięci lub wyłączni użytkownicy, którzy nie zalogują się wcześniej, nie mogą uzyskać dostępu do urządzenia. Nie włączono dla nich buforowanej nazwy użytkownika i hasła. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>P: Dlaczego mój użytkownik ma problemy z urządzeniami przyłączonymi do usługi Azure AD po zmianie nazwy UPN?

Odp **.:** Obecnie zmiany nazw UPN nie są w pełni obsługiwane na urządzeniach dołączonych do usługi Azure AD. Uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po zmianie nazwy UPN. W związku z tym użytkownicy mają problemy z logowaniem jednokrotnym i dostępem warunkowym na swoich urządzeniach. W tej chwili użytkownicy muszą zalogować się do systemu Windows za pomocą kafelka "inny użytkownik" przy użyciu nowej nazwy UPN, aby rozwiązać ten problem. Obecnie pracujemy nad tym problemem. Jednak użytkownicy logujący się przy użyciu usługi Windows Hello dla firm nie będą napotykać tego problemu. 

Zmiany nazw UPN są obsługiwane w ramach aktualizacji systemu Windows 10 2004. Użytkownicy na urządzeniach z tą aktualizacją nie będą mieli żadnych problemów po zmianie ich nazw UPN

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>P: Moi użytkownicy nie mogą przeszukiwać drukarek z urządzeń przyłączonych do usługi Azure AD. Jak mogę włączyć drukowanie z tych urządzeń?

Odp **.:** Aby wdrożyć drukarki dla urządzeń przyłączonych do usługi Azure AD, zobacz [Wdrażanie chmury hybrydowej systemu Windows Server przy użyciu funkcji wstępnego uwierzytelniania](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Do wdrożenia w chmurze hybrydowej jest potrzebny lokalny serwer systemu Windows. Obecnie usługa drukowania oparta na chmurze jest niedostępna. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>P: Jak mogę połączyć się ze zdalnym urządzeniem dołączonym do usługi Azure AD?

Odp **.:** Zobacz [nawiązywanie połączenia z komputerem przyłączonym do zdalnego Azure Active Directory](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>P: Dlaczego widzę *, że nie można w tym miejscu uzyskać z tego miejsca*?

Odp **.:** Czy skonfigurowano pewne reguły dostępu warunkowego, aby wymagały określonego stanu urządzenia? Jeśli urządzenie nie spełnia kryteriów, użytkownicy są blokowani i widzą ten komunikat. Oceń reguły zasad dostępu warunkowego. Upewnij się, że urządzenie spełnia kryteria, aby uniknąć komunikatu.

---

### <a name="q-why-dont-some-of-my-users-get-azure-ad-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>P: Dlaczego niektórzy użytkownicy nie pobierają odpowiedzi usługi Azure AD Multi-Factor Authentication na urządzeniach przyłączonych do usługi Azure AD?

Odp **.:** Użytkownik może dołączyć lub zarejestrować urządzenie w usłudze Azure AD przy użyciu Multi-Factor Authentication. Następnie samo urządzenie będzie zaufanym drugim czynnikiem dla tego użytkownika. Za każdym razem, gdy ten sam użytkownik loguje się na urządzeniu i uzyskuje dostęp do aplikacji, usługa Azure AD traktuje urządzenie jako drugi czynnik. Dzięki temu użytkownik może bezproblemowo uzyskiwać dostęp do aplikacji bez dodatkowych Multi-Factor Authenticationych. 

Takie zachowanie:

- Ma zastosowanie do przyłączonych do usługi Azure AD i zarejestrowanych urządzeń usługi Azure AD — ale nie dla urządzeń przyłączonych do hybrydowej usługi Azure AD.
- Nie dotyczy żadnych innych użytkowników, którzy logują się na tym urządzeniu. Wszyscy inni użytkownicy, którzy uzyskują dostęp do tego urządzenia, otrzymają wyzwanie Multi-Factor Authentication. Następnie mogą uzyskiwać dostęp do aplikacji, które wymagają Multi-Factor Authentication.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>P: Dlaczego otrzymuję *nazwę użytkownika lub hasło jest niepoprawny* dla urządzenia dołączonego do usługi Azure AD?

Odp **.:** Typowe przyczyny tego scenariusza są następujące:

- Twoje poświadczenia użytkownika nie są już prawidłowe.
- Komputer nie może komunikować się z Azure Active Directory. Sprawdź, czy występują problemy z łącznością sieciową.
- Logowania federacyjne wymagają, aby serwer federacyjny obsługiwał WS-Trust punkty końcowe, które są włączone i dostępne. 
- Włączono uwierzytelnianie przekazywane. Należy zmienić hasło tymczasowe po zalogowaniu się.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>P: Dlaczego widzę *... Wystąpił błąd* . Czy podczas próby dołączenia komputera do usługi Azure AD?

Odp **.:** Ten błąd występuje podczas konfigurowania Azure Active Directory rejestracji w usłudze Intune. Upewnij się, że użytkownik, który próbuje nawiązać połączenie z usługą Azure AD, ma przypisaną poprawną licencję usługi Intune. Aby uzyskać więcej informacji, zobacz [Konfigurowanie rejestracji dla urządzeń z systemem Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>P: Dlaczego moja próba dołączenia do usługi Azure AD kończy się niepowodzeniem, mimo że nie zostały wyświetlone żadne informacje o błędzie?

Odp **.:** Przyczyną jest to, że zalogowano się na urządzeniu przy użyciu lokalnego wbudowanego konta administratora. Przed rozpoczęciem instalacji Azure Active Directory należy utworzyć inne konto lokalne. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What to certyfikaty MS-Organization-P2P-Access obecne na naszych urządzeniach z systemem Windows 10?

Odp **.:** Certyfikaty MS-Organization-P2P-Access są wystawiane przez usługę Azure AD zarówno do usługi Azure AD, jak i hybrydowych urządzeń przyłączonych do usługi Azure AD. Te certyfikaty służą do włączania zaufania między urządzeniami w tej samej dzierżawie na potrzeby scenariuszy usług pulpitu zdalnego. Jeden certyfikat jest wystawiony dla urządzenia, a drugi jest wystawiony dla użytkownika. Certyfikat urządzenia jest obecny w usłudze `Local Computer\Personal\Certificates` i jest ważny przez jeden dzień. Ten certyfikat zostanie odnowiony (przez wystawienie nowego certyfikatu), jeśli urządzenie jest nadal aktywne w usłudze Azure AD. Certyfikat użytkownika jest obecny w programie `Current User\Personal\Certificates` i ten certyfikat jest również ważny przez jeden dzień, ale jest wystawiany na żądanie, gdy użytkownik próbuje wykonać sesję pulpitu zdalnego na innym urządzeniu przyłączonym do usługi Azure AD. Nie jest odnawiana po wygaśnięciu. Oba te certyfikaty są wystawiane przy użyciu certyfikatu MS-Organization-P2P-Access obecnego w `Local Computer\AAD Token Issuer\Certificates` . Ten certyfikat jest wystawiany przez usługę Azure AD podczas rejestracji urządzenia. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why mogę zobaczyć wiele wygasłych certyfikatów wystawionych przez firmę MS-Organization-P2P — dostęp na naszych urządzeniach z systemem Windows 10? Jak można je usunąć?

Odp **.:** Wystąpił problem opisany w systemie Windows 10 w wersji 1709 i niższym, gdzie wygasłe certyfikaty MS-Organization-P2P są nadal dostępne w magazynie komputer z powodu problemów kryptograficznych. Użytkownicy mogą mieć problemy z łącznością sieciową, jeśli używasz dowolnego klienta sieci VPN (na przykład Cisco AnyConnect), który nie może obsłużyć dużej liczby wygasłych certyfikatów. Ten problem został rozwiązany w wersji Windows 10 1803, aby automatycznie usunąć wszystkie takie wygasłe certyfikaty MS-Organization-P2P-Access. Możesz rozwiązać ten problem, aktualizując urządzenia do systemu Windows 10 1803. Jeśli nie możesz zaktualizować programu, możesz usunąć te certyfikaty bez żadnego niekorzystnego wpływu.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Sprzężenie hybrydowe usługi Azure AD — często zadawane pytania

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>P: Jak mogę odłączania hybrydowego urządzenia dołączonego do usługi Azure AD lokalnie na urządzeniu?

Odp **.:** W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD upewnij się, że funkcja automatycznej rejestracji została wyłączona. Następnie zaplanowane zadanie nie będzie ponownie rejestrować urządzenia. Następnie otwórz wiersz polecenia jako administrator i wprowadź `dsregcmd.exe /debug /leave` . Lub Uruchom to polecenie jako skrypt na kilku urządzeniach, aby rozłączyć zbiorczo.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>P: gdzie można znaleźć informacje dotyczące rozwiązywania problemów w celu zdiagnozowania niepowodzeń dołączania hybrydowej usługi Azure AD?

Odp **.:** Informacje dotyczące rozwiązywania problemów można znaleźć w następujących artykułach:

- [Rozwiązywanie problemów z Azure Active Directory hybrydowych dołączonych do urządzeń z systemami Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Rozwiązywanie problemów z Azure Active Directory hybrydowymi podłączonymi do urządzeń niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>P: Dlaczego widzę zduplikowany zarejestrowany rekord usługi Azure AD dla urządzenia dołączonego do hybrydowej usługi Azure AD systemu Windows 10 na liście urządzeń usługi Azure AD?

Odp **.:** Gdy użytkownicy dodają swoje konta do aplikacji na urządzeniu przyłączonym do domeny, może zostać wyświetlony monit o **dodanie konta do systemu Windows?** Jeśli w monicie wprowadzisz **wartość tak** , urządzenie zostanie zarejestrowane w usłudze Azure AD. Typ zaufania jest oznaczony jako zarejestrowany w usłudze Azure AD. Po włączeniu hybrydowego sprzężenia usługi Azure AD w organizacji urządzenie uzyskuje także przyłączoną do hybrydowej usługi Azure AD. Następnie dwa Stany urządzeń są wyświetlane dla tego samego urządzenia. 

Dołączanie hybrydowej usługi Azure AD ma pierwszeństwo przed zarejestrowanym stanem usługi Azure AD. Dlatego Twoje urządzenie jest uznawane za dołączoną hybrydową usługę Azure AD na potrzeby oceny uwierzytelniania i dostępu warunkowego. Możesz bezpiecznie usunąć zarejestrowany rekord urządzenia usługi Azure AD z portalu usługi Azure AD. Zapoznaj się z tym, aby [uniknąć tego podwójnego stanu na komputerze z systemem Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>P: Dlaczego mój użytkownik ma problemy z systemem Windows 10 hybrydowe urządzenia dołączone do usługi Azure AD po zmianie nazwy UPN?

Odp **.:** Obecnie zmiany nazw UPN nie są w pełni obsługiwane w przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD. Użytkownicy mogą logować się na urządzeniu i uzyskiwać dostęp do aplikacji lokalnych, więc uwierzytelnianie za pomocą usługi Azure AD kończy się niepowodzeniem po zmianie nazwy UPN. W związku z tym użytkownicy mają problemy z logowaniem jednokrotnym i dostępem warunkowym na swoich urządzeniach. W tej chwili musisz odłączyć urządzenie od usługi Azure AD (Uruchom polecenie "dsregcmd/Leave" z podniesionymi uprawnieniami) i ponownie Dołącz (automatycznie), aby rozwiązać ten problem. Obecnie pracujemy nad tym problemem. Jednak użytkownicy logujący się przy użyciu usługi Windows Hello dla firm nie będą napotykać tego problemu. 

Zmiany nazw UPN są obsługiwane w ramach aktualizacji systemu Windows 10 2004. Użytkownicy na urządzeniach z tą aktualizacją nie będą mieli żadnych problemów po zmianie ich nazw UPN

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>P: czy hybrydowe urządzenia z systemem Windows 10 dołączone do usługi Azure AD wymagają linii wglądu do kontrolera domeny w celu uzyskania dostępu do zasobów w chmurze?

Odp **.:** Nie, z wyjątkiem sytuacji, gdy hasło użytkownika zostanie zmienione. Po zakończeniu dołączania hybrydowego usługi Azure AD w systemie Windows 10, gdy użytkownik zalogował się co najmniej raz, urządzenie nie będzie wymagało dostępu do zasobów w chmurze do kontrolera domeny. System Windows 10 może pobrać Logowanie jednokrotne do aplikacji usługi Azure AD z dowolnego miejsca i z połączeniem internetowym, z wyjątkiem sytuacji, gdy hasło zostanie zmienione. Użytkownicy logujący się przy użyciu funkcji Windows Hello dla firm będą nadal korzystać z funkcji logowania jednokrotnego do aplikacji usługi Azure AD nawet po zmianie hasła, nawet jeśli nie mają wglądu w dane kontrolera domeny. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>P: co się stanie, jeśli użytkownik zmieni hasło i podejmie próbę zalogowania się do swojego urządzenia dołączonego do hybrydowej usługi Azure AD systemu Windows 10 poza siecią firmową?

Odp **.:** Jeśli hasło zostanie zmienione poza siecią firmową (na przykład za pomocą usługi Azure AD SSPR), użytkownik zaloguje się przy użyciu nowego hasła zakończy się niepowodzeniem. W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD jest to główny urząd Active Directory. Jeśli urządzenie nie ma linii wglądu do kontrolera domeny, nie można zweryfikować nowego hasła. Dlatego użytkownik musi nawiązać połączenie z kontrolerem domeny (za pośrednictwem sieci VPN lub w sieci firmowej), zanim będzie mógł zalogować się do urządzenia przy użyciu nowego hasła. W przeciwnym razie może zalogować się tylko przy użyciu starego hasła ze względu na możliwość logowania w systemie Windows. Jednak stare hasło jest unieważnione przez usługę Azure AD podczas żądań tokenów, a tym samym uniemożliwia logowanie jednokrotne i niepowodzenie wszystkich zasad dostępu warunkowego opartego na urządzeniach. Ten problem nie występuje, jeśli używasz usługi Windows Hello dla firm. 

---

## <a name="azure-ad-register-faq"></a>Rejestracja w usłudze Azure AD — często zadawane pytania

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>P: Jak mogę usunąć zarejestrowany stan usługi Azure AD na potrzeby lokalnego urządzenia?

**Z** 
- W przypadku zarejestrowanych urządzeń z systemem Windows 10 w usłudze Azure AD przejdź do pozycji **Ustawienia**  >  **konta**  >  **dostęp do zasobów służbowych**. Wybierz swoje konto i wybierz pozycję **Rozłącz**. Rejestracja urządzenia dotyczy profilu użytkownika w systemie Windows 10.
- W przypadku systemów iOS i Android można użyć Microsoft Authenticator   >  **rejestracji urządzeń** ustawień aplikacji i wybrać pozycję **Wyrejestruj urządzenie**.
- W przypadku macOS można użyć aplikacji Portal firmy Microsoft Intune, aby wyrejestrować urządzenie z zarządzania i usunąć wszelkie rejestracje. 

W przypadku urządzeń z systemem Windows 10 proces ten można zautomatyzować za pomocą [Narzędzia do usuwania Workplace Join (WPJ)](https://download.microsoft.com/download/8/e/f/8ef13ae0-6aa8-48a2-8697-5b1711134730/WPJCleanUp.zip)

> [!NOTE]
> To narzędzie usuwa wszystkie konta logowania jednokrotnego na urządzeniu. Po wykonaniu tej operacji wszystkie aplikacje utracą stan logowania jednokrotnego, a urządzenie zostanie wyrejestrowane z poziomu narzędzia do zarządzania (MDM) i wyrejestrowania z chmury. Przy następnym próbie zalogowania się aplikacji zostanie wyświetlony monit o ponowne dodanie konta.

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>P: jak zablokować użytkownikom możliwość dodawania dodatkowych kont służbowych (zarejestrowanych w usłudze Azure AD) na urządzeniach z systemem Windows 10 firmowych?

Odp **.:** Włącz następujący rejestr, aby uniemożliwić użytkownikom dodawanie dodatkowych kont służbowych do dołączanej domeny firmowej, przyłączonej do usługi Azure AD lub do hybrydowych urządzeń z systemem Windows 10 dołączonych do usługi Azure AD. Te zasady mogą również służyć do blokowania nieumyślnego uzyskiwania usługi Azure AD zarejestrowane przy użyciu tego samego konta użytkownika przez maszyny przyłączone do domeny. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>P: Czy można rejestrować urządzenia z systemem Android lub iOS BYOD?

Odp **.:** Tak, ale tylko w przypadku usługi rejestracji urządzeń platformy Azure i klientów hybrydowych. Usługa rejestracji urządzeń lokalnych w Active Directory Federation Services (AD FS) nie jest obsługiwana.

---
### <a name="q-how-can-i-register-a-macos-device"></a>P: Jak mogę zarejestrować urządzenie macOS?

Odp **.:** Wykonaj następujące czynności:

1.    [Tworzenie zasad zgodności](/intune/compliance-policy-create-mac-os)
1.    [Definiowanie zasad dostępu warunkowego dla urządzeń macOS](../conditional-access/overview.md) 

**Uwagi**

- Użytkownicy włączeni do zasad dostępu warunkowego potrzebują [obsługiwanej wersji pakietu Office dla programu macOS](../conditional-access/concept-conditional-access-conditions.md) w celu uzyskania dostępu do zasobów. 
- Podczas pierwszego dostępu spróbuj użyć monitu o zarejestrowanie urządzenia przy użyciu portalu firmy.

---
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zarejestrowanych urządzeniach usługi Azure AD](concept-azure-ad-register.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do usługi Azure AD](concept-azure-ad-join.md)
- Dowiedz się więcej o [urządzeniach przyłączonych do hybrydowej usługi Azure AD](concept-azure-ad-join-hybrid.md)
