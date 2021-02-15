---
title: Włącz zarządzaną tożsamość dla zdarzeń routingu (wersja zapoznawcza) — Portal
titleSuffix: Azure Digital Twins
description: Zapoznaj się z tematem Włączanie tożsamości przypisanej do systemu dla usługi Azure Digital bliźniaczych reprezentacji i używania jej do przesyłania dalej zdarzeń przy użyciu Azure Portal.
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1d54ecdac739bbc5a072426f5bf73f71ab394159
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417677"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Włącz zarządzaną tożsamość do routingu zdarzeń usługi Azure Digital bliźniaczych reprezentacji (wersja zapoznawcza): Azure Portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

W tym artykule opisano sposób włączania [tożsamości przypisanej do systemu dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (obecnie w wersji zapoznawczej) i korzystania z tożsamości podczas przekazywania zdarzeń do obsługiwanych miejsc docelowych, takich jak [centrum zdarzeń](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   miejsc docelowych i [kontener usługi Azure Storage](../storage/blobs/storage-blobs-introduction.md).

W tym artykule omówiono proces przy użyciu [**Azure Portal**](https://portal.azure.com).

Poniżej przedstawiono kroki omówione w tym artykule: 

1. Utwórz wystąpienie usługi Azure Digital bliźniaczych reprezentacji z tożsamością przypisaną do systemu lub Włącz tożsamość przypisaną przez system w istniejącym wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 
1. Dodaj odpowiednią rolę lub role do tożsamości. Na przykład Przypisz rolę **nadawcy danych usługi Azure Event Hub** do tożsamości, jeśli punkt końcowy ma wartość centrum zdarzeń lub **Azure Service Bus rolę nadawcy danych** , jeśli punkt końcowy jest Service Bus.
1. Utwórz punkt końcowy w usłudze Azure Digital bliźniaczych reprezentacji, który może korzystać z tożsamości przypisanych do systemu na potrzeby uwierzytelniania.

## <a name="enable-system-managed-identities-for-an-instance"></a>Włącz tożsamość zarządzaną przez system dla wystąpienia 

Po włączeniu tożsamości przypisanej do systemu w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji platforma Azure automatycznie tworzy dla niej tożsamość w [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Tej tożsamości można następnie użyć do uwierzytelniania w usłudze Azure Digital bliźniaczych reprezentacji Endpoints w celu przekazywania zdarzeń.

Tożsamość zarządzaną przez system można włączyć dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji **jako część początkowej konfiguracji wystąpienia** lub **włączyć ją później w wystąpieniu, które już istnieje**.

Każda z tych metod tworzenia będzie mieć te same opcje konfiguracji i ten sam wynik końcowy dla danego wystąpienia. W tej sekcji opisano sposób wykonywania obu tych czynności.

### <a name="add-a-system-managed-identity-during-instance-creation"></a>Dodawanie tożsamości zarządzanej przez system podczas tworzenia wystąpienia

W tej sekcji dowiesz się, jak włączyć tożsamość zarządzaną przez system w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji, która jest aktualnie tworzona. Ta sekcja koncentruje się na kroku tożsamości zarządzanej procesu tworzenia; pełny Przewodnik tworzenia nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji można znaleźć w temacie [*How to: Set a instance and Authentication*](how-to-set-up-instance-portal.md).

Opcja tożsamość zarządzana przez system znajduje się na karcie **Zaawansowane** w instalatorze wystąpienia.

Na tej karcie Wybierz opcję **Włącz** dla **tożsamości zarządzanej przez system** , aby włączyć tę funkcję.

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlenia karty Zaawansowane okna dialogowego Tworzenie zasobów dla usługi Azure Digital bliźniaczych reprezentacji. Istnieje wyróżnienie wokół nazwy karty, opcji on dla tożsamości zarządzanej przez system i przycisków nawigacji (przegląd + Utwórz, poprzedni, Następny: Zaawansowane).":::

Następnie możesz użyć dolnych przycisków nawigacji, aby kontynuować instalację pozostałej części wystąpienia.

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>Dodawanie tożsamości zarządzanej przez system do istniejącego wystąpienia

W tej sekcji dodasz tożsamość zarządzaną przez system do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, które już istnieje.

1. Najpierw przejdź do [Azure Portal](https://portal.azure.com) w przeglądarce.

1. Wyszukaj nazwę wystąpienia na pasku wyszukiwania portalu i wybierz go, aby wyświetlić jego szczegóły.

1. Wybierz pozycję **tożsamość (wersja zapoznawcza)** w menu po lewej stronie.

1. Na tej stronie **Wybierz opcję Włącz, aby** włączyć tę funkcję.

1. Naciśnij przycisk **Zapisz** i **tak** , aby potwierdzić.

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania strony Identity (wersja zapoznawcza) dla wystąpienia usługi Digital bliźniaczych reprezentacji. W menu wystąpienia usługi Azure Digital bliźniaczych reprezentacji znajduje się wyróżnienie, opcja Włącz dla opcji stan, Zapisz i przycisk Potwierdź.":::

Po zapisaniu zmiany na tej stronie zostaną wyświetlone więcej pól dla **identyfikatora obiektu** nowej tożsamości i **uprawnień**.

W razie potrzeby można skopiować **Identyfikator obiektu** z tego miejsca, a następnie użyć przycisku **uprawnienia** , aby wyświetlić role platformy Azure przypisane do tożsamości. Aby skonfigurować niektóre role, przejdź do następnej sekcji.

## <a name="assign-azure-roles-to-the-identity"></a>Przypisywanie ról platformy Azure do tożsamości 

Po utworzeniu tożsamości przypisanej do systemu dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy przypisać odpowiednie role do uwierzytelniania za pomocą różnych typów [punktów końcowych](concepts-route-events.md) w celu przekazywania zdarzeń do obsługiwanych miejsc docelowych. Ta sekcja zawiera opis opcji ról i sposobu ich przypisywania do tożsamości przypisanej do systemu.

>[!NOTE]
> Jest to ważny krok — bez niego, tożsamość nie będzie mogła uzyskiwać dostępu do punktów końcowych, a zdarzenia nie zostaną dostarczone.

### <a name="supported-destinations-and-azure-roles"></a>Obsługiwane miejsca docelowe i role platformy Azure 

Poniżej znajdują się minimalne role, których tożsamość musi uzyskać dostęp do punktu końcowego, w zależności od typu miejsca docelowego. Role z wyższymi uprawnieniami (na przykład role właściciela danych) również będą działały.

| Element docelowy | Rola na platformie Azure |
| --- | --- |
| Azure Event Hubs | Nadawca danych Event Hubs platformy Azure |
| Usługa Azure Service Bus | Nadawca danych Azure Service Bus |
| Kontener usługi Azure Storage | Współautor danych obiektu blob usługi Storage |

Aby uzyskać więcej informacji na temat punktów końcowych, tras i typów miejsc docelowych obsługiwanych w przypadku routingu w usłudze Azure Digital bliźniaczych reprezentacji, zobacz [*pojęcia: trasy zdarzeń*](concepts-route-events.md).

### <a name="assign-the-role"></a>Przypisywanie roli

>[!NOTE]
> Ta sekcja musi zostać wykonana przez użytkownika platformy Azure z uprawnieniami do zarządzania dostępem użytkowników do zasobów platformy Azure (w tym przyznawania i delegowania uprawnień). Typowe role spełniające to wymaganie są *właścicielami*, *administratorami kont* lub kombinacją administratorów i *współautorów* *dostępu użytkowników* . Aby uzyskać więcej informacji na temat wymagań dotyczących uprawnień dla ról bliźniaczych reprezentacji cyfrowych platformy Azure, zobacz [*How to: Konfiguracja instance and Authentication*](how-to-set-up-instance-portal.md#prerequisites-permission-requirements).

Aby przypisać rolę do tożsamości, Zacznij od otworzenia [Azure Portal](https://portal.azure.com).

1. Przejdź do zasobu punktu końcowego (centrum zdarzeń, tematu Service Bus lub kontenera magazynu), wyszukując jego nazwę na pasku wyszukiwania portalu. 
1. Wybierz pozycję **Kontrola dostępu (IAM)** w menu po lewej stronie.
1. Wybierz przycisk **+ Dodaj** , aby dodać nowe przypisanie roli.

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="Zrzut ekranu przedstawiający Azure Portal wyświetlania strony kontroli dostępu (IAM) dla centrum zdarzeń. Zostanie wyróżniony przycisk + Dodaj." lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. Na stronie **Dodawanie przypisania roli** wprowadź następujące wartości:
    * **Rola**: wybierz żądaną rolę z menu rozwijanego
    * **Przypisz dostęp do**: Wybierz **użytkownika, grupę lub nazwę główną usługi**
    * **Wybierz**: tutaj należy wybrać zarządzaną tożsamość wystąpienia usługi Azure Digital bliźniaczych reprezentacji, do którego jest przypisana rola. Nazwa zarządzanej tożsamości jest zgodna z nazwą wystąpienia, dlatego Wyszukaj nazwę wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Po wybraniu wyniku tożsamość wystąpienia zostanie wyświetlona w sekcji **wybrane elementy członkowskie** .

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="Wypełnianie pól na liście w oknie dialogowym Dodawanie przypisania roli":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Po zakończeniu wprowadzania szczegółów wybierz pozycję **Zapisz**.

## <a name="create-an-endpoint-with-identity-based-authorization"></a>Tworzenie punktu końcowego z autoryzacją opartą na tożsamościach

Po skonfigurowaniu tożsamości zarządzanej przez system dla wystąpienia usługi Azure Digital bliźniaczych reprezentacji i przypisaniu go do odpowiednich ról można utworzyć usługi Azure Digital bliźniaczych reprezentacji [Endpoints](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) , które mogą korzystać z tożsamości na potrzeby uwierzytelniania. Ta opcja jest dostępna tylko dla punktów końcowych centrum zdarzeń i typu Service Bus (nie jest to obsługiwane w przypadku Event Grid).

>[!NOTE]
> Nie można edytować punktu końcowego, który został już utworzony za pomocą tożsamości opartej na kluczach, aby zmienić uwierzytelnianie oparte na tożsamości. Podczas pierwszego tworzenia punktu końcowego należy wybrać typ uwierzytelniania.

Postępuj zgodnie z [instrukcjami, aby utworzyć punkt końcowy usługi Azure Digital bliźniaczych reprezentacji](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins).

Po przeprowadzeniu kroku, aby uzyskać szczegółowe informacje wymagane dla typu punktu końcowego, upewnij się, że wybrano typ uwierzytelniania na **podstawie tożsamości** .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="Zrzut ekranu przedstawiający tworzenie punktu końcowego typu centrum zdarzeń." lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Zakończ konfigurowanie punktu końcowego i wybierz pozycję **Zapisz**.

## <a name="considerations-for-disabling-system-managed-identities"></a>Zagadnienia dotyczące wyłączania tożsamości zarządzanych przez system

Ze względu na to, że tożsamość jest zarządzana niezależnie od punktów końcowych, które go używają, należy wziąć pod uwagę wpływ wszelkich zmian tożsamości lub ról na punkty końcowe w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Jeśli tożsamość jest wyłączona lub zostanie z niej usunięta niezbędna rola punktu końcowego, punkt końcowy może stać się niedostępny i przepływ zdarzeń zostanie zakłócony.

Aby nadal korzystać z punktu końcowego, który został skonfigurowany przy użyciu tożsamości zarządzanej, która została już wyłączona, należy usunąć punkt końcowy i [utworzyć go ponownie](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) z innym typem uwierzytelniania. Wznowienie dostarczania w punkcie końcowym do punktu końcowego może potrwać do godziny.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tożsamościach zarządzanych w usłudze Azure AD: 
* [*Tożsamości zarządzane dla zasobów platformy Azure*](../active-directory/managed-identities-azure-resources/overview.md)