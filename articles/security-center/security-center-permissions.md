---
title: Uprawnienia w Azure Security Center | Microsoft Docs
description: W tym artykule wyjaśniono, jak Azure Security Center używa kontroli dostępu opartej na rolach do przypisywania uprawnień użytkownikom i identyfikowania dozwolonych akcji dla każdej roli.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 01/03/2021
ms.author: memildin
ms.openlocfilehash: dcbb4977e1bfd17f0cbed61abf9ba335615b7799
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443764"
---
# <a name="permissions-in-azure-security-center"></a>Uprawnienia w usłudze Azure Security Center

Azure Security Center używa [kontroli dostępu opartej na rolach platformy Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md), która udostępnia [wbudowane role](../role-based-access-control/built-in-roles.md) , które można przypisać do użytkowników, grup i usług na platformie Azure.

Security Center ocenia konfigurację zasobów w celu zidentyfikowania problemów i luk w zabezpieczeniach. W Security Center widoczne są tylko informacje związane z zasobem, gdy przypiszesz rolę właściciela, współautora lub czytelnika dla subskrypcji lub grupy zasobów, do której należy zasób.

Oprócz tych ról istnieją dwie określone role usługi Security Center:

* **Czytelnik zabezpieczeń**: użytkownik, który należy do tej roli, ma uprawnienia do wyświetlania Security Center. Użytkownik może wyświetlać zalecenia, alerty, zasady zabezpieczeń i Stany zabezpieczeń, ale nie może wprowadzać zmian.
* **Administrator zabezpieczeń**: użytkownik, który należy do tej roli, ma takie same prawa jak czytelnik zabezpieczeń, a także może zaktualizować zasady zabezpieczeń i odrzucić alerty i zalecenia.

> [!NOTE]
> Role zabezpieczeń, czytelnik zabezpieczeń i administrator zabezpieczeń mają dostęp tylko w Security Center. Role zabezpieczeń nie mają dostępu do innych obszarów usługi platformy Azure, takich jak Storage, Web & Mobile lub Internet rzeczy.
>

## <a name="roles-and-allowed-actions"></a>Role i dozwolone akcje

W poniższej tabeli przedstawiono role i dozwolone akcje w Security Center.

| Akcja                                                                                                                                        | Czytelnik zabezpieczeń/ <br> Czytelnik | Administrator zabezpieczeń | Współautor grupy zasobów/ <br> Właściciel grupy zasobów | Współautor subskrypcji | Właściciel subskrypcji |
|:----------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
| Edytuj zasady zabezpieczeń                                                                                                                          | -                             | ✔             | -                                                      | -                        | ✔                 |
| Dodaj/Przypisz inicjatywy (w tym) normy zgodności z przepisami)                                                                           | -                             | -              | -                                                      | -                        | ✔                 |
| Włączanie/wyłączanie usługi Azure Defender                                                                                                               | -                             | ✔             | -                                                      | -                        | ✔                 |
| Włącz/Wyłącz funkcję autoaprowizacji                                                                                                            | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Stosowanie zaleceń dotyczących zabezpieczeń dla zasobu</br> (i Użyj [szybkiej poprawki!](security-center-remediate-recommendations.md#quick-fix-remediation)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Odrzuć alerty                                                                                                                                | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Wyświetlanie alertów i zaleceń                                                                                                               | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |

> [!NOTE]
> Zaleca się przypisanie użytkownikom najbardziej ograniczonej roli wystarczającej do wykonywania zadań. Na przykład Przypisz rolę czytelnik do użytkowników, którzy muszą jedynie wyświetlać informacje o kondycji zabezpieczeń zasobu, ale nie podejmować działań, takich jak stosowanie zaleceń lub edytowanie zasad.
>
>

## <a name="next-steps"></a>Następne kroki
W tym artykule wyjaśniono, jak Security Center używać usługi Azure RBAC do przypisywania uprawnień użytkownikom i identyfikowania dozwolonych akcji dla każdej roli. Teraz, po zapoznaniu się z przypisaniami ról potrzebnymi do monitorowania stanu zabezpieczeń subskrypcji, edytowania zasad zabezpieczeń i stosowania zaleceń, Dowiedz się, jak:

- [Ustawianie zasad zabezpieczeń w usłudze Security Center](tutorial-security-policy.md)
- [Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Security Center](security-center-recommendations.md)
- [Zarządzanie alertami zabezpieczeń i reagowanie na nie w Security Center](security-center-managing-and-responding-alerts.md)
- [Monitorowanie rozwiązań zabezpieczeń partnerów](./security-center-partner-integration.md)