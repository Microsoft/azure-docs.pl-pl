---
title: Azure CDN dziennikami nieprzetworzonymi HTTP
description: W tym artykule opisano Azure CDN dziennikach nieprzetworzonych HTTP.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80371634"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN dziennikami nieprzetworzonymi HTTP
Dzienniki RAW zawierają bogate informacje o operacjach i błędach, które są ważne w przypadku inspekcji i rozwiązywania problemów. Dzienniki pierwotne różnią się od dzienników aktywności. Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure. Dzienniki pierwotne zawierają rekord operacji dla zasobu.

> [!IMPORTANT]
> Funkcja dzienników RAW protokołu HTTP jest dostępna dla Azure CDN firmy Microsoft.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Konfiguracja

Aby skonfigurować dzienniki pierwotne dla Azure CDN z profilu Microsoft: 

1. Z menu Azure Portal wybierz pozycję **wszystkie zasoby** >> **\<>profilu sieci CDN **.

2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyki**.

3. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.

    ![Ustawienie diagnostyczne usługi CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Dzienniki RAW są dostępne tylko na poziomie profilu, podczas gdy zagregowane dzienniki kodów stanu HTTP są dostępne na poziomie punktu końcowego.

4. W obszarze **Ustawienia diagnostyczne**wprowadź nazwę ustawienia diagnostycznego w obszarze **Nazwa ustawień diagnostycznych**.

5. Wybierz **Dziennik** i Ustaw czas przechowywania w dniach.

6. Wybierz **szczegóły lokalizacji docelowej**. Opcje miejsca docelowego to:
    * **Wysyłanie do usługi Log Analytics**
        * Wybierz **obszar roboczy** **subskrypcja** i log Analytics.
    * **Archiwizowanie na koncie magazynu**
        * Wybierz **subskrypcję** i **konto magazynu**.
    * **Przesyłanie strumieniowe do centrum zdarzeń**
        * Wybierz **subskrypcję**, **przestrzeń nazw centrum zdarzeń**, **nazwę centrum zdarzeń (opcjonalnie)** i **nazwę zasad centrum zdarzeń**.

    ![Ustawienie diagnostyczne usługi CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Wybierz pozycję **Zapisz**.

## <a name="raw-logs-properties"></a>Właściwości dzienników nieprzetworzonych

Azure CDN z usługi firmy Microsoft obecnie udostępnia dzienniki pierwotne. Dzienniki RAW zapewniają pojedyncze żądania interfejsu API za pomocą każdego wpisu, który ma następujący schemat: 

| Właściwość              | Opis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |
| HttpMethod            | Metoda HTTP używana przez żądanie.                                                                                                                                                                     |
| HttpVersion           | Typ żądania lub połączenia.                                                                                                                                                                   |
| RequestUri            | Identyfikator URI odebranego żądania.                                                                                                                                                                         |
| RequestBytes          | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania.                                                                                                   |
| ResponseBytes         | Bajty wysłane przez serwer wewnętrznej bazy danych jako odpowiedź.                                                                                                                                                    |
| UserAgent             | Typ przeglądarki używany przez klienta.                                                                                                                                                               |
| ClientIp              | Adres IP klienta, który wykonał żądanie.                                                                                                                                                  |
| TimeTaken             | Czas trwania akcji (w milisekundach).                                                                                                                                            |
| To elementu SecurityProtocol      | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania.                                                                                                                           |
| Endpoint              | Host punktu końcowego usługi CDN został skonfigurowany w ramach nadrzędnego profilu CDN.                                                                                                                                   |
| Nazwa hosta zaplecza     | Nazwa hosta zaplecza lub źródła, w którym są wysyłane żądania.                                                                                                                                |
| Wysłane do osłony pochodzenia | Jeśli wartość jest równa true, oznacza to, że żądanie zostało odebrane z pamięci podręcznej osłony pochodzenia, zamiast w punkcie obecności krawędzi. Tarcza źródła jest nadrzędną pamięcią podręczną służącą do usprawnienia współczynnika trafień pamięci podręcznej.                                       |
| HttpStatusCode        | Kod stanu HTTP zwrócony z serwera proxy.                                                                                                                                                        |
| HttpStatusDetails     | Stan wynikający z żądania. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołania do stanu.                                                                                              |
| Skakując                   | Punkt obecności, który odpowiedział na żądanie użytkownika. Punkty obecności "skróty" są kodami portów lotniczych odpowiednich linii metra.                                                                                   |
| Stan pamięci podręcznej          | Oznacza, czy obiekt został zwrócony z pamięci podręcznej lub pochodzi z źródła.                                                                                                             |
> [!IMPORTANT]
> Funkcja dzienników RAW protokołu HTTP jest dostępna automatycznie dla wszystkich profilów utworzonych lub zaktualizowanych po **25 lutego 2020**. W przypadku utworzonych wcześniej profilów usługi CDN należy zaktualizować punkt końcowy usługi CDN po skonfigurowaniu rejestrowania. Na przykład jeden może przechodzić do filtrowania geograficznego w punktach końcowych usługi CDN i blokować każdy kraj, który nie jest związany z obciążeniem i trafiać. 

> [!NOTE]
> Dzienniki można wyświetlać w profilu Log Analytics, uruchamiając zapytanie. Przykładowe zapytanie będzie wyglądać jak AzureDiagnostics | gdzie Category = = "AzureCdnAccessLog"

## <a name="next-steps"></a>Następne kroki
W tym artykule włączono dzienniki RAW protokołu HTTP dla usługi Microsoft CDN.

Aby uzyskać więcej informacji na temat Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Analizuj](cdn-log-analysis.md) Azure CDN wzorców użytkowania.

* Dowiedz się więcej o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Skonfiguruj [log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
