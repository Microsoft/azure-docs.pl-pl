---
title: plik dołączania
description: plik dołączania
services: azure-communication-services
author: danieldoolabh
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 09/03/2020
ms.topic: include
ms.custom: include file
ms.author: dadoolab
ms.openlocfilehash: 9265caa3054cde6af311e655db2b2496524e8d24
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757164"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu biblioteki klienta programu SMS usługi komunikacyjnej Python w celu wysyłania wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2,7, 3,5 lub nowszy.
- Zasób i parametry połączenia aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Numer telefonu z włączoną funkcją SMS. [Pobierz numer telefonu](../get-phone-number.md).

### <a name="prerequisite-check"></a>Sprawdzanie wymagań wstępnych

- W terminalu lub oknie poleceń Uruchom `python --version` polecenie, aby sprawdzić, czy środowisko Python jest zainstalowane.
- Aby wyświetlić numery telefonów skojarzone z zasobem usług komunikacyjnych, zaloguj się do [Azure Portal](https://portal.azure.com/), Znajdź zasób usługi komunikacyjnej i Otwórz kartę **numery telefonów** w okienku nawigacji po lewej stronie.

## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Otwórz terminal lub okno poleceń, Utwórz nowy katalog dla aplikacji i przejdź do niego.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Użyj edytora tekstów, aby utworzyć plik o nazwie **send-SMS.py** w katalogu głównym projektu i dodać strukturę programu, w tym podstawową obsługę wyjątków. Do tego pliku zostanie dodany kod źródłowy dla tego przewodnika Szybki Start w poniższych sekcjach.

```python
import os
from azure.communication.sms import PhoneNumber
from azure.communication.sms import SendSmsOptions
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, zainstaluj bibliotekę klienta programu SMS dla usług Azure Communication Services dla pakietu Python za pomocą `pip install` polecenia.

```console
pip install azure-communication-sms
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta programu SMS usługi Azure Communication Services dla języka Python.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wysyłania wiadomości SMS. |
| SendSmsOptions | Ta klasa zawiera opcje konfigurowania raportowania dostarczania. Jeśli enable_delivery_report ma wartość true, zdarzenie będzie emitowane po pomyślnym dostarczeniu |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie elementu **SmsClient** z parametrami połączenia. Poniższy kod pobiera parametry połączenia dla zasobu ze zmiennej środowiskowej o nazwie `COMMUNICATION_SERVICES_CONNECTION_STRING` . Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-an-sms-message"></a>Wysyłanie wiadomości SMS

Wyślij wiadomość SMS przez wywołanie metody send. Dodaj ten kod na końcu `try` bloku w **send-SMS.py**:

```python

# calling send() with sms values
sms_response = sms_client.send(
        from_phone_number=PhoneNumber("<leased-phone-number>"),
        to_phone_numbers=[PhoneNumber("<to-phone-number>")],
        message="Hello World via SMS",
        send_sms_options=SendSmsOptions(enable_delivery_report=True)) # optional property

```

Należy zastąpić `<leased-phone-number>` numerem telefonu z włączoną obsługą programu SMS skojarzonym z usługą komunikacyjną i `<to-phone-number>` numerem telefonu, na który chcesz wysłać wiadomość. 

`send_sms_options`Parametr jest opcjonalnym parametrem, którego można użyć w celu skonfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.

## <a name="run-the-code"></a>Uruchamianie kodu

Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia.

```console
python send-sms.py
```
