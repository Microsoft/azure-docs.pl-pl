---
title: Konfigurowanie analizy zależności bez agentów w ocenie serwera Azure Migrate
description: Skonfiguruj analizę zależności bez agenta w ocenie serwera Azure Migrate.
ms.topic: how-to
ms.date: 6/08/2020
ms.openlocfilehash: 2e6e562a18fa2ee0b89416ea67cc15394e760ada
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536442"
---
# <a name="analyze-machine-dependencies-agentless"></a>Analizowanie zależności maszyny (bez agentów)

W tym artykule opisano sposób konfigurowania analizy zależności bez agentów w Azure Migrate: Ocena serwera. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować i zrozumieć zależności między maszynami w celu oceny i migracji do platformy Azure.


> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie dostępna w wersji zapoznawczej dla maszyn wirtualnych VMware odnalezionych za pomocą narzędzia do oceny serwera Azure Migrate:.
> Funkcje mogą być ograniczone lub niekompletne.
> Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="current-limitations"></a>Bieżące ograniczenia

- W widoku Analiza zależności nie można obecnie dodawać ani usuwać serwera z grupy.
- Mapa zależności dla grupy serwerów jest obecnie niedostępna.
- Zbieranie danych zależności można skonfigurować współbieżnie dla serwerów 400. Można analizować większą liczbę serwerów przez sekwencjonowanie w partiach 400.

## <a name="before-you-start"></a>Przed rozpoczęciem

- [Przejrzyj](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) obsługiwane systemy operacyjne i wymagane uprawnienia.
- Upewnij się, że:
    - Mieć projekt Azure Migrate. Jeśli tego nie zrobisz, [Utwórz](how-to-add-tool-first-time.md) je teraz.
    - Sprawdź, czy [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera do projektu.
    - Skonfiguruj [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania maszyn lokalnych. [Skonfiguruj urządzenie](how-to-set-up-appliance-vmware.md) dla maszyn wirtualnych VMware. Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera.
- Sprawdź, czy narzędzia VMware (nowsze niż 10,2) są zainstalowane na każdej maszynie wirtualnej, którą chcesz przeanalizować.


## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnalezienia zależności. [Dowiedz się więcej](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) o wymaganiach dotyczących konta maszyn wirtualnych z systemami Windows i Linux.


## <a name="add-the-user-account-to-the-appliance"></a>Dodaj konto użytkownika do urządzenia

Dodaj konto użytkownika do urządzenia.

1. Otwórz aplikację zarządzanie urządzeniami. 
2. Przejdź do panelu **Podaj szczegóły programu vCenter** .
3. W obszarze **Znajdź aplikację i zależności na maszynach wirtualnych**kliknij pozycję **Dodaj poświadczenia** .
3. Wybierz **system operacyjny**, podaj przyjazną nazwę konta i hasło w polu **Nazwa użytkownika** / **Password**
6. Kliknij pozycję **Zapisz**.
7. Kliknij przycisk **Zapisz i Rozpocznij odnajdywanie**.

    ![Dodaj konto użytkownika maszyny wirtualnej](./media/how-to-create-group-machine-dependencies-agentless/add-vm-credential.png)

## <a name="start-dependency-discovery"></a>Uruchom odnajdywanie zależności

Wybierz maszyny, na których chcesz włączyć odnajdowanie zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Dodaj serwery**.
4. Na stronie **Dodawanie serwerów** wybierz urządzenie, które odnajduje odpowiednie maszyny.
5. Z listy maszyna wybierz maszyny.
6. Kliknij pozycję **Dodaj serwery**.

    ![Uruchom odnajdywanie zależności](./media/how-to-create-group-machine-dependencies-agentless/start-dependency-discovery.png)

Możesz wizualizować zależności na sześć godzin po rozpoczęciu odnajdywania zależności.

## <a name="visualize-dependencies"></a>Wizualizacja zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Wyszukaj maszynę, którą chcesz wyświetlić.
3. W kolumnie **zależności** kliknij pozycję **Wyświetl zależności.**
4. Zmień okres, dla którego ma zostać wyświetlona mapa, przy użyciu listy rozwijanej czas **trwania** .
5. Rozwiń grupę **klientów** , aby wyświetlić listę maszyn z zależnością na wybranym komputerze.
6. Rozwiń grupę **portów** , aby wyświetlić listę maszyn z zależnością od wybranej maszyny.
7. Aby przejść do widoku mapy dowolnych maszyn zależnych, kliknij nazwę komputera > **Załaduj mapę serwera**

    ![Rozwiń węzeł Grupa portów serwera i Załaduj mapę serwera](./media/how-to-create-group-machine-dependencies-agentless/load-server-map.png)

    ![Rozwiń grupę klientów ](./media/how-to-create-group-machine-dependencies-agentless/expand-client-group.png)

8. Rozwiń wybraną maszynę, aby wyświetlić szczegóły poziomu procesu dla każdej zależności.

    ![Rozwiń serwer, aby wyświetlić procesy](./media/how-to-create-group-machine-dependencies-agentless/expand-server-processes.png)

> [!NOTE]
> Informacje o procesie dla zależności nie są zawsze dostępne. Jeśli ta funkcja jest niedostępna, zależność jest przedstawiana z procesem oznaczonym jako "nieznany proces".

## <a name="export-dependency-data"></a>Eksportuj dane zależności

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij pozycję **Eksportuj zależności aplikacji**.
4. Na stronie **Eksportuj zależności aplikacji** wybierz urządzenie, które odnajduje odpowiednie maszyny.
5. Wybierz godzinę rozpoczęcia i godzinę zakończenia. Należy pamiętać, że dane można pobrać tylko w ciągu ostatnich 30 dni.
6. Kliknij pozycję **Eksportuj zależność**.

Dane zależności są eksportowane i pobierane w formacie CSV. Pobrany plik zawiera dane zależności między wszystkimi maszynami, na których włączono obsługę analizy zależności. 

![Eksportuj zależności](./media/how-to-create-group-machine-dependencies-agentless/export.png)

### <a name="dependency-information"></a>Informacje o zależnościach

Każdy wiersz w wyeksportowanym woluminie CSV odpowiada zależności zaobserwowanej w określonym gnieździe czasu. 

Poniższa tabela zawiera podsumowanie pól w wyeksportowanym pliku CSV. Zwróć uwagę, że pola Nazwa serwera, aplikacja i proces są wypełniane tylko dla serwerów, na których jest włączona analiza zależności bez agenta.

**Nazwa pola** | **Szczegóły**
--- | --- 
Timeslot | Timeslot, w którym zaobserwowano zależność. <br/> Dane zależności są obecnie przechwytywane za pośrednictwem gniazd 6-godzinnych.
Nazwa serwera źródłowego | Nazwa maszyny źródłowej 
Aplikacja źródłowa | Nazwa aplikacji na maszynie źródłowej 
Proces źródłowy | Nazwa procesu na maszynie źródłowej 
Nazwa serwera docelowego | Nazwa maszyny docelowej
Docelowy adres IP | Adres IP maszyny docelowej
Aplikacja docelowa | Nazwa aplikacji na maszynie docelowej
Proces docelowy | Nazwa procesu na maszynie docelowej 
Port docelowy | Numer portu na maszynie docelowej


## <a name="stop-dependency-discovery"></a>Zatrzymaj odnajdywanie zależności

Wybierz maszyny, na których chcesz zatrzymać odnajdywanie zależności.

1. W **Azure Migrate: Ocena serwera**, kliknij przycisk **odnalezione serwery**.
2. Kliknij ikonę **analiza zależności** .
3. Kliknij przycisk **Usuń serwery**.
3. Na stronie **usuwanie serwerów** wybierz **urządzenie** , które odnajduje maszyny wirtualne, na których chcesz zatrzymać odnajdywanie zależności.
4. Z listy maszyna wybierz maszyny.
5. Kliknij przycisk **Usuń serwery**.


## <a name="next-steps"></a>Następne kroki

[Grupuj maszyny](how-to-create-a-group.md) do oceny.
