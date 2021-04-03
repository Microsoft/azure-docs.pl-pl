---
title: Komunikaty o błędach SAS maszyny wirtualnej — Azure Marketplace
description: Często zadawane pytania podczas pracy z sygnaturami dostępu współdzielonego (SAS).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: 1c89887117c10ca77ec4c04b3adbe3e2d9923479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93126842"
---
# <a name="virtual-machine-sas-failure-messages"></a>Komunikaty o błędach SAS maszyny wirtualnej

Poniżej przedstawiono typowe problemy występujące podczas pracy z sygnaturami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych dysków VHD dla rozwiązania) wraz z sugerowanymi rozwiązaniami.

| Problem | Komunikat o niepowodzeniu | Wprowadzanie poprawek |
| --------- | ------------------- | ------- |
| *Błąd podczas kopiowania obrazów* |  |  |
| nie znaleziono "?" w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego przy użyciu zalecanych narzędzi. |
| parametry "St" i "SE" nie są w identyfikatorze URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego o prawidłowej wartości daty **początkowej** i **końcowej** . |
| "Sp = RL" nie jest identyfikatorem URI SAS | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego z uprawnieniami ustawionymi jako `Read` i `List` . |
| Identyfikator URI sygnatury dostępu współdzielonego zawiera białe znaki w nazwie dysku VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj identyfikator URI sygnatury dostępu współdzielonego, aby usunąć białe znaki. |
| Błąd autoryzacji identyfikatora URI sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error.` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu współdzielonego. Wygeneruj ponownie w razie potrzeby. |
| Parametry identyfikatora URI sygnatury dostępu współdzielonego "St" i "SE" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS Uri.` | **Data rozpoczęcia** i Data **zakończenia** identyfikatora URI sygnatury dostępu współdzielonego ( `st` oraz `se` podciągi) muszą mieć pełny format daty i godziny, na przykład `11-02-2017T00:00:00Z` . Skrócone wersje są nieprawidłowe (niektóre polecenia w interfejsie wiersza polecenia platformy Azure mogą generować skrócone wartości domyślnie). |
|  |  |  |

Aby uzyskać szczegółowe informacje, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md).

## <a name="next-steps"></a>Następne kroki

- [Generuj identyfikator URI SAS](azure-vm-get-sas-uri.md)