---
title: Service Fabric platformy Azure z VS Code Wprowadzenie
description: Ten artykuł zawiera omówienie tworzenia Service Fabric aplikacji przy użyciu Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a655199d854462285ca98a2d8f454c483c061f99
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92316668"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric Visual Studio Code

[Rozszerzenie Service Fabric Reliable Services dla vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) zawiera narzędzia niezbędne do tworzenia, kompilowania i debugowania aplikacji Service Fabric w systemach operacyjnych Windows, Linux i macOS.

Ten artykuł zawiera omówienie wymagań i konfiguracji rozszerzenia oraz użycie różnych poleceń, które są udostępniane przez rozszerzenie. 

> [!IMPORTANT]
> Aplikacje Java Service Fabric można opracowywać na maszynach z systemem Windows, ale można je wdrożyć tylko w klastrach systemu Linux platformy Azure. Debugowanie aplikacji Java nie jest obsługiwane w systemie Windows.

## <a name="prerequisites"></a>Wymagania wstępne

Poniższe wymagania wstępne muszą być zainstalowane we wszystkich środowiskach.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Usługa Git](https://git-scm.com/)
* [Zestaw SDK Service Fabric](./service-fabric-get-started.md)
* Generatory narzędzia Yeoman — Zainstaluj odpowiednie generatory dla aplikacji

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Do programowania w języku Java należy zainstalować następujące wymagania wstępne:

* [Zestaw Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (wersja 1,8)
* [Gradle](https://gradle.org/install/)
* [Debuger dla rozszerzenia vs Code Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Wymagany do debugowania usług Java. Debugowanie usług Java jest obsługiwane tylko w systemie Linux. Możesz zainstalować program, klikając ikonę rozszerzenia na **pasku działania** w vs Code i wyszukując rozszerzenie lub z poziomu witryny vs Code Marketplace.

Następujące wymagania wstępne muszą być zainstalowane na potrzeby programowania w środowisku .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) (wersja 2.0.0 lub nowsza)
* [Rozszerzenie C# dla Visual Studio Code (obsługiwane przez OmniSharp) vs Code rozszerzenia](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Wymagany do debugowania usług C#. Możesz zainstalować program, klikając ikonę rozszerzenia na **pasku działania** w vs Code i wyszukując rozszerzenie lub z poziomu witryny vs Code Marketplace.

## <a name="setup"></a>Konfiguracja

1. Otwórz program VS Code.
2. Kliknij ikonę rozszerzenia na **pasku działania** po lewej stronie vs Code. Wyszukaj ciąg "Service Fabric". Kliknij przycisk **Instaluj** dla rozszerzenia Service Fabric Reliable Services.

## <a name="commands"></a>Polecenia
Rozszerzenie Service Fabric Reliable Services dla VS Code zawiera wiele poleceń, które ułatwiają deweloperom tworzenie i wdrażanie projektów Service Fabric. Możesz wywołać polecenia z **palety poleceń** , naciskając klawisz `(Ctrl + Shift + p)` , wpisując nazwę polecenia na pasku wprowadzania i wybierając odpowiednie polecenie z listy monitów. 

* Service Fabric: Tworzenie aplikacji 
* Service Fabric: publikowanie aplikacji 
* Service Fabric: wdrażanie aplikacji 
* Service Fabric: Usuwanie aplikacji  
* Service Fabric: kompilowanie aplikacji 
* Service Fabric: czyszczenie aplikacji 

### <a name="service-fabric-create-application"></a>Service Fabric: Tworzenie aplikacji

Polecenie **Service Fabric: Create Application** tworzy nową aplikację Service Fabric w bieżącym obszarze roboczym. W zależności od tego, które generatory narzędzia Yeoman są zainstalowane na komputerze deweloperskim, można utworzyć kilka typów aplikacji Service Fabric, takich jak Java, C#, kontener i projekty gościa. 

1.  Wybierz polecenie **Service Fabric: Create Application**
2.  Wybierz typ dla nowej aplikacji Service Fabric. 
3.  Wprowadź nazwę aplikacji, którą chcesz utworzyć
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji Service Fabric. 
4.  Postępuj zgodnie z monitami, aby nawiązać nazwę usługi. 
5.  Nowa aplikacja Service Fabric zostanie wyświetlona w obszarze roboczym.
6.  Otwórz nowy folder aplikacji tak, aby stał się folderem głównym w obszarze roboczym. Możesz kontynuować wykonywanie poleceń w tym miejscu.

### <a name="service-fabric-add-service"></a>Service Fabric: Dodawanie usługi
**Service Fabric: Add Service** polecenie dodaje nową usługę do istniejącej aplikacji Service Fabric. Aplikacja, do której zostanie dodana usługa, musi być katalogiem głównym obszaru roboczego. 

1.  Wybierz **Service Fabric: Dodaj usługę** .
2.  Wybierz typ bieżącej aplikacji Service Fabric. 
3.  Wybierz typ usługi, którą chcesz dodać do aplikacji Service Fabric. 
4.  Postępuj zgodnie z monitami, aby nawiązać nazwę usługi. 
5.  Nowa usługa zostanie wyświetlona w katalogu projektu. 

### <a name="service-fabric-publish-application"></a>Service Fabric: publikowanie aplikacji
**Service Fabric: Publish Application** polecenie wdraża aplikację Service Fabric w klastrze zdalnym. Klaster docelowy może być bezpiecznym lub niezabezpieczonym klastrem. Jeśli parametry nie są ustawione w Cloud.json, aplikacja zostanie wdrożona w klastrze lokalnym.

1.  Podczas pierwszego kompilowania aplikacji jest generowany Cloud.jsw pliku w katalogu projektu.
2.  Wprowadź wartości dla klastra, z którym chcesz nawiązać połączenie w Cloud.jspliku.
3.  Wybierz polecenie **Service Fabric: Publikuj aplikację** .
4.  Aby upewnić się, że aplikacja została zainstalowana, Wyświetl klaster docelowy z Service Fabric Explorer. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Wdróż aplikację (localhost)
**Service Fabric: Deploy aplikacji** polecenie wdraża aplikację Service Fabric w klastrze lokalnym. Przed użyciem polecenia upewnij się, że klaster lokalny jest uruchomiony. 

1. Wybierz polecenie **Service Fabric: Wdróż aplikację**
2. Wyświetl klaster lokalny z Service Fabric Explorer (http: \/ /localhost: 19080/Explorer), aby upewnić się, że aplikacja została zainstalowana. Może to potrwać pewien czas.
3. Możesz również użyć **Service Fabric: Publikuj** polecenie bez parametrów ustawionych w Cloud.jspliku, aby wdrożyć w klastrze lokalnym.

> [!NOTE]
> Wdrażanie aplikacji Java w klastrze lokalnym nie jest obsługiwane na maszynach z systemem Windows.

### <a name="service-fabric-remove-application"></a>Service Fabric: Usuwanie aplikacji
**Service Fabric: Remove aplikacji** usuwa aplikację Service Fabric z klastra, który został wcześniej wdrożony, przy użyciu rozszerzenia vs Code. 

1.  Wybierz polecenie **Service Fabric: Usuń aplikację** .
2.  Wyświetl klaster z Service Fabric Explorer, aby upewnić się, że aplikacja została usunięta. Może to potrwać pewien czas.

### <a name="service-fabric-build-application"></a>Service Fabric: kompilowanie aplikacji
Polecenie **Service Fabric: build Application** może kompilować aplikacje Service Fabric Java lub C#. 

1.  Przed wykonaniem tego polecenia upewnij się, że jesteś w folderze głównym aplikacji. Polecenie identyfikuje typ aplikacji (C# lub Java) i odpowiednio kompiluje aplikację.
2.  Wybierz polecenie **Service Fabric: build Application** .
3.  Dane wyjściowe procesu kompilacji są zapisywane w zintegrowanym terminalu.

### <a name="service-fabric-clean-application"></a>Service Fabric: czyszczenie aplikacji
Polecenie **Service Fabric: Clean Application** usuwa wszystkie pliki jar i biblioteki natywne, które zostały wygenerowane przez kompilację. Prawidłowy tylko dla aplikacji Java. 

1.  Przed wykonaniem tego polecenia upewnij się, że jesteś w folderze głównym aplikacji. 
2.  Wybierz polecenie **Service Fabric: Clean Application** .
3.  Dane wyjściowe procesu czystego są zapisywane w zintegrowanym terminalu.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się [, jak opracowywać i debugować aplikacje Service Fabric w języku C# przy użyciu vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Dowiedz się [, jak opracowywać i debugować aplikacje Service Fabric Java przy użyciu vs Code](./service-fabric-develop-java-applications-with-vs-code.md).