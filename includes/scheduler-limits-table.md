---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "75392394"
---
W poniższej tabeli opisano wszystkie główne limity przydziału, limity, wartości domyślne i ograniczenia w usłudze Azure Scheduler.

| Zasób | Opis ograniczenia |
| -------- | ----------------- |
| **Rozmiar zadania** | Maksymalny rozmiar zadania to 16 000. Jeśli operacja PUT lub PATCH powoduje, że rozmiar zadania przekracza ten limit, zwracany jest kod stanu nieprawidłowego żądania 400. | 
| **Kolekcje zadań** | Maksymalna liczba kolekcji zadań na subskrypcję platformy Azure to 200 000. | 
| **Liczba zadań na kolekcję** | Domyślnie maksymalna liczba zadań to pięć zadań w bezpłatnej kolekcji zadań i 50 zadań w standardowej kolekcji zadań. Można zmienić maksymalną liczbę zadań w kolekcji zadań. Wszystkie zadania w kolekcji zadań są ograniczone do wartości ustawionej w kolekcji zadań. Jeśli spróbujesz utworzyć więcej zadań niż maksymalny limit przydziału zadań, żądanie kończy się niepowodzeniem z kodem stanu konfliktu 409. | 
| **Czas rozpoczęcia** | Maksymalny "czas rozpoczęcia" wynosi 18 miesięcy. |
| **Zakres cyklu** | Maksymalny zakres cyklów to 18 miesięcy. | 
| **Częstotliwość** | Domyślnie maksymalny limit przydziału częstotliwości wynosi godzinę w bezpłatnej kolekcji zadań i jedną minutę w standardowej kolekcji zadań. <p>Maksymalna częstotliwość w kolekcji zadań może być mniejsza niż wartość maksymalna. Wszystkie zadania w kolekcji zadań są ograniczone do wartości ustawionej w kolekcji zadań. Jeśli podjęto próbę utworzenia zadania o wyższej częstotliwości niż maksymalna częstotliwość zbierania zadań, żądanie kończy się niepowodzeniem z kodem stanu konfliktu 409. | 
| **Rozmiar treści** | Maksymalny rozmiar treści żądania to 8 192 znaków. |
| **Rozmiar adresu URL żądania** | Maksymalny rozmiar adresu URL żądania to 2 048 znaków. |
| **Liczba nagłówków** | Maksymalna liczba nagłówków to 50. | 
| **Łączny rozmiar nagłówka** | Maksymalny rozmiar nagłówka zagregowanego to 4 096 znaków. |
| **Limit czasu** | Limit czasu żądania jest statyczny, czyli nie można go konfigurować. i wynosi 60 sekund dla akcji HTTP. Aby wykonać dłuższe operacje, należy postępować zgodnie z protokołami asynchronicznymi protokołu HTTP. Na przykład Zwróć 202 natychmiast, ale Kontynuuj pracę w tle. | 
| **Historia zadania** | Maksymalna treść odpowiedzi przechowywana w historii zadania wynosi 2 048 bajtów. |
| **Przechowywanie historii zadań** | Historia zadań jest przechowywana przez maksymalnie dwa miesiące lub do ostatnich 1 000 wykonań. | 
| **Zakończono i błędne przechowywanie zadań** | Zadania zakończone i błędne są przechowywane przez 60 dni. |
||| 

