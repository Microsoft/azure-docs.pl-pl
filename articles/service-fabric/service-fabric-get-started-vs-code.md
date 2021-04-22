---
title: Usługa Azure Service Fabric z VS Code Wprowadzenie
description: Ten artykuł zawiera omówienie tworzenia aplikacji Service Fabric przy użyciu Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876083"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric for Visual Studio Code

Rozszerzenie [Service Fabric Reliable Services dla programu VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) udostępnia narzędzia niezbędne do tworzenia, kompilowania i debugowania aplikacji Service Fabric w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł zawiera omówienie wymagań i konfiguracji rozszerzenia, a także użycia różnych poleceń dostarczanych przez rozszerzenie. 

> [!IMPORTANT]
> Service Fabric Java można programowania na maszynach z systemem Windows, ale można je wdrażać tylko w klastrach systemu Linux na platformie Azure. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne muszą być zainstalowane we wszystkich środowiskach.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Usługa Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Generatory Yeoman — instalowanie odpowiednich generatorów dla aplikacji

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

W celu programowania w języku Java należy zainstalować następujące wymagania wstępne:

* [Zestaw Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (wersja 1.8)
* [Gradle](https://gradle.org/install/)
* [Debuger rozszerzenia VS Code Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Wymagane do debugowania usług Java. Debugowanie usług Języka Java jest obsługiwane tylko w systemie Linux. Instalację można zainstalować, klikając ikonę  Rozszerzenia na pasku działań w witrynie VS Code i wyszukując rozszerzenie, lub w witrynie VS Code Marketplace.

Na platformie .NET Core/C# należy zainstalować następujące wymagania wstępne:

* [.NET Core](https://dotnet.microsoft.com/download) (wersja 2.0.0 lub nowsza)
* [Rozszerzenie języka C# Visual Studio Code (obsługiwane przez rozwiązanie OmniSharp VS Code)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Wymagane do debugowania usług języka C#. Instalację można zainstalować, klikając ikonę  Rozszerzenia na pasku działań w witrynie VS Code i wyszukując rozszerzenie lub VS Code Marketplace.

## <a name="setup"></a>Konfiguracja

1. Otwórz program VS Code.
2. Kliknij ikonę Rozszerzenia na pasku **działań** po lewej stronie VS Code. Wyszukaj "Service Fabric". Kliknij **przycisk Zainstaluj** dla Service Fabric Reliable Services rozszerzenia.

## <a name="commands"></a>Polecenia
Rozszerzenie Service Fabric Reliable Services dla VS Code udostępnia wiele poleceń, które ułatwiają deweloperom tworzenie i wdrażanie Service Fabric projektów. Polecenia można wywołać  z palety poleceń, naciskając klawisz , wpisując nazwę polecenia na pasku wejściowym i wybierając odpowiednie polecenie `(Ctrl + Shift + p)` z listy monitów. 

* Service Fabric: Tworzenie aplikacji 
* Service Fabric: Publikowanie aplikacji 
* Service Fabric: wdrażanie aplikacji 
* Service Fabric: Usuwanie aplikacji  
* Service Fabric: Tworzenie aplikacji 
* Service Fabric: Czyszczenie aplikacji 

### <a name="service-fabric-create-application"></a>Service Fabric: Tworzenie aplikacji

Polecenie **Service Fabric: Create Application** tworzy nową Service Fabric aplikacji w bieżącym obszarze roboczym. W zależności od tego, które generatory yeoman są zainstalowane na komputerze dewelopera, można utworzyć kilka typów aplikacji Service Fabric, w tym projekty Java, C#, Container i Guest. 

1.  Wybierz polecenie **Service Fabric: Create Application (Utwórz** aplikację).
2.  Wybierz typ nowej aplikacji Service Fabric aplikacji. 
3.  Wprowadź nazwę aplikacji, którą chcesz utworzyć
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji Service Fabric aplikacji. 
4.  Postępuj zgodnie z monitami, aby nazwać usługę. 
5.  Nowa aplikacja Service Fabric zostanie wyświetlona w obszarze roboczym.
6.  Otwórz nowy folder aplikacji, aby stał się folderem głównym w obszarze roboczym. Możesz kontynuować wykonywanie poleceń z tego miejscu.

### <a name="service-fabric-add-service"></a>Service Fabric: Dodawanie usługi
Polecenie **Service Fabric: Dodaj usługę** dodaje nową usługę do istniejącej Service Fabric aplikacji. Aplikacja, do których zostanie dodana usługa, musi być katalogiem głównym obszaru roboczego. 

1.  Wybierz polecenie **Service Fabric: Add Service (Dodaj usługę).**
2.  Wybierz typ bieżącej Service Fabric aplikacji. 
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji Service Fabric aplikacji. 
4.  Postępuj zgodnie z monitami, aby nazwać usługę. 
5.  Nowa usługa zostanie wyświetlona w katalogu projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publikowanie aplikacji
Polecenie **Service Fabric: Publish Application** wdraża aplikację Service Fabric w klastrze zdalnym. Klaster docelowy może być zabezpieczony lub niezabezpieczony. Jeśli parametry nie są Cloud.jswł., aplikacja zostanie wdrożona w klastrze lokalnym.

1.  Podczas pierwszego tworzyć aplikacji, Cloud.jsplik jest generowany w katalogu projektu.
2.  Wprowadź wartości klastra, z który chcesz nawiązać połączenie, w Cloud.jspliku.
3.  Wybierz polecenie **Service Fabric: Publish Application (Publikuj aplikację).**
4.  Wyświetl klaster docelowy z Service Fabric Explorer, aby potwierdzić, że aplikacja została zainstalowana. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: wdrażanie aplikacji (Localhost)
Polecenie **Service Fabric: Deploy Application** wdraża aplikację Service Fabric w klastrze lokalnym. Przed użyciem polecenia upewnij się, że klaster lokalny jest uruchomiony. 

1. Wybierz polecenie **Service Fabric: Deploy Application (Wdrażanie** aplikacji)
2. Wyświetl klaster lokalny z Service Fabric Explorer (http: \/ /localhost:19080/Explorer), aby potwierdzić, że aplikacja została zainstalowana. Może to potrwać pewien czas, dlatego należy się o cierpliwość.
3. Można również użyć **polecenia Service Fabric: Publikowanie** aplikacji bez parametrów ustawionych w pliku Cloud.jsw celu wdrożenia w klastrze lokalnym.

> [!NOTE]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na maszynach z systemem Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Usuwanie aplikacji
Polecenie **Service Fabric: Remove Application** usuwa aplikację Service Fabric z klastra, w który została wcześniej wdrożona, przy użyciu VS Code rozszerzenia. 

1.  Wybierz polecenie **Service Fabric: Usuń** aplikację.
2.  Wyświetl klaster z Service Fabric Explorer, aby potwierdzić, że aplikacja została usunięta. Może to potrwać pewien czas, dlatego należy się o cierpliwość.

### <a name="service-fabric-build-application"></a>Service Fabric: Tworzenie aplikacji
Polecenie **Service Fabric: Build Application** umożliwia tworzenie aplikacji w języku Java lub C# Service Fabric aplikacji. 

1.  Przed wykonaniem tego polecenia upewnij się, że jesteś w folderze głównym aplikacji. Polecenie identyfikuje typ aplikacji (C# lub Java) i odpowiednio tworzy aplikację.
2.  Wybierz polecenie **Service Fabric: Build Application (Aplikacja kompilacji).**
3.  Dane wyjściowe procesu kompilacji są zapisywane w zintegrowanym terminalu.

### <a name="service-fabric-clean-application"></a>Service Fabric: Czyszczenie aplikacji
Polecenie **Service Fabric: Clean Application** usuwa wszystkie pliki jar i biblioteki natywne wygenerowane przez kompilację. Prawidłowe tylko dla aplikacji Java. 

1.  Przed wykonaniem tego polecenia upewnij się, że jesteś w folderze głównym aplikacji. 
2.  Wybierz polecenie **Service Fabric: Clean Application** (Czyszczenie aplikacji).
3.  Dane wyjściowe procesu czyszczenia są zapisywane w zintegrowanym terminalu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [opracowywać i debugować aplikacje Service Fabric C# za pomocą VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Dowiedz się, jak [opracowywać i debugować aplikacje Java Service Fabric za pomocą VS Code](./service-fabric-develop-java-applications-with-vs-code.md).