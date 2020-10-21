---
title: Integrowanie z usługą Logic Apps
titleSuffix: Azure Digital Twins
description: Zobacz, jak połączyć Logic Apps z usługą Azure Digital bliźniaczych reprezentacji przy użyciu łącznika niestandardowego
author: baanders
ms.author: baanders
ms.date: 9/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 292305c3f899ac4156fd84e8edcb0d6e9a3c0b34
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92280852"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integracja z usługą Logic Apps przy użyciu łącznika niestandardowego

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) to usługa w chmurze, która ułatwia automatyzację przepływów pracy między aplikacjami i usługami. Łącząc Logic Apps z interfejsami API Digital bliźniaczych reprezentacji platformy Azure, możesz tworzyć takie zautomatyzowane przepływy na platformie Azure Digital bliźniaczych reprezentacji i ich danych.

Usługa Azure Digital bliźniaczych reprezentacji nie ma obecnie certyfikowanego (wstępnie utworzonego) łącznika dla Logic Apps. Zamiast tego bieżący proces używania Logic Apps z usługą Azure Digital bliźniaczych reprezentacji polega na utworzeniu [**niestandardowego łącznika Logic Apps**](../logic-apps/custom-connector-overview.md)przy użyciu niestandardowego programu [Azure Digital bliźniaczych reprezentacji Swagger](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , który został zmodyfikowany do pracy z Logic Apps.

> [!NOTE]
> Istnieje wiele wersji struktury Swagger zawartych w powyższym przykładzie niestandardowej struktury Swagger. Najnowsza wersja zostanie znaleziona w podfolderze z najnowszą datą, ale wcześniejsze wersje zawarte w próbce również są nadal obsługiwane.

Ten artykuł zawiera [Azure Portal](https://portal.azure.com) do **tworzenia łącznika niestandardowego** , który może służyć do nawiązywania połączenia Logic Apps z wystąpieniem Digital bliźniaczych reprezentacji platformy Azure. Następnie utworzysz **aplikację logiki** , która używa tego połączenia w przykładowym scenariuszu, w którym zdarzenia wyzwalane przez czasomierz będą automatycznie aktualizować sznurki w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem **Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) ** .
Zaloguj się do [Azure Portal](https://portal.azure.com) za pomocą tego konta. 

Należy również wykonać następujące czynności w ramach konfiguracji wymagań wstępnych. Pozostała część tej sekcji przeprowadzi Cię przez następujące kroki:
- Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji
- Pobierz klucz tajny klienta rejestracji aplikacji
- Dodaj dwuosiową cyfrę

### <a name="set-up-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Aby połączyć Logic Apps wystąpienie usługi Azure Digital bliźniaczych reprezentacji w tym artykule, musisz mieć już skonfigurowane **wystąpienie usługi Azure Digital bliźniaczych reprezentacji** . 

Najpierw **Skonfiguruj wystąpienie usługi Azure Digital bliźniaczych reprezentacji** i wymagane uwierzytelnianie, aby móc z nich korzystać. Aby to zrobić, postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Konfigurowanie wystąpienia i uwierzytelniania*](how-to-set-up-instance-portal.md). W zależności od preferowanego środowiska, artykuł instalacyjny jest oferowany dla [przykładowego skryptu wdrażania](how-to-set-up-instance-scripted.md) [Azure Portal](how-to-set-up-instance-portal.md), [interfejsu wiersza polecenia](how-to-set-up-instance-cli.md)lub Cloud Shell. Wszystkie wersje instrukcji zawierają również kroki umożliwiające sprawdzenie, czy każdy krok został pomyślnie wykonany i jest gotowy do przejścia do korzystania z nowego wystąpienia.
* Po skonfigurowaniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy określić **_nazwę hosta_** wystąpienia ([Znajdź w Azure Portal](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Aby uwierzytelnić aplikację ADT Explorer, należy również skonfigurować **rejestrację aplikacji**. Postępuj zgodnie z instrukcjami podanymi w temacie [*How to: Create a App Registration*](how-to-create-app-registration.md) to set up. 
* Po zarejestrowaniu aplikacji będziesz potrzebować identyfikatora **_aplikacji (klienta)_** rejestracji i **_identyfikatora (dzierżawy)_** ([Znajdź w Azure Portal](how-to-create-app-registration.md#collect-client-id-and-tenant-id)).

### <a name="get-app-registration-client-secret"></a>Pobierz klucz tajny klienta rejestracji aplikacji

Należy również utworzyć **_klucz tajny klienta_** dla rejestracji aplikacji usługi Azure AD. W tym celu przejdź do strony [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal (możesz użyć tego linku lub poszukać go na pasku wyszukiwania portalu). Wybierz swoją rejestrację utworzoną w poprzedniej sekcji z listy, aby otworzyć jej szczegóły. 

Trafij *Certyfikaty i wpisy tajne* z menu Rejestracja i wybierz pozycję *+ nowy klucz tajny klienta*.

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Wprowadź wszelkie wartości, które mają być stosowane w opisie i wygaśnie, i kliknij przycisk *Dodaj*.

:::image type="content" source="media/how-to-integrate-logic-apps/add-client-secret.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Teraz sprawdź, czy klucz tajny klienta jest widoczny na stronie _certyfikaty & wpisy tajne_ z polami _Expires_ i _Value_ . Zanotuj jego _wartość_ , aby użyć jej później (można także skopiować ją do schowka za pomocą ikony kopiowania)

:::image type="content" source="media/how-to-integrate-logic-apps/client-secret-value.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

### <a name="add-a-digital-twin"></a>Dodaj dwuosiową cyfrę

W tym artykule jest używane Logic Apps do aktualizowania sznurka w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji. Aby można było wykonać operację, należy dodać co najmniej jedną dwuosiową w wystąpieniu. 

Możesz dodać bliźniaczych reprezentacji za pomocą [interfejsów API DigitalTwins](how-to-use-apis-sdks.md), [zestawu SDK platformy .NET (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)lub [interfejsu wiersza polecenia platformy Azure Digital bliźniaczych reprezentacji](how-to-use-cli.md). Aby uzyskać szczegółowe instrukcje dotyczące sposobu tworzenia bliźniaczych reprezentacji przy użyciu tych metod, zobacz [*How to: Manage Digital bliźniaczych reprezentacji*](how-to-manage-twin.md).

Będziesz potrzebować **_identyfikatora sznurka_** w utworzonym wystąpieniu.

## <a name="create-custom-logic-apps-connector"></a>Tworzenie łącznika Logic Apps niestandardowego

W tym kroku utworzysz [niestandardowy łącznik Logic Apps](../logic-apps/custom-connector-overview.md) dla interfejsów API Digital bliźniaczych reprezentacji platformy Azure. Po wykonaniu tej czynności będzie można podłączyć usługę Azure Digital bliźniaczych reprezentacji podczas tworzenia aplikacji logiki w następnej sekcji.

Przejdź do strony [Logic Apps łącznika niestandardowego](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) w Azure Portal (możesz użyć tego linku lub wyszukać go na pasku wyszukiwania portalu). Trafij *i Dodaj*.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Na poniższej stronie *utwórz Logic Apps łącznika niestandardowego* wybierz subskrypcję i grupę zasobów oraz nazwę i lokalizację wdrożenia dla nowego łącznika. *Przejrzyj i Utwórz*. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Spowoduje to przejście do karty *Recenzja + tworzenie* , w której można utworzyć *zasób w dolnej* części.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Nastąpi przekierowanie do strony wdrożenia łącznika. Po zakończeniu wdrażania naciśnij przycisk *Przejdź do zasobu* , aby wyświetlić szczegóły łącznika w portalu.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurowanie łącznika dla usługi Azure Digital bliźniaczych reprezentacji

Następnie skonfigurujesz utworzony łącznik, aby uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji.

Najpierw pobierz niestandardową strukturę Swagger usługi Azure Digital bliźniaczych reprezentacji, która została zmodyfikowana w celu współpracy z Logic Apps. Pobierz przykład **niestandardowego struktury Swagger (Logic Apps Connector) usługi Azure Digital bliźniaczych reprezentacji** z [**tego linku**](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) , naciskając przycisk *Pobierz plik zip* . Przejdź do pobranego folderu *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip* i rozpakuj go. 

Niestandardowa struktura Swagger dla tego samouczka znajduje się w folderze _* * Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps **_. Ten folder zawiera podfoldery o nazwie *stabilne* i *Preview*, w których obie wersje struktury Swagger są zorganizowane według daty. Folder o najnowszej dacie będzie zawierać najnowszą kopię struktury Swagger. Niezależnie od wybranej wersji plik struktury Swagger ma nazwę _** digitaltwins.jsna * * _.

> [!NOTE]
> Jeśli nie pracujesz z funkcją w wersji zapoznawczej, zwykle zalecamy użycie najnowszej *stabilnej* wersji struktury Swagger. Jednak wcześniejsze wersje i wersje zapoznawcze struktury Swagger również są nadal obsługiwane. 

Następnie przejdź do strony omówienia łącznika w [Azure Portal](https://portal.azure.com) i kliknij przycisk *Edytuj*.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;." dla koloru.
    - Opis: Wypełnij wszystkie wartości, które chcesz.
    - Schemat: HTTPS (pozostaw domyślny)
    - Host: *Nazwa hosta* wystąpienia usługi Azure Digital bliźniaczych reprezentacji.
    - Podstawowy adres URL:/(pozostaw wartość domyślną)

Następnie naciśnij przycisk *zabezpieczeń* w dolnej części okna, aby przejść do kolejnego kroku konfiguracji.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

W kroku zabezpieczenia naciśnij pozycję *Edytuj* i skonfiguruj następujące informacje:
* **Typ uwierzytelniania**: OAuth 2,0
* **OAuth 2,0**:
    - Dostawca tożsamości: Azure Active Directory
    - Identyfikator klienta: *Identyfikator aplikacji (klienta)* dla rejestracji aplikacji usługi Azure AD
    - Wpis tajny klienta: *klucz tajny klienta* utworzony w [*wymaganiach wstępnych*](#prerequisites) rejestracji aplikacji usługi Azure AD
    - Adres URL logowania: https://login.windows.net (pozostaw wartość domyślną)
    - Identyfikator dzierżawy: *Identyfikator katalogu (dzierżawcy)* dla rejestracji aplikacji usługi Azure AD
    - Adres URL zasobu: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Zakres: Directory. AccessAsUser. All
    - Adres URL przekierowania: (pozostaw teraz wartość domyślną)

Zwróć uwagę, że w polu adres URL przekierowania zostanie *zapisany łącznik niestandardowy w celu wygenerowania adresu URL przekierowania*. Zrób to teraz przez naciśnięcie przycisku *Aktualizuj łącznik* w górnej części okienka, aby potwierdzić ustawienia łącznika.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

<!-- Success message? didn't see one -->

Wróć do pola adres URL przekierowania i skopiuj wygenerowaną wartość. Zostanie ona użyta w następnym kroku.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Są to wszystkie informacje wymagane do utworzenia łącznika (nie ma potrzeby dalszej ochrony przed poprzednimi zabezpieczeniami w kroku definicji). Możesz zamknąć okienko *edytowanie Logic Apps łącznika niestandardowego* .

>[!NOTE]
>Wróć do strony omówienia łącznika, w której pierwotnie trafisz *Edytowanie*, pamiętaj, że naciśnięcie opcji *Edytuj* ponownie spowoduje ponowne uruchomienie całego procesu wprowadzania konfiguracji. Nie spowoduje to wypełnienia wartości z ostatniego przekroczenia tego czasu, dlatego jeśli chcesz zapisać zaktualizowaną konfigurację z dowolnymi zmienionymi wartościami, musisz ponownie wprowadzić wszystkie pozostałe wartości, aby uniknąć ich zastąpienia przy użyciu wartości domyślnych.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Przyznawanie uprawnień łącznika w aplikacji usługi Azure AD

Następnie użyj wartości *adresu URL przekierowania* łącznika niestandardowego skopiowanej w ostatnim kroku, aby przyznać uprawnienia łącznika w rejestracji aplikacji usługi Azure AD.

Przejdź do strony [rejestracje aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) w Azure Portal i wybierz swoją rejestrację z listy. 

W obszarze *uwierzytelnianie* z menu Rejestracja Dodaj identyfikator URI.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;."::: 

Wprowadź *adres URL przekierowania* łącznika niestandardowego do nowego pola i kliknij ikonę *Zapisz* .

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Teraz po skonfigurowaniu łącznika niestandardowego, który będzie mógł uzyskać dostęp do interfejsów API Digital bliźniaczych reprezentacji platformy Azure. 

## <a name="create-logic-app"></a>Tworzenie aplikacji logiki

Następnie utworzysz aplikację logiki, która będzie używać nowego łącznika do automatyzowania aktualizacji usługi Azure Digital bliźniaczych reprezentacji.

W [Azure Portal](https://portal.azure.com)Wyszukaj *Aplikacje logiki* na pasku wyszukiwania portalu. Wybranie tej opcji powinno spowodować przejście na stronę *Aplikacje logiki* . Naciśnij przycisk *Utwórz aplikację logiki* , aby utworzyć nową aplikację logiki.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Na stronie *aplikacja logiki* poniżej wprowadź subskrypcję i grupę zasobów. Ponadto wybierz nazwę aplikacji logiki i wybierz lokalizację wdrożenia.

Naciśnij przycisk _Recenzja + Utwórz_ .

Spowoduje to przejście do karty *Recenzja i tworzenie* , gdzie można przejrzeć szczegóły i kliknąć przycisk *Utwórz* u dołu, aby utworzyć zasób.

Nastąpi przekierowanie do strony wdrożenia aplikacji logiki. Po zakończeniu wdrażania naciśnij przycisk *Przejdź do zasobu* , aby przejść do *projektanta Logic Apps*, w którym zostanie wypełniona logika przepływu pracy.

### <a name="design-workflow"></a>Projekt przepływu pracy

W *projektancie Logic Apps*w obszarze *Rozpocznij ze wspólnym wyzwalaczem*wybierz pozycję _**cykl**_.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

Na poniższej stronie *projektanta Logic Apps* Zmień częstotliwość **cyklu** na *sekundę*, aby zdarzenie było wyzwalane co 3 sekundy. Dzięki temu będzie można łatwo zobaczyć wyniki później, bez konieczności oczekiwania.

Naciśnij pozycję *+ nowy krok*.

Spowoduje to otwarcie pola *Wybierz akcję* . Przejdź do karty *niestandardowej* . Łącznik niestandardowy powinien być widoczny wcześniej w górnej części okna.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;." w artykule *How to: Manage Digital bliźniaczych reprezentacji*.
* _API-Version_: Najnowsza wersja interfejsu API. Obecnie ta wartość to *2020-10-31*.

Naciśnij klawisz *Save* w projektancie Logic Apps.

Możesz wybrać inne operacje, wybierając pozycję _+ nowy krok_ w tym samym oknie.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Widok portalu rejestracji aplikacji usługi Azure AD. W menu zasób znajduje się wyróżniona wartość &quot;certyfikaty i wpisy tajne&quot;, a na stronie znajduje się wartość &quot;nowy wpis tajny klienta&quot;.":::

## <a name="query-twin-to-see-the-update"></a>Zapytanie bliźniaczye, aby zobaczyć aktualizację

Teraz, gdy aplikacja logiki została utworzona, zdarzenie aktualizacji z przędzą zdefiniowane w projektancie Logic Apps powinno odbywać się w cyklu co trzy sekundy. Oznacza to, że po upływie trzech sekund powinno być możliwe przetworzenie zapytania dotyczącego sznurka i wyświetlenie nowych poprawionych wartości.

Możesz wysyłać zapytania do przędzy za pomocą wybranej metody (takiej jak [niestandardowa aplikacja kliencka](tutorial-command-line-app.md), [Przykładowa aplikacja programu Azure Digital bliźniaczych reprezentacji Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/), [zestawy SDK i interfejsy API](how-to-use-apis-sdks.md)lub [interfejs wiersza polecenia](how-to-use-cli.md)). 

Aby uzyskać więcej informacji o wysyłaniu zapytań do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, zobacz [*How to: Query The bliźniaczy Graf*](how-to-query-graph.md).

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono aplikację logiki, która regularnie aktualizuje dwuosiowe w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji za pomocą podanej poprawki. Możesz wypróbować inne interfejsy API w łączniku niestandardowym, aby utworzyć Logic Apps dla różnych akcji w wystąpieniu.

Aby dowiedzieć się więcej na temat dostępnych operacji API i wymaganych szczegółów, odwiedź stronę [*How to: use Digital bliźniaczych reprezentacji API and SDK*](how-to-use-apis-sdks.md).