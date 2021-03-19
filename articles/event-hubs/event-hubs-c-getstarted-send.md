---
title: 'Szybki Start: wysyłanie zdarzeń przy użyciu języka C — Event Hubs platformy Azure'
description: 'Szybki Start: Ten artykuł zawiera Przewodnik dotyczący tworzenia aplikacji C, która wysyła zdarzenia do usługi Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: bfe1ca1a45f7b33d7431aed13446d8d72f79fb90
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85315661"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Szybki Start: wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu języka C

## <a name="introduction"></a>Wprowadzenie
Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. Aby zapoznać się ze szczegółowym omówieniem usługi Event Hubs, zobacz [Omówienie usługi Event Hubs](event-hubs-about.md) i [Funkcje usługi Event Hubs](event-hubs-features.md).

W tym samouczku opisano sposób wysyłania zdarzeń do centrum zdarzeń przy użyciu aplikacji konsolowej w języku C. 

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tego samouczka są potrzebne są następujące elementy:

* Środowisko deweloperskie języka C. W tym samouczku przyjęto założenie, że stosy w witrynie Microsoft w zatoce na maszynie wirtualnej 14,04 z systemem Linux Azure
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Utwórz przestrzeń nazw Event Hubs i centrum zdarzeń**. Użyj [Azure Portal](https://portal.azure.com) , aby utworzyć przestrzeń nazw typu Event Hubs i uzyskać poświadczenia zarządzania wymagane przez aplikację do komunikacji z centrum zdarzeń. Aby utworzyć przestrzeń nazw i centrum zdarzeń, wykonaj procedurę opisaną w [tym artykule](event-hubs-create.md). Pobierz wartość klucza dostępu do centrum zdarzeń, wykonując instrukcje podane w artykule: [pobieranie parametrów połączenia](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Używasz klucza dostępu w kodzie zapisanym w dalszej części tego samouczka. Domyślna nazwa klucza to: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Napisz kod, aby wysyłać komunikaty do Event Hubs
W tej sekcji pokazano, jak napisać aplikację C do wysyłania zdarzeń do centrum zdarzeń. Kod używa biblioteki Proton AMQP z [projektu Apache Qpid](https://qpid.apache.org/). Jest to analogiczne do korzystania z Service Busch kolejek i tematów z AMQP języka C, jak pokazano [w tym przykładzie](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Aby uzyskać więcej informacji, zobacz [dokumentację programu Qpid Proton](https://qpid.apache.org/proton/index.html).

1. Na [stronie QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html)postępuj zgodnie z instrukcjami, aby zainstalować Qpid Proton, w zależności od środowiska.
2. Aby skompilować bibliotekę Proton, Zainstaluj następujące pakiety:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. Pobierz [bibliotekę Qpid Proton](https://qpid.apache.org/proton/index.html)i Wyodrębnij ją, np.:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Utwórz katalog kompilacji, skompiluj i zainstaluj:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. W katalogu służbowym Utwórz nowy plik o nazwie **Sender. c** z poniższym kodem. Pamiętaj, aby zastąpić wartości klucza SAS/nazwy, nazwy centrum zdarzeń i przestrzeni nazw. Należy również zastąpić zakodowaną w adresie URL wersję klucza dla **SendRule** utworzonego wcześniej. Możesz to zrobić na adres URL [.](https://www.w3schools.com/tags/ref_urlencode.asp)
   
    ```c
    #include "proton/message.h"
    #include "proton/messenger.h"
   
    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>
   
    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }
   
    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  
   
    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }
   
    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";
   
        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();
   
        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);
   
        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));
   
        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);
   
        pn_message_free(message);
    }
   
    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");
   
        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);
   
        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }
   
        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);
   
        return 0;
    }
    ```
6. Skompiluj plik **, przy** założeniu, że:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Ten kod używa wychodzącego okna 1, aby wymusić wymuszanie komunikatów tak szybko, jak to możliwe. Zaleca się, aby aplikacja próbowała wykonać wsadowe wiadomości w celu zwiększenia przepływności. Zapoznaj się ze [stroną QPID AMQP Messenger](https://qpid.apache.org/proton/messenger.html) , aby uzyskać informacje na temat używania biblioteki Proton Qpid w tym i innych środowiskach oraz z platform, dla których podano powiązania (obecnie Perl, php, Python i Ruby).

Uruchom aplikację, aby wysyłać komunikaty do centrum zdarzeń. 

Gratulacje! Wysłano komunikaty do centrum zdarzeń.

## <a name="next-steps"></a>Następne kroki
Przeczytaj następujące artykuły:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkcje i terminologia na platformie Azure Event Hubs](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
