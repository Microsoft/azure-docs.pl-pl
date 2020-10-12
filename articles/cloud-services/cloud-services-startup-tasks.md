---
title: Uruchamianie zadań uruchamiania na platformie Azure Cloud Services | Microsoft Docs
description: Zadania uruchamiania ułatwiają przygotowanie środowiska usługi w chmurze dla Twojej aplikacji. Naucz się, jak działają zadania uruchamiania i jak je wprowadzać
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/05/2017
ms.author: tagore
ms.openlocfilehash: fa48953e5e86ffa758fe556b7fb1072be9d74647
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75360314"
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Jak skonfigurować i uruchomić zadania uruchamiania dla usługi w chmurze
Zadania uruchamiania umożliwiają wykonywanie operacji przed rozpoczęciem roli. Operacje, które można wykonać, obejmują zainstalowanie składnika, zarejestrowanie składników modelu COM, ustawienie kluczy rejestru lub uruchomienie długotrwałego procesu.

> [!NOTE]
> Zadania uruchamiania nie mają zastosowania do Virtual Machines tylko do ról Sieć Web i proces roboczy usługi w chmurze.
> 
> 

## <a name="how-startup-tasks-work"></a>Jak działają zadania uruchamiania
Zadania uruchamiania to akcje, które są wykonywane przed rozpoczęciem ról i są zdefiniowane w pliku [ServiceDefinition. csdef] za pomocą elementu [Task] w ramach elementu [startowego] . Często zadania uruchamiania to pliki wsadowe, ale mogą to być również aplikacje konsolowe lub pliki wsadowe uruchamiające skrypty programu PowerShell.

Zmienne środowiskowe przekazują informacje do zadania uruchamiania, a Magazyn lokalny może służyć do przekazywania informacji z zadania uruchamiania. Na przykład zmienna środowiskowa może określać ścieżkę do programu, który chcesz zainstalować, a pliki mogą być zapisywane w magazynie lokalnym, które mogą być później odczytywane przez role.

Zadanie uruchamiania może rejestrować informacje i błędy do katalogu określonego przez zmienną środowiskową **temp** . Podczas zadania uruchamiania, zmienna środowiskowa **temp** jest rozpoznawana jako *C: \\ resources \\ temp \\ [Guid]. [ rolename] \\ RoleTemp* Directory podczas uruchamiania w chmurze.

Zadania uruchamiania mogą być również wykonywane kilka razy między ponownymi uruchomieniami. Na przykład zadanie uruchamiania będzie uruchamiane przy każdym odtworzeniu roli, ale odtwarzanie roli nie zawsze obejmuje ponowne uruchomienie. Zadania uruchamiania należy zapisywać w taki sposób, aby umożliwiały ich uruchamianie kilka razy bez problemów.

Zadania uruchamiania muszą kończyć się liczbą **ERRORLEVEL** (lub kodem zakończenia) równą zero, aby zakończyć proces uruchamiania. Jeśli zadanie uruchamiania zakończy się niezerem **ERRORLEVEL**, rola nie zostanie uruchomiona.

## <a name="role-startup-order"></a>Kolejność uruchamiania roli
Poniżej wymieniono procedurę uruchamiania roli na platformie Azure:

1. Wystąpienie jest oznaczone jako **Uruchamianie** i nie odbiera ruchu.
2. Wszystkie zadania uruchamiania są wykonywane zgodnie z ich atrybutem **TaskType** .
   
   * **Proste** zadania są wykonywane synchronicznie, po jednym naraz.
   * Zadania **tła** i **pierwszego planu** są uruchamiane asynchronicznie, równolegle do zadania uruchamiania.  
     
     > [!WARNING]
     > Usługi IIS mogą nie być w pełni skonfigurowane podczas etapu zadania uruchamiania w procesie uruchamiania, dlatego dane specyficzne dla roli mogą być niedostępne. Zadania uruchamiania, które wymagają danych specyficznych dla roli, powinny używać [Microsoft. windowsazure. serviceruntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)).
     > 
     > 
3. Proces hosta roli został uruchomiony, a lokacja jest tworzona w usługach IIS.
4. Metoda [Microsoft. windowsazure. serviceruntime. RoleEntryPoint. OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) jest wywoływana.
5. Wystąpienie jest oznaczone jako **gotowe** i ruch jest kierowany do wystąpienia.
6. Metoda [Microsoft. windowsazure. serviceruntime. RoleEntryPoint. Run](/previous-versions/azure/reference/ee772746(v=azure.100)) jest wywoływana.

## <a name="example-of-a-startup-task"></a>Przykład zadania uruchamiania
Zadania uruchamiania są zdefiniowane w pliku [ServiceDefinition. csdef] w elemencie **Task** . Atrybut **CommandLine** określa nazwę i parametry pliku wsadowego uruchamiania lub konsoli, atrybut **kontekście wykonywania** określa poziom uprawnień zadania uruchamiania, a atrybut **TaskType** określa sposób wykonywania zadania.

W tym przykładzie zmienna środowiskowa **MyVersionNumber**, jest tworzona dla zadania uruchamiania i ma ustawioną wartość "**1.0.0.0**".

**ServiceDefinition. csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

W poniższym przykładzie plik wsadowy **Startup. cmd** zapisuje wiersz "aktualna wersja pliku 1.0.0.0" do StartupLog.txtnego w katalogu określonym przez zmienną środowiskową temp. `EXIT /B 0`Wiersz gwarantuje, że zadanie uruchamiania zostanie zakończone przy użyciu **zmiennej ERRORLEVEL** równej zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> W programie Visual Studio Właściwość **Kopiuj do katalogu wyjściowego** dla pliku wsadowego uruchamiania powinna być ustawiona na wartość **Kopiuj zawsze** upewnij się, że plik wsadowy uruchamiania jest prawidłowo wdrożony w projekcie na platformie Azure (**głównego aplikacji \\ bin** for Web Roles i **głównego aplikacji** for Worker role).
> 
> 

## <a name="description-of-task-attributes"></a>Opis atrybutów elementu Task
Poniżej opisano atrybuty elementu **Task** w pliku [ServiceDefinition. csdef] :

**CommandLine** — określa wiersz polecenia dla zadania uruchamiania:

* Polecenie z opcjonalnymi parametrami wiersza polecenia, które rozpoczyna zadanie uruchamiania.
* Często jest to nazwa pliku wsadowego. cmd lub. bat.
* Zadanie jest względem \\ folderu bin głównego aplikacji dla wdrożenia. Zmienne środowiskowe nie są rozszerzane podczas określania ścieżki i pliku zadania. Jeśli jest wymagane rozwinięcie środowiska, można utworzyć mały skrypt. cmd, który wywołuje zadanie uruchamiania.
* Może to być Aplikacja konsolowa lub plik wsadowy, który uruchamia [skrypt programu PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**kontekście wykonywania** — określa poziom uprawnień dla zadania uruchamiania. Poziom uprawnień może być ograniczony lub podwyższony:

* **Separator**  
  Zadanie uruchamiania jest uruchamiane z tymi samymi uprawnieniami co rola. Gdy atrybut **kontekście wykonywania** dla elementu [Runtime] jest również **ograniczony**, są używane uprawnienia użytkownika.
* **pełny**  
  Zadanie uruchamiania jest uruchamiane z uprawnieniami administratora. Dzięki temu zadania uruchamiania mogą instalować programy, wprowadzać zmiany w konfiguracji usług IIS, wykonywać zmiany w rejestrze i inne zadania na poziomie administratora bez zwiększania poziomu uprawnień samej roli.  

> [!NOTE]
> Poziom uprawnień zadania uruchamiania nie musi być taki sam jak sama rola.
> 
> 

**TaskType** — określa sposób wykonywania zadania uruchamiania.

* **ułatwia**  
  Zadania są wykonywane synchronicznie, pojedynczo w kolejności określonej w pliku [ServiceDefinition. csdef] . Po zakończeniu jednego **prostego** zadania uruchamiania z liczbą **ERRORLEVEL** równą zero, wykonywane jest następne **proste** zadanie uruchamiania. Jeśli nie ma więcej **prostych** zadań uruchamiania do wykonania, zostanie uruchomiona sama rola.   
  
  > [!NOTE]
  > Jeśli **proste** zadanie zakończy się niezerem **ERRORLEVEL**, wystąpienie zostanie zablokowane. Kolejne **proste** zadania uruchamiania i sama rola nie zostaną uruchomione.
  > 
  > 
  
    Aby upewnić się, że plik wsadowy kończy się liczbą **ERRORLEVEL** równą zero, wykonaj polecenie `EXIT /B 0` na końcu procesu pliku wsadowego.
* **background**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchomieniem roli.
* **pierwszego planu**  
  Zadania są wykonywane asynchronicznie, równolegle z uruchomieniem roli. Kluczowa różnica między **pierwszym** i zadaniem w **tle** polega na tym, że zadanie na **pierwszym planie** uniemożliwia odtworzenie lub zamknięcie roli do momentu zakończenia zadania. Zadania w **tle** nie mają tego ograniczenia.

## <a name="environment-variables"></a>Zmienne środowiskowe
Zmienne środowiskowe umożliwiają przekazywanie informacji do zadania uruchamiania. Można na przykład umieścić ścieżkę do obiektu BLOB, który zawiera program do zainstalowania, lub numery portów, które będą używane przez rolę, lub ustawienia kontroli funkcji zadania uruchamiania.

Istnieją dwa rodzaje zmiennych środowiskowych dla zadań uruchamiania; statyczne zmienne środowiskowe i zmienne środowiskowe oparte na elementach członkowskich klasy [RoleEnvironment] . Oba znajdują się w sekcji [Environment] pliku [ServiceDefinition. csdef] i używają atrybutu element [zmiennej] i **nazwy** .

Zmienne środowiskowe static używają atrybutu **Value** elementu [Variable] . W powyższym przykładzie powstaje zmienna środowiskowa **MyVersionNumber** , która ma wartość statyczną "**1.0.0.0**". Innym przykładem jest utworzenie zmiennej środowiskowej **StagingOrProduction** , którą można ręcznie ustawić na wartości "**przemieszczanie**" lub "**produkcja**", aby wykonać różne akcje uruchamiania na podstawie wartości zmiennej środowiskowej **StagingOrProduction** .

Zmienne środowiskowe oparte na elementach członkowskich klasy RoleEnvironment nie używają atrybutu **Value** elementu [Variable] . Zamiast tego element podrzędny [RoleInstanceValue] z odpowiednią wartością atrybutu **XPath** służy do tworzenia zmiennej środowiskowej na podstawie określonego elementu członkowskiego klasy [RoleEnvironment] . Wartości atrybutu **XPath** umożliwiające dostęp do różnych wartości [RoleEnvironment] można znaleźć [tutaj](cloud-services-role-config-xpath.md).

Na przykład, aby utworzyć zmienną środowiskową "**true**", gdy wystąpienie jest uruchomione w emulatorze obliczeniowym i "**false**" podczas działania w chmurze, należy użyć następującej [zmiennej] i elementów [RoleInstanceValue] :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak wykonywać [typowe zadania uruchomieniowe](cloud-services-startup-tasks-common.md) w usłudze w chmurze.

[Pakowanie](cloud-services-model-and-package.md) usługi w chmurze.  

[ServiceDefinition. csdef]: cloud-services-model-and-package.md#csdef
[Zadanie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Uruchamianie]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Środowisko uruchomieniowe]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Środowisko]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Zmienna]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx



