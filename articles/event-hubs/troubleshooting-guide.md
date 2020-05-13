---
title: Przewodnik rozwiązywania problemów — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera listę wyjątków i sugerowanych akcji usługi Azure Event Hubs Messaging.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: ab3cbdf938409f4eacffa10ae5cb20f8c36b5856
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124673"
---
# <a name="azure-event-hubs---troubleshooting-guide"></a>Azure Event Hubs — Przewodnik rozwiązywania problemów
Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów i zalecenia dotyczące kilku problemów, które mogą wystąpić podczas korzystania z usługi Azure EventHubs.

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemy z łącznością, certyfikatem lub limitem czasu
Poniższe kroki mogą pomóc w rozwiązywaniu problemów z łącznością/certyfikatem/przekroczeniem limitu czasu dla wszystkich usług w programie *. servicebus.windows.net. 

- Przejdź do lub [Wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/` . Pomaga sprawdzić, czy masz problemy z filtrowaniem adresów IP lub siecią wirtualną lub łańcuchem certyfikatów (najbardziej typowe w przypadku korzystania z zestawu Java SDK).

    Przykład pomyślnego komunikatu:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Przykładowy komunikat o błędzie błędu:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Uruchom następujące polecenie, aby sprawdzić, czy dowolny port jest zablokowany przez zaporę. Używane porty to 443 (HTTPS), 5671 (AMQP) i 9093 (Kafka). W zależności od używanej biblioteki używane są również inne porty. Oto przykładowe polecenie, które sprawdza, czy port 5671 jest zablokowany.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    W systemie Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Gdy występują sporadyczne problemy z łącznością, uruchom następujące polecenie, aby sprawdzić, czy pakiety pozostaną usunięte. To polecenie próbuje nawiązać 25 różnych połączeń TCP co 1 sekundę z usługą. Następnie można sprawdzić, ile z nich zakończyło się powodzeniem/niepowodzeniem, i zobaczyć także opóźnienia połączeń TCP. Możesz pobrać `psping` Narzędzie z tego [miejsca](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Możesz użyć równoważnych poleceń, jeśli używasz innych narzędzi, takich jak `tnc` , `ping` , i tak dalej. 
- Uzyskaj ślad sieci, jeśli poprzednie kroki nie pomagają i nie analizują go przy użyciu narzędzi, takich jak [Wireshark](https://www.wireshark.org/). W razie konieczności skontaktuj się z [Pomoc techniczna firmy Microsoft](https://support.microsoft.com/) . 

## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemy, które mogą wystąpić w przypadku uaktualnień/ponownych uruchomień usług
Uaktualnienia i ponowne uruchomienia usługi zaplecza mogą spowodować następujący wpływ na aplikacje:

- Żądania mogą mieć na chwilę ograniczenie przepustowości.
- Może wystąpić spadek przychodzących komunikatów/żądań.
- Plik dziennika może zawierać komunikaty o błędach.
- Aplikacje mogą być odłączone od usługi przez kilka sekund.

Jeśli kod aplikacji korzysta z zestawu SDK, zasady ponawiania są już wbudowane i aktywne. Aplikacja będzie się łączyć ponownie bez znaczącego wpływu na aplikację/przepływ pracy.


## <a name="next-steps"></a>Następne kroki

Następujące linki pozwalają dowiedzieć się więcej na temat usługi Event Hubs:

* [Przegląd usługi Event Hubs](event-hubs-what-is-event-hubs.md)
* [Tworzenie centrum zdarzeń](event-hubs-create.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
