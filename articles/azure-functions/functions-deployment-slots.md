---
title: Azure Functions miejsc wdrożenia
description: Dowiedz się, jak tworzyć i używać miejsc wdrożenia przy użyciu Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 73282145abd8bfe804b47fda3bf5f12dc691ff3a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551360"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions miejsc wdrożenia

Azure Functions miejsca wdrożenia pozwalają aplikacji funkcji uruchamiać różne wystąpienia o nazwie "gniazda". Gniazda są różnymi środowiskami udostępnianymi za pośrednictwem publicznie dostępnego punktu końcowego. Jedno wystąpienie aplikacji jest zawsze mapowane na miejsce produkcyjne i można zamienić wystąpienia przypisane do gniazda na żądanie. Aplikacje funkcji działające w ramach planu usługi aplikacji mogą mieć wiele gniazd, a w ramach planu zużycia dozwolone jest tylko jedno gniazdo.

Poniżej przedstawiono sposób, w jaki funkcje mają wpływ na zamienienie gniazd:

- Przekierowywanie ruchu jest bezproblemowe; żadne żądania nie są usuwane ze względu na zamianę.
- Jeśli funkcja jest uruchomiona w trakcie wymiany, wykonywanie jest kontynuowane, a następne wyzwalacze są kierowane do zamienionego wystąpienia aplikacji.

## <a name="why-use-slots"></a>Dlaczego warto korzystać z gniazd?

Istnieją różne zalety korzystania z miejsc wdrożenia. W poniższych scenariuszach opisano typowe zastosowania dla miejsc:

- **Różne środowiska do różnych celów**: korzystanie z różnych gniazd umożliwia odróżnienie wystąpień aplikacji przed zamianą na miejsce produkcyjne lub w miejscu przejściowym.
- **Przedgrzane**: wdrożenie w gnieździe zamiast bezpośrednio do środowiska produkcyjnego umożliwia rozgrzanie aplikacji przed przeprowadzeniem jej na żywo. Ponadto użycie gniazd zmniejsza czas oczekiwania dla obciążeń wyzwalanych przez protokół HTTP. Wystąpienia są rozgrzane przed wdrożeniem, co zmniejsza zimny start dla nowo wdrożonych funkcji.
- **Łatwe powrotu**: po wymianie w środowisku produkcyjnym gniazdo z wcześniej przygotowaną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany wprowadzone w gnieździe produkcyjnym nie są oczekiwane, możesz natychmiast cofnąć zamianę, aby przywrócić "ostatnie znane dobre wystąpienie".

## <a name="swap-operations"></a>Operacje zamiany

W trakcie wymiany jedno gniazdo jest uznawane za źródło i inne miejsce docelowe. Miejsce źródłowe ma wystąpienie aplikacji stosowanej w gnieździe docelowym. Poniższe kroki zapewniają, że w gnieździe docelowym nie występują przestoje podczas wymiany:

1. **Zastosuj ustawienia:** Ustawienia z gniazda docelowego są stosowane do wszystkich wystąpień miejsca źródłowego. Na przykład ustawienia produkcyjne są stosowane do wystąpienia przejściowego. Zastosowane ustawienia obejmują następujące kategorie:
    - Ustawienia aplikacji i parametry połączenia [specyficzne dla gniazda](#manage-settings) (jeśli dotyczy)
    - Ustawienia [ciągłego wdrażania](../app-service/deploy-continuous-deployment.md) (jeśli są włączone)
    - [App Service ustawienia uwierzytelniania](../app-service/overview-authentication-authorization.md) (jeśli są włączone)

1. **Poczekaj na ponowne uruchomienie i dostępność:** Zamiana czeka na każde wystąpienie w gnieździe źródłowym, aby zakończyć jego ponowne uruchomienie i było dostępne dla żądań. Jeśli jakiekolwiek wystąpienie nie powiedzie się ponownie, operacja wymiany przywraca wszystkie zmiany w gnieździe źródłowym i zatrzymuje operację.

1. **Routing aktualizacji:** Jeśli wszystkie wystąpienia w gnieździe źródłowym zostaną rozgrzane pomyślnie, te dwa gniazda ukończą zamianę, przełączając reguły routingu. Po wykonaniu tego kroku miejsce docelowe (na przykład miejsce produkcyjne) zawiera aplikację, która została wcześniej poddana w gnieździe źródłowym.

1. **Powtórz operację:** Teraz, gdy gniazdo źródłowe zawiera wcześniej aplikację do wstępnego zamiany w gnieździe docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia dla miejsca źródłowego.

Należy pamiętać o następujących kwestiach:

- W dowolnym momencie operacji wymiany inicjowanie zamienionych aplikacji odbywa się w miejscu źródłowym. Gniazdo docelowe pozostaje w trybie online, gdy gniazdo źródłowe jest przygotowana, niezależnie od tego, czy swap zakończy się powodzeniem, czy nie.

- Aby wymienić miejsce przejściowe z miejscem produkcyjnym, upewnij się, że miejsce produkcyjne jest *zawsze* miejscem docelowym. W ten sposób operacja zamiany nie ma wpływu na aplikację produkcyjną.

- Ustawienia związane ze źródłami i powiązaniami zdarzeń muszą zostać skonfigurowane jako [Ustawienia miejsca wdrożenia](#manage-settings) *przed rozpoczęciem wymiany*. Oznaczanie ich jako "Sticky" przed czasem gwarantuje, że zdarzenia i wyjścia są kierowane do właściwego wystąpienia.

## <a name="manage-settings"></a>Zarządzanie ustawieniami

Niektóre ustawienia konfiguracji są specyficzne dla gniazda. Poniższa lista zawiera szczegółowe informacje o zmianach ustawień w przypadku wymiany miejsc, które pozostają takie same.

**Ustawienia specyficzne dla gniazda**:

* Publikowanie punktów końcowych
* Niestandardowe nazwy domen
* Certyfikaty inne niż publiczne i ustawienia protokołu TLS/SSL
* Ustawienia skalowania
* Harmonogramy zadań WebJob
* Ograniczenia adresów IP
* Zawsze włączone
* Ustawienia diagnostyczne
* Współużytkowanie zasobów między źródłami (CORS)

**Ustawienia specyficzne dla gniazda**:

* Ustawienia ogólne, takie jak wersja platformy, 32/64-bitowa, gniazda sieci Web
* Ustawienia aplikacji (można skonfigurować do nalepki do miejsca)
* Parametry połączenia (można je skonfigurować do nawiązania połączenia z gniazdem)
* Mapowania obsługi
* Certyfikaty publiczne
* Zawartość zadań WebJob
* Połączenia hybrydowe *
* Integracja sieci wirtualnej *
* Punkty końcowe usługi *
* Azure Content Delivery Network *

Funkcje oznaczone gwiazdką (*) są planowane jako niezamienione. 

> [!NOTE]
> Niektóre ustawienia aplikacji, które mają zastosowanie do niewymienianych ustawień, również nie są zamieniane. Na przykład, ponieważ ustawienia diagnostyczne nie są wymieniane, powiązane ustawienia aplikacji, takie jak `WEBSITE_HTTPLOGGING_RETENTION_DAYS` i, `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` również nie są wymieniane, nawet jeśli nie są wyświetlane jako ustawienia gniazda.
>

### <a name="create-a-deployment-setting"></a>Utwórz ustawienie wdrożenia

Można oznaczyć ustawienia jako ustawienia wdrożenia, co sprawia, że "Stick". Ustawienie programu Sticky Notes nie zamienia wystąpienia aplikacji.

W przypadku utworzenia ustawienia wdrożenia w jednym gnieździe upewnij się, że to ustawienie ma wartość unikatową w każdym innym miejscu, które jest uwzględnione w wymianie. W ten sposób, gdy wartość ustawienia nie ulegnie zmianie, nazwy ustawień pozostają spójne między gniazdami. Ta spójność nazw gwarantuje, że kod nie próbuje uzyskać dostępu do ustawienia zdefiniowanego w jednym gnieździe, ale innym.

Wykonaj następujące kroki, aby utworzyć ustawienie wdrożenia:

1. Przejdź do **miejsc wdrożenia** w aplikacji funkcji, a następnie wybierz nazwę gniazda.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź gniazda w Azure Portal." border="true":::

1. Wybierz pozycję **Konfiguracja**, a następnie wybierz nazwę ustawienia, które chcesz nawiązać z bieżącym miejscem.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Skonfiguruj ustawienie aplikacji dla gniazda w Azure Portal." border="true":::

1. Wybierz **ustawienie miejsce wdrożenia**, a następnie wybierz przycisk **OK**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Skonfiguruj ustawienie miejsca wdrożenia." border="true":::

1. Gdy sekcja ustawienia znika, wybierz pozycję **Zapisz** , aby zachować zmiany

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Zapisz ustawienie miejsca wdrożenia." border="true":::

## <a name="deployment"></a>Wdrożenie

Gniazda są puste podczas tworzenia gniazda. Możesz użyć dowolnej z [obsługiwanych technologii wdrażania](./functions-deployment-technologies.md) , aby wdrożyć aplikację w gnieździe.

## <a name="scaling"></a>Skalowanie

Wszystkie gniazda są skalowane do tej samej liczby procesów roboczych co miejsce produkcyjne.

- W przypadku planów zużycia gniazdo jest skalowane w miarę skalowania aplikacji funkcji.
- W przypadku planów App Service aplikacja jest skalowana do stałej liczby procesów roboczych. Gniazda są uruchamiane na tej samej liczbie procesów roboczych co plan aplikacji.

## <a name="add-a-slot"></a>Dodawanie miejsca

Możesz dodać gniazdo za pośrednictwem [interfejsu wiersza polecenia](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create) lub portalu. Poniższe kroki pokazują, jak utworzyć nowe miejsce w portalu:

1. Przejdź do aplikacji funkcji.

1. Wybierz pozycję miejsca **wdrożenia**, a następnie wybierz pozycję **+ Dodaj miejsce**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Dodaj Azure Functions miejsce wdrożenia." border="true":::

1. Wpisz nazwę gniazda i wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nazwij Azure Functions miejsce wdrożenia." border="true":::

## <a name="swap-slots"></a>Zastępowanie miejsc

Możesz zamienić gniazda za pośrednictwem [interfejsu wiersza polecenia](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap) lub portalu. W poniższych krokach przedstawiono sposób wymiany gniazd w portalu:

1. Przejdź do aplikacji funkcji.
1. Wybierz pozycję miejsca **wdrożenia**, a następnie wybierz pozycję **Zamień**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Zrzut ekranu pokazujący stronę &quot;miejsce wdrożenia&quot; z wybraną akcją &quot;Dodaj miejsce&quot;." border="true":::

1. Sprawdź ustawienia konfiguracji wymiany i wybierz pozycję **Zamień**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Zamień miejsce wdrożenia." border="true":::

Operacja może chwilę potrwać, gdy trwa wykonywanie operacji zamiany.

## <a name="roll-back-a-swap"></a>Wycofywanie wymiany

Jeśli Zamiana powoduje błąd lub po prostu chcesz "cofnąć" zamianę, możesz przywrócić pierwotny stan. Aby powrócić do stanu sprzed wymiany, należy wykonać kolejną zamianę w celu odwrócenia zamiany.

## <a name="remove-a-slot"></a>Usuń gniazdo

Możesz usunąć gniazdo za pośrednictwem [interfejsu wiersza polecenia](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete) lub portalu. W poniższych krokach pokazano, jak usunąć miejsce w portalu:

1. Przejdź do **miejsc wdrożenia** w aplikacji funkcji, a następnie wybierz nazwę gniazda.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź gniazda w Azure Portal." border="true":::

1. Wybierz pozycję **Usuń**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Zrzut ekranu pokazujący stronę &quot;przegląd&quot; z wybraną akcją &quot;Usuń&quot;." border="true":::

1. Wpisz nazwę miejsca wdrożenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Usuń miejsce wdrożenia w Azure Portal." border="true":::

1. Zamknij okienko potwierdzenie usunięcia.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Potwierdzenie usunięcia miejsca wdrożenia." border="true":::

## <a name="automate-slot-management"></a>Automatyzowanie zarządzania gniazdami

Korzystając z [interfejsu wiersza polecenia platformy Azure](/cli/azure/functionapp/deployment/slot), można zautomatyzować następujące działania w gnieździe:

- [Create](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-create)
- [delete](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-delete)
- [list](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-list)
- [wymiany](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-swap)
- [Zamień na siebie](/cli/azure/functionapp/deployment/slot#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Zmień plan App Service

Za pomocą aplikacji funkcji działającej w ramach planu App Service można zmienić bazowy Plan App Service dla gniazda.

> [!NOTE]
> Nie można zmienić planu App Service gniazda w ramach planu zużycia.

Aby zmienić plan App Service gniazda, wykonaj następujące czynności:

1. Przejdź do **miejsc wdrożenia** w aplikacji funkcji, a następnie wybierz nazwę gniazda.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź gniazda w Azure Portal." border="true":::

1. W obszarze **plan App Service** wybierz pozycję **Zmień App Service plan**.

1. Wybierz plan, do którego chcesz przeprowadzić uaktualnienie, lub Utwórz nowy plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Zmień plan App Service w Azure Portal." border="true":::

1. Wybierz przycisk **OK**.

## <a name="limitations"></a>Ograniczenia

Azure Functions miejsca wdrożenia mają następujące ograniczenia:

- Liczba gniazd dostępnych dla aplikacji zależy od planu. Plan zużycia jest dozwolony tylko w jednym miejscu wdrożenia. Dodatkowe gniazda są dostępne dla aplikacji uruchamianych w ramach planu App Service.
- Wymiana gniazda powoduje zresetowanie kluczy dla aplikacji, których `AzureWebJobsSecretStorageType` ustawienie aplikacji jest równe `files` .
- Gniazda nie są dostępne dla planu zużycia systemu Linux.

## <a name="support-levels"></a>Poziomy pomocy technicznej

Istnieją dwa poziomy wsparcia dla miejsc wdrożenia:

- **Ogólna dostępność (ga)**: w pełni obsługiwana i zatwierdzona do użycia w środowisku produkcyjnym.
- **Wersja zapoznawcza**: nie jest jeszcze obsługiwana, ale oczekuje się, że w przyszłości zostanie osiągnięty stan ga.

| System operacyjny/plan hostingu           | Poziom pomocy technicznej     |
| ------------------------- | -------------------- |
| Użycie systemu Windows       | Ogólna dostępność |
| Windows Premium           | Ogólna dostępność  |
| Dedykowane systemu Windows         | Ogólna dostępność |
| Użycie systemu Linux         | Wersja zapoznawcza          |
| System Linux Premium             | Ogólna dostępność  |
| System Linux — dedykowany           | Ogólna dostępność |

## <a name="next-steps"></a>Następne kroki

- [Technologie wdrażania w Azure Functions](./functions-deployment-technologies.md)
