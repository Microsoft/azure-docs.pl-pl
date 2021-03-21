---
title: Rozwiązywanie problemów z projektem Azure FarmBeats
description: W tym artykule opisano sposób rozwiązywania problemów z usługą Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: c45b6196b82682b37e253a33eed3940b68b4d61e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172988"
---
# <a name="troubleshoot-azure-farmbeats"></a>Rozwiązywanie problemów z projektem Azure FarmBeats

Ten artykuł zawiera rozwiązania typowych problemów z usługą Azure FarmBeats. Aby uzyskać dodatkową pomoc, skontaktuj się z nami [&forum pomocy technicznej](/answers/topics/azure-farmbeats.html) lub Wyślij wiadomość e-mail na adres farmbeatssupport@microsoft.com .

> [!NOTE]
  > Jeśli zainstalowano FarmBeats w kwietniu, a zadania kończą się niepowodzeniem z pustym komunikatem o błędzie, instalacja mogła nie zostać przypisana do przydziału usługi Batch w celu określenia priorytetów wsparcia dla krytycznych organizacji kondycji i bezpieczeństwa. Więcej informacji można znaleźć [tutaj](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/). Musisz zażądać przydzielenia maszyn wirtualnych do konta wsadowego, aby pomyślnie uruchomić zadania.

## <a name="install-issues"></a>Problemy z instalacją

  > [!NOTE]
  > W przypadku ponownego uruchomienia instalacji z powodu błędu należy usunąć **grupę zasobów** lub usunąć wszystkie zasoby z grupy zasobów przed ponownym wyzwoleniem instalacji.

### <a name="invalid-sentinel-credentials"></a>Nieprawidłowe poświadczenia kontrolki

Poświadczenia kontrolki wskaźnikowej podane podczas instalacji są nieprawidłowe. Uruchom ponownie instalację z prawidłowymi poświadczeniami.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Osiągnięto limit przydziału kont usługi Batch dla określonej subskrypcji

Zwiększ limit przydziału lub Usuń nieużywane konta programu Batch i ponownie uruchom instalację.

### <a name="invalid-resource-group-location"></a>Nieprawidłowa lokalizacja grupy zasobów

Upewnij się, że **Grupa zasobów** znajduje się w tej samej lokalizacji co **region** określony podczas instalacji.

### <a name="other-install-issues"></a>Inne problemy z instalacją

Skontaktuj się z nami, podając następujące informacje:

- Identyfikator subskrypcji
- Nazwa grupy zasobów
- Postępuj zgodnie z poniższymi instrukcjami, aby dołączyć plik dziennika dla niepowodzenia wdrożenia:

    1. Przejdź do **grupy zasobów** w Azure Portal.

    2. Wybierz pozycję **wdrożenia** w obszarze **Ustawienia** po lewej stronie.

    3. Dla każdego wdrożenia, które **nie powiodło się**, należy wybrać szczegóły i pobrać Szczegóły wdrożenia. Dołącz ten plik do wiadomości e-mail.

## <a name="sensor-telemetry"></a>Dane telemetryczne czujnika

### <a name="cant-view-telemetry-data"></a>Nie można wyświetlić danych telemetrycznych

**Objaw**: wdrożono urządzenia lub czujniki, a połączono FarmBeats z partnerem urządzenia, ale nie można uzyskać ani wyświetlić danych telemetrycznych w FarmBeats.

**Akcja naprawcza**

1. Przejdź do grupy zasobów FarmBeats.
2. Wybierz przestrzeń nazw **centrum zdarzeń** ("Sensor-partner-EH-przestrzeń nazw-XXXX"), kliknij pozycję "Event Hubs", a następnie wyszukaj liczbę komunikatów przychodzących w centrum zdarzeń przypisanym do partnera.
3. Wykonaj jedną z następujących czynności:

   - Jeśli nie ma *żadnych komunikatów przychodzących*, skontaktuj się z partnerem urządzenia.  
   - Jeśli istnieją *przychodzące komunikaty*, skontaktuj się z nami przy użyciu dzienników Datahub i akceleratora oraz przechwyconych danych telemetrycznych.

Aby zrozumieć sposób pobierania dzienników, przejdź do sekcji ["zbieranie dzienników ręcznie"](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Nie można wyświetlić danych telemetrycznych po pozyskaniu danych historycznych/przesyłanych strumieniowo z czujników

**Objaw**: wdrożono urządzenia lub czujniki, a w usłudze EventHub zostały utworzone urządzenia/czujniki dotyczące FarmBeats i pozyskiwanej telemetrii, ale nie można uzyskać ani wyświetlić danych telemetrycznych w FarmBeats.

**Akcja naprawcza**

1. Upewnij się, że pomyślnie ukończono rejestrację partnera — możesz to sprawdzić, przechodząc do datahub Swagger, przejdź do interfejsu API/partner, a następnie wybierz pozycję Pobierz i sprawdź, czy partner jest zarejestrowany. Jeśli nie, wykonaj następujące [kroki](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) , aby dodać partnera.

2. Upewnij się, że użyto poprawnego formatu komunikatów telemetrycznych:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Nie masz parametrów połączenia usługi Azure Event Hubs

**Akcja naprawcza**

1. W programie Datahub Swagger przejdź do interfejsu API partnera.
2. Wybierz pozycję **Pobierz**  >  **spróbuj**  >  **wykonać** operację.

> [!NOTE]
> Identyfikator partnera partnera, który Cię interesuje.

3. Wróć do interfejsu API partnera i wybierz pozycję **Pobierz/ \<ID>**.
4. Określ identyfikator partnera z kroku 3, a następnie wybierz pozycję **Wykonaj**.

   Odpowiedź interfejsu API powinna mieć Event Hubs parametry połączenia.

### <a name="device-appears-offline"></a>Urządzenie jest w trybie offline

**Objawy**: urządzenia są zainstalowane i połączono FarmBeats z partnerem urządzenia. Urządzenia są w trybie online i wysyłają dane telemetryczne, ale są wyświetlane w trybie offline.

**Akcja naprawcza** Interwał raportowania nie jest skonfigurowany dla tego urządzenia. Aby ustawić interwał raportowania, skontaktuj się z producentem urządzenia. 

### <a name="error-deleting-a-device"></a>Błąd usuwania urządzenia

Podczas usuwania urządzenia może wystąpić jeden z następujących typowych scenariuszy błędów:  

**Komunikat**: "urządzenie jest przywoływane w czujników: istnieje co najmniej jedna czujników skojarzona z urządzeniem. Usuń czujniki, a następnie usuń urządzenie ".  

**Znaczenie**: urządzenie jest skojarzone z wieloma czujnikami, które są wdrożone w farmie.

**Akcja naprawcza**  

1. Usuń czujniki skojarzone z urządzeniem za pomocą akceleratora.  
2. Jeśli chcesz skojarzyć czujniki z innym urządzeniem, skontaktuj się z partnerem urządzenia, aby to zrobić.  
3. Usuń urządzenie przy użyciu `DELETE API` wywołania i ustaw dla parametru Force *wartość true*.  

**Komunikat**: "urządzenie jest przywoływane w urządzeniach jako ParentDeviceId: istnieje co najmniej jedno urządzenie, które jest skojarzone z urządzeniem jako urządzenia podrzędne. Usuń je, a następnie usuń to urządzenie ".  

**Znaczenie**: na urządzeniu są skojarzone inne urządzenia.  

**Akcja naprawcza**

1. Usuń urządzenia, które są skojarzone z tym określonym urządzeniem.  
2. Usuń określone urządzenie.  

    > [!NOTE]
    > Nie można usunąć urządzenia, jeśli są z nim skojarzone czujniki. Więcej informacji o sposobach usuwania skojarzonych czujników znajduje się w sekcji **usuwanie czujnika** w temacie [pobieranie danych czujników z partnerów czujników](get-sensor-data-from-sensor-partner.md).
    > Partnerzy nie mają uprawnień do usuwania urządzenia lub czujnika. Tylko Administratorzy mają uprawnienia do usuwania.

## <a name="issues-with-jobs"></a>Problemy związane z zadaniami

### <a name="farmbeats-internal-error"></a>Błąd wewnętrzny FarmBeats

**Komunikat**: "FarmBeats błąd wewnętrzny, zobacz Przewodnik rozwiązywania problemów, aby uzyskać więcej szczegółów".

**Akcja naprawcza** Ten problem może być spowodowany tymczasowym błędem w potoku danych. Utwórz ponownie zadanie. Jeśli błąd będzie się powtarzać, skontaktuj się z nami przy użyciu komunikatu o błędzie/dzienników.

## <a name="accelerator-troubleshooting"></a>Rozwiązywanie problemów z akceleratorem

### <a name="access-control"></a>Kontrola dostępu

**Problem**: Wystąpił błąd podczas dodawania przypisania roli.

**Komunikat**: "nie znaleziono pasujących użytkowników".

**Akcja naprawcza** Sprawdź identyfikator poczty e-mail, dla którego próbujesz dodać przypisanie roli. Identyfikator poczty e-mail musi być dokładnym dopasowaniem identyfikatora, który jest zarejestrowany dla tego użytkownika w Active Directory. Jeśli błąd będzie się powtarzać, skontaktuj się z nami przy użyciu komunikatu o błędzie/dzienników.

### <a name="unable-to-log-in-to-accelerator"></a>Nie można zalogować się do akceleratora

**Komunikat**: "błąd: nie masz uprawnień do wywołania usługi. Skontaktuj się z administratorem, aby uzyskać autoryzację ".

**Akcja naprawcza** Poproszenie administratora o autoryzację w celu uzyskania dostępu do wdrożenia FarmBeats. Można to zrobić, wykonując wpis interfejsów API RoleAssignment lub za pomocą Access Control w okienku **Ustawienia** w akceleratorze.  

Jeśli masz już udzielony dostęp i wystąpił ten błąd, spróbuj ponownie, odświeżając stronę. Jeśli błąd będzie się powtarzać, skontaktuj się z nami przy użyciu komunikatu o błędzie/dzienników.

![Zrzut ekranu pokazujący Błąd autoryzacji.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemy akceleratora  

**Problem**: Wystąpił błąd akceleratora nieokreślonej przyczyny.

**Komunikat**: "błąd: Wystąpił nieznany błąd".

**Akcja naprawcza** Ten błąd występuje, gdy strona jest bezczynna za długa. Odśwież stronę. Jeśli błąd będzie się powtarzać, skontaktuj się z nami przy użyciu komunikatu o błędzie/dzienników.

**Problem**: akcelerator FarmBeats nie pokazuje najnowszej wersji, nawet po uaktualnieniu FarmBeatsDeployment.

**Akcja naprawcza** Ten błąd występuje z powodu trwałości procesu roboczego usługi w przeglądarce. Wykonaj następujące czynności:

1. Zamknij wszystkie karty przeglądarki z otwartym akceleratorem, a następnie zamknij okno przeglądarki.
2. Uruchom nowe wystąpienie przeglądarki i ponownie załaduj akcelerator URI. Ta akcja spowoduje załadowanie nowej wersji akceleratora.

## <a name="sentinel-imagery-related-issues"></a>Wskaźnik: problemy związane z obrazami

### <a name="wrong-username-or-password"></a>Nieprawidłowa nazwa użytkownika lub hasło

**Komunikat o niepowodzeniu zadania**: "pełne uwierzytelnianie jest wymagane w celu uzyskania dostępu do tego zasobu".

**Akcja naprawcza**: wykonaj jedną z następujących czynności:

- Zaktualizuj FarmBeats przy użyciu prawidłowej nazwy użytkownika/hasła, wykonując poniższe kroki, a następnie spróbuj ponownie wykonać zadanie.

  **Aktualizuj nazwę użytkownika wskaźnikowego**

    1. Zaloguj się do [Azure Portal](https://portal.azure.com).
    2. W polu **wyszukiwania** Wyszukaj grupę zasobów FarmBeats Datahub.
    3. Wybierz pozycję Magazyn konta magazynu * * * * * > **kontenery** tworzenie  >  **wsadowe plików**  >  **to_vm**  >  **config.ini**
    4. Wybierz pozycję **Edytuj**
    5. Aktualizowanie nazwy użytkownika w sekcji sentinel_account

  **Zaktualizuj hasło do kontrolki**

    1. Zaloguj się do [Azure Portal](https://portal.azure.com).
    2. W polu **wyszukiwania** Wyszukaj grupę zasobów FarmBeats Datahub.
    3. Wybierz magazyn kluczy — * * * * *
    4. Wybieranie zasad dostępu w obszarze Ustawienia
    5. Wybierz pozycję **Dodaj zasady dostępu**
    6. Użyj **zarządzania kluczami tajnymi** dla konfiguracji z szablonu i Dodaj siebie do podmiotu zabezpieczeń
    7. Wybierz pozycję **Dodaj**, a następnie wybierz pozycję **Zapisz** na stronie **zasady dostępu**
    8. Wybierz wpisy **tajne** w obszarze **Ustawienia**
    9. Wybierz **wskaźnik kontrolny — hasło**
    10. Utwórz nową wersję wartości i włącz ją.

- Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centrum centrów danych: niewłaściwy adres URL lub niedostępna witryna

**Komunikat o niepowodzeniu zadania**: "Niestety, wystąpił problem. Strona, do której próbujesz uzyskać dostęp, jest (tymczasowo) niedostępna. "

**Działanie naprawcze**:

1. Otwórz [wskaźnik](https://scihub.copernicus.eu/dhus/) kontrolny w przeglądarce, aby sprawdzić, czy witryna sieci Web jest dostępna.
2. Jeśli witryna sieci Web jest niedostępna, sprawdź, czy Zapora, Sieć firmowa lub inne oprogramowanie blokujące uniemożliwia dostęp do witryny sieci Web, a następnie wykonaj niezbędne kroki, aby zezwolić na adres URL wskaźnikiem. 
3. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.  

### <a name="sentinel-server-down-for-maintenance"></a>Serwer wskaźnikowy: do konserwacji

**Komunikat o niepowodzeniu zadania**: "Copernicus otwarte centrum dostępu zostanie wkrótce zakończone! Przepraszamy za niedogodności, ale w tej chwili wykonujemy pewne czynności konserwacyjne. Wkrótce będziemy z powrotem w trybie online. 

**Działanie naprawcze**:

Ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne.

1. Jeśli jakieś zadanie lub potok nie powiedzie się, ponieważ trwa wykonywanie konserwacji, prześlij ponownie zadanie po pewnym czasie. 

   Aby uzyskać informacje o wszelkich planowanych lub nieplanowanych działaniach związanych z konserwacją wskaźnikiem, przejdź do witryny [Copernicus Open Access Hub](https://scihub.copernicus.eu/news/) .  

2. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Wskaźnik kontrolny: osiągnięto maksymalną liczbę połączeń

**Komunikat o niepowodzeniu zadania**: "Maksymalna liczba dwóch współbieżnych przepływów osiągniętych przez użytkownika" \<username> ".

**Znaczenie**: Jeśli zadanie nie powiedzie się, ponieważ osiągnięto maksymalną liczbę połączeń, to samo konto wskaźnikowe jest używane w wielu zadaniach.

**Działanie naprawcze**: Spróbuj wykonać jedną z następujących czynności:

* Poczekaj na zakończenie innych zadań przed ponownym uruchomieniem zadania zakończonego niepowodzeniem.
* Utwórz nowe konto wskaźnikiem, a następnie zaktualizuj nazwę użytkownika i hasło do kontrolki w FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Serwer wskaźnikowy: odmowa połączenia

**Komunikat o niepowodzeniu zadania**: "serwer odmówił połączenia w: http://172.30.175.69:8983/solr/dhus ".

**Działania naprawcze**: ten problem może wystąpić, jeśli na serwerze wskaźnikowym są wykonywane jakiekolwiek działania konserwacyjne.

1. Jeśli jakieś zadanie lub potok nie powiedzie się, ponieważ trwa wykonywanie konserwacji, prześlij ponownie zadanie po pewnym czasie.

   Aby uzyskać informacje o wszelkich planowanych lub nieplanowanych działaniach związanych z konserwacją wskaźnikiem, przejdź do witryny [Copernicus Open Access Hub](https://scihub.copernicus.eu/news/) .  

2. Uruchom ponownie zadanie zakończone niepowodzeniem lub Uruchom zadanie indeksów satelitarnych dla zakresu dat od 5 do 7 dni, a następnie sprawdź, czy zadanie zakończyło się pomyślnie.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa wilgotności gleby ma białe obszary

**Problem**: **Mapa wilgotności gleby** została wygenerowana, ale mapa ma głównie białe obszary.

**Działanie naprawcze**: ten problem może wystąpić, jeśli w przypadku, gdy dla danego czasu, dla którego zażądano mapy, istnieją wartości NDVI mniejsze niż 0,3. Aby uzyskać więcej informacji, odwiedź [Przewodnik techniczny w witrynie wskaźnikowej](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Uruchom ponownie zadanie dla innego zakresu dat i sprawdź, czy wartości NDVI w indeksach satelitarnych są większe niż 0,3.

## <a name="collect-logs-manually"></a>Zbierz dzienniki ręcznie

[Zainstaluj i wdróż Eksplorator usługi Azure Storage]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Zbieranie dzienników zadań Azure Data Factory lub dzienników App Service w Datahub

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów FarmBeats Datahub.
3. Na pulpicie nawigacyjnym **grupy zasobów** Wyszukaj konto magazynu *datahublogs \** . Na przykład *datahublogsmvxmq*.  
4. W kolumnie **Nazwa** wybierz konto magazynu, aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W **okienku datahubblogs \* *_ wybierz pozycję _* Otwórz w Eksploratorze,** aby wyświetlić **otwarty Eksplorator usługi Azure Storage** aplikację.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję Dzienniki **zadań** dla dzienników Azure Data Factory lub dzienników **appinsights** dla App Service dzienników.
7. Wybierz pozycję **Pobierz** i Pobierz dzienniki do folderu lokalnego na komputerze.

    ![Zrzut ekranu pokazujący pobrane pliki dziennika.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Zbieranie dzienników zadań Azure Data Factory lub dzienników App Service dla akceleratora

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W polu **wyszukiwania** Wyszukaj grupę zasobów akceleratora FarmBeats.
3. Na pulpicie nawigacyjnym **grupy zasobów** Wyszukaj konto magazynu *magazynu \** . Na przykład *storagedop4k \**.
4. Wybierz konto magazynu w kolumnie **Nazwa** , aby wyświetlić pulpit nawigacyjny **konta magazynu** .
5. W **okienku Storage \* *_ wybierz pozycję _* Otwórz w Eksploratorze,** aby otworzyć aplikację Eksplorator usługi Azure Storage.
6. W lewym okienku wybierz pozycję **kontenery obiektów BLOB**, a następnie wybierz pozycję Dzienniki **zadań** dla dzienników Azure Data Factory lub dzienników **appinsights** dla App Service dzienników.
7. Wybierz pozycję **Pobierz** i Pobierz dzienniki do folderu lokalnego na komputerze.

## <a name="high-cpu-usage"></a>Wysokie użycie procesora

**Błąd**: otrzymasz alert e-mail, który odwołuje się do **alertu o wysokim zużyciu procesora CPU**.

**Działanie naprawcze**:

1. Przejdź do grupy zasobów FarmBeats Datahub.
2. Wybierz **usługę App Service**.  
3. Przejdź do strony skalowanie w górę [App Service cennika](https://azure.microsoft.com/pricing/details/app-service/windows/), a następnie wybierz odpowiednią warstwę cenową.

## <a name="weather-data-job-failures"></a>Błędy zadań dla danych pogody

**Błąd**: uruchamiasz zadania w celu uzyskania danych pogodowych, ale zadanie kończy się niepowodzeniem

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Zbieranie dzienników w celu rozwiązywania błędów zadań dotyczących danych pogody

1. Przejdź do grupy zasobów FarmBeats w Azure Portal.
2. Kliknij usługę Data Factory, która jest częścią grupy zasobów. Usługa będzie mieć tag "SKU: Datahub"

> [!NOTE]
> Aby wyświetlić Tagi usług w grupie zasobów, kliknij pozycję "Edytuj kolumny" i Dodaj "Tagi" do widoku grupy zasobów

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Zrzut ekranu, który podświetla tag SKU: Datahub.":::

3. Na stronie Przegląd fabryki danych kliknij pozycję **Tworzenie i monitorowanie**. Zostanie otwarta nowa karta w przeglądarce. Kliknij pozycję **monitor**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Zrzut ekranu, który podświetla opcję menu Monitoruj.":::

4. Zostanie wyświetlona lista uruchomień potoków, które są częścią wykonywania zadania pogodowego. Kliknij zadanie, dla którego chcesz zebrać dzienniki
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Zrzut ekranu, który podświetla opcję menu uruchomienia potoku i wybrane zadanie.":::

5. Na stronie Przegląd potoku zostanie wyświetlona lista uruchomień działania. Zanotuj identyfikatory przebiegu działań, dla których chcesz zebrać dzienniki
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Zrzut ekranu przedstawiający listę uruchomień działania.":::

6. Wróć do grupy zasobów FarmBeats w Azure Portal i kliknij konto magazynu o nazwie **datahublogs-xxxx**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Zrzut ekranu, który wyróżnia konto magazynu o nazwie datahublogs-XXXX.":::

7. Kliknij pozycję **kontenery**  ->  **adfjobs**. W polu wyszukiwania wprowadź identyfikator uruchomienia zadania zanotowany w kroku 5 powyżej.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="FarmBeats projektu":::

8. Wynik wyszukiwania będzie zawierać folder, w którym znajdują się dzienniki dotyczące danego zadania. Pobierz dzienniki i wyślij je do farmbeatssupport@microsoft.com pomocy w celu debugowania problemu.