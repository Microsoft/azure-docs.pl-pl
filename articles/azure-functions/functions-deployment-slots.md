---
title: Azure Functions wdrożenia
description: Dowiedz się, jak tworzyć miejsca wdrożenia i używać ich z Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.openlocfilehash: 5f003b68283217f7877dc650ae4f07ddc5a31012
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789343"
---
# <a name="azure-functions-deployment-slots"></a>Azure Functions wdrożenia

Azure Functions wdrożenia umożliwiają aplikacji funkcji uruchamianie różnych wystąpień nazywanych "miejscami". Miejsca to różne środowiska udostępniane za pośrednictwem publicznie dostępnego punktu końcowego. Jedno wystąpienie aplikacji jest zawsze mapowane na miejsce produkcyjne i można zamienić wystąpienia przypisane do miejsca na żądanie. Aplikacje funkcji uruchomione w ramach planu usługi Apps Service mogą mieć wiele miejsc, a w ramach planu Zużycie dozwolone jest tylko jedno miejsce.

Poniżej przedstawiono sposób, w jaki zamiana miejsc wpływa na funkcje:

- Przekierowywanie ruchu jest bezproblemowe; żadne żądania nie są porzucane z powodu zamiany.
- Jeśli funkcja jest uruchomiona podczas zamiany, wykonywanie jest kontynuowane, a kolejne wyzwalacze są kierowane do zamienione wystąpienie aplikacji.

## <a name="why-use-slots"></a>Dlaczego warto używać miejsc?

Korzystanie z miejsc wdrożenia ma wiele zalet. W poniższych scenariuszach opisano typowe zastosowania miejsc:

- **Różne środowiska do różnych celów:** użycie różnych miejsc umożliwia odróżnienie wystąpień aplikacji przed zamianą na środowisko produkcyjne lub miejsce przejściowe.
- **Wstępne wdrażanie:** wdrożenie w miejscu zamiast bezpośrednio w środowisku produkcyjnym umożliwia rozgrzewce aplikacji przed rozpoczęciem transmisji na żywo. Ponadto użycie miejsc zmniejsza opóźnienie obciążeń wyzwalanych przez protokół HTTP. Wystąpienia są rozgrzewki przed wdrożeniem, co zmniejsza zimny start dla nowo wdrożonych funkcji.
- **Łatwe powroty:** po zamianie z środowiskiem produkcyjnym miejsce z wcześniej przejechaną aplikacją ma teraz poprzednią aplikację produkcyjną. Jeśli zmiany zamienione w miejscu produkcyjnym nie są zgodnie z oczekiwaniami, możesz natychmiast odwrócić zamianę, aby uzyskać zwrot "ostatniego znanego dobrego wystąpienia".

## <a name="swap-operations"></a>Operacje zamiany

Podczas zamiany jedno miejsce jest uznawane za źródło, a drugie miejsce docelowe. Miejsce źródłowe ma wystąpienie aplikacji, które jest stosowane do miejsca docelowego. Poniższe kroki zapewniają, że w miejscu docelowym nie wystąpi przestój podczas zamiany:

1. **Zastosuj ustawienia:** Ustawienia z miejsca docelowego są stosowane do wszystkich wystąpień miejsca źródłowego. Na przykład ustawienia produkcyjne są stosowane do wystąpienia przejściowego. Zastosowane ustawienia obejmują następujące kategorie:
    - [Ustawienia aplikacji specyficzne dla](#manage-settings) miejsca i parametry połączenia (jeśli dotyczy)
    - [Ustawienia ciągłego wdrażania](../app-service/deploy-continuous-deployment.md) (jeśli są włączone)
    - [App Service uwierzytelniania](../app-service/overview-authentication-authorization.md) (jeśli jest włączone)

1. **Poczekaj na ponowne uruchomienia i dostępność:** Zamiana czeka, aż każde wystąpienie w miejscu źródłowym ukończy ponowne uruchomienie i będzie dostępne dla żądań. Jeśli ponowne uruchomienie jakiegokolwiek wystąpienia nie powiedzie się, operacja zamiany przywraca wszystkie zmiany do miejsca źródłowego i zatrzymuje operację.

1. **Aktualizowanie routingu:** Jeśli wszystkie wystąpienia w miejscu źródłowym zostaną pomyślnie rozgrzane, dwa miejsca ukończą zamianę, przełączając reguły routingu. Po tym kroku miejsce docelowe (na przykład miejsce produkcyjne) ma aplikację, która została wcześniej rozgrzana w miejscu źródłowym.

1. **Powtórz operację:** Teraz, gdy miejsce źródłowe zawiera aplikację przed zamianą wcześniej w miejscu docelowym, wykonaj tę samą operację, stosując wszystkie ustawienia i ponownie uruchamiając wystąpienia dla miejsca źródłowego.

Należy pamiętać o następujących kwestiach:

- W dowolnym momencie operacji zamiany inicjowanie zamienione aplikacje odbywa się w miejscu źródłowym. Miejsce docelowe pozostaje w trybie online podczas przygotowania miejsca źródłowego bez względu na to, czy zamiana zakończy się powodzeniem, czy niepowodzeniem.

- Aby zamienić miejsce przejściowe na miejsce produkcyjne, upewnij się, że miejsce produkcyjne *jest* zawsze miejscem docelowym. W ten sposób operacja zamiany nie ma wpływu na aplikację produkcyjną.

- Ustawienia związane ze źródłami zdarzeń i powiązaniami należy skonfigurować jako ustawienia miejsca [wdrożenia](#manage-settings) przed *rozpoczęciem zamiany*. Oznaczenie ich z wyprzedzeniem jako "sticky" gwarantuje, że zdarzenia i dane wyjściowe są kierowane do odpowiedniego wystąpienia.

## <a name="manage-settings"></a>Zarządzanie ustawieniami

Niektóre ustawienia konfiguracji są specyficzne dla miejsca. Poniżej przedstawiono szczegółowe informacje o tym, które ustawienia zmieniają się podczas zamiany miejsc i które pozostają takie same.

**Ustawienia specyficzne dla miejsca:**

* Publikowanie punktów końcowych
* Niestandardowe nazwy domen
* Niepublicznie dostępne certyfikaty i ustawienia protokołu TLS/SSL
* Ustawienia skalowania
* Harmonogramy zadań WebJob
* Ograniczenia adresów IP
* Zawsze włączone
* Ustawienia diagnostyczne
* Współużytkowanie zasobów między źródłami (CORS)

**Ustawienia nieo specyficzne dla miejsca:**

* Ustawienia ogólne, takie jak wersja struktury, 32/64-bitowe gniazda internetowe
* Ustawienia aplikacji (można skonfigurować tak, aby nie było miejsca)
* Parametry połączenia (można je skonfigurować tak, aby przywierały do miejsca)
* Mapowania programu obsługi
* Certyfikaty publiczne
* Zawartość usługi WebJobs
* Połączenia hybrydowe *
* Integracja z siecią wirtualną *
* Punkty końcowe usługi *
* Azure Content Delivery Network *

Funkcje oznaczone gwiazdką (*) mają być niezamapowane. 

> [!NOTE]
> Niektóre ustawienia aplikacji, które dotyczą niezapiętych ustawień, również nie są zamieniane. Na przykład ponieważ ustawienia diagnostyczne nie są zamieniane, powiązane ustawienia aplikacji, takie jak i , również nie są zamieniane, nawet jeśli nie są wyświetlane `WEBSITE_HTTPLOGGING_RETENTION_DAYS` `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` jako ustawienia miejsca.
>

### <a name="create-a-deployment-setting"></a>Tworzenie ustawienia wdrożenia

Ustawienia można oznaczyć jako ustawienie wdrożenia, dzięki czemu będzie ono "sticky". Ustawienie sticky nie jest zamieniane z wystąpieniem aplikacji.

Jeśli tworzysz ustawienie wdrożenia w jednym miejscu, pamiętaj, aby utworzyć to samo ustawienie z unikatową wartością w dowolnym innym miejscu, które jest związane z zamianą. Dzięki temu, mimo że wartość ustawienia nie zmienia się, nazwy ustawień pozostają spójne między miejscami. Ta spójność nazw gwarantuje, że kod nie próbuje uzyskać dostępu do ustawienia zdefiniowanego w jednym miejscu, ale nie w innym.

Aby utworzyć ustawienie wdrożenia, należy wykonać następujące czynności:

1. Przejdź do **opcji Miejsca** wdrożenia w aplikacji funkcji, a następnie wybierz nazwę miejsca.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź miejsca w Azure Portal." border="true":::

1. Wybierz **pozycję** Konfiguracja, a następnie wybierz nazwę ustawienia, które chcesz przykleić do bieżącego miejsca.

    :::image type="content" source="./media/functions-deployment-slots/functions-configure-deployment-slot.png" alt-text="Skonfiguruj ustawienie aplikacji dla miejsca w Azure Portal." border="true":::

1. Wybierz **pozycję Ustawienie miejsca wdrożenia,** a następnie wybierz przycisk **OK.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-setting.png" alt-text="Skonfiguruj ustawienie miejsca wdrożenia." border="true":::

1. Gdy sekcja ustawień zniknie, wybierz pozycję **Zapisz,** aby zachować zmiany

    :::image type="content" source="./media/functions-deployment-slots/functions-save-deployment-slot-setting.png" alt-text="Zapisz ustawienie miejsca wdrożenia." border="true":::

## <a name="deployment"></a>Wdrożenie

Miejsca są puste podczas tworzenia miejsca. Do wdrożenia aplikacji w miejscu [możesz](./functions-deployment-technologies.md) użyć dowolnej z obsługiwanych technologii wdrażania.

## <a name="scaling"></a>Skalowanie

Wszystkie miejsca są skalowane do tej samej liczby procesów pracy co miejsce produkcyjne.

- W przypadku planów Zużycie miejsce jest skalowane w przypadku skalowania aplikacji funkcji.
- W App Service aplikacja jest skalowana do stałej liczby pracowników. Miejsca są uruchamiane dla tej samej liczby pracowników co plan aplikacji.

## <a name="add-a-slot"></a>Dodawanie miejsca

Miejsce można dodać za pośrednictwem interfejsu [wiersza polecenia](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create) lub portalu. W poniższych krokach pokazano, jak utworzyć nowe miejsce w portalu:

1. Przejdź do aplikacji funkcji.

1. Wybierz **pozycję Miejsca wdrożenia,** a następnie **wybierz pozycję + Dodaj miejsce.**

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add.png" alt-text="Dodaj Azure Functions wdrożenia." border="true":::

1. Wpisz nazwę miejsca i wybierz pozycję **Dodaj**.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slots-add-name.png" alt-text="Nazwij Azure Functions wdrożenia." border="true":::

## <a name="swap-slots"></a>Zamienianie miejsc

Miejsca można zamienić za pośrednictwem interfejsu [wiersza](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap) polecenia lub portalu. W poniższych krokach pokazano, jak zamienić miejsca w portalu:

1. Przejdź do aplikacji funkcji.
1. Wybierz **pozycję Miejsca wdrożenia,** a następnie wybierz pozycję **Zamień**.

    :::image type="content" source="./media/functions-deployment-slots/functions-swap-deployment-slot.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;Miejsce wdrożenia&quot; z wybraną akcją &quot;Dodaj miejsce&quot;." border="true":::

1. Sprawdź ustawienia konfiguracji zamiany i wybierz pozycję **Zamień**
    
    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png" alt-text="Zamień miejsce wdrożenia." border="true":::

Operacja zamiany może chwilę potrwać.

## <a name="roll-back-a-swap"></a>Wycofywanie zamiany

Jeśli zamiana powoduje błąd lub chcesz po prostu "cofnąć" zamianę, możesz wrócić do stanu początkowego. Aby wrócić do stanu przed zamianą, wykonaj kolejną zamianę, aby cofnąć zamianę.

## <a name="remove-a-slot"></a>Usuwanie miejsca

Miejsce można usunąć za pomocą interfejsu [wiersza polecenia](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete) lub portalu. Poniższe kroki pokazują, jak usunąć miejsce w portalu:

1. Przejdź do **opcji Miejsca** wdrożenia w aplikacji funkcji, a następnie wybierz nazwę miejsca.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź miejsca w Azure Portal." border="true":::

1. Wybierz pozycję **Usuń**.

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;Przegląd&quot; z wybraną akcją &quot;Usuń&quot;." border="true":::

1. Wpisz nazwę miejsca wdrożenia, które chcesz usunąć, a następnie wybierz pozycję **Usuń.**

    :::image type="content" source="./media/functions-deployment-slots/functions-delete-deployment-slot-details.png" alt-text="Usuń miejsce wdrożenia w Azure Portal." border="true":::

1. Zamknij okienko potwierdzenia usunięcia.

    :::image type="content" source="./media/functions-deployment-slots/functions-deployment-slot-deleted.png" alt-text="Potwierdzenie usunięcia miejsca wdrożenia." border="true":::

## <a name="automate-slot-management"></a>Automatyzowanie zarządzania miejscami

Za pomocą [interfejsu wiersza](/cli/azure/functionapp/deployment/slot)polecenia platformy Azure można zautomatyzować następujące akcje dla miejsca:

- [Utworzyć](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_create)
- [delete](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_delete)
- [list](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_list)
- [Wymiany](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_swap)
- [zamiana automatyczna](/cli/azure/functionapp/deployment/slot#az_functionapp_deployment_slot_auto_swap)

## <a name="change-app-service-plan"></a>Zmienianie App Service planu

W przypadku aplikacji funkcji działającej w ramach planu App Service można zmienić bazowy plan App Service dla miejsca.

> [!NOTE]
> Nie można zmienić planu App Service w ramach planu Zużycie.

Aby zmienić plan App Service miejsca, należy wykonać następujące czynności:

1. Przejdź do **opcji Miejsca wdrożenia** w aplikacji funkcji, a następnie wybierz nazwę miejsca.

    :::image type="content" source="./media/functions-deployment-slots/functions-navigate-slots.png" alt-text="Znajdź miejsca w Azure Portal." border="true":::

1. W **App Service wybierz** pozycję **Zmień App Service planu.**

1. Wybierz plan, do którego chcesz uaktualnić program, lub utwórz nowy plan.

    :::image type="content" source="./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-apply.png" alt-text="Zmień plan App Service w Azure Portal." border="true":::

1. Wybierz przycisk **OK**.

## <a name="limitations"></a>Ograniczenia

Azure Functions wdrożenia mają następujące ograniczenia:

- Liczba miejsc dostępnych dla aplikacji zależy od planu. Plan Zużycie jest dozwolony tylko w jednym miejscu wdrożenia. Dodatkowe miejsca są dostępne dla aplikacji uruchomionych w ramach App Service planu.
- Zamiana miejsca powoduje zresetowanie kluczy dla aplikacji, które `AzureWebJobsSecretStorageType` mają ustawienie aplikacji o wartości `files` .
- Miejsca nie są dostępne dla Zużycie w systemie Linux planu.

## <a name="support-levels"></a>Poziomy pomocy technicznej

Istnieją dwa poziomy obsługi miejsc wdrożenia:

- **Ogólna dostępność:** w pełni obsługiwana i zatwierdzona do użytku produkcyjnego.
- **Wersja** zapoznawcza: jeszcze nie jest obsługiwana, ale oczekuje się, że w przyszłości stanie się ona nadal gałązną.

| System operacyjny/plan hostingu           | Poziom pomocy technicznej     |
| ------------------------- | -------------------- |
| Zużycie systemu Windows       | Ogólna dostępność |
| Windows Premium           | Ogólna dostępność  |
| Windows Dedicated         | Ogólna dostępność |
| Zużycie w systemie Linux         | Wersja zapoznawcza          |
| Linux Premium             | Ogólna dostępność  |
| Linux Dedicated           | Ogólna dostępność |

## <a name="next-steps"></a>Następne kroki

- [Technologie wdrażania w Azure Functions](./functions-deployment-technologies.md)
