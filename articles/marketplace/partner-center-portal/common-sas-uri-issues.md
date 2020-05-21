---
title: Typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki — Azure Marketplace
description: Napotkano typowe problemy i sugerowane rozwiązania podczas pracy z sygnaturami dostępu współdzielonego.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 56db3562efdc0406e745fd38b73df0a473d0ecd5
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83724602"
---
# <a name="common-sas-uri-issues-and-fixes"></a>Typowe problemy dotyczące identyfikatorów URI sygnatury dostępu współdzielonego i poprawki

Poniżej przedstawiono typowe problemy występujące podczas pracy z sygnaturami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych dysków VHD dla rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Problem** | **Komunikat o niepowodzeniu** | **Poprawka** |
| --------- | ------------------- | ------- |
| *Błąd podczas kopiowania obrazów* |  |  |
| nie znaleziono "?" w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego przy użyciu zalecanych narzędzi. |
| parametry "St" i "SE" nie są w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego o prawidłowej wartości daty **początkowej** i **końcowej** . |
| "Sp = RL" nie jest identyfikatorem URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego z uprawnieniami ustawionymi jako `Read` i `List` . |
| Identyfikator URI sygnatury dostępu współdzielonego zawiera białe znaki w nazwie dysku VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego, aby usunąć białe znaki. |
| Błąd autoryzacji identyfikatora URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error.` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu współdzielonego. Wygeneruj ponownie w razie potrzeby. |
| Parametry identyfikatora URI sygnatury dostępu współdzielonego "St" i "SE" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | **Data rozpoczęcia** i Data **zakończenia** identyfikatora URI sygnatury dostępu współdzielonego ( `st` oraz `se` podciągi) muszą mieć pełny format daty i godziny, na przykład `11-02-2017T00:00:00Z` . Skrócone wersje są nieprawidłowe (niektóre polecenia w interfejsie wiersza polecenia platformy Azure mogą generować skrócone wartości domyślnie). |
|  |  |  |

Aby uzyskać szczegółowe informacje, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
