---
title: Rozwiązywanie problemów z łącznością między funkcją Synapse Studio i magazynem
description: Rozwiązywanie problemów z łącznością między funkcją Synapse Studio i magazynem
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117065"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Rozwiązywanie problemów z łącznością między programem Synapse Studio dla usługi Azure Synapse Analytics i magazynem

W programie Synapse Studio można eksplorować zasoby danych znajdujące się w połączonym magazynie. Ten przewodnik pomoże rozwiązać problemy z łącznością podczas próby uzyskania dostępu do zasobów danych. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>#1 przypadku: brak prawidłowych uprawnień dla konta magazynu

Jeśli konto magazynu nie ma odpowiednich uprawnień, nie można rozszerzyć struktury magazynu za pomocą "danych" — > "połączone" w programie Synapse Studio. Zobacz poniższy zrzut ekranu problemu. 

Szczegółowy komunikat o błędzie może się różnić, ale ogólne znaczenie komunikatu o błędzie to: "to żądanie nie ma autoryzacji do wykonania tej operacji".

W węźle połączonego magazynu:  
![Problem z łącznością z magazynem 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

W węźle kontenera magazynu:  
![Problem z łącznością z magazynem 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Rozwiązanie**: Aby przypisać konto do właściwej roli, zobacz [Używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>#2 przypadku: nie można wysłać żądania do serwera magazynu

Po wybraniu strzałki, aby rozwinąć strukturę magazynu w obszarze "dane" — > "połączone" w programie Synapse Studio, problem "REQUEST_SEND_ERROR" można zobaczyć w lewym panelu. Zobacz poniższy zrzut ekranu problemu:

W węźle połączonego magazynu:  
![Problem z łącznością z magazynem 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

W węźle kontenera magazynu:  
![Problem z łącznością z magazynem 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Może istnieć kilka możliwych przyczyn tego problemu:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Zasób magazynu znajduje się za siecią wirtualną, a prywatny punkt końcowy magazynu musi być skonfigurowany

**Rozwiązanie**: w tym przypadku należy skonfigurować prywatny punkt końcowy magazynu dla konta magazynu. Aby uzyskać informacje na temat konfigurowania prywatnego punktu końcowego magazynu dla sieci wirtualnej, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../security/how-to-connect-to-workspace-from-restricted-network.md).

\<storage-account-name\>Aby sprawdzić łączność po skonfigurowaniu prywatnego punktu końcowego magazynu, można użyć polecenia "nslookup. DFS.Core.Windows.NET". Powinien zwrócić ciąg podobny do: " \<storage-account-name\> . privatelink.DFS.Core.Windows.NET".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Zasób magazynu nie znajduje się za siecią wirtualną, ale punkt końcowy Blob service (Azure AD) nie jest dostępny ze względu na skonfigurowaną zaporę

**Rozwiązanie**: w tym przypadku należy otworzyć konto magazynu w Azure Portal. W lewym okienku nawigacji przewiń w dół, aby **zapewnić obsługę i rozwiązywanie problemów** , a następnie wybierz pozycję **Sprawdzanie łączności** , aby sprawdzić stan łączności usługi **BLOB Service (Azure AD)** . Jeśli nie jest dostępny, postępuj zgodnie z podwyższonym przewodnikiem, aby sprawdzić konfigurację **zapory i sieci wirtualnych** na stronie konta magazynu. Aby uzyskać więcej informacji na temat zapór magazynowych, zobacz [Konfigurowanie zapór usługi Azure Storage i sieci wirtualnych](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Inne problemy do sprawdzenia 

* Zasób magazynu, do którego uzyskujesz dostęp, jest Azure Data Lake Storage Gen2 i znajduje się za zaporą i siecią wirtualną (skonfigurowany prywatny punkt końcowy magazynu) w tym samym czasie.
* Zasób kontenera, do którego uzyskujesz dostęp, został usunięty lub nie istnieje.
* Dzierżawca: dzierżawa obszaru roboczego użyta do zalogowania nie jest taka sama w przypadku dzierżawy konta magazynu. 


## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomogą rozwiązać problemu, [Utwórz bilet pomocy technicznej](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).