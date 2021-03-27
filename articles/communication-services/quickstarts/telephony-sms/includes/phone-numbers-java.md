---
ms.openlocfilehash: 06033f13b1f63849408646835a844c2eafa8d64f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629416"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zestaw Java Development Kit (JDK)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) w wersji 8 lub nowszej.
- [Apache Maven](https://maven.apache.org/download.cgi).-wdrożony zasób usług komunikacyjnych i parametry połączenia. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-java-application"></a>Tworzenie nowej aplikacji Java

Otwórz terminal lub okno poleceń. Przejdź do katalogu, w którym chcesz utworzyć aplikację Java. Uruchom poniższe polecenie, aby wygenerować projekt Java z szablonu Maven-Archetype-szybkiego startu.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Zauważ, że zadanie "Generuj" utworzyło katalog o takiej samej nazwie jak nazwa `artifactId` . W tym katalogu Katalog Src/Main/Java zawiera kod źródłowy projektu, `src/test/java directory` zawiera źródło testu, a `pom.xml` plik jest modelem obiektu projektu projektu lub pliku pom.

### <a name="install-the-package"></a>Zainstaluj pakiet

Otwórz plik **pom.xml** w edytorze tekstów. Dodaj następujące elementy zależności do grupy zależności.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.6</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Z katalogu projektu:

1. Przejdź do katalogu */src/Main/Java/com/Communication/QuickStart*
1. Otwórz plik *App. Java* w edytorze
1. Zastąp `System.out.println("Hello world!");` instrukcję
1. Dodaj `import` dyrektywy

Użyj następującego kodu, aby rozpocząć:

```java
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.http.*;
import com.azure.core.http.netty.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Uwierzytelnianie klienta numerów telefonów

PhoneNumberClientBuilder jest włączona, aby używać uwierzytelniania Azure Active Directory
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .httpClient(httpClient)
    .buildClient();
```

Alternatywnie przy użyciu punktu końcowego i klucza dostępu z zasobu komunikacyjnego do uwierzytelniania jest również przekroczyła dozwoloną.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure Portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Zarządzanie numerami telefonów

### <a name="search-for-available-phone-numbers"></a>Wyszukaj dostępne numery telefonów

Aby kupić numery telefonów, należy najpierw wyszukać dostępne numery telefonów. Aby wyszukać numery telefonów, podaj numer kierunkowy, typ przypisania, [możliwości numeru](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)telefonu, [typ numeru telefonu](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)i ilość. Należy pamiętać, że dla bezpłatnego typu numeru telefonu, dostarczając kod obszaru jest opcjonalny.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Zakupy numerów telefonów

Wynikiem wyszukiwania numerów telefonów jest PhoneNumberSearchResult. Zawiera to, `searchId` które można przesłać do interfejsu API numerów zakupów w celu uzyskania liczb w wyszukiwaniu. Należy pamiętać, że wywołanie interfejsu API numerów telefonów zakupów spowoduje naliczenie opłaty za Twoje konto platformy Azure.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Uzyskaj numery telefonów

Po wybraniu numeru zakupu można pobrać go z klienta programu.
```java
AcquiredPhoneNumber phoneNumber = phoneNumberClient.getPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Możesz również pobrać wszystkie zakupione numery telefonów.
``` java
PagedIterable<AcquiredPhoneNumber> phoneNumbers = createPhoneNumberClient().listPhoneNumbers(Context.NONE);
AcquiredPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Aktualizowanie możliwości numeru telefonu

Za pomocą numeru zakupionego można zaktualizować możliwości.
```java
PhoneNumberCapabilitiesRequest capabilitiesRequest = new PhoneNumberCapabilitiesRequest();
capabilitiesRequest
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND);
AcquiredPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+18001234567", capabilitiesRequest, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling());
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms());
```

### <a name="release-phone-number"></a>Numer telefonu wydania

Możesz zwolnić zakupiony numer telefonu.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+18001234567", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
```

## <a name="run-the-code"></a>Uruchamianie kodu

Przejdź do katalogu zawierającego plik *pom.xml* i skompiluj projekt przy użyciu następującego `mvn` polecenia.

```console
mvn compile
```

Następnie Skompiluj pakiet.

```console
mvn package
```

Uruchom następujące `mvn` polecenie, aby uruchomić aplikację.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

W danych wyjściowych aplikacji opisano każdą akcję, która została ukończona:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->