---
title: Tworzenie rozwiązań partnerskich usługi Datadog — Azure
description: W tym artykule opisano sposób użycia Azure Portal w celu utworzenia wystąpienia usługi Datadog.
ms.service: partner-services
ms.topic: quickstart
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: references_regions
ms.openlocfilehash: 7af8b82c5da6c60527b45b6e8e292b9f067016ec
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748636"
---
# <a name="quickstart-get-started-with-datadog"></a>Szybki Start: Rozpoczynanie pracy z usługą usługi Datadog

W tym przewodniku szybki start utworzysz wystąpienie elementu usługi Datadog. Można utworzyć nową organizację usługi Datadog lub link do istniejącej organizacji usługi Datadog.

## <a name="pre-requisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure usługi Datadog, musisz mieć dostęp **właściciela** do subskrypcji platformy Azure. Przed rozpoczęciem instalacji upewnij się, że masz odpowiedni dostęp.

Aby użyć funkcji SAML (SAML) Single Sign-On (SSO) w ramach zasobu usługi Datadog, należy skonfigurować aplikację dla przedsiębiorstw. Aby dodać aplikację przedsiębiorstwa, potrzebna jest jedna z następujących ról: Administrator globalny, administrator aplikacji w chmurze, administrator aplikacji lub właściciel jednostki usługi.

Aby skonfigurować aplikację dla przedsiębiorstw, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **Azure Active Directory**.
1. W lewym okienku wybierz pozycję **Aplikacje dla przedsiębiorstw**.
1. Wybierz pozycję **Nowa aplikacja**.
1. W obszarze **Dodaj z galerii** Wyszukaj pozycję *usługi Datadog*. Wybierz wynik wyszukiwania, a następnie wybierz pozycję **Dodaj**.

   :::image type="content" source="media/create/datadog-azure-ad-app-gallery.png" alt-text="Usługi Datadog aplikację w galerii usługi AAD Enterprise." border="true":::

1. Po utworzeniu aplikacji przejdź do pozycji właściwości w panelu bocznym. Ustaw **wymagane przypisanie użytkownika?** na **nie**, a następnie wybierz pozycję **Zapisz**.

   :::image type="content" source="media/create/user-assignment-required.png" alt-text="Ustawianie właściwości dla aplikacji usługi Datadog" border="true":::

1. Przejdź do pozycji **Logowanie jednokrotne** w panelu bocznym. Następnie wybierz pozycję **SAML**.

   :::image type="content" source="media/create/saml-sso.png" alt-text="Uwierzytelnianie SAML." border="true":::

1. Wybierz opcję **tak** po wyświetleniu monitu o **zapisanie ustawień logowania** jednokrotnego.

   :::image type="content" source="media/create/save-sso.png" alt-text="Zapisz Logowanie jednokrotne dla aplikacji usługi Datadog" border="true":::

1. Konfiguracja logowania jednokrotnego została zakończona.

## <a name="find-offer"></a>Znajdź ofertę

Użyj Azure Portal, aby znaleźć usługi Datadog.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/) i zaloguj się.

1. Jeśli w ostatniej sesji odwiedzono Portal **Marketplace** , wybierz ikonę z dostępnych opcji. W przeciwnym razie wyszukaj _witrynę Marketplace_.

    :::image type="content" source="media/create/marketplace.png" alt-text="Ikona witryny Marketplace.":::

1. W portalu Marketplace Wyszukaj pozycję **usługi Datadog**.

1. Na ekranie przegląd planu wybierz pozycję **Konfiguruj i Subskrybuj**.

   :::image type="content" source="media/create/datadog-app.png" alt-text="Aplikacja usługi Datadog w portalu Azure Marketplace.":::

## <a name="create-a-datadog-resource-in-azure"></a>Tworzenie zasobu usługi Datadog na platformie Azure

W portalu jest wyświetlany formularz służący do tworzenia zasobu usługi Datadog.

:::image type="content" source="media/create/datadog-create-resource.png" alt-text="Utwórz zasób usługi Datadog" border="true":::

Podaj następujące wartości.

|Właściwość | Opis
|:-----------|:-------- |
| Subskrypcja | Wybierz subskrypcję platformy Azure, która ma zostać użyta do utworzenia zasobu usługi Datadog. Musisz mieć dostęp właściciela. |
| Grupa zasobów | Określ, czy chcesz utworzyć nową grupę zasobów, czy użyć istniejącej grupy. [Grupa zasobów](../../azure-resource-manager/management/overview.md#resource-groups) to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. |
| Nazwa zasobu | Określ nazwę dla zasobu usługi Datadog. Ta nazwa będzie nazwą nowej organizacji usługi Datadog podczas tworzenia nowej organizacji usługi Datadog. |
| Lokalizacja | Wybierz pozycję Zachodnie stany USA 2. Obecnie jedynym obsługiwanym regionem jest zachodnie stany USA 2. |
| Organizacja usługi Datadog | Aby utworzyć nową organizację usługi Datadog, wybierz pozycję **Nowy**. Aby połączyć się z istniejącą organizacją usługi Datadog, wybierz pozycję **istniejące**. |
| Plan cenowy | Podczas tworzenia nowej organizacji wybierz z listy dostępnych planów usługi Datadog. |
| Okres rozliczeniowy | Miesięczne. |

Jeśli łączysz się z istniejącą organizacją usługi Datadog, zobacz następną sekcję. W przeciwnym razie wybierz kolejno pozycje **Dalej: metryki i dzienniki** , a następnie przejdź do następnej sekcji.

## <a name="link-to-existing-datadog-organization"></a>Link do istniejącej organizacji usługi Datadog

Nowy zasób usługi Datadog można połączyć z platformą Azure do istniejącej organizacji usługi Datadog.

Wybierz pozycję **istniejący** w obszarze organizacja danych, a następnie wybierz pozycję **Połącz z organizacją usługi Datadog**.

:::image type="content" source="media/create/link-to-existing.png" alt-text="Połącz z istniejącą organizacją usługi Datadog." border="true":::

Łącze spowoduje otwarcie okna uwierzytelniania usługi Datadog. Zaloguj się do usługi Datadog.

Domyślnie platforma Azure łączy bieżącą organizację usługi Datadog z zasobem usługi Datadog. Jeśli chcesz połączyć się z inną organizacją, wybierz odpowiednią organizację w oknie uwierzytelnianie, jak pokazano poniżej.

:::image type="content" source="media/create/select-datadog-organization.png" alt-text="Wybierz odpowiednią organizację usługi Datadog do powiązania" border="true":::

## <a name="configure-metrics-and-logs"></a>Konfigurowanie metryk i dzienników

Użyj tagów zasobów platformy Azure do skonfigurowania, które metryki i dzienniki są wysyłane do usługi Datadog. Metryki i dzienniki można dołączać do określonych zasobów lub wykluczać z nich.

Reguły tagów do wysyłania **metryk** są następujące:

- Domyślnie metryki są zbierane dla wszystkich zasobów, z wyjątkiem maszyn wirtualnych, zestawów skalowania maszyn wirtualnych i planów usługi App Service.
- Maszyny wirtualne, zestawy skalowania maszyn wirtualnych i plany usługi App Service z *dołączanymi* tagami wysyłają metryki do usługi Datadog.
- Maszyny wirtualne, zestawy skalowania maszyn wirtualnych i plany usługi App Service ze znacznikami *exclude* nie wysyłają metryk do usługi Datadog.
- W przypadku konfliktu między regułami dołączania i wykluczania Wykluczanie ma priorytet

Reguły tagów do wysyłania **dzienników** są następujące:

- Domyślnie dzienniki są zbierane dla wszystkich zasobów.
- Zasoby platformy Azure z tagami *include* umożliwiają wysyłanie dzienników do usługi Datadog.
- Zasoby platformy Azure z tagami  *exclude* nie wysyłają dzienników do usługi Datadog.
- Jeśli występuje konflikt między regułami dołączania i wykluczania, wykluczenie ma priorytet.

Na przykład zrzut ekranu poniżej przedstawia regułę tagu, w której tylko te maszyny wirtualne, zestawy skalowania maszyn wirtualnych i plany usługi App Service oznaczone jako *usługi Datadog = true* wysyłają metryki do usługi Datadog.

:::image type="content" source="media/create/config-metrics-logs.png" alt-text="Skonfiguruj dzienniki i metryki." border="true":::

Istnieją dwa typy dzienników, które mogą być emitowane z platformy Azure do usługi Datadog.

1. **Dzienniki na poziomie subskrypcji** — zapewniają wgląd w operacje na zasobach na [płaszczyźnie kontroli](../../azure-resource-manager/management/control-plane-and-data-plane.md). Uwzględniono również aktualizacje zdarzeń dotyczących kondycji usługi. Użyj dziennika aktywności, aby określić, kto i kiedy w przypadku operacji zapisu (PUT, POST, DELETE). Dla każdej subskrypcji platformy Azure istnieje pojedynczy dziennik aktywności.

1. **Dzienniki zasobów platformy Azure** — zapewniają wgląd w operacje wykonywane w ramach zasobu platformy Azure w [płaszczyźnie danych](../../azure-resource-manager/management/control-plane-and-data-plane.md). Na przykład, pobranie klucza tajnego z Key Vault jest operacją płaszczyzny danych. Lub, żądanie do bazy danych jest również operacją płaszczyzny danych. Zawartość dzienników zasobów zależy od usługi platformy Azure i typu zasobu.

Aby wysłać dzienniki poziomu subskrypcji do usługi Datadog, wybierz pozycję **Wyślij dzienniki aktywności subskrypcji**. Jeśli ta opcja nie jest zaznaczona, żaden z dzienników na poziomie subskrypcji nie zostanie wysłany do usługi Datadog.

Aby wysłać dzienniki zasobów platformy Azure do usługi Datadog, wybierz pozycję **Wyślij dzienniki zasobów platformy Azure dla wszystkich zdefiniowanych zasobów**. Typy dzienników zasobów platformy Azure są wymienione w temacie [Azure monitor kategorie dzienników zasobów](../../azure-monitor/essentials/resource-logs-categories.md).  Aby odfiltrować zestaw zasobów platformy Azure wysyłający dzienniki do usługi Datadog, użyj tagów zasobów platformy Azure.  

W przypadku dzienników wysyłanych do usługi usługi Datadog zostanie naliczona opłata przez platformę Azure. Aby uzyskać więcej informacji, zobacz [Cennik dzienników platformy](https://azure.microsoft.com/pricing/details/monitor/) wysyłanych do partnerów portalu Azure Marketplace.

Po zakończeniu konfigurowania metryk i dzienników wybierz pozycję **Dalej: Logowanie jednokrotne**.

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego

Jeśli Twoja organizacja używa Azure Active Directory jako dostawcy tożsamości, możesz nawiązać Logowanie jednokrotne z Azure Portal do usługi Datadog. Jeśli Twoja organizacja korzysta z innego dostawcy tożsamości lub nie chcesz teraz ustanawiać logowania jednokrotnego, możesz pominąć tę sekcję.

W przypadku łączenia zasobu usługi Datadog z istniejącą organizacją usługi Datadog nie można skonfigurować rejestracji jednokrotnej w tym kroku. Zamiast tego należy skonfigurować Logowanie jednokrotne po utworzeniu zasobu usługi Datadog. Aby uzyskać więcej informacji, zobacz [ponowne konfigurowanie logowania jednokrotnego](manage.md#reconfigure-single-sign-on).

:::image type="content" source="media/create/linking-sso.png" alt-text="Logowanie jednokrotne na potrzeby łączenia z istniejącą organizacją usługi Datadog." border="true":::

Aby nawiązać Logowanie jednokrotne za pomocą usługi Azure Active Directory, zaznacz pole wyboru **Włącz logowanie jednokrotne za pomocą Azure Active Directory**.

Azure Portal pobiera odpowiednią aplikację usługi Datadog z Azure Active Directory. Aplikacja jest zgodna z aplikacją przedsiębiorstwa podaną we wcześniejszym kroku.

Wybierz nazwę aplikacji usługi Datadog.

:::image type="content" source="media/create/sso.png" alt-text="Włącz logowanie jednokrotne do usługi Datadog." border="true":::

Wybierz pozycję **Dalej: Tagi**.

## <a name="add-custom-tags"></a>Dodawanie tagów niestandardowych

Możesz określić niestandardowe znaczniki dla nowego zasobu usługi Datadog. Podaj pary nazwa-wartość dla tagów, które mają zostać zastosowane do zasobu usługi Datadog.

:::image type="content" source="media/create/tags.png" alt-text="Dodaj niestandardowe znaczniki dla zasobu usługi Datadog." border="true":::

Po zakończeniu dodawania tagów wybierz kolejno pozycje **Dalej: recenzja + Utwórz**.

## <a name="review--create-datadog-resource"></a>Przegląd + tworzenie zasobu usługi Datadog

Przejrzyj wybrane opcje i warunki użytkowania. Po zakończeniu walidacji wybierz pozycję **Utwórz**.

:::image type="content" source="media/create/review-create.png" alt-text="Przejrzyj i utwórz zasób usługi Datadog." border="true":::

Na platformie Azure jest wdrażany zasób usługi Datadog.

Po zakończeniu procesu wybierz pozycję **Przejdź do zasobu** , aby wyświetlić zasób usługi Datadog.

:::image type="content" source="media/create/go-to-resource.png" alt-text="Wdrażanie zasobów usługi Datadog." border="true":::

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zarządzanie zasobem usługi Datadog](manage.md)
