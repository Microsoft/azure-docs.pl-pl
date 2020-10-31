---
title: Rozwiązywanie problemów dotyczących urządzeń dołączonych hybrydowo do usługi Azure Active Directory
description: Rozwiązywanie problemów hybrydowych Azure Active Directory dołączonych do urządzeń z systemami Windows 10 i Windows Server 2016.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: e235bf90568a1382a5ecee3ff4d2283aaa32f10b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083220"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-devices"></a>Rozwiązywanie problemów dotyczących urządzeń dołączonych hybrydowo do usługi Azure Active Directory

Zawartość tego artykułu dotyczy urządzeń z systemem Windows 10 lub Windows Server 2016.

W przypadku innych klientów z systemem Windows Zapoznaj się z artykułem [Rozwiązywanie problemów z Azure Active Directory hybrydowych podłączonych do urządzeń niższego poziomu](troubleshoot-hybrid-join-windows-legacy.md)

W tym artykule przyjęto założenie, że [skonfigurowano urządzenia hybrydowe Azure Active Directory dołączone](hybrid-azuread-join-plan.md) do obsługi następujących scenariuszy:

- Dostęp warunkowy oparty na urządzeniach
- [Roaming ustawień przedsiębiorstwa](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)

Ten dokument zawiera wskazówki dotyczące rozwiązywania problemów w celu rozwiązania potencjalnych problemów.

W przypadku systemów Windows 10 i Windows Server 2016 sprzężenie hybrydowe Azure Active Directory obsługuje aktualizację systemu Windows 10 listopad 2015 lub nowszą.

## <a name="troubleshoot-join-failures"></a>Rozwiązywanie problemów z błędami sprzężeń

### <a name="step-1-retrieve-the-join-status"></a>Krok 1. Pobieranie stanu sprzężenia

**Aby pobrać stan sprzężenia:**

1. Otwórz wiersz polecenia jako administrator
2. Wpisz `dsregcmd /status`

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Krok 2. oszacowanie stanu sprzężenia

Przejrzyj poniższe pola i upewnij się, że mają one oczekiwane wartości:

#### <a name="domainjoined--yes"></a>DomainJoined: tak

To pole wskazuje, czy urządzenie jest przyłączone do lokalnego Active Directory, czy nie. Jeśli wartość **nie** jest równa, urządzenie nie może wykonać sprzężenia hybrydowego usługi Azure AD.

#### <a name="workplacejoined--no"></a>WorkplaceJoined: nie

To pole wskazuje, czy urządzenie jest zarejestrowane w usłudze Azure AD jako urządzenie osobiste (oznaczone jako *dołączone do miejsca pracy* ). Ta wartość powinna być **nie** dla komputera przyłączonego do domeny, który jest również przyłączony do hybrydowej usługi Azure AD. Jeśli wartość to **tak** , konto służbowe zostało dodane przed ukończeniem hybrydowego sprzężenia usługi Azure AD. W takim przypadku konto jest ignorowane w przypadku korzystania z systemu Windows 10 w wersji 1607 lub nowszej.

#### <a name="azureadjoined--yes"></a>AzureAdJoined: tak

To pole wskazuje, czy urządzenie jest przyłączone. Ta wartość będzie równa **tak** , jeśli urządzenie jest przyłączone do usługi Azure AD lub do hybrydowego urządzenia dołączonego do usługi Azure AD.
Jeśli wartość **nie** jest, przyłączenie do usługi Azure AD nie zostało jeszcze ukończone.

Przejdź do następnych kroków w celu dalszego rozwiązywania problemów.

### <a name="step-3-find-the-phase-in-which-join-failed-and-the-errorcode"></a>Krok 3. znalezienie fazy, w której dołączenie nie powiodło się i kod błędu

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i nowsze

Wyszukaj podsekcję "poprzednia rejestracja" w sekcji "dane diagnostyczne" danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może wykonać hybrydowego sprzężenia usługi Azure AD.
W polu "faza błędu" jest wskazywana faza błędu sprzężenia, podczas gdy "kod błędu klienta" oznacza kod błędu operacji JOIN.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

#### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podgląd zdarzeń, aby zlokalizować fazę i kod błędu dla błędów sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzeń użytkowników** w Podglądzie zdarzeń. Znajduje się w obszarze **Dziennik aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **rejestracja urządzeń użytkowników** w systemie Microsoft Windows
2. Poszukaj zdarzeń o następujących eventIDs 304, 305, 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Zrzut ekranu przedstawiający Podgląd zdarzeń. Jest zaznaczone zdarzenie z opcją I D 304, a jego informacje są wyświetlane z wyróżnionym kodem błędu i etapem." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Zrzut ekranu przedstawiający Podgląd zdarzeń. Jest zaznaczone zdarzenie z opcją I D 304, a jego informacje są wyświetlane z wyróżnionym kodem błędu i etapem." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions-from-the-lists-below"></a>Krok 4. Sprawdź możliwe przyczyny i rozwiązania z poniższych list

#### <a name="pre-check-phase"></a>Faza przed sprawdzeniem

Możliwe przyczyny niepowodzenia:

- Urządzenie nie ma wglądu w szczegółowe dane kontrolera domeny.
   - Urządzenie musi znajdować się w sieci wewnętrznej organizacji lub w sieci VPN z obsługą sieci na lokalnym kontrolerze domeny Active Directory (AD).

#### <a name="discover-phase"></a>Faza odnajdywania

Możliwe przyczyny niepowodzenia:

- Obiekt punktu połączenia z usługą został niepoprawnie skonfigurowany/nie można odczytać obiektu SCP z kontrolera domeny.
   - Wymagany jest prawidłowy obiekt SCP w lesie usługi AD, do którego należy urządzenie, wskazujący na zweryfikowaną nazwę domeny w usłudze Azure AD.
   - Szczegółowe informacje znajdują się w sekcji [konfigurowanie punktu połączenia z usługą](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- Nie można nawiązać połączenia i pobrać metadanych odnajdywania z punktu końcowego odnajdywania.
   - Urządzenie powinno mieć dostęp `https://enterpriseregistration.windows.net` w kontekście systemu w celu odnajdywania punktów końcowych rejestracji i autoryzacji.
   - Jeśli środowisko lokalne wymaga serwera proxy wychodzącego, administrator IT musi upewnić się, że konto komputera urządzenia może odnajdywać i dyskretnie uwierzytelniać się na wychodzącym serwerze proxy.
- Nie można połączyć się z punktem końcowym obszaru użytkownika i przeprowadzić odnajdywanie obszaru. (Tylko system Windows 10 w wersji 1809 lub nowszej)
   - Urządzenie powinno mieć dostęp `https://login.microsoftonline.com` w kontekście systemu, aby przeprowadzić odnajdywanie obszarów dla zweryfikowanej domeny i określić typ domeny (zarządzany/federacyjny).
   - Jeśli środowisko lokalne wymaga serwera proxy wychodzącego, administrator IT musi upewnić się, że kontekst systemu na urządzeniu może odnajdywać i dyskretnie uwierzytelniać się na wychodzącym serwerze proxy.

**Typowe kody błędów:**

- **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611)
   - Przyczyna: nie można odczytać obiektu SCP i uzyskać informacji o dzierżawie usługi Azure AD.
   - Rozwiązanie: Zapoznaj się z sekcją [konfigurowanie punktu połączenia z usługą](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join).
- **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607)
   - Przyczyna: błąd wykrywania ogólnego. Nie można pobrać metadanych odnajdywania z DRS.
   - Rozwiązanie: Znajdź poniżej Poniższy błąd, aby dokładniej zbadać.
- **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609)
   - Przyczyna: upłynął limit czasu operacji podczas odnajdywania.
   - Rozwiązanie: Upewnij się, że `https://enterpriseregistration.windows.net` jest dostępny w kontekście systemu. Aby uzyskać więcej informacji, zapoznaj się z sekcją [wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c0021/-2145648611)
   - Przyczyna: Ogólna awaria odnajdywania obszaru. Nie można określić typu domeny (zarządzane/federacyjne) z usługi STS.
   - Rozwiązanie: Znajdź poniżej Poniższy błąd, aby dokładniej zbadać.

**Popularne kody błędów:**

Aby znaleźć kod błędu dla kodu błędu odnajdowania, należy użyć jednej z następujących metod.

##### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i nowsze

Wyszukaj "DRS Discovery test" w sekcji "dane diagnostyczne" w danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może wykonać hybrydowego sprzężenia usługi Azure AD.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podgląd zdarzeń, aby zlokalizować fazę i kod błędu dla błędów sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzeń użytkowników** w Podglądzie zdarzeń. Znajduje się w obszarze **Dziennik aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **rejestracja urządzeń użytkowników** w systemie Microsoft Windows
2. Wyszukaj zdarzenia z następującym eventIDs 201

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Zrzut ekranu przedstawiający Podgląd zdarzeń. Jest zaznaczone zdarzenie z opcją I D 304, a jego informacje są wyświetlane z wyróżnionym kodem błędu i etapem." border="false":::

###### <a name="network-errors"></a>Błędy sieci

- **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867)
   - Przyczyna: nie można nawiązać połączenia z serwerem
   - Rozwiązanie: zapewnianie łączności sieciowej z wymaganymi zasobami firmy Microsoft. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Przyczyna: ogólny limit czasu sieci.
   - Rozwiązanie: zapewnianie łączności sieciowej z wymaganymi zasobami firmy Microsoft. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721)
   - Przyczyna: stos sieciowy nie może zdekodować odpowiedzi z serwera.
   - Rozwiązanie: Upewnij się, że serwer proxy sieci nie zakłóca i nie modyfikuje odpowiedzi serwera.

###### <a name="http-errors"></a>Błędy HTTP

- **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582)
   - Przyczyna: obiekt SCP został skonfigurowany z nieprawidłowym IDENTYFIKATORem dzierżawy. Nie znaleziono aktywnych subskrypcji w dzierżawie.
   - Rozwiązanie: Upewnij się, że obiekt SCP został skonfigurowany z prawidłowym IDENTYFIKATORem dzierżawy usługi Azure AD i aktywnymi subskrypcjami lub obecnymi w dzierżawie.
- **DSREG_SERVER_BUSY** (0x801c0025/-2145648603)
   - Przyczyna: HTTP 503 z serwera DRS.
   - Rozwiązanie: serwer jest obecnie niedostępny. przyszłe próby dołączenia prawdopodobnie zakończą się powodzeniem po ponownym uruchomieniu serwera w trybie online.

###### <a name="other-errors"></a>Inne błędy

- **E_INVALIDDATA** (0x8007000D/-2147024883)
   - Przyczyna: nie można przeanalizować pliku JSON odpowiedzi serwera. Prawdopodobnie z powodu powrotu serwera proxy HTTP 200 przy użyciu strony uwierzytelniania HTML.
   - Rozwiązanie: Jeśli środowisko lokalne wymaga serwera proxy wychodzącego, administrator IT musi upewnić się, że kontekst systemu na urządzeniu może odnajdywać i dyskretnie uwierzytelniać się na wychodzącym serwerze proxy.

#### <a name="authentication-phase"></a>Faza uwierzytelniania

Dotyczy tylko kont domeny federacyjnej.

Przyczyny niepowodzenia:

- Nie można uzyskać tokenu dostępu w trybie dyskretnym dla zasobu DRS.
   - Urządzenia z systemem Windows 10 uzyskują token uwierzytelniania z usługi federacyjnej przy użyciu zintegrowanego uwierzytelniania systemu Windows w aktywnym punkcie końcowym WS-Trust. Szczegóły: [konfiguracja usługa federacyjna](hybrid-azuread-join-manual.md#set-up-issuance-of-claims)

**Typowe kody błędów:**

Użyj dzienników Podgląd zdarzeń, aby znaleźć kod błędu, kod błędu, kod błędu serwera i komunikat o błędzie serwera.

1. Otwórz dzienniki zdarzeń **rejestracji urządzeń użytkowników** w Podglądzie zdarzeń. Znajduje się w obszarze **Dziennik aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **rejestracja urządzeń użytkowników** w systemie Microsoft Windows
2. Wyszukaj zdarzenia z następującym eventID 305

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Zrzut ekranu przedstawiający Podgląd zdarzeń. Jest zaznaczone zdarzenie z opcją I D 304, a jego informacje są wyświetlane z wyróżnionym kodem błędu i etapem." border="false":::

##### <a name="configuration-errors"></a>Błędy konfiguracji

- **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057)
   - Przyczyna: protokół uwierzytelniania nie jest protokołem WS-Trust.
   - Rozwiązanie: lokalny dostawca tożsamości musi obsługiwać WS-Trust
- **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036)
   - Przyczyna: lokalna usługa federacyjna nie zwróciła odpowiedzi XML.
   - Rozwiązanie: Upewnij się, że punkt końcowy MEX zwraca prawidłowy kod XML. Upewnij się, że serwer proxy nie zakłóca i nie zwraca odpowiedzi z nieprawidłowym kodem XML.
- **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045)
   - Przyczyna: nie można odnaleźć punktu końcowego na potrzeby uwierzytelniania nazwy użytkownika/hasła.
   - Rozwiązanie: Sprawdź ustawienia lokalnego dostawcy tożsamości. Upewnij się, że WS-Trust punkty końcowe są włączone i upewnij się, że odpowiedź MEX zawiera te poprawne punkty końcowe.

##### <a name="network-errors"></a>Błędy sieci

- **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614)
   - Przyczyna: ogólny limit czasu sieci.
   - Rozwiązanie: Upewnij się, że `https://login.microsoftonline.com` jest dostępny w kontekście systemu. Upewnij się, że lokalny dostawca tożsamości jest dostępny w kontekście systemu. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące łączności sieciowej](hybrid-azuread-join-managed-domains.md#prerequisites).
- **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586)
   - Przyczyna: przerwano połączenie z punktem końcowym uwierzytelniania.
   - Rozwiązanie: spróbuj ponownie za jakiś czas lub spróbuj połączyć się z alternatywną stabilną lokalizacją sieciową.
- **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441)
   - Przyczyna: nie można sprawdzić poprawności Transport Layer Security (TLS), wcześniej znanego jako SSL (SSL) certyfikatu wysłanego przez serwer.
   - Rozwiązanie: Sprawdź pochylenie czasu klienta. Ponów próbę po upływie pewnego czasu lub spróbuj połączyć się z alternatywną stabilną lokalizacją sieciową.
- **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587)
   - Przyczyna: próba nawiązania połączenia `https://login.microsoftonline.com` nie powiodła się.
   - Rozwiązanie: Sprawdź połączenie sieciowe `https://login.microsoftonline.com` .

##### <a name="other-errors"></a>Inne błędy

- **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829)
   - Przyczyna: token SAML pochodzący od lokalnego dostawcy tożsamości nie został zaakceptowany przez usługę Azure AD.
   - Rozwiązanie: Sprawdź ustawienia serwera federacyjnego. Wyszukaj kod błędu serwera w dziennikach uwierzytelniania.
- **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060)
   - Przyczyna: Wystąpił wyjątek błędu serwera WS-Trust odpowiedzi i nie powiodło się uzyskanie potwierdzenia
   - Rozwiązanie: Sprawdź ustawienia serwera federacyjnego. Wyszukaj kod błędu serwera w dziennikach uwierzytelniania.
- **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074)
   - Przyczyna: Wystąpił błąd podczas próby pobrania tokenu dostępu z punktu końcowego tokenu.
   - Rozwiązanie: poszukaj podstawowego błędu w dzienniku ADAL.
- **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323)
   - Przyczyna: ogólny błąd biblioteki ADAL
   - Rozwiązanie: Wyszukaj kod błędu lub kod błędu serwera z dzienników uwierzytelniania.

#### <a name="join-phase"></a>Faza sprzężenia

Przyczyny niepowodzenia:

Znajdź Typ rejestracji i wyszukaj kod błędu z poniższej listy.

#### <a name="windows-10-1803-and-above"></a>Windows 10 1803 i nowsze

Wyszukaj podsekcję "poprzednia rejestracja" w sekcji "dane diagnostyczne" danych wyjściowych stanu sprzężenia. Ta sekcja jest wyświetlana tylko wtedy, gdy urządzenie jest przyłączone do domeny i nie może wykonać hybrydowego sprzężenia usługi Azure AD.
Pole "typ rejestracji" oznacza typ wykonanego sprzężenia.

```
+----------------------------------------------------------------------+
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

#### <a name="older-windows-10-versions"></a>Starsze wersje systemu Windows 10

Użyj dzienników Podgląd zdarzeń, aby zlokalizować fazę i kod błędu dla błędów sprzężenia.

1. Otwórz dzienniki zdarzeń **rejestracji urządzeń użytkowników** w Podglądzie zdarzeń. Znajduje się w obszarze **Dziennik aplikacji i usług**  >  **Microsoft**  >  **Windows**  >  **rejestracja urządzeń użytkowników** w systemie Microsoft Windows
2. Wyszukaj zdarzenia z następującym eventIDs 204

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Zrzut ekranu przedstawiający Podgląd zdarzeń. Jest zaznaczone zdarzenie z opcją I D 304, a jego informacje są wyświetlane z wyróżnionym kodem błędu i etapem." border="false":::

##### <a name="http-errors-returned-from-drs-server"></a>Błędy HTTP zwrócone z serwera DRS

- **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630)
   - Przyczyna: odebrano odpowiedź o błędzie z DRS z ErrorCode: "DirectoryError"
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera, aby zapoznać się z możliwymi przyczynami i rozwiązaniami.
- **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638)
   - Przyczyna: odebrano odpowiedź o błędzie z DRS z ErrorCode: "AuthenticationError" i ErrorSubCode nie jest "DeviceNotFound".
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera, aby zapoznać się z możliwymi przyczynami i rozwiązaniami.
- **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634)
   - Przyczyna: odebrano odpowiedź o błędzie z DRS z ErrorCode: "DirectoryError"
   - Rozwiązanie: Zapoznaj się z kodem błędu serwera, aby zapoznać się z możliwymi przyczynami i rozwiązaniami.

##### <a name="tpm-errors"></a>Błędy modułu TPM

- **NTE_BAD_KEYSET** (0x80090016/-2146893802)
   - Przyczyna: nie można wykonać operacji modułu TPM lub jest ona nieprawidłowa
   - Rozwiązanie: prawdopodobnie z powodu złego obrazu programu Sysprep. Upewnij się, że maszyna, z której utworzono obraz programu Sysprep, nie jest przyłączona do usługi Azure AD, dołączona do hybrydowej usługi Azure AD lub zarejestrowana usługa Azure AD.
- **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641)
   - Przyczyna: ogólny błąd modułu TPM.
   - Rozwiązanie: Wyłącz moduł TPM na urządzeniach z tym błędem. System Windows 10 w wersji 1809 lub nowszej automatycznie wykrywa błędy modułu TPM i wykonuje sprzężenie hybrydowe usługi Azure AD bez użycia modułu TPM.
- **TPM_E_NOTFIPS** (0x80280036/-2144862154)
   - Przyczyna: moduł TPM w trybie FIPS nie jest obecnie obsługiwany.
   - Rozwiązanie: Wyłącz moduł TPM na urządzeniach z tym błędem. System Windows 1809 automatycznie wykrywa awarie modułu TPM i wykonuje sprzężenie hybrydowe usługi Azure AD bez użycia modułu TPM.
- **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775)
   - Przyczyna: moduł TPM został zablokowany.
   - Rozwiązanie: błąd przejściowy. Poczekaj na cooldown okres. Próba dołączenia po pewnym czasie powinna się powieść. Więcej informacji można znaleźć w artykule podstawowe informacje dotyczące [modułów TPM](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering)

##### <a name="network-errors"></a>Błędy sieci

- **WININET_E_TIMEOUT** (0x80072EE2/-2147012894)
   - Przyczyna: ogólny limit czasu sieci podczas próby zarejestrowania urządzenia w usłudze DRS
   - Rozwiązanie: sprawdź łączność sieciową `https://enterpriseregistration.windows.net` .
- **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889)
   - Przyczyna: nie można rozpoznać nazwy serwera lub adresu.
   - Rozwiązanie: sprawdź łączność sieciową `https://enterpriseregistration.windows.net` . Upewnij się, że rozpoznawanie nazw DNS dla nazwy hosta jest dokładne w n/w i na urządzeniu.
- **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866)
   - Przyczyna: połączenie z serwerem zostało nieprawidłowo przerwane.
   - Rozwiązanie: spróbuj ponownie za jakiś czas lub spróbuj połączyć się z alternatywną stabilną lokalizacją sieciową.

##### <a name="federated-join-server-errors"></a>Błędy serwera dołączania federacyjnego

| Kod błędu serwera | Komunikat o błędzie serwera | Możliwe przyczyny | Rozwiązanie |
| --- | --- | --- | --- |
| DirectoryError | Twoje żądanie zostało tymczasowo ograniczone. Spróbuj ponownie za 300 sekund. | Oczekiwany błąd. Prawdopodobnie z powodu wykonywania wielu żądań rejestracji w krótkim sukcesie. | Ponów próbę przyłączenia po okresie cooldown |

##### <a name="sync-join-server-errors"></a>Błędy serwera łączenia z synchronizacją

| Kod błędu serwera | Komunikat o błędzie serwera | Możliwe przyczyny | Rozwiązanie |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: <UUID> nie znaleziono dzierżawy. Ten błąd może wystąpić, jeśli nie ma aktywnych subskrypcji dla dzierżawy. Skontaktuj się z administratorem subskrypcji. | Identyfikator dzierżawy w obiekcie SCP jest nieprawidłowy | Upewnij się, że obiekt SCP jest skonfigurowany z prawidłowym IDENTYFIKATORem dzierżawy usługi Azure AD i aktywnymi subskrypcjami, które znajdują się w dzierżawie. |
| DirectoryError | Nie znaleziono obiektu urządzenia o podanym IDENTYFIKATORze. | Oczekiwany błąd podczas przyłączania do synchronizacji. Obiekt urządzenia nie został zsynchronizowany z usługi AD z usługą Azure AD | Poczekaj na zakończenie synchronizacji Azure AD Connect, a kolejna próba sprzężenia po zakończeniu synchronizacji rozwiąże problem |
| AuthenticationError | Weryfikacja identyfikatora SID komputera docelowego | Certyfikat na urządzeniu usługi Azure AD nie jest zgodny z certyfikatem używanym do podpisywania obiektu BLOB podczas przyłączania do synchronizacji. Ten błąd zazwyczaj oznacza, że synchronizacja nie została jeszcze ukończona. |  Poczekaj na zakończenie synchronizacji Azure AD Connect, a kolejna próba sprzężenia po zakończeniu synchronizacji rozwiąże problem |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Krok 5. zbieranie dzienników i pomoc techniczna firmy Microsoft kontaktów

Pobierz Auth.zip pliku z [https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH](https://github.com/CSS-Windows/WindowsDiag/tree/master/ADS/AUTH)

1. Rozpakuj pliki i Zmień nazwy uwzględnionych plików **start-auth.txt** i **stop-auth.txt** do **Start-auth. cmd** i **stop-auth. cmd** .
1. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom program **Start-auth. cmd** .
1. Użyj konta przełącznika, aby przełączyć się do innej sesji z użytkownikiem problemu.
1. Odtwórz problem.
1. Użyj opcji Przełącz konto, aby przełączyć się z powrotem do sesji administracyjnej z uruchomionym śledzeniem.
1. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom program **stop-auth. cmd** .
1. Zip i Wyślij folder **Authlogs** z folderu, z którego zostały wykonane skrypty.

## <a name="troubleshoot-post-join-issues"></a>Rozwiązywanie problemów z przyłączaniem

### <a name="retrieve-the-join-status"></a>Pobieranie stanu sprzężenia

#### <a name="wamdefaultset-yes-and-azureadprt-yes"></a>WamDefaultSet: tak i AzureADPrt: tak

Te pola wskazują, czy użytkownik pomyślnie uwierzytelnił się w usłudze Azure AD podczas logowania się do urządzenia.
Jeśli wartości **nie** są, może to być spowodowane:

- Zły klucz magazynu w module TPM skojarzonym z urządzeniem po rejestracji (Sprawdź KeySignTest, gdy działa podwyższony poziom).
- Alternatywny identyfikator logowania
- Nie znaleziono serwera proxy HTTP

## <a name="known-issues"></a>Znane problemy
- W obszarze Ustawienia — konta > — > dostęp do miejsca pracy lub szkoły, urządzenia dołączone do hybrydowej usługi Azure AD mogą wyświetlać dwa różne konta, jedno dla usługi Azure AD i jeden dla lokalnej usługi AD, połączone z hotspotami mobilnymi lub zewnętrznymi sieciami Wi-Fi. Jest to tylko problem z interfejsem użytkownika i nie ma żadnego wpływu na funkcjonalność.

## <a name="next-steps"></a>Następne kroki

Kontynuuj [Rozwiązywanie problemów z urządzeniami za pomocą polecenia dsregcmd](troubleshoot-device-dsregcmd.md)

Pytania można znaleźć w temacie [często zadawane pytania dotyczące zarządzania urządzeniami](faq.md)