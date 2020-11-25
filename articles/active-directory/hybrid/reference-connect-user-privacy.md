---
title: Azure AD Connect i prywatność użytkowników | Microsoft Docs
description: W tym dokumencie opisano sposób uzyskiwania Rodo zgodności z Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1d1bf35d03f0f7c4d11e4cc7e9b6f98f38d8ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997702"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Prywatność użytkowników i Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Ten artykuł dotyczy Azure AD Connect i poufności użytkowników.  Informacje dotyczące Azure AD Connect Health i prywatności użytkowników znajdują się w [tym](reference-connect-health-user-privacy.md)artykule.

Zwiększ prywatność użytkowników w przypadku instalacji Azure AD Connect na dwa sposoby:

1.  Na żądanie Wyodrębnij dane dla osoby i Usuń z nich dane z instalacji
2.  Upewnij się, że żadne dane nie są przechowywane dłużej niż 48 godzin.

Zespół Azure AD Connect zaleca drugą opcję, ponieważ jest znacznie łatwiejszy do wdrożenia i konserwacji.

Serwer synchronizacji Azure AD Connect przechowuje następujące dane dotyczące prywatności użytkownika:
1.  Dane dotyczące osoby w **bazie danych Azure AD Connect**
2.  Dane w plikach **dziennika zdarzeń systemu Windows** , które mogą zawierać informacje o osobie
3.  Dane w **plikach dziennika instalacji Azure AD Connect** , które mogą zawierać informacje o osobie

W przypadku usuwania danych użytkownika Azure AD Connect klienci powinni przestrzegać następujących wytycznych:
1.  Usuń zawartość folderu, który regularnie zawiera pliki dziennika instalacji Azure AD Connect — co najmniej co 48 godzin
2.  Ten produkt może również tworzyć dzienniki zdarzeń.  Aby dowiedzieć się więcej na temat dzienników dzienników zdarzeń, zapoznaj się z [dokumentacją w tym miejscu](/windows/win32/wes/windows-event-log).

Dane dotyczące osoby są automatycznie usuwane z bazy danych Azure AD Connect, gdy dane tej osoby zostaną usunięte z systemu źródłowego, z którego pochodzą. Żadna konkretna akcja od administratorów nie musi być zgodna z Rodo.  Jednak wymaga to, aby dane Azure AD Connect były synchronizowane ze źródłem danych co najmniej co dwa dni.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Usuń zawartość folderu pliku dziennika instalacji Azure AD Connect
Regularnie sprawdzaj i usuwaj zawartość folderu **c:\programdata\aadconnect** — z wyjątkiem pliku **PersistedState.Xml** . Ten plik przechowuje stan poprzedniej instalacji usługi Azure A Connect i jest używany podczas instalacji uaktualnienia. Ten plik nie zawiera żadnych danych dotyczących osoby i nie należy go usuwać.

>[!IMPORTANT]
>Nie usuwaj pliku PersistedState.xml.  Ten plik nie zawiera informacji o użytkowniku i utrzymuje stan poprzedniej instalacji.

Możesz przejrzeć i usunąć te pliki przy użyciu Eksploratora Windows lub użyć skryptu, takiego jak następujące, aby wykonać niezbędne czynności:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Zaplanuj uruchamianie tego skryptu co 48 godzin
Wykonaj następujące kroki, aby zaplanować uruchamianie skryptu co 48 godzin.

1.  Zapisz skrypt w pliku o rozszerzeniu **&#46;ps1**, a następnie otwórz Panel sterowania, a następnie kliknij pozycję **systemy i zabezpieczenia**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  W obszarze Narzędzia administracyjne kliknij pozycję **Zaplanuj zadania**.
    ![Zadanie podrzędne](./media/reference-connect-user-privacy/gdpr3.png)
3.  W Harmonogram zadań kliknij prawym przyciskiem myszy **bibliotekę harmonogram zadań** , a następnie kliknij pozycję **Utwórz zadanie podstawowe...**
4.  Wprowadź nazwę nowego zadania i kliknij przycisk **dalej**.
5.  Wybierz pozycję **codziennie** dla wyzwalacza zadania i kliknij przycisk **dalej**.
6.  Ustaw cykl na **2 dni** i kliknij przycisk **dalej**.
7.  Wybierz pozycję **Uruchom program** jako akcję, a następnie kliknij przycisk **dalej**.
8.  Wpisz polecenie **PowerShell** w polu dla programu/skryptu i w polu z etykietą **Dodaj argumenty (opcjonalnie)** wprowadź pełną ścieżkę do utworzonego wcześniej skryptu, a następnie kliknij przycisk **dalej**.
9.  Na następnym ekranie są wyświetlane podsumowanie zadania, które chcesz utworzyć. Sprawdź wartości i kliknij przycisk **Zakończ** , aby utworzyć zadanie.



## <a name="next-steps"></a>Następne kroki
* [Zapoznaj się z zasadami zachowania poufności informacji firmy Microsoft w centrum zaufania](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health i prywatność użytkowników](reference-connect-health-user-privacy.md)