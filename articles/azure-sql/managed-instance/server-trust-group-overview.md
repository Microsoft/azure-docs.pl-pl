---
title: Grupa zaufania serwera
titleSuffix: Azure SQL Managed Instance
description: Informacje o grupie zaufania serwera i sposobach zarządzania relacją zaufania między wystąpieniami zarządzanymi usługi Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 911d7ffa2b1d313147ca73d0ceb285ea2e84b1f7
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91979626"
---
# <a name="use-server-trust-groups-to-setup-and-manage-trust-between-sql-managed-instances"></a>Używanie grup zaufania serwera do konfigurowania zaufania między wystąpieniami zarządzanymi SQL i zarządzania nimi
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Grupa zaufania serwera to koncepcja służąca do zarządzania relacją zaufania między wystąpieniami zarządzanymi usługi Azure SQL. Utworzenie grupy i dodanie wystąpień do zaufania opartego na certyfikacie jest ustanawiane między wszystkimi elementami członkowskimi. można go używać w różnych scenariuszach obejmujących wiele wystąpień. Usunięcie serwerów z grupy lub usunięcie grupy skutkuje usunięciem relacji zaufania między serwerami. Aby utworzyć lub usunąć grupę zaufania serwera, użytkownik musi mieć uprawnienia do zapisu w wystąpieniu zarządzanym.
[Grupa zaufania serwera](https://aka.ms/mi-server-trust-group-arm) jest obiektem Azure Resource Manager, który odnosi się do Azure Portal jednostki o nazwie **Grupa zaufania SQL**.

> [!NOTE]
> Grupa zaufania serwera jest wprowadzana w publicznej wersji zapoznawczej transakcji rozproszonych między wystąpieniami zarządzanymi usługi Azure SQL. Obecnie istnieją pewne ograniczenia, które zostaną opisane w dalszej części tego artykułu.

## <a name="server-trust-group-setup"></a>Konfiguracja grupy zaufania serwera

W poniższej sekcji opisano konfigurowanie grupy zaufania serwera.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/).

2. Przejdź do wystąpienia zarządzanego usługi Azure SQL, które ma zostać dodane do nowo utworzonej grupy zaufania serwera.

3. Na stronie ustawienia **zabezpieczeń** wybierz kartę **grupy zaufania SQL** .

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="Grupy zaufania serwera":::

4. Na stronie Konfiguracja grupy zaufania serwera wybierz ikonę **Nowa grupa** .

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="Grupy zaufania serwera":::

5. W bloku Tworzenie **grupy zaufania SQL** wybierz **nazwę grupy**. Musi być unikatowa we wszystkich regionach, w których znajdują się członkowie grupy. **Zakres zaufania** definiuje typ scenariusza obejmującego wiele wystąpień, który jest włączony w grupie zaufania serwera i w wersji zapoznawczej jedynym odpowiednim zakresem zaufania są **transakcje rozproszone**, więc jest to wstępne zaznaczenie i nie można go zmienić. Wszyscy **członkowie grupy** muszą należeć do tej samej **subskrypcji** , ale mogą znajdować się w różnych grupach zasobów. Wybierz **grupę zasobów** i **SQL Server/wystąpienie** , aby wybrać wystąpienie zarządzane Azure SQL, które ma być członkiem grupy.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="Grupy zaufania serwera":::

6. Po wypełnieniu wszystkich wymaganych pól kliknij przycisk **Zapisz**.

## <a name="server-trust-group-maintenance-and-deletion"></a>Obsługa i usuwanie grupy zaufania serwera

Nie ma możliwości edytowania grupy zaufania serwera. Aby usunąć wystąpienie zarządzane z grupy, należy usunąć grupę i utworzyć nową.

W poniższej sekcji opisano proces usuwania grupy zaufania serwera. 
1. Przejdź do witryny Azure Portal.
2. Przejdź do wystąpienia zarządzanego, które należy do grupy zaufania.
3. Na stronie ustawienia **zabezpieczeń** wybierz kartę **grupy zaufania SQL** .
4. Wybierz grupę zaufania, którą chcesz usunąć.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="Grupy zaufania serwera":::
5. Kliknij pozycję **Usuń grupę**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="Grupy zaufania serwera":::
6. Wpisz nazwę grupy zaufania serwera, aby potwierdzić usunięcie, a następnie kliknij przycisk **Usuń**.
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="Grupy zaufania serwera":::

> [!NOTE]
> Usunięcie grupy zaufania serwera może nie natychmiast usunąć relacji zaufania między dwoma wystąpieniami zarządzanymi. Usunięcie zaufania można wymusić, wywołując w [trybie failover](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) wystąpienia zarządzane. Sprawdź [znane problemy](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) z najnowszymi aktualizacjami.

## <a name="limitations"></a>Ograniczenia

W publicznej wersji zapoznawczej następujące ograniczenia mają zastosowanie do grup zaufania serwera.
 * Nazwa grupy zaufania serwera musi być unikatowa we wszystkich regionach, w których są członkami.
 * Grupa może zawierać tylko wystąpienia zarządzane usługi Azure SQL, które muszą znajdować się w tej samej subskrypcji platformy Azure.
 * Grupa może mieć dokładnie dwa wystąpienia zarządzane. Jeśli trzeba wykonać transakcje rozproszone w więcej niż dwóch wystąpieniach zarządzanych, które można wykonać, tworząc grupę zaufania serwera dla każdej pary wystąpień zarządzanych.
 * Transakcje rozproszone są jedynym odpowiednim zakresem dla grup zaufania serwera.
 * Grupa zaufania serwera może być zarządzana tylko z poziomu Azure Portal. Obsługa programu PowerShell i interfejsu wiersza polecenia będzie późniejsza.
 * Nie można edytować grupy zaufania serwera w Azure Portal. Można go utworzyć lub porzucić.
 * Dodatkowe ograniczenia transakcji rozproszonych mogą być powiązane z Twoim scenariuszem. Najbardziej istotny jest to, że wystąpienia zarządzane SQL, które uczestniczą w grupie zaufania serwera, muszą być osiągalne za pośrednictwem prywatnych punktów końcowych lub w innej łączności musi działać na poziomie sieci wirtualnej. Upewnij się, że znasz bieżące [ograniczenia dotyczące transakcji rozproszonych dla wystąpienia zarządzanego Azure SQL](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations).

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat transakcji rozproszonych w wystąpieniu zarządzanym usługi Azure SQL, zobacz [transakcje rozproszone](../database/elastic-transactions-overview.md).
* Aby uzyskać informacje o aktualizacjach wersji i stanie znanych problemów, zobacz [Informacje o wersji wystąpienia zarządzanego](../database/doc-changes-updates-release-notes.md).
* W przypadku żądań funkcji należy dodać je do [forum wystąpienia zarządzanego](https://feedback.azure.com/forums/915676-sql-managed-instance).