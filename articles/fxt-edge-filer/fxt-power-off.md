---
title: Jak zamknąć Microsoft Azure FXT Edge
description: Zapoznaj się z procedurami uruchamiania i bezpiecznego zamykania węzła usługi Azure FXT Edge przy użyciu oprogramowania w panelu sterowania klastra.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: d597d5d1e86a2ea13363bc270fb55304d77dee5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88184877"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Jak bezpiecznie wyłączyć sprzęt usługi Azure FXT Edge

Mimo że można użyć fizycznego przycisku energia, aby przełączyć się na pojedynczy węzeł, nie należy używać go do zamykania jednostki w normalnych warunkach.

Gdy węzeł usługi Microsoft Azure FXT Edge jest używany jako część klastra, należy zamknąć sprzęt przy użyciu oprogramowania panelu sterowania klastra. 

> [!NOTE] 
> Aby uniknąć utraty danych lub uszkodzenia, należy zawsze używać oprogramowania panelu sterowania do zamykania pliku usługi Azure FXT Edge. Nie używaj fizycznego przycisku energia do zamknięcia, chyba że zostanie to zrobione przez dział obsługi klienta firmy Microsoft.
> 
> W przypadku awarii elektrycznej Odłącz przewody zasilania lub Użyj mechanizmu rozłączania energii elektrycznej centrum danych.

## <a name="shut-down-a-node-from-the-control-panel"></a>Zamknij węzeł w panelu sterowania

Postępuj zgodnie z poniższymi instrukcjami, aby bezpiecznie wyłączyć węzeł usługi Azure FXT Edge:

1. Zaloguj się do panelu sterowania klastra. (Instrukcje w temacie [otwieranie stron ustawień](fxt-cluster-create.md#open-the-settings-pages))
1. Kliknij kartę **Ustawienia** , a następnie załaduj **Cluster**  >  stronę**węzły FXT** klastra.
1. Na liście węzłów klastra Znajdź ten, który chcesz zamknąć. Kliknij przycisk **Wyłącz** w kolumnie **Akcje** . 
1. Poczekaj chwilę. Węzeł zostanie zamknięty i nastąpi wyłączenie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o diodach LED stanu i innych wskaźnikach w temacie [monitorowanie stanu sprzętu usługi Azure FXT Edge](fxt-monitor.md).
* Dowiedz się więcej na temat zasilaczy usługi Azure FXT Edge dotyczącej zasilaczy w [kablach połączenia](fxt-network-power.md#connect-power-cables).
