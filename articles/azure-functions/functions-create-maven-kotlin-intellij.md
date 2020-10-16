---
title: Tworzenie funkcji platformy Azure przy użyciu Kotlin i IntelliJ
description: Dowiedz się, jak utworzyć i opublikować prostą bezserwerową aplikację wywoływaną przez protokół HTTP na platformie Azure przy użyciu Kotlin i IntelliJ.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: 09dd868dc9e05241943899654d7c8bb427a8f268
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104839"
---
# <a name="quickstart-create-your-first-http-triggered-function-with-kotlin-and-intellij"></a>Szybki Start: Tworzenie pierwszej funkcji wyzwalanej przez protokół HTTP przy użyciu Kotlin i IntelliJ

W tym artykule opisano sposób tworzenia projektu funkcji bez użycia [serwera](https://azure.microsoft.com/overview/serverless-computing/) z pomysłami IntelliJ i Apache Maven. Pokazano również, jak lokalnie debugować kod funkcji w zintegrowanym środowisku programistycznym (IDE), a następnie wdrożyć projekt funkcji na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego

Aby opracować funkcję z Kotlin i IntelliJ, Zainstaluj następujące oprogramowanie:

- [Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) (JDK), wersja 8
- [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza
- [INTELLIJ pomysł](https://www.jetbrains.com/idea/download), wersja Community lub Ultimate z Maven
- [Interfejs wiersza polecenia platformy Azure](/cli/azure)
- [Wersja 2. x](functions-run-local.md#v2) Azure Functions Core Tools. Zapewnia lokalne środowisko programistyczne do pisania, uruchamiania i debugowania Azure Functions.

> [!IMPORTANT]
> Aby wykonać kroki opisane w tym artykule, zmienna środowiskowa JAVA_HOME musi być ustawiona na lokalizację instalacji JDK.

## <a name="create-a-functions-project"></a>Tworzenie projektu funkcji

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

## <a name="run-functions-locally-in-the-ide"></a>Uruchamianie funkcji lokalnie w środowisku IDE

> [!NOTE]
> Aby uruchamiać i debugować funkcje lokalnie, upewnij się, że zainstalowano [Azure Functions Core Tools w wersji 2](functions-run-local.md#v2).

1. Importuj zmiany ręcznie lub Włącz [autoimport](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html).
1. Otwórz pasek narzędzi **projektów Maven** .
1. Rozwiń węzeł **cykl życia**, a następnie otwórz **pakiet**. Rozwiązanie jest skompilowane i spakowane w nowo utworzonym katalogu docelowym.
1. Rozwiń węzeł **wtyczki**  >  **Azure-Functions** i Otwórz pozycję **Azure-Functions: Run** , aby uruchomić Azure Functions lokalnego środowiska uruchomieniowego.  
  ![Maven pasek narzędzi dla Azure Functions](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. Zamknij okno dialogowe uruchamiania po zakończeniu testowania funkcji. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="debug-the-function-in-intellij"></a>Debuguj funkcję w IntelliJ

1. Aby uruchomić hosta funkcji w trybie debugowania, należy dodać **-DenableDebug** jako argument podczas uruchamiania funkcji. Konfigurację można zmienić w [celach Maven](https://www.jetbrains.com/help/idea/maven-support.html#run_goal) lub uruchomić następujące polecenie w oknie terminalu:  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   To polecenie powoduje, że host funkcji otwiera port debugowania w 5005.

1. W menu **Uruchom** wybierz polecenie **Edytuj konfiguracje**.
1. Wybierz pozycję **(+)** , aby dodać element **zdalny**.
1. Wypełnij pola _Nazwa_ i _Ustawienia_ , a następnie wybierz przycisk **OK** , aby zapisać konfigurację.
1. Po zakończeniu instalacji wybierz kolejno opcje **debuguj < nazwa konfiguracji zdalnej >** lub naciśnij klawisze SHIFT + F9 na klawiaturze, aby rozpocząć debugowanie.

   ![Funkcje debugowania w IntelliJ](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. Po zakończeniu Zatrzymaj debuger i uruchomiony proces. Tylko jeden host funkcji może być aktywny i uruchamiany lokalnie.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

1. Aby można było wdrożyć funkcję na platformie Azure, musisz [zalogować się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

   ``` azurecli
   az login
   ```

1. Wdróż swój kod w nowej funkcji przy użyciu `azure-functions:deploy` elementu docelowego Maven. Możesz również wybrać opcję **Azure-Functions: Deploy** w oknie projekty Maven.

   ```
   mvn azure-functions:deploy
   ```

1. Znajdź adres URL funkcji w danych wyjściowych interfejsu wiersza polecenia platformy Azure po pomyślnym wdrożeniu funkcji.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>Następne kroki

Po wdrożeniu pierwszej funkcji Kotlin na platformie Azure Zapoznaj się z [przewodnikiem dewelopera usługi Java Functions](functions-reference-java.md) , aby uzyskać więcej informacji na temat opracowywania funkcji Java i Kotlin.
- Dodaj dodatkowe funkcje z różnymi wyzwalaczami do projektu przy użyciu `azure-functions:add` elementu docelowego Maven.