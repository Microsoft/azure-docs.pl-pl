---
title: Połącz z serwerem IBM MQ
description: Wysyłanie i pobieranie wiadomości za pomocą serwera platformy Azure lub lokalnego programu IBM MQ oraz Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, estfan, logicappspm
ms.topic: article
ms.date: 03/10/2021
tags: connectors
ms.openlocfilehash: a07eb6e592c68794f0e4038a7cf9a42bd396b47a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103495236"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Nawiązywanie połączenia z serwerem IBM MQ z poziomu usługi Azure Logic Apps

Łącznik MQ wysyła i pobiera komunikaty przechowywane na serwerze MQ lokalnie lub na platformie Azure. Ten łącznik zawiera klienta Microsoft MQ, który komunikuje się ze zdalnym serwerem IBM MQ w sieci TCP/IP. Ten artykuł zawiera Przewodnik początkowy dotyczący używania łącznika MQ. Możesz rozpocząć od przejrzenia pojedynczej wiadomości w kolejce, a następnie wypróbowania innych akcji.

Łącznik MQ zawiera te akcje, ale nie zapewnia żadnych wyzwalaczy:

- Przeglądaj pojedynczy komunikat bez usuwania komunikatu z serwera MQ.
- Przeglądaj partię komunikatów bez usuwania komunikatów z serwera MQ.
- Odbierz pojedynczy komunikat i Usuń komunikat z serwera MQ.
- Odbieraj partię komunikatów i usuwaj komunikaty z serwera MQ.
- Wyślij pojedynczy komunikat do serwera MQ.

Poniżej przedstawiono oficjalnie obsługiwane wersje oprogramowania IBM WebSphere MQ:

  * MQ 7,5
  * MQ 8,0
  * MQ 9,0
  * MQ 9,1

## <a name="prerequisites"></a>Wymagania wstępne

* W przypadku korzystania z serwera lokalnego MQ należy [zainstalować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md) na serwerze w sieci.

  > [!NOTE]
  > Jeśli serwer MQ jest publicznie dostępny lub dostępny na platformie Azure, nie musisz korzystać z bramy Data Gateway.

  * Aby łącznik MQ działał, na serwerze, na którym zainstalowano lokalną bramę danych, należy również zainstalować .NET Framework 4,6.
  
  * Po zainstalowaniu lokalnej bramy danych należy również [utworzyć zasób bramy platformy Azure dla lokalnej bramy danych](../logic-apps/logic-apps-gateway-connection.md) używanej przez łącznik MQ do uzyskiwania dostępu do lokalnego serwera MQ.

* Aplikacja logiki, w której ma być używany łącznik MQ. Łącznik MQ nie ma żadnych wyzwalaczy, więc musisz najpierw dodać wyzwalacz do aplikacji logiki. Można na przykład użyć [wyzwalacza cykl](../connectors/connectors-native-recurrence.md). Jeśli dopiero zaczynasz tworzyć aplikacje logiki, Wypróbuj ten [Przewodnik Szybki Start, aby utworzyć swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="limitations"></a>Ograniczenia

Łącznik MQ nie obsługuje ani nie używa pola **Format** komunikatu i nie wykonuje konwersji zestawu znaków. Łącznik umieszcza w komunikacie JSON tylko dane wyświetlane w polu komunikat, a następnie wysyła komunikat.

<a name="create-connection"></a>

## <a name="create-mq-connection"></a>Utwórz połączenie MQ

Jeśli nie masz jeszcze połączenia MQ po dodaniu akcji MQ, zostanie wyświetlony monit o utworzenie połączenia, na przykład:

![Podaj informacje o połączeniu](media/connectors-create-api-mq/connection-properties.png)

1. Jeśli łączysz się z lokalnym serwerem MQ, wybierz pozycję **Połącz za pośrednictwem lokalnej bramy danych**.

1. Podaj informacje o połączeniu dla serwera MQ.

   * W przypadku **serwera** wpisz nazwę serwera MQ lub wprowadź adres IP, po którym następuje dwukropek i numer portu.

   * Aby użyć Transport Layer Security (TLS) lub Secure Sockets Layer (SSL), wybierz pozycję **Włącz protokół SSL?**.

     Łącznik MQ obecnie obsługuje tylko uwierzytelnianie serwera, a nie uwierzytelnianie klienta. Aby uzyskać więcej informacji, zobacz [problemy z połączeniem i uwierzytelnianiem](#connection-problems).

1. W sekcji **brama** wykonaj następujące kroki:

   1. Z listy **subskrypcja** wybierz subskrypcję platformy Azure skojarzoną z zasobem bramy platformy Azure.

   1. Z listy **brama połączeń** wybierz zasób bramy platformy Azure, którego chcesz użyć.

1. Gdy wszystko będzie gotowe, wybierz przycisk **Utwórz**.

<a name="connection-problems"></a>

### <a name="connection-and-authentication-problems"></a>Problemy z połączeniem i uwierzytelnianiem

Gdy aplikacja logiki próbuje nawiązać połączenie z lokalnym serwerem MQ, może wystąpić następujący błąd:

`"MQ: Could not Connect the Queue Manager '<queue-manager-name>': The Server was expecting an SSL connection."`

* Jeśli używasz łącznika MQ bezpośrednio na platformie Azure, serwer MQ musi używać certyfikatu wydanego przez zaufany [urząd certyfikacji](https://www.ssl.com/faqs/what-is-a-certificate-authority/).

* Jeśli używasz lokalnej bramy danych, spróbuj użyć certyfikatu wydanego przez zaufany [urząd certyfikacji](https://www.ssl.com/faqs/what-is-a-certificate-authority/) , jeśli jest to możliwe. Jeśli jednak ta opcja nie jest możliwa, można użyć certyfikatu z podpisem własnym, który nie jest wystawiony przez zaufany [urząd certyfikacji](https://www.ssl.com/faqs/what-is-a-certificate-authority/) i jest uznawany za mniej bezpieczny.

  Aby zainstalować certyfikat z podpisem własnym serwera, można użyć narzędzia **Menedżer certyfikacji systemu Windows** (certmgr. msc). W tym scenariuszu na komputerze lokalnym, na którym jest uruchomiona usługa lokalnej bramy danych, należy zainstalować certyfikat w magazynie certyfikatów **komputera lokalnego** na poziomie **zaufanych głównych urzędów certyfikacji** .

  1. Na komputerze, na którym działa usługa bramy danych lokalnych, otwórz menu Start, Znajdź i wybierz pozycję **Zarządzaj certyfikatami użytkowników**.

  1. Po otwarciu narzędzia Menedżer certyfikacji systemu Windows przejdź do folderu **Certyfikaty —**  >   **Zaufane główne** urzędy certyfikacji komputera lokalnego i Zainstaluj certyfikat.

     > [!IMPORTANT]
     > Upewnij się, że certyfikat jest instalowany w   >  magazynie **zaufanych głównych** urzędów certyfikacji komputera lokalnego.

* Serwer MQ wymaga zdefiniowania specyfikacji szyfrowania, która ma być używana dla połączeń TLS/SSL. Jednak SslStream w programie .NET nie pozwala na określenie kolejności szyfrowania. Aby obejść to ograniczenie, można zmienić konfigurację serwera MQ w taki sposób, aby była zgodna z pierwszą specyfikacją szyfrowania w zestawie, który wysyła łącznik w negocjacji TLS/SSL.

  Podczas próby nawiązania połączenia serwer MQ rejestruje komunikat zdarzenia wskazujący, że połączenie nie powiodło się, ponieważ inne zakończenie użyto niepoprawnej specyfikacji szyfru. Komunikat zdarzenia zawiera specyfikację szyfrowania, która pojawia się na liście jako pierwsza. Zaktualizuj specyfikację szyfrowania w konfiguracji kanału, aby pasowała do specyfikacji szyfru w komunikacie o zdarzeniu.

## <a name="browse-single-message"></a>Przeglądaj pojedynczy komunikat

1. W aplikacji logiki pod wyzwalaczem lub inną akcją wybierz pozycję **nowy krok**.

1. W polu wyszukiwania wpisz `mq` , a następnie wybierz akcję **Przeglądaj wiadomość** .

   ![Wybierz akcję "Przeglądaj wiadomość"](media/connectors-create-api-mq/browse-message.png)

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection).

1. Po utworzeniu połączenia skonfiguruj właściwości akcji **Przeglądaj wiadomość** :

   | Właściwość | Opis |
   |----------|-------------|
   | **Kolejka** | Jeśli różni się od kolejki określonej w połączeniu, określ tę kolejkę. |
   | Właściwość **MessageID**, **Identyfikator korelacji**, **GroupID** i inne właściwości | Przeglądaj w poszukiwaniu komunikatów opartych na różnych właściwościach komunikatu MQ |
   | **IncludeInfo** | Aby uwzględnić dodatkowe informacje o komunikatach w danych wyjściowych, wybierz pozycję **prawda**. Aby pominąć dodatkowe informacje o komunikatach w danych wyjściowych, wybierz **wartość FAŁSZ**. |
   | **Limit czasu** | Wprowadź wartość, aby określić czas oczekiwania na nadejście komunikatu w pustej kolejce. Jeśli nic nie zostanie wprowadzone, zostanie pobrany pierwszy komunikat w kolejce i nie ma czasu oczekiwania na wyświetlenie komunikatu. |
   |||

   Na przykład:

   ![Właściwości akcji "Przeglądaj wiadomość"](media/connectors-create-api-mq/browse-message-properties.png)

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**. Aby przetestować aplikację, wybierz pozycję **Uruchom**.

   Po zakończeniu przebiegu Projektant pokazuje kroki przepływu pracy i ich stan, aby można było przejrzeć dane wyjściowe.

1. Aby wyświetlić szczegóły dotyczące każdego kroku, kliknij pasek tytułu kroku. Aby przejrzeć więcej informacji na temat danych wyjściowych kroku, wybierz pozycję **Pokaż nieprzetworzone dane wyjściowe**.

   ![Przeglądaj dane wyjściowe wiadomości](media/connectors-create-api-mq/browse-message-output.png)

   Oto przykładowe nieprzetworzone dane wyjściowe:

   ![Przeglądaj nieprzetworzone dane wyjściowe wiadomości](media/connectors-create-api-mq/browse-message-raw-output.png)

1. Jeśli ustawisz wartość **IncludeInfo** na **true**, wyświetlane są dodatkowe dane wyjściowe:

   ![Przeglądanie informacji dotyczących wiadomości](media/connectors-create-api-mq/browse-message-include-info.png)

## <a name="browse-multiple-messages"></a>Przeglądaj wiele komunikatów

Akcja **Przeglądaj wiadomości** zawiera opcję **BatchSize** , aby wskazać liczbę komunikatów do zwrócenia z kolejki. Jeśli **BatchSize** nie ma wartości, zwracane są wszystkie komunikaty. Zwrócone dane wyjściowe to tablica komunikatów.

1. Wykonaj poprzednie kroki, ale zamiast tego Dodaj akcję **Przeglądaj komunikaty** .

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection). W przeciwnym razie używane jest domyślnie pierwsze skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Lub wybierz inne połączenie.

1. Podaj informacje dla akcji.

1. Zapisz i uruchom aplikację logiki.

   Gdy aplikacja logiki zakończy działanie, Oto przykładowe dane wyjściowe akcji **Przeglądaj komunikaty** :

   ![Przykładowe dane wyjściowe polecenia Przeglądaj wiadomości](media/connectors-create-api-mq/browse-messages-output.png)

## <a name="receive-single-message"></a>Odbierz pojedynczy komunikat

Akcja **Odbierz wiadomość** zawiera te same dane wejściowe i wyjściowe co Akcja **Przeglądaj wiadomość** . Gdy używasz **komunikatu Receive**, komunikat jest usuwany z kolejki.

## <a name="receive-multiple-messages"></a>Odbieranie wielu komunikatów

Akcja **Odbierz komunikaty** ma te same dane wejściowe i wyjściowe co Akcja **Przeglądaj wiadomości** . W przypadku korzystania z **komunikatów Receive** komunikaty są usuwane z kolejki.

> [!NOTE]
> Podczas uruchamiania akcji Przeglądaj lub Odbierz w kolejce, która nie ma żadnych komunikatów, akcja kończy się niepowodzeniem w wyniku:
>
> ![MQ — błąd "Brak komunikatu"](media/connectors-create-api-mq/mq-no-message-error.png)

## <a name="send-message"></a>Wyślij wiadomość

1. Wykonaj poprzednie kroki, ale zamiast tego Dodaj akcję **Wyślij wiadomość** .

1. Jeśli połączenie MQ nie zostało jeszcze utworzone, zostanie wyświetlony monit o [utworzenie tego połączenia](#create-connection). W przeciwnym razie używane jest domyślnie pierwsze skonfigurowane połączenie. Aby utworzyć nowe połączenie, wybierz pozycję **Zmień połączenie**. Lub wybierz inne połączenie.

1. Podaj informacje dla akcji. W przypadku **MessageType** wybierz prawidłowy typ komunikatu: **datagram**, **odpowiedź** lub **żądanie**

   ![Właściwości akcji "Wyślij wiadomość"](media/connectors-create-api-mq/send-message-properties.png)

1. Zapisz i uruchom aplikację logiki.

   Gdy aplikacja logiki zakończy działanie, Oto przykładowe dane wyjściowe akcji **Wyślij wiadomość** :

   ![Przykład danych wyjściowych "Send Message"](media/connectors-create-api-mq/send-message-output.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak akcje i limity, które są opisane w pliku Swagger łącznika, przejrzyj [stronę odwołania łącznika](/connectors/mq/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
