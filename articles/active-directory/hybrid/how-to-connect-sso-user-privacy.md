---
title: Prywatność użytkowników i bezproblemowe pojedyncze Sign-On usługi Azure AD | Microsoft Docs
description: W tym artykule poznasz Azure Active Directory (Azure AD) bezproblemowe logowanie jednokrotne i zgodność Rodo.
services: active-directory
keywords: Co to jest Azure AD Connect, Rodo, wymagane składniki usługi Azure AD, logowanie jednokrotne i logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa1a30c548ef60cd9b596031f4115297dd20844
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89278602"
---
# <a name="user-privacy-and-azure-ad-seamless-single-sign-on"></a>Prywatność użytkownika i bezproblemowe logowanie jednokrotne do usługi Azure AD

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie


Bezproblemowy Logowanie jednokrotne w usłudze Azure AD tworzy następujący typ dziennika, który może zawierać dane osobowe: 

- Pliki dziennika śledzenia Azure AD Connect.

Zwiększ prywatność użytkowników w celu zapewnienia bezproblemowego logowania jednokrotnego na dwa sposoby:

1.  Na żądanie Wyodrębnij dane dla osoby i Usuń z nich dane z tej osoby.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zdecydowanie zalecamy, aby druga opcja była łatwiejsza do wdrożenia i konserwacji. Zobacz następujące instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuń pliki dziennika śledzenia Azure AD Connect

Sprawdź zawartość folderu **%ProgramData%\AADConnect** i Usuń zawartość dziennika śledzenia (pliki **Trace- \* . log** ) tego folderu w ciągu 48 godzin od zainstalowania lub uaktualnienia Azure AD Connect lub zmodyfikowania bezproblemowej konfiguracji logowania jednokrotnego, ponieważ ta akcja może utworzyć dane objęte Rodo.

>[!IMPORTANT]
>Nie usuwaj pliku **PersistedState.xml** w tym folderze, ponieważ ten plik jest używany do zachowywania stanu poprzedniej instalacji Azure AD Connect i jest używany podczas instalacji uaktualnienia. Ten plik nigdy nie będzie zawierał żadnych danych dotyczących osoby i nigdy nie powinien być usunięty.

Możesz przejrzeć i usunąć te pliki dzienników śledzenia za pomocą Eksploratora Windows lub użyć następującego skryptu programu PowerShell, aby wykonać niezbędne czynności:

```powershell
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". Rozszerzenie PS1. Uruchom ten skrypt zgodnie z wymaganiami.

Aby dowiedzieć się więcej o powiązanych Azure AD Connect wymaganiach Rodo, zobacz [ten artykuł](reference-connect-user-privacy.md).

### <a name="note-about-domain-controller-logs"></a>Uwaga na temat dzienników kontrolera domeny

W przypadku włączenia rejestrowania inspekcji ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej o konfigurowaniu zasad inspekcji, przeczytaj ten [artykuł](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Następne kroki

* [Zapoznaj się z zasadami zachowania poufności informacji firmy Microsoft w centrum zaufania](https://www.microsoft.com/trustcenter)
  - [**Rozwiązywanie problemów**](tshoot-connect-sso.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.
  - [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) — w przypadku zgłaszania nowych żądań funkcji.