---
title: Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect
description: Ten dokument zawiera opis często zadawanych pytań dotyczących aprowizacji w chmurze.
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
ms.openlocfilehash: a94d356cb3c0345f575b4b5a44aa7f228535e66d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87019883"
---
# <a name="importing-and-exporting-azure-ad-connect-configuration-settings-public-preview"></a>Importowanie i eksportowanie ustawień konfiguracji Azure AD Connect (publiczna wersja zapoznawcza) 

Azure AD Connect wdrożenia różnią się w zależności od instalacji w trybie ekspresowym pojedynczego lasu, do złożonych wdrożeń, które są synchronizowane w wielu lasach przy użyciu niestandardowych reguł synchronizacji. Ze względu na dużą liczbę opcji konfiguracji i mechanizmów, ważne jest, aby zrozumieć, jakie ustawienia są stosowane, i móc szybko wdrożyć serwer z identyczną konfiguracją. Ta funkcja wprowadza możliwość wykazania konfiguracji danego serwera synchronizacji i importowania ustawień do nowego wdrożenia. Różne migawki ustawień synchronizacji można porównać w celu łatwego wizualizowania różnic między dwoma serwerami lub tego samego serwera w czasie. 

Za każdym razem, gdy konfiguracja zostanie zmieniona w Kreatorze Azure AD Connect, nowy plik ustawień JSON z sygnaturami czasowymi jest automatycznie eksportowany do **%ProgramData%\AADConnect**. Nazwa pliku ustawień ma postać **zastosowana-SynchronizationPolicy-*. JSON** , gdzie Ostatnia część nazwy pliku jest sygnaturą czasową. 

> [!IMPORTANT]
> Tylko zmiany wprowadzone przez Azure AD Connect są automatycznie eksportowane. Wszelkie zmiany wprowadzone przy użyciu programu PowerShell, Synchronization Service Manager lub edytora reguł synchronizacji należy wyeksportować na żądanie w zależności od potrzeb, aby zachować aktualną kopię. Eksport na żądanie może również służyć do umieszczania kopii ustawień w bezpiecznej lokalizacji na potrzeby odzyskiwania po awarii. 

## <a name="exporting-azure-ad-connect-settings"></a>Eksportowanie ustawień Azure AD Connect 

Aby wyświetlić podsumowanie ustawień konfiguracji, Otwórz narzędzie Azure AD Connect i wybierz dodatkowe zadanie o nazwie: Wyświetl lub Eksportuj bieżącą konfigurację. Krótkie podsumowanie ustawień jest wyświetlane wraz z możliwością eksportowania pełnej konfiguracji serwera. 

Domyślnie ustawienia zostaną wyeksportowane do **%ProgramData%\AADConnect**, jednak można zapisać ustawienia w chronionej lokalizacji, aby zapewnić dostępność w razie awarii. Ustawienia są eksportowane przy użyciu formatu pliku JSON i nie powinny być ręcznie tworzone ani edytowane w celu zapewnienia spójności logicznej. Importowanie ręcznie utworzonych lub edytowanych plików nie jest obsługiwane i może prowadzić do nieoczekiwanych wyników. 

## <a name="importing-azure-ad-connect-settings"></a>Importowanie ustawień Azure AD Connect 

Aby zaimportować wcześniej wyeksportowane ustawienia, wykonaj następujące czynności:
 
1. Zainstaluj **Azure AD Connect** na nowym serwerze. 
2. Wybierz opcję **Dostosuj** po stronie **powitalnej** . 
3. Kliknij pozycję **Importuj ustawienia synchronizacji**. Przeglądaj w poszukiwaniu wcześniej wyeksportowanego pliku ustawień JSON.  
4. Kliknij przycisk **Zainstaluj**.

![Zainstaluj składniki](media/how-to-connect-import-export-config/import1.png)

> [!NOTE]
> Zastąp ustawienia na tej stronie, takie jak użycie SQL Server zamiast LocalDB lub użycie istniejącego konta usługi zamiast domyślnego atrybutu VSA itp. Te ustawienia nie są importowane z pliku ustawień konfiguracji, ale są przeznaczone do celów informacyjnych i porównawczych.

### <a name="import-installation-experience"></a>Importowanie środowiska instalacji 

Środowisko instalacji importu jest celowo proste dzięki minimalnym nakładom danych przez użytkownika, co pozwala na łatwe zapewnianie odtwarzalności istniejącego serwera.  

Poniżej przedstawiono jedyne zmiany, które można wprowadzić podczas instalacji. Wszystkie inne zmiany można wykonać po zakończeniu instalacji w Kreatorze Azure AD Connect. 
- **Poświadczenia Azure Active Directory**   — Nazwa konta administratora globalnego platformy Azure używana do konfigurowania oryginalnego serwera jest sugerowana domyślnie i **należy**   ją zmienić, jeśli chcesz synchronizować informacje z nowym katalogiem.
- **Logowanie użytkownika**   — Opcje logowania skonfigurowane dla oryginalnego serwera są domyślnie wybrane i automatycznie monitują o poświadczenia lub inne informacje potrzebne podczas konfigurowania. W rzadkich przypadkach może być konieczne skonfigurowanie serwera z różnymi opcjami, aby uniknąć zmiany zachowania aktywnego serwera. W przeciwnym razie naciśnij pozycję Dalej, aby użyć tych samych ustawień. 
- **Poświadczenia**   katalogu lokalnego — dla każdego katalogu lokalnego dołączonego do ustawień synchronizacji należy podać poświadczenia, aby utworzyć konto synchronizacji lub podać wstępnie utworzone konto niestandardowej synchronizacji. Ta procedura jest taka sama jak środowisko instalacji czystej z wyjątkiem tego, że nie można dodawać ani usuwać katalogów. 
- **Opcje konfiguracji**   — Podobnie jak w przypadku czystej instalacji, można skonfigurować początkowe ustawienia, czy należy uruchomić automatyczną synchronizację czy włączyć tryb przejściowy. Główną różnicą jest to, że tryb przejściowy jest celowo domyślnie włączony, aby umożliwić porównanie wyników konfiguracji i synchronizacji przed aktywnie eksportowaniem wyników do platformy Azure. 

![Łączenie katalogów](media/how-to-connect-import-export-config/import2.png)

> [!NOTE]
> Tylko jeden serwer synchronizacji może należeć do roli głównej i aktywnie eksportować zmiany konfiguracji na platformie Azure. Wszystkie inne serwery muszą być umieszczone w trybie przejściowym. 

## <a name="migrating-settings-from-an-existing-server"></a>Migrowanie ustawień z istniejącego serwera 

Jeśli istniejący serwer nie obsługuje zarządzania ustawieniami, możesz zdecydować się na uaktualnienie serwera w miejscu lub przeprowadzenie migracji ustawień do użycia na nowym serwerze przemieszczania.  

Migracja wymaga uruchomienia skryptu programu PowerShell, który wyodrębnia istniejące ustawienia do użycia w nowej instalacji.Ta metoda jest zalecana, aby wykazać ustawienia istniejącego serwera, a następnie zastosować je na nowo zainstalowanym serwerze przemieszczania.Porównanie ustawień oryginalnego serwera z nowo utworzonym serwerem spowoduje szybkie Wizualizowanie zmian między serwerami.Jak zawsze, postępuj zgodnie z procesem certyfikacji organizacji, aby upewnić się, że nie jest wymagana żadna dodatkowa konfiguracja.  

### <a name="migration-process"></a>Proces migracji 
Aby przeprowadzić migrację ustawień, wykonaj następujące czynności:

1. Uruchom **AzureADConnect.msi** na nowym serwerze tymczasowym i Zatrzymaj na stronie powitalnej Azure AD Connect.

2. Skopiuj **MigrateSettings.ps1** z katalogu Microsoft Azure AD Connect\Tools do lokalizacji na istniejącym serwerze.  Na przykład C:\setup.  Gdzie Instalator jest katalogiem, który został utworzony na istniejącym serwerze.

   ![Łączenie katalogów](media/how-to-connect-import-export-config/migrate1.png)

3. Uruchom skrypt jak pokazano poniżej i Zapisz cały katalog konfiguracji serwera niższego poziomu. Skopiuj ten katalog do nowego serwera przemieszczania. Należy pamiętać, aby skopiować cały folder **wyeksportowany-konfiguracja serwera-*** na nowy serwer.

   ![Łączenie katalogów ](media/how-to-connect-import-export-config/migrate2.png)
    ![ Połącz katalogi](media/how-to-connect-import-export-config/migrate3.png)

5. Uruchom **Azure AD Connect** przez dwukrotne kliknięcie ikony na pulpicie. Zaakceptuj umowę EULA, na następnej stronie kliknij przycisk **Dostosuj** . 
6. Zaznacz pole wyboru **Importuj ustawienia synchronizacji** , a następnie kliknij przycisk **Przeglądaj** , aby przeglądać skopiowane przez folder Exported-konfiguracja serwera-*, a następnie wybierz MigratedPolicy.js, aby zaimportować zmigrowane ustawienia.

   ![Łączenie katalogów](media/how-to-connect-import-export-config/migrate4.png)

7. Aby porównać zmigrowane ustawienia z ustawieniami zastosowanymi, należy poszukać najnowszej **zmigrowane-SynchronizationPolicy-*. JSON** i **zastosowano-SynchronizationPolicy-*. JSON** (* to sygnatura czasowa) w obszarze **%ProgramData%\AADConnect**. Użyj swojego ulubionego narzędzia do porównywania plików, aby porównać parzystość. 

## <a name="post-installation-verification"></a>Weryfikacja po instalacji 

Porównywanie pierwotnie zaimportowanego pliku ustawień z wyeksportowanym plikiem ustawień nowo wdrożonego serwera jest istotnym krokiem w zrozumieniu wszelkich różnic między zamierzonymi, a powstałymi wdrożeniami. Korzystanie z ulubionej aplikacji do porównywania tekstu obok siebie daje błyskawiczną wizualizację, która szybko wyróżnia wszystkie żądane lub przypadkowe zmiany. Mimo że wiele wcześniej kroków konfiguracji ręcznej jest teraz eliminowanych, nadal należy przestrzegać procesu certyfikacji organizacji, aby upewnić się, że nie jest wymagana żadna dodatkowa konfiguracja. Taka konfiguracja może wystąpić w przypadku korzystania z ustawień zaawansowanych, które nie są obecnie przechwytywane w publicznej wersji zapoznawczej zarządzania ustawieniami. 

Znane ograniczenia obejmują następujące elementy: 
- **Reguły synchronizacji**   — pierwszeństwo dla reguły niestandardowej musi znajdować się w zarezerwowanym zakresie 0-99, aby uniknąć konfliktów ze standardowymi regułami firmy Microsoft. Umieszczenie reguły niestandardowej poza zarezerwowanym zakresem może spowodować, że reguła niestandardowa zostanie przesunięta w taki sposób, jak reguły standardowe zostaną dodane do konfiguracji. Podobny problem wystąpi, jeśli konfiguracja zawiera zmodyfikowane standardowe reguły. Modyfikowanie reguły standardowej jest zdecydowanie odradzane, a umieszczenie reguły prawdopodobnie jest nieprawidłowe. 
- **Zapisywanie zwrotne urządzeń**   — te ustawienia są dostępne w katalogu, ale nie są obecnie stosowane podczas konfigurowania. Jeśli funkcja zapisywania zwrotnego urządzeń została włączona dla oryginalnego serwera, należy ręcznie skonfigurować tę funkcję na nowo wdrożonym serwerze. 
- **Synchronizowane typy obiektów**   — Chociaż istnieje możliwość ograniczenia listy synchronizowanych typów obiektów (użytkowników, kontaktów, grup itp.) przy użyciu Synchronization Service Manager, ta funkcja nie jest obecnie obsługiwana przez ustawienia synchronizacji. Po ukończeniu instalacji należy ręcznie ponownie zastosować konfigurację zaawansowaną. 
- **Niestandardowe profile uruchamiania**   — Chociaż istnieje możliwość modyfikacji domyślnego zestawu uruchamiania profilów przy użyciu Synchronization Service Manager, ta funkcja nie jest obecnie obsługiwana przez ustawienia synchronizacji. Po ukończeniu instalacji należy ręcznie ponownie zastosować konfigurację zaawansowaną. 
- **Konfigurowanie hierarchii aprowizacji**   — Ta zaawansowana funkcja Synchronization Service Manager nie jest obsługiwana przez ustawienia synchronizacji i należy ją ręcznie skonfigurować po zakończeniu wstępnego wdrożenia. 
- Uwierzytelnianie AD FS i **serwera pingfederate**   — metody logowania skojarzone z tymi funkcjami uwierzytelniania będą automatycznie wybierane, jednak musisz interaktywnie dostarczyć wszystkie inne wymagane parametry konfiguracji. 
- **Wyłączona reguła synchronizacji niestandardowej zostanie zaimportowana jako włączona** Wyłączona reguła synchronizacji niestandardowej zostanie zaimportowana jako włączona. Upewnij się, że jest również wyłączona na nowym serwerze.

 ## <a name="next-steps"></a>Następne kroki

- [Sprzęt i wymagania wstępne](how-to-connect-install-prerequisites.md) 
- [Ustawienia ekspresowe](how-to-connect-install-express.md)
- [Ustawienia dostosowane](how-to-connect-install-custom.md)
- [Instalowanie agentów programu Azure AD Connect Health](how-to-connect-health-agent-install.md) 
