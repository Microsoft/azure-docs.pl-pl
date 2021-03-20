---
title: Zarządzanie środowiskami usługi integracji w Azure Logic Apps
description: Sprawdź kondycję sieci i Zarządzaj aplikacjami logiki, połączeniami, łącznikami niestandardowymi i kontami integracji w środowisku usługi integracji (ISE) dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: 4df9543e5e747de640562b7e5be224e257e0cfd1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92676113"
---
# <a name="manage-your-integration-service-environment-ise-in-azure-logic-apps"></a>Zarządzanie środowiskiem usługi integracji (ISE) w usłudze Azure Logic Apps

W tym artykule pokazano, jak wykonywać zadania zarządzania dla [środowiska usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), na przykład:

* Zarządzaj zasobami, takimi jak aplikacje logiki, połączenia, konta integracji i łączniki w ISE.

* Sprawdź kondycję sieci ISE.

* Dodaj pojemność, uruchom ponownie ISE lub Usuń ISE, wykonując czynności opisane w tym temacie. Aby dodać te artefakty do ISE, zobacz [Dodawanie artefaktów do środowiska usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md).

## <a name="view-your-ise"></a>Wyświetlanie ISE

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania portalu wprowadź "środowiska usługi integracji", a następnie wybierz pozycję **środowiska usługi integracji**.

   ![Znajdź środowiska usługi integracji](./media/ise-manage-integration-service-environment/find-integration-service-environment.png)

1. Z listy wyników wybierz środowisko usługi integracji.

   ![Wybierz środowisko usługi integracji](./media/ise-manage-integration-service-environment/select-integration-service-environment.png)

1. Przejdź do następnych sekcji, aby znaleźć aplikacje logiki, połączenia, łączniki lub konta integracji w ISE.

<a name="check-network-health"></a>

## <a name="check-network-health"></a>Sprawdź kondycję sieci

W menu ISE w obszarze **Ustawienia** wybierz pozycję **kondycja sieci**. W tym okienku jest wyświetlany stan kondycji podsieci i zależności wychodzące z innych usług.

![Sprawdź kondycję sieci](./media/ise-manage-integration-service-environment/ise-check-network-health.png)

> [!CAUTION]
> Jeśli sieć ISE stanie się zła, wewnętrzna App Service Environment (ASE), która jest używana przez ISE, może być również w złej kondycji. Jeśli środowisko ASE jest w złej kondycji przez ponad siedem dni, środowisko ASE zostanie zawieszone. Aby rozwiązać ten stan, należy sprawdzić konfigurację sieci wirtualnej. Rozwiąż wszystkie problemy, które znajdziesz, a następnie uruchom ponownie ISE. W przeciwnym razie po upływie 90 dni zawieszone środowisko ASE zostanie usunięte, a ISE stanie się bezużyteczny. Upewnij się, że ISE w dobrej kondycji, aby zezwolić na wymagany ruch.
> 
> Więcej informacji można znaleźć w następujących tematach:
>
> * [Omówienie diagnostyki Azure App Service](../app-service/overview-diagnostics.md)
> * [Rejestrowanie komunikatów dla Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

<a name="find-logic-apps"></a>

## <a name="manage-your-logic-apps"></a>Zarządzanie aplikacjami logiki

Aplikacje logiki można wyświetlać i zarządzać nimi, które znajdują się w ISE.

1. W menu ISE w obszarze **Ustawienia** wybierz pozycję **Aplikacje logiki**.

   ![Wyświetlanie aplikacji logiki](./media/ise-manage-integration-service-environment/ise-find-logic-apps.png)

1. Aby usunąć aplikacje logiki, które nie są już potrzebne w ISE, wybierz te aplikacje logiki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

> [!NOTE]
> Jeśli usuniesz i utworzysz ponownie podrzędną aplikację logiki, musisz ponownie zapisać nadrzędną aplikację logiki. Ponownie utworzona aplikacja podrzędna będzie miała inne metadane.
> Jeśli nie zapiszesz ponownie nadrzędnej aplikacji logiki po ponownym utworzeniu elementu podrzędnego, Twoje wywołania podrzędnej aplikacji logiki zakończą się niepowodzeniem z błędem "Brak autoryzacji". To zachowanie dotyczy aplikacji logiki nadrzędny-podrzędny, na przykład tych, które używają artefaktów na kontach integracji lub wywołują usługi Azure Functions.

<a name="find-api-connections"></a>

## <a name="manage-api-connections"></a>Zarządzanie połączeniami interfejsu API

Można wyświetlać i zarządzać połączeniami utworzonymi przez aplikacje logiki działające w ISE.

1. W menu ISE w obszarze **Ustawienia** wybierz pozycję **połączenia interfejsu API**.

   ![Wyświetlanie połączeń interfejsu API](./media/ise-manage-integration-service-environment/ise-find-api-connections.png)

1. Aby usunąć połączenia, które nie są już potrzebne w ISE, wybierz te połączenia, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="manage-api-connectors"></a>

## <a name="manage-ise-connectors"></a>Zarządzanie łącznikami ISE

Można wyświetlać łączniki interfejsu API wdrożone w ISE i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia** wybierz pozycję **Łączniki zarządzane**.

   ![Wyświetlanie łączników zarządzanych](./media/ise-manage-integration-service-environment/ise-view-managed-connectors.png)

1. Aby usunąć łączniki, które nie mają być dostępne w ISE, wybierz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="find-custom-connectors"></a>

## <a name="manage-custom-connectors"></a>Zarządzanie łącznikami niestandardowymi

Można wyświetlać łączniki niestandardowe wdrożone w ISE i zarządzać nimi.

1. W menu ISE w obszarze **Ustawienia** wybierz pozycję **Łączniki niestandardowe**.

   ![Znajdowanie łączników niestandardowych](./media/ise-manage-integration-service-environment/ise-find-custom-connectors.png)

1. Aby usunąć łączniki niestandardowe, które nie są już potrzebne w ISE, wybierz te łączniki, a następnie wybierz pozycję **Usuń**. Aby potwierdzić, że chcesz usunąć, wybierz pozycję **tak**.

<a name="find-integration-accounts"></a>

## <a name="manage-integration-accounts"></a>Zarządzanie kontami integracji

1. W menu ISE w obszarze **Ustawienia** wybierz pozycję **konta integracji**.

   ![Znajdowanie kont integracji](./media/ise-manage-integration-service-environment/ise-find-integration-accounts.png)

1. Aby usunąć konta integracji z usługi ISE, gdy nie są już potrzebne, wybierz te konta integracji, a następnie wybierz pozycję **Usuń**.

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Dodawanie pojemności środowiska ISE

Jednostka bazowa ISE Premium ma stałą pojemność, więc jeśli potrzebujesz większej przepływności, możesz dodać więcej jednostek skalowania podczas tworzenia lub później. Jednostka SKU dla deweloperów nie obejmuje możliwości dodawania jednostek skalowania.

1. W [Azure Portal](https://portal.azure.com)przejdź do ISE.

1. Aby sprawdzić metryki użycia i wydajności dla ISE, w menu ISE wybierz pozycję **Przegląd**.

   ![Wyświetl użycie dla ISE](./media/ise-manage-integration-service-environment/integration-service-environment-usage.png)

1. W obszarze **Ustawienia** wybierz pozycję **Skaluj w poziomie**. W okienku **Konfigurowanie** wybierz jedną z następujących opcji:

   * [**Skalowanie ręczne**](#manual-scale): skalowanie w oparciu o liczbę jednostek przetwarzania, których chcesz użyć.
   * [**Niestandardowe automatyczne skalowanie**](#custom-autoscale): skalowanie w oparciu o metryki wydajności, wybierając spośród różnych kryteriów i określając warunki progowe spełniające te kryteria.

   ![Zrzut ekranu pokazujący stronę "Skaluj w poziomie" z wybraną opcją "skalowanie ręczne".](./media/ise-manage-integration-service-environment/select-scale-out-options.png)

<a name="manual-scale"></a>

### <a name="manual-scale"></a>Skalowanie ręczne

1. Po wybraniu opcji **skalowanie ręczne** wybierz liczbę jednostek skalowania **, które** mają być używane.

   ![Wybierz odpowiedni typ skalowania](./media/ise-manage-integration-service-environment/select-manual-scale-out-units.png)

1. Po zakończeniu wybierz pozycję **Zapisz**.

<a name="custom-autoscale"></a>

### <a name="custom-autoscale"></a>Niestandardowe Skalowanie automatyczne

1. Po wybraniu opcji **niestandardowe automatyczne skalowanie** dla **nazwy ustawienia automatycznego skalowania** Podaj nazwę ustawienia i opcjonalnie wybierz grupę zasobów platformy Azure, do której należy to ustawienie.

   ![Podaj nazwę ustawienia automatycznego skalowania i wybierz grupę zasobów](./media/ise-manage-integration-service-environment/select-custom-autoscale.png)

1. W przypadku warunku **domyślnego** wybierz opcję **skalowanie na podstawie metryki** lub **skali do określonej liczby wystąpień**.

   * Jeśli wybierzesz opcję oparty na wystąpieniu, wprowadź liczbę jednostek przetwarzania, która jest wartością z przedziału od 0 do 10.

   * W przypadku wybrania opcji opartych na pomiarach wykonaj następujące czynności:

     1. W sekcji **reguły** wybierz pozycję **Dodaj regułę**.

     1. W okienku **reguła skalowania** Skonfiguruj swoje kryteria i akcję, które mają być wykonywane po wyzwoleniu reguły.

     1. W obszarze **limity wystąpień** określ następujące wartości:

        * **Minimum**: minimalna liczba jednostek przetwarzania do użycia
        * **Maksimum**: Maksymalna liczba jednostek przetwarzania do użycia
        * **Domyślnie**: Jeśli wystąpią problemy podczas odczytywania metryk zasobów, a bieżąca pojemność jest mniejsza niż domyślna pojemność, Skalowanie automatyczne jest skalowane do domyślnej liczby jednostek przetwarzania. Jeśli jednak bieżąca pojemność przekracza pojemność domyślną, Skalowanie automatyczne nie jest skalowane.

1. Aby dodać kolejny warunek, wybierz pozycję **Dodaj warunek skalowania**.

1. Po zakończeniu korzystania z ustawień automatycznego skalowania Zapisz zmiany.

<a name="restart-ISE"></a>

## <a name="restart-ise"></a>Uruchom ponownie ISE

W przypadku zmiany ustawień serwera DNS lub serwera DNS należy ponownie uruchomić ISE, aby ISE mógł pobrać te zmiany. Ponowne uruchomienie jednostki SKU Premium ISE nie powoduje przestoju z powodu nadmiarowości i składników, które są ponownie uruchamiane pojedynczo podczas odtwarzania. Jednak jednostka SKU dla deweloperów ISE się przestój, ponieważ nie istnieje nadmiarowość. Aby uzyskać więcej informacji, zobacz [ISE SKU](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level).

1. W [Azure Portal](https://portal.azure.com)przejdź do ISE.

1. W menu ISE wybierz pozycję **Przegląd**. Na pasku narzędzi przegląd **Uruchom ponownie** program.

   ![Uruchom ponownie środowisko usługi integracji](./media/connect-virtual-network-vnet-isolated-environment/restart-integration-service-environment.png)

<a name="delete-ise"></a>

## <a name="delete-ise"></a>Usuwanie środowiska ISE

Przed usunięciem ISE, które nie są już potrzebne, lub grupy zasobów platformy Azure zawierającej ISE, sprawdź, czy w grupie zasobów platformy Azure, która zawiera te zasoby lub w sieci wirtualnej platformy Azure, nie ma zasad ani blokad, ponieważ te elementy mogą blokować usuwanie.

Po usunięciu ISE może być konieczne odczekanie do 9 godzin przed podjęciem próby usunięcia sieci wirtualnej lub podsieci platformy Azure.

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usługi integracji](../logic-apps/add-artifacts-integration-service-environment-ise.md)
