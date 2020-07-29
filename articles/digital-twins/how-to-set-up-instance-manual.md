---
title: Konfigurowanie wystąpienia i uwierzytelniania (ręczne)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji, w tym prawidłowe uwierzytelnianie. Wersja ręczna.
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d2d5ce0bc988badc6f25726206a953d87de7eaa2
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371463"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-manual"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (ręczne)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

W tej wersji tego artykułu przechodzą te kroki ręcznie, po jednym z nich. Aby przeprowadzić instalację zautomatyzowaną za pomocą przykładowego skryptu wdrożenia, zobacz wersję skryptu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-starter.md](../../includes/digital-twins-setup-starter.md)]

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

### <a name="verify-success"></a>Weryfikowanie sukcesu

Jeśli wystąpienie zostało utworzone pomyślnie, wynik w Cloud Shell będzie wyglądać podobnie do podania informacji o utworzonym zasobie:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

Zwróć uwagę na nazwy *hosta*, *nazwę*i *źródłową* wystąpienia usługi Azure Digital bliźniaczych reprezentacji z danych wyjściowych. Są to wszystkie ważne wartości, które mogą być potrzebne podczas dalszej pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, aby skonfigurować uwierzytelnianie i powiązane zasoby platformy Azure.

> [!TIP]
> Można wyświetlić te właściwości wraz ze wszystkimi właściwościami wystąpienia w dowolnym momencie, uruchamiając polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Następnie uzyskasz odpowiednie uprawnienia użytkownika platformy Azure do zarządzania nim.

## <a name="set-up-your-users-access-permissions"></a>Konfigurowanie uprawnień dostępu użytkownika

Usługa Azure Digital bliźniaczych reprezentacji korzysta z [usługi Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) na potrzeby kontroli dostępu opartej na ROLACH (RBAC). Oznacza to, że zanim użytkownik będzie mógł wykonać wywołania płaszczyzny danych do wystąpienia usługi Azure Digital bliźniaczych reprezentacji, użytkownik musi najpierw przypisać rolę z uprawnieniami do tego celu.

W przypadku usługi Azure Digital bliźniaczych reprezentacji ta rola jest _**właścicielem usługi Azure Digital bliźniaczych reprezentacji (wersja zapoznawcza)**_. Więcej informacji o rolach i zabezpieczeniach można znaleźć w temacie [*pojęcia: zabezpieczenia dla rozwiązań Digital bliźniaczych reprezentacji na platformie Azure*](concepts-security.md).

W tej sekcji przedstawiono sposób tworzenia przypisania roli dla użytkownika w wystąpieniu usługi Azure Digital bliźniaczych reprezentacji przy użyciu poczty e-mail skojarzonej z dzierżawą usługi Azure AD w ramach subskrypcji platformy Azure. W zależności od roli i uprawnień w ramach Twojej subskrypcji platformy Azure Możesz skonfigurować tę opcję samodzielnie lub skonfigurować ją w imieniu kogoś innego, kto będzie zarządzać wystąpieniem usługi Azure Digital bliźniaczych reprezentacji.

### <a name="assign-the-role"></a>Przypisywanie roli

Aby przyznać użytkownikowi uprawnienia do zarządzania wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, należy przypisać im rolę " _**wersja zapoznawcza" usługi Azure Digital bliźniaczych reprezentacji**_ . 

> [!NOTE]
> Należy zauważyć, że ta rola różni się od roli *właściciela* usługi Azure AD, która również może być przypisana w zakresie wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Są to dwie odrębne role zarządzania, a *właściciel* usługi Azure AD nie udziela dostępu do funkcji płaszczyzny danych, które są udzielane za pomocą *usługi Azure Digital bliźniaczych reprezentacji Owner (wersja zapoznawcza)*.

Użyj następującego polecenia, aby przypisać rolę (musi być uruchomiona przez właściciela subskrypcji platformy Azure):

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-email-of-user-to-assign>" --role "Azure Digital Twins Owner (Preview)"
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli.

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie *400: nieprawidłowego żądania* , uruchom następujące polecenie, aby uzyskać identyfikator *objectid* dla użytkownika:
> ```azurecli
> az ad user show --id <Azure-AD-email-of-user-to-assign> --query objectId
> ```
> Następnie powtórz polecenie przypisania roli, używając *identyfikatora obiektu* użytkownika zamiast wiadomości e-mail.

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji i masz przypisane uprawnienia do zarządzania nim. Następnie skonfigurujesz uprawnienia do aplikacji klienckiej w celu uzyskania do niej dostępu.

## <a name="set-up-access-permissions-for-client-applications"></a>Konfigurowanie uprawnień dostępu dla aplikacji klienckich

Po skonfigurowaniu wystąpienia usługi Azure Digital bliźniaczych reprezentacji często można korzystać z tego wystąpienia za pomocą utworzonej aplikacji klienckiej. Aby to zrobić, należy się upewnić, że aplikacja kliencka będzie mogła uwierzytelniać się w usłudze Azure Digital bliźniaczych reprezentacji. W tym celu należy skonfigurować **rejestrację aplikacji** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) do użycia w aplikacji klienckiej.

Ta rejestracja aplikacji to miejsce, w którym można skonfigurować uprawnienia dostępu do [interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-use-apis-sdks.md). Później aplikacja kliencka będzie uwierzytelniana względem rejestracji aplikacji i w wyniku przyznania skonfigurowanych uprawnień dostępu do interfejsów API.

>[!TIP]
> Właściciel subskrypcji może chcieć skonfigurować nową rejestrację aplikacji dla każdego nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji *lub* zrobić to tylko raz i nawiązać rejestrację pojedynczej aplikacji, która będzie współużytkowana przez wszystkie wystąpienia usługi Azure Digital bliźniaczych reprezentacji w ramach subskrypcji.

### <a name="create-the-registration"></a>Utwórz rejestrację

Aby utworzyć rejestrację aplikacji, należy podać identyfikatory zasobów dla interfejsów API Digital bliźniaczych reprezentacji systemu Azure oraz uprawnienia linii bazowej do interfejsu API. W katalogu roboczym Otwórz nowy plik, a następnie wprowadź Poniższy fragment kodu JSON, aby skonfigurować następujące szczegóły: 

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

Zapisz ten plik jako *manifest.js*.

> [!NOTE] 
> Istnieją pewne miejsca, w których "przyjazny", czytelny dla użytkownika ciąg `https://digitaltwins.azure.net` może być używany dla identyfikatora aplikacji Digital bliźniaczych reprezentacji zasobów platformy Azure zamiast identyfikatora GUID `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` . Na przykład wiele przykładów w tym zestawie dokumentacji używa uwierzytelniania z biblioteką MSAL, a dla tego można użyć przyjaznego ciągu. Jednak w tym kroku tworzenia rejestracji aplikacji identyfikator GUID identyfikatora jest wymagany, tak jak pokazano powyżej. 

W oknie Cloud Shell kliknij ikonę "przekazywanie/pobieranie plików" i wybierz pozycję "Przekaż".

:::image type="content" source="media/how-to-set-up-instance/cloud-shell-upload.png" alt-text="Okno Cloud Shell pokazujące wybór opcji przekazywania":::
Przejdź do *manifest.js* po utworzeniu i naciśnij pozycję "Otwórz".

Następnie uruchom następujące polecenie, aby utworzyć rejestrację aplikacji (zastępując symbole zastępcze w razie potrzeby):

```azurecli
az ad app create --display-name <name-for-your-app> --native-app --required-resource-accesses manifest.json --reply-url http://localhost
```

Poniżej znajduje się fragment danych wyjściowych tego polecenia, przedstawiający informacje o utworzonej rejestracji:

:::image type="content" source="media/how-to-set-up-instance/new-app-registration.png" alt-text="Cloud Shell dane wyjściowe nowej rejestracji aplikacji usługi Azure AD":::

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-app-registration-1.md](../../includes/digital-twins-setup-verify-app-registration-1.md)]

Najpierw sprawdź, czy ustawienia z przekazanych *manifest.js* zostały prawidłowo ustawione podczas rejestracji. W tym celu wybierz pozycję *manifest* z paska menu, aby wyświetlić kod manifestu rejestracji aplikacji. Przewiń w dół okna kod i poszukaj pól z *manifest.jsw* obszarze `requiredResourceAccess` :

[!INCLUDE [digital-twins-setup-verify-app-registration-2.md](../../includes/digital-twins-setup-verify-app-registration-2.md)]

### <a name="collect-important-values"></a>Zbierz ważne wartości

Następnie na pasku menu wybierz pozycję *Przegląd* , aby wyświetlić szczegóły rejestracji aplikacji:

:::image type="content" source="media/how-to-set-up-instance/app-important-values.png" alt-text="Widok portalu ważnych wartości rejestracji aplikacji":::

Zanotuj *Identyfikator* *aplikacji (klienta)* na stronie **użytkownika** . Te wartości będą później konieczne do [uwierzytelnienia aplikacji klienckiej względem interfejsów API Digital bliźniaczych reprezentacji platformy Azure](how-to-authenticate-client.md). Jeśli nie jesteś osobą, która będzie pisać kod dla takich aplikacji, musisz udostępnić te wartości osobie, która będzie.

### <a name="other-possible-steps-for-your-organization"></a>Inne możliwe kroki dla organizacji

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak połączyć aplikację kliencką z wystąpieniem, pisząc kod uwierzytelniania aplikacji klienta:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)
