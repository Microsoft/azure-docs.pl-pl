---
title: Rozwiązywanie problemów Azure Active Directory Domain Services | Microsoft Docs "
description: Dowiedz się, jak rozwiązywać typowe błędy podczas tworzenia Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 4bc8c604-f57c-4f28-9dac-8b9164a0cf0b
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: 7642a32ce69dbbbb5ddebbe56b74f3202b2e6422
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86039572"
---
# <a name="common-errors-and-troubleshooting-steps-for-azure-active-directory-domain-services"></a>Typowe błędy i kroki rozwiązywania problemów dla Azure Active Directory Domain Services

Jako centralna część tożsamości i uwierzytelniania dla aplikacji Azure Active Directory Domain Services (AD DS platformy Azure) czasami występują problemy. Jeśli wystąpią problemy, istnieją typowe komunikaty o błędach i powiązane kroki rozwiązywania problemów, które ułatwiają ponowne uruchomienie. W dowolnym momencie możesz również [otworzyć żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

W tym artykule przedstawiono procedurę rozwiązywania problemów z typowymi problemami dotyczącymi usługi Azure AD DS.

## <a name="you-cannot-enable-azure-ad-domain-services-for-your-azure-ad-directory"></a>Nie można włączyć Azure AD Domain Services dla katalogu usługi Azure AD

Jeśli masz problemy z włączeniem usługi Azure AD DS, przejrzyj następujące typowe błędy i wykonaj kroki, aby je rozwiązać:

| **Przykładowy komunikat o błędzie** | **Rozwiązanie** |
| --- |:--- |
| *Nazwa aaddscontoso.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.* |[Konflikt nazw domen w sieci wirtualnej](troubleshoot.md#domain-name-conflict) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Usługa nie ma wystarczających uprawnień do aplikacji o nazwie "Azure AD Domain Services Sync". Usuń aplikację o nazwie "Azure AD Domain Services Sync", a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.* |[Usługi domenowe nie mają wystarczających uprawnień do aplikacji do synchronizacji Azure AD Domain Services](troubleshoot.md#inadequate-permissions) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja usług domenowych w dzierżawie usługi Azure AD nie ma wymaganych uprawnień, aby włączyć usługi domenowe. Usuń aplikację z identyfikatorem aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.* |[Aplikacja usług domenowych nie jest prawidłowo skonfigurowana w dzierżawie usługi Azure AD](troubleshoot.md#invalid-configuration) |
| *Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację przy użyciu identyfikatora aplikacji 00000002-0000-0000-C000-000000000000, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.* |[Aplikacja Microsoft Graph jest wyłączona w dzierżawie usługi Azure AD](troubleshoot.md#microsoft-graph-disabled) |

### <a name="domain-name-conflict"></a>Konflikt nazw domen

**Komunikat o błędzie**

*Nazwa aaddscontoso.com jest już używana w tej sieci. Określ nazwę, która nie jest używana.*

**Rozwiązanie**

Sprawdź, czy nie masz istniejącego środowiska AD DS o tej samej nazwie domeny w tej samej lub równorzędnej sieci wirtualnej. Na przykład może istnieć AD DS domena o nazwie *aaddscontoso.com* , która jest uruchamiana na maszynach wirtualnych platformy Azure. Gdy próbujesz włączyć domenę zarządzaną platformy Azure AD DS z tą samą nazwą domeny *aaddscontoso.com* w sieci wirtualnej, żądana operacja nie powiedzie się.

Ten błąd jest spowodowany konfliktami nazw dla nazwy domeny w sieci wirtualnej. Wyszukiwanie DNS sprawdza, czy istniejące środowisko AD DS reaguje na żądaną nazwę domeny. Aby rozwiązać ten problem, użyj innej nazwy do skonfigurowania domeny zarządzanej lub Wycofaj obsługę administracyjną istniejącej AD DS domeny, a następnie spróbuj ponownie włączyć usługę Azure AD DS.

### <a name="inadequate-permissions"></a>Niewystarczające uprawnienia

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Usługa nie ma wystarczających uprawnień do aplikacji o nazwie "Azure AD Domain Services Sync". Usuń aplikację o nazwie "Azure AD Domain Services Sync", a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.*

**Rozwiązanie**

Sprawdź, czy w katalogu usługi Azure AD znajduje się aplikacja o nazwie *Azure AD Domain Services Sync* . Jeśli ta aplikacja istnieje, usuń ją, a następnie spróbuj ponownie, aby włączyć usługę Azure AD DS. Aby wyszukać istniejącą aplikację i usunąć ją w razie potrzeby, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**. Wybierz opcję *wszystkie aplikacje* z menu rozwijanego **Typ aplikacji** , a następnie wybierz pozycję **Zastosuj**.
1. W polu wyszukiwania wprowadź *Azure AD Domain Services Sync*. Jeśli aplikacja istnieje, zaznacz ją i wybierz pozycję **Usuń**.
1. Po usunięciu aplikacji spróbuj ponownie włączyć usługę Azure AD DS.

### <a name="invalid-configuration"></a>Nieprawidłowa konfiguracja

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja usług domenowych w dzierżawie usługi Azure AD nie ma wymaganych uprawnień, aby włączyć usługi domenowe. Usuń aplikację z identyfikatorem aplikacji d87dcbc6-a371-462e-88e3-28ad15ec4e64, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.*

**Rozwiązanie**

Sprawdź, czy masz istniejącą aplikację o nazwie *AzureActiveDirectoryDomainControllerServices* z identyfikatorem aplikacji *d87dcbc6-a371-462e-88e3-28ad15ec4e64* w katalogu usługi Azure AD. Jeśli ta aplikacja istnieje, usuń ją, a następnie spróbuj ponownie, aby włączyć usługę Azure AD DS.

Użyj poniższego skryptu programu PowerShell, aby wyszukać istniejące wystąpienie aplikacji i usunąć je w razie potrzeby:

```powershell
$InformationPreference = "Continue"
$WarningPreference = "Continue"

$aadDsSp = Get-AzureADServicePrincipal -Filter "AppId eq 'd87dcbc6-a371-462e-88e3-28ad15ec4e64'" -ErrorAction Ignore
if ($aadDsSp -ne $null)
{
    Write-Information "Found Azure AD Domain Services application. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $aadDsSp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services application."
}

$identifierUri = "https://sync.aaddc.activedirectory.windowsazure.com"
$appFilter = "IdentifierUris eq '" + $identifierUri + "'"
$app = Get-AzureADApplication -Filter $appFilter
if ($app -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync application. Deleting it ..."
    Remove-AzureADApplication -ObjectId $app.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync application."
}

$spFilter = "ServicePrincipalNames eq '" + $identifierUri + "'"
$sp = Get-AzureADServicePrincipal -Filter $spFilter
if ($sp -ne $null)
{
    Write-Information "Found Azure AD Domain Services Sync service principal. Deleting it ..."
    Remove-AzureADServicePrincipal -ObjectId $sp.ObjectId
    Write-Information "Deleted the Azure AD Domain Services Sync service principal."
}
```

### <a name="microsoft-graph-disabled"></a>Microsoft Graph wyłączone

**Komunikat o błędzie**

*Nie można włączyć usług domenowych w tej dzierżawie usługi Azure AD. Aplikacja Microsoft Azure AD jest wyłączona w dzierżawie usługi Azure AD. Włącz aplikację przy użyciu identyfikatora aplikacji 00000002-0000-0000-C000-000000000000, a następnie spróbuj włączyć usługi domenowe dla dzierżawy usługi Azure AD.*

**Rozwiązanie**

Sprawdź, czy aplikacja została wyłączona z identyfikatorem *00000002-0000-0000-C000-000000000000*. Ta aplikacja jest aplikacją Microsoft Azure AD i zapewnia interfejs API programu Graph dostęp do dzierżawy usługi Azure AD. Aby zsynchronizować dzierżawę usługi Azure AD, ta aplikacja musi być włączona.

Aby sprawdzić stan tej aplikacji i włączyć ją w razie potrzeby, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**. Wybierz opcję *wszystkie aplikacje* z menu rozwijanego **Typ aplikacji** , a następnie wybierz pozycję **Zastosuj**.
1. W polu wyszukiwania wprowadź *00000002-0000-0000-C000-00000000000*. Wybierz aplikację, a następnie wybierz pozycję **Właściwości**.
1. Jeśli opcja **włączone dla użytkowników** jest ustawiona na wartość *nie*, wybierz opcję *tak*, a następnie pozycję **Zapisz**.
1. Po włączeniu aplikacji spróbuj ponownie włączyć usługę Azure AD DS.

## <a name="users-are-unable-to-sign-in-to-the-azure-ad-domain-services-managed-domain"></a>Użytkownicy nie mogą zalogować się do domeny zarządzanej usług Azure AD Domain Services

Jeśli co najmniej jeden użytkownik w dzierżawie usługi Azure AD nie może zalogować się do domeny zarządzanej, wykonaj następujące kroki rozwiązywania problemów:

* **Format poświadczeń** — spróbuj użyć formatu UPN w celu określenia poświadczeń, takich jak `dee@aaddscontoso.onmicrosoft.com` . Format nazwy UPN jest zalecanym sposobem określania poświadczeń w usłudze Azure AD DS. Upewnij się, że ta nazwa UPN została prawidłowo skonfigurowana w usłudze Azure AD.

    Nazwa *sAMAccountName* dla Twojego konta, taka jak *AADDSCONTOSO\driley* , może być generowana automatycznie, jeśli istnieje wielu użytkowników z tym samym prefiksem nazwy UPN w dzierżawie lub jeśli prefiks nazwy UPN jest zbyt długi. W związku z tym format *sAMAccountName* dla konta może być inny niż oczekiwany lub używany w domenie lokalnej.

* **Synchronizacja haseł** — upewnij się, że włączono synchronizację haseł dla [użytkowników tylko w chmurze][cloud-only-passwords] lub dla [środowisk hybrydowych przy użyciu Azure AD Connect][hybrid-phs].
    * **Zsynchronizowane konta hybrydowe:** Jeśli konta użytkowników, których to dotyczy, są synchronizowane z katalogu lokalnego, należy sprawdzić następujące zagadnienia:
    
      * [Najnowsza zalecana wersja Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)została wdrożona lub zaktualizowana do programu.
      * Azure AD Connect skonfigurowano w celu [przeprowadzenia pełnej synchronizacji][hybrid-phs].
      * W zależności od rozmiaru katalogu może upłynąć trochę czasu dla kont użytkowników i skrótów poświadczeń, które będą dostępne w domenie zarządzanej. Przed podjęciem próby uwierzytelnienia w domenie zarządzanej upewnij się, że zaczekasz wystarczającą ilość czasu.
      * Jeśli problem będzie się powtarzać po sprawdzeniu poprzednich kroków, spróbuj ponownie uruchomić *usługę synchronizacji Microsoft Azure AD*. Na serwerze Azure AD Connect Otwórz wiersz polecenia, a następnie uruchom następujące polecenia:
    
        ```console
        net stop 'Microsoft Azure AD Sync'
        net start 'Microsoft Azure AD Sync'
        ```

    * **Konta tylko w chmurze**: Jeśli konto użytkownika, którego to dotyczy, jest kontem użytkownika tylko w chmurze, upewnij się, że [użytkownik zmienił hasło po włączeniu usługi Azure AD DS][cloud-only-passwords]. To Resetowanie hasła powoduje wygenerowanie wymaganych skrótów poświadczeń dla domeny zarządzanej.

* **Sprawdź, czy konto użytkownika jest aktywne**: domyślnie pięć nieudanych prób wprowadzenia hasła w ciągu 2 minut w domenie zarządzanej powoduje zablokowanie konta użytkownika przez 30 minut. Użytkownik nie może się zalogować, gdy konto jest zablokowane. Po 30 minutach konto użytkownika zostanie automatycznie odblokowane.
  * Nieprawidłowe próby hasła w domenie zarządzanej nie Zablokuj konta użytkownika w usłudze Azure AD. Konto użytkownika jest blokowane tylko w domenie zarządzanej. Sprawdź stan konta użytkownika w *konsoli administracyjnej Active Directory (usługach ADAC)* przy użyciu [maszyny wirtualnej zarządzania][management-vm], a nie w usłudze Azure AD.
  * Możesz również [skonfigurować szczegółowe zasady haseł][password-policy] , aby zmienić domyślny próg blokady i czas trwania.

* **Konta zewnętrzne** — Sprawdź, czy konto użytkownika, którego to dotyczy, nie jest kontem zewnętrznym w dzierżawie usługi Azure AD. Przykłady kont zewnętrznych obejmują konta Microsoft, takie jak `dee@live.com` konta użytkowników, z zewnętrznego katalogu usługi Azure AD. Usługa Azure AD DS nie przechowuje poświadczeń dla kont użytkowników zewnętrznych, dlatego nie może zalogować się do domeny zarządzanej.

## <a name="there-are-one-or-more-alerts-on-your-managed-domain"></a>Istnieje co najmniej jeden alert w domenie zarządzanej

Jeśli w domenie zarządzanej istnieją aktywne alerty, może to uniemożliwić poprawne działanie procesu uwierzytelniania.

Aby sprawdzić, czy istnieją aktywne alerty, [Sprawdź stan kondycji domeny zarządzanej][check-health]. Jeśli są wyświetlane alerty, [Rozwiąż problemy i Rozwiąż je][troubleshoot-alerts].

## <a name="users-removed-from-your-azure-ad-tenant-are-not-removed-from-your-managed-domain"></a>Users removed from your Azure AD tenant are not removed from your managed domain (Użytkownicy usunięci z dzierżawy usługi Azure AD nie są usuwani z domeny zarządzanej)

Usługa Azure AD chroni przed przypadkowym usunięciem obiektów użytkowników. Po usunięciu konta użytkownika z dzierżawy usługi Azure AD odpowiedni obiekt użytkownika zostanie przeniesiony do kosza. Gdy ta operacja usuwania zostanie zsynchronizowana z domeną zarządzaną, odpowiednie konto użytkownika zostanie oznaczone jako wyłączone. Ta funkcja ułatwia odzyskiwanie lub cofanie usunięcia konta użytkownika.

Konto użytkownika pozostaje w stanie wyłączenia w domenie zarządzanej, nawet jeśli ponownie utworzysz konto użytkownika z tą samą nazwą UPN w katalogu usługi Azure AD. Aby usunąć konto użytkownika z domeny zarządzanej, należy wymusić usunięcie go z dzierżawy usługi Azure AD.

Aby w pełni usunąć konto użytkownika z domeny zarządzanej, trwale Usuń użytkownika z dzierżawy usługi Azure AD za pomocą polecenia cmdlet [Remove-MsolUser][Remove-MsolUser] programu PowerShell z `-RemoveFromRecycleBin` parametrem.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[cloud-only-passwords]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[hybrid-phs]: tutorial-configure-password-hash-sync.md
[management-vm]: tutorial-create-management-vm.md
[password-policy]: password-policy.md
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
[Remove-MsolUser]: /powershell/module/MSOnline/Remove-MsolUser
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
