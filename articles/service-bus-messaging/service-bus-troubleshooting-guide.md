---
title: Przewodnik rozwiązywania problemów dla Azure Service Bus | Microsoft Docs
description: Poznaj wskazówki dotyczące rozwiązywania problemów i zalecenia dotyczące kilku problemów, które mogą wystąpić podczas korzystania z Azure Service Bus.
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: c5813f901f31f96633e11c2ed8162338bdd9bbf7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98881218"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Przewodnik rozwiązywania problemów Azure Service Bus
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów i zalecenia dotyczące kilku problemów, które mogą wystąpić podczas korzystania z Azure Service Bus. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/przekroczeniem limitu czasu dla wszystkich usług w programie *. servicebus.windows.net. 

- Przejdź do lub [Wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów, które są powszechne w przypadku korzystania z zestawu Java SDK.

    Przykład pomyślnego komunikatu:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykładowy komunikat o błędzie błędu:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy dowolny port jest zablokowany przez zaporę. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9354 (NET Messaging/SBMP). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Możesz pobrać `psping` Narzędzie z tego [miejsca](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc` , `ping` , i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemy, które mogą wystąpić w przypadku uaktualnień/ponownych uruchomień usług

### <a name="symptoms"></a>Objawy
- Żądania mogą mieć na chwilę ograniczenie przepustowości.
- Może wystąpić spadek przychodzących komunikatów/żądań.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą być odłączone od usługi przez kilka sekund.

### <a name="cause"></a>Przyczyna
Uaktualnienia i ponowne uruchomienia usługi zaplecza mogą spowodować te problemy w aplikacjach.

### <a name="resolution"></a>Rozwiązanie
Jeśli kod aplikacji używa zestawu SDK, zasady ponawiania są już wbudowane i aktywne. Aplikacja będzie się łączyć ponownie bez znaczącego wpływu na aplikację/przepływ pracy.

## <a name="unauthorized-access-send-claims-are-required"></a>Nieautoryzowany dostęp: wymagane jest wysłanie oświadczeń

### <a name="symptoms"></a>Objawy 
Ten błąd może wystąpić podczas próby uzyskania dostępu do Service Bus tematu z programu Visual Studio na komputerze lokalnym przy użyciu tożsamości zarządzanej przypisanej przez użytkownika z uprawnieniami do wysyłania.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Przyczyna
Tożsamość nie ma uprawnień dostępu do tematu Service Bus. 

### <a name="resolution"></a>Rozwiązanie
Aby rozwiązać ten problem, zainstaluj bibliotekę [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) .  Aby uzyskać więcej informacji, zobacz [lokalne uwierzytelnianie deweloperskie](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication). 

Aby dowiedzieć się, jak przypisać uprawnienia do ról, zobacz temat [uwierzytelnianie tożsamości zarządzanej przy użyciu Azure Active Directory w celu uzyskania dostępu do zasobów Azure Service Bus](service-bus-managed-service-identity.md).

## <a name="service-bus-exception-put-token-failed"></a>Wyjątek Service Bus: nie można umieścić tokenu

### <a name="symptoms"></a>Objawy
Podczas próby wysłania więcej niż 1000 komunikatów przy użyciu tego samego połączenia Service Bus zostanie wyświetlony następujący komunikat o błędzie: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Przyczyna
Istnieje ograniczenie liczby tokenów używanych do wysyłania i odbierania wiadomości przy użyciu jednego połączenia z przestrzenią nazw Service Bus. Jest to 1000. 

### <a name="resolution"></a>Rozwiązanie
Otwórz nowe połączenie z przestrzenią nazw Service Bus, aby wysłać więcej komunikatów.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 

- [Wyjątki Azure Resource Manager](service-bus-resource-manager-exceptions.md). Lista wyjątków generowanych podczas współpracy z Azure Service Bus przy użyciu Azure Resource Manager (za pośrednictwem szablonów lub wywołań bezpośrednich).
- [Wyjątki komunikatów](service-bus-messaging-exceptions.md). Zawiera listę wyjątków wygenerowanych przez .NET Framework dla Azure Service Bus.