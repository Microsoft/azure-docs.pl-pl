---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: aba9b71ec2fbfedecf08577c7bd2eae7a28a5588
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644304"
---
Rozpocznij pracę z usługami Azure Communications Services przy użyciu zestawu SDK programu SMS usługi Communication Services w celu wysyłania wiadomości SMS.

W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2,7 lub 3.6 +.
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
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

Mimo że w katalogu aplikacji, Zainstaluj zestaw SMS SDK usługi Azure Communication Services dla języka Python za pomocą `pip install` polecenia.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SMS SDK usługi Azure Communication Services dla języka Python.

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Ta klasa jest wymagana dla wszystkich funkcji programu SMS. Utwórz wystąpienie go przy użyciu informacji o subskrypcji i użyj go do wysyłania wiadomości SMS.                                                                                                                 |
| SmsSendResult               | Ta klasa zawiera wynik z usługi SMS.                                          |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Utwórz wystąpienie elementu **SmsClient** z parametrami połączenia. Dowiedz się, jak [zarządzać parametrami połączenia zasobu](../../create-communication-resource.md#store-your-connection-string).

```python
# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(<connection_string>)
```
Dla uproszczenia korzystamy z parametrów połączenia w tym przewodniku Szybki Start, ale w środowiskach produkcyjnych zalecamy korzystanie z [zarządzanych tożsamości](../../../quickstarts/managed-identity.md) , ponieważ są one bezpieczniejsze i zarządzane na dużą skalę.


## <a name="send-a-11-sms-message"></a>Wyślij wiadomość SMS z systemem 1:1

Aby wysłać wiadomość SMS do pojedynczego adresata, wywołaj ```send``` metodę z **SmsClient** za pomocą jednego numeru telefonu odbiorcy. Możesz również przekazać parametry opcjonalne, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe. Dodaj ten kod na końcu `try` bloku w **send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>",
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Należy zastąpić `<from-phone-number>` numerem telefonu z włączoną obsługą programu SMS skojarzonym z usługą komunikacyjną i `<to-phone-number>` numerem telefonu, na który chcesz wysłać wiadomość. 

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

## <a name="send-a-1n-sms-message"></a>Wyślij wiadomość SMS z 1: N

Aby wysłać wiadomość SMS do listy adresatów, wywołaj ```send``` metodę z **SmsClient** z listą numerów telefonów adresatów. Możesz również przekazać parametry opcjonalne, aby określić, czy raport dostarczania powinien być włączony, i ustawić Tagi niestandardowe. Dodaj ten kod na końcu `try` bloku w **send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Należy zastąpić `<from-phone-number>` numerem telefonu z włączoną obsługą programu SMS skojarzonym z usługą komunikacji oraz `<to-phone-number-1>` `<to-phone-number-2>` z numerami telefonów, do których chcesz wysłać wiadomość. 

> [!WARNING]
> Należy zauważyć, że numery telefonów powinny być podane w standardowym formacie E. 164. (np.: + 14255550123).

## <a name="optional-parameters"></a>Parametry opcjonalne

`enable_delivery_report`Parametr jest opcjonalnym parametrem, którego można użyć w celu skonfigurowania raportowania dostarczania. Jest to przydatne w scenariuszach, w których chcesz emitować zdarzenia podczas dostarczania wiadomości SMS. Zapoznaj się z tematem [Obsługa zdarzeń programu SMS](../handle-sms-events.md) — Szybki Start, aby skonfigurować dostarczanie raportów dla wiadomości SMS.

`tag`Parametr jest opcjonalnym parametrem, za pomocą którego można zastosować tag do raportu dostarczania.

## <a name="run-the-code"></a>Uruchamianie kodu
Uruchom aplikację z katalogu aplikacji za pomocą `python` polecenia.

```console
python send-sms.py
```

Kompletny skrypt języka Python powinien wyglądać następująco:

```python

import os
from azure.communication.sms import SmsClient

try:
    # Create the SmsClient object which will be used to send SMS messages
    sms_client = SmsClient.from_connection_string("<connection string>")
    # calling send() with sms values
    sms_responses = sms_client.send(
       from_="<from-phone-number>",
       to="<to-phone-number>",
       message="Hello World via SMS",
       enable_delivery_report=True, # optional property
       tag="custom-tag") # optional property

except Exception as ex:
    print('Exception:')
    print(ex)
```
