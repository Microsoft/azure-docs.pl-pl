---
title: Obejście ustawień zapory magazynu
description: Ustawienie zapory sieciowej konta magazynu może spowodować niepowodzenie podczas tworzenia obiektu docelowego usługi Azure Blob Storage w pamięci podręcznej Azure HPC. W tym artykule przedstawiono obejście tego problemu do momentu wprowadzenia poprawki oprogramowania.
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.author: v-erkel
ms.openlocfilehash: 6916c79e9110a88beff65d487fac72441382c2f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87092374"
---
# <a name="work-around-blob-storage-account-firewall-settings"></a>Obchodzenie ustawień zapory konta magazynu obiektów blob

Określone ustawienie używane w zaporach konta magazynu może spowodować niepowodzenie tworzenia obiektów docelowych magazynu obiektów BLOB. Zespół pamięci podręcznej platformy Azure HPC działa na podstawie poprawki oprogramowania dla tego problemu, ale można go obejść, wykonując instrukcje podane w tym artykule.

Ustawienie zapory zezwalające na dostęp tylko z poziomu opcji wybrane sieci może uniemożliwić utworzenie przez pamięć podręczną magazynu obiektów BLOB. Ta konfiguracja znajduje się na stronie ustawień **zapory i sieci wirtualnych** konta magazynu.

Problem polega na tym, że usługa pamięci podręcznej używa ukrytej sieci wirtualnej usługi, która jest oddzielona od środowiska klienta. Nie można jawnie autoryzować tej sieci do uzyskiwania dostępu do konta magazynu.

Gdy tworzysz obiekt docelowy magazynu obiektów blob, usługa pamięci podręcznej używa tej sieci do sprawdzenia, czy kontener jest pusty. Jeśli Zapora nie zezwala na dostęp z ukrytej sieci, sprawdzanie kończy się niepowodzeniem, a Tworzenie miejsca docelowego magazynu kończy się niepowodzeniem.

Aby obejść ten problem, tymczasowo Zmień ustawienia zapory podczas tworzenia miejsca docelowego magazynu:

1. Przejdź do strony **zapory i sieci wirtualne** konta magazynu i zmień ustawienie "Zezwalaj na dostęp z" do **wszystkich sieci**.
1. Utwórz obiekt docelowy magazynu obiektów BLOB w pamięci podręcznej platformy Azure HPC.
1. Po pomyślnym utworzeniu miejsca docelowego magazynu Zmień ustawienie zapory konta z powrotem na **wybrane sieci**.

Pamięć podręczna platformy Azure HPC nie używa sieci wirtualnej usługi do uzyskiwania dostępu do końcowego miejsca docelowego magazynu.

Aby uzyskać pomoc dotyczącą tego obejścia, [skontaktuj się z pomocą techniczną firmy Microsoft](hpc-cache-support-ticket.md).
