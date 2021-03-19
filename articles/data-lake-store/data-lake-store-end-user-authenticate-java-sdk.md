---
title: Uwierzytelnianie użytkowników końcowych — Java z Data Lake Storage Gen1 — Azure
description: Dowiedz się, jak uzyskać uwierzytelnianie użytkowników końcowych za pomocą Azure Data Lake Storage Gen1 przy użyciu Azure Active Directory z językiem Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87318865"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Uwierzytelnianie użytkownika końcowego za pomocą Azure Data Lake Storage Gen1 przy użyciu języka Java
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

Ten artykuł zawiera informacje na temat używania zestawu SDK języka Java do uwierzytelniania użytkowników końcowych przy użyciu Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie między usługami i Data Lake Storage Gen1 przy użyciu zestawu Java SDK, zobacz Uwierzytelnianie między usługami i usługą [Data Lake Storage Gen1 przy użyciu języka Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz Azure Active Directory aplikację "native"**. Należy wykonać czynności opisane w temacie [uwierzytelnianie użytkowników końcowych z Data Lake Storage Gen1 przy użyciu Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Ten samouczek używa programu Maven na potrzeby zależności między kompilacją i projektem. Chociaż istnieje możliwość kompilacji bez używania systemu kompilacji, takiego jak Maven lub Gradle, systemy te znacznie ułatwiają zarządzanie zależnościami.

* (Opcjonalnie) Wtyczka [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) lub [Eclipse](https://www.eclipse.org/downloads/) przypominająca środowisko IDE lub podobna.

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
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
   
    Pierwsza zależność polega na użyciu zestawu SDK Data Lake Storage Gen1 ( `azure-data-lake-store-sdk` ) z repozytorium Maven. Drugą zależnością (`slf4j-nop`) jest określenie struktury rejestrowania używanej dla tej aplikacji. Zestaw Data Lake Storage Gen1 SDK używa fasady rejestrowania [SLF4J](https://www.slf4j.org/) , która umożliwia wybór spośród wielu popularnych platform rejestrowania, takich jak Log4J, rejestrowanie w języku Java, Logback itp. lub bez rejestrowania. W tym przykładzie wyłączyliśmy rejestrowanie, dlatego używamy powiązania **slf4j-nop**. Aby używać innych opcji rejestrowania w aplikacji, zobacz [tutaj](https://www.slf4j.org/manual.html#projectDep).

3. Dodaj do swojej aplikacji następujące instrukcje importowania.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Użyj poniższego fragmentu kodu w aplikacji Java, aby uzyskać token dla aplikacji Active Directory Native utworzonej wcześniej przy użyciu narzędzia `DeviceCodeTokenProvider` . Zastąp pole **Wypełnij w tym miejscu** wartościami rzeczywistymi Azure Active Directory aplikacji natywnych.

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

Zestaw Data Lake Storage Gen1 SDK oferuje wygodne metody umożliwiające zarządzanie tokenami zabezpieczającymi, które są potrzebne do komunikowania się z kontem Data Lake Storage Gen1. Jednak zestaw SDK nie zmusza do używania tylko tych metod. Możesz również używać jakichkolwiek innych sposobów uzyskiwania tokenu, na przykład przy użyciu [zestawu SDK usługi Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java) lub własnego kodu niestandardowego.

## <a name="next-steps"></a>Następne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Java SDK. Teraz możesz zapoznać się z następującymi artykułami dotyczącymi używania zestawu SDK języka Java do pracy z Azure Data Lake Storage Gen1.

* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu Java SDK](data-lake-store-get-started-java-sdk.md)


