---
title: Dokumentacja analizy kodu zabezpieczeń firmy Microsoft — często zadawane pytania
description: Dowiedz się więcej o rozszerzeniu analizy kodu zabezpieczeń firmy Microsoft, przeglądając często zadawane pytania.
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 3bff42b9b82104909c79b75c7b1de51c9ddb6e0d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801319"
---
# <a name="frequently-asked-questions"></a>Często zadawane pytania

> [!Note]
> Od 1 marca 2022 rozszerzenie Microsoft Security code Analysis (MSCA) zostanie wycofane. Istniejący klienci MSCA będą zachować dostęp do MSCA do 1 marca 2022. Zapoznaj się z [narzędziami do analizy kodu źródłowego OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) , aby zapoznać się z alternatywnymi opcjami w usłudze Azure DevOps. W przypadku klientów mających na celu zaplanowanie migracji do usługi GitHub można sprawdzić [Zaawansowane zabezpieczenia usługi GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Masz pytania? Zapoznaj się z poniższymi PYTANIAmi, aby uzyskać więcej informacji.

## <a name="general-faq"></a>Ogólne często zadawane pytania

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Czy mogę zainstalować rozszerzenie na dowolnym wystąpieniu programu Visual Studio Team Foundation Server zamiast w wystąpieniu usługi Azure DevOps?

Nie. Rozszerzenie nie jest dostępne do pobrania i instalacji dla programu Visual Studio Team Foundation Server.

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Czy muszę uruchomić analizę kodu zabezpieczeń firmy Microsoft z moją kompilacją? 

Może. Jest to zależne od typu narzędzia do analizy. Kod źródłowy może być jedynym wymaganym warunkiem lub dane wyjściowe kompilacji mogą być wymagane.

Na przykład skaner poświadczeń (CredScan) analizuje pliki w strukturze folderów repozytorium kodu. Ze względu na tę analizę można uruchomić zadania kompilacji dzienników analizy zabezpieczeń i opublikować je w autonomicznej kompilacji w celu uzyskania wyników.

W przypadku innych narzędzi, takich jak BinSkim, które analizują artefakty po kompilacji, najpierw wymagana jest kompilacja.

### <a name="can-i-break-my-build-when-results-are-found"></a>Czy mogę przerwać kompilację po znalezieniu wyników?

Tak. Możesz wprowadzić przerwanie kompilacji, gdy dowolne narzędzie zgłosi problem lub problem w jego pliku dziennika. Wystarczy dodać zadanie kompilacji po analizie i zaznaczyć pole wyboru dla każdego narzędzia, dla którego chcesz przerwać kompilację.

W interfejsie użytkownika zadania po przeanalizie można przerwać kompilację, gdy dowolne narzędzie zgłosi tylko błędy lub błędy i ostrzeżenia.

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Jak argumenty wiersza polecenia w usłudze Azure DevOps różnią się od tych argumentów w autonomicznych narzędziach pulpitu? 

W większości przypadków zadania kompilacji Azure DevOps są bezpośrednimi otokami wokół argumentów wiersza polecenia narzędzi zabezpieczeń. Można przekazać jako argumenty do zadania kompilacji wszystko, co jest zwykle przekazywane do narzędzia wiersza polecenia.

Zauważalne różnice:

- Narzędzia są uruchamiane z folderu źródłowego agenta $ (Build. SourcesDirectory) lub z% BUILD_SOURCESDIRECTORY%. Przykładem jest C:\agent \_ work\1\s.
- Ścieżki w argumentach mogą być względne względem katalogu głównego katalogu źródłowego wymienionego wcześniej. Ścieżki mogą być również bezwzględne. Ścieżki bezwzględne są uzyskiwane przy użyciu zmiennych kompilacji DevOps platformy Azure lub przez uruchomienie agenta lokalnego z znanymi lokalizacjami wdrożenia zasobów lokalnych.
- Narzędzia automatycznie zapewniają ścieżkę lub folder pliku wyjściowego. Jeśli podano lokalizację wyjściową dla zadania kompilacji, Ta lokalizacja zostanie zastąpiona ścieżką do dobrze znanej lokalizacji dzienników w agencie kompilacji.
- Niektóre dodatkowe argumenty wiersza polecenia są zmieniane dla niektórych narzędzi. Jednym z przykładów jest dodanie lub usunięcie opcji, które zapewniają, że nie jest uruchamiany graficzny interfejs użytkownika.

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Czy mogę uruchomić zadanie kompilacji, takie jak skaner poświadczeń dla wielu repozytoriów w kompilacji DevOps Azure?

Nie. Uruchamianie bezpiecznych narzędzi programistycznych w wielu repozytoriach w pojedynczym potoku nie jest obsługiwane.

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>Określony plik wyjściowy nie jest tworzony lub nie można znaleźć podanego pliku wyjściowego

Zadania kompilacji filtrują niektóre dane wejściowe użytkownika. Dla tego pytania należy zaktualizować lokalizację wygenerowanego pliku wyjściowego jako wspólną lokalizację w agencie kompilacji. Aby uzyskać więcej informacji na temat tej lokalizacji, zobacz następujące pytania.

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>Gdzie znajdują się pliki wyjściowe generowane przez narzędzia? 

Zadania kompilacji automatycznie dodają ścieżki wyjściowe do tej dobrze znanej lokalizacji na agencie kompilacji: $ (Agent. BuildDirectory) \_ sdt\logs. Ze względu na to, że firma Microsoft stosuje się do tej lokalizacji, wszystkie zespoły, które tworzą lub zużywają dzienniki analizy kodu, mają dostęp do danych wyjściowych.

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>Czy można kolejkować kompilację, aby uruchamiać te zadania na hostowanym agencie kompilacji? 

Tak. Wszystkie zadania i narzędzia w rozszerzeniu można wykonać na hostowanym agencie kompilacji.

>[!NOTE]
> Zadanie kompilacji skanera chroniącego przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączonym usługą Windows Defender. Udostępniony program Visual Studio 2017 i jego nowsze wersje zapewniają taki Agent. Zadanie kompilacji nie zostanie uruchomione w hostowanym agencie programu Visual Studio 2015.
>
> Mimo że nie można zaktualizować podpisów tych agentów, sygnatury powinny zawsze być starsze niż trzy godziny.

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>Czy można uruchamiać te zadania kompilacji jako część potoku wersji, zamiast potoku kompilacji?

W większości przypadków tak.

Jednak usługa Azure DevOps nie obsługuje uruchamiania zadań w ramach potoków wydań, gdy te zadania publikują artefakty. Brak pomocy technicznej uniemożliwia pomyślne uruchomienie zadania publikowania dzienników analizy zabezpieczeń w potoku wydania. Zadanie w zamian kończy się niepowodzeniem z komunikatem o błędzie opisowym.

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>Skąd zadania kompilacji pobierają narzędzia?

Zadania kompilacji mogą pobrać pakiety NuGet narzędzi z [kanału informacyjnego usługi Azure DevOps zarządzanie pakietami](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json). Zadania kompilacji mogą również korzystać z Menedżera pakietów węzła, który musi być wstępnie zainstalowany na agencie kompilacji. Przykładem takiej instalacji jest polecenie **npm Install tslint**.

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>Jaki wpływ na instalację rozszerzenia należy do mojej organizacji usługi Azure DevOps? 

Po zainstalowaniu zadania kompilacji zabezpieczeń udostępniane przez rozszerzenie staną się dostępne dla wszystkich użytkowników w organizacji. Podczas tworzenia lub edytowania potoku platformy Azure te zadania są dostępne z listy kolekcja zadań kompilacji. W przeciwnym razie instalacja rozszerzenia w organizacji usługi Azure DevOps nie przynosi żadnego efektu. Instalacja nie modyfikuje żadnych ustawień konta, ustawień projektu ani potoków.

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>Czy zainstalowanie rozszerzenia modyfikuje istniejące Azure Pipelines? 

Nie. Zainstalowanie rozszerzenia sprawia, że zadania kompilacji zabezpieczeń są dostępne dla wszystkich potoków. Nadal trzeba dodać lub zaktualizować definicje kompilacji, aby narzędzia mogły współpracowały z procesem kompilacji.

## <a name="task-specific-faq"></a>Często zadawane pytania dotyczące zadań

Pytania dotyczące zadań kompilacji są wymienione w tej sekcji.

### <a name="credential-scanner"></a>Skaner poświadczeń

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>Jakie są typowe scenariusze pomijania i przykłady?

Poniżej znajdują się szczegółowe informacje o dwóch typowych scenariuszach pomijania.

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>Aby pominąć wszystkie wystąpienia danego wpisu tajnego w określonej ścieżce

Klucz skrótu wpisu tajnego z pliku wyjściowego CredScan jest wymagany, jak pokazano w poniższym przykładzie.

```js
{
    "tool": "Credential Scanner",
    "suppressions": [
    {
        "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
        "_justification": "Secret used by MSDN sample, it is fake."
    }
  ]
}
```

>[!WARNING]
> Klucz skrótu jest generowany przez część pasującej wartości lub zawartości pliku. Każda Poprawka kodu źródłowego może zmienić klucz skrótu i wyłączyć regułę pomijania.

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>Aby pominąć wszystkie wpisy tajne w określonym pliku lub pominąć sam plik tajny

Wyrażeniem pliku może być nazwa pliku. Może to być również część podstawowa elementu pełnej ścieżki pliku lub nazwy pliku. Symbole wieloznaczne nie są obsługiwane.

W poniższych przykładach pokazano, jak pominąć plik \<InputPath>\src\JS\lib\angular.js

Przykłady prawidłowych reguł pomijania:

- \<InputPath>\src\JS\lib\angular.js — pomija plik w określonej ścieżce
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js — pomija wszystkie pliki o tej samej nazwie

```js
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
```

>[!WARNING] 
> Wszystkie przyszłe wpisy tajne dodane do pliku również zostaną pominięte automatycznie.

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>Jakie są zalecane wskazówki dotyczące zarządzania wpisami tajnymi?

Poniższe zasoby ułatwiają bezpieczne zarządzanie kluczami tajnymi i dostęp do poufnych informacji z poziomu aplikacji:

 - [Usługa Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../azure-sql/database/authentication-aad-overview.md)
 - [Usługa Azure AD tożsamość usługi zarządzanej (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Zarządzane tożsamości w Azure App Service i Azure Functions](../../app-service/overview-managed-identity.md)
 - [Biblioteka AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication)


Aby uzyskać więcej informacji, zobacz wpis w blogu [Zarządzanie kluczami tajnymi bezpiecznie w chmurze](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/).

#### <a name="can-i-write-my-own-custom-searchers"></a>Czy mogę napisać własne niestandardowe wyszukiwania?

Skaner poświadczeń bazuje na zestawie wyszukiwania zawartości, które są powszechnie zdefiniowane w pliku buildsearchers.xml. Plik zawiera tablicę serializowanych obiektów XML, które reprezentują obiekt **ContentSearcher** . Program jest dystrybuowany z zestawem dobrze przetestowanych wyszukiwań. Ale można również zaimplementować własne niestandardowe wyszukiwania.

Wyszukiwanie zawartości jest zdefiniowane w następujący sposób:

- **Nazwa**: nazwa opisowa wyszukiwania, który ma być używany w plikach wyjściowych skanera poświadczeń. Zalecamy użycie konwencji nazewnictwa notacji CamelCase-Case dla nazw usługi Search.
- **RuleID**: stabilny identyfikator nieprzezroczystego elementu wyszukiwania:
    - Domyślnym analitykiem skanera poświadczeń jest przypisana wartość **RuleID** , na przykład CSCAN0010, CSCAN0020 lub CSCAN0030. Ostatnia cyfra jest zarezerwowana dla potencjalnie łączących lub dzielących grupy wyszukiwania za pomocą wyrażeń regularnych (wyrażenie regularne).
    - Wartość **RuleID** niestandardowego wyszukiwania powinna mieć własną przestrzeń nazw. Przykłady obejmują CSCAN- \<Namespace\> 0,010, CSCAN- \<Namespace\> 0020 i CSCAN- \<Namespace\> 0030.
    - W pełni kwalifikowana nazwa wyszukiwania to kombinacja wartości **RuleID** i nazwy szukanej. Przykłady obejmują CSCAN0010. KeyStoreFiles i CSCAN0020. Base64EncodedCertificate.
- **ResourceMatchPattern**: wyrażenie regularne rozszerzeń plików do sprawdzenia dla wyszukiwania.
- **ContentSearchPatterns**: tablica ciągów zawierających instrukcje wyrażenia regularnego do dopasowania. Jeśli nie zdefiniowano żadnych wzorców wyszukiwania, zwracane są wszystkie pliki zgodne z wartością **ResourceMatchPattern** .
- **ContentSearchFilters**: tablica ciągów zawierających instrukcje wyrażenia regularnego do filtrowania fałszywych wartości dodatnich dla wyszukiwania.
- **MatchDetails**: Komunikat opisowy, instrukcje ograniczenia lub oba, które mają zostać dodane dla każdego dopasowania do usługi wyszukiwania.
- **Zalecenie**: sugestie — zawartość pola dla dopasowania przy użyciu formatu raportu wstępnie szybkiego.
- **Ważność**: liczba całkowita, która odzwierciedla poziom ważności problemu. Najwyższy poziom ważności ma wartość 1.

  ![Plik XML pokazujący konfigurację skanera poświadczeń](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Analizatory Roslyn

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Jakie są typowe błędy podczas korzystania z zadania analizatorów Roslyn?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>Projekt został przywrócony przy użyciu nieprawidłowej wersji Microsoft.NETCore.App

Pełny komunikat o błędzie:

"Błąd: projekt został przywrócony przy użyciu Microsoft.NETCore.App wersja *x. x. x*, ale z bieżącymi ustawieniami w zamian zostanie użyta wersja *y. y. y* . Aby rozwiązać ten problem, upewnij się, że te same ustawienia są używane do przywracania i dla kolejnych operacji, takich jak Kompilowanie lub publikowanie. Zazwyczaj ten problem może wystąpić, jeśli właściwość RuntimeIdentifier jest ustawiona podczas kompilowania lub publikowania, ale nie podczas przywracania.

Ponieważ zadania analizatorów Roslyn są uruchamiane jako część kompilacji, drzewo źródłowe na maszynie kompilacji musi być w stanie możliwego do skompilowania.

Krok między głównymi krokami kompilowania i Roslynów może spowodować umieszczenie drzewa źródłowego w stanie uniemożliwiającym Kompilowanie. Ten dodatkowy krok prawdopodobnie **dotnet.exe publikowanie**. Spróbuj zduplikować krok, który wykonuje przywracanie NuGet tuż przed krokiem Roslyn analizatorów. Ten zduplikowany krok może spowodować przywrócenie drzewa źródła w stanie możliwego do skompilowania.

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe nie może utworzyć wystąpienia analizatora

Pełny komunikat o błędzie:

Zakończono działanie "csc.exe" z kodem błędu 1 — nie można utworzyć wystąpienia elementu analizatora *AAAA* z dysku C: \\ *bbbb*. dll: nie powiodło się załadowanie pliku lub zestawu "Microsoft. CodeAnalysis, Version =*X. x. x. x*, Culture = neutral, PublicKeyToken = 31bf3856ad364e35" lub jednej z jego zależności. System nie może odnaleźć określonego pliku. "

Upewnij się, że kompilator obsługuje analizatory Roslyn. Uruchomienie polecenia **csc.exe/Version** powinno zgłosić wartość wersji 2,6 lub nowszej.

Czasami plik. csproj może zastąpić instalację programu Visual Studio na maszynie kompilacji, odwołując się do pakietu z Microsoft.Net. Kompilators. Jeśli nie zamierzasz używać określonej wersji kompilatora, Usuń odwołania do Microsoft.Net. Kompilators. W przeciwnym razie upewnij się, że wersja przywoływanego pakietu jest również 2,6 lub nowsza.

Spróbuj uzyskać ścieżkę dziennika błędów, która jest określona w **csc.exe opcji/ErrorLog** . Opcja i ścieżka są wyświetlane w dzienniku dla zadania kompilacji analizatory Roslyn. Mogą wyglądać podobnie do **/ErrorLog: f:\ts-services-123 \_ work\456\s\Some\Project\Code\Code.csproj.Sarif**

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>Wersja kompilatora C# jest zbyt mała

Aby uzyskać najnowsze wersje kompilatora języka C#, przejdź do [Microsoft.NET. kompilators](https://www.nuget.org/packages/Microsoft.Net.Compilers). Aby uzyskać zainstalowaną wersję, uruchom **csc.exe/Version** w wierszu polecenia. Upewnij się, że odwołujesz się do pakietu NuGet Microsoft.Net. COMPILERS w wersji 2,6 lub nowszej.

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>Nie znaleziono dzienników MSBuild i VSBuild

Zadanie kompilacji analizatorów Roslyn musi wysyłać zapytania do usługi Azure DevOps dla dziennika MSBuild z zadania kompilacji MSBuild. Jeśli zadanie analizatora zostanie uruchomione natychmiast po wykonaniu zadania MSBuild, dziennik nie będzie jeszcze dostępny. Umieść inne zadania między zadaniem programu MSBuild a zadaniem analizatorów Roslyn. Przykłady innych zadań obejmują skaner BinSkim i chroniący przed złośliwym oprogramowaniem.

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz dodatkowej pomocy, obsługa analizy kodu zabezpieczeń firmy Microsoft jest dostępna od poniedziałku do piątku od 9:00 AM do 5:00 PM (pacyficzny czas standardowy).

- Dołączanie: Zapoznaj się z naszą [dokumentacją](security-code-analysis-onboard.md) dołączania
  
- Pomoc techniczna: Wyślij wiadomość E-mail do naszego zespołu przy [pomocy technicznej analizy kodu zabezpieczeń firmy Microsoft](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)