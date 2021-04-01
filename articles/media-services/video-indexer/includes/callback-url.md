---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994501"
---
Adres URL używany do powiadamiania klienta (za pomocą żądania POST) o następujących zdarzeniach:

- Zmiana stanu indeksowania: 
    - Właściwości:    
    
        |Nazwa|Opis|
        |---|---|
        |identyfikator|Identyfikator wideo|
        |stan|Stan wideo|  
    - Przykład: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&State = przetworzone
- Osoba rozpoznana na filmie wideo:
  - Właściwości
    
      |Nazwa|Opis|
      |---|---|
      |identyfikator| Identyfikator wideo|
      |faceId|Identyfikator Face ID w indeksie wideo|
      |knownPersonId|Identyfikator osoby, unikatowy w ramach danego modelu twarzy|
      |personName|Imię i nazwisko osoby|
        
    - Przykład: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&FaceID = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&PersonName = Inigo_Montoya 
