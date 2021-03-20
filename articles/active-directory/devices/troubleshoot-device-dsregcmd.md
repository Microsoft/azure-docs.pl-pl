---
title: Rozwiązywanie problemów przy użyciu polecenia dsregcmd — Azure Active Directory
description: Użycie danych wyjściowych z dsregcmd do zrozumienia stanu urządzeń w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614b3f927dc22bc534168f8fe95cc2f97031b621
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98725527"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd

Narzędzie dsregcmd/status musi być uruchamiane jako konto użytkownika domeny.

## <a name="device-state"></a>Stan urządzenia

Ta sekcja zawiera listę parametrów stanu przyłączania urządzenia. W poniższej tabeli przedstawiono kryteria dla urządzenia w różnych stanach sprzężenia.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Stan urządzenia |
| ---   | ---   | ---   | ---   |
| TAK | NO | NO | Przyłączone do usługi Azure AD |
| NO | NO | TAK | Przyłączone do domeny |
| TAK | NO | TAK | Dołączono do hybrydowej usługi AD |
| NO | TAK | TAK | Lokalne DRS dołączone |

> [!NOTE]
> W sekcji "stan użytkownika" jest wyświetlany stan Workplace Join (zarejestrowane w usłudze Azure AD)

- **AzureAdJoined:** -ustaw na wartość "tak", jeśli urządzenie jest przyłączone do usługi Azure AD. "Nie" w przeciwnym razie.
- **EnterpriseJoined:** -ustaw na wartość "yes", jeśli urządzenie jest przyłączone do lokalnego DRS. Urządzenie nie może być zarówno EnterpriseJoined, jak i AzureAdJoined.
- **DomainJoined:** -ustaw na wartość "tak", jeśli urządzenie jest przyłączone do domeny (AD).
- **Nazwa_domeny:** -ustawiana jako nazwa domeny, jeśli urządzenie jest przyłączone do domeny.

### <a name="sample-device-state-output"></a>Przykładowe dane wyjściowe stanu urządzenia

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Szczegóły urządzenia

Wyświetlane tylko wtedy, gdy urządzenie jest dołączone do usługi Azure AD lub dołączono hybrydową usługę Azure AD (nie zarejestrowano usługi Azure AD). Ta sekcja zawiera informacje o urządzeniach identyfikujących dane przechowywane w chmurze.

- **DeviceID:** — unikatowy identyfikator urządzenia w dzierżawie usługi Azure AD
- **Odcisk palca:** -odcisk palca certyfikatu urządzenia 
- **DeviceCertificateValidity:** — ważność certyfikatu urządzenia
- **KeyContainerId:** -ContainerId klucza prywatnego urządzenia skojarzonego z certyfikatem urządzenia
- Kluczowy **:** -Key (sprzęt/oprogramowanie) służący do przechowywania klucza prywatnego urządzenia.
- **TpmProtected:** -"yes", jeśli klucz prywatny urządzenia jest przechowywany w SPRZĘTOWYM module TPM.

### <a name="sample-device-details-output"></a>Przykładowe dane wyjściowe szczegółów urządzenia

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Szczegóły dzierżawy

Wyświetlane tylko wtedy, gdy urządzenie jest dołączone do usługi Azure AD lub dołączono hybrydową usługę Azure AD (nie zarejestrowano usługi Azure AD). Ta sekcja zawiera szczegółowe informacje o typowych dzierżawach, gdy urządzenie jest przyłączone do usługi Azure AD.

> [!NOTE]
> Jeśli adresy URL zarządzania urządzeniami przenośnymi w tej sekcji są puste, oznacza to, że zarządzanie urządzeniami przenośnymi nie zostało skonfigurowane lub bieżący użytkownik nie jest objęty zakresem rejestracji w usłudze MDM. Sprawdź ustawienia mobilności w usłudze Azure AD, aby przejrzeć konfigurację usługi MDM.

> [!NOTE]
> Nawet jeśli widzisz adresy URL MDM, nie oznacza to, że urządzenie jest zarządzane przez MDM. Informacje są wyświetlane, jeśli dzierżawa ma konfigurację MDM na potrzeby automatycznego rejestrowania, nawet jeśli samo urządzenie nie jest zarządzane. 

### <a name="sample-tenant-details-output"></a>Przykładowe dane wyjściowe szczegółowej dzierżawy

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Stan użytkownika

Ta sekcja zawiera listę stanów różnych atrybutów dla użytkownika aktualnie zalogowanego na urządzeniu.

> [!NOTE]
> Polecenie musi zostać uruchomione w kontekście użytkownika, aby można było pobrać prawidłowy stan.

- **NgcSet:** -ustaw wartość "yes" (tak), jeśli ustawiono klucz powitalny systemu Windows dla aktualnie zalogowanego użytkownika.
- **NgcKeyId:** -ID klucza Hello systemu Windows, jeśli jest ustawiony dla bieżącego zalogowanego użytkownika.
- **Resetowanie:** -wskazuje, czy klucz powitalny systemu Windows może być resetowany przez użytkownika. 
- **Możliwe wartości:** -DestructiveOnly, NonDestructiveOnly, DestructiveAndNonDestructive lub nieznany, jeśli wystąpi błąd. 
- **WorkplaceJoined:** -ustaw wartość "tak", jeśli zarejestrowane konta usługi Azure AD zostały dodane do urządzenia w bieżącym kontekście ntuser.
- **WamDefaultSet:** -ustaw na wartość "yes", jeśli dla zalogowanego użytkownika zostanie utworzone domyślne konto webaccount. W tym polu może być wyświetlany komunikat o błędzie, jeśli dsreg/status jest uruchamiany z wiersza polecenia z podwyższonym poziomem uprawnień. 
- **WamDefaultAuthority:** -jako "organizacje" dla usługi Azure AD.
- **WamDefaultId:** -Always " https://login.microsoft.com " dla usługi Azure AD.
- **WamDefaultGUID:** — identyfikator GUID dostawcy wam (Azure AD/konto Microsoft) dla domyślnego konta Web. 

### <a name="sample-user-state-output"></a>Dane wyjściowe przykładowego stanu użytkownika

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>Stan logowania jednokrotnego

Ta sekcja może być ignorowana w przypadku urządzeń zarejestrowanych w usłudze Azure AD.

> [!NOTE]
> Polecenie musi zostać uruchomione w kontekście użytkownika, aby można było pobrać prawidłowy stan dla tego użytkownika.

- **AzureAdPrt:** -ustaw na wartość "yes", jeśli na urządzeniu znajduje się PRT zalogowanego użytkownika.
- **AzureAdPrtUpdateTime:** -ustawiono godzinę w formacie UTC ostatniej aktualizacji PRT.
- **AzureAdPrtExpiryTime:** -Ustaw czas w formacie UTC, gdy PRT ma wygasnąć, jeśli nie zostanie odnowiony.
- **AzureAdPrtAuthority:** — adres URL urzędu usługi Azure AD
- **EnterprisePrt:** -ustaw wartość "yes" (tak), jeśli urządzenie ma PRT z lokalnych usług AD FS. W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD urządzenie może mieć jednocześnie PRT z usługi Azure AD i lokalnej usługi AD. Lokalne urządzenia przyłączone będą mieć tylko PRT Enterprise.
- **EnterprisePrtUpdateTime:** -ustawiono godzinę w formacie UTC ostatniej aktualizacji przedsiębiorstwa PRT.
- **EnterprisePrtExpiryTime:** -Ustaw czas w formacie UTC, gdy PRT ma wygasnąć, jeśli nie zostanie odnowiony.
- **EnterprisePrtAuthority:** — adres URL urzędu ADFS

### <a name="sample-sso-state-output"></a>Przykład danych wyjściowych stanu logowania jednokrotnego

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2019-01-24 19:15:26.000 UTC
      AzureAdPrtExpiryTime : 2019-02-07 19:15:26.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostic-data"></a>Dane diagnostyczne

### <a name="pre-join-diagnostics"></a>Diagnostyka przed dołączeniem

Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może wykonać hybrydowego sprzężenia usługi Azure AD.

Ta sekcja wykonuje różne testy ułatwiające zdiagnozowanie błędów sprzężeń. Ta sekcja zawiera również szczegóły poprzedniej (?). Te informacje obejmują fazę błędu, kod błędu, identyfikator żądania serwera, stan HTTP odpowiedzi serwera, komunikat o błędzie odpowiedzi serwera.

- **Kontekst użytkownika:** — kontekst, w którym uruchomiono diagnostykę. Możliwe wartości: SYSTEM, niepodwyższony poziom użytkownika, PODWYŻSZONy użytkownik. 

   > [!NOTE]
   > Ponieważ rzeczywiste sprzężenie jest wykonywane w kontekście systemu, uruchomienie diagnostyki w kontekście systemu jest najbliżej rzeczywistego scenariusza sprzężenia. Aby uruchomić diagnostykę w kontekście systemu, polecenie dsregcmd/status musi być uruchamiane z wiersza polecenia z podwyższonym poziomem uprawnień.

- **Czas klienta:** — czas systemowy w formacie UTC.
- **Test łączności z usługą AD:** -test wykonuje test łączności z kontrolerem domeny. Błąd w tym teście prawdopodobnie spowoduje błędy sprzężenia w fazie wstępnego sprawdzania.
- **Test konfiguracji usługi AD:** -test odczytuje i weryfikuje, czy obiekt SCP jest prawidłowo skonfigurowany w lokalnym lesie usługi AD. Błędy w tym teście prawdopodobnie spowodują błędy sprzężenia w fazie odnajdywania z kodem błędu 0x801c001d.
- **Test odnajdywania DRS:** -test pobiera punkty końcowe DRS z punktu końcowego metadanych odnajdywania i wykonuje żądanie obszaru użytkownika. Błędy w tym teście prawdopodobnie spowodują błędy sprzężenia w fazie odnajdywania.
- **Test łączności DRS:** -test wykonuje Basic test łączności do punktu końcowego DRS.
- **Test pozyskiwania tokenu:** -test próbuje uzyskać token uwierzytelniania usługi Azure AD, Jeśli dzierżawca użytkownika jest federacyjny. Błędy w tym teście prawdopodobnie spowodują błędy sprzężenia w fazie uwierzytelniania. Jeśli uwierzytelnianie nie powiedzie się, zostanie podjęta próba połączenia z funkcją powrotu, chyba że rezerwowa zostanie jawnie wyłączona przy użyciu poniższych ustawień klucza rejestru.
```
    Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
    Value: FallbackToSyncJoin
    Type:  REG_DWORD
    Value: 0x0 -> Disabled
    Value: 0x1 -> Enabled
    Default (No Key): Enabled
 ```
- **Powrót do synchronizacji-Join:** -ustaw wartość "Enabled" (włączone), Jeśli powyższy klucz rejestru, aby zapobiec dołączeniu powrotu do synchronizacji z błędami uwierzytelniania, nie jest obecny. Ta opcja jest dostępna w systemie Windows 10 1803 i nowszych.
- **Poprzednia rejestracja:** godzina poprzednia próba dołączenia. Rejestrowane są tylko nieudane próby dołączenia.
- **Faza błędu:** -etap dołączania, w którym został przerwany. Możliwe wartości to wstępne sprawdzanie, odnajdowanie, uwierzytelnianie, połączenie.
- **Kod błędu klienta:** -zwrócony kod błędu klienta (HRESULT).
- Kod błędu **serwera:** -serwer o kodzie, jeśli żądanie zostało wysłane do serwera, a serwer odpowiedział z kodem błędu. 
- **Komunikat serwera:** — zwrócony komunikat serwera wraz z kodem błędu.
- **Stan protokołu https:** — stan http zwrócony przez serwer.
- **Identyfikator żądania:-żądanie** klienta wysłane do serwera. Przydatne do skorelowania z dziennikami po stronie serwera.

### <a name="sample-pre-join-diagnostics-output"></a>Przykładowe dane wyjściowe diagnostyki przed dołączeniem

Poniższy przykład pokazuje, że test diagnostyczny kończy się niepowodzeniem z błędem odnajdywania.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

W poniższym przykładzie przedstawiono testy diagnostyczne przekazywane, ale próba rejestracji nie powiodła się z powodu błędu katalogu, który jest oczekiwany dla sprzężenia synchronizacji. Po zakończeniu zadania synchronizacji Azure AD Connect urządzenie będzie mogło nawiązać połączenie.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnostyka po przyłączeniu

W tej sekcji przedstawiono dane wyjściowe testów Sanity wykonanych na urządzeniu przyłączonym do chmury.

- **AadRecoveryEnabled:** — Jeśli tak, klucze przechowywane na urządzeniu nie są używane, a urządzenie jest oznaczone do odzyskania. Następne logowanie spowoduje wyzwolenie przepływu odzyskiwania i ponowne zarejestrowanie urządzenia.
- **KeySignTest:** -IF "Passed" klucze urządzeń są w dobrej kondycji. Jeśli KeySignTest nie powiedzie się, urządzenie będzie zazwyczaj oznaczone do odzyskania. Następne logowanie spowoduje wyzwolenie przepływu odzyskiwania i ponowne zarejestrowanie urządzenia. W przypadku urządzeń przyłączonych do hybrydowej usługi Azure AD odzyskiwanie jest dyskretne. Gdy usługa Azure AD przyłączona lub zarejestrowano usługę Azure AD, urządzenia wyświetlą monit o uwierzytelnienie użytkownika w celu odzyskania i ponownego zarejestrowania urządzenia w razie potrzeby. **KeySignTest wymaga podniesionych uprawnień.**

#### <a name="sample-post-join-diagnostics-output"></a>Przykładowe dane wyjściowe diagnostyki po dołączeniu

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Sprawdzanie wymagań wstępnych NGC

Ta sekcja wykonuje sprawdzanie wymagań wstępnych dotyczących aprowizacji usługi Windows Hello dla firm (WHFB). 

> [!NOTE]
> Szczegóły sprawdzania wymagań wstępnych NGC w dsregcmd/status mogą nie być widoczne, jeśli użytkownik pomyślnie skonfigurował już WHFB.

- **IsDeviceJoined:** -ustaw na wartość "tak", jeśli urządzenie jest przyłączone do usługi Azure AD.
- **IsUserAzureAD:** -ustaw na wartość "tak", Jeśli zalogowany użytkownik jest obecny w usłudze Azure AD.
- **PolicyEnabled:** -ustaw na wartość "yes", jeśli na urządzeniu włączono zasady WHFB.
- **PostLogonEnabled:** -ustaw na wartość "yes", jeśli rejestracja WHFB jest natywnie wywoływana przez platformę. Jeśli jest ustawiona na wartość "nie", oznacza to, że rejestracja w usłudze Windows Hello dla firm jest wyzwalana przez mechanizm niestandardowy
- **DeviceEligible:** -ustaw wartość "yes" (tak), jeśli urządzenie spełnia wymagania sprzętowe wymagane do zarejestrowania się w usłudze WHFB.
- **SessionIsNotRemote:** -ustaw na wartość "tak", jeśli bieżący użytkownik jest zalogowany bezpośrednio na urządzeniu, a nie zdalnie.
- **CertEnrollment:** -specyficzne dla WHFB wdrożenie zaufania certyfikatu wskazujące urząd rejestracji certyfikatu dla WHFB. Ustaw wartość "urząd rejestracji", jeśli źródło zasad WHFB jest zasady grupy "Zarządzanie urządzeniami przenośnymi", jeśli źródłem jest MDM. "Brak" w przeciwnym razie
- **AdfsRefreshToken:** — specyficzne dla wdrożenia zaufania certyfikatu WHFB. Występuje tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Wskazuje, czy urządzenie ma PRT Enterprise dla użytkownika.
- **AdfsRaIsReady:** — specyficzne dla wdrożenia zaufania certyfikatu WHFB.  Występuje tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Ustaw wartość "YES" (tak), jeśli w metadanych odnajdowania określono obsługę WHFB *oraz* szablon certyfikatu logowania.
- **LogonCertTemplateReady:** — specyficzne dla wdrożenia zaufania certyfikatu WHFB. Występuje tylko wtedy, gdy CertEnrollment jest "urząd rejestracji". Ustaw wartość "tak", jeśli stan szablonu certyfikatu logowania jest prawidłowy i pomaga w rozwiązywaniu problemów z urzędem rejestrowania usług AD FS.
- **PreReqResult:** — zapewnia wynik całej oceny wymagań wstępnych WHFB. Ustawienie "przyinicjuje obsługę", jeśli rejestracja WHFB będzie uruchamiana jako zadanie po zalogowaniu, gdy użytkownik zaloguje się w następnym czasie.

### <a name="sample-ngc-prerequisite-check-output"></a>Przykładowe dane wyjściowe sprawdzania wymagań wstępnych NGC

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Następne kroki

Pytania można znaleźć w temacie [często zadawane pytania dotyczące zarządzania urządzeniami](faq.md)
