---
title: Zmień rozmiar puli pojemności lub woluminu dla Azure NetApp Files | Microsoft Docs
description: Dowiedz się, jak zmienić rozmiar puli pojemności lub woluminu. Zmiana rozmiarów puli pojemności zmienia zakupione Azure NetApp Files pojemności.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012480"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Zmienianie rozmiaru puli pojemności lub woluminu
W razie potrzeby można zmienić rozmiar puli pojemności lub woluminu. 

## <a name="resize-the-capacity-pool"></a>Zmiana rozmiaru puli pojemności 

Rozmiar puli pojemności można zmienić w 1-TiB przyrostach lub zmniejszeniu. Jednak rozmiar puli pojemności nie może być mniejszy niż 4 TiB. Zmiana rozmiarów puli pojemności zmienia zakupione Azure NetApp Files pojemności.

1. W bloku Zarządzanie kontem usługi NetApp kliknij pulę pojemności, której rozmiar chcesz zmienić. 
2. Kliknij prawym przyciskiem myszy nazwę puli pojemności lub kliknij "..." na końcu wiersza puli pojemności, aby wyświetlić menu kontekstowe. 
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć pulę pojemności.

## <a name="resize-a-volume"></a>Zmiana rozmiaru woluminu

W razie potrzeby można zmienić rozmiar woluminu. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.

1. W bloku Zarządzanie kontem usługi NetApp kliknij pozycję **woluminy**. 
2. Kliknij prawym przyciskiem myszy nazwę woluminu, którego rozmiar chcesz zmienić, lub kliknij "..." na końcu wiersza woluminu, aby wyświetlić menu kontekstowe.
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć wolumin.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
- [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.md) 