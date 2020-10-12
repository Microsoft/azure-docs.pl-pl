---
title: Konfigurowanie wystąpienia i uwierzytelniania (interfejs wiersza polecenia)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsu wiersza polecenia
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bb5ff0bf7347b87fdc3a103a03f9ff58279a367d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320777"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

W tej wersji tego artykułu przechodzą te kroki ręcznie, po jednym z nich przy użyciu interfejsu wiersza polecenia.
* Aby wykonać te kroki ręcznie przy użyciu Azure Portal, zobacz wersję portalu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (Portal)*](how-to-set-up-instance-portal.md).
* Aby przeprowadzić instalację zautomatyzowaną za pomocą przykładowego skryptu wdrożenia, zobacz wersję skryptu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Tworzenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

W tej sekcji utworzysz **nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji** przy użyciu polecenia Cloud Shell. Musisz podać:
* Grupa zasobów, w której ma zostać wdrożona. Jeśli nie masz jeszcze istniejącej grupy zasobów, możesz utworzyć ją teraz za pomocą tego polecenia:
    ```azurecli
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Region wdrożenia. Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nazwa wystąpienia. Nazwa nowego wystąpienia musi być unikatowa w obrębie regionu subskrypcji (co oznacza, że jeśli subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z wybranej nazwy, zostanie wyświetlony monit o wybranie innej nazwy).

Użyj tych wartości w poniższym poleceniu, aby utworzyć wystąpienie:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Weryfikowanie sukcesu i zbieranie ważnych wartości

Jeśli wystąpienie zostało utworzone pomyślnie, wynik w Cloud Shell będzie wyglądać podobnie do podania informacji o utworzonym zasobie:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

Zwróć uwagę na nazwy *hosta*, *nazwę*i *źródłową* wystąpienia usługi Azure Digital bliźniaczych reprezentacji z danych wyjściowych. Są to wszystkie ważne wartości, które mogą być potrzebne podczas dalszej pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, aby skonfigurować uwierzytelnianie i powiązane zasoby platformy Azure. Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy udostępnić te wartości.

> [!TIP]
> Można wyświetlić te właściwości wraz ze wszystkimi właściwościami wystąpienia w dowolnym momencie, uruchamiając polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Następnie uzyskasz odpowiednie uprawnienia użytkownika platformy Azure do zarządzania nim.

## <a name="set-up-user-access-permissions"></a>Konfigurowanie uprawnień dostępu użytkowników

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Użyj następującego polecenia, aby przypisać rolę (musi być uruchamiana przez użytkownika z [odpowiednimi uprawnieniami](#prerequisites-permission-requirements) w ramach subskrypcji platformy Azure). Polecenie wymaga przekazania *nazwy głównej użytkownika* na koncie usługi Azure AD dla użytkownika, do którego ma zostać przypisana rola. W większości przypadków będzie to zgodne z wiadomościami e-mail użytkownika na koncie usługi Azure AD.

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli.

> [!NOTE]
> Jeśli to polecenie zwróci błąd informujący, że interfejs wiersza polecenia **nie może odnaleźć nazwy głównej użytkownika lub usługi w usłudze Graph Database**:
>
> Zamiast tego Przypisz rolę przy użyciu *identyfikatora obiektu* użytkownika. Może się tak zdarzyć w przypadku użytkowników korzystających z osobistych [kont Microsoft (kont MSA)](https://account.microsoft.com/account). 
>
> Użyj [Azure Portal stronie Azure Active Directory użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) , aby wybrać konto użytkownika i otworzyć jego szczegóły. Skopiuj *Identyfikator obiektu użytkownika:*
>
> :::image type="content" source="media/includes/user-id.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji" lightbox="media/includes/user-id.png":::
>
> Następnie powtórz polecenie listy przypisywania ról przy użyciu *identyfikatora obiektu* użytkownika dla `assignee` powyższego parametru.

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji i masz przypisane uprawnienia do zarządzania nim. Następnie skonfigurujesz uprawnienia do aplikacji klienckiej w celu uzyskania do niej dostępu.

## <a name="set-up-access-permissions-for-client-applications"></a>Konfigurowanie uprawnień dostępu dla aplikacji klienckich

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Aby utworzyć rejestrację aplikacji, należy podać identyfikatory zasobów dla interfejsów API Digital bliźniaczych reprezentacji systemu Azure oraz uprawnienia linii bazowej do interfejsu API.

W katalogu roboczym Utwórz nowy plik, a następnie wprowadź Poniższy fragment kodu JSON, aby skonfigurować następujące szczegóły: 

```json
[{
    "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
    "resourceAccess": [
     {
       "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
       "type": "Scope"
     }
    ]
}]
``` 

Zapisz ten plik jako _**manifest.js**_.

> [!NOTE] 
> Istnieją pewne miejsca, w których "przyjazny", czytelny dla użytkownika ciąg `https://digitaltwins.azure.net` może być używany dla identyfikatora aplikacji Digital bliźniaczych reprezentacji zasobów platformy Azure zamiast identyfikatora GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Na przykład wiele przykładów w tym zestawie dokumentacji używa uwierzytelniania z biblioteką MSAL, a dla tego można użyć przyjaznego ciągu. Jednak w tym kroku tworzenia rejestracji aplikacji identyfikator GUID identyfikatora jest wymagany, tak jak pokazano powyżej. 

Następnie przekażesz ten plik do Cloud Shell. W oknie Cloud Shell kliknij ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::
Przejdź do *manifest.js* po utworzeniu i naciśnij pozycję "Otwórz".

Następnie uruchom następujące polecenie, aby utworzyć rejestrację aplikacji z adresem URL odpowiedzi *publicznej klienta/macierzystego (mobile & Desktop)* `http://localhost` . Zastąp symbole zastępcze w razie konieczności:

```azurecli
az ad app create --display-name <name-for-your-app-registration> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Poniżej znajduje się fragment danych wyjściowych tego polecenia, przedstawiający informacje o utworzonej rejestracji:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/new-app-registration.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Następnie sprawdź, czy ustawienia z przekazanych *manifest.js* zostały prawidłowo ustawione podczas rejestracji. W tym celu wybierz pozycję *manifest* z paska menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj pól z *manifest.jsw* obszarze `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Zbierz ważne wartości

Następnie na pasku menu wybierz pozycję *Przegląd* , aby wyświetlić szczegóły rejestracji aplikacji:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

Zanotuj *Identyfikator* *aplikacji (klienta)* na stronie **użytkownika** . Te wartości będą później konieczne do [uwierzytelnienia aplikacji klienckiej względem interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-authenticate-client.md). Jeśli nie jesteś osobą, która będzie pisać kod dla takich aplikacji, musisz udostępnić te wartości osobie, która będzie.

### <a name="other-possible-steps-for-your-organization"></a>Inne możliwe kroki dla organizacji

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Następne kroki

Przetestuj poszczególne wywołania interfejsu API REST w wystąpieniu przy użyciu poleceń interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji: 
* [odwołanie AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
* [*Instrukcje: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-cli.md)

Lub zapoznaj się z tematem jak połączyć aplikację kliencką z wystąpieniem, pisząc kod uwierzytelniania aplikacji klienta:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)