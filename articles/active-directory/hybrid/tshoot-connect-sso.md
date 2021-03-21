---
title: 'Azure Active Directory Connect: Rozwiązywanie problemów z bezproblemową pojedynczą Sign-On | Microsoft Docs'
description: W tym temacie opisano sposób rozwiązywania problemów Azure Active Directory bezproblemowe pojedyncze Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101644650"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Rozwiązywanie problemów Azure Active Directory bezproblemowe pojedyncze Sign-On

Ten artykuł ułatwia znalezienie informacji o rozwiązywaniu problemów dotyczących typowych problemów dotyczących Azure Active Directory (usługa Azure AD) bezproblemowe pojedyncze Sign-On (bezproblemowe logowanie jednokrotne).

## <a name="known-issues"></a>Znane problemy

- W kilku przypadkach włączenie bezproblemowego logowania jednokrotnego może potrwać do 30 minut.
- Jeśli wyłączysz i ponownie włączysz bezproblemową rejestrację jednokrotną w dzierżawie, użytkownicy nie będą mogli korzystać z logowania jednokrotnego do buforowanych biletów Kerberos, zwykle przez 10 godzin wygasły.
- Jeśli bezproblemowe logowanie jednokrotne powiedzie się, użytkownik nie ma możliwości wybrania opcji nie **wylogowuj mnie**. Ze względu na to zachowanie, [scenariusze mapowania programu SharePoint i usługi OneDrive](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nie działają.
- Microsoft 365 klienci Win32 (Outlook, Word, Excel i inne) z wersjami 16.0.8730. xxxx i powyżej są obsługiwane za pomocą nieinteraktywnego przepływu. Inne wersje nie są obsługiwane. w tych wersjach użytkownicy będą wprowadzać nazwy użytkowników, ale nie hasła, aby się zalogować. W przypadku usługi OneDrive konieczne będzie aktywowanie [funkcji konfiguracji dyskretnej usługi OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) w celu zalogowania dyskretnego.
- Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarce Firefox.
- Bezproblemowe logowanie jednokrotne nie działa w programie Internet Explorer, gdy włączony jest rozszerzony tryb chroniony.
- Bezproblemowe logowanie jednokrotne nie działa w trybie przeglądania prywatnego w przeglądarce Microsoft Edge (starsza wersja).
- Bezproblemowe logowanie jednokrotne nie działa w przeglądarkach mobilnych w systemach iOS i Android.
- Jeśli użytkownik jest częścią zbyt wielu grup w Active Directory, bilet protokołu Kerberos użytkownika będzie prawdopodobnie zbyt duży do przetworzenia i spowoduje to niepowodzenie bezproblemowego logowania jednokrotnego. Żądania HTTPS usługi Azure AD mogą mieć nagłówki o maksymalnym rozmiarze 50 KB; Bilety protokołu Kerberos muszą być mniejsze niż ten limit, aby pomieścić inne artefakty usługi Azure AD (zazwyczaj 2-5 KB), takie jak pliki cookie. Naszym zaleceniem jest zredukowanie członkostwa w grupach użytkowników i spróbuj ponownie.
- W przypadku synchronizacji co najmniej 30 Active Directory lasów nie można włączyć bezproblemowego logowania jednokrotnego za pomocą Azure AD Connect. Aby obejść ten element, można [ręcznie włączyć](#manual-reset-of-the-feature) tę funkcję w dzierżawie.
- Dodanie adresu URL usługi Azure AD ( `https://autologon.microsoftazuread-sso.com` ) do strefy Zaufane witryny zamiast lokalnej strefy intranetowej *uniemożliwia użytkownikom logowanie* się.
- Bezproblemowe logowanie jednokrotne obsługuje typy szyfrowania AES256_HMAC_SHA1, AES128_HMAC_SHA1 i RC4_HMAC_MD5 dla protokołu Kerberos. Zaleca się, aby w celu zwiększenia bezpieczeństwa typ szyfrowania dla konta AzureADSSOAcc $ miał wartość AES256_HMAC_SHA1 lub jeden z typów AES a RC4. Typ szyfrowania jest przechowywany w atrybucie msDS-Supportedencryptiontypes konta konta w Active Directory.  Jeśli typ szyfrowania AzureADSSOAcc $ Account jest ustawiony na RC4_HMAC_MD5 i chcesz zmienić go na jeden z typów szyfrowania AES, upewnij się, że najpierw przeniesiesz klucz odszyfrowujący protokołu Kerberos konta AzureADSSOAcc $ zgodnie z opisem w [dokumencie często zadawane pytania](how-to-connect-sso-faq.md) , w przeciwnym razie bezproblemowe logowanie jednokrotne nie następuje.
-  Jeśli masz więcej niż jeden las z zaufaniem lasu, włączenie logowania jednokrotnego w jednym z lasów spowoduje włączenie logowania jednokrotnego we wszystkich zaufanych lasach. Jeśli włączysz logowanie jednokrotne w lesie, w którym logowanie jednokrotne jest już włączone, zostanie wyświetlony komunikat o błędzie informujący o tym, że logowanie jednokrotne jest już włączone w lesie.

## <a name="check-status-of-feature"></a>Sprawdź stan funkcji

Upewnij się, że funkcja bezproblemowego logowania jednokrotnego jest nadal **włączona** w dzierżawie. Stan można sprawdzić, przechodząc do okienka **Azure AD Connect** w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory centrum administracyjnego: Azure AD Connect okienko](./media/tshoot-connect-sso/sso10.png)

Kliknij pozycję przez, aby wyświetlić wszystkie lasy usługi AD, które zostały włączone dla bezproblemowego logowania jednokrotnego.

![Azure Active Directory centrum administracyjnego: bezproblemowe okienko logowania jednokrotnego](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Przyczyny niepowodzenia logowania w centrum administracyjnym Azure Active Directory (wymaga licencji Premium)

Jeśli dzierżawa ma skojarzoną licencję Azure AD — wersja Premium, możesz również sprawdzić [raport aktywności logowania](../reports-monitoring/concept-sign-ins.md) w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/).

![Azure Active Directory centrum administracyjnego: Raport logowania](./media/tshoot-connect-sso/sso9.png)

Przejdź do **Azure Active Directory**  >  **logowania** w [centrum administracyjnym Azure Active Directory](https://aad.portal.azure.com/), a następnie wybierz działanie logowania określonego użytkownika. Poszukaj pola **Kod błędu logowania** . Zamapuj wartość tego pola na przyczynę niepowodzenia i rozdzielczość, korzystając z następującej tabeli:

|Kod błędu logowania|Przyczyna niepowodzenia logowania|Rozwiązanie
| --- | --- | ---
| 81001 | Bilet Kerberos użytkownika jest zbyt duży. | Zmniejsz liczbę członkostw użytkownika w grupach i spróbuj ponownie.
| 81002 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81003 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81004 | Próba uwierzytelniania Kerberos nie powiodła się. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81008 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81009 | Nie można zweryfikować biletu Kerberos użytkownika. | Zobacz [listę kontrolną rozwiązywania problemów](#troubleshooting-checklist).
| 81010 | Bezproblemowe logowanie jednokrotne nie powiodło się, ponieważ ważność biletu Kerberos użytkownika wygasła lub bilet jest nieprawidłowy. | Użytkownik musi zalogować się na urządzeniu przyłączonym do domeny w sieci firmowej.
| 81011 | Nie można znaleźć obiektu użytkownika na podstawie informacji z biletu Kerberos użytkownika. | Użyj Azure AD Connect, aby zsynchronizować informacje o użytkowniku w usłudze Azure AD.
| 81012 | Użytkownik próbujący zalogować się do usługi Azure AD różni się od użytkownika, który jest zalogowany na urządzeniu. | Użytkownik musi zalogować się z innego urządzenia.
| 81013 | Nie można znaleźć obiektu użytkownika na podstawie informacji z biletu Kerberos użytkownika. |Użyj Azure AD Connect, aby zsynchronizować informacje o użytkowniku w usłudze Azure AD. 

## <a name="troubleshooting-checklist"></a>Lista kontrolna rozwiązywania problemów

Użyj poniższej listy kontrolnej, aby rozwiązać problemy z logowaniem jednokrotnym:

- Upewnij się, że funkcja bezproblemowego logowania jednokrotnego jest włączona w Azure AD Connect. Jeśli nie możesz włączyć funkcji (na przykład ze względu na zablokowany port), upewnij się, że zostały spełnione wszystkie [wymagania wstępne](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) .
- Jeśli włączono [usługę Azure AD Join](../devices/overview.md) i bezproblemową rejestrację jednokrotną w dzierżawie, upewnij się, że problem nie jest przyłączany do usługi Azure AD. Logowanie jednokrotne z usługi Azure AD ma wyższy priorytet niż bezproblemowe logowanie jednokrotne, jeśli urządzenie jest zarejestrowane w usłudze Azure AD i przyłączone do domeny. W przypadku logowania jednokrotnego z usługi Azure AD użytkownik widzi kafelek logowania "połączony z systemem Windows".
- Upewnij się, że adres URL usługi Azure AD ( `https://autologon.microsoftazuread-sso.com` ) jest częścią ustawień strefy intranetowej użytkownika.
- Upewnij się, że urządzenie firmowe jest przyłączone do domeny Active Directory. Aby bezproblemowe logowanie jednokrotne działało, urządzenie _nie_ musi być [przyłączone do usługi Azure AD](../devices/overview.md) .
- Upewnij się, że użytkownik jest zalogowany na urządzeniu za pomocą konta domeny Active Directory.
- Upewnij się, że konto użytkownika pochodzi z lasu Active Directory, w którym skonfigurowano bezproblemowe logowanie jednokrotne.
- Upewnij się, że urządzenie jest połączone z siecią firmową.
- Upewnij się, że czas urządzenia jest zsynchronizowany z upływem czasu zarówno w Active Directory, jak i na kontrolerach domeny, i że są one w ciągu pięciu minut od siebie.
- Upewnij się, że `AZUREADSSOACC` konto komputera jest obecne i włączone w każdym lesie usługi AD, dla którego chcesz bezproblemowo włączyć logowanie jednokrotne. Jeśli konto komputera zostało usunięte lub nie istnieje, możesz je utworzyć ponownie za pomocą [poleceń cmdlet programu PowerShell](#manual-reset-of-the-feature) .
- Utwórz listę istniejących biletów protokołu Kerberos na urządzeniu przy użyciu `klist` polecenia z wiersza polecenia. Upewnij się, że bilety dla `AZUREADSSOACC` konta komputera są obecne. Bilety protokołu Kerberos użytkowników są zwykle ważne przez 10 godzin. Możesz mieć różne ustawienia w Active Directory.
- Jeśli użytkownik wyłączył i włączył bezproblemowe logowanie jednokrotne w dzierżawie, użytkownicy nie będą mogli korzystać z logowania jednokrotnego do czasu wygaśnięcia biletów w buforze Kerberos.
- Przeczyść istniejące bilety protokołu Kerberos z urządzenia przy użyciu `klist purge` polecenia i spróbuj ponownie.
- Aby określić, czy występują problemy związane z językiem JavaScript, Przejrzyj dzienniki konsoli w przeglądarce (w obszarze **Narzędzia deweloperskie**).
- Przejrzyj [dzienniki kontrolera domeny](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Dzienniki kontrolera domeny

Jeśli włączysz inspekcję sukcesów na kontrolerze domeny, za każdym razem, gdy użytkownik zaloguje się za pomocą bezproblemowego logowania jednokrotnego, wpis zabezpieczeń jest rejestrowany w dzienniku zdarzeń. Te zdarzenia zabezpieczeń można znaleźć za pomocą poniższego zapytania. (Poszukaj zdarzenia **4769** skojarzonego z kontem komputera **AzureADSSOAcc $**).

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Ręczne resetowanie funkcji

Jeśli Rozwiązywanie problemów nie powiodło się, można ręcznie zresetować funkcję w dzierżawie. Wykonaj następujące kroki na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1. zaimportowanie bezproblemowego modułu programu PowerShell logowania jednokrotnego

1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj bezproblemowy moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2. Pobieranie listy Active Directory lasów, w których włączono bezproblemowe logowanie jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Wywołanie `Get-AzureADSSOStatus` . To polecenie umożliwia wyświetlenie listy Active Directory lasów (Zobacz listę "domeny"), na której włączono tę funkcję.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3. wyłączenie bezproblemowego logowania jednokrotnego dla każdego lasu Active Directory, w którym skonfigurowano funkcję

1. Wywołanie `$creds = Get-Credential` . Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu Active Directory.

   > [!NOTE]
   >Należy podać nazwę użytkownika poświadczeń administratora domeny w formacie nazwy konta SAM (contoso\johndoe lub contoso. com\johndoe). Używamy części nazwy użytkownika do lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Użyte konto administratora domeny nie może być członkiem grupy Chronieni użytkownicy. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Wywołanie `Disable-AzureADSSOForest -OnPremCredentials $creds` . To polecenie usuwa `AZUREADSSOACC` konto komputera z lokalnego kontrolera domeny dla danego lasu Active Directory.
3. Powtórz powyższe kroki dla każdego lasu Active Directory, w którym skonfigurowano funkcję.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4. Włączanie bezproblemowego logowania jednokrotnego dla każdego lasu Active Directory

1. Wywołanie `Enable-AzureADSSOForest` . Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu Active Directory.

   > [!NOTE]
   >Należy podać nazwę użytkownika poświadczeń administratora domeny w formacie nazwy konta SAM (contoso\johndoe lub contoso. com\johndoe). Używamy części nazwy użytkownika do lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Użyte konto administratora domeny nie może być członkiem grupy Chronieni użytkownicy. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Powtórz poprzedni krok dla każdego lasu Active Directory, w którym chcesz skonfigurować funkcję.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włączanie funkcji w dzierżawie

Aby włączyć funkcję w dzierżawie, wywołaj polecenie `Enable-AzureADSSO -Enable $true` .
