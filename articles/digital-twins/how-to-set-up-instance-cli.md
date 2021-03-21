---
title: Konfigurowanie wystąpienia i uwierzytelniania (interfejs wiersza polecenia)
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsu wiersza polecenia
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7108ee956c18fbcc150b56cbcb8ae1c69841dda4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198591"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Konfigurowanie wystąpienia i uwierzytelniania usługi Azure Digital bliźniaczych reprezentacji (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

W tym artykule opisano procedurę **konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji**, w tym tworzenia wystąpienia i konfigurowania uwierzytelniania. Po ukończeniu tego artykułu będzie dostępne wystąpienie usługi Azure Digital bliźniaczych reprezentacji gotowe do rozpoczęcia programowania.

W tej wersji tego artykułu przechodzą te kroki ręcznie, po jednym z nich przy użyciu interfejsu wiersza polecenia.
* Aby wykonać te kroki ręcznie przy użyciu Azure Portal, zobacz wersję portalu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (Portal)*](how-to-set-up-instance-portal.md).
* Aby przeprowadzić instalację zautomatyzowaną za pomocą przykładowego skryptu wdrożenia, zobacz wersję skryptu w tym artykule: [*instrukcje: Konfigurowanie wystąpienia i uwierzytelniania (skrypty)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]
[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Konfigurowanie sesji Cloud Shell
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Tworzenie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

W tej sekcji utworzysz **nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji** przy użyciu polecenia Cloud Shell. Musisz podać:
* Grupa zasobów, w której zostanie wdrożone wystąpienie. Jeśli nie masz jeszcze istniejącej grupy zasobów, możesz utworzyć ją teraz za pomocą tego polecenia:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Region wdrożenia. Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [*usługi Azure dostępne według regionów*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Nazwa wystąpienia. Jeśli Twoja subskrypcja ma inne wystąpienie usługi Azure Digital bliźniaczych reprezentacji w regionie, w którym już korzystasz z określonej nazwy, zostanie wyświetlony monit o wybranie innej nazwy.

Użyj tych wartości w poniższym poleceniu, aby utworzyć wystąpienie:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

### <a name="verify-success-and-collect-important-values"></a>Weryfikowanie sukcesu i zbieranie ważnych wartości

Jeśli wystąpienie zostało utworzone pomyślnie, wynik w Cloud Shell będzie wyglądać podobnie do podania informacji o utworzonym zasobie:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

Zwróć uwagę na nazwy **hosta**, **nazwę** i **źródłową** wystąpienia usługi Azure Digital bliźniaczych reprezentacji z danych wyjściowych. Są to wszystkie ważne wartości, które mogą być potrzebne podczas dalszej pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, aby skonfigurować uwierzytelnianie i powiązane zasoby platformy Azure. Jeśli inni użytkownicy będą programowanie względem wystąpienia, należy udostępnić te wartości.

> [!TIP]
> Można wyświetlić te właściwości wraz ze wszystkimi właściwościami wystąpienia w dowolnym momencie, uruchamiając polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji. Następnie uzyskasz odpowiednie uprawnienia użytkownika platformy Azure do zarządzania nim.

## <a name="set-up-user-access-permissions"></a>Konfigurowanie uprawnień dostępu użytkowników

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Użyj następującego polecenia, aby przypisać rolę (musi być uruchamiana przez użytkownika z [odpowiednimi uprawnieniami](#prerequisites-permission-requirements) w ramach subskrypcji platformy Azure). Polecenie wymaga przekazania *nazwy głównej użytkownika* na koncie usługi Azure AD dla użytkownika, do którego ma zostać przypisana rola. W większości przypadków będzie to zgodne z wiadomościami e-mail użytkownika na koncie usługi Azure AD.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli.

> [!NOTE]
> Jeśli to polecenie zwróci błąd informujący, że interfejs wiersza polecenia **nie może odnaleźć nazwy głównej użytkownika lub usługi w usłudze Graph Database**:
>
> Zamiast tego Przypisz rolę przy użyciu *identyfikatora obiektu* użytkownika. Może się tak zdarzyć w przypadku użytkowników korzystających z osobistych [kont Microsoft (kont MSA)](https://account.microsoft.com/account). 
>
> Użyj [Azure Portal stronie Azure Active Directory użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) , aby wybrać konto użytkownika i otworzyć jego szczegóły. Skopiuj *Identyfikator obiektu użytkownika:*
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Widok strony użytkownika w Azure Portal wyróżniania identyfikatora GUID w polu &quot;Identyfikator obiektu&quot;" lightbox="media/includes/user-id.png":::
>
> Następnie powtórz polecenie listy przypisywania ról przy użyciu *identyfikatora obiektu* użytkownika dla `assignee` powyższego parametru.

### <a name="verify-success"></a>Weryfikowanie sukcesu

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji i masz przypisane uprawnienia do zarządzania nim.

## <a name="next-steps"></a>Następne kroki

Przetestuj poszczególne wywołania interfejsu API REST w wystąpieniu przy użyciu poleceń interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji: 
* [odwołanie AZ DT](/cli/azure/ext/azure-iot/dt)
* [*Instrukcje: korzystanie z interfejsu wiersza polecenia usługi Azure Digital bliźniaczych reprezentacji*](how-to-use-cli.md)

Lub zapoznaj się z tematem jak połączyć aplikację kliencką z wystąpieniem przy użyciu kodu uwierzytelniania:
* [*Instrukcje: zapisywanie kodu uwierzytelniania aplikacji*](how-to-authenticate-client.md)