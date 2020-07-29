---
title: Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center
description: Ten dokument ułatwia korzystanie z adaptacyjnej kontroli aplikacji w Azure Security Center, aby dozwolonych aplikacje działające na maszynach platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77604677"
---
# <a name="adaptive-application-controls"></a>Funkcje adaptacyjnego sterowania aplikacjami
Z tego przewodnika dowiesz się, jak skonfigurować funkcje sterowania aplikacjami w usłudze Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co to są funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center?
Adaptacyjna kontrola aplikacji to inteligentne, zautomatyzowane i kompleksowe rozwiązanie od Azure Security Center, które pomaga kontrolować, które aplikacje mogą być uruchamiane na maszynach z platformą Azure i poza platformą Azure (Windows i Linux). Dzięki temu można zwiększyć ochronę komputerów przed złośliwym oprogramowaniem. Security Center korzysta z uczenia maszynowego do analizowania aplikacji uruchomionych na maszynach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i konserwowania zasad listy dozwolonych aplikacji, co pozwala:

- Blokuj lub Ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogą zostać pominięte przez rozwiązania chroniące przed złośliwym kodem.
- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

> [!NOTE]
> W przypadku maszyn z systemami innym niż Azure i Linux funkcje adaptacyjnego sterowania aplikacjami są obsługiwane tylko w trybie inspekcji.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak włączyć funkcje adaptacyjnego sterowania aplikacjami?

Adaptacyjne kontrole aplikacji ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach maszyn. Ta funkcja jest dostępna zarówno na platformie Azure, jak i w systemie Windows (wszystkie wersje, klasyczne lub Azure Resource Manager) i na maszynach z systemem Linux. Wykonaj następujące kroki, aby skonfigurować listę dozwolonych aplikacji:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

1. W okienku po lewej stronie wybierz pozycję **Funkcje adaptacyjnego sterowania aplikacjami** w obszarze **Zaawansowana ochrona w chmurze**.

    [![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

Zostanie wyświetlona strona **Adaptacyjne kontrolki aplikacji**.

![funkcje sterowania](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Sekcja **Grupy maszyn wirtualnych** zawiera trzy karty:

* **Skonfigurowane**: lista grup zawierających maszyny wirtualne, dla których skonfigurowano sterowanie aplikacjami.
* **Zalecane**: lista grup, dla których zaleca się sterowanie aplikacjami. Usługa Security Center przy użyciu uczenia maszynowego identyfikuje maszyny wirtualne odpowiednie do zastosowania funkcji sterowania aplikacjami na podstawie tego, czy na tych maszynach są spójnie uruchamiane te same aplikacje.
* **Brak zaleceń**: lista grup zawierających maszyny wirtualne bez żadnych zaleceń dotyczących sterowania aplikacjami. Na przykład maszyny wirtualne, na których aplikacje stale się zmieniają i nie osiągają stanu stabilnego.

> [!NOTE]
> Usługa Security Center korzysta z własnościowego algorytmu klastrowania w celu tworzenia grup maszyn wirtualnych, jednocześnie zapewniając, że podobne maszyny wirtualne uzyskają optymalne zalecane zasady sterowania aplikacjami.
>
>

### <a name="configure-a-new-application-control-policy"></a>Konfigurowanie nowych zasad sterowania aplikacjami

1. Wybierz kartę **zalecane** , aby uzyskać listę grup z zaleceniami dotyczącymi kontroli aplikacji:

   ![Zalecane](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Lista zawiera następujące pozycje:

   - **Nazwa grupy**: Nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie
   - **Stan**: stan zaleceń
   - **Ważność**: poziom ważności zaleceń

2. Kliknij grupę, aby otworzyć opcję **Utwórz reguły sterowania aplikacjami** .

   [![Reguły sterowania aplikacjami](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. Na stronie **Wybieranie maszyn wirtualnych**Przejrzyj listę zalecanych maszyn wirtualnych i usuń zaznaczenie wszystkich elementów, których nie chcesz stosować do programu. Następnie zostaną wyświetlone dwie listy:

   - **Zalecane aplikacje**: Lista aplikacji, które są często używane na maszynach wirtualnych w tej grupie, i zaleca się ich uruchomienie.
   - **Więcej aplikacji**: Lista aplikacji, które rzadziej często znajdują się na maszynach wirtualnych w tej grupie lub które są znane jako wykorzystania (Zobacz więcej poniżej) i zalecane do przeglądu.

4. Dokonaj przeglądu aplikacji na każdej liście i wyczyść zaznaczenie pól wyboru tych, które nie mają być stosowane. Każda lista zawiera takie elementy, jak:

   - **Nazwa**: informacje o certyfikacie lub pełna ścieżka aplikacji
   - **TYPY PLIKÓW**: typ pliku aplikacji. Może to być plik EXE, skrypt, MSI lub dowolną permutację tych typów.
   - Możliwe do **wykorzystania**: ikona ostrzeżenia wskazuje, czy dana aplikacja może być używana przez osobę atakującą do obejścia listy dozwolonych aplikacji. Zaleca się dokonanie przeglądu tych aplikacji przed ich zatwierdzeniem.
   - **UŻYTKOWNICY**: lista użytkowników, którym zezwala się na uruchomienie aplikacji

5. Po wybraniu opcji wybierz przycisk **Utwórz**. <br>
   Po wybraniu opcji Utwórz program Azure Security Center automatycznie tworzy odpowiednie reguły na podstawie wbudowanej aplikacji Zezwalaj na listę dostępności na serwerach systemu Windows (AppLocker).

> [!NOTE]
> - Usługa Security Center używa danych z co najmniej dwóch tygodni w celu utworzenia planu bazowego i przygotowania unikatowych zaleceń dla grup maszyn wirtualnych. Nowi klienci usługi Security Center w warstwie Standardowa powinni spodziewać się, że najpierw ich grupy maszyn wirtualnych pojawią się na karcie *Brak zaleceń*.
> - Funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center nie obsługują maszyn wirtualnych, dla których zasady funkcji AppLocker zostały już włączone za pomocą obiektu zasad grupy lub lokalnych zasad zabezpieczeń.
> -  Zgodnie z najlepszymi rozwiązaniami w zakresie bezpieczeństwa, Security Center będzie zawsze próbować utworzyć regułę wydawcy dla aplikacji, które są wybrane do dozwolonych, i tylko wtedy, gdy aplikacja nie ma informacji o wydawcy (bez podpisu), zostanie utworzona reguła ścieżki dla pełnej ścieżki konkretnej aplikacji.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edytowanie i monitorowanie grupy, dla której skonfigurowano sterowanie aplikacjami

1. Aby edytować i monitorować grupę, dla której skonfigurowano zasady listy dozwolonych aplikacji, Wróć do strony **adaptacyjne kontrolki aplikacji** i wybierz pozycję **skonfigurowane** w obszarze **grupy maszyn wirtualnych**:

   ![Grupy](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Lista zawiera następujące pozycje:

   - **Nazwa grupy**: Nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie
   - **Tryb**: w trybie inspekcji będą rejestrowane próby uruchomienia aplikacji, które nie znajdują się na liście dozwolonych. Wymuś, że aplikacje nie będą działać, chyba że znajdują się na liście dozwolonych
   - **Alerty**: wszystkie bieżące naruszenia

2. Kliknij grupę, aby wprowadzić zmiany na stronie **Edytowanie zasad kontroli aplikacji** .

   ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. W obszarze **Tryb ochrony** do wyboru są następujące opcje:

   - **Inspekcja**: w tym trybie rozwiązanie sterowania aplikacjami nie będzie wymuszać reguł, a jedynie prowadzić inspekcję działań na chronionych maszynach wirtualnych. Jest to zalecane w scenariuszach, w których trzeba najpierw przyjrzeć się ogólnym zachowaniom, zanim zablokuje się uruchamianie aplikacji na docelowej maszynie wirtualnej.
   - **Wymuszanie**: w tym trybie rozwiązanie sterowania aplikacjami będzie wymuszać reguły, a aplikacje, które nie powinny być uruchamiane, będą blokowane.

   > [!NOTE]
   > -  Tryb **wymuszania** ochrony jest wyłączony do momentu dalszej uwagi.
   > - Jak wcześniej wspomniano, domyślnie nowe zasady sterowania aplikacjami są zawsze skonfigurowane w trybie *inspekcji*. 
   >

4. W obszarze **rozszerzenie zasad**Dodaj dowolną ścieżkę aplikacji, którą chcesz zezwolić. Po dodaniu tych ścieżek Security Center aktualizuje zasady listy dozwolonych aplikacji na maszynach wirtualnych w ramach wybranej grupy maszyn wirtualnych i tworzy odpowiednie reguły dla tych aplikacji, a także reguły, które już istnieją.

5. Zapoznaj się z bieżącymi naruszeniami wymienionymi w sekcji **Ostatnie alerty** . Kliknij każdy wiersz, który ma zostać przekierowany do strony **alerty** w Azure Security Center i Wyświetl wszystkie alerty wykryte przez Azure Security Center na skojarzonych maszynach wirtualnych.
   - **Alerty**: wszystkie naruszenia, które zostały zarejestrowane.
   - **Nie. z maszyn**wirtualnych: liczba maszyn wirtualnych z tym typem alertu.

6. W obszarze reguły **listy dozwolonych wydawcy**, **ścieżki listy dozwolonych reguły**i **reguły listy dozwolonych skrótu** można zobaczyć, które reguły listy dozwolonych aplikacji są obecnie skonfigurowane na maszynach wirtualnych w grupie, zgodnie z typem kolekcji reguł. Dla każdej reguły można zobaczyć:

   - **Reguła**: konkretne parametry, zgodnie z którymi aplikacja jest kontrolowana przez funkcję AppLocker, aby określić, czy aplikacja może być uruchamiana.
   - **Typ pliku**: typy plików objęte określoną regułą. Może to być dowolny z następujących elementów: EXE, Script, MSI lub dowolne permutacje tego typu plików.
   - **Użytkownicy**: nazwa lub liczba użytkowników, którzy mogą uruchamiać aplikację objętą regułą listy dozwolonych aplikacji.

   ![Reguły umieszczania na liście dozwolonych](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Kliknij trzy kropki na końcu każdego wiersza, jeśli chcesz usunąć konkretną regułę lub edytować uprawnionych użytkowników.

8. Po wprowadzeniu zmian w zasadach **adaptacyjnych kontroli aplikacji** kliknij przycisk **Zapisz**.

### <a name="not-recommended-list"></a>Lista bez zaleceń

Security Center zaleca się tylko zasady listy dozwolonych aplikacji dla maszyn wirtualnych z stabilnym zestawem aplikacji. Zalecenia nie są tworzone w przypadku ciągłych zmian aplikacji na skojarzonych maszynach wirtualnych.

![Zalecenie](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Lista zawiera:
- **Nazwa grupy**: Nazwa subskrypcji i grupy
- **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie

Azure Security Center umożliwia zdefiniowanie zasad listy dozwolonych aplikacji dla niezalecanych grup maszyn wirtualnych. Postępuj zgodnie z tymi samymi zasadami, które zostały wcześniej opisane, aby skonfigurować zasady listy dozwolonych aplikacji również dla tych grup.

## <a name="move-a-vm-from-one-group-to-another"></a>Przenoszenie maszyny wirtualnej z jednej grupy do innej

 Po przeniesieniu maszyny wirtualnej z jednej grupy do drugiej zasady kontroli aplikacji zastosowane do niej zmieniają ustawienia grupy, do której została przeniesiona. Możesz również przenieść maszynę wirtualną ze skonfigurowanej grupy do grupy, która nie jest skonfigurowana, co spowoduje usunięcie wszystkich zasad kontroli aplikacji, które zostały wcześniej zastosowane do maszyny wirtualnej.

 1. Na stronie **adaptacyjne kontrolki aplikacji** na karcie **skonfigurowany** kliknij grupę, do której należy obecnie przenieść maszynę wirtualną.
1. Kliknij pozycję **skonfigurowane maszyny wirtualne i komputery**.
1. Kliknij trzy kropki w wierszu maszyny wirtualnej, aby przenieść, a następnie kliknij przycisk **Przenieś**. Zostanie otwarte okno **Przenieś komputer do innej grupy** .

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Wybierz grupę, do której chcesz przenieść maszynę wirtualną, a następnie kliknij pozycję **Przenieś komputer**, a następnie kliknij przycisk **Zapisz**.

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Pamiętaj, aby kliknąć przycisk **Zapisz** po kliknięciu pozycji **Przenieś komputer**. Jeśli nie klikniesz przycisku **Zapisz**, komputer nie zostanie przeniesiony.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób korzystania z adaptacyjnej kontroli aplikacji w Azure Security Center, aby dozwolonych aplikacje działające na platformie Azure i maszynach wirtualnych spoza platformy Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Azure Security Center Przewodnik rozwiązywania problemów](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
