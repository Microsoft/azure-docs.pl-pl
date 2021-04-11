---
title: Dostosowywanie zadań analizy kodu zabezpieczeń firmy Microsoft
titleSuffix: Azure
description: W tym artykule opisano dostosowywanie zadań w rozszerzeniu Microsoft Security code Analysis
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
ms.openlocfilehash: ad395e1b782edb28845bb7db0607d2bab5b5697c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802016"
---
# <a name="configure-and-customize-the-build-tasks"></a>Konfigurowanie i dostosowywanie zadań kompilacji

> [!Note]
> Od 1 marca 2022 rozszerzenie Microsoft Security code Analysis (MSCA) zostanie wycofane. Istniejący klienci MSCA będą zachować dostęp do MSCA do 1 marca 2022. Zapoznaj się z [narzędziami do analizy kodu źródłowego OWASP](https://owasp.org/www-community/Source_Code_Analysis_Tools) , aby zapoznać się z alternatywnymi opcjami w usłudze Azure DevOps. W przypadku klientów mających na celu zaplanowanie migracji do usługi GitHub można sprawdzić [Zaawansowane zabezpieczenia usługi GitHub](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security).

W tym artykule szczegółowo opisano opcje konfiguracji dostępne w poszczególnych zadaniach kompilacji. Artykuł rozpoczyna się od zadań związanych z narzędziami do analizy kodu zabezpieczeń. Jest ona zakończona z zadaniami po przetworzeniu.

## <a name="anti-malware-scanner-task"></a>Zadanie skanera chroniącego przed złośliwym oprogramowaniem

>[!NOTE]
> Zadanie kompilacji skanera chroniącego przed złośliwym oprogramowaniem wymaga agenta kompilacji z włączonym usługą Windows Defender. Udostępniony program Visual Studio 2017 i jego nowsze wersje zapewniają taki Agent. Zadanie kompilacji nie zostanie uruchomione w hostowanym agencie programu Visual Studio 2015.
>
> Mimo że nie można zaktualizować podpisów tych agentów, sygnatury powinny zawsze być starsze niż trzy godziny.

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i tekście.

![Konfigurowanie zadania kompilacji skanera chroniącego przed złośliwym oprogramowaniem](./media/security-tools/5-add-anti-malware-task600.png)

W polu listy **Typ** zrzutu ekranu jest zaznaczona wartość **podstawowa** . Wybierz pozycję **niestandardowa** , aby podać argumenty wiersza polecenia, które dostosowują skanowanie.

Usługa Windows Defender pobiera i instaluje podpisy przy użyciu klienta Windows Update. Jeśli aktualizacja podpisu nie powiedzie się w agencie kompilacji, kod błędu **HRESULT** prawdopodobnie będzie pochodził z Windows Update.

Aby uzyskać więcej informacji na temat Windows Update błędów i ich środków zaradczych, zobacz [Windows Update kodów błędów według składnika](/windows/deployment/update/windows-update-error-reference) i artykułu TechNet [Windows Update kod błędu agenta](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAMLymi chroniącymi przed złośliwym oprogramowaniem](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>BinSkim, zadanie

> [!NOTE]
> Aby można było uruchomić zadanie BinSkim, kompilacja musi spełniać jeden z następujących warunków:
>  - Kompilacja tworzy artefakty binarne z kodu zarządzanego.
>  - Masz zadeklarowane artefakty binarne, które chcesz analizować przy użyciu BinSkim.

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji BinSkim](./media/security-tools/7-binskim-task-details.png)

- Ustaw konfigurację kompilacji na Debuguj, aby pliki debugowania PDB zostały utworzone. BinSkim używa tych plików do mapowania problemów z wyjściowego plików binarnych z powrotem do kodu źródłowego.
- Aby uniknąć przeszukiwania i tworzenia własnego wiersza polecenia:
     - Na liście **Typ** wybierz pozycję **podstawowa**.
     - Na liście **Funkcja** wybierz pozycję **Analizuj**.
- W obszarze **docelowy** wprowadź jeden lub więcej specyfikatorów dla pliku, katalogu lub wzorca filtru. Te specyfikatory są rozpoznawane jako co najmniej jeden plik binarny do przeanalizowania:
    - Wiele określonych elementów docelowych musi być rozdzielonych średnikami (;).
    - Specyfikator może być pojedynczym plikiem lub zawierać symbole wieloznaczne.
    - Specyfikacje katalogów muszą zawsze kończyć się znakiem \\ *.
    - Przykłady:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Jeśli na liście **Typ** wybierzesz pozycję **wiersz polecenia** , musisz uruchomić binskim.exe:
     - Upewnij się, że pierwsze argumenty binskim.exe są **analizowane** przez zlecenie, po którym następuje co najmniej jedna Specyfikacja ścieżki. Każda ścieżka może być pełną ścieżką lub ścieżką względną do katalogu źródłowego.
     - Wiele ścieżek docelowych musi być rozdzielonych spacją.
     - Możesz pominąć opcję **/o** lub **/Output** . Wartość wyjściowa jest dodawana do użytkownika lub zastępowana.
     - Standardowe konfiguracje wiersza polecenia są wyświetlane w następujący sposób.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> Końcowe \\ * jest ważne w przypadku określenia katalogów dla elementu docelowego.

Aby uzyskać więcej informacji na temat argumentów wiersza polecenia BinSkim, reguł według identyfikatora lub kodów zakończenia, zobacz [Podręcznik użytkownika BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML BinSkim](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Zadanie skanera poświadczeń

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji skanera poświadczeń](./media/security-tools/3-taskdetails.png)

Dostępne opcje to:
  - **Nazwa wyświetlana**: Nazwa zadania usługi Azure DevOps. Wartość domyślna to uruchom skaner poświadczeń
  - **Wersja główna narzędzia**: dostępne wartości to **CredScan v2**, **CredScan V1**. Zalecamy klientom korzystanie z wersji **CredScan v2** .
  - **Format danych wyjściowych**: dostępne wartości to **TSV**, **CSV**, **SARIF** i **PREfast**.
  - **Wersja narzędzia**: zalecamy wybranie opcji **najnowsze**.
  - **Folder skanowania**: folder repozytorium do przeskanowania.
  - **Typ pliku wyszukiwania**: opcje lokalizowania pliku wyszukiwania, który jest używany do skanowania.
  - **Plik** pominięć: plik [JSON](https://json.org/) może pominąć problemy w dzienniku danych wyjściowych. Aby uzyskać więcej informacji o scenariuszach pomijania, zobacz sekcję często zadawanych pytań w tym artykule.
  - **Pełne dane wyjściowe**: z oczywistym objaśnieniem.
  - **Rozmiar wsadu**: liczba współbieżnych wątków używanych do uruchamiania skanera poświadczeń. Wartość domyślna to 20. Możliwe wartości mieszczą się w zakresie od 1 do 2 147 483 647.
  - **Limit czasu dopasowania**: czas (w sekundach) poświęcany na próbę dopasowania elementu Search przed opuszczeniem kontroli.
  - **Rozmiar buforu odczytu plików**: rozmiar w bajtach buforu używany podczas odczytywania zawartości. Wartość domyślna to 524 288.  
  - **Maksymalna liczba bajtów odczytu plików**, które można odczytać z pliku podczas analizy zawartości. Wartość domyślna to 104 857 600.
  - **Opcje**  >  kontrolki **Uruchom to zadanie**: określa, kiedy zadanie zostanie uruchomione. Wybierz opcję **warunki niestandardowe** , aby określić bardziej złożone warunki.
  - **Wersja**: wersja zadania kompilacji w ramach usługi Azure DevOps. Ta opcja nie jest często używana.

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML skanera](yaml-configuration.md#credential-scanner-task) .

## <a name="roslyn-analyzers-task"></a>Zadanie analizatorów Roslyn

> [!NOTE]
> Aby można było uruchomić zadanie analizatorów Roslyn, kompilacja musi spełniać następujące warunki:
>
> - Definicja kompilacji zawiera wbudowane zadanie kompilacji MSBuild lub VSBuild do kompilowania kodu w języku C# lub Visual Basic. Zadanie analizatory jest zależne od danych wejściowych i wyjściowych wbudowanego zadania do uruchamiania kompilacji MSBuild z włączonymi analizatorami Roslyn.
> - Agent kompilacji, na którym uruchomiono to zadanie kompilacji, ma zainstalowany program Visual Studio 2017 w wersji 15,5 lub nowszej, tak aby używał kompilatora w wersji 2,6 lub nowszej.

Szczegóły konfiguracji zadania przedstawiono na poniższej liście.

Dostępne opcje to:

- **Zestaw reguł**: wymagane wartości to **SDL**, **zalecane przez SDL** lub własny niestandardowy zestaw reguł.
- **Wersja analizatorów**: zalecamy wybranie opcji **najnowsze**.
- **Plik pominięć ostrzeżeń kompilatora**: plik tekstowy z listą identyfikatorów ostrzeżeń, które są pomijane.
- **Opcje**  >  kontrolki **Uruchom to zadanie**: określa, kiedy zadanie zostanie uruchomione. Wybierz opcję **warunki niestandardowe** , aby określić bardziej złożone warunki.

> [!NOTE]
>
> - Analizatory Roslyn są zintegrowane z kompilatorem i mogą być uruchamiane tylko w ramach kompilacji csc.exe. W związku z tym to zadanie wymaga, aby polecenie kompilatora uruchomione wcześniej w kompilacji zostało powtórzone lub ponownie uruchomione. To odtwarzanie lub uruchamianie jest wykonywane przez wykonanie zapytania dotyczącego usługi Azure DevOps (dawniej Visual Studio Team Services) w przypadku dzienników zadań kompilacji programu MSBuild.
>
>   Nie istnieje żaden inny sposób, aby zadanie niezawodnie pobrać wiersz polecenia kompilacji MSBuild z definicji kompilacji. Rozważamy Dodawanie dowolnego pola tekstowego, aby umożliwić użytkownikom wprowadzanie ich linii poleceń. Jednak będzie trudno zachować aktualność tych wierszy poleceń i synchronizować ją z główną kompilacją.
>
>   Niestandardowe kompilacje wymagają odtwarzania całego zestawu poleceń, a nie tylko poleceń kompilatora. W takich przypadkach włączenie analizatorów Roslyn nie jest proste ani niezawodne.
>
> - Analizatory Roslyn są zintegrowane z kompilatorem. Do wywołania, analizatory Roslyn wymagają kompilacji.
>
>   To nowe zadanie kompilacji jest implementowane przez ponowne Kompilowanie projektów C#, które zostały już skompilowane. Nowe zadanie używa tylko zadań kompilacji MSBuild i VSBuild w tej samej kompilacji lub definicji kompilacji co oryginalne zadanie. Jednak w takim przypadku nowe zadanie używa ich z włączonymi analizatorami Roslyn.
>
>   Jeśli nowe zadanie jest uruchamiane na tym samym agencie co oryginalne zadanie, dane wyjściowe nowego zadania zastąpią dane wyjściowe oryginalnego zadania w folderze źródła *s* . Chociaż dane wyjściowe kompilacji są takie same, zalecamy uruchomienie programu MSBuild, skopiowanie danych wyjściowych do katalogu przemieszczania artefaktów, a następnie uruchomienie analizatorów Roslyn.

Aby uzyskać dodatkowe zasoby dla Roslyn analizatorów, zapoznaj [się z analizatorami opartymi na Roslyn](/dotnet/standard/analyzers/api-analyzer) na Microsoft docs.

Pakiet analizatora zainstalowany i używany przez to zadanie kompilacji można znaleźć na stronie narzędzia NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML analizatorów Roslyn](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>TSLint, zadanie

Aby uzyskać więcej informacji na temat TSLint, przejdź do [repozytorium GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Ponieważ użytkownik może mieć świadomość, Strona główna [repozytorium usługi GitHub TSLint](https://github.com/palantir/tslint) informuje, że TSLint będzie przestarzałe w 2019. Firma Microsoft bada [ESLint](https://github.com/eslint/eslint) jako alternatywne zadanie.

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML TSLint](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Zadanie publikowania dzienników analizy zabezpieczeń

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji dzienników analizy zabezpieczeń](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nazwa artefaktu**: dowolny identyfikator ciągu.
- **Typ artefaktu**: w zależności od dokonanego wyboru można publikować dzienniki w Azure DevOps Server lub do udostępnionego pliku, który jest dostępny dla agenta kompilacji.
- **Narzędzia**: możesz wybrać opcję zachowywania dzienników dla określonych narzędzi lub wybrać **wszystkie narzędzia** , aby zachować wszystkie dzienniki.

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami publikowania dzienników zabezpieczeń YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Zadanie raportu zabezpieczeń

Szczegóły konfiguracji raportów zabezpieczeń przedstawiono na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji raportu zabezpieczeń](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Raporty**: Wybierz **dowolną z opcji**: **plik TSV** i format **pliku HTML** . Dla każdego wybranego formatu jest tworzony jeden plik raportu.
- **Narzędzia**: wybierz narzędzia w definicji kompilacji, dla którego chcesz uzyskać podsumowanie wykrytych problemów. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy są widoczne tylko błędy, czy też w raporcie podsumowującym znajdują się błędy i ostrzeżenia.
- **Opcje zaawansowane**: Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz wybrać opcję rejestrowania ostrzeżenia lub błędu. Jeśli rejestrujesz błąd, zadanie zakończy się niepowodzeniem.
- **Folder dzienników bazowych**: można dostosować podstawowy folder dzienników, w którym mają zostać znalezione dzienniki. Jednak ta opcja nie jest zazwyczaj używana.

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML raportu zabezpieczeń](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Zadanie po analizie

Szczegóły konfiguracji zadania są pokazane na poniższym zrzucie ekranu i na liście.

![Konfigurowanie zadania kompilacji po analizie](./media/security-tools/a-post-analysis600.png)

- **Narzędzia**: wybierz narzędzia w definicji kompilacji, dla którego chcesz warunkowo wstrzyknąć przerwę kompilacji. Dla każdego wybranego narzędzia może być dostępna opcja wyboru, czy ma zostać przerwana tylko w przypadku błędów, czy tylko w przypadku błędów i ostrzeżeń.
- **Raport**: można opcjonalnie napisać wyniki, które powodują przerwanie kompilacji. Wyniki są zapisywane w oknie i pliku dziennika konsoli usługi Azure DevOps.
- **Opcje zaawansowane**: Jeśli nie ma żadnych dzienników dla jednego z wybranych narzędzi, możesz wybrać opcję rejestrowania ostrzeżenia lub błędu. Jeśli rejestrujesz błąd, zadanie zakończy się niepowodzeniem.

Aby uzyskać informacje na temat konfiguracji YAML dla tego zadania, zapoznaj się z naszymi [opcjami YAML po analizie](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat konfiguracji opartej na YAML, zapoznaj się z naszym [przewodnikiem konfiguracyjnym YAML](yaml-configuration.md).

Jeśli masz więcej pytań dotyczących rozszerzenia analizy kodu zabezpieczeń i oferowanych narzędzi, zapoznaj się z [naszą stroną często zadawanych pytań](security-code-analysis-faq.md).