---
title: Typowe problemy dotyczące adresów URL i poprawki dla portalu Azure Marketplace
description: Wyświetl listę typowych problemów dotyczących używania identyfikatorów URI sygnatury dostępu współdzielonego i możliwych rozwiązań.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/27/2018
ms.author: dsindona
ms.openlocfilehash: 723762695d34380b7f237fa9082dc470dafcc8df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147041"
---
# <a name="common-sas-url-issues-and-fixes"></a>Typowe problemy i poprawki dotyczące adresów URL sygnatury dostępu współdzielonego

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [typowe problemy dotyczące adresów URL i poprawek w usłudze SAS,](https://docs.microsoft.com/azure/marketplace/partner-center-portal/common-sas-uri-issues) aby zarządzać migrowanymi ofertami.

W poniższej tabeli przedstawiono niektóre typowe problemy występujące podczas pracy z sygnaturami dostępu współdzielonego (które są używane do identyfikowania i udostępniania przekazanych dysków VHD dla danego rozwiązania) wraz z sugerowanymi rozwiązaniami.

| **Problem** | **Komunikat o niepowodzeniu** | **Poprawka** | 
| --------- | ------------------- | ------- | 
| &emsp;  *Błąd podczas kopiowania obrazów* |  |  |
| nie znaleziono "?" w adresie URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego za pomocą zalecanych narzędzi. |
| parametry "St" i "SE" nie są w adresie URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego o poprawne wartości **daty rozpoczęcia** i **zakończenia** . | 
| "Sp = RL" nie jest adresem URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob using provided SAS Uri` | Zaktualizuj adres URL sygnatury dostępu współdzielonego z uprawnieniami ustawionymi jako `Read` i `List`. | 
| Adres URL sygnatury dostępu współdzielonego zawiera białe znaki w nazwie dysku VHD | `Failure: Copying Images. Not able to download blob using provided SAS Uri.` | Zaktualizuj adres URL sygnatury dostępu współdzielonego, aby usunąć białe znaki. |
| Błąd autoryzacji adresu URL sygnatury dostępu współdzielonego | `Failure: Copying Images. Not able to download blob due to authorization error` | Przejrzyj i popraw format identyfikatora URI sygnatury dostępu współdzielonego. Wygeneruj ponownie w razie potrzeby. |
| Parametry adresu URL sygnatury dostępu współdzielonego "St" i "SE" nie mają pełnej specyfikacji daty i godziny | `Failure: Copying Images. Not able to download blob due to incorrect SAS URL` | **Data rozpoczęcia** i Data **zakończenia** adresu URL sygnatury`st` dostępu `se` współdzielonego (oraz podciągi) muszą mieć pełny format DateTime `11-02-2017T00:00:00Z`, taki jak. Skrócone wersje są nieprawidłowe. (Niektóre polecenia w interfejsie wiersza polecenia platformy Azure mogą domyślnie generować skrócone wartości). | 
|  |  |  |

Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/).
