---
title: Omówienie dokumentacji analizy kodu zabezpieczeń firmy Microsoft
description: Dowiedz się więcej o rozszerzeniu Microsoft Security code Analysis. Dzięki temu rozszerzeniu można dodać analizę kodu zabezpieczeń do potoków CI/identyfikatorów DevOps platformy Azure.
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
ms.openlocfilehash: 4443bdbc134f222256c6af8bb1f52c684cdb4624
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801200"
---
# <a name="about-microsoft-security-code-analysis"></a>Informacje o analizie kodu zabezpieczeń firmy Microsoft

> [!Note]
> Od 1 marca 2022 rozszerzenie Microsoft Security code Analysis (MSCA) zostanie wycofane. Istniejący klienci MSCA będą zachować dostęp do MSCA do 1 marca 2022. Zapoznaj się z [narzędziami do analizy kodu źródłowego OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) , aby zapoznać się z alternatywnymi opcjami w usłudze Azure DevOps. W przypadku klientów mających na celu zaplanowanie migracji do usługi GitHub można sprawdzić [Zaawansowane zabezpieczenia usługi GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

Dzięki rozszerzeniu Microsoft Security code Analysis zespoły mogą dodać analizę kodu zabezpieczeń do potoków ciągłej integracji i dostarczania (CI/CD) usługi Azure DevOps. Ta analiza jest zalecana przez specjalistów [ds. bezpieczeństwa](https://www.microsoft.com/securityengineering/sdl/practices) w firmie Microsoft.

Spójne środowisko użytkownika upraszcza zabezpieczenia, ukrywając złożoność uruchamiania narzędzi. W przypadku dostarczania narzędzi przez narzędzia NuGet zespoły nie muszą już zarządzać instalacją ani aktualizacją narzędzi. Przy użyciu zarówno wiersza polecenia, jak i podstawowych interfejsów dla zadań kompilacji, wszyscy użytkownicy mogą mieć dowolną kontrolę nad narzędziami.

Zespoły mogą również korzystać z zaawansowanych możliwości dostosujesz, takich jak:

- Publikowanie dzienników do przechowywania.
- Generowanie raportów ukierunkowanych na dewelopera.
- Konfigurowanie podziałów kompilacji na testach regresji.

## <a name="why-should-i-use-microsoft-security-code-analysis"></a>Dlaczego należy używać analizy kodu zabezpieczeń firmy Microsoft?

### <a name="security-simplified"></a>Uproszczone zabezpieczenia

Dodawanie narzędzi do analizy kodu zabezpieczeń firmy Microsoft do potoku usługi Azure DevOps jest tak proste jak dodanie nowych zadań. Dostosuj zadania lub użyj ich domyślnych zachowań. Zadania są uruchamiane jako część potoku usługi Azure DevOps i tworzą dzienniki zawierające szczegółowe informacje o różnych typach wyników.

### <a name="clean-builds"></a>Czyste kompilacje

Po rozwiązaniu problemów początkowych raportowanych przez narzędzia można skonfigurować rozszerzenie, aby przerwać kompilacje w nowych problemach. Skonfigurowanie kompilacji ciągłej integracji dla każdego żądania ściągnięcia jest proste.

### <a name="set-it-and-forget-it"></a>Ustaw ją i zapomnij

Domyślnie zadania i narzędzia kompilacji są aktualne. Jeśli istnieje zaktualizowana wersja narzędzia, nie musisz go pobrać i zainstalować. Rozszerzenie zajmuje się aktualizacją.

### <a name="under-the-hood"></a>Kulisy

Zadania kompilacji rozszerzenia ukrywają złożoność:
  - Uruchamianie narzędzi do statycznej analizy zabezpieczeń.
  - Przetwarzanie wyników z plików dziennika w celu utworzenia raportu podsumowującego lub przerwania kompilacji.

## <a name="microsoft-security-code-analysis-tool-set"></a>Zestaw narzędzi do analizy kodu zabezpieczeń firmy Microsoft

Rozszerzenie Microsoft Security code Analysis zapewnia, że najnowsze wersje ważnych narzędzi analitycznych są łatwo dostępne dla Ciebie. Rozszerzenie obejmuje zarówno narzędzia zarządzane przez firmę Microsoft, jak i narzędzia Open Source.

Te narzędzia są automatycznie pobierane do agenta hostowanego w chmurze po użyciu odpowiedniego zadania kompilacji do skonfigurowania i uruchomienia potoku.

Ta sekcja zawiera zestaw narzędzi, które są obecnie dostępne w rozszerzeniu. Zobacz, aby dowiedzieć się więcej o większej liczbie narzędzi. Wyślij nam swoje sugestie dotyczące narzędzi, które chcesz dodać.

### <a name="anti-malware-scanner"></a>Skaner chroniący przed złośliwym oprogramowaniem

Zadanie kompilacji skanera chroniącego przed złośliwym oprogramowaniem jest teraz dołączone do rozszerzenia Microsoft Security code Analysis. To zadanie musi być uruchomione na agencie kompilacji, na którym zainstalowano już usługę Windows Defender. Aby uzyskać więcej informacji, zobacz [witrynę sieci Web usługi Windows Defender](https://aka.ms/defender).

### <a name="binskim"></a>BinSkim

BinSkim jest przenośnym skanerem plików wykonywalnych (PE), który weryfikuje ustawienia kompilatora, ustawienia konsolidatora i inne cechy charakterystyczne dla bezpieczeństwa plików binarnych. To zadanie kompilacji udostępnia otokę wiersza polecenia wokół binskim.exe aplikacji konsolowej. BinSkim to narzędzie Open Source. Aby uzyskać więcej informacji, zobacz [BinSkim w witrynie GitHub](https://github.com/Microsoft/binskim).

### <a name="credential-scanner"></a>Skaner poświadczeń

Hasła i inne wpisy tajne przechowywane w kodzie źródłowym są istotnym problemem. Skaner poświadczeń jest zastrzeżonym narzędziem do analizy statycznej, które pomaga w rozwiązaniu tego problemu. Narzędzie wykrywa poświadczenia, wpisy tajne, certyfikaty i inną poufną zawartość w kodzie źródłowym i w danych wyjściowych kompilacji.

### <a name="roslyn-analyzers"></a>Analizatory Roslyn

Analizatory Roslyn to narzędzie zintegrowane z kompilatorem firmy Microsoft służące do statycznej analizy kodu zarządzanego C# i Visual Basic. Aby uzyskać więcej informacji, zobacz [analizatory oparte na Roslyn](/dotnet/fundamentals/code-analysis/quality-rules/security-warnings).

### <a name="tslint"></a>TSLint

TSLint to rozszerzalne narzędzie do analizy statycznej, które sprawdza kod języka TypeScript pod kątem czytelności, łatwość utrzymania i błędów w funkcji. Jest ona szeroko obsługiwana przez nowoczesne edytory i systemy kompilacji. Można dostosować ją przy użyciu własnych reguł lint, konfiguracji i programu formatującego. TSLint to narzędzie Open Source. Aby uzyskać więcej informacji, zobacz [TSLint w witrynie GitHub](https://github.com/palantir/tslint).

## <a name="analysis-and-post-processing-of-results"></a>Analiza i przetwarzanie końcowe wyników

Rozszerzenie Microsoft Security code Analysis Extension zawiera również trzy dostosujesz zadania. Te zadania pomagają analizować wyniki znalezione przez zadania narzędzi zabezpieczeń. Po dodaniu do potoku te zadania zwykle wykonują wszystkie pozostałe zadania narzędzia.

### <a name="publish-security-analysis-logs"></a>Publikowanie dzienników analizy zabezpieczeń

Zadanie kompilacji dzienniki analizy zabezpieczeń zachowuje pliki dziennika narzędzi zabezpieczeń, które są uruchamiane podczas kompilacji. Te dzienniki można odczytać w celu zbadania i kontynuacji.

Pliki dziennika można opublikować do Azure Artifacts jako plik. zip. Możesz również skopiować je do dostępnego udziału plików z prywatnego agenta kompilacji.

### <a name="security-report"></a>Raport o zabezpieczeniach

Zadanie kompilacji raportu zabezpieczeń analizuje pliki dziennika. Te pliki są tworzone przez narzędzia zabezpieczeń, które są uruchamiane podczas kompilacji. Następnie zadanie kompilacji tworzy pojedynczy plik raportu podsumowującego. Ten plik przedstawia wszystkie problemy znalezione przez narzędzia do analizy.

To zadanie można skonfigurować w celu raportowania wyników dla określonych narzędzi lub dla wszystkich narzędzi. Możesz również wybrać poziom problemu, który ma być raportowany, na przykład tylko błędy lub błędy i ostrzeżenia.

### <a name="post-analysis-build-break"></a>Po analizie (przerwanie kompilacji)

Za pomocą zadania kompilacji po analizie można wstrzyknąć przerwanie kompilacji, co spowoduje niepowodzenie kompilacji. Wprowadzasz przerwanie kompilacji, jeśli co najmniej jeden z narzędzi analitycznych zgłosi problemy w kodzie.

Można skonfigurować to zadanie, aby przerwać kompilację pod kątem problemów znalezionych przez określone narzędzia lub wszystkie narzędzia. Można ją również skonfigurować na podstawie ważności znalezionych problemów, takich jak błędy lub ostrzeżenia.

>[!NOTE]
>Zgodnie z projektem każde zadanie kompilacji powiedzie się, jeśli zadanie zakończy się pomyślnie. Jest to prawdą niezależnie od tego, czy narzędzie wykryje problemy, dzięki czemu kompilacja może zostać uruchomiona, umożliwiając wykonanie wszystkich narzędzi.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje na temat sposobu dołączania i instalowania analizy kodu zabezpieczeń firmy Microsoft, zapoznaj się z naszym przewodnikiem dotyczącym dołączania [i instalacji](security-code-analysis-onboard.md).

Aby uzyskać więcej informacji na temat konfigurowania zadań kompilacji, zobacz nasze [Przewodnik po konfiguracji](security-code-analysis-customize.md) lub [Przewodnik po konfiguracji YAML](yaml-configuration.md).

Jeśli masz więcej pytań na temat rozszerzenia i oferowanych narzędzi, zapoznaj się z naszą [stroną często zadawanych pytań](security-code-analysis-faq.md).