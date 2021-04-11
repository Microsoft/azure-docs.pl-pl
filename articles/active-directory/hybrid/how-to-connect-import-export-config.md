---
title: Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect
description: W tym artykule opisano często zadawane pytania dotyczące aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e2bdaa2c7a7648124fbe0be60e5a0af2f83238f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226565"
---
# <a name="import-and-export-azure-ad-connect-configuration-settings"></a>Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect 

Wdrożenia programu Azure Active Directory (Azure AD) Connect różnią się w zależności od instalacji w trybie ekspresowym pojedynczego lasu do złożonych wdrożeń, które są synchronizowane w wielu lasach przy użyciu niestandardowych reguł synchronizacji. Ze względu na dużą liczbę opcji i mechanizmów konfiguracji niezbędne jest zrozumienie, jakie ustawienia są stosowane, i możliwość szybkiego wdrożenia serwera z identyczną konfiguracją. Ta funkcja wprowadza możliwość wykazania konfiguracji danego serwera synchronizacji i importowania ustawień do nowego wdrożenia. Różne migawki ustawień synchronizacji można porównać w celu łatwego wizualizowania różnic między dwoma serwerami lub tego samego serwera w czasie.

Za każdym razem, gdy konfiguracja zostanie zmieniona w Kreatorze Azure AD Connect, nowy plik ustawień JSON z sygnaturami czasowymi jest automatycznie eksportowany do **%ProgramData%\AADConnect**. Nazwa pliku ustawień ma postać **zastosowana-SynchronizationPolicy-*. JSON**, gdzie Ostatnia część nazwy pliku jest sygnaturą czasową.

> [!IMPORTANT]
> Tylko zmiany wprowadzone przez Azure AD Connect są automatycznie eksportowane. Wszelkie zmiany wprowadzone przy użyciu programu PowerShell, Synchronization Service Manager lub Edytor reguł synchronizacji należy wyeksportować na żądanie w zależności od potrzeb, aby zachować aktualną kopię. Eksport na żądanie może również służyć do umieszczania kopii ustawień w bezpiecznej lokalizacji na potrzeby odzyskiwania po awarii.

## <a name="export-azure-ad-connect-settings"></a>Eksportuj ustawienia Azure AD Connect 

Aby wyświetlić podsumowanie ustawień konfiguracji, Otwórz narzędzie Azure AD Connect i wybierz dodatkowe zadanie o nazwie **Widok lub Eksportuj bieżącą konfigurację**. Krótkie podsumowanie ustawień jest wyświetlane wraz z możliwością eksportowania pełnej konfiguracji serwera.

Domyślnie ustawienia są eksportowane do **%ProgramData%\AADConnect**. Można również zapisać ustawienia w chronionej lokalizacji, aby zapewnić dostępność w przypadku wystąpienia awarii. Ustawienia są eksportowane przy użyciu formatu pliku JSON i nie powinny być tworzone ręcznie ani edytowane w celu zapewnienia spójności logicznej. Importowanie ręcznie utworzonego lub edytowanego pliku nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników.

## <a name="import-azure-ad-connect-settings"></a>Importuj ustawienia Azure AD Connect

Aby zaimportować wcześniej wyeksportowane ustawienia:
 
1. Zainstaluj **Azure AD Connect** na nowym serwerze.
1. Wybierz opcję **Dostosuj** po stronie **powitalnej** .
1. Wybierz pozycję **Importuj ustawienia synchronizacji**. Przeglądaj w poszukiwaniu wcześniej wyeksportowanego pliku ustawień JSON.
1. Wybierz pozycję **Zainstaluj**.

   ![Zrzut ekranu pokazujący ekran Zainstaluj składniki wymagane](media/how-to-connect-import-export-config/import-1.png)

> [!NOTE]
> Zastąp ustawienia na tej stronie, takie jak użycie SQL Server zamiast LocalDB lub użycie istniejącego konta usługi zamiast domyślnego atrybutu VSA. Te ustawienia nie są importowane z pliku ustawień konfiguracji. Są one przeznaczone do celów informacyjnych i porównawczych.

### <a name="import-installation-experience"></a>Importowanie środowiska instalacji 

Środowisko instalacji importu jest celowo proste, a minimalne dane wejściowe użytkownika mogą łatwo zapewnić odtwarzalność istniejącego serwera.

Poniżej przedstawiono jedyne zmiany, które można wprowadzić podczas instalacji. Wszystkie inne zmiany można wykonać po instalacji za pomocą Kreatora Azure AD Connect:
- **Poświadczenia Azure Active Directory**: nazwa konta administratora globalnego platformy Azure używana do konfigurowania oryginalnego serwera jest sugerowana domyślnie. *Należy* ją   zmienić, jeśli chcesz synchronizować informacje z nowym katalogiem.
- **Logowanie użytkownika**: opcje logowania skonfigurowane dla oryginalnego serwera są domyślnie wybrane i automatycznie monitują o poświadczenia lub inne informacje, które są potrzebne podczas konfigurowania. W rzadkich przypadkach może być konieczne skonfigurowanie serwera z różnymi opcjami, aby uniknąć zmiany zachowania aktywnego serwera. W przeciwnym razie wybierz pozycję **dalej** , aby użyć tych samych ustawień.
- **Poświadczenia katalogu lokalnego**: dla każdego katalogu lokalnego dołączonego do ustawień synchronizacji należy podać poświadczenia, aby utworzyć konto synchronizacji lub podać wstępnie utworzone konto synchronizacji niestandardowej. Ta procedura jest taka sama jak w przypadku czystej instalacji z wyjątkiem tego, że nie można dodawać ani usuwać katalogów.
- **Opcje konfiguracji**: podobnie jak w przypadku czystej instalacji, można skonfigurować początkowe ustawienia, czy należy uruchomić automatyczną synchronizację czy włączyć tryb przejściowy. Główną różnicą jest to, że tryb przejściowy jest celowo domyślnie włączony, aby umożliwić porównanie wyników konfiguracji i synchronizacji przed aktywnie eksportowaniem wyników do platformy Azure.

![Zrzut ekranu przedstawiający ekran Łączenie katalogów](media/how-to-connect-import-export-config/import-2.png)

> [!NOTE]
> Tylko jeden serwer synchronizacji może należeć do roli głównej i aktywnie eksportować zmiany konfiguracji na platformie Azure. Wszystkie inne serwery muszą być umieszczone w trybie przejściowym.

## <a name="migrate-settings-from-an-existing-server"></a>Migrowanie ustawień z istniejącego serwera 

Jeśli istniejący serwer nie obsługuje zarządzania ustawieniami, możesz wybrać opcję uaktualnienia serwera w miejscu lub przeprowadzenia migracji ustawień do użycia na nowym serwerze tymczasowym.

Migracja wymaga uruchomienia skryptu programu PowerShell, który wyodrębnia istniejące ustawienia do użycia w nowej instalacji.Użyj tej metody, aby wykazać ustawienia istniejącego serwera, a następnie zastosować je na nowo zainstalowanym serwerze przemieszczania.Porównanie ustawień oryginalnego serwera z nowo utworzonym serwerem szybko wizualizuje zmiany między serwerami.Jak zawsze, postępuj zgodnie z procesem certyfikacji organizacji, aby upewnić się, że nie jest wymagana żadna dodatkowa konfiguracja.

### <a name="migration-process"></a>Proces migracji 
Aby przeprowadzić migrację ustawień:

 1. Rozpocznij **AzureADConnect.msi** na nowym serwerze tymczasowym i Zatrzymaj na stronie **powitalnej** Azure AD Connect.

 2. Skopiuj **MigrateSettings.ps1** z katalogu Microsoft Azure AD Connect\Tools do lokalizacji na istniejącym serwerze. Przykładem jest C:\setup, gdzie Instalator jest katalogiem, który został utworzony na istniejącym serwerze.</br>
     ![Zrzut ekranu przedstawiający katalogi Azure AD Connect.](media/how-to-connect-import-export-config/migrate-1.png)

     >[!NOTE]
     > Jeśli zostanie wyświetlony komunikat: "nie można odnaleźć parametru pozycyjnego akceptującego argument **true**.", jak pokazano poniżej:
     >
     >
     >![Zrzut ekranu przedstawiający błąd ](media/how-to-connect-import-export-config/migrate-5.png) , edytuj plik MigrateSettings.ps1 i usuń **$true** i uruchom skrypt: ![ zrzut ekranu, aby edytować konfigurację](media/how-to-connect-import-export-config/migrate-6.png)
 



 3. Uruchom skrypt jak pokazano poniżej i Zapisz cały katalog konfiguracji serwera niższego poziomu. Skopiuj ten katalog do nowego serwera przemieszczania. Należy skopiować cały folder **wyeksportowany-konfiguracja serwera-*** na nowy serwer.
     ![Zrzut ekranu pokazujący skrypt w programie Windows PowerShell. ](media/how-to-connect-import-export-config/migrate-2.png)![ Zrzut ekranu pokazujący Kopiowanie folderu Export-konfiguracja serwera-*.](media/how-to-connect-import-export-config/migrate-3.png)

 4. Rozpocznij **Azure AD Connect** przez dwukrotne kliknięcie ikony na pulpicie. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft, a następnie na następnej stronie wybierz pozycję **Dostosuj**.
 5. Zaznacz pole wyboru **Importuj ustawienia synchronizacji** . Wybierz przycisk **Przeglądaj** , aby przeglądać folder skopiowane przez eksportowany-konfiguracja serwera-*. Wybierz MigratedPolicy.js, aby zaimportować zmigrowane ustawienia.

     ![Zrzut ekranu pokazujący opcję importowania ustawień synchronizacji.](media/how-to-connect-import-export-config/migrate-4.png)

## <a name="post-installation-verification"></a>Weryfikacja po instalacji 

Porównanie pierwotnie zaimportowanego pliku ustawień z wyeksportowanym plikiem ustawień nowo wdrożonego serwera jest istotnym krokiem w zrozumieniu różnic między zamierzonymi a powstałymi wdrożeniami. Korzystanie z ulubionej aplikacji do porównywania tekstu obok siebie daje błyskawiczną wizualizację, która szybko wyróżnia wszystkie żądane lub przypadkowe zmiany.

Mimo że wiele wcześniej kroków konfiguracji ręcznej jest teraz eliminowanych, nadal należy przestrzegać procesu certyfikacji organizacji, aby upewnić się, że nie jest wymagana żadna dodatkowa konfiguracja. Taka konfiguracja może wystąpić w przypadku korzystania z ustawień zaawansowanych, które nie są obecnie przechwytywane w tej wersji zarządzania ustawieniami.

Poniżej przedstawiono znane ograniczenia:
- **Reguły synchronizacji**: pierwszeństwo dla reguły niestandardowej musi mieć zarezerwowany zakres od 0 do 99, aby uniknąć konfliktów ze standardowymi regułami firmy Microsoft. Umieszczenie reguły niestandardowej poza zarezerwowanym zakresem może spowodować, że reguła niestandardowa zostanie przesunięta w taki sposób, jak reguły standardowe zostaną dodane do konfiguracji. Podobny problem wystąpi, jeśli konfiguracja zawiera zmodyfikowane standardowe reguły. Nie zaleca się modyfikowania reguły standardowej, a położenie reguły jest prawdopodobnie nieprawidłowe.
- **Zapisywanie zwrotne urządzeń**: te ustawienia są w wykazie. Nie są one obecnie stosowane podczas konfigurowania. Jeśli funkcja zapisywania zwrotnego urządzeń została włączona dla oryginalnego serwera, należy ręcznie skonfigurować tę funkcję na nowo wdrożonym serwerze.
- **Synchronizowane typy obiektów**: Chociaż istnieje możliwość ograniczenia listy synchronizowanych typów obiektów (takich jak użytkownicy, kontakty i grupy) za pomocą Synchronization Service Manager, ta funkcja nie jest obecnie obsługiwana przez ustawienia synchronizacji. Po zakończeniu instalacji należy ręcznie ponownie zastosować konfigurację zaawansowaną.
- **Niestandardowe profile uruchamiania**: Chociaż istnieje możliwość modyfikacji domyślnego zestawu uruchamiania profilów przy użyciu Synchronization Service Manager, ta funkcja nie jest obecnie obsługiwana przez ustawienia synchronizacji. Po zakończeniu instalacji należy ręcznie ponownie zastosować konfigurację zaawansowaną.
- **Konfigurowanie hierarchii aprowizacji**: Ta zaawansowana funkcja Synchronization Service Manager nie jest obsługiwana przez ustawienia synchronizacji. Należy ręcznie zmienić konfigurację po zakończeniu wstępnego wdrożenia.
- **Active Directory Federation Services (AD FS) i uwierzytelnianie serwera pingfederate**: metody logowania skojarzone z tymi funkcjami uwierzytelniania są automatycznie wybierane. Musisz interaktywnie dostarczyć wszystkie inne wymagane parametry konfiguracji.
- **Wyłączona reguła synchronizacji niestandardowej zostanie zaimportowana jako włączona**: wyłączona reguła synchronizacji niestandardowej zostanie zaimportowana jako włączona. Upewnij się, że jest ona również wyłączona na nowym serwerze.

 ## <a name="next-steps"></a>Następne kroki

- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)
- [Instalowanie agentów programu Azure AD Connect Health](how-to-connect-health-agent-install.md) 
