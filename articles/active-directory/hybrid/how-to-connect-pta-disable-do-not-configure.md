---
title: Wyłącz pta podczas korzystania z usługi Azure AD Connect "Nie konfiguruj" | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób wyłączania pta za pomocą funkcji "nie konfiguruj" usługi Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa1046dc64fed3edb6c9d04f76a96f488769ff42
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726802"
---
# <a name="disable-pta-when-using-azure-ad-connect-do-not-configure"></a>Wyłącz PTA podczas korzystania z usługi Azure AD Connect "Nie konfiguruj"

Jeśli używasz uwierzytelniania przekazywanego za pomocą usługi Azure AD Connect i masz go ustawić na "Nie konfiguruj", można go wyłączyć. Wyłączenie PTA można wykonać za pomocą następujących poleceń cmdlet. 

## <a name="prerequisites"></a>Wymagania wstępne
Wymagane są następujące wymagania wstępne:
- Każdy komputer z systemem Windows, który ma agenta PTA zainstalowany. 
- Agent musi być w wersji 1.5.1742.0 lub nowszej. 
- Konto administratora globalnego platformy Azure w celu uruchomienia poleceń cmdlet programu PowerShell w celu wyłączenia pta.

>[!NOTE]
> Jeśli agent jest starszy, może nie mieć poleceń cmdlet wymaganych do wykonania tej operacji. Możesz uzyskać nowego agenta z usługi Azure Portal zainstalować go na dowolnym komputerze z systemem Windows i podać poświadczenia administratora. (Instalacja agenta nie wpływa na stan PTA w chmurze)

> [!IMPORTANT]
> Jeśli używasz chmury azure dla instytucji rządowych, a następnie trzeba będzie przekazać w ENVIRONMENTNAME parametr z następującą wartością. 
>
>| Nazwa środowiska | Chmura |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Aby wyłączyć PTA
Z poziomu sesji programu PowerShell należy wyłączyć pta następujące elementy:
1. PS C:\Pliki programów\Agent uwierzytelniania usługi Microsoft Azure AD Connect>`Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` lub `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` lub `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Jeśli nie masz dostępu do agenta

Jeśli nie masz komputera agenta, możesz użyć następującego polecenia, aby zainstalować agenta.

1. Pobierz najnowszy agent Auth z portal.azure.com.
2. Zainstaluj funkcję: `.\AADConnectAuthAgentSetup.exe` lub`.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Następne kroki

- [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)