---
title: Rozwiązywanie alertów głównych usługi w Azure AD Domain Services | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z alertami konfiguracji jednostki usługi dla Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: f168870c-b43a-4dd6-a13f-5cfadc5edf2c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 15dba02d3db45d663b8ba0aa7eb93bbc3f388532
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645878"
---
# <a name="known-issues-service-principal-alerts-in-azure-active-directory-domain-services"></a>Znane problemy: alerty nazw głównych usług w Azure Active Directory Domain Services

[Nazwy główne usług](../active-directory/develop/app-objects-and-service-principals.md) to aplikacje używane przez platformę Azure do zarządzania, aktualizowania i konserwowania domeny zarządzanej Azure Active Directory Domain Services (Azure AD DS). Jeśli jednostka usługi zostanie usunięta, wpłynie to na funkcjonalność w domenie zarządzanej.

Ten artykuł ułatwia rozwiązywanie problemów i rozwiązywanie alertów dotyczących konfiguracji jednostki usługi.

## <a name="alert-aadds102-service-principal-not-found"></a>Alert AADDS102: nie znaleziono nazwy głównej usługi

### <a name="alert-message"></a>Komunikat alertu

*Nazwa główna usługi wymagana do poprawnego działania Azure AD Domain Services została usunięta z katalogu usługi Azure AD. Ta konfiguracja ma wpływ na zdolność firmy Microsoft do monitorowania, poprawiania i synchronizowania domeny zarządzanej oraz zarządzania nią.*

Jeśli zostanie usunięta wymagana jednostka usługi, platforma Azure nie będzie mogła wykonywać zautomatyzowanych zadań zarządzania. Domena zarządzana może nieprawidłowo stosować aktualizacje lub tworzyć kopie zapasowe.

### <a name="check-for-missing-service-principals"></a>Sprawdź brakujące jednostki usługi

Aby sprawdzić, której nazwy głównej usługi nie ma, i należy ją utworzyć ponownie, wykonaj następujące czynności:

1. W Azure Portal wybierz pozycję **Azure Active Directory** z menu nawigacji po lewej stronie.
1. Wybierz pozycję **aplikacje dla przedsiębiorstw**. Wybierz opcję *wszystkie aplikacje* z menu rozwijanego **Typ aplikacji** , a następnie wybierz pozycję **Zastosuj**.
1. Wyszukaj każdy z następujących identyfikatorów aplikacji. Jeśli nie zostanie znaleziona istniejąca aplikacja, wykonaj kroki opisane w sekcji *rozwiązanie* , aby utworzyć nazwę główną usługi, lub ponownie zarejestruj przestrzeń nazw.

    | Identyfikator aplikacji | Rozwiązanie |
    | :--- | :--- |
    | 2565bd9d-da50-47d4-8b85-4c97f669dc36 | [Utwórz ponownie brakującą nazwę główną usługi](#recreate-a-missing-service-principal) |
    | 443155a6-77f3-45e3-882b-22b3a8d431fb | [Zarejestruj ponownie przestrzeń nazw Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | abba844e-bc0e-44b0-947a-dc74e5d09022 | [Zarejestruj ponownie przestrzeń nazw Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |
    | d87dcbc6-a371-462e-88e3-28ad15ec4e64 | [Zarejestruj ponownie przestrzeń nazw Microsoft. AAD](#re-register-the-microsoft-aad-namespace) |

### <a name="recreate-a-missing-service-principal"></a>Utwórz ponownie brakującą nazwę główną usługi

Jeśli w katalogu usługi Azure AD brakuje identyfikatora aplikacji *2565bd9d-DA50-47d4-8b85-4c97f669dc36* , użyj programu Azure AD PowerShell, aby wykonać następujące czynności. Aby uzyskać więcej informacji, zobacz [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. W razie konieczności zainstaluj moduł Azure AD PowerShell i zaimportuj go w następujący sposób:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

1. Teraz należy ponownie utworzyć nazwę główną usługi przy użyciu polecenia cmdlet [New-AzureAdServicePrincipal][New-AzureAdServicePrincipal] :

    ```powershell
    New-AzureAdServicePrincipal -AppId "2565bd9d-da50-47d4-8b85-4c97f669dc36"
    ```

Kondycja domeny zarządzanej automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

### <a name="re-register-the-microsoft-aad-namespace"></a>Zarejestruj ponownie przestrzeń nazw usługi Microsoft AAD

Jeśli w katalogu usługi Azure AD brakuje identyfikatora aplikacji *443155a6-77f3-45e3-882b-22b3a8d431fb*, *abba844e-bc0e-44b0-947a-dc74e5d09022* lub *d87dcbc6-a371-462e-88e3-28ad15ec4e64* , wykonaj następujące kroki, aby ponownie zarejestrować dostawcę zasobów *Microsoft. AAD* :

1. W Azure Portal Wyszukaj i wybierz pozycję **subskrypcje**.
1. Wybierz subskrypcję skojarzoną z domeną zarządzaną.
1. W okienku nawigacji po lewej stronie wybierz pozycję **dostawcy zasobów**.
1. Wyszukaj ciąg *Microsoft. AAD*, a następnie wybierz pozycję **zarejestruj ponownie**.

Kondycja domeny zarządzanej automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="alert-aadds105-password-synchronization-application-is-out-of-date"></a>Alert AADDS105: aplikacja do synchronizacji haseł jest nieaktualna

### <a name="alert-message"></a>Komunikat alertu

*Nazwa główna usługi z identyfikatorem aplikacji "d87dcbc6-a371-462e-88e3-28ad15ec4e64" została usunięta, a następnie ponownie utworzona. Ponowne tworzenie jest pozostawiane za niespójnymi uprawnieniami do Azure AD Domain Services zasobów wymaganych do obsługi domeny zarządzanej. Może to wpłynąć na synchronizację haseł w domenie zarządzanej.*

Usługa Azure AD DS automatycznie synchronizuje konta użytkowników i poświadczenia z usługi Azure AD. Jeśli wystąpi problem z aplikacją usługi Azure AD używaną dla tego procesu, synchronizacja poświadczeń między usługą Azure AD DS i usługą Azure AD kończy się niepowodzeniem.

### <a name="resolution"></a>Rozwiązanie

Aby ponownie utworzyć aplikację usługi Azure AD służącą do synchronizacji poświadczeń, użyj programu Azure AD PowerShell, aby wykonać następujące czynności. Aby uzyskać więcej informacji, zobacz [Instalowanie programu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2).

1. W razie konieczności zainstaluj moduł Azure AD PowerShell i zaimportuj go w następujący sposób:

    ```powershell
    Install-Module AzureAD
    Import-Module AzureAD
    ```

2. Teraz usuń starą aplikację i obiekt za pomocą następujących poleceń cmdlet programu PowerShell:

    ```powershell
    $app = Get-AzureADApplication -Filter "IdentifierUris eq 'https://sync.aaddc.activedirectory.windowsazure.com'"
    Remove-AzureADApplication -ObjectId $app.ObjectId
    $spObject = Get-AzureADServicePrincipal -Filter "DisplayName eq 'Azure AD Domain Services Sync'"
    Remove-AzureADServicePrincipal -ObjectId $spObject
    ```

Po usunięciu obu aplikacji platforma Azure automatycznie odtworzy je i podejmie próbę wznowienia synchronizacji haseł. Kondycja domeny zarządzanej automatycznie aktualizuje się w ciągu dwóch godzin i usuwa alert.

## <a name="next-steps"></a>Następne kroki

Jeśli nadal masz problemy, [Otwórz żądanie pomocy technicznej platformy Azure][azure-support] , aby uzyskać dodatkową pomoc dotyczącą rozwiązywania problemów.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[New-AzureAdServicePrincipal]: /powershell/module/AzureAD/New-AzureADServicePrincipal
