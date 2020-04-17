---
title: Często zadawane pytania dotyczące dokumentacji analizy kodu zabezpieczeń firmy Microsoft
description: Ten artykuł zawiera często zadawane pytania dotyczące rozszerzenia Analizy kodu zabezpieczeń firmy Microsoft
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: cb04a8e5a6d8c982a35cb5c448e4b6d93825bf73
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460226"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania
Masz pytania? Aby uzyskać więcej informacji, zapoznaj się z poniższym często zadawanymi pytaniami.

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Czy można zainstalować rozszerzenie w wystąpieniu programu Visual Studio Team Foundation Server zamiast w wystąpieniu usługi Azure DevOps?

Nie. Rozszerzenie nie jest dostępne do pobierania i instalacji dla programu Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Czy muszę uruchamiać analizę kodu zabezpieczeń firmy Microsoft za pomocą kompilacji? 

Może. To zależy od typu narzędzia analitycznego. Kod źródłowy może być jedyną rzeczą, która jest wymagana lub dane wyjściowe kompilacji mogą być wymagane.

Na przykład skaner poświadczeń (CredScan) analizuje pliki w strukturze folderów repozytorium kodu. Z tej analizy można uruchomić credscan i publikować dzienniki analizy zabezpieczeń budować zadania w kompilacji autonomicznej, aby uzyskać wyniki.

W przypadku innych narzędzi, takich jak BinSkie, które analizują artefakty po kompilacji, kompilacja jest wymagana jako pierwsza.

### <a name="can-i-break-my-build-when-results-are-found"></a>Czy mogę przerwać kompilację po znalezieniu wyników?

Tak. Przerwę kompilacji można wprowadzić, gdy dowolne narzędzie zgłasza problem lub problem w pliku dziennika. Wystarczy dodać zadanie kompilacji po analizie i zaznacz pole wyboru dla dowolnego narzędzia, dla którego chcesz przerwać kompilację.

W interfejsie użytkownika zadania po analizie można podzielić kompilację, gdy dowolne narzędzie zgłasza tylko błędy lub oba błędy i ostrzeżenia.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Czym różnią się argumenty wiersza polecenia w usłudze Azure DevOps od tych argumentów w autonomicznych narzędziach pulpitu? 

W przeważającej części zadania kompilacji azure DevOps są bezpośrednimi otokami wokół argumentów wiersza polecenia narzędzi zabezpieczeń. Można przekazać jako argumenty do zadania kompilacji wszystko, co zwykle przekazujesz do narzędzia wiersza polecenia.

Zauważalne różnice:

- Narzędzia są uruchamiane z folderu źródłowego agenta $(Build.SourcesDirectory) lub z %BUILD_SOURCESDIRECTORY%. Przykładem jest C:\agent\_work\1\s.
- Ścieżki w argumentach mogą być powiązane z katalogiem źródłowym wcześniej wymienionym. Ścieżki mogą być również bezwzględne. Ścieżki bezwzględne są uruchamiane przy użyciu zmiennych kompilacji programu Azure DevOps lub przy użyciu lokalnego agenta ze znanymi lokalizacjami wdrażania zasobów lokalnych.
- Narzędzia automatycznie zapewniają wyjściową ścieżkę pliku lub folder. Jeśli podasz lokalizację wyjściową dla zadania kompilacji, ta lokalizacja zostanie zastąpiona ścieżką do naszej dobrze znanej lokalizacji dzienników na agencie kompilacji
- Niektóre dodatkowe argumenty wiersza polecenia są zmieniane dla niektórych narzędzi. Jednym z przykładów jest dodanie lub usunięcie opcji, które zapewniają, że nie zostanie uruchomiony żaden interfejs graficzny.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Czy mogę uruchomić zadanie kompilacji, takie jak Skaner poświadczeń w wielu repozytoriach w kompilacji programu Azure DevOps?

Nie. Uruchamianie narzędzi bezpiecznego programowania w wielu repozytoriach w jednym potoku nie jest obsługiwane.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Określony plik wyjściowy nie jest tworzony lub nie mogę znaleźć określonego pliku wyjściowego

Zadania kompilacji filtrują niektóre dane wejściowe użytkownika. W szczególności w przypadku tego pytania aktualizują lokalizację wygenerowanego pliku wyjściowego jako wspólną lokalizację w agencie kompilacji. Aby uzyskać więcej informacji na temat tej lokalizacji, zobacz następujące pytania.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Gdzie są generowane pliki wyjściowe przez zapisane narzędzia? 

Zadania kompilacji automatycznie dodają ścieżki wyjściowe do tej dobrze znanej lokalizacji w agencie kompilacji: $(Agent.BuildDirectory)\_sdt\logs. Ponieważ standaryzujemy w tej lokalizacji, wszystkie zespoły, które produkują lub zużywają dzienniki analizy kodu mają dostęp do danych wyjściowych.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Czy mogę kolejkować kompilacji do uruchamiania tych zadań na agenta hosta kompilacji? 

Tak. Wszystkie zadania i narzędzia w rozszerzeniu mogą być wykonywane na agenta kompilacji hostowanego.

>[!NOTE]
> Zadanie kompilacji skanera przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączoną funkcją Usługi Windows Defender. Hostowane visual studio 2017 i później zapewnić takiego agenta. Zadanie kompilacji nie zostanie uruchomione w programie Visual Studio 2015 hostowanego agenta.
>
> Mimo że podpisów nie można zaktualizować na tych agentów, podpisy powinny być zawsze mniej niż trzy godziny.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Czy można uruchomić te zadania kompilacji jako część potoku wydania, w przeciwieństwie do potoku kompilacji?

W większości przypadków tak.

Jednak usługa Azure DevOps nie obsługuje uruchamiania zadań w ramach potoków wydania, gdy te zadania publikują artefakty. Ten brak pomocy technicznej uniemożliwia pomyślne uruchomienie zadania Dzienniki analizy zabezpieczeń publikowania w potoku wydania. Zamiast tego zadanie kończy się niepowodzeniem z opisowym komunikatem o błędzie.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Skąd zadania kompilacji pobrać narzędzia?

Tworzenie zadań można pobrać pakiety NuGet narzędzi z [kanału zarządzania pakietami usługi Azure DevOps](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Zadania kompilacji można również użyć Menedżera pakietów węzłów, które muszą być preinstalowane w agencie kompilacji. Przykładem takiej instalacji jest polecenie **npm install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Jaki wpływ ma instalacja rozszerzenia na moją organizację Azure DevOps? 

Po ich instalacji zadania kompilacji zabezpieczeń udostępniane przez rozszerzenie stają się dostępne dla wszystkich użytkowników w organizacji. Podczas tworzenia lub edytowania usługi Azure Pipeline te zadania są dostępne z listy kolekcji zadań kompilacji. W przeciwnym razie zainstalowanie rozszerzenia w organizacji Azure DevOps nie ma wpływu. Instalacja nie modyfikuje żadnych ustawień konta, ustawień projektu ani potoków.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Czy zainstalowanie rozszerzenia modyfikuje moje istniejące potoki platformy Azure? 

Nie. Zainstalowanie rozszerzenia udostępnia zadania kompilacji zabezpieczeń do uzupełnienia potoków. Nadal musisz dodać lub zaktualizować definicje kompilacji, aby narzędzia mogły pracować z procesem kompilacji.

## <a name="task-specific-faq"></a>Często zadawane pytania dotyczące poszczególnych zadań

Pytania specyficzne dla zadań kompilacji są wymienione w tej sekcji.

### <a name="credential-scanner"></a>Skaner poświadczeń

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Jakie są typowe scenariusze tłumienia i przykłady?

Oto szczegóły dwóch typowych scenariuszy tłumienia.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Aby pominąć wszystkie wystąpienia danego klucza tajnego w określonej ścieżce

Wymagany jest klucz skrótu klucza tajnego z pliku wyjściowego CredScan, jak pokazano w poniższym przykładzie.

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> Klucz skrótu jest generowany przez część pasującej wartości lub zawartości pliku. Każda poprawka kodu źródłowego może zmienić klucz skrótu i wyłączyć regułę tłumienia.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Aby pominąć wszystkie wpisy tajne w określonym pliku lub pominąć sam plik wpisów tajnych

Wyrażeniem pliku może być nazwa pliku. Może to być również basename część pełnej ścieżki pliku lub nazwę pliku. Symbole wieloznaczne nie są obsługiwane.

Poniższe przykłady pokazują, jak \<pominąć plik InputPath>\src\JS\lib\angular.js

Przykłady prawidłowych reguł wygaszania:

- \<InputPath>\src\JS\lib\angular.js - pomija plik w określonej ścieżce
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - pomija dowolny plik o tej samej nazwie

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> Wszystkie przyszłe wpisy tajne dodane do pliku również zostaną automatycznie pominięte.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Jakie są zalecane wytyczne dotyczące zarządzania wpisami tajnymi?

Następujące zasoby ułatwiają bezpieczne zarządzanie wpisami tajnymi i uzyskiwanie dostępu do poufnych informacji z poziomu aplikacji:

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Usługa Azure Active Directory (Usługa Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Tożsamość usługi Azure AD Managed Service (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Tożsamości zarządzane w usłudze Azure App Service i usłudze Azure Functions](../../app-service/overview-managed-identity.md)
 - [Biblioteka appauthentication](../../key-vault/general/service-to-service-authentication.md)


Aby uzyskać więcej informacji, zobacz wpis w blogu [Zarządzanie wpisami secrets bezpiecznie w chmurze](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Czy mogę napisać własne wyszukiwarki niestandardowe?

Skaner poświadczeń opiera się na zestawie wyszukiwarek zawartości, które są powszechnie zdefiniowane w pliku buildsearchers.xml. Plik zawiera tablicę obiektów szeregowych XML, które reprezentują obiekt **ContentSearcher.** Program jest dystrybuowany z zestawem dobrze przetestowanych wyszukiwarek. Ale można również zaimplementować własne niestandardowe wyszukiwarki.

Wyszukiwarka zawartości jest zdefiniowana w następujący sposób:

- **Nazwa**: Opisowa nazwa wyszukiwarki, która ma być używana w plikach wyjściowych skanera poświadczeń. Zalecamy użycie konwencji nazewnictwa przypadku wielbłąda dla nazw wyszukiwarki.
- **Identyfikator reguły**: Stabilny nieprzezroczysty identyfikator wyszukiwarki:
    - Domyślnemu wyszukiwaczowi skanera poświadczeń jest przypisywana wartość **Identyfikator reguły,** taka jak CSCAN0010, CSCAN0020 lub CSCAN0030. Ostatnia cyfra jest zarezerwowana dla potencjalnie scalania lub dzielenia grup wyszukiwania za pomocą wyrażeń regularnych (wyrażenia regularne).
    - **Wartość RuleId** dla niestandardowego wyszukiwarki powinna mieć własną przestrzeń nazw. Przykłady obejmują\<CSCAN-\>Namespace 0010, CSCAN-\<Namespace\>0020 i CSCAN-\<Namespace\>0030.
    - W pełni kwalifikowana nazwa wyszukiwarki to kombinacja wartości **RuleId** i nazwy wyszukiwarki. Przykładami są CSCAN0010. KeyStoreFiles i CSCAN0020. Certyfikat Base64EncodedCertificate.
- **ResourceMatchPattern**: Regex rozszerzeń plików, aby sprawdzić przed wyszukiwarką.
- **ContentSearchPatterns**: Tablica ciągów zawierających instrukcje regex do dopasowania. Jeśli nie zdefiniowano wzorców wyszukiwania, zwracane są wszystkie pliki pasujące do wartości **ResourceMatchPattern.**
- **ContentSearchFilters**: Tablica ciągów zawierających instrukcje wyrażenia regularnego do filtrowania fałszywych alarmów specyficznych dla wyszukiwarki.
- **MatchDetails:** Opisowy komunikat, instrukcje łagodzenia lub oba mają zostać dodane dla każdego dopasowania wyszukiwarki.
- **Zalecenie:** Zawartość pola sugestie dla dopasowania przy użyciu formatu raportu PREfast.
- **Ważność:** Liczba całkowita, która odzwierciedla poziom ważności problemu. Najwyższy poziom ważności ma wartość 1.

  ![XML z konfiguracją skanera poświadczeń](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analizatory Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Jakie są typowe błędy podczas korzystania z zadania Analizatory Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projekt został przywrócony przy użyciu niewłaściwej wersji microsoft.NETCore.App

Pełny komunikat o błędzie:

"Błąd: Projekt został przywrócony przy użyciu wersji Microsoft.NETCore.App *x.x.x*, ale z bieżącymi ustawieniami zamiast tego zostanie użyta wersja *y.y.y.* Aby rozwiązać ten problem, upewnij się, że te same ustawienia są używane do przywracania i dla kolejnych operacji, takich jak kompilacja lub publikowanie. Zazwyczaj ten problem może wystąpić, jeśli RuntimeIdentifier właściwość jest ustawiona podczas kompilacji lub publikowania, ale nie podczas przywracania."

Ponieważ zadania Analizatorów Roslyn są uruchamiane jako część kompilacji, drzewo źródłowe na komputerze kompilacji musi być w stanie kompilacji.

Krok między głównym kompilacji i Roslyn Analizatorów kroki mogły umieścić drzewa źródłowego w stanie, który uniemożliwia budowanie. Ten dodatkowy krok jest prawdopodobnie **dotnet.exe opublikować**. Spróbuj powielać krok, który wykonuje przywracanie NuGet tuż przed krokiem Analizatorów Roslyn. Ten zduplikowany krok może umieścić drzewo źródłowe z powrotem w stanie kompilacji.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe nie może utworzyć wystąpienia analizatora

Pełny komunikat o błędzie:

"'csc.exe' zakończył o kodzie błędu 1 -- Nie można utworzyć wystąpienia\\ *analizatora AAAA* z C:*BBBB*.dll : Nie można załadować pliku lub zestawu 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35' lub jednej z jego zależności. System nie może odnaleźć określonego pliku."

Upewnij się, że kompilator obsługuje analizatory Roslyn. Uruchomienie polecenia **csc.exe /version** powinno zgłosić wartość wersji 2.6 lub nowszą.

Czasami plik csproj może zastąpić instalację programu Visual Studio komputera kompilacji, odwołując się do pakietu z microsoft.net.compilers. Jeśli nie zamierzasz używać określonej wersji kompilatora, usuń odwołania do microsoft.Net.Compilers. W przeciwnym razie upewnij się, że wersja pakietu, do którego istnieje odwołanie, jest również 2.6 lub nowsza.

Spróbuj uzyskać ścieżkę dziennika błędów, która jest określona w **csc.exe /errorlog** opcji. Opcja i ścieżka pojawiają się w dzienniku dla roslyn analizatory zadania kompilacji. Mogą wyglądać jak **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>Wersja kompilatora języka C# nie jest wystarczająco aktualna

Aby uzyskać najnowsze wersje kompilatora języka C#, przejdź do [microsoft.net.compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers). Aby uzyskać zainstalowaną wersję, uruchom **program csc.exe /version** w wierszu polecenia. Upewnij się, że odwołujesz się do pakietu Microsoft.Net.Compilers NuGet, który jest w wersji 2.6 lub nowszej.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Nie znaleziono dzienników MSBuild i VSBuild

Zadanie kompilacji Analizatorów Roslyn musi zbadać usługę Azure DevOps dla dziennika MSBuild z zadania kompilacji MSBuild. Jeśli zadanie analizatora jest uruchamiane natychmiast po zadaniu MSBuild, dziennik nie będzie jeszcze dostępny. Umieść inne zadania między zadaniem MSBuild a zadaniem Analizatorów Roslyn. Przykładami innych zadań są BinSkim i Anti-Malware Scanner.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowej pomocy, pomoc techniczna dotycząca analizy kodu zabezpieczeń firmy Microsoft jest dostępna od poniedziałku do piątku w godzinach od 9:00 do 17:00 czasu pacyficznego.

- Dołączanie: Zapoznaj się z naszą [dokumentacją dołączania](security-code-analysis-onboard.md)
  
- Pomoc techniczna: Wyślij wiadomość e-mail do naszego zespołu na [pomoc techniczną w zakresie analizy kodu zabezpieczeń firmy Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)