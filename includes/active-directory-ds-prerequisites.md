---
title: dołączanie pliku
description: Uwzględnij plik z wymaganiami wstępnymi
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 66b5f8e2-e08d-43c8-b460-6204aecda8f7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.custom: include file
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: maheshu
ms.openlocfilehash: f7b1b294e9500ef9e0aadd24cfe3cd4e61fddda1
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "68426931"
---
> [!IMPORTANT]
> **Przed ukończeniem zadań w tym artykule Włącz synchronizację skrótów haseł, aby Azure AD Domain Services.**
>
> Postępuj zgodnie z poniższymi instrukcjami, w zależności od typu użytkowników w katalogu usługi Azure AD. Wykonaj oba zestawy instrukcji, jeśli masz połączenie z kontami użytkowników tylko w chmurze i synchronizowanych w katalogu usługi Azure AD. Może nie być możliwe przeprowadzenie następujących operacji w przypadku próby użycia konta gościa B2B (na przykład usługi Gmail lub MSA od innego dostawcy tożsamości, którego zezwolimy), ponieważ nie ma hasła dla tych użytkowników synchronizowanych z domeną zarządzaną, ponieważ są to konta gościa w katalogu. Pełne informacje na temat tych kont, w tym hasła, mogą znajdować się poza usługą Azure AD, a ponieważ te informacje nie są w usłudze Azure AD, dlatego nie są nawet synchronizowane z domeną zarządzaną. 
> - [Instrukcje dotyczące kont użytkowników tylko w chmurze](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)
> - [Instrukcje dotyczące kont użytkowników synchronizowanych z katalogu lokalnego](../articles/active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)
