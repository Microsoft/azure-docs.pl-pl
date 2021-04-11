---
title: Rozwiązywanie problemów z woluminem LDAP Azure NetApp Files | Microsoft Docs
description: Opisuje rozwiązania dotyczące warunków błędów, które mogą wystąpić podczas konfigurowania woluminów LDAP dla Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.author: b-juche
ms.openlocfilehash: eea3f691bc6d91948dc73b4a02c89abfac12d384
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106499031"
---
# <a name="troubleshoot-ldap-volume-issues"></a>Rozwiązywanie problemów z woluminami LDAP

W tym artykule opisano rozwiązania przyczyn błędów, które mogą wystąpić podczas konfigurowania woluminów LDAP.

## <a name="errors-and-resolutions-for-ldap-volumes"></a>Błędy i rozwiązania dla woluminów LDAP

|     Warunki błędów    |     Rozwiązania    |
|-|-|
| Wystąpił błąd podczas tworzenia woluminu SMB z ldapEnabled jako true: <br> `Error Message: ldapEnabled option is only supported with NFS protocol volume. ` | Nie można utworzyć woluminu SMB z włączonym protokołem LDAP. <br> Utwórz woluminy SMB z wyłączonym protokołem LDAP. |
| Wystąpił błąd podczas aktualizowania wartości parametru ldapEnabled dla istniejącego woluminu: <br> `Error Message: ldapEnabled parameter is not allowed to update` |  Nie można zmodyfikować ustawienia opcji LDAP po utworzeniu woluminu. <br> Nie Aktualizuj ustawienia opcji LDAP na utworzonym woluminie. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie dodawania protokołu LDAP z rozszerzonymi grupami dla dostępu do woluminu systemu plików NFS](configure-ldap-extended-groups.md) . |
| Wystąpił błąd podczas tworzenia woluminu NFS z włączoną obsługą protokołu LDAP: <br> `Could not query DNS server` <br> `Sample error message:` <br> `"log": time="2020-10-21 05:04:04.300" level=info msg=Res method=GET url=/v2/Volumes/070d0d72-d82c-c893-8ce3-17894e56cea3  x-correlation-id=9bb9e9fe-abb6-4eb5-a1e4-9e5fbb838813 x-request-id=c8032cb4-2453-05a9-6d61-31ca4a922d85 xresp="200:  {\"created\":\"2020-10-21T05:02:55.000Z\",\"lifeCycleState\":\"error\",\"lifeCycleStateDetails\":\"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available.\",\"name\":\"smb1\",\"ownerId\ \":\"8c925a51-b913-11e9-b0de-9af5941b8ed0\",\"region\":\"westus2stage\",\"volumeId\":\"070d0d72-d82c-c893-8ce3-` |  Ten błąd występuje, ponieważ usługa DNS jest nieosiągalna. <br> <ul><li> Sprawdź, czy skonfigurowano poprawną lokację (zakres lokacji) dla Azure NetApp Files. </li><li> Przyczyną niedostępności systemu DNS może być niepoprawny adres IP lub problemy z siecią DNS. Sprawdź adres IP DNS wprowadzony w połączeniu z usługą AD, aby upewnić się, że jest on poprawny. </li><li> Upewnij się, że usługi AD i wolumin znajdują się w tym samym regionie i tej samej sieci wirtualnej. Jeśli znajdują się w różnych sieci wirtualnychach, upewnij się, że Komunikacja równorzędna sieci wirtualnej jest ustanowiona między tymi dwoma sieci wirtualnych.</li></ul> |
| Wystąpił błąd podczas tworzenia woluminu na podstawie migawki: <br> `Aggregate does not exist` | Azure NetApp Files nie obsługuje aprowizacji nowego woluminu z włączoną obsługą protokołu LDAP z migawki, która należy do woluminu wyłączonego z LDAP. <br> Spróbuj utworzyć nowy wolumin wyłączony z protokołem LDAP z danej migawki. |

## <a name="next-steps"></a>Następne kroki  

* [Konfiguracja dodaje protokół LDAP z rozszerzonymi grupami dla dostępu do woluminu NFS](configure-ldap-extended-groups.md)
* [Tworzenie woluminu NFS dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Tworzenie woluminu Dual-Protocol (NFSv3 i SMB) dla Azure NetApp Files](create-volumes-dual-protocol.md)