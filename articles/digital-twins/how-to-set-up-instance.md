---
title: Tworzenie wystąpienia usługi Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Zobacz jak skonfigurować wystąpienie usługi Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: c9489b9e1afe5e42121f61a3b0b50b28b2401bd3
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392288"
---
# <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

W tym artykule przedstawiono podstawowe kroki konfigurowania nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji. Obejmuje to utworzenie wystąpienia i przypisanie do niego uprawnień [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) .

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="set-up-an-azure-digital-twins-instance"></a>Konfigurowanie wystąpienia usługi Azure Digital bliźniaczych reprezentacji

Następnie uruchom następujące polecenia, aby utworzyć nową grupę zasobów platformy Azure do użycia w ramach tej procedury, a następnie utwórz nowe wystąpienie usługi Azure Digital bliźniaczych reprezentacji w tej grupie zasobów.

```azurecli
az group create --location <region> --name <name-for-your-resource-group>
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group> -l <region>
```

> [!TIP]
> Aby uzyskać listę nazw regionów platformy Azure, które mogą być przekazywane do poleceń w interfejsie wiersza polecenia platformy Azure, uruchom następujące polecenie:
> ```azurecli
> az account list-locations -o table
> ```
> Aby zobaczyć, które regiony obsługują usługę Azure Digital bliźniaczych reprezentacji, odwiedź stronę [usługi Azure dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).

Wyniki tych poleceń wyglądają podobnie jak w przypadku podania informacji o utworzonych zasobach:

:::image type="content" source="media/how-to-set-up-instance/create-instance.png" alt-text="okno Polecenie pomyślne utworzenie grupy zasobów i wystąpienia usługi Azure Digital bliźniaczych reprezentacji":::

Zwróć uwagę na nazwy *hosta*, *nazwę*i *źródłową* wystąpienia usługi Azure Digital bliźniaczych reprezentacji z danych wyjściowych. Są to wszystkie kluczowe wartości, które mogą być potrzebne podczas dalszej pracy z wystąpieniem usługi Azure Digital bliźniaczych reprezentacji, aby skonfigurować uwierzytelnianie i powiązane zasoby platformy Azure.

> [!TIP]
> Można wyświetlić te właściwości wraz ze wszystkimi właściwościami wystąpienia w dowolnym momencie, uruchamiając polecenie `az dt show --dt-name <your-Azure-Digital-Twins-instance>` .

### <a name="assign-azure-active-directory-permissions"></a>Przypisywanie uprawnień Azure Active Directory

Usługa Azure Digital bliźniaczych reprezentacji korzysta z usługi [Azure Active Directory (AAD)](../active-directory/fundamentals/active-directory-whatis.md) na potrzeby kontroli dostępu opartej na ROLACH (RBAC). Oznacza to, że przed przystąpieniem do wykonywania wywołań płaszczyzny danych do wystąpienia usługi Azure Digital bliźniaczych reprezentacji należy najpierw przypisać rolę z tymi uprawnieniami.

Aby móc używać usługi Azure Digital bliźniaczych reprezentacji z aplikacją kliencką, należy również upewnić się, że aplikacja kliencka może być uwierzytelniana w usłudze Azure Digital bliźniaczych reprezentacji. W tym celu należy skonfigurować rejestrację aplikacji Azure Active Directory (AAD), którą można przeczytać w temacie [jak to zrobić: uwierzytelnianie aplikacji klienckiej](how-to-authenticate-client.md).

#### <a name="assign-yourself-a-role"></a>Przypisywanie siebie do roli

Utwórz przypisanie roli dla siebie, używając poczty e-mail skojarzonej z dzierżawą usługi AAD w ramach subskrypcji platformy Azure. 

Najpierw upewnij się, że jesteś sklasyfikowany jako właściciel w ramach subskrypcji platformy Azure. Można to sprawdzić za pomocą `az role assignment list --assignee <your-Azure-email>` polecenia i sprawdzając, czy wartość *RoleDefinitionName* jest *właścicielem*. Jako właściciel subskrypcji możesz użyć następującego polecenia, aby przypisać użytkownika do roli właściciela dla wystąpienia bliźniaczych reprezentacji Digital Azure:

```azurecli
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-AAD-email>" --role "Azure Digital Twins Owner (Preview)"
```

Wynikiem tego polecenia jest informacje o utworzonym przypisaniu roli.

> [!TIP]
> Jeśli zostanie wyświetlony komunikat o błędzie *400: nieprawidłowego żądania* , uruchom następujące polecenie, aby uzyskać identyfikator *objectid* dla użytkownika:
> ```azurecli
> az ad user show --id <your-AAD-email> --query objectId
> ```
> Następnie powtórz polecenie przypisania roli, używając *identyfikatora obiektu* użytkownika zamiast wiadomości e-mail.

Masz teraz już gotowe do użycia wystąpienie usługi Azure Digital bliźniaczych reprezentacji.

## <a name="next-steps"></a>Następne kroki

Zobacz jak skonfigurować i uwierzytelnić aplikację kliencką do pracy z wystąpieniem:
* [Instrukcje: uwierzytelnianie aplikacji klienckiej](how-to-authenticate-client.md)
