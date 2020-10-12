---
title: Migrowanie Azure Service Bus przestrzenie nazw — Standard do warstwy Premium
description: Przewodnik umożliwiający migrację istniejących Azure Service Bus standardowych przestrzeni nazw do warstwy Premium
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 1ed09a077f086390c658e6650171c552b361008d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85340739"
---
# <a name="migrate-existing-azure-service-bus-standard-namespaces-to-the-premium-tier"></a>Migrowanie istniejących Azure Service Bus standardowych przestrzeni nazw do warstwy Premium

Wcześniej Azure Service Bus oferowane przestrzenie nazw tylko w warstwie Standardowa. Przestrzenie nazw to konfiguracje wielodostępne, które są zoptymalizowane pod kątem małych przepływności i środowisk deweloperskich. Warstwa Premium oferuje dedykowane zasoby na przestrzeń nazw na potrzeby przewidywalnego opóźnienia i zwiększonej przepływności przy stałej cenie. Warstwa Premium jest zoptymalizowana pod kątem wysokiej przepływności i środowisk produkcyjnych, które wymagają dodatkowych funkcji w przedsiębiorstwie.

W tym artykule opisano sposób migrowania istniejących przestrzeni nazw warstwy standardowej do warstwy Premium.  

>[!WARNING]
> Migracja jest przeznaczona dla Service Bus standardowych przestrzeni nazw, które mają zostać uaktualnione do warstwy Premium. Narzędzie migracji nie obsługuje obniżania poziomu.

Niektóre punkty do zanotowania:

- Ta migracja ma miejsce, co oznacza, że istniejące aplikacje nadawcy i odbiorcy **nie wymagają żadnych zmian w kodzie ani konfiguracji**. Istniejące parametry połączenia będą automatycznie wskazywały na nową przestrzeń nazw w warstwie Premium.
- Przestrzeń nazw **Premium** nie powinna mieć **żadnych jednostek** , aby migracja zakończyła się pomyślnie.
- Wszystkie **jednostki** w standardowej przestrzeni nazw są **kopiowane** do przestrzeni nazw Premium podczas procesu migracji.
- Migracja obsługuje jednostki **1 000 na jednostkę obsługi komunikatów** w warstwie Premium. Aby określić liczbę potrzebnych jednostek obsługi komunikatów, Zacznij od liczby jednostek, które znajdują się w bieżącej przestrzeni nazw w warstwie Standardowa.
- Nie można bezpośrednio migrować z **warstwy Podstawowa** do **warstwy Premium**, ale możesz to zrobić pośrednio, przechodząc od wersji podstawowej do standardowej, a następnie ze standardu do Premium w następnym kroku.

## <a name="migration-steps"></a>Kroki migracji

Niektóre warunki są skojarzone z procesem migracji. Zapoznaj się z poniższymi krokami, aby zmniejszyć prawdopodobieństwo wystąpienia błędów. W tych krokach opisano proces migracji, a szczegóły krok po kroku znajdują się w poniższych sekcjach.

1. Utwórz nową przestrzeń nazw Premium.
1. Parowanie przestrzeni nazw w warstwie Standardowa i Premium.
1. Synchronizuj (kopiowanie) jednostki od normy do przestrzeni nazw w warstwie Premium.
1. Zatwierdź migrację.
1. Opróżniaj jednostki w standardowej przestrzeni nazw przy użyciu nazwy po migracji dla przestrzeni nazw.
1. Usuń standardową przestrzeń nazw.

>[!IMPORTANT]
> Po zatwierdzeniu migracji uzyskaj dostęp do starej, standardowej przestrzeni nazw i Opróżnij kolejki i subskrypcje. Po opróżnieniu wiadomości mogą one być wysyłane do nowej przestrzeni nazw Premium, która ma zostać przetworzona przez aplikacje odbiornika. Po opróżnieniu kolejek i subskrypcji zalecamy usunięcie starej standardowej przestrzeni nazw.

### <a name="migrate-by-using-the-azure-cli-or-powershell"></a>Migrowanie przy użyciu interfejsu wiersza polecenia platformy Azure lub programu PowerShell

Aby przeprowadzić migrację standardowej przestrzeni nazw Service Bus do warstwy Premium przy użyciu interfejsu wiersza polecenia platformy Azure lub narzędzia PowerShell, wykonaj następujące kroki.

1. Utwórz nową przestrzeń nazw w warstwie Premium Service Bus. Możesz odwoływać się do [szablonów Azure Resource Manager](service-bus-resource-manager-namespace.md) lub [użyć Azure Portal](service-bus-create-namespace-portal.md). Upewnij się, że wybrano opcję **Premium** dla parametru **serviceBusSku** .

1. Ustaw następujące zmienne środowiskowe, aby uprościć polecenia migracji.

   ```
   resourceGroup = <resource group for the standard namespace>
   standardNamespace = <standard namespace to migrate>
   premiumNamespaceArmId = <Azure Resource Manager ID of the premium namespace to migrate to>
   postMigrationDnsName = <post migration DNS name entry to access the standard namespace>
   ```

    >[!IMPORTANT]
    > Alias/nazwa (post_migration_dns_name) po migracji zostanie użyta w celu uzyskania dostępu do starej standardowej migracji przestrzeni nazw. Użyj tego, aby opróżnić kolejki i subskrypcje, a następnie usuń przestrzeń nazw.

1. Parowanie przestrzeni nazw warstwy Standardowa i Premium oraz Rozpoczynanie synchronizacji przy użyciu następującego polecenia:

    ```azurecli-interactive
    az servicebus migration start --resource-group $resourceGroup --name $standardNamespace --target-namespace $premiumNamespaceArmId --post-migration-name $postMigrationDnsName
    ```

1. Sprawdź stan migracji przy użyciu następującego polecenia:

    ```azurecli-interactive
    az servicebus migration show --resource-group $resourceGroup --name $standardNamespace
    ```

    Migracja jest uważana za ukończoną, gdy widoczne są następujące wartości:

    * MigrationState = "Active"
    * pendingReplicationsOperationsCount = 0
    * provisioningState = "powodzenie"

    To polecenie wyświetla również konfigurację migracji. Sprawdź, czy wartości są ustawione prawidłowo. Sprawdź również przestrzeń nazw Premium w portalu, aby upewnić się, że wszystkie kolejki i tematy zostały utworzone i że są one zgodne z tym, co istniało w standardowej przestrzeni nazw.

1. Zatwierdź migrację, wykonując następujące polecenie:

   ```azurecli-interactive
   az servicebus migration complete --resource-group $resourceGroup --name $standardNamespace
   ```

### <a name="migrate-by-using-the-azure-portal"></a>Migrowanie przy użyciu Azure Portal

Migracja przy użyciu Azure Portal ma ten sam przepływ logiczny co migracja przy użyciu poleceń. Wykonaj następujące kroki, aby przeprowadzić migrację przy użyciu Azure Portal.

1. W menu **nawigacji** w okienku po lewej stronie wybierz pozycję **Migruj do warstwy Premium**. Kliknij przycisk **Rozpocznij** , aby przejść do następnej strony.
    ![Strona docelowa migracji][]

1. Ukończ **instalację**.
   ![Konfiguracja przestrzeni nazw][]
   1. Utwórz i przypisz przestrzeń nazw Premium, aby przeprowadzić migrację istniejącej standardowej przestrzeni nazw do programu.
        ![Konfiguracja przestrzeni nazw — tworzenie przestrzeni nazw Premium][]
   1. Wybierz **nazwę po migracji**. Ta nazwa będzie używana w celu uzyskania dostępu do standardowej przestrzeni nazw po zakończeniu migracji.
        ![Konfiguracja przestrzeni nazw — wybierz nazwę migracji][]
   1. Wybierz pozycję **"dalej"** , aby kontynuować.
1. Synchronizuj jednostki między przestrzeniami nazw Standard i Premium.
    ![Konfiguracja przestrzeni nazw — jednostki synchronizacji — Rozpocznij][]

   1. Wybierz pozycję **Rozpocznij synchronizację** , aby rozpocząć synchronizowanie jednostek.
   1. W oknie dialogowym wybierz pozycję **tak** , aby potwierdzić i uruchomić synchronizację.
   1. Poczekaj, aż synchronizacja zostanie zakończona. Stan jest dostępny na pasku stanu.
        ![Konfiguracja przestrzeni nazw — jednostki synchronizacji — postęp][]
        >[!IMPORTANT]
        > Jeśli zachodzi potrzeba przerwania migracji z dowolnego powodu, zapoznaj się z przepływem przerwań w sekcji często zadawanych pytań tego dokumentu.
   1. Po zakończeniu synchronizacji wybierz pozycję **dalej** w dolnej części strony.

1. Przejrzyj zmiany na stronie Podsumowanie. Wybierz pozycję **pełna migracja** , aby przełączyć obszary nazw i ukończyć migrację.
    ![Przełącz menu przełączania przestrzeni nazw][]  
    Strona potwierdzenia zostanie wyświetlona po zakończeniu migracji.
    ![Przełącz przestrzeń nazw — powodzenie][]

## <a name="caveats"></a>Zastrzeżenia

Niektóre funkcje udostępniane przez usługę Azure Service Bus Standard nie są obsługiwane przez Azure Service Bus warstwy Premium. Są one zaprojektowane, ponieważ warstwa Premium oferuje dedykowane zasoby umożliwiające przewidywalną przepływność i opóźnienia.

Poniżej znajduje się lista funkcji nieobsługiwanych przez usługę Premium i ich środki zaradcze.

### <a name="express-entities"></a>Jednostki ekspresowe

   Jednostki ekspresowe, które nie zatwierdzają żadnych danych komunikatu do magazynu, nie są obsługiwane w wersji Premium. Dedykowane zasoby zapewniają znaczną poprawę przepływności przy jednoczesnym zapewnieniu, że dane są utrwalane, zgodnie z oczekiwaniami z dowolnego systemu obsługi komunikatów w przedsiębiorstwie.

   Podczas migracji każda z jednostek ekspresowych w standardowej przestrzeni nazw zostanie utworzona w przestrzeni nazw Premium jako jednostka niebędąca ekspresem.

   W przypadku korzystania z szablonów Azure Resource Manager (ARM) Upewnij się, że flaga "enableExpress" została usunięta z konfiguracji wdrożenia, aby automatyczne przepływy pracy były wykonywane bez błędów.

### <a name="partitioned-entities"></a>Partycjonowane jednostki

   Partycjonowane jednostki były obsługiwane w warstwie Standardowa w celu zapewnienia lepszej dostępności w konfiguracji wielodostępnej. Po udostępnieniu dedykowanych zasobów dla przestrzeni nazw w warstwie Premium nie jest to już potrzebne.

   Podczas migracji każda jednostka partycjonowana w standardowej przestrzeni nazw jest tworzona w przestrzeni nazw Premium jako jednostka niepartycjonowana.

   Jeśli szablon ARM ustawia "enablePartitioning" na wartość "true" dla określonej kolejki lub tematu, to zostanie on zignorowany przez brokera.

## <a name="faqs"></a>Często zadawane pytania

### <a name="what-happens-when-the-migration-is-committed"></a>Co się stanie, gdy migracja zostanie zatwierdzona?

Po zatwierdzeniu migracji parametry połączenia wskazujące standardową przestrzeń nazw będą wskazywały na przestrzeń nazw w warstwie Premium.

Aplikacje nadawcy i odbiorcy będą rozłączane ze standardową przestrzenią nazw i automatycznie łączą się z przestrzenią nazw w warstwie Premium.

### <a name="what-do-i-do-after-the-standard-to-premium-migration-is-complete"></a>Co mam zrobić po zakończeniu migracji z wersji Standard do Premium?

Migracja standardowa na Premium zapewnia, że metadane jednostki, takie jak tematy, subskrypcje i filtry, są kopiowane z standardowej przestrzeni nazw do przestrzeni nazw w warstwie Premium. Dane komunikatu przekazane do standardowej przestrzeni nazw nie są kopiowane z standardowej przestrzeni nazw do przestrzeni nazw Premium.

Standardowa przestrzeń nazw może zawierać komunikaty, które zostały wysłane i zatwierdzone podczas migracji. Ręcznie Opróżniaj te komunikaty ze standardowego obszaru nazw i ręcznie Wyślij je do przestrzeni nazw Premium. Aby ręcznie opróżnić komunikaty, użyj aplikacji konsolowej lub skryptu, który opróżnia standardowe jednostki przestrzeni nazw za pomocą nazwy DNS po migracji określonej w poleceniach migracji. Wyślij te komunikaty do przestrzeni nazw Premium, aby mogły być przetwarzane przez odbiorniki.

Po opróżnieniu komunikatów Usuń standardową przestrzeń nazw.

>[!IMPORTANT]
> Po opróżnieniu komunikatów ze standardowego obszaru nazw usuń standardową przestrzeń nazw. Jest to ważne, ponieważ parametry połączenia, które początkowo odwołują się do standardowej przestrzeni nazw, teraz odwołują się do przestrzeni nazw w warstwie Premium. Nie będzie już potrzebna standardowa przestrzeń nazw. Usunięcie przemigrowanej standardowej przestrzeni nazw pomaga zmniejszyć liczbę dalszych pomyłek.

### <a name="how-much-downtime-do-i-expect"></a>Jak dużo przestojów oczekuje?

Proces migracji ma na celu zmniejszenie oczekiwanych przestojów aplikacji. Przestoje są skracane przy użyciu parametrów połączenia, które są używane przez nadawcę i aplikacje odbiornika do wskazywania nowej przestrzeni nazw w warstwie Premium.

Przestój, który jest napotykany przez aplikację, jest ograniczony do czasu potrzebnego do zaktualizowania wpisu DNS, aby wskazywał przestrzeń nazw Premium. Przestój jest około 5 minut.

### <a name="do-i-have-to-make-any-configuration-changes-while-doing-the-migration"></a>Czy muszę wprowadzić jakiekolwiek zmiany w konfiguracji podczas migracji?

Nie, nie trzeba zmieniać kodu lub konfiguracji, aby przeprowadzić migrację. Parametry połączenia używane przez nadawcę i aplikacje odbiornika do uzyskiwania dostępu do standardowej przestrzeni nazw są automatycznie mapowane do działania jako alias dla przestrzeni nazw w warstwie Premium.

### <a name="what-happens-when-i-abort-the-migration"></a>Co się stanie w przypadku przerwania migracji?

Migrację można przerwać przy użyciu `Abort` polecenia lub za pomocą Azure Portal.

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```azurecli-interactive
az servicebus migration abort --resource-group $resourceGroup --name $standardNamespace
```

#### <a name="azure-portal"></a>Azure Portal

![Przerwij przepływ — Przerwij ][]
 ![ przepływ przerwania synchronizacji — ukończono przerywanie][]

Gdy proces migracji zostanie przerwany, przerywa proces kopiowania jednostek (tematów, subskrypcji i filtrów) z warstwy Standardowa do przestrzeni nazw Premium i przerywa parowanie.

Parametry połączenia nie są aktualizowane, aby wskazywały przestrzeń nazw w warstwie Premium. Istniejące aplikacje będą nadal działały tak, jak przed rozpoczęciem migracji.

Nie usuwa jednak jednostek z przestrzeni nazw Premium ani nie usuwa przestrzeni nazw Premium. Usuń jednostki ręcznie, jeśli zdecydujesz się nie przenieść dalej do migracji.

>[!IMPORTANT]
> Jeśli zdecydujesz się na przerwanie migracji, Usuń przestrzeń nazw Premium, która została zainicjowana dla migracji, aby nie naliczać opłat za zasoby.

#### <a name="i-dont-want-to-have-to-drain-the-messages-what-do-i-do"></a>Nie chcę, aby opróżniał komunikaty. Co mam zrobić?

Mogą istnieć komunikaty wysyłane przez aplikacje nadawców i zatwierdzone do magazynu w standardowej przestrzeni nazw podczas migracji, a dopiero po zatwierdzeniu migracji.

Podczas migracji rzeczywiste dane komunikatów/ładunek nie są kopiowane z warstwy Standardowa do przestrzeni nazw Premium. Komunikaty muszą być opróżniane ręcznie, a następnie wysyłane do przestrzeni nazw w warstwie Premium.

Jeśli jednak można przeprowadzić migrację w oknie planowanej konserwacji/dla gospodarstw domowych i nie chcesz ręcznie opróżniać i wysyłać komunikatów, wykonaj następujące kroki:

1. Zatrzymaj aplikacje nadawcy. Aplikacje odbiornika będą przetwarzać komunikaty znajdujące się obecnie w standardowej przestrzeni nazw i opróżniają kolejkę.
1. Gdy kolejki i subskrypcje w standardowej przestrzeni nazw są puste, wykonaj procedurę opisaną wcześniej w celu przeprowadzenie migracji z warstwy Standardowa do przestrzeni nazw Premium.
1. Po zakończeniu migracji możesz ponownie uruchomić aplikacje nadawcy.
1. Nadawcy i odbiorcy będą teraz automatycznie łączyć się z przestrzenią nazw Premium.

    >[!NOTE]
    > Nie jest konieczne zatrzymywanie aplikacji odbiornika do migracji.
    >
    > Po zakończeniu migracji aplikacje odbiornika rozłączą się ze standardową przestrzenią nazw i automatycznie łączą się z przestrzenią nazw Premium.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [różnicach między obsługą komunikatów w warstwach Standardowa i Premium](./service-bus-premium-messaging.md).
* Dowiedz się więcej na temat [aspektów odzyskiwania wysokiej dostępności i Geo-Disaster dla Service Bus Premium](service-bus-outages-disasters.md#protecting-against-outages-and-disasters---service-bus-premium).

[Strona docelowa migracji]: ./media/service-bus-standard-premium-migration/1.png
[Konfiguracja przestrzeni nazw]: ./media/service-bus-standard-premium-migration/2.png
[Konfiguracja przestrzeni nazw — tworzenie przestrzeni nazw Premium]: ./media/service-bus-standard-premium-migration/3.png
[Konfiguracja przestrzeni nazw — wybierz nazwę migracji]: ./media/service-bus-standard-premium-migration/4.png
[Konfiguracja przestrzeni nazw — jednostki synchronizacji — Rozpocznij]: ./media/service-bus-standard-premium-migration/5.png
[Konfiguracja przestrzeni nazw — jednostki synchronizacji — postęp]: ./media/service-bus-standard-premium-migration/8.png
[Przełącz menu przełączania przestrzeni nazw]: ./media/service-bus-standard-premium-migration/9.png
[Przełącz przestrzeń nazw — powodzenie]: ./media/service-bus-standard-premium-migration/12.png

[Przerwij przepływ — Przerwij synchronizację]: ./media/service-bus-standard-premium-migration/abort1.png
[Przerwanie przepływu — ukończono przerywanie]: ./media/service-bus-standard-premium-migration/abort3.png
