---
title: Tworzenie funkcji Kotlin w Azure Functions przy użyciu IntelliJ
description: Dowiedz się, jak za pomocą IntelliJ utworzyć prostą funkcję Kotlin wyzwalaną przez protokół HTTP, która następnie zostanie uruchomiona w środowisku bezserwerowym na platformie Azure.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f02643ee28d76d4f90206a1aa2879b4672da2a38
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179463"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>Tworzenie pierwszej funkcji Kotlin na platformie Azure przy użyciu usługi IntelliJ

W tym artykule opisano sposób tworzenia funkcji Java wyzwalanej przez protokół HTTP w projekcie POMYSŁu IntelliJ, uruchamiania i debugowania projektu w zintegrowanym środowisku programistycznym (IDE), a wreszcie wdrożyć projekt funkcji w aplikacji funkcji na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby tworzyć i publikować funkcje Kotlin na platformie Azure przy użyciu programu IntelliJ, Zainstaluj następujące oprogramowanie:

- [Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), wersja 8
- [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza
- [INTELLIJ pomysł](https://www.jetbrains.com/idea/download), wersja Community lub Ultimate z Maven
- [Interfejs wiersza polecenia platformy Azure](/cli/azure)
- [Wersja 2. x](functions-run-local.md#v2) Azure Functions Core Tools. Zapewnia lokalne środowisko programistyczne do pisania, uruchamiania i debugowania Azure Functions.

> [!IMPORTANT]
> Aby wykonać kroki opisane w tym artykule, zmienna środowiskowa JAVA_HOME musi być ustawiona na lokalizację instalacji JDK.

## <a name="create-a-function-project"></a>Tworzenie projektu funkcji

1. W IntelliJ pomysł wybierz pozycję **Utwórz nowy projekt**.  
1. W oknie **Nowy projekt** w okienku po lewej stronie wybierz pozycję **Maven** .
1. Zaznacz pole wyboru **Utwórz z Archetype** , a następnie wybierz pozycję **Dodaj Archetype** dla [platformy Azure-Functions-Kotlin-Archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype).
1. W oknie **Dodawanie Archetype** Wypełnij pola w następujący sposób:
    - _GroupID_: com. Microsoft. Azure
    - _ArtifactId_: Azure-Functions-Kotlin-Archetype
    - _Wersja_: Użyj najnowszej wersji z [repozytorium centralnego](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 
     ![ Utwórz projekt Maven z Archetype w IntelliJ pomysłie](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. Wybierz przycisk **OK**, a następnie wybierz przycisk **dalej**.
1. Wprowadź szczegóły dla bieżącego projektu i wybierz pozycję **Zakończ**.

Maven tworzy pliki projektu w nowym folderze o tej samej nazwie, co wartość _ArtifactId_ . Wygenerowany kod projektu jest prostą funkcją [wyzwalaną przez protokół http](./functions-bindings-http-webhook.md) , która umożliwia echo treści wyzwalanego żądania HTTP.

## <a name="run-project-locally-in-the-ide"></a>Uruchamianie projektu lokalnie w środowisku IDE

> [!NOTE]
> Aby uruchomić i debugować projekt lokalnie, upewnij się, że zainstalowano [Azure Functions Core Tools, wersja 2](functions-run-local.md#v2).

1. Importuj zmiany ręcznie lub Włącz [autoimport](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otwórz pasek narzędzi **projektów Maven** .
1. Rozwiń węzeł **cykl życia**, a następnie otwórz **pakiet**. Rozwiązanie jest skompilowane i spakowane w nowo utworzonym katalogu docelowym.
1. Rozwiń węzeł **wtyczki**  >  **Azure-Functions** i Otwórz pozycję **Azure-Functions: Run** , aby uruchomić Azure Functions lokalnego środowiska uruchomieniowego.  
  ![Maven pasek narzędzi dla Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Zamknij okno dialogowe uruchamiania po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="debug-the-project-in-intellij"></a>Debuguj projekt w IntelliJ

1. Aby uruchomić hosta funkcji w trybie debugowania, należy dodać **-DenableDebug** jako argument podczas uruchamiania funkcji. Konfigurację można zmienić w [celach Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) lub uruchomić następujące polecenie w oknie terminalu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   To polecenie powoduje, że host funkcji otwiera port debugowania w 5005.

1. W menu **Uruchom** wybierz polecenie **Edytuj konfiguracje**.
1. Wybierz pozycję **(+)** , aby dodać element **zdalny**.
1. Wypełnij pola _Nazwa_ i _Ustawienia_ , a następnie wybierz przycisk **OK** , aby zapisać konfigurację.
1. Po zakończeniu instalacji wybierz kolejno opcje **debuguj < nazwa konfiguracji zdalnej >** lub naciśnij klawisze SHIFT + F9 na klawiaturze, aby rozpocząć debugowanie.

   ![Debuguj projekt w IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Po zakończeniu Zatrzymaj debuger i uruchomiony proces. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="deploy-the-project-to-azure"></a>Wdrażanie projektu na platformie Azure

1. Aby można było wdrożyć projekt w aplikacji funkcji na platformie Azure, musisz [zalogować się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

   ``` azurecli
   az login
   ```

1. Wdróż swój kod w nowej aplikacji funkcji przy użyciu `azure-functions:deploy` elementu docelowego Maven. Możesz również wybrać opcję **Azure-Functions: Deploy** w oknie projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Znajdź adres URL funkcji wyzwalacza HTTP w danych wyjściowych interfejsu wiersza polecenia platformy Azure po pomyślnym wdrożeniu aplikacji funkcji.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu pierwszej aplikacji funkcji Kotlin na platformie Azure Zapoznaj się Azure Functions z [przewodnikiem dewelopera dla języka Java](functions-reference-java.md) , aby uzyskać więcej informacji na temat opracowywania funkcji Java i Kotlin.
- Dodaj dodatkowe aplikacje funkcji z różnymi wyzwalaczami do projektu przy użyciu `azure-functions:add` elementu docelowego Maven.
