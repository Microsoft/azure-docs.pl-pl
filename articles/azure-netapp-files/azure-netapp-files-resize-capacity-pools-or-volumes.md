---
title: Zmień rozmiar puli pojemności lub woluminu dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób zmiany rozmiaru puli pojemności lub woluminu.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 7da604e8e49b0732680e5f641d1ff6e899ad474d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483486"
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

