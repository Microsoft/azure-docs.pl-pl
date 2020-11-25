---
title: 'Azure AD Connect: co to jest moduł ADConnectivityTool programu PowerShell | Microsoft Docs'
description: Ten dokument zawiera wprowadzenie do nowego modułu programu ADConnectivity PowerShell i sposobu jego użycia w celu ułatwienia rozwiązywania problemów.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973460"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Rozwiązywanie problemów z łącznością z usługą Azure AD za pomocą modułu ADConnectivityTool PowerShell

Narzędzie ADConnectivity jest modułem programu PowerShell, który jest używany w jednej z następujących czynności:

- Podczas instalacji, gdy problem z łącznością sieciową uniemożliwia pomyślne sprawdzenie poprawności poświadczeń Active Directory przez użytkownika w kreatorze.
- Po instalacji przez użytkownika, który wywołuje funkcje z sesji programu PowerShell.

Narzędzie znajduje się w folderze: **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool. PSM1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool podczas instalacji

Na stronie **łączenie katalogów** w Kreatorze Azure AD Connect, jeśli wystąpi problem z siecią, ADConnectivityTool automatycznie użyje jednej z funkcji, aby określić, co się dzieje.  W przypadku problemów z siecią mogą być brane pod uwagę następujące problemy:

- Nazwa lasu podana przez użytkownika została nieprawidłowo wpisana lub wymieniony Las nie istnieje 
- Port UDP 389 jest zamknięty na kontrolerach domeny skojarzonych z lasem udostępnionym przez użytkownika
- Poświadczenia podane w oknie "konto lasu AD" nie mają uprawnień do pobierania kontrolerów domeny skojarzonych z lasem docelowym
- Każdy z portów TCP 53, 88 lub 389 jest zamknięty na kontrolerach domeny skojarzonych z lasem udostępnionym przez użytkownika 
- Porty UDP 389 i port TCP (lub portów) są zamknięte
- Nie można rozpoznać systemu DNS dla podanego lasu and\or skojarzonego z nim kontrolerów domeny

Za każdym razem, gdy zostaną znalezione jakiekolwiek z tych problemów, w Kreatorze AADConnect zostanie wyświetlony powiązany komunikat o błędzie:


![Błąd](media/how-to-connect-adconnectivitytools/error1.png)

Na przykład, gdy próbujemy dodać katalog na ekranie **Połącz katalogi** , Azure AD Connect musi on sprawdzić i oczekiwać, że może komunikować się z kontrolerem domeny przez port 389.  Jeśli nie, zostanie wyświetlony komunikat o błędzie, który jest wyświetlany na powyższym zrzucie ekranu.  

To właśnie zdarza się w tle, ponieważ Azure AD Connect wywołuje `Start-NetworkConnectivityDiagnosisTools` funkcję.  Ta funkcja jest wywoływana, gdy weryfikacja poświadczeń nie powiedzie się z powodu problemu z łącznością sieciową.

Na koniec, po każdym wywołaniu narzędzia z kreatora zostanie wygenerowany szczegółowy plik dziennika. Dziennik znajduje się w **C:\ProgramData\AADConnect\ADConnectivityTool- \<date> - \<time> . log**

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools po instalacji
Po zainstalowaniu Azure AD Connect można użyć dowolnej funkcji w module ADConnectivityTools PowerShell.  

Informacje referencyjne dotyczące funkcji można znaleźć w [dokumentacji ADConnectivityTools](reference-connect-adconnectivitytools.md)

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Zamierzamy wywołać tę funkcję, **ponieważ może ona** zostać wywołana ręcznie po zaimportowaniu ADConnectivityTool. Psm1 do programu PowerShell. 

Ta funkcja wykonuje tę samą logikę, która jest uruchamiana przez kreatora Azure AD Connect, aby sprawdzić poprawność podanych poświadczeń usługi AD.  Zapewnia to jednak bardziej szczegółowe wyjaśnienie problemu i sugerowane rozwiązanie. 

Weryfikacja łączności obejmuje następujące kroki:
-   Pobierz nazwę FQDN domeny (w pełni kwalifikowana nazwa domeny)
-   Sprawdź, czy jeśli użytkownik wybrał opcję "Utwórz nowe konto usługi AD", te poświadczenia należą do grupy administratorów przedsiębiorstwa
-   Pobierz obiekt nazwy FQDN lasu
-   Upewnij się, że co najmniej jedna domena skojarzona z wcześniej uzyskanym obiektem FQDN lasu jest osiągalna
-   Sprawdź, czy poziom funkcjonalności lasu to Windows Server 2003 lub nowszy.

Użytkownik będzie mógł dodać katalog, jeśli wszystkie te akcje zostały wykonane pomyślnie.

Jeśli użytkownik uruchamia tę funkcję po rozwiązaniu problemu (lub jeśli w ogóle nie istnieje żaden problem), dane wyjściowe będą wskazywać, że użytkownik powróci do kreatora Azure AD Connect i spróbuje ponownie wstawić poświadczenia.



## <a name="next-steps"></a>Następne kroki
- [Azure AD Connect: Konta i uprawnienia](reference-connect-accounts-permissions.md)
- [Instalacja ekspresowa](how-to-connect-install-express.md)
- [Instalacja niestandardowa](how-to-connect-install-custom.md)
- [Informacje o module ADConnectivityTools](reference-connect-adconnectivitytools.md)

