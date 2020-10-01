---
author: baanders
description: plik dołączany dla wymagania roli w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 2f10a6915a3edf673316ae9151b6052442678ef9
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832357"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Warunki wstępne: wymagania dotyczące uprawnień

Aby móc wykonać wszystkie kroki opisane w tym artykule, musisz być sklasyfikowany jako właściciel w ramach subskrypcji platformy Azure. 

Poziom uprawnień można sprawdzić, uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

> [!NOTE]
> Jeśli to polecenie zwróci błąd informujący, że interfejs wiersza polecenia **nie może odnaleźć nazwy głównej użytkownika lub usługi w usłudze Graph Database**:
>
> Użyj *identyfikatora obiektu* użytkownika zamiast wiadomości e-mail w pozostałej części tego artykułu. Może się tak zdarzyć w przypadku użytkowników korzystających z osobistych [kont Microsoft (kont MSA)](https://account.microsoft.com/account). 
>
> Użyj [Azure Portal stronie Azure Active Directory użytkowników](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) , aby wybrać konto użytkownika i otworzyć jego szczegóły. Kopiuj *Identyfikator obiektu użytkownika*:
>
> :::image type="content" source="../articles/digital-twins/media/includes/user-id.png" alt-text="Widok strony użytkownika w Azure Portal wyróżniania identyfikatora GUID w polu &quot;Identyfikator obiektu&quot;" lightbox="../articles/digital-twins/media/includes/user-id.png":::
>
> Następnie powtórz polecenie listy przypisywania ról, używając *identyfikatora obiektu* użytkownika zamiast wiadomości e-mail.

Po uruchomieniu polecenia listy przypisywania ról, jeśli jesteś właścicielem, `roleDefinitionName` wartość w danych wyjściowych jest *właścicielem*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Widok strony użytkownika w Azure Portal wyróżniania identyfikatora GUID w polu &quot;Identyfikator obiektu&quot;":::

Jeśli okaże się, że wartość to *współautor* lub coś innego niż *właściciel*, można wykonać jedną z następujących czynności:
* Skontaktuj się z właścicielem subskrypcji i poproś właściciela o wykonanie kroków opisanych w tym artykule w Twoim imieniu
* Skontaktuj się z właścicielem subskrypcji lub osobą z rolą administratora dostępu użytkowników w ramach subskrypcji i poproś o podniesienie uprawnień do właściciela subskrypcji, aby móc kontynuować pracę. Niezależnie od tego, czy jest to odpowiednie, zależy od organizacji i Twojej roli.