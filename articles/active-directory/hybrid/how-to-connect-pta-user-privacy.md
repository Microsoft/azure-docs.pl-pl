---
title: Prywatność użytkowników i Azure Active Directory uwierzytelniania przekazywanego | Microsoft Docs
description: Ten artykuł dotyczy uwierzytelniania przekazywanego przez usługę Azure Active Directory (Azure AD) i zgodność Rodo.
services: active-directory
keywords: Azure AD Connect uwierzytelnianie przekazywane, Rodo, wymagane składniki usługi Azure AD, logowanie jednokrotne i logowanie jednokrotne
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89279520"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Prywatność użytkownika i uwierzytelnianie przekazywane usługi Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Omówienie

Uwierzytelnianie przekazywane przez usługę Azure AD tworzy następujący typ dziennika, który może zawierać dane osobowe:

- Pliki dziennika śledzenia Azure AD Connect.
- Pliki dziennika śledzenia agenta uwierzytelniania.
- Pliki dziennika zdarzeń systemu Windows.

Podwyższanie poziomu prywatności użytkowników w celu uwierzytelniania przekazywanego na dwa sposoby:

1.  Na żądanie Wyodrębnij dane dla osoby i Usuń z nich dane z tej osoby.
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zdecydowanie zalecamy, aby druga opcja była łatwiejsza do wdrożenia i konserwacji. Poniżej przedstawiono instrukcje dla każdego typu dziennika:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Usuń pliki dziennika śledzenia Azure AD Connect

Sprawdź zawartość folderu **%ProgramData%\AADConnect** i Usuń zawartość dziennika śledzenia (pliki**Trace- \* . log** ) tego folderu w ciągu 48 godzin od zainstalowania lub uaktualnienia Azure AD Connect lub modyfikacji konfiguracji uwierzytelniania przekazywanego, ponieważ ta akcja może utworzyć dane objęte Rodo.

>[!IMPORTANT]
>Nie usuwaj pliku **PersistedState.xml** w tym folderze, ponieważ ten plik jest używany do zachowywania stanu poprzedniej instalacji Azure AD Connect i jest używany podczas instalacji uaktualnienia. Ten plik nigdy nie będzie zawierał żadnych danych dotyczących osoby i nigdy nie powinien być usunięty.

Możesz przejrzeć i usunąć te pliki dzienników śledzenia za pomocą Eksploratora Windows lub użyć następującego skryptu programu PowerShell, aby wykonać niezbędne czynności:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Zapisz skrypt w pliku z ". Rozszerzenie PS1. Uruchom ten skrypt zgodnie z wymaganiami.

Aby dowiedzieć się więcej o powiązanych Azure AD Connect wymaganiach Rodo, zobacz [ten artykuł](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Usuwanie dzienników zdarzeń agenta uwierzytelniania

Ten produkt może również tworzyć **dzienniki zdarzeń systemu Windows**. Aby dowiedzieć się więcej, Przeczytaj [ten artykuł](/windows/win32/wes/windows-event-log).

Aby wyświetlić dzienniki powiązane z agentem uwierzytelniania przekazywanego, Otwórz aplikację **Podgląd zdarzeń** na serwerze i sprawdź w obszarze **Application and Service Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

### <a name="delete-authentication-agent-trace-log-files"></a>Usuń pliki dziennika śledzenia agenta uwierzytelniania

Należy regularnie sprawdzać zawartość **%ProgramData%\Microsoft\Azure uwierzytelniania programu AD Connect Agent\Trace** i usuwać zawartość tego folderu co 48 godzin. 

>[!IMPORTANT]
>Jeśli usługa agenta uwierzytelniania jest uruchomiona, nie będzie można usunąć bieżącego pliku dziennika w folderze. Zatrzymaj usługę przed ponowną próbą. Aby uniknąć błędów logowania użytkownika, należy wcześniej skonfigurować uwierzytelnianie przekazywane w celu zapewnienia [wysokiej dostępności](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows lub użyć następującego skryptu, aby wykonać niezbędne czynności:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Aby zaplanować uruchamianie skryptu co 48 godzin, wykonaj następujące kroki:

1.  Zapisz skrypt w pliku z ". Rozszerzenie PS1.
2.  Otwórz **Panel sterowania** , a następnie kliknij pozycję **system i zabezpieczenia**.
3.  W obszarze **Narzędzia administracyjne** kliknij pozycję "**Zaplanuj zadania**".
4.  W **harmonogram zadań**kliknij prawym przyciskiem myszy pozycję "**Biblioteka harmonogramu zadań**" i kliknij polecenie "**Utwórz zadanie podstawowe...**".
5.  Wprowadź nazwę nowego zadania i kliknij przycisk **dalej**.
6.  Wybierz pozycję "**codziennie**" dla **wyzwalacza zadania** , a następnie kliknij przycisk **dalej**.
7.  Ustaw cykl na dwa dni i kliknij przycisk **dalej**.
8.  Wybierz pozycję "**Uruchom program**" jako akcję, a następnie kliknij przycisk **dalej**.
9.  Wpisz "**PowerShell**" w polu dla programu/skryptu i w polu zatytułowanym "**Dodaj argumenty (opcjonalne)**" wprowadź pełną ścieżkę do utworzonego wcześniej skryptu, a następnie kliknij przycisk **dalej**.
10. Na następnym ekranie są wyświetlane podsumowanie zadania, które chcesz utworzyć. Sprawdź wartości i kliknij przycisk **Zakończ** , aby utworzyć zadanie:
 
### <a name="note-about-domain-controller-logs"></a>Uwaga na temat dzienników kontrolera domeny

W przypadku włączenia rejestrowania inspekcji ten produkt może generować dzienniki zabezpieczeń dla kontrolerów domeny. Aby dowiedzieć się więcej o konfigurowaniu zasad inspekcji, przeczytaj ten [artykuł](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności informacji firmy Microsoft w centrum zaufania](https://www.microsoft.com/trustcenter)
* [**Rozwiązywanie problemów**](tshoot-connect-pass-through-authentication.md) — Dowiedz się, jak rozwiązywać typowe problemy z funkcją.