---
title: Bezpieczne Azure Data Lake Analytics dla wielu użytkowników
description: Dowiedz się, jak skonfigurować wielu użytkowników do uruchamiania zadań w Azure Data Lake Analytics.
ms.service: data-lake-analytics
services: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 9006a22c588a7f1456585d40da0b4345145c6d05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132487"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurowanie dostępu użytkownika do informacji o zadaniu w Azure Data Lake Analytics 

W Azure Data Lake Analytics można użyć wielu kont użytkowników lub jednostek usługi do uruchamiania zadań. 

Aby te same użytkownicy widzieli szczegółowe informacje o zadaniach, użytkownicy muszą mieć możliwość odczytywania zawartości folderów zadań. Foldery zadań znajdują się w `/system/` katalogu. 

Jeśli nie skonfigurowano wymaganych uprawnień, użytkownik może zobaczyć błąd: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurowanie dostępu użytkownika do informacji o zadaniu

Aby skonfigurować listy ACL w folderach, można użyć **Kreatora dodawania użytkownika** . Aby uzyskać więcej informacji, zobacz [Dodawanie nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Jeśli potrzebujesz bardziej szczegółowej kontroli lub potrzebujesz skryptu na uprawnienia, Zabezpiecz foldery w następujący sposób:

1. Przyznaj uprawnienia do **wykonywania** (za pośrednictwem listy ACL dostępu) w folderze głównym:
   - /
   
2. Przyznaj uprawnienia do **wykonywania** i **odczytu** (za pośrednictwem listy ACL dostępu i domyślnej listy ACL) w folderach, które zawierają foldery zadań. Na przykład w przypadku konkretnego zadania uruchomionego na 25 maja 2018 te foldery muszą być dostępne:
   - /odzyskiwanie
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Następne kroki
[Dodawanie nowego użytkownika](data-lake-analytics-manage-use-portal.md#add-a-new-user)
