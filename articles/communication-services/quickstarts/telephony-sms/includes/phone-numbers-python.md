---
ms.openlocfilehash: 283d7d1c83dc4a68901c17c36b548e00e1044e34
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105629401"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 lub nowszy.
- Wdrożony zasób usług komunikacyjnych i parametry połączenia. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Otwórz terminal lub okno poleceń i Utwórz nowy katalog dla aplikacji, a następnie przejdź do niego.

```console
mkdir phone-numbers-quickstart && cd phone-numbers-quickstart
```

Za pomocą edytora tekstów Utwórz plik o nazwie phone_numbers_sample. PR w katalogu głównym projektu i Dodaj następujący kod. Będziemy dodawać pozostałe kod szybkiego startu w poniższych sekcjach.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

try:
   print('Azure Communication Services - Phone Numbers Quickstart')
   # Quickstart code goes here
except Exception as ex:
   print('Exception:')
   print(ex)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę kliencką Administracja usług Azure Communication Services dla pakietu Python za pomocą `pip install` polecenia.

```console
pip install azure-communication-phone-numbers
```

## <a name="authenticate-the-phone-numbers-client"></a>Uwierzytelnianie klienta numerów telefonów

`PhoneNumbersClient`Umożliwia korzystanie z uwierzytelniania Azure Active Directory. Korzystanie z `DefaultAzureCredential` obiektu jest najprostszym sposobem na rozpoczęcie pracy z Azure Active Directory i można go zainstalować przy użyciu `pip install` polecenia. 

```console
pip install azure-identity
```

Utworzenie `DefaultAzureCredential` obiektu wymaga `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` ,, i jest `AZURE_TENANT_ID` już ustawiony jako zmienne środowiskowe z odpowiednimi wartościami z zarejestrowanej aplikacji usługi Azure AD.

Po zainstalowaniu `azure-identity` biblioteki można kontynuować uwierzytelnianie klienta.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
except Exception as ex:
    print('Exception:')
    print(ex)
```

Alternatywnie przy użyciu punktu końcowego i klucza dostępu z zasobu komunikacyjnego do uwierzytelniania jest również przekroczyła dozwoloną.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient

# You can find your connection string from your resource in the Azure Portal
connection_string = 'https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<YOUR_ACCESS_KEY>'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    phone_numbers_client = PhoneNumbersClient.from_connection_string(connection_string)
except Exception as ex:
    print('Exception:')
    print(ex)
```

## <a name="functions"></a>Funkcje

Po `PhoneNumbersClient` uwierzytelnieniu można rozpocząć pracę nad różnymi funkcjami, które może to zrobić.

### <a name="search-for-available-phone-numbers"></a>Wyszukaj dostępne numery telefonów

Aby kupić numery telefonów, należy najpierw wyszukać dowolne dostępne numery telefonów. Aby wyszukać numery telefonów, podaj numer kierunkowy, typ przypisania, [możliwości numeru](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)telefonu, [typ numeru telefonu](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)i ilość (wartość domyślna to 1). Należy pamiętać, że dla bezpłatnego typu numeru telefonu, dostarczając kod obszaru jest opcjonalny.

```python
import os
from azure.communication.phonenumbers import PhoneNumbersClient, PhoneNumberCapabilityType, PhoneNumberAssignmentType, PhoneNumberType, PhoneNumberCapabilities
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = self.phone_number_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        polling = True
    )
    search_result = search_poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="purchase-phone-numbers"></a>Zakupy numerów telefonów

Wynikiem wyszukiwania numerów telefonów jest `PhoneNumberSearchResult` . Zawiera to, `searchId` które można przesłać do interfejsu API numerów zakupów w celu uzyskania liczb w wyszukiwaniu. Należy pamiętać, że wywołanie interfejsu API numerów telefonów zakupów spowoduje naliczenie opłaty za Twoje konto platformy Azure.

```python
import os
from azure.communication.phonenumbers import (
    PhoneNumbersClient, 
    PhoneNumberCapabilityType, 
    PhoneNumberAssignmentType, 
    PhoneNumberType, 
    PhoneNumberCapabilities
)
from azure.identity import DefaultAzureCredential

# You can find your endpoint from your resource in the Azure Portal
endpoint = 'https://<RESOURCE_NAME>.communication.azure.com'
try:
    print('Azure Communication Services - Phone Numbers Quickstart')
    credential = DefaultAzureCredential()
    phone_numbers_client = PhoneNumbersClient(endpoint, credential)
    capabilities = PhoneNumberCapabilities(
        calling = PhoneNumberCapabilityType.INBOUND,
        sms = PhoneNumberCapabilityType.INBOUND_OUTBOUND
    )
    search_poller = phone_numbers_client.begin_search_available_phone_numbers(
        "US",
        PhoneNumberType.TOLL_FREE,
        PhoneNumberAssignmentType.APPLICATION,
        capabilities,
        area_code="833",
        polling = True
    )
    search_result = poller.result()
    print ('Search id: ' + search_result.search_id)
    phone_number_list = search_result.phone_numbers
    print('Reserved phone numbers:')
    for phone_number in phone_number_list:
        print(phone_number)

    purchase_poller = phone_numbers_client.begin_purchase_phone_numbers(search_result.search_id, polling = True)
    purchase_poller.result()
    print("The status of the purchase operation was: " + purchase_poller.status())
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="get-purchased-phone-numbers"></a>Pobierz zakupione numery telefonów

Po wybraniu numeru zakupu można pobrać go z klienta programu. 

```python
purchased_phone_number_information = phone_numbers_client.get_purchased_phone_number("+18001234567")
print('Phone number: ' + purchased_phone_number_information.phone_number)
print('Country code: ' + purchased_phone_number_information.country_code)
```

Możesz również pobrać wszystkie zakupione numery telefonów.

```python
purchased_phone_numbers = phone_numbers_client.list_purchased_phone_numbers()
print('Purchased phone numbers:')
for purchased_phone_number in purchased_phone_numbers:
    print(purchased_phone_number.phone_number)
```

### <a name="update-phone-number-capabilities"></a>Aktualizowanie możliwości numeru telefonu

Możesz zaktualizować możliwości wcześniej zakupionego numeru telefonu.
```python
update_poller = phone_numbers_client.begin_update_phone_number_capabilities(
    "+18001234567",
    PhoneNumberCapabilityType.OUTBOUND,
    PhoneNumberCapabilityType.OUTBOUND,
    polling = True
)
update_poller.result()
print('Status of the operation: ' + update_poller.status())
```

### <a name="release-phone-number"></a>Numer telefonu wydania

Możesz zwolnić zakupiony numer telefonu.

```python
release_poller = phone_numbers_client.begin_release_phone_number("+18001234567")
release_poller.result()
print('Status of the operation: ' + release_poller.status())
```

## <a name="run-the-code"></a>Uruchamianie kodu

W wierszu konsoli przejdź do katalogu zawierającego plik phone_numbers_sample. PR, a następnie wykonaj poniższe polecenie języka Python, aby uruchomić aplikację.

```console
./phone_numbers_sample.py
```
