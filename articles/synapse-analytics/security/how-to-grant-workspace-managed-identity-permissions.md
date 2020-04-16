---
title: Jak udzielić uprawnień do tożsamości zarządzanej w obszarze roboczym Usługi Azure Synapse
description: Artykuł, który wyjaśnia, jak skonfigurować uprawnienia do tożsamości zarządzanej w obszarze roboczym Usługi Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9f519022fffe98c565c3b2d30f6578b9ebb70c57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428019"
---
# <a name="grant-permissions-to-workspace-managed-identity-preview"></a>Udziel uprawnień do tożsamości zarządzanej obszaru roboczego (wersja zapoznawcza)

W tym artykule dowiesz się, jak udzielić uprawnień do tożsamości zarządzanej w obszarze roboczym synapsa platformy Azure. Uprawnienia z kolei umożliwiają dostęp do pul SQL w obszarze roboczym i konta magazynu ADLS gen2 za pośrednictwem witryny Azure portal.

>[!NOTE]
>Ta tożsamość zarządzana obszaru roboczego będzie określana jako tożsamość zarządzana za pośrednictwem pozostałej części tego dokumentu.

## <a name="grant-the-managed-identity--permissions-to-the-sql-pool"></a>Udzielanie uprawnień tożsamości zarządzanej do puli SQL

Tożsamość zarządzana udziela uprawnień do pul SQL w obszarze roboczym. Po przyznaniu uprawnień można aranżować potoki, które wykonują działania związane z pulą SQL. Podczas tworzenia obszaru roboczego Usługi Azure Synapse przy użyciu witryny Azure Portal, można udzielić uprawnień kontroli tożsamości zarządzanej w pulach SQL.

Wybierz **zabezpieczenia + sieci** podczas tworzenia obszaru roboczego Usługi Azure Synapse. Następnie wybierz **pozycję Grant CONTROL do zarządzanej tożsamości obszaru roboczego w pulach SQL**.

![Uprawnienie KONTROLA dla pul SQL](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-16.png)

## <a name="grant-the-managed-identity-permissions-to-adls-gen2-storage-account"></a>Udzielanie uprawnień tożsamości zarządzanej kontu magazynu ADLS gen2

Konto magazynu ADLS gen2 jest wymagane do utworzenia obszaru roboczego Usługi Azure Synapse. Aby pomyślnie uruchomić pule platformy Spark w obszarze roboczym Usługi Azure Synapse, tożsamość zarządzana usługi Azure Synapse wymaga roli *współautora danych obiektów blob magazynu* na tym koncie magazynu. Aranżacja potoku w usłudze Azure Synapse również korzysta z tej roli.

### <a name="grant-permissions-to-managed-identity-during-workspace-creation"></a>Udziel uprawnień do tożsamości zarządzanej podczas tworzenia obszaru roboczego

Usługa Azure Synapse podejmie próbę przyznania roli współautora danych obiektów blob magazynu do tożsamości zarządzanej po utworzeniu obszaru roboczego Usługi Azure Synapse przy użyciu witryny Azure portal. Szczegóły konta magazynu ADLS gen2 można podać na karcie **Podstawy.**

![Karta Podstawowe informacje w przepływie tworzenia obszaru roboczego](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-1.png)

Wybierz konto magazynu ADLS gen2 i system plików w **polu Nazwa konta** i Nazwa systemu **plików**.

![Podanie danych konta magazynu ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-2.png)

Jeśli twórca obszaru roboczego jest również **właścicielem** konta magazynu ADLS gen2, następnie usługa Azure Synapse przypisze rolę *współautora danych obiektu blob magazynu* do tożsamości zarządzanej. Poniżej znajdują się następujące informacje o wprowadzonych danych konta magazynu.

![Pomyślne przypisanie współautora danych obiektów blob magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-3.png)

Jeśli twórca obszaru roboczego nie jest właścicielem konta magazynu ADLS gen2, a następnie usługi Azure Synapse nie przypisuje roli *współautora danych obiektu blob magazynu* do tożsamości zarządzanej. Komunikat wyświetlany poniżej szczegółów konta magazynu powiadamia twórcę obszaru roboczego, że nie ma wystarczających uprawnień do przyznania roli *współautora danych obiektów blob magazynu* do tożsamości zarządzanej.

![Nieudane przypisywanie współautora danych obiektów blob magazynu](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-4.png)

Jak stwierdza komunikat, nie można utworzyć pul platformy Spark, chyba że *współautor danych obiektu blob magazynu* jest przypisany do tożsamości zarządzanej.

### <a name="grant-permissions-to-managed-identity-after-workspace-creation"></a>Udziel uprawnień do tożsamości zarządzanej po utworzeniu obszaru roboczego

Podczas tworzenia obszaru roboczego, jeśli nie przypisać *współautora danych obiektu blob magazynu* do tożsamości zarządzanej, **właściciel** konta magazynu ADLS gen2 ręcznie przypisuje tę rolę do tożsamości. Poniższe kroki pomogą Ci wykonać ręczne przypisanie.

#### <a name="step-1-navigate-to-the-adls-gen2-storage-account-in-azure-portal"></a>Krok 1: Przejdź do konta magazynu ADLS gen2 w witrynie Azure portal

W witrynie Azure portal otwórz konto magazynu ADLS gen2 i wybierz **pozycję Przegląd** z lewej strony nawigacji. Wystarczy przypisać rolę *współautora danych obiektów blob magazynu* na poziomie kontenera lub systemu plików. Wybierz **kontenery**.  
![Omówienie konta magazynu ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-5.png)

#### <a name="step-2-select-the-container"></a>Krok 2: Wybierz pojemnik

Tożsamość zarządzana powinna mieć dostęp do danych do kontenera (systemu plików), który został podany podczas tworzenia obszaru roboczego. Ten kontener lub system plików można znaleźć w witrynie Azure portal. Otwórz obszar roboczy Usługi Azure Synapse w witrynie Azure portal i wybierz kartę **Przegląd** z lewej strony nawigacji.
![Kontener konta magazynu ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-7.png)


Wybierz ten sam kontener lub system plików, aby przyznać rolę *współautora danych obiektów blob magazynu* do tożsamości zarządzanej.
![Wybór kontenera konta magazynu ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-6.png)

#### <a name="step-3-navigate-to-access-control"></a>Krok 3: Przejdź do kontroli dostępu

Wybierz **pozycję Kontrola dostępu (IAM)**.

![Kontrola dostępu(IAM)](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-8.png)

#### <a name="step-4-add-a-new-role-assignment"></a>Krok 4: Dodawanie nowego przypisania roli

Wybierz pozycję **+ Dodaj**.

![Dodawanie nowego przypisania roli](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-9.png)

#### <a name="step-5-select-the-rbac-role"></a>Krok 5: Wybierz rolę RBAC

Wybierz rolę **Współautor danych obiektów blob magazynu.**

![Wybierz rolę RBAC](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-10.png)

#### <a name="step-6-select-the-azure-ad-security-principal"></a>Krok 6: Wybierz podmiot zabezpieczeń usługi Azure AD

Wybierz **użytkownika, grupę lub jednostkę usługi Azure AD** z listy **rozwijanej Przypisz dostęp do.**

![Wybierz podmiot zabezpieczeń AAD](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-11.png)

#### <a name="step-7-search-for-the-managed-identity"></a>Krok 7: Wyszukaj zarządzana tożsamość

Nazwa tożsamości zarządzanej jest również nazwą obszaru roboczego. Wyszukaj zarządzana tożsamość, wprowadzając nazwę obszaru roboczego Usługi Azure Synapse w **polu Wybierz**. Na liście powinna zostać wyświetlona tożsamość zarządzana.

![Znajdowanie tożsamości zarządzanej](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-12.png)

#### <a name="step-8-select-the-managed-identity"></a>Krok 8: Wybierz tożsamość zarządzana

Wybierz tożsamość zarządzana do **wybranych członków**. Wybierz **pozycję Zapisz,** aby dodać przypisanie roli.

![Wybierz tożsamość zarządzana](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-13.png)

#### <a name="step-9-verify-that-the-storage-blob-data-contributor-role-is-assigned-to-the-managed-identity"></a>Krok 9: Sprawdź, czy rola współautora danych obiektu Blob magazynu jest przypisana do tożsamości zarządzanej

Wybierz **pozycję Kontrola dostępu(IAM),** a następnie wybierz pozycję **Przypisania ról**.

![Weryfikowanie przypisania roli](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-14.png)

Twoja tożsamość zarządzana powinna być widoczna na liście w sekcji **Współautor danych obiektów blob magazynu** z przypisaną do niego rolą *współautora danych obiektów blob magazynu.* 
![Wybór kontenera konta magazynu ADLS gen2](./media/how-to-grant-workspace-managed-identity-permissions/configure-workspace-managed-identity-15.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [tożsamości zarządzanej w obszarze roboczym](./synapse-workspace-managed-identity.md)
