---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88761727"
---
### <a name="the-data-model-of-the-schema"></a>Model danych schematu

| Pole | Typ danych | Opis |
| ---- | ---- | ---- |
| **AdditionalData** | dynamiczna | Liczba alertów, Liczba zakładek, Liczba komentarzy, nazwy produktów alertów i taktykę |
| **AlertIds** | dynamiczna | Alerty, z których zostało utworzone zdarzenie |
| **BookmarkIds** | dynamiczna | Jednostki z zakładkami |
| **Klasyfikacja** | ciąg | Klasyfikacja zamykania zdarzenia |
| **ClassificationComment** | ciąg | Komentarz dotyczący klasyfikacji zamknięcia zdarzenia |
| **ClassificationReason** | ciąg | Przyczyna zamknięcia zdarzenia |
| **ClosedTime** | datetime | Sygnatura czasowa (UTC) czasu ostatniego zamknięcia zdarzenia |
| **Komentarze** | dynamiczna | Komentarze do zdarzeń |
| **CreatedTime** | datetime | Sygnatura czasowa (UTC) czasu utworzenia zdarzenia |
| **Opis** | ciąg | Opis zdarzenia |
| **FirstActivityTime** | datetime | Czas pierwszego zdarzenia |
| **FirstModifiedTime** | datetime | Sygnatura czasowa (UTC) od momentu pierwszej modyfikacji zdarzenia |
| **Zdarzeniename** | ciąg | Wewnętrzny identyfikator GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | ciąg | Łącze do zdarzenia |
| **Etykiety** | dynamiczna | Tagi |
| **LastActivityTime** | datetime | Czas ostatniego zdarzenia |
| **LastModifiedTime** | datetime | Sygnatura czasowa (UTC) czasu ostatniej modyfikacji zdarzenia <br>(modyfikacja opisana przez bieżący rekord) |
| **ModifiedBy** | ciąg | Użytkownik lub system, który zmodyfikował zdarzenie |
| **Właściciel** | dynamiczna |  |
| **RelatedAnalyticRuleIds** | dynamiczna | Reguły, z których zostały wyzwolone alerty zdarzenia |
| **Ważność** | ciąg | Ważność incydentu (Wysoka/Średnia/niska/informacyjna) |
| **SourceSystem** | ciąg | Stała ("Azure") |
| **Stan** | ciąg |  |
| **TenantId** | ciąg |  |
| **TimeGenerated** | datetime | Sygnatura czasowa (UTC) czasu utworzenia bieżącego rekordu <br>(po modyfikacji zdarzenia) |
| **Tytuł** | ciąg | 
| **Typ** | ciąg | Stała ("SecurityIncident") |
|
