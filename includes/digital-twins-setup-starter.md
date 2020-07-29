---
author: baanders
description: plik dołączany dla informacji Starter w usłudze Azure Digital bliźniaczych reprezentacji Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: dcc616dd8d30bfadb751df9ba24d71d72675428a
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87371462"
---
>[!NOTE]
>Te operacje mają zostać wykonane przez użytkownika z rolą *właściciela* w ramach subskrypcji platformy Azure. Chociaż niektóre elementy można wykonać bez tego podwyższonego poziomu uprawnień, współpraca właściciela będzie wymagała całkowitego skonfigurowania użytecznego wystąpienia. Więcej informacji na ten temat znajduje się w sekcji [*wymagania wstępne: wymagane uprawnienia*](#prerequisites-permission-requirements) poniżej.

Pełna Konfiguracja nowego wystąpienia usługi Azure Digital bliźniaczych reprezentacji składa się z trzech części:
1. **Tworzenie wystąpienia**
2. **Konfigurowanie uprawnień dostępu użytkownika**: użytkownik platformy Azure musi mieć rolę *właściciela Digital bliźniaczych reprezentacji (wersja zapoznawcza)* na tym wystąpieniu, aby móc wykonywać działania związane z zarządzaniem. W tym kroku połączysz się z tą rolą (Jeśli jesteś właścicielem w ramach subskrypcji platformy Azure) lub skontaktuj się z właścicielem subskrypcji, aby przypisać go do Ciebie.
3. **Konfigurowanie uprawnień dostępu dla aplikacji klienckich**: często można napisać aplikację kliencką używaną do współpracy z wystąpieniem. Aby aplikacja kliencka mogła uzyskać dostęp do usługi Azure Digital bliźniaczych reprezentacji, należy skonfigurować *rejestrację aplikacji* w [Azure Active Directory](../articles/active-directory/fundamentals/active-directory-whatis.md) , która będzie używana przez aplikację kliencką do uwierzytelniania w wystąpieniu.

Aby można było to zrobić, będzie potrzebna subskrypcja platformy Azure. Możesz bezpłatnie skonfigurować jedno z [nich.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Warunki wstępne: wymagania dotyczące uprawnień

Aby móc wykonać wszystkie kroki opisane w tym artykule, musisz być sklasyfikowany jako właściciel w ramach subskrypcji platformy Azure. 

Poziom uprawnień można sprawdzić, uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Jeśli jesteś właścicielem, `roleDefinitionName` wartość w danych wyjściowych jest *właścicielem*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/owner-role.png" alt-text="Okno Cloud Shell pokazujące dane wyjściowe polecenia AZ role przypisania list":::

Jeśli okaże się, że wartość to *współautor* lub coś innego niż *właściciel*, można wykonać jedną z następujących czynności:
* Skontaktuj się z właścicielem subskrypcji i poproś właściciela o wykonanie kroków opisanych w tym artykule w Twoim imieniu
* Skontaktuj się z właścicielem subskrypcji lub osobą z rolą administratora dostępu użytkowników w ramach subskrypcji i poproś o podniesienie uprawnień do właściciela subskrypcji, aby móc kontynuować pracę. Niezależnie od tego, czy jest to odpowiednie, zależy od organizacji i Twojej roli.
