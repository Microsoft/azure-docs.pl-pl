---
title: Obróć poświadczenia logowania w Azure Stream Analytics zadaniach
description: W tym artykule opisano sposób aktualizowania poświadczeń wejść i ujścia danych wyjściowych w zadaniach Azure Stream Analytics.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: fd6c072f9783e8ff5d4d5e465b513c2e530bfd63
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015236"
---
# <a name="rotate-login-credentials-for-inputs-and-outputs-of-a-stream-analytics-job"></a>Obróć poświadczenia logowania dla danych wejściowych i wyjściowych zadania Stream Analytics

Za każdym razem, gdy ponownie wygenerujesz poświadczenia dla danych wejściowych lub wyjściowych zadania Stream Analytics, należy zaktualizować to zadanie przy użyciu nowych poświadczeń. Należy zatrzymać zadanie przed aktualizacją poświadczeń, nie można zastąpić poświadczeń, gdy zadanie jest uruchomione. Aby skrócić opóźnienie między zatrzymywaniem i ponownym uruchamianiem zadania, Stream Analytics obsługuje wznawianie zadania z jego ostatnich danych wyjściowych. W tym temacie opisano proces rotacji poświadczeń logowania i ponownego uruchamiania zadania przy użyciu nowych poświadczeń.

## <a name="regenerate-new-credentials-and-update-your-job-with-the-new-credentials"></a>Wygeneruj ponownie nowe poświadczenia i zaktualizuj swoje zadanie przy użyciu nowych poświadczeń 

W tej sekcji przeprowadzimy Cię przez ponowne generowanie poświadczeń dla Blob Storage, Event Hubs, SQL Database i Table Storage. 

### <a name="blob-storagetable-storage"></a>Magazyn obiektów BLOB/magazyn tabel
1. Zaloguj się do Azure Portal > Przeglądaj konto magazynu używane jako dane wejściowe i wyjściowe dla zadania Stream Analytics.    
2. W sekcji Ustawienia Otwórz pozycję **klucze dostępu**. Między dwoma kluczami domyślnymi (Klucz1, klucz2) wybierz ten, który nie jest używany przez zadanie i ponownie go Wygeneruj:  
   ![Ponowne generowanie kluczy dla konta magazynu](media/stream-analytics-login-credentials-inputs-outputs/regenerate-storage-keys.png)
3. Skopiuj nowo wygenerowany klucz.    
4. W Azure Portal Przeglądaj zadanie Stream Analytics > wybierz pozycję **Zatrzymaj** i poczekaj na zatrzymanie zadania.    
5. Znajdź dane wejściowe/wyjściowe magazynu obiektów BLOB/Table, dla których chcesz zaktualizować poświadczenia.    
6. Znajdź pole **klucz konta magazynu** i wklej nowo wygenerowany klucz > kliknij przycisk **Zapisz**.    
7. Test połączenia zostanie uruchomiony automatycznie po zapisaniu zmian, można go wyświetlić na karcie powiadomienia. Istnieją dwie powiadomienia — jeden odpowiada za zapisanie aktualizacji, a inne odpowiadają za testowanie połączenia:  
   ![Powiadomienia po przeprowadzeniu edycji klucza](media/stream-analytics-login-credentials-inputs-outputs/edited-key-notifications.png)
8. Wykonaj [zadania z ostatniej zatrzymanej sekcji czas](#start-your-job-from-the-last-stopped-time) .

### <a name="event-hubs"></a>Usługa Event Hubs

1. Zaloguj się do Azure Portal > Przejrzyj centrum zdarzeń, które zostało użyte jako dane wejściowe i wyjściowe dla zadania Stream Analytics.    
2. W sekcji Ustawienia Otwórz pozycję **zasady dostępu współdzielonego** i wybierz wymagane zasady dostępu. Między **kluczem podstawowym** a **kluczem pomocniczym** wybierz ten, który nie jest używany przez zadanie i ponownie go Wygeneruj:  
   ![Wygeneruj ponownie klucze dla Event Hubs](media/stream-analytics-login-credentials-inputs-outputs/regenerate-event-hub-keys.png)
3. Skopiuj nowo wygenerowany klucz.    
4. W Azure Portal Przeglądaj zadanie Stream Analytics > wybierz pozycję **Zatrzymaj** i poczekaj na zatrzymanie zadania.    
5. Znajdź dane wejściowe/wyjściowe centrów zdarzeń, dla których chcesz zaktualizować poświadczenia.    
6. Znajdź pole **klucz zasad centrum zdarzeń** i wklej nowo wygenerowany klucz > kliknij przycisk **Zapisz**.    
7. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
8. Wykonaj [zadania z ostatniej zatrzymanej sekcji czas](#start-your-job-from-the-last-stopped-time) .

### <a name="sql-database"></a>SQL Database

Musisz nawiązać połączenie z usługą SQL Database, aby zaktualizować poświadczenia logowania istniejącego użytkownika. Poświadczenia można aktualizować za pomocą Azure Portal lub narzędzia po stronie klienta, takiego jak SQL Server Management Studio. W tej sekcji przedstawiono proces aktualizowania poświadczeń przy użyciu Azure Portal.

1. Zaloguj się do Azure Portal > Przeglądaj bazę danych SQL, która została użyta jako dane wyjściowe dla zadania Stream Analytics.    
2. Z **poziomu Eksploratora danych** Zaloguj się/Połącz z bazą danych > wybierz typ autoryzacji **jako uwierzytelnianie programu SQL Server** > wpisz nazwę **logowania** i **hasło** , > wybierz **przycisk OK**.  
   ![Wygeneruj ponownie poświadczenia dla SQL Database](media/stream-analytics-login-credentials-inputs-outputs/regenerate-sql-credentials.png)

3. Na karcie zapytanie Zmień hasło jednego z użytkowników, uruchamiając następujące zapytanie (Pamiętaj, aby zamienić `<user_name>` nazwę użytkownika i `<new_password>` nowe hasło):  

   ```SQL
   Alter user `<user_name>` WITH PASSWORD = '<new_password>'
   Alter role db_owner Add member `<user_name>`
   ```

4. Zanotuj nowe hasło.    
5. W Azure Portal Przeglądaj zadanie Stream Analytics > wybierz pozycję **Zatrzymaj** i poczekaj na zatrzymanie zadania.    
6. Znajdź SQL Database dane wyjściowe, dla których chcesz obrócić poświadczenia. Zaktualizuj hasło i Zapisz zmiany.    
7. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
8. Wykonaj [zadania z ostatniej zatrzymanej sekcji czas](#start-your-job-from-the-last-stopped-time) .

### <a name="power-bi"></a>Power BI
1. Zaloguj się do Azure Portal > Przejrzyj zadanie Stream Analytics > wybierz pozycję **Zatrzymaj** i poczekaj na zatrzymanie zadania.    
2. Znajdź Power BI dane wyjściowe, dla których chcesz odnowić poświadczenia, > kliknij pozycję **Odnów autoryzację** (komunikat o powodzeniu powinien zostać wyświetlony) > **Zapisz** zmiany.    
3. Test połączenia zostanie automatycznie uruchomiony po zapisaniu zmian, upewnij się, że pomyślnie przeszedł.    
4. Wykonaj [zadania z ostatniej zatrzymanej sekcji czas](#start-your-job-from-the-last-stopped-time) .

## <a name="start-your-job-from-the-last-stopped-time"></a>Rozpocznij zadanie od czasu ostatniego zatrzymania

1. Przejdź do okienka **Przegląd** zadania > wybierz pozycję **Rozpocznij** , aby uruchomić zadanie.    
2. Zaznacz pole **przy ostatnim zatrzymaniu** > kliknij przycisk **Rozpocznij**. Należy zauważyć, że opcja "Kiedy ostatnio zatrzymana" pojawia się tylko wtedy, gdy wcześniej uruchomiono zadanie i wygenerowało niektóre dane wyjściowe. Zadanie jest uruchamiane ponownie w oparciu o czas ostatniej wartości wyjściowej.
   ![Uruchamianie zadania Stream Analytics](media/stream-analytics-login-credentials-inputs-outputs/start-stream-analytics-job.png)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](/rest/api/streamanalytics/)