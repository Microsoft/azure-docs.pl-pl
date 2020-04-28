---
title: Azure Functions miejsc wdrożenia
description: Dowiedz się, jak tworzyć i używać miejsc wdrożenia przy użyciu Azure Functions
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75769221"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions miejsc wdrożenia

Azure Functions miejsca wdrożenia pozwalają aplikacji funkcji uruchamiać różne wystąpienia o nazwie "gniazda". Gniazda są różnymi środowiskami udostępnianymi za pośrednictwem publicznie dostępnego punktu końcowego. Jedno wystąpienie aplikacji jest zawsze mapowane na miejsce produkcyjne i można zamienić wystąpienia przypisane do gniazda na żądanie. Aplikacje funkcji działające w ramach planu usługi aplikacji mogą mieć wiele gniazd, a w ramach planu zużycia dozwolone jest tylko jedno gniazdo.

Poniżej przedstawiono sposób, w jaki funkcje mają wpływ na zamienienie gniazd:

- Przekierowywanie ruchu jest bezproblemowe; żadne żądania nie są usuwane ze względu na zamianę.
- Jeśli funkcja jest uruchomiona w trakcie wymiany, wykonywanie jest kontynuowane, a kolejne wyzwalacze są kierowane do zamienionego wystąpienia aplikacji.

> [!NOTE]
> Gniazda nie są obecnie dostępne dla planu zużycia systemu Linux.

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

- W dowolnym momencie operacji wymiany inicjowanie zamienionych aplikacji odbywa się w miejscu źródłowym. Gniazdo docelowe pozostaje w trybie online, gdy miejsce źródłowe jest przygotowywane, niezależnie od tego, czy swap zakończyło się powodzeniem, czy niepowodzeniem.

- Aby wymienić miejsce przejściowe z miejscem produkcyjnym, upewnij się, że miejsce produkcyjne jest *zawsze* miejscem docelowym. W ten sposób operacja zamiany nie ma wpływu na aplikację produkcyjną.

- Ustawienia związane ze źródłami i powiązaniami zdarzeń muszą zostać skonfigurowane jako [Ustawienia miejsca wdrożenia](#manage-settings) *przed zainicjowaniem zamiany*. Oznaczanie ich jako "Sticky" przed czasem gwarantuje, że zdarzenia i wyjścia są kierowane do właściwego wystąpienia.

## <a name="manage-settings"></a>Zarządzanie ustawieniami

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Utwórz ustawienie wdrożenia

Można oznaczyć ustawienia jako ustawienie wdrożenia, które powoduje "Sticky IT". Ustawienie programu Sticky Notes nie jest zamieniane na wystąpienie aplikacji.

W przypadku utworzenia ustawienia wdrożenia w jednym gnieździe upewnij się, że to ustawienie ma wartość unikatową w dowolnym innym gnieździe uwzględnionym w wymianie. W ten sposób, gdy wartość ustawienia nie ulegnie zmianie, nazwy ustawień pozostają spójne między gniazdami. Ta spójność nazw gwarantuje, że kod nie próbuje uzyskać dostępu do ustawienia zdefiniowanego w jednym gnieździe, ale innym.

Wykonaj następujące kroki, aby utworzyć ustawienie wdrożenia:

- Przejdź do *miejsc* w aplikacji funkcji
- Kliknij nazwę miejsca
- W obszarze *funkcje platformy > ustawienia ogólne*kliknij pozycję **Konfiguracja** .
- Kliknij nazwę ustawienia, które chcesz połączyć z bieżącym miejscem
- Kliknij pole wyboru **Ustawienia miejsca wdrożenia**
- Kliknij przycisk **OK** .
- Po wyznikaniu bloku ustawienia kliknij przycisk **Zapisz** , aby zachować zmiany

![Ustawienie miejsca wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Wdrożenie

Gniazda są puste podczas tworzenia gniazda. Możesz użyć dowolnej z [obsługiwanych technologii wdrażania](./functions-deployment-technologies.md) , aby wdrożyć aplikację w gnieździe.

## <a name="scaling"></a>Skalowanie

Wszystkie gniazda są skalowane do tej samej liczby procesów roboczych co miejsce produkcyjne.

- W przypadku planów zużycia gniazdo jest skalowane w miarę skalowania aplikacji funkcji.
- W przypadku planów App Service aplikacja jest skalowana do stałej liczby procesów roboczych. Gniazda są uruchamiane na tej samej liczbie procesów roboczych co plan aplikacji.

## <a name="add-a-slot"></a>Dodawanie miejsca

Możesz dodać gniazdo za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) lub portalu. Poniższe kroki pokazują, jak utworzyć nowe miejsce w portalu:

1. Przejdź do aplikacji funkcji i kliknij znak **Plus** *obok pozycji miejsca.*

    ![Dodaj Azure Functions miejsce wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Wprowadź nazwę w polu tekstowym, a następnie naciśnij przycisk **Utwórz** .

    ![Nazwa Azure Functions miejsce wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Zastępowanie miejsc

Możesz zamienić gniazda za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) lub portalu. W poniższych krokach przedstawiono sposób wymiany gniazd w portalu:

1. Przejdź do aplikacji funkcji
1. Kliknij nazwę miejsca źródłowego, które chcesz zamienić
1. Na karcie *Omówienie* kliknij przycisk ![ **swap** Zamień Azure Functions miejsce wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Sprawdź ustawienia konfiguracji wymiany **i kliknij pozycję swap** ![swap Azure Functions miejsce wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

Operacja może chwilę potrwać, gdy trwa wykonywanie operacji zamiany.

## <a name="roll-back-a-swap"></a>Wycofywanie wymiany

Jeśli Zamiana powoduje błąd lub po prostu chcesz "cofnąć" zamianę, możesz przywrócić pierwotny stan. Aby powrócić do stanu sprzed wymiany, należy wykonać kolejną zamianę w celu odwrócenia zamiany.

## <a name="remove-a-slot"></a>Usuń gniazdo

Możesz usunąć gniazdo za pośrednictwem [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) lub portalu. W poniższych krokach pokazano, jak usunąć miejsce w portalu:

1. Przejdź do omówienia aplikacji funkcji

1. Kliknij przycisk **Usuń**

    ![Dodaj Azure Functions miejsce wdrożenia](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatyzowanie zarządzania gniazdami

Korzystając z [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest), można zautomatyzować następujące działania w gnieździe:

- [Create](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [usunięty](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [list](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [wymiany](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [Zamień na siebie](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Zmień plan App Service

W przypadku aplikacji funkcji działającej w ramach planu App Service istnieje możliwość zmiany bazowego planu App Service dla gniazda.

> [!NOTE]
> Nie można zmienić planu App Service gniazda w ramach planu zużycia.

Aby zmienić plan App Service gniazda, wykonaj następujące czynności:

1. Przejdź do miejsca

1. W obszarze *funkcje platformy*kliknij pozycję **wszystkie ustawienia** .

    ![Zmień plan usługi App Service](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Kliknij **plan App Service**

1. Wybierz nowy plan App Service lub Utwórz nowy plan

1. Kliknij przycisk **OK** .

    ![Zmień plan usługi App Service](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Ograniczenia

Azure Functions miejsca wdrożenia mają następujące ograniczenia:

- Liczba gniazd dostępnych dla aplikacji zależy od planu. Plan zużycia jest dozwolony tylko w jednym miejscu wdrożenia. Dodatkowe gniazda są dostępne dla aplikacji uruchamianych w ramach planu App Service.
- Wymiana gniazda powoduje zresetowanie kluczy dla aplikacji, których ustawienie `AzureWebJobsSecretStorageType` aplikacji jest równe. `files`
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
| Użycie systemu Linux         | Nieobsługiwane          |
| System Linux Premium             | Ogólna dostępność  |
| System Linux — dedykowany           | Ogólna dostępność |

## <a name="next-steps"></a>Następne kroki

- [Technologie wdrażania w Azure Functions](./functions-deployment-technologies.md)
