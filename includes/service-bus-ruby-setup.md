---
author: spelluru
ms.service: service-bus
ms.topic: include
ms.date: 11/09/2018
ms.author: spelluru
ms.openlocfilehash: aec13c6beb8dbfcdd5f38e7f96b86bf03e42fa37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80986792"
---
## <a name="create-a-ruby-application"></a>Tworzenie aplikacji języka Ruby
Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji języka Ruby na platformie Azure](../articles/virtual-machines/linux/classic/ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Skonfiguruj aplikację do używania Service Bus
Aby użyć Service Bus, Pobierz pakiet Azure Ruby i użyj go, który obejmuje zestaw wygodnych bibliotek, które komunikują się z usługami REST magazynu.

### <a name="use-rubygems-to-obtain-the-package"></a>Używanie narzędzia RubyGems do pobierania pakietu
1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (system Windows), **Terminal** (system Mac) lub **Bash** (system Unix).
2. Wpisz "rozwiązania Gem Install Azure" w oknie polecenia, aby zainstalować rozwiązania Gem i zależności.

### <a name="import-the-package"></a>Importowanie pakietu
Za pomocą ulubionego edytora tekstu Dodaj następujące polecenie na początku pliku Ruby, w którym zamierzasz używać magazynu:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Skonfiguruj połączenie Service Bus
Użyj poniższego kodu, aby ustawić wartości przestrzeni nazw, nazwę klucza, klucza, osoby podpisującej i hosta:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Ustaw wartość przestrzeni nazw na utworzoną wartość, a nie cały adres URL. Na przykład użyj **"yourexamplenamespace"**, a nie "yourexamplenamespace.ServiceBus.Windows.NET".

Podczas pracy z wieloma przestrzeniami nazw można przekazać klucz i jego nazwę do konstruktora podczas tworzenia `SharedAccessSigner` obiektów

```ruby
sb_namespace = '<your azure service bus namespace>'
sb_sas_key_name = '<your azure service bus access keyname>'
sb_sas_key = '<your azure service bus access key>'

signer = Azure::ServiceBus::Auth::SharedAccessSigner.new(sb_sas_key_name, sb_sas_key)
sb_host = "https://#{sb_namespace}.servicebus.windows.net"
```
