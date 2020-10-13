---
title: Jak chronić hierarchię zasobów — zarządzanie platformą Azure
description: Dowiedz się, jak chronić hierarchię zasobów przy użyciu ustawień hierarchii, które obejmują ustawienie domyślnej grupy zarządzania.
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 8630562786da922a36baa3bec4863acbb21b197d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91533983"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>Jak chronić hierarchię zasobów

Zasoby, grupy zasobów, subskrypcje, grupy zarządzania i dzierżawcę zbiorowo tworzą hierarchię zasobów. Ustawienia w głównej grupie zarządzania, takie jak role niestandardowe platformy Azure lub przypisania zasad Azure Policy, mogą mieć wpływ na każdy zasób w hierarchii zasobów. Ważne jest, aby chronić hierarchię zasobów przed zmianami, które mogą mieć negatywny wpływ na wszystkie zasoby.

Grupy zarządzania mają teraz ustawienia hierarchii, które umożliwiają administratorowi dzierżawy sterowanie tymi zachowaniami. W tym artykule opisano wszystkie dostępne ustawienia hierarchii oraz sposób ich konfigurowania.

## <a name="azure-rbac-permissions-for-hierarchy-settings"></a>Uprawnienia kontroli RBAC platformy Azure dla ustawień hierarchii

Skonfigurowanie dowolnych ustawień hierarchii wymaga dwóch następujących operacji dostawcy zasobów w głównej grupie zarządzania:

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

Te operacje umożliwiają użytkownikowi odczytywanie i aktualizowanie ustawień hierarchii. Operacje nie zapewniają żadnych innych dostępów do hierarchii lub zasobów grupy zarządzania. Obie te operacje są dostępne w **Administratorze ustawień hierarchii**ról wbudowanej platformy Azure.

## <a name="setting---default-management-group"></a>Ustawienie — domyślna grupa zarządzania

Domyślnie Nowa subskrypcja dodana w ramach dzierżawy jest dodawana jako członek głównej grupy zarządzania. Jeśli przydziały zasad, kontrola dostępu oparta na rolach (Azure RBAC) i inne konstrukcje ładu są przypisane do głównej grupy zarządzania, natychmiast wpływają na te nowe subskrypcje. Z tego powodu wiele organizacji nie stosuje tych konstrukcji w głównej grupie zarządzania, nawet jeśli jest to wymagane miejsce do przypisywania. W innych przypadkach bardziej restrykcyjny zestaw kontrolek jest wymagany w przypadku nowych subskrypcji, ale nie powinien być przypisany do wszystkich subskrypcji. To ustawienie obsługuje oba przypadki użycia.

Zezwalając na zdefiniowanie domyślnej grupy zarządzania dla nowych subskrypcji, można zastosować konstrukcje ładu dla całej organizacji w głównej grupie zarządzania, a także oddzielną grupę zarządzania z przypisaniami zasad lub przypisaniami ról platformy Azure bardziej przystosowanymi do nowej subskrypcji.

### <a name="set-default-management-group-in-portal"></a>Ustaw domyślną grupę zarządzania w portalu

Aby skonfigurować to ustawienie w Azure Portal, wykonaj następujące kroki:

1. Użyj paska wyszukiwania, aby wyszukać i wybrać pozycję "grupy zarządzania".

1. W głównej grupie zarządzania wybierz pozycję **szczegóły** obok nazwy grupy zarządzania.

1. W obszarze **Ustawienia**wybierz pozycję **Ustawienia hierarchii**.

1. Wybierz przycisk **Zmień domyślną grupę zarządzania** .

   > [!NOTE]
   > Jeśli przycisk **Zmień domyślną grupę** zarządzania jest wyłączony, wyświetlana grupa zarządzania nie jest główną grupą zarządzania lub podmiot zabezpieczeń nie ma wystarczających uprawnień, aby zmienić ustawienia hierarchii.

1. Wybierz grupę zarządzania z hierarchii i użyj przycisku **Wybierz** .

### <a name="set-default-management-group-with-rest-api"></a>Ustawianie domyślnej grupy zarządzania za pomocą interfejsu API REST

Aby skonfigurować to ustawienie za pomocą interfejsu API REST, zostanie wywołany punkt końcowy [ustawień hierarchii](/rest/api/resources/hierarchysettings) . W tym celu należy użyć poniższego identyfikatora URI interfejsu API REST i formatu treści. Zastąp ciąg `{rootMgID}` identyfikatorem głównej grupy zarządzania i `{defaultGroupID}` identyfikatorem grupy zarządzania, aby stał się domyślną grupą zarządzania:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

Aby ustawić domyślną grupę zarządzania z powrotem do głównej grupy zarządzania, należy użyć tego samego punktu końcowego i ustawić **defaultManagementGroup** na wartość `/providers/Microsoft.Management/managementGroups/{rootMgID}` .

## <a name="setting---require-authorization"></a>Ustawienie — Wymagaj autoryzacji

Wszyscy użytkownicy domyślnie mogą tworzyć nowe grupy zarządzania w ramach dzierżawy. Administratorzy dzierżawy mogą chcieć udzielić tych uprawnień tylko określonym użytkownikom, aby zachować spójność i zgodność w hierarchii grupy zarządzania. Jeśli ta funkcja jest włączona, użytkownik musi wykonać `Microsoft.Management/managementGroups/write` operację w głównej grupie zarządzania, aby utworzyć nowe podrzędne grupy zarządzania.

### <a name="set-require-authorization-in-portal"></a>Ustawianie opcji Wymagaj autoryzacji w portalu

Aby skonfigurować to ustawienie w Azure Portal, wykonaj następujące kroki:

1. Użyj paska wyszukiwania, aby wyszukać i wybrać pozycję "grupy zarządzania".

1. W głównej grupie zarządzania wybierz pozycję **szczegóły** obok nazwy grupy zarządzania.

1. W obszarze **Ustawienia**wybierz pozycję **Ustawienia hierarchii**.

1. Przełącz **uprawnienia wymagane do tworzenia nowych grup zarządzania.** opcja włączona.

   > [!NOTE]
   > Jeśli **wymagane są uprawnienia do tworzenia nowych grup zarządzania.** przełącznik jest wyłączony. wyświetlana grupa zarządzania nie jest główną grupą zarządzania lub podmiot zabezpieczeń nie ma wystarczających uprawnień, aby zmienić ustawienia hierarchii.

### <a name="set-require-authorization-with-rest-api"></a>Ustaw wymaganie autoryzacji za pomocą interfejsu API REST

Aby skonfigurować to ustawienie za pomocą interfejsu API REST, zostanie wywołany punkt końcowy [ustawień hierarchii](/rest/api/resources/hierarchysettings) . W tym celu należy użyć poniższego identyfikatora URI interfejsu API REST i formatu treści. Ta wartość jest wartością _logiczną_, dlatego dla wartości należy podać wartość **true** lub **false** . Wartość **true** włącza tę metodę ochrony hierarchii grup zarządzania:

- Identyfikator URI interfejsu API REST

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- Treść żądania

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

Aby wyłączyć ustawienie, użyj tego samego punktu końcowego i ustaw **requireAuthorizationForGroupCreation** na wartość **false**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat grup zarządzania, zobacz:

- [Tworzenie grup zarządzania w celu organizowania zasobów platformy Azure](../create.md)
- [Jak zmienić lub usunąć grupy zarządzania oraz zarządzać nimi](../manage.md)
