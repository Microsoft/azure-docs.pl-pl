---
title: Nawiązywanie połączenia z interfejsem API Azure Media Services v3 — Java
description: W tym artykule opisano sposób nawiązywania połączenia z interfejsem API programu Azure Media Services v3 przy użyciu języka Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 8f5044a5fb43956e857bc7b93b92e15d40225326
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013240"
---
# <a name="connect-to-media-services-v3-api---java"></a>Nawiązywanie połączenia z interfejsem API Media Services v3 — Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule pokazano, jak nawiązać połączenie z zestawem SDK Java Azure Media Services v3 przy użyciu głównej metody logowania.

W tym artykule Visual Studio Code jest używany do tworzenia przykładowej aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Postępuj zgodnie [z pisaniem środowiska Java Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) , aby zainstalować program:

   - Zestaw JDK.
   - Apache Maven
   - Pakiet rozszerzeń języka Java
- Upewnij się, że ustawisz `JAVA_HOME` `PATH` zmienne środowiskowe i.
- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.
- Wykonaj kroki opisane w temacie [interfejsy API dostępu](./access-api-howto.md) . Zapisz identyfikator subskrypcji, identyfikator aplikacji (identyfikator klienta), klucz uwierzytelniania (klucz tajny) i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

Przejrzyj również:

- [Środowisko Java w Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Zarządzanie projektami Java w VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Tworzenie projektu Maven

Otwórz narzędzie wiersza polecenia i `cd` do katalogu, w którym chcesz utworzyć projekt.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Po uruchomieniu polecenia `pom.xml` `App.java` są tworzone pliki, i. 

## <a name="add-dependencies"></a>Dodaj zależności

1. W Visual Studio Code Otwórz folder, w którym znajduje się projekt
1. Znajdź i Otwórz `pom.xml`
1. Dodawanie wymaganych zależności

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Nawiązywanie połączenia z klientem Java

1. Otwórz `App.java` plik w obszarze `src\main\java\com\azure\ams` i upewnij się, że pakiet znajduje się na górze:

    ```java
    package com.azure.ams;
    ```
1. W obszarze Instrukcja pakietu Dodaj następujące instrukcje importu:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Aby utworzyć Active Directory poświadczenia, które należy wykonać, Dodaj następujący kod do metody Main klasy App i ustaw wartości uzyskanych z [interfejsów API dostępu](./access-api-howto.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Uruchom aplikację.

## <a name="see-also"></a>Zobacz też

- [Koncepcje Media Services](concepts-overview.md)
- [Zestaw SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Dokumentacja języka Java](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01: zarządzanie na platformie Azure](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Następne kroki

Można teraz dołączać `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` i uruchamiać manipulowanie jednostkami.

Aby uzyskać więcej przykładów kodu, zobacz repozytorium [przykładów zestawu SDK języka Java](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) .