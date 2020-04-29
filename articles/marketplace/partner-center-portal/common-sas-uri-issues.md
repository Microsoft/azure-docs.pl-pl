---
title: Typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki — Azure Marketplace
description: Napotkano typowe problemy i sugerowane rozwiązania podczas pracy z sygnaturami dostępu współdzielonego.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: fb86b1c5ec3be5a423dc2abd295aa8beb8f23f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266242"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki

> [!IMPORTANT]
> Przenosimy zarządzanie ofertami maszyn wirtualnych platformy Azure z usługi portal Cloud Partner do Centrum partnerskiego. Dopóki Twoje oferty nie zostaną zmigrowane, postępuj zgodnie z instrukcjami zawartymi w temacie [typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-sas-url-issues) dotyczące Portal Cloud partner zarządzania ofertami.

Poniżej przedstawiono typowe problemy występujące podczas pracy z sygnaturami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych dysków VHD dla rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Problem** | **Komunikat o niepowodzeniu** | **Poprawka** |
| --------- | ------------------- | ------- |
| *Błąd podczas kopiowania obrazów* |  |  |
| nie znaleziono "?" w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego przy użyciu zalecanych narzędzi. |
| parametry "St" i "SE" nie są w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego o prawidłowej wartości daty **początkowej** i **końcowej** . |
| "Sp = RL" nie jest identyfikatorem URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego z uprawnieniami ustawionymi jako `Read` i `List`. |
| Identyfikator URI sygnatury dostępu współdzielonego zawiera białe znaki w nazwie dysku VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego, aby usunąć białe znaki. |
| Błąd autoryzacji identyfikatora URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error.` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu współdzielonego. Wygeneruj ponownie w razie potrzeby. |
| Parametry identyfikatora URI sygnatury dostępu współdzielonego "St" i "SE" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | **Data rozpoczęcia** i Data **zakończenia** identyfikatora URI sygnatury`st` dostępu `se` współdzielonego (oraz podciągi) muszą mieć pełny format daty `11-02-2017T00:00:00Z`i godziny, na przykład. Skrócone wersje są nieprawidłowe (niektóre polecenia w interfejsie wiersza polecenia platformy Azure mogą generować skrócone wartości domyślnie). |
|  |  |  |

Aby uzyskać szczegółowe informacje, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
