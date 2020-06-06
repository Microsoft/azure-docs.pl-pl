---
title: Konfigurowanie analizy zależności bez agentów w ocenie serwera Azure Migrate
description: Skonfiguruj analizę zależności bez agenta w ocenie serwera Azure Migrate.
ms.topic: how-to
ms.date: 2/24/2020
ms.openlocfilehash: 3259c861b0e64b560eb2a17a832a02b87855bebf
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449196"
---
# <a name="set-up-agentless-dependency-visualization"></a>Konfigurowanie wizualizacji zależności bez agenta 

W tym artykule opisano sposób konfigurowania analizy zależności bez agentów w Azure Migrate: Ocena serwera. [Analiza zależności](concepts-dependency-visualization.md) pomaga identyfikować i zrozumieć zależności między maszynami, które mają być oceniane i migrowane do platformy Azure.


> [!IMPORTANT]
> Wizualizacja zależności bez agenta jest obecnie dostępna tylko w wersji zapoznawczej dla maszyn wirtualnych VMware, odnaleziona za pomocą narzędzia do oceny serwera Azure Migrate:.
> Funkcje mogą być ograniczone lub niekompletne.
> Ta wersja zapoznawcza jest objęta wsparciem klienta i może być używana na potrzeby obciążeń produkcyjnych.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



## <a name="before-you-start"></a>Przed rozpoczęciem

- [Dowiedz się więcej o](concepts-dependency-visualization.md#agentless-analysis) analizie zależności bez agentów.
- [Zapoznaj](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) się z wymaganiami dotyczącymi wymagań wstępnych i pomocy technicznej w zakresie konfigurowania wizualizacji zależności bez agenta dla maszyn wirtualnych VMware
- Upewnij się, że [utworzono](how-to-add-tool-first-time.md) projekt Azure Migrate.
- Jeśli projekt został już utworzony, upewnij się, że [dodano](how-to-assess.md) Azure Migrate: Narzędzie do oceny serwera.
- Upewnij się, że skonfigurowano [urządzenie Azure Migrate](migrate-appliance.md) w celu odnajdywania maszyn lokalnych. Dowiedz się, jak skonfigurować urządzenie dla maszyn wirtualnych [VMware](how-to-set-up-appliance-vmware.md) . Urządzenie odnajduje maszyny lokalne i wysyła metadane i dane wydajności do Azure Migrate: Ocena serwera.


## <a name="current-limitations"></a>Bieżące ograniczenia

- Teraz nie można dodać ani usunąć serwera z grupy w widoku Analiza zależności.
- Mapa zależności dla grupy serwerów nie jest obecnie dostępna.
- Obecnie nie można pobrać danych zależności w formacie tabelarycznym.

## <a name="create-a-user-account-for-discovery"></a>Tworzenie konta użytkownika na potrzeby odnajdywania

Skonfiguruj konto użytkownika, aby Ocena serwera mogła uzyskać dostęp do maszyny wirtualnej w celu odnajdywania. [Dowiedz się więcej](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) o wymaganiach dotyczących konta.


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

    ![Export dependencies](./media/how-to-create-group-machine-dependencies-agentless/export.png)


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
