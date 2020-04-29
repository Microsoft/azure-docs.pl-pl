---
title: Przygotowanie rozwiązania Dynamics 365
description: Platforma do pakowania, instalowania i odinstalowywania składników
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: ac1e4fa541e945f20904ced114a36b58d14585ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80278590"
---
# <a name="dynamics-365-solution-preparation"></a>Przygotowanie rozwiązania Dynamics 365

System rozwiązań Dynamics 365 to platforma służąca do tworzenia pakietów, instalowania i odinstalowywania składników, które zapewniają konkretne funkcje biznesowe. Rozwiązania są używane przez niezależnych dostawców oprogramowania i innych partnerów systemu Microsoft Dynamics 365 do dystrybuowania utworzonych przez siebie rozszerzeń.

Jeśli jesteś istniejącym dostawcą oprogramowania Dynamics 365 (xRM), prawdopodobnie już utworzono rozwiązanie zarządzane i masz plik rozwiązania. zip. W rozwiązaniu upewnij się, że pola "Nazwa wyświetlana" i "opis" odzwierciedlają elementy, które mają być widoczne dla klientów. Są one wyświetlane w centrum administracyjnym usługi CRM Online.

![CRMScreenShot1](media/CRMScreenShot1.png)

_**Uwaga:** W poniższym przykładzie pakietu przyjmujemy, że nazwa rozwiązania to "SampleSolution. zip"_

Jeśli jesteś nowym dostawcą oprogramowania, możesz uzyskać więcej szczegółowych informacji na temat tworzenia rozwiązania tutaj:[https://msdn.microsoft.com/library/gg334530.aspx](https://msdn.microsoft.com/library/gg334530.aspx)

Jeśli Twoje rozwiązanie wymaga danych pomocniczych:

* Tworzenie przykładowych danych w środowisku testowym
* Za pomocą narzędzia do migracji konfiguracji można utworzyć schemat z regułami porównania dla danych.
* Zapisz schemat konfiguracji przy użyciu plików projektu. Będzie on potrzebny później, jeśli zaktualizujesz dane konfiguracji.
* Wyeksportuj dane konfiguracyjne. Pamiętaj, aby nadać plikowi eksportu nazwę, która ma znaczenie dla eksportu.
