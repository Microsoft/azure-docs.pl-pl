---
title: Profilowanie usługi w chmurze (klasycznej) lokalnie w emulatorze obliczeń | Microsoft Docs
description: Zbadaj problemy z wydajnością w usługach w chmurze za pomocą profilera programu Visual Studio
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2f924d84967c1a1928a47b59fd3a8c28da091130
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743563"
---
# <a name="testing-the-performance-of-a-cloud-service-classic-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testowanie wydajności usługi w chmurze (klasycznej) lokalnie w emulatorze obliczeń platformy Azure przy użyciu programu Visual Studio profiler

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Różne narzędzia i techniki są dostępne do testowania wydajności usług Cloud Services.
Po opublikowaniu usługi w chmurze na platformie Azure możesz mieć program Visual Studio zbierający dane profilowania, a następnie przeanalizować ją lokalnie, zgodnie z opisem w temacie [Profilowanie aplikacji platformy Azure][1].
Można również użyć diagnostyki do śledzenia różnych liczników wydajności, zgodnie z opisem w temacie [Korzystanie z liczników wydajności na platformie Azure][2].
Możesz również utworzyć profil aplikacji lokalnie w emulatorze obliczeniowym przed wdrożeniem jej w chmurze.

W tym artykule opisano metodę profilowania próbkowania procesora, którą można wykonać lokalnie w emulatorze. Próbkowanie procesora to metoda profilowania, która nie jest bardzo inwazyjna. W wyznaczeniu interwału próbkowania Profiler tworzy migawkę stosu wywołań. Dane są zbierane w określonym czasie i wyświetlane w raporcie. Ta metoda profilowania umożliwia wskazanie, gdzie w aplikacji intensywnie korzystających z obliczeń większość pracy procesora CPU jest realizowana.  Dzięki temu można skoncentrować się na "ścieżce gorącej", gdzie Twoja aplikacja jest najbardziej czasochłonna.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Skonfiguruj program Visual Studio do profilowania
Po pierwsze istnieje kilka opcji konfiguracji programu Visual Studio, które mogą być przydatne podczas profilowania. Aby poznać raporty profilowania, potrzebne są symbole (pliki. pdb) dla aplikacji, a także symbole dla bibliotek systemowych. Upewnij się, że odwołujesz się do dostępnych serwerów symboli. W tym celu w menu **Narzędzia** w programie Visual Studio wybierz pozycję **Opcje**, a następnie wybierz pozycję **debugowanie**, a następnie pozycję **symbole**. Upewnij się, że serwery symboli firmy Microsoft znajdują się w **lokalizacji pliku symboli (. pdb)**.  Można również odwołać https://referencesource.microsoft.com/symbols się do, które mogą mieć dodatkowe pliki symboli.

![Opcje symboli][4]

W razie potrzeby można uprościć raporty generowane przez profiler przez ustawienie Tylko mój kod. Przy włączonej Tylko mój kod stosy wywołań funkcji są uproszczone, dzięki czemu wywołania są całkowicie wewnętrzne dla bibliotek, a .NET Framework są ukryte w raportach. W menu **Narzędzia** wybierz polecenie **Opcje**. Następnie rozwiń węzeł **Narzędzia wydajności** , a następnie wybierz pozycję **Ogólne**. Zaznacz pole wyboru **włącz tylko mój kod dla raportów profilera**.

![Opcje Tylko mój kod][17]

Możesz użyć tych instrukcji z istniejącym projektem lub z nowym projektem.  W przypadku utworzenia nowego projektu w celu wypróbowania opisanych poniżej technik wybierz projekt **usługi w chmurze platformy Azure** w języku C# i wybierz **rolę sieci Web** i **rolę procesu roboczego**.

![Role projektu usługi w chmurze platformy Azure][5]

Na przykład, Dodaj kod do projektu, który zajmuje dużo czasu i demonstruje oczywisty problem z wydajnością. Na przykład Dodaj następujący kod do projektu roli proces roboczy:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Wywołaj ten kod z metody RunAsync w klasie pochodnej RoleEntryPoint roli procesu roboczego. (Zignoruj ostrzeżenie o metodzie, która jest uruchamiana synchronicznie).

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Kompiluj i uruchom usługę w chmurze lokalnie bez debugowania (Ctrl + F5), używając konfiguracji rozwiązania ustawionej na wartość **Release**. Daje to pewność, że wszystkie pliki i foldery są tworzone na potrzeby lokalnego uruchamiania aplikacji i gwarantują, że wszystkie Emulatory są uruchomione. Uruchom interfejs użytkownika emulatora obliczeń z paska zadań, aby sprawdzić, czy rola proces roboczy jest uruchomiona.

## <a name="2-attach-to-a-process"></a>2: dołączanie do procesu
Zamiast profilowania aplikacji przez uruchomienie jej z poziomu środowiska IDE programu Visual Studio 2010, należy dołączyć Profiler do uruchomionego procesu. 

Aby dołączyć Profiler do procesu, w menu **Analizuj** wybierz pozycję **Profiler** i **Dołącz/Odłącz**.

![Opcja dołączania profilu][6]

W przypadku roli proces roboczy Znajdź proces WaWorkerHost.exe.

![Proces WaWorkerHost][7]

Jeśli folder projektu znajduje się na dysku sieciowym, profiler zostanie poproszony o podanie innej lokalizacji w celu zapisania raportów profilowania.

 Możesz również dołączyć do roli sieci Web, dołączając do WaIISHost.exe.
Jeśli w aplikacji istnieje wiele procesów roli procesu roboczego, należy je rozróżnić przy użyciu identyfikatora procesu. Możesz programowo zbadać identyfikator procesu, uzyskując dostęp do obiektu procesu. Na przykład, jeśli dodasz ten kod do metody Run klasy pochodnej RoleEntryPoint w roli, możesz sprawdzić dziennik w interfejsie użytkownika emulatora obliczeń, aby dowiedzieć się, z jakim procesem nawiązać połączenie.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Aby wyświetlić dziennik, Uruchom interfejs użytkownika emulatora obliczeń.

![Uruchamianie interfejsu użytkownika emulatora obliczeń][8]

Otwórz okno konsoli dziennika roli procesu roboczego w interfejsie użytkownika emulatora obliczeń, klikając pasek tytułu okna konsoli. Identyfikator procesu można zobaczyć w dzienniku.

![Wyświetl identyfikator procesu][9]

Po dołączeniu, wykonaj kroki w interfejsie użytkownika aplikacji (w razie potrzeby), aby odtworzyć scenariusz.

Jeśli chcesz zatrzymać profilowanie, wybierz link **Zatrzymaj profilowanie** .

![Zatrzymaj profilowanie opcji][10]

## <a name="3-view-performance-reports"></a>3: wyświetlanie raportów o wydajności
Zostanie wyświetlony raport dotyczący wydajności aplikacji.

W tym momencie Profiler zatrzyma wykonywanie, zapisuje dane w pliku. vsp i wyświetla raport, który pokazuje analizę tych danych.

![Raport profilera][11]

Jeśli zobaczysz ciąg. wstrcpy w ścieżce gorącej, kliknij pozycję Tylko mój kod, aby zmienić widok, aby wyświetlić tylko kod użytkownika.  Jeśli widzisz ciąg. Concat, spróbuj nacisnąć przycisk Pokaż wszystko.

Powinna zostać wyświetlona Metoda łączenia metody i ciąg. Concat pobierająca dużą część czasu wykonywania.

![Analiza raportu][12]

Jeśli w tym artykule dodano kod łączenia ciągów, w Lista zadań dla tego elementu powinno zostać wyświetlone ostrzeżenie. Może również pojawić się ostrzeżenie o nadmiernej ilości wyrzucania elementów bezużytecznych, co jest spowodowane liczbą ciągów, które zostały utworzone i usunięte.

![Ostrzeżenia dotyczące wydajności][14]

## <a name="4-make-changes-and-compare-performance"></a>4: wprowadzanie zmian i porównywanie wydajności
Możesz również porównać wydajność przed i po zmianie kodu.  Zatrzymaj uruchomiony proces i edytuj kod w celu zastąpienia operacji łączenia ciągów przy użyciu elementu StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Wykonaj inną wydajność, a następnie Porównaj wydajność. W Eksplorator wydajności, jeśli są uruchomione w tej samej sesji, można po prostu wybrać oba raporty, otworzyć menu skrótów, a następnie wybrać **PORÓWNAJ raporty wydajności**. Jeśli chcesz porównać z przebiegiem w innej sesji wydajności, otwórz menu **Analizuj** i wybierz polecenie **PORÓWNAJ raporty wydajności**. Określ oba pliki w wyświetlonym oknie dialogowym.

![Porównanie opcji raportów wydajności][15]

Raporty wyróżniają różnice między dwoma przebiegami.

![Raport porównawczy][16]

Gratulacje! Już zaczynasz pracę z profilerem.

## <a name="troubleshooting"></a>Rozwiązywanie problemów
* Upewnij się, że tworzysz profiluje kompilację wydania i uruchamiasz ją bez debugowania.
* Jeśli opcja Dołącz/Odłącz nie jest włączona w menu profilera, uruchom Kreatora wydajności.
* Użyj interfejsu użytkownika emulatora obliczeń, aby wyświetlić stan aplikacji. 
* Jeśli masz problemy z uruchamianiem aplikacji w emulatorze lub dołączeniem profilera, Zamknij emulator obliczeń i uruchom go ponownie. Jeśli to nie rozwiąże problemu, spróbuj ponownie uruchomić. Ten problem może wystąpić w przypadku zawieszania i usuwania uruchomionych wdrożeń przy użyciu emulatora obliczeń.
* Jeśli użyto dowolnego polecenia profilowania z wiersza polecenia, szczególnie ustawienia globalne, upewnij się, że VSPerfClrEnv/GlobalOff został wywołany i że VsPerfMon.exe został zamknięty.
* Jeśli podczas próbkowania zostanie wyświetlony komunikat "PRF0025: nie zebrano żadnych danych", sprawdź, czy załączony proces ma aktywność procesora. Aplikacje, które nie wykonuje żadnej pracy obliczeniowej, mogą nie generować danych próbkowania.  Istnieje również możliwość, że proces zakończył działanie przed wykonaniem pobierania próbek. Sprawdź, czy metoda run dla profilowania roli nie kończy się.

## <a name="next-steps"></a>Następne kroki
Instrumentacja plików binarnych platformy Azure w emulatorze nie jest obsługiwana w programie Visual Studio profiler, ale jeśli chcesz przetestować alokację pamięci, możesz wybrać tę opcję podczas profilowania. Możesz również wybrać profilowanie współbieżności, który pomaga określić, czy wątki są marnowane czasu na zablokowanie, czy też w warstwach profilowanie interakcji, które ułatwiają Śledzenie problemów z wydajnością podczas interakcji między warstwami aplikacji, najczęściej między warstwą danych i rolą proces roboczy.  Możesz wyświetlić kwerendy bazy danych, które aplikacja generuje, i użyć danych profilowania do usprawnienia korzystania z bazy danych. Aby uzyskać informacje na temat profilowania interakcji między warstwami, zobacz Przewodnik po wpisie [w blogu: używanie profilera interakcji z warstwą w programie Visual Studio Team System 2010][3].

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png