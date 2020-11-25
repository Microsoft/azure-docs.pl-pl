---
title: Uwierzytelnianie między usługami — Data Lake Storage Gen1 — zestaw Java SDK
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory z językiem Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000389"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Uwierzytelnianie między usługami Azure Data Lake Storage Gen1 przy użyciu języka Java

> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

Ten artykuł zawiera informacje na temat korzystania z zestawu SDK języka Java do uwierzytelniania między usługami przy użyciu Azure Data Lake Storage Gen1. Uwierzytelnianie użytkowników końcowych z Data Lake Storage Gen1 przy użyciu zestawu Java SDK nie jest obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikacji "Web"**. Należy wykonać kroki opisane w temacie [uwierzytelnianie między usługami Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilowania bez używania systemu kompilacji, takiego jak Maven lub Gradle, te systemy znacznie ułatwiają zarządzanie zależnościami.

* Obowiązkowe Środowisko IDE, takie jak [INTELLIJ pomysł](https://www.jetbrains.com/idea/download/) lub [przezaćmienie](https://www.eclipse.org/downloads/) lub podobne.

## <a name="service-to-service-authentication"></a>Uwierzytelnianie między usługami

1. Utwórz projekt Maven za pomocą polecenia [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) w wierszu polecenia lub w środowisku IDE. Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki IntelliJ, zobacz [tutaj](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Aby uzyskać instrukcje dotyczące sposobu tworzenia projektu Java przy użyciu wtyczki Eclipse, zobacz [tutaj](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Dodaj poniższe zależności do Twojego pliku **pom.xml** programu Maven. Dodaj następujący fragment kodu przed **\</project>** tagiem:

    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```

    Pierwsza zależność polega na użyciu zestawu SDK Data Lake Storage Gen1 ( `azure-data-lake-store-sdk` ) z repozytorium Maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw Data Lake Storage Gen1 SDK używa fasady rejestrowania [slf4j](https://www.slf4j.org/) , która umożliwia wybór spośród wielu popularnych platform rejestrowania, takich jak Log4J, rejestrowanie w języku Java, logback itp. lub bez rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Użyj poniższego fragmentu kodu w aplikacji Java, aby uzyskać token dla aplikacji sieci Web Active Directory utworzonej wcześniej przy użyciu jednej z podklas `AccessTokenProvider` (w poniższym przykładzie użyto `ClientCredsTokenProvider` ). Dostawca tokenu zapisuje w pamięci poświadczenia użyte do uzyskania tokenu i automatycznie odnawia token, gdy zbliża się czas jego wygaśnięcia. Istnieje możliwość utworzenia własnych podklas, `AccessTokenProvider` tak aby tokeny były uzyskiwane przez kod klienta. Na razie po prostu użyjemy go w zestawie SDK.

    Zastąp wartość **FILL-IN-HERE** (Wypełnij tutaj) rzeczywistymi wartościami dla aplikacji internetowej usługi Azure Active Directory.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Zestaw Data Lake Storage Gen1 SDK oferuje wygodne metody umożliwiające zarządzanie tokenami zabezpieczającymi, które są potrzebne do komunikowania się z kontem Data Lake Storage Gen1. Jednak zestaw SDK nie zmusza do używania tylko tych metod. Możesz również używać jakichkolwiek innych sposobów uzyskiwania tokenu, na przykład przy użyciu [zestawu SDK usługi Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) lub własnego kodu niestandardowego.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Java SDK. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi używania zestawu SDK języka Java do pracy z Data Lake Storage Gen1.

* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu Java SDK](data-lake-store-get-started-java-sdk.md)
