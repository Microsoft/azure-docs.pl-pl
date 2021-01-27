---
title: Wyłącz PTA w przypadku korzystania z Azure AD Connect "nie Konfiguruj" | Microsoft Docs
description: W tym artykule opisano sposób wyłączenia PTA z użyciem funkcji "nie Konfiguruj" Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/20/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26112b1e799cbde3145e7137c686b4b336db4bab
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919939"
---
# <a name="disable-pta-when-using-azure-ad-connect"></a>Wyłącz PTA przy użyciu Azure AD Connect

Jeśli używasz uwierzytelniania przekazywanego z Azure AD Connect i ustawiono opcję **"nie Konfiguruj"**, możesz ją wyłączyć. 

>[!NOTE]
>Jeśli PHS już włączona, wyłączenie PTA spowoduje powrót do PHS dzierżawcy.

Wyłączenie PTA można wykonać przy użyciu następujących poleceń cmdlet. 

## <a name="prerequisites"></a>Wymagania wstępne
Wymagane są następujące wymagania wstępne:
- Wszystkie maszyny z systemem Windows, na których jest zainstalowany agent PTA. 
- Agent musi być w wersji 1.5.1742.0 lub nowszej. 
- Konto administratora globalnego platformy Azure w celu uruchomienia poleceń cmdlet programu PowerShell w celu wyłączenia PTA.

>[!NOTE]
> Jeśli Agent jest starszy, może nie mieć poleceń cmdlet wymaganych do ukończenia tej operacji. Nowy Agent można pobrać z witryny Azure Portal, a następnie zainstalować go na dowolnym komputerze z systemem Windows i podać poświadczenia administratora. (Zainstalowanie agenta nie ma wpływu na stan PTA w chmurze)

> [!IMPORTANT]
> Jeśli używasz chmury Azure Government, musisz przekazać parametr ENVIRONMENTname o następującej wartości. 
>
>| Nazwa środowiska | Chmura |
>| - | - |
>| AzureUSGovernment | US Gov|


## <a name="to-disable-pta"></a>Aby wyłączyć PTA
W ramach sesji programu PowerShell Użyj następujących poleceń, aby wyłączyć PTA:
1. Agent uwierzytelniania PS C:\Program Files\Microsoft Azure AD Connect> `Import-Module .\Modules\PassthroughAuthPSModule`
2. `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth` lub `Get-PassthroughAuthenticationEnablementStatus -Feature PassthroughAuth -EnvironmentName <identifier>`
3. `Disable-PassthroughAuthentication  -Feature PassthroughAuth` lub `Disable-PassthroughAuthentication -Feature PassthroughAuth -EnvironmentName <identifier>`

## <a name="if-you-dont-have-access-to-an-agent"></a>Jeśli nie masz dostępu do agenta

Jeśli nie masz maszyny agenta, możesz użyć następującego polecenia, aby zainstalować agenta.

1. Pobierz najnowszego agenta uwierzytelniania z portal.azure.com.
2. Zainstaluj funkcję: `.\AADConnectAuthAgentSetup.exe` lub `.\AADConnectAuthAgentSetup.exe ENVIRONMENTNAME=<identifier>`


## <a name="next-steps"></a>Następne kroki

- [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)
