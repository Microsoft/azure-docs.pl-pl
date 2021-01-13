---
title: 'Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Java | Azure'
titleSuffix: Microsoft identity platform
description: W tym przewodniku szybki start dowiesz się, jak dodać logowanie za pomocą firmy Microsoft do aplikacji sieci Web Java za pomocą OpenID Connect Connect.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/09/2019
ms.author: sagonzal
ms.custom: aaddev, scenarios:getting-started, languages:Java, devx-track-java
ms.openlocfilehash: 91aa6c96c714bff26ea7e0df5b2b6971c68edec0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98178572"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>Szybki Start: Dodawanie logowania do aplikacji sieci Web w języku Java przez firmę Microsoft

W tym przewodniku szybki start pobrano i uruchomimy przykład kodu, który pokazuje, jak aplikacja sieci Web w języku Java może zalogować użytkowników i wywoływać interfejs API Microsoft Graph. Użytkownicy z dowolnej organizacji Azure Active Directory (Azure AD) mogą logować się do aplikacji.

 Aby zapoznać się z omówieniem, zobacz [Diagram przedstawiający sposób działania przykładu](#how-the-sample-works).

## <a name="prerequisites"></a>Wymagania wstępne

Do uruchomienia tego przykładu potrzebne są:

- [Zestaw Java Development Kit (JDK)](https://openjdk.java.net/) 8 lub nowszy. 
- [Maven](https://maven.apache.org/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>Rejestrowanie i pobieranie aplikacji Szybki start
> Istnieją dwa sposoby uruchamiania aplikacji szybkiego startu: Express (opcja 1) i ręczna (opcja 2).
>
> ### <a name="option-1-register-and-automatically-configure-your-app-and-then-download-the-code-sample"></a>Opcja 1: rejestrowanie i automatyczne konfigurowanie aplikacji, a następnie pobieranie przykładu kodu
>
> 1. Przejdź do środowiska szybkiego startu w <a href="https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavaQuickstartPage/sourceType/docs" target="_blank">Azure Portal rejestracje aplikacji <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
> 1. Wprowadź nazwę aplikacji, a następnie wybierz pozycję **zarejestruj**.
> 1. Postępuj zgodnie z instrukcjami w samouczku szybki start dla portalu, aby pobrać automatycznie skonfigurowany kod aplikacji.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Opcja 2. Zarejestrowanie i ręczne skonfigurowanie aplikacji oraz przykładowego kodu
>
> #### <a name="step-1-register-your-application"></a>Krok 1. Rejestrowanie aplikacji
>
> Aby zarejestrować aplikację i ręcznie dodać do niej informacje o rejestracji aplikacji, wykonaj następujące kroki:
>
> 1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
> 1. Jeśli masz dostęp do wielu dzierżawców, Użyj filtru **katalogów i subskrypcji** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: w górnym menu, aby wybrać dzierżawcę, w którym chcesz zarejestrować aplikację.
> 1. Wyszukaj i wybierz pozycję **Azure Active Directory**.
> 1. W obszarze **Zarządzanie** wybierz pozycję **Rejestracje aplikacji**.
> 1. Wybierz pozycję **Nowa rejestracja**.
> 1. Wprowadź **nazwę** aplikacji, na przykład **Java-webapp**. Użytkownicy Twojej aplikacji mogą zobaczyć tę nazwę. Później można ją zmienić.
> 1. Wybierz pozycję **Zarejestruj**.
> 1. Na stronie **Przegląd** Zanotuj **Identyfikator aplikacji (klienta)** i **Identyfikator katalogu (dzierżawy)**. Te wartości będą potrzebne później.
> 1. W obszarze **Zarządzaj** wybierz pozycję **uwierzytelnianie**.
> 1. Wybierz pozycję **Dodaj platformę**  >  **sieci Web**.
> 1. W sekcji **identyfikatory URI przekierowania** wprowadź wartość `https://localhost:8443/msal4jsample/secure/aad` .
> 1. Wybierz pozycję **Konfiguruj**.
> 1. W sekcji **sieci Web** w obszarze **identyfikatory URI przekierowania** wprowadź `https://localhost:8443/msal4jsample/graph/me` jako drugi identyfikator URI przekierowania.
> 1. W obszarze **Zarządzanie** wybierz pozycję **Certyfikaty i wpisy tajne**. W sekcji wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta**.
> 1. Wprowadź opis klucza (na przykład *wpis tajny aplikacji*), Pozostaw domyślne wygaśnięcie, a następnie wybierz pozycję **Dodaj**.
> 1. Zanotuj **wartość** klucza tajnego klienta. Będzie on potrzebny później.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Krok 1. Konfigurowanie aplikacji w witrynie Azure Portal
>
> Aby użyć przykładu kodu w tym przewodniku Szybki Start, należy wykonać następujące:
>
> 1. Dodaj adresy URL odpowiedzi `https://localhost:8443/msal4jsample/secure/aad` i `https://localhost:8443/msal4jsample/graph/me` .
> 1. Utwórz klucz tajny klienta.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Wprowadź zmiany automatycznie]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Już skonfigurowano](media/quickstart-v2-aspnet-webapp/green-check.png) Twoja aplikacja została skonfigurowana za pomocą tych atrybutów.

#### <a name="step-2-download-the-code-sample"></a>Krok 2. Pobieranie przykładu kodu
> [!div renderon="docs"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> Pobierz projekt i wyodrębnij plik. zip do folderu w folderze głównym dysku. Na przykład *C:\Azure-Samples*.
>
> Aby używać protokołu HTTPS z localhost, podaj `server.ssl.key` właściwości. Aby wygenerować certyfikat z podpisem własnym, użyj narzędzia klucza (dołączonego do środowiska JRE).
>
> Oto przykład:
>  ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   Umieść wygenerowany plik magazynu kluczy w folderze *resources* .

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Pobierz przykład kodu](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-code-sample"></a>Krok 3. Konfigurowanie przykładu kodu
> 1. Wyodrębnij plik zip do folderu lokalnego.
> 1. *Obowiązkowe.* Jeśli używasz zintegrowanego środowiska programistycznego, Otwórz przykład w tym środowisku.
> 1. Otwórz plik *Application. Properties* . Można go znaleźć w folderze *src/Main/sources/* folder. Zastąp wartości w polach `aad.clientId` , `aad.authority` oraz `aad.secretKey` odpowiednio identyfikatorem aplikacji, identyfikatorem dzierżawy i wartością klucza tajnego klienta. Oto, co powinno wyglądać następująco:
>
>    ```file
>    aad.clientId=Enter_the_Application_Id_here
>    aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
>    aad.secretKey=Enter_the_Client_Secret_Here
>    aad.redirectUriSignin=https://localhost:8443/msal4jsample/secure/aad
>    aad.redirectUriGraph=https://localhost:8443/msal4jsample/graph/me
>    aad.msGraphEndpointHost="https://graph.microsoft.com/"
>    ```
>    W poprzednim kodzie:
>
>    - `Enter_the_Application_Id_here` to identyfikator aplikacji dla zarejestrowanej aplikacji.
>    - `Enter_the_Client_Secret_Here` to **wpis tajny klienta** utworzony w **certyfikatach & wpisy tajne** dla zarejestrowanej aplikacji.
>    - `Enter_the_Tenant_Info_Here` to wartość **identyfikatora katalogu (dzierżawy)** zarejestrowanej aplikacji.
> 1. Aby używać protokołu HTTPS z localhost, podaj `server.ssl.key` właściwości. Aby wygenerować certyfikat z podpisem własnym, użyj narzędzia klucza (dołączonego do środowiska JRE).
>
>    Oto przykład:
>
>     ```
>      keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
>
>      server.ssl.key-store-type=PKCS12
>      server.ssl.key-store=classpath:keystore.p12
>      server.ssl.key-store-password=password
>      server.ssl.key-alias=testCert
>      ```
>   1. Umieść wygenerowany plik magazynu kluczy w folderze *resources* .


> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-run-the-code-sample"></a>Krok 3. Uruchamianie przykładu kodu
> [!div renderon="docs"]
> #### <a name="step-4-run-the-code-sample"></a>Krok 4. Uruchamianie przykładu kodu

Aby uruchomić projekt, wykonaj jedną z następujących czynności:

- Uruchom ją bezpośrednio z poziomu środowiska IDE przy użyciu osadzonego serwera rozruchowego sprężyny. 
- Spakuj go do pliku WAR przy użyciu [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html), a następnie wdróż go w rozwiązaniu kontenera J2EE, takim jak [Apache Tomcat](http://tomcat.apache.org/).

##### <a name="running-the-project-from-an-ide"></a>Uruchamianie projektu z poziomu środowiska IDE

Aby uruchomić aplikację sieci Web z poziomu środowiska IDE, wybierz pozycję Uruchom, a następnie przejdź do strony głównej projektu. W tym przykładzie adres URL standardowej strony głównej to https://localhost:8443 .

1. Na stronie frontonu wybierz przycisk **Zaloguj** , aby przekierować użytkowników do Azure Active Directory i monitować o poświadczenia.

1. Po uwierzytelnieniu użytkownicy są przekierowywani do programu `https://localhost:8443/msal4jsample/secure/aad` . Są one teraz zalogowane, a na stronie zostaną wyświetlone informacje o koncie użytkownika. Przykładowy interfejs użytkownika zawiera następujące przyciski:
    - **Wyloguj**: podpisuje bieżącego użytkownika poza aplikacją i przekierowuje tego użytkownika do strony głównej.
    - **Pokaż informacje o użytkowniku**: uzyskuje token dla Microsoft Graph i wywołuje Microsoft Graph z żądaniem zawierającym token, który zwraca podstawowe informacje o zalogowanym użytkowniku.

##### <a name="running-the-project-from-tomcat"></a>Uruchamianie projektu z Tomcat

Jeśli chcesz wdrożyć próbkę sieci Web w Tomcat, musisz wprowadzić kilka zmian w kodzie źródłowym.

1. Otwórz *MS-Identity-Java-webapp/pom.xml*.
    - W obszarze `<name>msal-web-sample</name>` , Dodaj `<packaging>war</packaging>` .

2. Otwórz *MS-Identity-Java-webapp/src/Main/Java/com. Microsoft. Azure. msalwebsample/MsalWebSampleApplication*.

    - Usuń cały kod źródłowy i zastąp go tym kodem:

      ```Java
       package com.microsoft.azure.msalwebsample;

       import org.springframework.boot.SpringApplication;
       import org.springframework.boot.autoconfigure.SpringBootApplication;
       import org.springframework.boot.builder.SpringApplicationBuilder;
       import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

       @SpringBootApplication
       public class MsalWebSampleApplication extends SpringBootServletInitializer {

        public static void main(String[] args) {
         SpringApplication.run(MsalWebSampleApplication.class, args);
        }

        @Override
        protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
         return builder.sources(MsalWebSampleApplication.class);
        }
       }
      ```

3.   Domyślnym portem HTTP Tomcat jest 8080, ale wymagane jest połączenie HTTPS przez port 8443. Aby skonfigurować to ustawienie:
        - Przejdź do *Tomcat/conf/server.xml*.
        - Wyszukaj `<connector>` tag i Zastąp istniejący łącznik tym łącznikiem:

          ```xml
          <Connector
                   protocol="org.apache.coyote.http11.Http11NioProtocol"
                   port="8443" maxThreads="200"
                   scheme="https" secure="true" SSLEnabled="true"
                   keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
                   clientAuth="false" sslProtocol="TLS"/>
          ```

4. Otwórz okno wiersza polecenia. Przejdź do folderu głównego tego przykładu (w którym znajduje się plik pom.xml), a następnie uruchom polecenie, `mvn package` Aby skompilować projekt.
    - To polecenie spowoduje wygenerowanie pliku *msal-Web-Sample-0.1.0. War* w katalogu */targets* .
    - Zmień nazwę tego pliku na *msal4jsample. War*.
    - Wdróż plik WAR przy użyciu Tomcat lub dowolnego innego rozwiązania kontenera J2EE.
        - Aby wdrożyć plik msal4jsample. War, skopiuj go do katalogu */webapps/* w instalacji programu Tomcat, a następnie uruchom serwer Tomcat.

5. Po wdrożeniu pliku przejdź do programu przy https://localhost:8443/msal4jsample użyciu przeglądarki.


> [!IMPORTANT]
> Ta aplikacja szybkiego startu używa klucza tajnego klienta, aby zidentyfikować siebie jako klienta poufnego. Ponieważ klucz tajny klienta jest dodawany jako zwykły tekst do plików projektu, ze względów bezpieczeństwa zaleca się użycie certyfikatu zamiast wpisu tajnego klienta przed użyciem aplikacji w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat korzystania z certyfikatu, zobacz [poświadczenia certyfikatu na potrzeby uwierzytelniania aplikacji](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Więcej informacji

### <a name="how-the-sample-works"></a>Jak działa przykład
![Diagram przedstawiający sposób działania przykładowej aplikacji wygenerowanej przez ten przewodnik Szybki Start.](media/quickstart-v2-java-webapp/java-quickstart.svg)

### <a name="get-msal"></a>Pobierz MSAL

MSAL for Java (MSAL4J) to biblioteka języka Java służąca do logowania użytkowników i żądania tokenów, które są używane w celu uzyskania dostępu do interfejsu API chronionego przez platformę tożsamości firmy Microsoft.

Dodaj MSAL4J do aplikacji za pomocą Maven lub Gradle, aby zarządzać zależnościami, wprowadzając następujące zmiany w pliku pom.xml (Maven) lub kompilacji. Gradle (Gradle) aplikacji.

W pom.xml:

```XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>msal4j</artifactId>
    <version>1.0.0</version>
</dependency>
```

W Build. Gradle:

```$xslt
compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
```

### <a name="initialize-msal"></a>Zainicjuj MSAL

Dodaj odwołanie do MSAL for Java, dodając następujący kod na początku pliku, w którym będziesz używać MSAL4J:

```Java
import com.microsoft.aad.msal4j.*;
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Następne kroki

Dokładniejsze omówienie tworzenia aplikacji sieci Web, które logują się do użytkowników na platformie tożsamości firmy Microsoft, można znaleźć w wieloczęściowej serii scenariuszy:

> [!div class="nextstepaction"]
> [Scenariusz: aplikacja sieci Web, która loguje użytkowników](scenario-web-app-sign-user-overview.md?tabs=java)
