---
title: Przycisk audio usługi Azure Percept i zachowanie diody LED
description: Dowiedz się więcej na temat przycisku i Stanów diody LED usługi Azure Percept audio
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095752"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Przycisk audio usługi Azure Percept i zachowanie diody LED

Zapoznaj się z poniższymi wskazówkami, aby uzyskać informacje na temat przycisku i Stanów diody LED dźwięku usługi Azure Percept.

## <a name="button-behavior"></a>Zachowanie przycisku

Za pomocą przycisków można kontrolować zachowanie urządzenia.

|Stan przycisku|  Zachowanie|
|------------|----------|
|Głos|  Naciśnij klawisz, aby wyciszyć/wyłączyć wyciszenie macierzy mikrofonu. Zdarzenie przycisku jest wyzwalane po naciśnięciu.|
|/Object|   Naciśnij klawisz, aby pominąć słowo kluczowe wykrywania trendów i uaktywnić stan nasłuchiwania polecenia. Naciśnij przycisk ponownie, aby zatrzymać aktywny dialog agenta i wrócić do stanu wykrywania trendów słowa kluczowego. Zdarzenie przycisku jest wyzwalane po naciśnięciu. Środek do oznaczania działa tylko wtedy, gdy przycisk jest wciśnięty, gdy Agent mówi.|

## <a name="led-behavior"></a>Zachowanie diody LED

Możesz użyć wskaźników LED, aby zrozumieć, z jakim stanem jest urządzenie.

|BRANŻ|   Stan diody LED|  Wyczyść stan modelu SoM|
|---|------------|----------------| 
|L02|   1x biały, statyczny na |Włącz |
|L02|   1x biały, 0,5 Hz miga|  Trwa uwierzytelnianie |
|L01 & L02 & L03|   3. niebieskie, static on|     Oczekiwanie na słowo kluczowe|
|L01 & L02 & L03|   Dioda LED miga, 20fps | Nasłuchiwanie lub mówienie|
|L01 & L02 & L03|   Wyścigi tablicowe diod LED, 20fps|    Myśleć|
|L01 & L02 & L03|   3. czerwony, statyczny na | Głos|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące rozwiązywania problemów z urządzeniem audio usługi Azure Percept, zobacz ten [Przewodnik](./troubleshoot-audio-accessory-speech-module.md).