---
title: Rozwiązywanie problemów z łącznością między funkcją Synapse Studio i magazynem
description: Rozwiązywanie problemów z łącznością między funkcją Synapse Studio i magazynem
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566277"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Rozwiązywanie problemów z łącznością między programem Synapse Studio dla usługi Azure Synapse Analytics i magazynem

W Synapse Studio możesz eksplorować zasoby danych znajdujące się w połączonym magazynie. Ten przewodnik pomoże Ci rozwiązać problemy z łącznością podczas próby uzyskania dostępu do zasobów danych. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Przypadek #1: Konto magazynu nie ma odpowiednich uprawnień

Jeśli Twoje konto magazynu nie ma odpowiednich uprawnień, nie możesz rozszerzyć struktury magazynu za pomocą ustawienia "Dane" > "Połączone" w Synapse Studio. Zobacz poniższy zrzut ekranu objawu problemu. 

Szczegółowy komunikat o błędzie może się różnić, ale ogólnym znaczeniem komunikatu o błędzie jest: "To żądanie nie ma autoryzacji do wykonania tej operacji".

W połączonym węźle magazynu:  
![Problem z łącznością z magazynem 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

W węźle kontenera magazynu:  
![Problem z łącznością z magazynem 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**ROZWIĄZANIE:** Aby przypisać konto do odpowiedniej roli, zobacz Use the Azure Portal to assign an Azure role for access to blob and queue data (Przypisywanie roli platformy Azure w celu uzyskania dostępu do danych obiektów [blob i kolejek)](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Przypadek #2: Nie można wysłać żądania do serwera magazynu

Po wybraniu strzałki w celu rozwinięcia struktury magazynu w obszarze "Dane" — > "Połączone" w Synapse Studio, w panelu po lewej stronie może zostać wyświetlony problem "REQUEST_SEND_ERROR". Zobacz poniższy zrzut ekranu objawu problemu:

W połączonym węźle magazynu:  
![Problem z łącznością z magazynem 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

W węźle kontenera magazynu:  
![Problem z łącznością z magazynem 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Ten problem może mieć kilka możliwych przyczyn:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Zasób magazynu znajduje się za siecią wirtualną, a prywatny punkt końcowy magazynu musi zostać skonfigurowany

**ROZWIĄZANIE:** W tym przypadku należy skonfigurować prywatny punkt końcowy magazynu dla konta magazynu. Aby dowiedzieć się, jak skonfigurować prywatny punkt końcowy magazynu dla sieci wirtualnej, zobacz Use the Azure Portal to assign an Azure role for access to blob and queue data (Przypisywanie roli platformy Azure w celu uzyskania dostępu do danych obiektów [blob i kolejek).](../security/how-to-connect-to-workspace-from-restricted-network.md)

Możesz użyć polecenia "nslookup .dfs.core.windows.net", aby sprawdzić łączność po skonfigurowaniu \<storage-account-name\> prywatnego punktu końcowego magazynu. Powinien zostać zwrócony ciąg podobny do: \<storage-account-name\> ".privatelink.dfs.core.windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Zasób magazynu nie znajduje się za siecią wirtualną, ale punkt końcowy usługi Blob service (Azure AD) nie jest dostępny ze względu na skonfigurowaną zaporę

**ROZWIĄZANIE:** W tym przypadku musisz otworzyć konto magazynu w Azure Portal. W lewym okienku nawigacji przewiń w dół do opcji **Pomoc techniczna i** rozwiązywanie problemów i wybierz pozycję **Sprawdzanie** łączności, aby sprawdzić stan **Blob service (Azure AD).** Jeśli nie jest on dostępny, postępuj zgodnie  z przewodnikiem, aby sprawdzić konfigurację zapór i sieci wirtualnych na stronie konta magazynu. Aby uzyskać więcej informacji na temat zapór magazynu, zobacz Konfigurowanie zapór i sieci wirtualnych [usługi Azure Storage.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Inne problemy do sprawdzenia 

* Zasób magazynu, do których uzyskujesz dostęp, jest Azure Data Lake Storage Gen2 znajduje się za zaporą i siecią wirtualną (ze skonfigurowanym prywatnym punktem końcowym magazynu) w tym samym czasie.
* Zasób kontenera, do których uzyskujesz dostęp, został usunięty lub nie istnieje.
* Krzyżowanie dzierżawy: dzierżawa obszaru roboczego używana przez użytkownika do logowania nie jest taka sama jak dzierżawa konta magazynu. 


## <a name="next-steps"></a>Następne kroki
Jeśli poprzednie kroki nie pomogą rozwiązać problemu, utwórz [bilet pomocy technicznej](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).