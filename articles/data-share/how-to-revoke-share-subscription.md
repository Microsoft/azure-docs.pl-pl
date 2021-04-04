---
title: Odwoływanie subskrypcji udostępniania w udziale danych platformy Azure
description: Dowiedz się, jak odwołać subskrypcję udziału z odbiorcy przy użyciu udziału danych platformy Azure.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 102a0099c95073793ba6a1a85f518c7539327bf3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87511857"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Jak odwołać subskrypcję udziału użytkownika w udziale danych platformy Azure

W tym artykule wyjaśniono, jak odwołać subskrypcję udziału z co najmniej jednego użytkownika korzystającego z udziału danych platformy Azure. Zapobiega to wyzwoleniu większej liczby migawek przez konsumenta. Jeśli użytkownik nie wyzwolił jeszcze migawki, nigdy nie otrzyma danych po odwołaniu subskrypcji udziału. Jeśli wcześniej wyzwoliły migawkę, najnowsze dane, które mają pozostawać w ich koncie.

## <a name="navigate-to-a-sent-data-share"></a>Przejdź do wysłanego udziału danych

W udziale danych platformy Azure przejdź do wysłanego udziału i wybierz kartę **udostępnianie subskrypcji** .

![Odwołaj subskrypcję udziałów](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Zaznacz pola obok adresatów, których subskrypcje mają być usunięte, a następnie kliknij przycisk **odwołaj**. Odbiorca nie będzie już otrzymywać aktualizacji swoich danych.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o tym, jak [monitorować udziały danych](how-to-monitor.md).