---
title: Użyj odbiornika nazwy sieci rozproszonej (DNN) zamiast odbiornika VNN dla grup dostępności na SQL Server maszynach wirtualnych.
description: Komunikat, aby przekierować klientów do korzystania z odbiornika DNN zamiast odbiornika VNN.
ms.topic: include
author: MashaMSFT
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4ea8428ae23537cde584bf6944732f65ebd2f26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92168000"
---
> [!NOTE]
> Klienci korzystający z SQL Server 2019 CU8 i nowszych w systemie Windows 2016 i nowszych mogą zastąpić tradycyjny odbiornik VNN i Azure Load Balancer z [odbiornikiem nazwa sieci rozproszonej (DNN)](../virtual-machines/windows/availability-group-distributed-network-name-dnn-listener-configure.md) . Pomiń pozostałe kroki opisane w tym artykule, które tworzą odbiornik i moduł równoważenia obciążenia.
