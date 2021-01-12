---
title: 'Azure AD Connect: bezproblemowe pojedyncze Sign-On — często zadawane pytania | Microsoft Docs'
description: Odpowiedzi na często zadawane pytania dotyczące Azure Active Directory bezproblemowego logowania jednokrotnego.
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
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dac4b461d4506015f0ef374eae37f67c445791d
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98107875"
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory bezproblemowe logowanie jednokrotne: często zadawane pytania

W tym artykule opisano często zadawane pytania dotyczące Azure Active Directory bezproblemowe pojedyncze Sign-On (bezproblemowe logowanie jednokrotne). Kontynuuj sprawdzanie pod kątem nowej zawartości.

**P: Jakie metody logowania umożliwiają bezproblemowe logowanie jednokrotne za pomocą**

Bezproblemowe logowanie jednokrotne może być łączone z [synchronizacją skrótów haseł](how-to-connect-password-hash-synchronization.md) lub metodami logowania przy [użyciu uwierzytelniania przekazywanego](how-to-connect-pta.md) . Ta funkcja nie może być używana z usługą Active Directory Federation Services (AD FS).

**P: ma bezproblemowe bezpłatne funkcje rejestracji jednokrotnej?**

Bezproblemowe logowanie jednokrotne jest bezpłatną funkcją i nie są potrzebne żadne płatne wersje usługi Azure AD do użycia.

**P: ma bezproblemowe dostęp do logowania jednokrotnego w [chmurze Microsoft Azure (Niemcy)](https://www.microsoft.de/cloud-deutschland) i w [chmurze Microsoft Azure Government](https://azure.microsoft.com/features/gov/)?**

Bezproblemowe logowanie jednokrotne jest dostępne dla chmury Azure Governmentej. Aby uzyskać szczegółowe informacje, zobacz [uwagi dotyczące tożsamości hybrydowej dla Azure Government](./reference-connect-government-cloud.md).

**P: Jakie aplikacje wykorzystują `domain_hint` funkcję lub z `login_hint` możliwością parametrów bezproblemowego logowania jednokrotnego?**

Poniżej wymieniono niekompletną listę aplikacji, które mogą wysyłać te parametry do usługi Azure AD. w związku z tym użytkownicy uzyskują dyskretne środowisko logowania za pomocą bezproblemowego logowania jednokrotnego (tzn. nie ma potrzeby wprowadzania nazw użytkowników ani haseł przez użytkownika):

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| Panel dostępu | https: \/ /myapps.Microsoft.com/contoso.com |
| Program Outlook w sieci Web | https: \/ /Outlook.office365.com/contoso.com |
| Portale pakietu Office 365 | https: \/ /Portal.Office.com? domain_hint = contoso. com, https: \/ /www.office.com? domain_hint = contoso. com |

Ponadto użytkownicy uzyskują ciche działania w przypadku, gdy aplikacja wysyła żądania logowania do punktów końcowych usługi Azure AD skonfigurowanych jako dzierżawcy, czyli https: \/ /login.microsoftonline.com/contoso.com/<.. > lub https: \/ /login.microsoftonline.com/<tenant_ID>/<.. > — zamiast wspólnego punktu końcowego usługi Azure AD — czyli protokołu https: \/ /login.microsoftonline.com/common/<... >. Poniżej wymieniono niekompletną listę aplikacji, które tworzą te typy żądań logowania.

| Nazwa aplikacji | Adres URL aplikacji do użycia |
| -- | -- |
| SharePoint Online | https: \/ /contoso.SharePoint.com |
| Azure Portal | https: \/ /Portal.Azure.com/contoso.com |

W powyższych tabelach Zastąp ciąg "contoso.com" nazwą domeny, aby uzyskać dostęp do odpowiednich adresów URL aplikacji dla dzierżawy.

Jeśli chcesz, aby inne aplikacje korzystali z naszych dyskretnych funkcji logowania, poinformuj nas o tym w sekcji opinii.

**P: zapewnia bezproblemową obsługę logowania jednokrotnego `Alternate ID` jako nazwę użytkownika, a nie `userPrincipalName` ?**

Tak. Bezproblemowe logowanie jednokrotne `Alternate ID` jest obsługiwane jako nazwa użytkownika po skonfigurowaniu w Azure AD Connect, jak pokazano [poniżej](how-to-connect-install-custom.md). Nie wszystkie aplikacje Microsoft 365 obsługują `Alternate ID` . Zapoznaj się z dokumentacją dotyczącą pomocy technicznej dotyczącej określonej aplikacji.

**P: Jaka jest różnica między środowiskiem logowania jednokrotnego udostępnianym przez [usługę Azure AD Join](../devices/overview.md) i bezproblemowe logowanie jednokrotne?**

[Usługa Azure AD Join](../devices/overview.md) umożliwia logowanie jednokrotne dla użytkowników, jeśli ich urządzenia są zarejestrowane w usłudze Azure AD. Urządzenia te nie muszą być przyłączone do domeny. Logowanie jednokrotne jest dostarczane przy użyciu *podstawowych tokenów odświeżania* lub *PRTs*, a nie protokołu Kerberos. Środowisko użytkownika jest najbardziej optymalne na urządzeniach z systemem Windows 10. Logowanie jednokrotne odbywa się automatycznie w przeglądarce Microsoft Edge. Działa również w programie Chrome przy użyciu rozszerzenia przeglądarki.

W dzierżawie można używać zarówno sprzężenia usługi Azure AD, jak i bezproblemowego logowania jednokrotnego. Te dwie funkcje uzupełniają się. Jeśli obie funkcje są włączone, logowanie jednokrotne z usługi Azure AD ma pierwszeństwo przed bezproblemowym logowaniem jednokrotnym.

**P: chcę zarejestrować urządzenia z systemem innym niż Windows 10 w usłudze Azure AD bez używania AD FS. Czy zamiast tego można użyć bezproblemowego logowania jednokrotnego?**

Tak, ten scenariusz wymaga wersji 2,1 lub nowszej [klienta dołączania do miejsca pracy](https://www.microsoft.com/download/details.aspx?id=53554).

**P: jak można wycofać klucz odszyfrowujący protokołu Kerberos `AZUREADSSO` konta komputera?**

Ważne jest, aby często przetworzyć klucz odszyfrowujący protokołu Kerberos `AZUREADSSO` konta komputera (co reprezentuje usługę Azure AD) utworzony w lokalnym lesie usługi AD.

>[!IMPORTANT]
>Zdecydowanie zalecamy przeprowadzenie klucza odszyfrowywania Kerberos co najmniej co 30 dni.

Wykonaj następujące kroki na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect:

   > [!NOTE]
   >Aby wykonać poniższe kroki, wymagane są poświadczenia administratora domeny i administratora globalnego.
   >Jeśli nie jesteś administratorem domeny i masz przypisane uprawnienia administratora domeny, należy wywołać `Update-AzureADSSOForest -OnPremCredentials $creds -PreserveCustomPermissionsOnDesktopSsoAccount`

   **Krok 1. Pobierz listę lasów usługi AD, w których włączono bezproblemowe logowanie jednokrotne**

   1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1` .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
   5. Wywołanie `Get-AzureADSSOStatus | ConvertFrom-Json` . To polecenie umożliwia wyświetlenie listy lasów usługi AD (Zobacz listę "domeny"), na której włączono tę funkcję.

   **Krok 2. Zaktualizuj klucz odszyfrowujący protokołu Kerberos w każdym lesie usługi AD, na którym został on skonfigurowany**

   1. Wywołanie `$creds = Get-Credential` . Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi AD.

   > [!NOTE]
   >Należy podać nazwę użytkownika poświadczeń administratora domeny w formacie nazwy konta SAM (contoso\johndoe lub contoso. com\johndoe). Używamy części nazwy użytkownika do lokalizowania kontrolera domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Użyte konto administratora domeny nie może być członkiem grupy Chronieni użytkownicy. Jeśli tak, operacja zakończy się niepowodzeniem.

   2. Wywołanie `Update-AzureADSSOForest -OnPremCredentials $creds` . To polecenie aktualizuje klucz odszyfrowujący protokołu Kerberos dla `AZUREADSSO` konta komputera w tym określonym lesie usługi AD i aktualizuje go w usłudze Azure AD.
   
   3. Powtórz powyższe kroki dla każdego lasu usługi AD, dla którego skonfigurowano funkcję.
   
  >[!NOTE]
   >Jeśli aktualizujesz Las, inny niż Azure AD Connect, upewnij się, że jest dostępna łączność z serwerem wykazu globalnego (TCP 3268 i TCP 3269).

   >[!IMPORTANT]
   >Upewnij się, że polecenie _nie_ jest uruchamiane `Update-AzureADSSOForest` więcej niż raz. W przeciwnym razie funkcja przestanie działać do czasu wygaśnięcia biletów protokołu Kerberos użytkowników i zostanie ponownie wystawiona przez Active Directory lokalnych.

**P: Jak mogę wyłączyć bezproblemowe logowanie jednokrotne?**

   **Krok 1. Wyłącz funkcję w dzierżawie**

   **Opcja A: Wyłącz używanie Azure AD Connect**
    
   1. Uruchom Azure AD Connect, wybierz pozycję **Zmień stronę logowania użytkownika** , a następnie kliknij przycisk **dalej**.
   2. Usuń zaznaczenie opcji **Włącz logowanie jednokrotne** . Kontynuuj pracę z kreatorem.

   Po ukończeniu działania kreatora bezproblemowy Logowanie jednokrotne zostanie wyłączone w dzierżawie. Zobaczysz jednak komunikat na ekranie, który odczytuje w następujący sposób:

   "Logowanie jednokrotne jest teraz wyłączone, ale istnieją dodatkowe czynności ręczne, które należy wykonać w celu ukończenia czyszczenia. Dowiedz się więcej "

   Aby ukończyć proces oczyszczania, wykonaj kroki 2 i 3 na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect.

   **Opcja B: wyłączanie przy użyciu programu PowerShell**

   Na serwerze lokalnym, na którym jest uruchomiony program Azure AD Connect, wykonaj następujące czynności:

   1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1` .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
   5. Wywołanie `Enable-AzureADSSO -Enable $false` .
   
   W tym momencie bezproblemowe logowanie jednokrotne jest wyłączone, ale domeny pozostaną skonfigurowane tak, aby umożliwić bezproblemowe logowanie jednokrotne. Jeśli chcesz całkowicie usunąć domeny z bezproblemowej konfiguracji rejestracji jednokrotnej, wywołaj następujące polecenie cmdlet po wykonaniu kroku 5 powyżej: `Disable-AzureADSSOForest -DomainFqdn <fqdn>` .

   >[!IMPORTANT]
   >Wyłączenie bezproblemowego logowania jednokrotnego przy użyciu programu PowerShell nie spowoduje zmiany stanu w Azure AD Connect. Bezproblemowe logowanie jednokrotne zostanie wyświetlone jako włączone na stronie **zmiany logowania użytkownika** .

   **Krok 2. Pobierz listę lasów usługi AD, w których włączono bezproblemowe logowanie jednokrotne**

   Wykonaj zadania od 1 do 4 poniżej, jeśli wyłączono bezproblemowe logowanie jednokrotne przy użyciu Azure AD Connect. Jeśli wyłączysz bezproblemowe logowanie jednokrotne przy użyciu programu PowerShell, przejdź do kolejnego zadania 5 poniżej.

   1. Najpierw pobierz i zainstaluj program [Azure AD PowerShell](/powershell/azure/active-directory/overview).
   2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
   3. Zaimportuj bezproblemowe moduł programu PowerShell dla logowania jednokrotnego za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1` .
   4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . To polecenie powinno umożliwić podręczne wprowadzenie poświadczeń administratora globalnego dzierżawy.
   5. Wywołanie `Get-AzureADSSOStatus | ConvertFrom-Json` . To polecenie umożliwia wyświetlenie listy lasów usługi AD (Zobacz listę "domeny"), na której włączono tę funkcję.

   **Krok 3. Ręcznie usuń `AZUREADSSO` konto komputera z poszczególnych lasów usługi AD, które są widoczne na liście.**

## <a name="next-steps"></a>Następne kroki

- [**Przewodnik Szybki Start**](how-to-connect-sso-quick-start.md) — Rozpoczynanie pracy z usługą Azure AD bez logowania jednokrotnego.
- [**Szczegółowee techniczne głębokie**](how-to-connect-sso-how-it-works.md) — zrozumienie, jak działa ta funkcja.
- [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.
