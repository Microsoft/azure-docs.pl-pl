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
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294239"
---
### <a name="the-data-model-of-the-schema"></a>Model danych schematu

| Pole | Typ danych | Opis |
| ---- | ---- | ---- |
| **AdditionalData** | dynamic | Liczba alertów, Liczba zakładek, Liczba komentarzy, nazwy produktów alertów i taktykę |
| **AlertIds** | dynamic | Alerty, z których zostało utworzone zdarzenie |
| **BookmarkIds** | dynamic | Jednostki z zakładkami |
| **Klasyfikacja** | ciąg | Klasyfikacja zamykania zdarzenia |
| **ClassificationComment** | ciąg | Komentarz dotyczący klasyfikacji zamknięcia zdarzenia |
| **ClassificationReason** | ciąg | Przyczyna zamknięcia zdarzenia |
| **ClosureTime** | datetime | Sygnatura czasowa (UTC) czasu ostatniego zamknięcia zdarzenia |
| **Komentarze** | dynamic | Komentarze do zdarzeń |
| **CreatedTime** | datetime | Sygnatura czasowa (UTC) czasu utworzenia zdarzenia |
| **Opis** | ciąg | Opis zdarzenia |
| **FirstActivityTime** | datetime | Czas pierwszego zdarzenia |
| **FirstModifiedTime** | datetime | Sygnatura czasowa (UTC) od momentu pierwszej modyfikacji zdarzenia |
| **Zdarzeniename** | ciąg | Wewnętrzny identyfikator GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | ciąg | Łącze do zdarzenia |
| **Etykiety** | dynamic | Tagi |
| **LastActivityTime** | datetime | Czas ostatniego zdarzenia |
| **LastModifiedTime** | datetime | Sygnatura czasowa (UTC) czasu ostatniej modyfikacji zdarzenia <br>(modyfikacja opisana przez bieżący rekord) |
| **ModifiedBy** | ciąg | Użytkownik lub system, który zmodyfikował zdarzenie |
| **Właściciel** | dynamic |  |
| **RelatedAnalyticRuleIds** | dynamic | Reguły, z których zostały wyzwolone alerty zdarzenia |
| **Ważność** | ciąg | Ważność incydentu (Wysoka/Średnia/niska/informacyjna) |
| **SourceSystem** | ciąg | Stała ("Azure") |
| **Stan** | ciąg |  |
| **TenantId** | ciąg |  |
| **TimeGenerated** | datetime | Sygnatura czasowa (UTC) czasu utworzenia bieżącego rekordu <br>(po modyfikacji zdarzenia) |
| **Tytuł** | ciąg | 
| **Typ** | ciąg | Stała ("SecurityIncident") |
|
