---
title: Konfigurowanie wystąpienia i uwierzytelniania (portal)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji przy użyciu Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 61b396cbcc8c91c75c961f702de7ed6a33e676e4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947021"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (Portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

W tej wersji tego artykułu przechodzą te kroki ręcznie, po jednym z nich przy użyciu Azure Portal. Witryna Azure Portal to ujednolicona, oparta na sieci Web konsola, która stanowi alternatywę dla narzędzi wiersza polecenia.
* Aby wykonać te kroki ręcznie przy użyciu interfejsu wiersza polecenia, zobacz wersja interfejsu wiersza polecenia w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (CLI)*](how-to-set-up-instance-cli.md).
* Aby przeprowadzić instalację zautomatyzowaną za pomocą przykładowego skryptu wdrożenia, zobacz wersję skryptu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Tworzenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

W tej sekcji utworzysz **nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji** przy użyciu [Azure Portal](https://ms.portal.azure.com/). Przejdź do portalu i zaloguj się przy użyciu swoich poświadczeń.

W portalu Zacznij od wybrania pozycji _Utwórz zasób_ w menu strony głównej usług platformy Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Wybieranie pozycji &quot;Utwórz zasób&quot; ze strony głównej Azure Portal":::

Wyszukaj pozycję *Azure Digital bliźniaczych reprezentacji* w polu wyszukiwania i wybierz usługę **Azure Digital bliźniaczych reprezentacji** z wyników. Wybierz przycisk _Utwórz_ , aby utworzyć nowe wystąpienie usługi.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Wybieranie pozycji &quot;Utwórz&quot; na stronie usługi Azure Digital bliźniaczych reprezentacji":::

Na poniższej stronie **Tworzenie zasobów** Wypełnij wartości podanych poniżej:
* **Subskrypcja**: subskrypcja platformy Azure, której używasz
  - **Grupa zasobów**: Grupa zasobów, w której ma zostać wdrożone wystąpienie. Jeśli nie masz jeszcze istniejącej grupy zasobów, możesz ją utworzyć, wybierając pozycję *Utwórz nowe* łącze i wprowadzając nazwę nowej grupy zasobów.
* **Location (lokalizacja**): bliźniaczych reprezentacji na potrzeby wdrożenia przy użyciu cyfrowego obszaru platformy Azure. Aby uzyskać więcej informacji na temat pomocy technicznej regionalnej, odwiedź stronę [*usługi platformy Azure dostępne według regionów (usługa Azure Digital bliźniaczych reprezentacji)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Nazwa zasobu**: nazwa wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Jeśli Twoja subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z określonej nazwy, zostanie wyświetlony monit o wybranie innej nazwy.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Wypełnianie opisanych wartości, aby utworzyć zasób usługi Azure Digital bliźniaczych reprezentacji":::

Po zakończeniu możesz wybrać opcję **Recenzja + Utwórz** , jeśli nie chcesz konfigurować dodatkowych ustawień dla danego wystąpienia. Spowoduje to przejście do strony Podsumowanie, na której można przejrzeć wprowadzone informacje o wystąpieniu i zakończyć pracę z poleceniem **Utwórz**. 

Jeśli chcesz skonfigurować więcej szczegółów dla danego wystąpienia, w następnej sekcji opisano pozostałe karty ustawień.

### <a name="additional-setup-options"></a>Dodatkowe opcje instalacji

Poniżej znajdują się dodatkowe opcje, które można skonfigurować podczas instalacji, przy użyciu innych kart w procesie **tworzenia zasobów** .

* **Sieć**: na tej karcie można włączyć prywatne punkty końcowe przy użyciu [prywatnego linku platformy Azure](../private-link/private-link-overview.md) , aby wyeliminować zagrożenie sieci publicznej do wystąpienia. Aby uzyskać instrukcje, zobacz [*How to: Enable Private Access with Private link*](how-to-enable-private-link.md#add-a-private-endpoint-during-instance-creation).
* **Zaawansowane**: na tej karcie można włączyć [tożsamość zarządzaną przez system](../active-directory/managed-identities-azure-resources/overview.md) dla danego wystąpienia, które może być używane podczas przekazywania zdarzeń do [punktów końcowych](concepts-route-events.md). Aby uzyskać instrukcje, zobacz [*How to: Enable Managed tożsamośćs for Routing Events*](how-to-enable-managed-identities.md).
* **Tagi**: na tej karcie można dodać tagi do wystąpienia, aby pomóc w organizowaniu go wśród zasobów platformy Azure. Aby uzyskać więcej informacji na temat tagów zasobów platformy Azure, zobacz temat [*zasoby tagów, grupy zasobów i subskrypcje dla organizacji logicznej*](../azure-resource-manager/management/tag-resources.md).

### <a name="verify-success-and-collect-important-values"></a>Weryfikowanie sukcesu i zbieranie ważnych wartości

Po zakończeniu instalacji wystąpienia, wybierając pozycję **Utwórz**, można wyświetlić stan wdrożenia wystąpienia w obszarze powiadomień platformy Azure na pasku ikon portalu. Powiadomienie będzie wskazywać, kiedy wdrożenie zakończyło się pomyślnie, i będzie można wybrać przycisk _Przejdź do zasobu_ , aby wyświetlić utworzone wystąpienie.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Widok powiadomień platformy Azure przedstawiający pomyślne wdrożenie i wyróżnienie przycisku &quot;przejdź do zasobu&quot;":::

Alternatywnie, jeśli wdrożenie nie powiedzie się, powiadomienie wskaże przyczynę. Zwróć uwagę na komunikat o błędzie i ponów próbę utworzenia wystąpienia.

>[!TIP]
>Po utworzeniu wystąpienia możesz powrócić do jego strony w dowolnym momencie, wyszukując nazwę wystąpienia na pasku wyszukiwania Azure Portal.

Na stronie *Przegląd* wystąpienia Zanotuj jego *nazwę*, *grupę zasobów* i *nazwę hosta*. Są to wszystkie ważne wartości, które mogą być potrzebne w przypadku kontynuowania pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji. Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy udostępnić te wartości.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Wyróżnianie ważnych wartości na stronie przeglądowej wystąpienia":::

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Następnie uzyskasz odpowiednie uprawnienia użytkownika platformy Azure do zarządzania nim.

## <a name="set-up-user-access-permissions"></a>Konfigurowanie uprawnień dostępu użytkowników

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Najpierw otwórz stronę wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal. Z menu wystąpienia wybierz pozycję *Kontrola dostępu (IAM)*. Wybierz przycisk  **+ Dodaj** , aby dodać nowe przypisanie roli.

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Wybieranie, aby dodać przypisanie roli na stronie &quot;kontrola dostępu (IAM)&quot;":::

Na poniższej stronie *Dodawanie przypisania roli* Wypełnij wartości (musi zostać wykonane przez użytkownika z [odpowiednimi uprawnieniami](#prerequisites-permission-requirements) w ramach subskrypcji platformy Azure):
* **Rola**: Wybierz *właściciela danych Digital bliźniaczych reprezentacji systemu Azure* z menu rozwijanego
* **Przypisz dostęp do**: Użyj *użytkownika, grupy lub nazwy głównej usługi*
* **Wybierz**: Wyszukaj nazwę lub adres e-mail użytkownika do przypisania. Po wybraniu wyniku użytkownik zostanie wyświetlony w sekcji *wybrane elementy członkowskie* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Wypełnianie pól na liście w oknie dialogowym Dodawanie przypisania roli":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Po zakończeniu wprowadzania szczegółów naciśnij przycisk *Zapisz* .

### <a name="verify-success"></a>Weryfikowanie sukcesu

Można wyświetlić przypisaną rolę, która została skonfigurowana w obszarze *Kontrola dostępu (IAM) > przypisań ról*. Użytkownik powinien zostać wyświetlony na liście z rolą *właściciela danych cyfrowych bliźniaczych reprezentacji systemu Azure*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Widok przypisań roli dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji w Azure Portal":::

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji i masz przypisane uprawnienia do zarządzania nim.

## <a name="next-steps"></a>Następne kroki

Przetestuj poszczególne wywołania interfejsu API REST w wystąpieniu przy użyciu poleceń interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji: 
* [odwołanie AZ DT](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest)
* [*Instrukcje: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-cli.md)

Lub zapoznaj się z tematem jak połączyć aplikację kliencką z wystąpieniem przy użyciu kodu uwierzytelniania:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)