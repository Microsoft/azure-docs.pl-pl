---
title: Przycisk audio usługi Azure Percept i zachowanie diody LED
description: Dowiedz się więcej na temat przycisku i Stanów diody LED usługi Azure Percept audio
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: fa919acb527084d19ab88b2e7895d4e6ab0b72d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609075"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Przycisk audio usługi Azure Percept i zachowanie diody LED

Zapoznaj się z poniższymi wskazówkami, aby uzyskać informacje na temat przycisku i Stanów LED urządzenia audio usługi Azure Percept.

## <a name="button-behavior"></a>Zachowanie przycisku

Użyj przycisków, aby sterować zachowaniem urządzenia.

|Stan przycisku|Zachowanie|
|------------|----------|
|Głos|Naciśnij klawisz, aby wyciszyć/wyciszyć tablicę MIC. Zdarzenie przycisku jest wyzwalane po naciśnięciu.|
|/Object|Naciśnij klawisz, aby pominąć słowo kluczowe wykrywania trendów i uaktywnić stan nasłuchiwania polecenia. Naciśnij przycisk ponownie, aby zatrzymać aktywny dialog agenta i powrócić do stanu wykrywania trendów słowa kluczowego. Zdarzenie przycisku jest wyzwalane po naciśnięciu. Środek o stanie działa tylko wtedy, gdy przycisk zostanie naciśnięty, gdy Agent wysłuchuje|

## <a name="led-behavior"></a>Zachowanie diody LED

Użyj wskaźników LED, aby zrozumieć, z jakim stanem jest urządzenie.

|BRANŻ|Stan diody LED|Wyczyść stan modelu SoM|
|---|------------|----------------|
|L02|1x biały, statyczny na|Włącz |
|L02|1x biały, 0,5 Hz miga|Trwa uwierzytelnianie |
|L01 & L02 & L03|3. niebieskie, static on|Oczekiwanie na słowo kluczowe|
|L01 & L02 & L03|Dioda LED miga, 20fps |Nasłuchiwanie lub mówienie|
|L01 & L02 & L03|Wyścigi tablicowe diod LED, 20fps|Myśleć|
|L01 & L02 & L03|3. czerwony, statyczny na |Głos|

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki dotyczące rozwiązywania problemów z urządzeniem audio usługi Azure Percept, zobacz ten [Przewodnik](./troubleshoot-audio-accessory-speech-module.md).